# 02 - Service Networking: ClusterIP → NodePort → Public Access

> Prerequisite: [01 - Create a Kubernetes Cluster](01_create_k8s_cluster.md).
> In 01 we already verified Pod → Pod (cross-node) connectivity. This doc verifies the Service layer:
> starting from **Pod → Service (ClusterIP) → Pod**, then extending all the way to **NodePort**,
> **public access (Windows → OCI → NodePort → Pod)**, and **Deployment multi-replica + load balancing**.
> Involves kube-proxy + Calico + CoreDNS + NodePort + OCI networking.

## 0. Goal

The full chain we want to end up with:

```
                      Internet
                         │
                         ▼
                 OCI Public IP
                141.148.73.62
                         │  :32403
                         ▼
                  NodePort (any node)
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

Verification roadmap (✓ = done):

```
Pod → Pod                        ✓  (01)
Pod → Service (ClusterIP)        ✓  (§1–§5)
Pod → Service DNS → Pod          ✓  (§7)
Node IP:NodePort → Service → Pod ✓  (§8)
Public → NodePort → Pod          ✓  (§9)
Deployment multi-replica + LB    ✓  (§10–§11)
Cross-node backend               ✓  (§12)
```

---

## 1. Create the backend Pod

Run on k8s-master:

```bash
kubectl run web-server \
  --image=nginx:1.27 \
  --restart=Never
```

Check:

```bash
kubectl get pod web-server -o wide
```

Actual result:

```
NAME         READY   STATUS    RESTARTS   AGE     IP              NODE
web-server   1/1     Running   0          4m42s   10.244.194.75   k8s-worker1
```

---

## 2. Create the Service

```bash
kubectl expose pod web-server \
  --port=80 \
  --target-port=80 \
  --name=web-service
```

```bash
kubectl get svc web-service -o wide
```

Actual result:

```
NAME          TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE     SELECTOR
web-service   ClusterIP   10.106.58.88   <none>        80/TCP    3m40s   run=web-server
```

Three key pieces of information here:

- `web-service`: the Service name
- `10.106.58.88`: the Service's **ClusterIP**
- `run=web-server`: the **Selector**, which decides which Pods are its backends

---

## 3. Confirm the Service actually has a Pod behind it

```bash
kubectl get endpoints web-service
```

Or, preferred on newer versions, look at EndpointSlice:

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

Expected:

```
web-service   10.244.194.75:80
```

That is:

```
Service
10.106.58.88:80
     │
     ▼
web-server
10.244.194.75:80
```

---

## 4. Create the client Pod

net-test-2 from earlier was already deleted, so create a new test Pod:

```bash
kubectl run client \
  --image=busybox:1.36 \
  --restart=Never \
  -- sleep 86400
```

```bash
kubectl get pods -o wide
```

Actual result:

```
NAME         READY   STATUS    RESTARTS   AGE     IP              NODE
client       1/1     Running   0          10s     10.244.194.76   k8s-worker1
web-server   1/1     Running   0          4m42s   10.244.194.75   k8s-worker1
```

---

## 5. Access via the ClusterIP

First get the ClusterIP (`10.106.58.88` in this example), then:

```bash
kubectl exec client -- wget -qO- http://10.106.58.88
```

Actual result: the nginx HTML comes back successfully:

```
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title></head>
<body>
<h1>Welcome to nginx!</h1>
...
```

✅ The whole ClusterIP Service data path now works:

```
client Pod → ClusterIP → kube-proxy → web-server Pod → nginx → HTTP 200
```

---

## 6. How is the ClusterIP actually bound to web-service?

The key question: **how exactly is the ClusterIP `10.106.58.88` bound to `web-service`?**

Answer: it is not a direct binding between ClusterIP and Service. Rather:

1. The API Server allocates a **virtual IP** to the Service from the Service CIDR
2. The Service finds its backend Pods via the **Selector**
3. The **EndpointSlice** records the current actual backends
4. **kube-proxy** writes the forwarding rules for the virtual IP into the kernel on every node

### 6.1 What `expose` actually created

`kubectl expose pod web-server ...` actually submits this object to the API Server:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    run: web-server
  ports:
    - port: 80        # the port the Service exposes
      targetPort: 80  # the port to forward to on the Pod
  type: ClusterIP
```

