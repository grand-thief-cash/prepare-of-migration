# 02 - Service 网络：ClusterIP → NodePort → 公网访问

> 前置：[01 - Create a Kubernetes Cluster](01_create_k8s_cluster.md)。
> 01 里我们已验证了 Pod → Pod（跨节点）连通性，本文验证 Service 这一层：
> 从 **Pod → Service (ClusterIP) → Pod** 出发，一路扩展到 **NodePort**、
> **公网访问（Windows → OCI → NodePort → Pod）**，再到 **Deployment 多副本 + 负载均衡**。
> 涉及 kube-proxy + Calico + CoreDNS + NodePort + OCI 网络。

## 0. Goal

最终要打通的完整链路：

```
                      Internet
                         │
                         ▼
                 OCI Public IP
                141.148.73.62
                         │  :32403
                         ▼
                  NodePort (任意节点)
                         │
                         ▼
                    kube-proxy
                         │
                         ▼
              web-service 10.106.58.88:80
                         │
                         ▼
                   EndpointSlice
                    /          \
                   ▼            ▼
          Worker Pod         Master Pod
       10.244.194.79      10.244.235.210
              │                   │
              └────── Calico ─────┘
```

验证路线（✓ = 已完成）：

```
Pod → Pod                        ✓  (01)
Pod → Service (ClusterIP)        ✓  (§1–§5)
Pod → Service DNS → Pod          ✓  (§7)
Node IP:NodePort → Service → Pod ✓  (§8)
公网 → NodePort → Pod            ✓  (§9)
Deployment 多副本 + 负载均衡      ✓  (§10–§11)
跨节点 Backend                   ✓  (§12)
```

---

## 1. 创建后端 Pod

在 k8s-master 执行：

```bash
kubectl run web-server \
  --image=nginx:1.27 \
  --restart=Never
```

检查：

```bash
kubectl get pod web-server -o wide
```

实际结果：

```
NAME         READY   STATUS    RESTARTS   AGE     IP              NODE
web-server   1/1     Running   0          4m42s   10.244.194.75   k8s-worker1
```

---

## 2. 创建 Service

```bash
kubectl expose pod web-server \
  --port=80 \
  --target-port=80 \
  --name=web-service
```

```bash
kubectl get svc web-service -o wide
```

实际结果：

```
NAME          TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE     SELECTOR
web-service   ClusterIP   10.106.58.88   <none>        80/TCP    3m40s   run=web-server
```

这里出现三个关键信息：

- `web-service`：Service 名字
- `10.106.58.88`：Service 的 **ClusterIP**
- `run=web-server`：**Selector**，决定哪些 Pod 是它的后端

---

## 3. 确认 Service 后面确实有 Pod

```bash
kubectl get endpoints web-service
```

或者新版本更推荐看 EndpointSlice：

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

期望看到：

```
web-service   10.244.194.75:80
```

即：

```
Service
10.106.58.88:80
     │
     ▼
web-server
10.244.194.75:80
```

---

## 4. 创建 client Pod

之前的 net-test-2 已经删了，创建一个新的测试 Pod：

```bash
kubectl run client \
  --image=busybox:1.36 \
  --restart=Never \
  -- sleep 86400
```

```bash
kubectl get pods -o wide
```

实际结果：

```
NAME         READY   STATUS    RESTARTS   AGE     IP              NODE
client       1/1     Running   0          10s     10.244.194.76   k8s-worker1
web-server   1/1     Running   0          4m42s   10.244.194.75   k8s-worker1
```

---

## 5. 通过 ClusterIP 访问

先拿到 ClusterIP（本例为 `10.106.58.88`），然后：

```bash
kubectl exec client -- wget -qO- http://10.106.58.88
```

实际结果：成功返回 nginx HTML：

```
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title></head>
<body>
<h1>Welcome to nginx!</h1>
...
```

✅ 说明整个 ClusterIP Service 数据路径已经打通：

```
client Pod → ClusterIP → kube-proxy → web-server Pod → nginx → HTTP 200
```

---

## 6. 原理：ClusterIP 是怎么和 web-service 绑定的？