### 6.2 Who allocates the ClusterIP?

The API Server / Service Controller allocates it from the cluster's **Service CIDR**.

Note that Pod IPs and Service IPs come from **two different address pools**:

```
Pod Network (Calico, specified via --pod-network-cidr)
10.244.0.0/16
        └── Pod IPs: 10.244.194.75 / 10.244.194.76 / ...

Service Network (kubeadm default 10.96.0.0/12)
10.96.0.0 – 10.111.255.255
        └── ClusterIP: 10.106.58.88   ← happens to fall in this range
```

> Verify the Service CIDR:
> ```bash
> sudo grep service-cluster-ip-range /etc/kubernetes/manifests/kube-apiserver.yaml
> ```

### 6.3 The ClusterIP is a virtual IP

`web-service` is **not a Pod**, and `10.106.58.88` does not correspond to any real network interface.
It is only an "entry point"; the thing actually doing the work is the forwarding rules kube-proxy writes into the kernel.

### 6.4 Selector → EndpointSlice

The Service does not memorize Pod IPs. Instead it declares via the selector that "all Pods carrying the `run=web-server` label are my backends", and the **EndpointSlice** stores the current actual backend list:

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

You can confirm the Pod's label like this:

```bash
kubectl get pod web-server --show-labels
# NAME         LABELS
# web-server   run=web-server
```

### 6.5 Enter kube-proxy

When the client Pod issues `wget http://10.106.58.88`, all it knows is the destination IP — it has no idea where the nginx Pod actually is. The one that completes

```
10.106.58.88:80  →  10.244.194.75:80
```

this translation is **kube-proxy** on every node (iptables mode by default, writing rules into netfilter):

```
                 kube-proxy
                     │
                     ▼
10.106.58.88 ─────────────────► 10.244.194.75:80
    ClusterIP                       Pod
```

> Interesting detail: in this example the client and web-server are actually on the **same node** (k8s-worker1),
> but the traffic still goes through kube-proxy's iptables rules — at the "enter forwarding" step, the path is the same as the cross-node one.

### 6.6 Full data path recap

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

### 6.7 Common misconceptions

❌ "Kubernetes created an IP for web-service and bound it to the Pod"

✅ The reality:

```
Service web-service
   ├── ClusterIP: 10.106.58.88   (virtual entry point)
   ├── Port: 80
   └── Selector: run=web-server
              │
              ▼
         EndpointSlice
              │
              ▼
         10.244.194.75:80   (real backend)
```

What kube-proxy is responsible for is exactly the hop that translates "virtual entry point ClusterIP → real backend Endpoint".

### 6.8 Why the Service keeps working after a Pod rebuild

Suppose `web-server` (10.244.194.75) is deleted and K8s creates a new Pod:

```
web-server  10.244.235.100   (new IP, but label still run=web-server)
```

EndpointSlice updates automatically:

```
old: 10.244.194.75:80  →  new: 10.244.235.100:80
```

The Service's ClusterIP `10.106.58.88` stays **exactly the same**, so clients keep hitting the same address.

This is the core value of a Service: **providing a stable access point for a group of Pods whose IPs keep changing.**

Pod IPs are unstable by nature — a rebuild changes the IP, sometimes even the node. If a client hard-codes a Pod IP, everything breaks the moment the Pod is replaced.

---

## 7. DNS test ✅

So far access has used the hard-coded ClusterIP. The more convenient K8s way is **CoreDNS** resolving the Service name to the ClusterIP. Three commands, verified in order:

### 7.1 Endpoints: the Service's backend record

```bash
kubectl get endpoints web-service
```
```
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME          ENDPOINTS          AGE
web-service   10.244.194.75:80   65m
```

> `v1 Endpoints` is deprecated as of v1.33+ and kept only for backward compatibility. Going forward, use EndpointSlice (see 7.2).