最关键的问题：**`10.106.58.88` 这个 ClusterIP 到底是怎么和 `web-service` 绑定起来的？**

答案：不是 ClusterIP 和 Service 直接"绑定"，而是——

1. API Server 从 Service CIDR 给 Service 分配一个**虚拟 IP**
2. Service 通过 **Selector** 找到后端 Pod
3. **EndpointSlice** 记录当前实际后端
4. **kube-proxy** 在每个节点上把虚拟 IP 的转发规则写进内核

### 6.1 `expose` 实际创建了什么

`kubectl expose pod web-server ...` 实际上是向 API Server 提交了这样一个对象：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    run: web-server
  ports:
    - port: 80        # Service 暴露的端口
      targetPort: 80  # 转发到 Pod 的端口
  type: ClusterIP
```

### 6.2 ClusterIP 是谁分配的？

API Server / Service Controller 根据集群的 **Service CIDR** 分配。

注意 Pod IP 和 Service IP 来自**两个不同的地址池**：

```
Pod Network（Calico，--pod-network-cidr 指定）
10.244.0.0/16
        └── Pod IP: 10.244.194.75 / 10.244.194.76 / ...

Service Network（kubeadm 默认 10.96.0.0/12）
10.96.0.0 – 10.111.255.255
        └── ClusterIP: 10.106.58.88   ← 正好落在这个范围内
```

> 验证 Service CIDR：
> ```bash
> sudo grep service-cluster-ip-range /etc/kubernetes/manifests/kube-apiserver.yaml
> ```

### 6.3 ClusterIP 是虚拟 IP

`web-service` **不是一个 Pod**，`10.106.58.88` 也不对应任何真实网卡。
它只是一个"入口"，真正干活的是 kube-proxy 写进内核的转发规则。

### 6.4 Selector → EndpointSlice

Service 不直接记住 Pod IP，而是通过 selector 声明"所有带 `run=web-server` 标签的 Pod 都是我的后端"，由 **EndpointSlice** 保存当前实际的后端列表：

```
Service web-service
      │
      │ selector: run=web-server
      ▼
EndpointSlice
      │
      │ endpoint
      ▼
10.244.194.75:80
```

Pod 的 label 可以这样确认：

```bash
kubectl get pod web-server --show-labels
# NAME         LABELS
# web-server   run=web-server
```

### 6.5 kube-proxy 出场

client Pod 发起 `wget http://10.106.58.88` 时，它只知道目标 IP，并不知道 nginx Pod 在哪。完成

```
10.106.58.88:80  →  10.244.194.75:80
```

转换的，是每个节点上的 **kube-proxy**（默认 iptables 模式，把规则写进 netfilter）：

```
                 kube-proxy
                     │
                     ▼
10.106.58.88:80 ──────────────► 10.244.194.75:80
    ClusterIP                       Pod
```

> 有意思的点：本例 client 和 web-server 其实在**同一个节点**（k8s-worker1）上，
> 但流量依然走 kube-proxy 的 iptables 规则，和跨节点路径在"进入转发"这一步是一样的。

### 6.6 完整数据路径复盘

```
① client Pod (10.244.194.76)
        │  HTTP GET
        ▼
② Service ClusterIP (10.106.58.88:80)
        │  kube-proxy: Service → Endpoint
        ▼
③ Backend Pod (10.244.194.75:80)
        │
        ▼
④ nginx → HTTP 200
```

### 6.7 容易误解的地方

❌ "Kubernetes 给 web-service 创建了一个 IP，然后这个 IP 绑定到了 Pod"

✅ 实际是：

```
Service web-service
   ├── ClusterIP: 10.106.58.88   （虚拟入口）
   ├── Port: 80
   └── Selector: run=web-server
              │
              ▼
         EndpointSlice
              │
              ▼
         10.244.194.75:80   （真实后端）
```

kube-proxy 负责的，就是"虚拟入口 ClusterIP → 真实后端 Endpoint"这一跳的转换。
```

### 6.8 为什么 Pod 重建后 Service 还能工作

假设 `web-server`（10.244.194.75）被删掉，K8s 创建一个新 Pod：

```
web-server  10.244.235.100   （新 IP，但 label 仍是 run=web-server）
```

EndpointSlice 自动更新：

```
旧: 10.244.194.75:80  →  新: 10.244.235.100:80
```

而 Service 的 ClusterIP `10.106.58.88` **完全不变**，客户端依然访问同一个地址。

这就是 Service 最核心的价值：**给一组会变化的 Pod 提供一个稳定的访问入口。**

Pod IP 天生不稳定——重建会换 IP，甚至换节点。如果客户端硬编码 Pod IP，Pod 一换就废了。

---

## 7. DNS 测试 ✅

现在访问用的是硬编码的 ClusterIP。K8s 更方便的方式是 **CoreDNS** 把 Service 名字解析成 ClusterIP。三个命令依次验证：

### 7.1 Endpoints：Service 的后端记录

```bash
kubectl get endpoints web-service
```
```
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME          ENDPOINTS          AGE
web-service   10.244.194.75:80   65m
```

> `v1 Endpoints` 在 v1.33+ 已标记弃用，只为了向后兼容保留。以后直接用 EndpointSlice（见 7.2）。

### 7.2 EndpointSlice：现代的记录方式

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```
```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS       AGE
web-service-92fps   IPv4          80      10.244.194.75   65m
```

两条命令看到的是同一个事实：Service `web-service` 的当前后端是 `10.244.194.75:80`。

### 7.3 DNS 解析

```bash
kubectl exec client -- nslookup web-service
```
```
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   web-service.default.svc.cluster.local
Address: 10.106.58.88

** server can't find web-service.svc.cluster.local: NXDOMAIN
** server can't find web-service.cluster.local: NXDOMAIN
** server can't find web-service.vnc1.oraclevcn.com: NXDOMAIN
...
command terminated with exit code 1
```

成功解析到了 `10.106.58.88`。这段输出有三个值得注意的点：

**① `10.96.0.10` 是谁？**

它是 `kube-dns` 这个 Service 自己的 ClusterIP（CoreDNS 的稳定入口）：

```bash
kubectl get svc -n kube-system kube-dns
# NAME       TYPE        CLUSTER-IP   PORT(S)
# kube-dns   ClusterIP   10.96.0.10   53/UDP,53/TCP
```

DNS 本身也是一个 Service——所以每个 Pod 的 `/etc/resolv.conf` 里才有一个固定不变的 DNS 地址。

**② 为什么全名是 `web-service.default.svc.cluster.local`？**

集群内 Service 的完整域名格式：

```
<service>.<namespace>.svc.cluster.local
web-service.default.svc.cluster.local
     │          │
     │          └── 我们没指定 namespace，Pod 在 default
     └── Service 名字
```

短名 `web-service` 也能解析，是因为 Pod 的 `/etc/resolv.conf` 带有 search 列表，DNS 会按顺序补全后缀尝试。

**③ 那些 NXDOMAIN 和 exit code 1 是怎么回事？**

`nslookup` 按 search 列表逐个后缀尝试：`default.svc.cluster.local` 命中后，busybox 还会把剩下的后缀（`svc.cluster.local`、`cluster.local`、以及从节点继承的 `vnc1.oraclevcn.com`）都试一遍，失败的打 NXDOMAIN。**第一次查询其实已经成功**，后面的失败只是尝试过程；最后 exit code 1 也是 busybox nslookup 的怪癖（只要有查询失败就返回非 0），不影响结果。

### 7.4 最终验证：用域名访问

```bash
kubectl exec client -- wget -qO- http://web-service
```
```
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title></head>
<body>
<h1>Welcome to nginx!</h1>
...
```

完整链路：

```
wget http://web-service
   │
   ▼
CoreDNS (10.96.0.10)  ──►  web-service → 10.106.58.88
   │
   ▼
ClusterIP 10.106.58.88:80
   │
   ▼  kube-proxy (iptables)
web-server Pod 10.244.194.75:80
   │
   ▼
nginx → HTTP 200
```