### 7.2 EndpointSlice: the modern way to record it

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```
```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS       AGE
web-service-92fps   IPv4          80      10.244.194.75   65m
```

Both commands show the same fact: the current backend of Service `web-service` is `10.244.194.75:80`.

### 7.3 DNS resolution

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

It resolved to `10.106.58.88` successfully. Three things worth noting in this output:

**① Who is `10.96.0.10`?**

It is the ClusterIP of the `kube-dns` Service itself (CoreDNS's stable entry point):

```bash
kubectl get svc -n kube-system kube-dns
# NAME       TYPE        CLUSTER-IP   PORT(S)
# kube-dns   ClusterIP   10.96.0.10   53/UDP,53/TCP
```

DNS is itself a Service — which is exactly why every Pod has a fixed, unchanging DNS address in its `/etc/resolv.conf`.

**② Why is the full name `web-service.default.svc.cluster.local`?**

The full domain format for in-cluster Services:

```
<service>.<namespace>.svc.cluster.local
web-service.default.svc.cluster.local
     │          │
     │          └── we didn't specify a namespace; the Pod is in default
     └── Service name
```

The short name `web-service` also resolves because the Pod's `/etc/resolv.conf` carries a search list, and DNS tries completing the suffix in order.

**③ What about those NXDOMAINs and exit code 1?**

`nslookup` walks through the search list suffix by suffix: after `default.svc.cluster.local` hits, busybox still tries the remaining suffixes (`svc.cluster.local`, `cluster.local`, and the node-inherited `vnc1.oraclevcn.com`), printing NXDOMAIN for the failures. **The first query actually succeeded** — the later failures are just part of the trying process. The final exit code 1 is also a busybox nslookup quirk (it returns non-zero as long as any query failed); it doesn't affect the result.

### 7.4 Final verification: access by name

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

Full chain:

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

✅ At this point all three layers are working: **Pod → Pod / Pod → Service (ClusterIP) / Pod → Service DNS → Pod**.

---

## 8. NodePort: exposing the Service on the nodes

So far `web-service` was only reachable from inside the cluster (client Pod → ClusterIP). This step changes the Service type to **NodePort**, so that every node's IP can reach the same Service.

### 8.1 Change the Service type

```bash
kubectl patch svc web-service -p '{"spec":{"type":"NodePort"}}'
```

```bash
kubectl get svc web-service -o wide
```

Actual result:

```
NAME          TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
web-service   NodePort   10.106.58.88   <none>        80:32403/TCP   ...
```

The key piece of information is `80:32403/TCP`:

```
Service port          NodePort
     80                  32403
     │                     │
     ▼                     ▼
ClusterIP             every node "listens"
10.106.58.88:80       10.0.0.128:32403 / 10.0.0.164:32403
```

- When `nodePort` is not explicitly specified, K8s randomly allocates one from the default range **30000–32767**.
- The ClusterIP part is completely unchanged — accessing via `http://web-service` keeps working.

### 8.2 The backend did not change

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

The Endpoint is still `10.244.194.75:80`. Changing the Service type only adds an "entry point"; it does not touch the "backend":

```
NodePort :32403     ← the new entry point
ClusterIP :80       ← the original entry point
      │
      ▼
  the same Service / EndpointSlice
      │
      ▼
  10.244.194.75:80 (nginx)
```

### 8.3 Test from inside the nodes

On k8s-master:

```bash
curl http://10.0.0.164:32403   # worker's NodePort → nginx ✅
curl http://10.0.0.128:32403   # master's NodePort → nginx ✅
```

Both returned the nginx page. **The most important point**:

> Hitting the **master**'s NodePort also worked, even though the backend Pod runs on **worker1**.
> NodePort does not require the Pod to live on the node being hit — any node's NodePort
> can feed traffic into the Service, and kube-proxy then delivers it to a backend that may live on a different node.

### 8.4 A common false alarm: `ss` shows nothing on 32403

On a node:

```bash
sudo ss -lntp | grep 32403
# usually no output at all
```

Seeing no LISTEN **does not mean NodePort is broken**. kube-proxy runs in iptables mode by default: it does not start a userspace listener process. Instead it writes DNAT rules into the kernel's netfilter, and the kernel rewrites the destination address directly when a packet passes through PREROUTING / OUTPUT:

```
packet → 10.0.0.164:32403
             │
             │  netfilter (rules written by kube-proxy)
             ▼
         DNAT → 10.244.194.75:80
```

This maps exactly onto the packet processing path in the [iptables doc](../linux/network/iptables_1.md): the NodePort translation happens at the `nat.PREROUTING` position (traffic from outside) / `nat.OUTPUT` position (traffic generated locally). So the most direct way to check whether NodePort works is curl, not `ss`.

---

## 9. Public access: Windows → OCI → NodePort → Pod

### 9.1 The test

From your own computer (Windows), hit the worker's public IP:

```powershell
curl.exe -s -i http://141.148.73.62:32403
```

Actual result:

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 615
```

✅ The full chain works:

```
Windows / Internet
       │
       ▼
OCI Public IP
141.148.73.62
       │  :32403
       ▼
OCI Security List / NSG      ← cloud-layer firewall
       │
       ▼
Ubuntu iptables              ← OS-layer firewall
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

### 9.2 Why this experiment is especially valuable

While troubleshooting kube-apiserver :6443 earlier we already discovered: **OCI allowing traffic ≠ Ubuntu necessarily allowing traffic**.
Traffic from the public internet to a Pod crosses three independent layers:

| Layer | Owner | Status in this run |
| --- | --- | --- |
| OCI Security List / NSG | cloud control plane | allowed (worked with no changes made) |
| Ubuntu iptables / ufw | node OS | allowed |
| Kubernetes (kube-proxy) | cluster | NodePort rules pushed automatically by kube-proxy |

If public access fails, the troubleshooting order should also be **OCI → Ubuntu → Kubernetes**, one layer at a time —
not immediately opening a broad `0.0.0.0/0` rule.

---

## 10. From a bare Pod to a Deployment

### 10.1 Why a Deployment is needed

`web-server` was created by `kubectl run` as a bare Pod. Trying to run two nginx replicas:

```bash
kubectl scale pod web-server --replicas=2
# error: cannot scale ...   ← a Pod has no concept of replicas
```

The standard approach is a clean division of labor:

> **Deployment manages Pods** (replica count / self-healing / rolling updates), **Service manages access to Pods**.

```
Deployment
    ↓
ReplicaSet
    ↓
Pod × N
```

### 10.2 The swap

```bash
kubectl delete pod web-server
kubectl create deployment web-server --image=nginx:1.27 --replicas=2
kubectl get pods -o wide
```

Actual result:

```
NAME                          READY   STATUS    IP              NODE
web-server-7486f6d5b-86zfb    1/1     Running   10.244.194.77   k8s-worker1
web-server-7486f6d5b-b28xh    1/1     Running   10.244.194.78   k8s-worker1
```

Pod names become `deployment-name-replicaset-hash-random-suffix` — no longer the simple name of a bare Pod.

### 10.3 Pitfall: the Service suddenly has no backends

`kubectl get pods --show-labels` shows the new Pods' label is:

```
app=web-server
```

while the Service's selector is still the old one:

```
run=web-server
```

The two don't match → the EndpointSlice goes empty, and the Service has no backends at all.

The value of this pitfall is that it reinforces a core concept:

> ❌ "The Service automatically finds the Pod named web-server"
>
> ✅ **The Service finds Pods by label selector.**
> What the Pod is named doesn't matter — a label mismatch is the fatal part.

### 10.4 Fix: change the selector to the new label

```bash
kubectl patch svc web-service -p '{"spec":{"selector":{"app":"web-server"}}}'
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS
web-service-92fps   IPv4          80      10.244.194.77,10.244.194.78
```

The EndpointSlice now has two backends, and the structure officially becomes:

```
web-service
     │
     ▼
EndpointSlice
     ├── 10.244.194.77:80   (Pod ...-86zfb)
     └── 10.244.194.78:80   (Pod ...-b28xh)
```

Verify recovery:

```bash
kubectl exec client -- wget -qO- http://web-service
# <!DOCTYPE html> ...  ← the Service found backends again
```

---

## 11. Observing load balancing

### 11.1 Give each Pod an identity