✅ 至此三层网络全部走通：**Pod → Pod / Pod → Service (ClusterIP) / Pod → Service DNS → Pod**。

---

## 8. NodePort：把 Service 暴露到节点

到目前为止，`web-service` 只能从集群内部访问（client Pod → ClusterIP）。这一步把 Service 类型改成 **NodePort**，让每个 Node 的 IP 也能访问同一个 Service。

### 8.1 修改 Service 类型

```bash
kubectl patch svc web-service -p '{"spec":{"type":"NodePort"}}'
```

```bash
kubectl get svc web-service -o wide
```

实际结果：

```
NAME          TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
web-service   NodePort   10.106.58.88   <none>        80:32403/TCP   ...
```

关键信息是 `80:32403/TCP`：

```
Service port          NodePort
     80                  32403
     │                     │
     ▼                     ▼
ClusterIP             每个 Node 都"监听"
10.106.58.88:80       10.0.0.128:32403 / 10.0.0.164:32403
```

- 没有显式指定 `nodePort` 时，K8s 从默认范围 **30000–32767** 随机分配。
- ClusterIP 部分完全不变，原来 `http://web-service` 的访问方式继续有效。

### 8.2 后端没有变

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

Endpoint 仍然是 `10.244.194.75:80`。改 Service 类型只增加"入口"，不改变"后端"：

```
NodePort :32403     ← 新增的入口
ClusterIP :80       ← 原有的入口
      │
      ▼
  同一个 Service / EndpointSlice
      │
      ▼
  10.244.194.75:80 (nginx)
```

### 8.3 从节点内部测试

在 k8s-master 上：

```bash
curl http://10.0.0.164:32403   # 访问 worker 的 NodePort → nginx ✅
curl http://10.0.0.128:32403   # 访问 master 的 NodePort → nginx ✅
```

两个都返回了 nginx 页面。**最重要的一点**：

> 访问 **master** 的 NodePort 也成功了，而 backend Pod 运行在 **worker1** 上。
> NodePort 并不要求 Pod 在被访问的那个节点上 —— 任何节点的 NodePort
> 都能把流量送进 Service，再由 kube-proxy 送到（可能位于其他节点的）后端。

### 8.4 一个容易误判的现象：`ss` 看不到 32403

在节点上执行：

```bash
sudo ss -lntp | grep 32403
# 通常没有任何输出
```

看不到 LISTEN **不代表 NodePort 有问题**。kube-proxy 默认是 iptables 模式：它不启动用户态监听进程，而是把 DNAT 规则写进内核 netfilter，由内核在 packet 经过 PREROUTING / OUTPUT 时直接改写目标地址：

```
packet → 10.0.0.164:32403
             │
             │  netfilter（kube-proxy 写入的规则）
             ▼
         DNAT → 10.244.194.75:80
```

这正好对应 [iptables 文档](../linux/network/iptables_1.md) 里的 packet processing path：NodePort 的转换发生在 `nat.PREROUTING`（外部进来的流量）/ `nat.OUTPUT`（本机发出的流量）位置。所以判断 NodePort 是否工作的最直接方式是 curl，而不是 `ss`。

---

## 9. 公网访问：Windows → OCI → NodePort → Pod

### 9.1 测试

在自己电脑（Windows）上访问 worker 的公网 IP：

```powershell
curl.exe -s -i http://141.148.73.62:32403
```

实际结果：

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 615
```

✅ 完整链路打通：

```
Windows / Internet
       │
       ▼
OCI Public IP
141.148.73.62
       │  :32403
       ▼
OCI Security List / NSG      ← 云层防火墙
       │
       ▼
Ubuntu iptables              ← OS 层防火墙
       │
       ▼
NodePort :32403
       │
       ▼
kube-proxy (netfilter)
       │
       ▼
web-service → EndpointSlice
       │
       ▼