The two nginx pages are identical, so you can't tell which Pod a request landed on. Write different content into each:

```bash
kubectl exec web-server-7486f6d5b-86zfb -- \
  sh -c 'echo "Hello from Pod 86zfb - IP 10.244.194.77" > /usr/share/nginx/html/index.html'
kubectl exec web-server-7486f6d5b-b28xh -- \
  sh -c 'echo "Hello from Pod b28xh - IP 10.244.194.78" > /usr/share/nginx/html/index.html'
```

First bypass the Service and verify that these really are two different nginx instances:

```bash
kubectl exec client -- wget -qO- http://10.244.194.77
# Hello from Pod 86zfb - IP 10.244.194.77
kubectl exec client -- wget -qO- http://10.244.194.78
# Hello from Pod b28xh - IP 10.244.194.78
```

### 11.2 Hit the Service many times

```bash
for i in $(seq 1 10); do
  kubectl exec client -- wget -qO- http://web-service
  echo
done
```

Actual result (excerpt):

```
Hello from Pod 86zfb - IP 10.244.194.77
Hello from Pod b28xh - IP 10.244.194.78
Hello from Pod b28xh - IP 10.244.194.78
Hello from Pod b28xh - IP 10.244.194.78
```

Both Pods appeared → the Service really is distributing traffic across multiple Endpoints.

The same test through the public NodePort (Windows PowerShell):

```powershell
for ($i=1; $i -le 10; $i++) {
    curl.exe -s http://141.148.73.62:32403
    Write-Host
}
```

Both Pods' responses show up there too — proving that the whole "public → NodePort → Service → multiple Pods" distribution chain works.

### 11.3 Correcting a common misconception: it is not strict round-robin

Don't expect a strict `77 / 78 / 77 / 78` alternation. In the default iptables mode, kube-proxy picks a backend **at random** (there is no round-robin counter), so `77 / 78 / 78 / 78` is perfectly normal. What to watch for:

- ✅ requests reaching **different** Endpoints
- ❌ not a requirement that every request be a strict 50/50 split

(If you want session persistence or a more even distribution, look into `sessionAffinity`, `internalTrafficPolicy`, IPVS mode, etc. — a topic for later.)

---

## 12. Cross-node backends

### 12.1 Why both Pods are on worker1

```bash
kubectl describe node k8s-master | grep -i taint
# Taints: node-role.kubernetes.io/control-plane:NoSchedule
```

The master carries the `NoSchedule` taint by default, so ordinary Pods are not scheduled onto it — that's why every Pod in 01/02 landed on worker1.

### 12.2 Temporarily remove the taint so the Pods redistribute

```bash
kubectl taint nodes k8s-master node-role.kubernetes.io/control-plane:NoSchedule-
kubectl describe node k8s-master | grep -i taint
# Taints: <none>

kubectl rollout restart deployment web-server
kubectl get pods -l app=web-server -o wide -w    # Ctrl+C once both are Running
```

Actual result:

```
NAME                       READY   STATUS    IP               NODE
web-server-888d69d-824kf   1/1     Running   10.244.194.79    k8s-worker1
web-server-888d69d-sw5c8   1/1     Running   10.244.235.210   k8s-master
```

The scheduler chose freely, and the outcome was one on the Worker and one on the Master — exactly the ideal distribution.

Two things to note:

- Removing the taint does **not** move already-running Pods (the scheduler has no reason to migrate them), so you must `rollout restart` to have the Deployment recreate the Pods.
- The scheduler may also put both Pods on the worker (or both on the master) — all three outcomes are legitimate to it. Don't restart repeatedly just to force a "one worker, one master" split; if you truly want to control placement, the next thing to learn is `nodeSelector` / `nodeAffinity`.

### 12.3 The Pod CIDR is divided per node

```
k8s-worker1  Pod CIDR 10.244.194.0/24  →  10.244.194.79
k8s-master   Pod CIDR 10.244.235.0/24  →  10.244.235.210
```

You can guess which node a Pod is on just from the third octet of its IP (Calico allocates a /24 per node by default).

### 12.4 The Service automatically picks up Pods from both nodes

```bash
kubectl get endpointslice -l kubernetes.io/service-name=web-service
```