nginx Pod 10.244.194.75:80 → HTTP 200
```

### 9.2 为什么这个实验特别有价值

之前排查 kube-apiserver :6443 时已经发现：**OCI 允许流量 ≠ Ubuntu 一定允许流量**。
从公网到 Pod，流量要穿过三个互相独立的层：

| 层 | 谁负责 | 本例状态 |
| --- | --- | --- |
| OCI Security List / NSG | 云控制面 | 已放行（本次未做任何修改即成功） |
| Ubuntu iptables / ufw | 节点 OS | 已放行 |
| Kubernetes (kube-proxy) | 集群 | NodePort 规则由 kube-proxy 自动下发 |

如果公网访问失败，定位顺序也应该是 **OCI → Ubuntu → Kubernetes** 一层层验证，
而不是一上来就开一条 `0.0.0.0/0` 的大规则。

---

## 10. 从裸 Pod 到 Deployment

### 10.1 为什么需要 Deployment

`web-server` 是 `kubectl run` 直接创建的裸 Pod，想跑两个 nginx 时发现：

```bash
kubectl scale pod web-server --replicas=2
# error: cannot scale ...   ← Pod 没有 replicas 概念
```

标准做法是各管各的：

> **Deployment 管理 Pod**（副本数 / 自愈 / 滚动更新），**Service 管理 Pod 的访问**。

```
Deployment
    ↓
ReplicaSet
    ↓
Pod × N
```

### 10.2 替换

```bash
kubectl delete pod web-server
kubectl create deployment web-server --image=nginx:1.27 --replicas=2
kubectl get pods -o wide
```

实际结果：

```
NAME                          READY   STATUS    IP              NODE
web-server-7486f6d5b-86zfb    1/1     Running   10.244.194.77   k8s-worker1
web-server-7486f6d5b-b28xh    1/1     Running   10.244.194.78   k8s-worker1
```

Pod 名字变成 `Deployment名-ReplicaSet哈希-随机后缀`，不再是裸 Pod 的简单名字。

### 10.3 踩坑：Service 突然找不到后端了

`kubectl get pods --show-labels` 发现新 Pod 的 label 是：

```
app=web-server
```

而 Service 的 selector 还是旧的：

```
run=web-server
```

两边对不上 → EndpointSlice 变空，Service 没有任何 backend。

这个坑的价值在于强化一个核心概念：

> ❌ "Service 会自动找到名字叫 web-server 的 Pod"
>
> ✅ **Service 是通过 label selector 找 Pod 的。**
> Pod 叫什么名字无所谓，label 对不上才是致命的。

### 10.4 修复：把 selector 改成新的 label

```bash
kubectl patch svc web-service -p '{"spec":{"selector":{"app":"web-server"}}}'
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS
web-service-92fps   IPv4          80      10.244.194.77,10.244.194.78
```

EndpointSlice 现在有两个 backend，结构正式变成：

```
web-service
     │
     ▼
EndpointSlice
     ├── 10.244.194.77:80   (Pod ...-86zfb)
     └── 10.244.194.78:80   (Pod ...-b28xh)
```

验证恢复：

```bash
kubectl exec client -- wget -qO- http://web-service
# <!DOCTYPE html> ...  ← Service 重新找到后端
```

---

## 11. 观察负载均衡

### 11.1 给每个 Pod 一个身份

两个 nginx 返回的页面一模一样，看不出请求到底落到了谁身上。分别写入不同的内容：

```bash
kubectl exec web-server-7486f6d5b-86zfb -- \
  sh -c 'echo "Hello from Pod 86zfb - IP 10.244.194.77" > /usr/share/nginx/html/index.html'
kubectl exec web-server-7486f6d5b-b28xh -- \
  sh -c 'echo "Hello from Pod b28xh - IP 10.244.194.78" > /usr/share/nginx/html/index.html'
```

先绕过 Service 直接验证两个 backend 确实是两个不同的 nginx 实例：

```bash
kubectl exec client -- wget -qO- http://10.244.194.77
# Hello from Pod 86zfb - IP 10.244.194.77
kubectl exec client -- wget -qO- http://10.244.194.78
# Hello from Pod b28xh - IP 10.244.194.78
```

### 11.2 通过 Service 访问多次

```bash
for i in $(seq 1 10); do
  kubectl exec client -- wget -qO- http://web-service
  echo