```
NAME                ADDRESSTYPE   PORTS   ENDPOINTS
web-service-92fps   IPv4          80      10.244.194.79,10.244.235.210
```

No extra configuration needed: the EndpointSlice only cares about labels, not nodes. Whether cross-node connectivity works is Calico's job (verified in 01); the Service only registers both Pods as backends. At this point the results from 01 and 02 truly merge into a single chain.

---

## 13. Interlude: Pods are ephemeral

After the rollout restart, hitting the Service returns the default nginx page again — the `Hello from Pod ...` content previously written via `kubectl exec` is gone.

Why: restart = delete the old Pod, create a new Pod, and the new Pod runs a **brand-new container** (with the original index.html baked into the image).

> **Pods are ephemeral. Never store state or configuration directly in a Pod's filesystem.**
> In production, use images, ConfigMaps, Secrets, or persistent storage — not `kubectl exec ... > index.html`.

---

## 14. Full chain summary

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
                 NodePort (any node)
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

Verification checklist:

| Item | Source | Status |
| --- | --- | --- |
| Pod → Pod (cross-node) | 01 | ✅ |
| Pod → ClusterIP | this doc §5 | ✅ |
| Service → EndpointSlice | this doc §3 / §7 | ✅ |
| CoreDNS / Service DNS | this doc §7 | ✅ |
| NodePort (master / worker) | this doc §8 | ✅ |
| Public → NodePort → Pod | this doc §9 | ✅ |
| Deployment 2 replicas + multiple Endpoints | this doc §10 | ✅ |
| Load balancing (not strict round-robin) | this doc §11 | ✅ |
| Cross-node backend | this doc §12 | ✅ |

---

## 15. Next

- **Downward API + Pod self-identification**: stop hard-coding IPs; let each Pod report its own identity via Downward API environment variables (`metadata.name` / `spec.nodeName` / `status.podIP`), then hit the NodePort repeatedly from the public internet and watch requests flow between the Master Pod and the Worker Pod with your own eyes.
  - ⚠️ Do not do `kubectl set env deployment/web-server POD_NAME=$(kubectl get pod ...)` — that bakes the first Pod's name into a fixed environment variable shared by all Pods. The correct approach is a `fieldRef` in the Pod spec referencing `metadata.name`, so each Pod injects its own name.
- **Inspect the real kube-proxy rules**: `sudo iptables-save | grep 10.106.58.88` and `sudo iptables-save | grep KUBE-SVC` to find the actual rules kube-proxy wrote on the node, connecting §8.4's theory to what's really in the kernel (echoes the [iptables doc](../linux/network/iptables_1.md)).
- **Cleanup reminder**: once all experiments are done, restore the master's taint:
  ```bash
  kubectl taint nodes k8s-master node-role.kubernetes.io/control-plane:NoSchedule
  ```
- **Declarative YAML**: replace `kubectl create deployment` with `kind: Deployment` + `kind: Service` manifests.

---

## Appendix: Observations

- `web-server` and `client` were both scheduled onto `k8s-worker1` (the master has a taint by default and doesn't run ordinary Pods — see §12).
- `kubectl expose pod` produced the selector `run=web-server` (from the label `kubectl run` applies automatically), while `kubectl create deployment` labels Pods with `app=web-server` — this mismatch is exactly the root cause of the §10.3 pitfall.
- ClusterIP `10.106.58.88` falls inside kubeadm's default Service CIDR `10.96.0.0/12`, which does not overlap with the Pod CIDR `10.244.0.0/16`.
- NodePort `32403` is within the default range 30000–32767; both nodes use the same NodePort number (32403).
- `kubectl patch` takes effect immediately when changing the Service type / selector, and the ClusterIP stays the same.
- The public test went through the worker's OCI Public IP `141.148.73.62`; no firewall changes were made for 32403 beforehand and it still worked, meaning OCI/OS-layer allow rules already existed (if a different port fails, troubleshoot in the §9.2 order).
- The rollout restart produced a new ReplicaSet (`7486f6d5b` → `888d69d`), and the manual changes on the old Pods vanished together with them (§13).