done
```

实际结果（节选）：

```
Hello from Pod 86zfb - IP 10.244.194.77
Hello from Pod b28xh - IP 10.244.194.78
Hello from Pod b28xh - IP 10.244.194.78
Hello from Pod b28xh - IP 10.244.194.78
```

两个 Pod 都出现了 → Service 确实在多个 Endpoint 之间分流。

公网 NodePort 同样测试（Windows PowerShell）：

```powershell
for ($i=1; $i -le 10; $i++) {
    curl.exe -s http://141.148.73.62:32403
    Write-Host
}
```

同样能看到两个 Pod 的返回 —— 证明「公网 → NodePort → Service → 多个 Pod」的分流链路是通的。

### 11.3 纠正一个常见误解：不是严格 round-robin

不要期待 `77 / 78 / 77 / 78` 严格交替。默认 iptables 模式下 kube-proxy 是**随机**选择 backend（没有轮询计数器），所以 `77 / 78 / 78 / 78` 完全正常。观察的重点：

- ✅ 请求能到达**不同**的 Endpoint
- ❌ 不要求每次请求严格 50/50

（想要会话保持或更均匀的分配，可以研究 `sessionAffinity`、`internalTrafficPolicy`、IPVS 模式，属于后话。）

---

## 12. 跨节点 Backend

### 12.1 为什么两个 Pod 都在 worker1

```bash
kubectl describe node k8s-master | grep -i taint
# Taints: node-role.kubernetes.io/control-plane:NoSchedule
```

Master 默认带 `NoSchedule` taint，普通 Pod 不会被调度上去——这就是 01/02 里所有 Pod 都落在 worker1 的原因。

### 12.2 临时解除 taint，让 Pod 重新分布

```bash
kubectl taint nodes k8s-master node-role.kubernetes.io/control-plane:NoSchedule-
kubectl describe node k8s-master | grep -i taint
# Taints: <none>

kubectl rollout restart deployment web-server
kubectl get pods -l app=web-server -o wide -w    # 都 Running 后 Ctrl+C
```

实际结果：

```
NAME                       READY   STATUS    IP               NODE
web-server-888d69d-824kf   1/1     Running   10.244.194.79    k8s-worker1
web-server-888d69d-sw5c8   1/1     Running   10.244.235.210   k8s-master
```

Scheduler 自由调度，结果一个在 Worker、一个在 Master —— 正好是理想分布。

两个注意点：

- 解除 taint **不会搬走已经运行的 Pod**（Scheduler 没有理由主动迁移），所以必须 `rollout restart` 让 Deployment 重建 Pod。
- Scheduler 也可能把两个 Pod 都放在 worker（或都在 master），三种结果对它来说都合理。不要为了凑"一 worker 一 master"反复重启；真想要指定分布，下一步该学的是 `nodeSelector` / `nodeAffinity`。

### 12.3 Pod CIDR 是按节点划分的

```
k8s-worker1  Pod CIDR 10.244.194.0/24  →  10.244.194.79
k8s-master   Pod CIDR 10.244.235.0/24  →  10.244.235.210
```

看 IP 的第三段就能猜到 Pod 在哪个节点上（Calico 默认按节点分配 /24）。

### 12.4 Service 自动纳入两个节点的 Pod

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS
web-service-92fps   IPv4          80      10.244.194.79,10.244.235.210
```

不需要任何额外配置：EndpointSlice 只认 label 不认节点。跨节点能否连通由 Calico 保证（01 已验证），Service 只负责把两个 Pod 都登记为 backend。至此 01 的成果和 02 的成果真正合并成一条链。

---

## 13. 插曲：Pod 是临时的（Ephemeral）

rollout restart 之后再访问 Service，页面变回了 nginx 默认页 —— 之前 `kubectl exec` 写进容器的 `Hello from Pod ...` 没了。

原因：restart = 删旧 Pod、建新 Pod，新 Pod 用的是**全新容器**（镜像里原始的 index.html）。

> **Pod 是临时的，不要把状态/配置直接写进 Pod 的文件系统。**
> 生产上应该用镜像、ConfigMap、Secret 或持久化存储，而不是 `kubectl exec ... > index.html`。

---

## 14. 完整链路总结

```
                      Internet
                         │
                         ▼
                 OCI Public IP
                141.148.73.62
                         │  :32403
                         ▼
              OCI Security List / NSG
                         │
                         ▼
                  Ubuntu iptables
                         │
                         ▼
                 NodePort (任意节点)
                         │
                         ▼
                    kube-proxy
                         │
                         ▼
              web-service 10.106.58.88:80
                         │
                         ▼
                   EndpointSlice
                    /          \
                   ▼            ▼
          Worker Pod         Master Pod
       10.244.194.79      10.244.235.210
              │                   │
              └────── Calico ─────┘
```

已验证清单：

| 项 | 出处 | 状态 |
| --- | --- | --- |
| Pod → Pod（跨节点） | 01 | ✅ |
| Pod → ClusterIP | 本文 §5 | ✅ |
| Service → EndpointSlice | 本文 §3 / §7 | ✅ |
| CoreDNS / Service DNS | 本文 §7 | ✅ |
| NodePort（master / worker） | 本文 §8 | ✅ |
| 公网 → NodePort → Pod | 本文 §9 | ✅ |
| Deployment 2 副本 + 多 Endpoint | 本文 §10 | ✅ |
| 负载均衡（非严格轮询） | 本文 §11 | ✅ |
| 跨节点 Backend | 本文 §12 | ✅ |

---

## 15. Next

- **Downward API + Pod 自识别**：不再手工写死 IP，让每个 Pod 通过 Downward API 环境变量（`metadata.name` / `spec.nodeName` / `status.podIP`）报告自己的身份，然后从公网连续访问 NodePort，肉眼观察请求在 Master Pod / Worker Pod 之间流动。
  - ⚠️ 不能用 `kubectl set env deployment/web-server POD_NAME=$(kubectl get pod ...)` 把第一个 Pod 的名字写死成固定环境变量——那是所有 Pod 共享的常量。正确做法是在 Pod spec 里用 `fieldRef` 引用 `metadata.name`，这样每个 Pod 注入的都是自己的名字。
- **kube-proxy 规则实查**：`sudo iptables-save | grep 10.106.58.88` 和 `sudo iptables-save | grep KUBE-SVC`，在节点上找到 kube-proxy 写的真实规则，把 §8.4 的理论和内核层面对应起来（呼应 [iptables 文档](../linux/network/iptables_1.md)）。
- **收尾提醒**：实验全部做完后，把 master 的 taint 恢复回去：
  ```bash
  kubectl taint nodes k8s-master node-role.kubernetes.io/control-plane:NoSchedule
  ```
- **声明式 YAML**：把 `kubectl create deployment` 换成 `kind: Deployment` + `kind: Service` 的 YAML 清单。

---

## Appendix: 观察记录

- `web-server` 与 `client` 都被调度到了 `k8s-worker1`（master 默认有 taint 不跑普通 Pod，见 §12）。
- `kubectl expose pod` 生成的 selector 是 `run=web-server`（来自 `kubectl run` 自动打的 label）；`kubectl create deployment` 打的 label 是 `app=web-server`——两者不一致正是 §10.3 踩坑的根源。
- ClusterIP `10.106.58.88` 落在 kubeadm 默认 Service CIDR `10.96.0.0/12` 内，与 Pod CIDR `10.244.0.0/16` 互不重叠。
- NodePort `32403` 在默认范围 30000–32767 内；两个节点的 NodePort 端口号相同（都是 32403）。
- `kubectl patch` 在线修改 Service 类型 / selector 都立即生效，且 ClusterIP 不变。
- 公网测试走的是 worker 的 OCI Public IP `141.148.73.62`；本次测试前没有为 32403 做任何防火墙改动就成功了，说明 OCI/OS 层已有放行规则（如果换端口失败，按 §9.2 的顺序排查）。
- rollout restart 生成新的 ReplicaSet（`7486f6d5b` → `888d69d`），旧 Pod 上的手工修改随旧 Pod 一起消失（§13）。
