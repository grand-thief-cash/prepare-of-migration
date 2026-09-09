# 01 - Create a Kubernetes Cluster (kubeadm, 2 Nodes)

## 0. Goal

```
                    your computer
                       │
                       │ SSH
                       ▼
              ┌──────────────────┐
              │   Node 1         │
              │                  │
              │ Control Plane    │
              │ kube-apiserver   │
              │ scheduler        │
              │ controller       │
              │ etcd             │
              │                  │
              │ Worker           │
              │ kubelet          │
              │ containerd       │
              └────────┬─────────┘
                       │
                       │ Kubernetes Network
                       │
              ┌────────▼─────────┐
              │   Node 2         │
              │                  │
              │ Worker           │
              │ kubelet          │
              │ containerd       │
              │                  │
              │ Pods             │
              └──────────────────┘
```

Deploy routine: **Ubuntu + containerd + kubeadm + kubelet + kubectl + CNI**

Roadmap:

```
Done ✓
   │
   ▼
kubeadm init
   │
   ▼
Control Plane ✓
   │
   ▼
Install Calico CNI       ← this doc
   │
   ▼
Node Ready
   │
   ▼
worker kubeadm join
   │
   ▼
2 Node Cluster
```

Later episodes (covered in later articles):

```
kubectl
   │
   ▼
API Server
   │
   ├── Scheduler
   │
   ├── Controller Manager
   │
   └── etcd
        │
        ▼
      Nodes
        │
        ├── kubelet
        │
        ├── containerd
        │
        └── Pods
```

Knowledge covered:

- kubeadm / kubelet / kubectl
- containerd
- CNI (Calico)
- CoreDNS
- etcd / API Server / Scheduler / Controller Manager

---

## 1. Machines

```
Oracle Cloud
    ↓
Ubuntu ARM64
    ↓
Node (1 OCPU, 6GB RAM, 50GB Disk) * 2
```

---

## 2. Node Checklist

Run through this on both VMs.

### 2.1 SSH reachable
Both machines can be logged into via ssh.

### 2.2 OS version
```bash
cat /etc/os-release
```
```
PRETTY_NAME="Ubuntu 24.04.4 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.4 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
```

### 2.3 Hardware architecture
```bash
uname -m
# aarch64
```

### 2.4 CPU count
```bash
nproc
# 1   (kubeadm init will fail later because < 2, see §7)
```

### 2.5 Memory / Swap
```bash
free -h
# Mem: 5.8Gi
```
```bash
swapon --show
# no output = swap is off, which is exactly what k8s requires — nothing to do here
```

### 2.6 Hostname
```bash
hostname
# instance-20260831-1410
```

### 2.7 IP
```bash
ip addr
# node1: 10.0.0.128/24  (enp0s6)
# node2: 10.0.0.164/24  (enp0s6)
```

### 2.8 Node-to-node connectivity
```bash
ping -c 4 10.0.0.164
# 4 packets transmitted, 4 received, 0% packet loss
```

> If ping fails: OCI Console → Networking → VCN → Security Lists →
> Ingress Rules, add:
> ```
> Source CIDR: 10.0.0.0/24
> IP Protocol: ICMP
> Type: 8
> Code: 0
> ```

---

## 3. Basic Setup

### 3.1 Set hostname
```bash
# node1
sudo hostnamectl set-hostname k8s-master
# node2
sudo hostnamectl set-hostname k8s-worker1
```

### 3.2 Update the system
```bash
sudo apt update
sudo apt upgrade -y
sudo reboot
```

---

## 4. Install containerd

### 4.1 Why containerd

Docker ≠ Kubernetes. K8s talks to the container runtime via CRI:

```
                 Kubernetes
                     │
                  kubelet
                     │
                    CRI
                     │
                     ▼
                 containerd
                     │
                    runc
                     │
                     ▼
                  Linux
```

The full request chain:

```
kubectl → API Server → Scheduler → kubelet → containerd → container
```

### 4.2 Install
```bash
sudo apt install -y containerd
containerd --version
# containerd github.com/containerd/containerd/v2 2.2.1
```

### 4.3 Configure SystemdCgroup
```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo nano /etc/containerd/config.toml
# Ctrl+W to search for SystemdCgroup, change it to SystemdCgroup = true
```

Restart and verify:
```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
sudo systemctl status containerd
grep SystemdCgroup /etc/containerd/config.toml
# SystemdCgroup = true
```

---

## 5. Install Kubernetes Components

We pick `v1.34` (the latest as of 2026-08 is 1.37; v1.34 chosen for compatibility).

### 5.1 Confirm the CRI plugin is available

K8s 1.26+ requires the runtime to support CRI V1, otherwise kubelet cannot register as a worker node:
```bash
sudo ctr plugins ls | grep -E 'io.containerd.cri|TYPE'
# io.containerd.cri.v1   images    -                ok
# io.containerd.cri.v1   runtime   linux/arm64/v8   ok
```

### 5.2 Add the apt repository
```bash
sudo apt install -y apt-transport-https ca-certificates curl gpg

sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key \
  | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' \
  | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

sudo apt update
```

Confirm the repo is usable:
```bash
apt-cache madison kubeadm
# kubeadm | 1.34.11-1.1 | https://pkgs.k8s.io/core:/stable:/v1.34/deb  Packages
# ...
```

### 5.3 Install the trio

- **kubeadm**: creates/joins clusters
- **kubelet**: the K8s agent on every node
- **kubectl**: the CLI for operating the cluster

```bash
sudo apt install -y \
  kubelet=1.34.11-1.1 \
  kubeadm=1.34.11-1.1 \
  kubectl=1.34.11-1.1
```

Verify:
```bash
kubeadm version    # v1.34.11
kubectl version --client
kubelet --version
```

### 5.4 Hold the versions

K8s has its own upgrade flow and doesn't rely on apt, so hold the packages:
```bash
sudo apt-mark hold kubelet kubeadm kubectl
apt-mark showhold
```

### 5.5 Enable kubelet

```bash
sudo systemctl enable kubelet
sudo systemctl status kubelet
# Active: inactive (dead)  ← this is normal!
```
> kubelet won't turn Active until `kubeadm init` / `kubeadm join` runs.

### 5.6 Current state snapshot

```
                 Oracle Cloud VCN
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
   k8s-master                 k8s-worker1
   10.0.0.128                 10.0.0.164
   ARM64 / 1 CPU / 5.8 GB     ARM64 / 1 CPU / 5.8 GB
          │                         │
          ├─ containerd 2.2.1      ├─ containerd 2.2.1
          ├─ CRI v1 ✓              ├─ CRI v1 ✓
          ├─ kubeadm 1.34.11       ├─ kubeadm 1.34.11
          ├─ kubelet 1.34.11       ├─ kubelet 1.34.11
          └─ kubectl 1.34.11       └─ kubectl 1.34.11
```

---

## 6. Kernel & Network Preparation

### 6.1 What K8s requires from the network

Pod A (10.244.0.2) ↔ Pod B (10.244.1.3) must be able to reach each other. The essence of the Pod network is Linux network namespaces:

```
                    Linux
                      │
              ┌───────┴───────┐
              │               │
           eth0            cni0 / ...
              │               │
              │          ┌────┴────┐
              │          │         │
            Host       Pod A     Pod B
```

This involves: bridge, iptables / nftables, forwarding, netfilter.

### 6.2 Load kernel modules
```bash
sudo modprobe overlay
sudo modprobe br_netfilter
lsmod | grep -E 'overlay|br_netfilter'
```

**Why these two modules?**

- `overlay`: container images generally use OverlayFS (lower layers + writable layer).
- `br_netfilter`: the Pod network goes through a Linux bridge (Pod → veth → bridge → eth0),
  and K8s network rules rely on netfilter/iptables to handle traffic on the bridge.

Persist them (so they survive a reboot):
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

### 6.3 Configure sysctl
```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
```

- `net.ipv4.ip_forward = 1`: allows Linux to forward IP packets (Pod → eth0 → other nodes); required for cross-node Pod communication.
- `bridge-nf-call-iptables`: sends bridged traffic through netfilter/iptables; Service / IPVS / CNI / NetworkPolicy all depend on it.

---

## 7. kubeadm init

### 7.1 Network planning

Using Calico:
- Node network: `10.0.0.0/24` (Oracle VCN)
- Pod network: `10.244.0.0/16`

```
Oracle VCN 10.0.0.x
     │
     ▼
 Kubernetes Nodes
     │
     ▼
 Pod Network 10.244.x.x
```

### 7.2 Initialize (on the master node)

Running it directly fails (only 1 CPU; the preflight check requires 2):
```
[preflight] Some fatal errors occurred:
        [ERROR NumCPU]: the number of available CPUs 1 is less than the required 2
```

Skip that check:
```bash
sudo kubeadm init \
  --apiserver-advertise-address=10.0.0.128 \
  --pod-network-cidr=10.244.0.0/16 \
  --ignore-preflight-errors=NumCPU
```

Successful output (key parts):
```
Your Kubernetes control-plane has initialized successfully!

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubeadm join 10.0.0.128:6443 --token xxx --discovery-token-ca-cert-hash sha256:xxx
```

Configure kubectl as instructed:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 7.3 Observing the state: NotReady

```bash
kubectl get nodes
# NAME         STATUS     ROLES           AGE     VERSION
# k8s-master   NotReady   control-plane   2m43s   v1.34.11

kubectl get pods -A
# etcd / kube-apiserver / kube-controller-manager / kube-scheduler / kube-proxy → Running
# coredns → Pending
```

The Control Plane is up, but the node is **NotReady** and CoreDNS is **Pending** —
because the cluster has no Pod network (CNI) yet:

```
                    Kubernetes
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
       Control Plane           Pod Network
             ✓                     ❌
```

---

## 8. Install Calico CNI

### 8.1 What is CNI

CNI (Container Network Interface) is a standard interface for configuring Pod networking:

```
CNI
 ├── allocates a Pod IP
 ├── creates network interfaces
 ├── attaches the Pod to the network
 └── sets up the related routes
```

It is a standard/interface with multiple implementations: Calico / Cilium / Flannel / ... This doc uses **Calico**.

### 8.2 Tigera Operator

Tigera Operator ≠ Calico CNI itself. It is a K8s Operator (think of it as a robot) that manages the lifecycle of Calico: watches state, creates/modifies resources, detects failures, self-heals.

Ordinary K8s apps: `Deployment → Pod`;
an Operator continuously watches and reconciles.

### 8.3 Install the Operator
```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.31.3/manifests/tigera-operator.yaml

kubectl get pods -n tigera-operator
# tigera-operator-85979684d8-p8rpd   1/1   Running
```

### 8.4 Create the Installation

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.31.3/manifests/custom-resources.yaml
# installation.operator.tigera.io/default created
# apiserver.operator.tigera.io/default created
# goldmane.operator.tigera.io/default created
# whisker.operator.tigera.io/default created
```

This creates Calico's default installation, including:
- **Calico Node**: runs on every node, manages the Pod network
- **Calico Typha**: optional component that scales Calico in large clusters
- **Calico API Server**: manages the API for Calico resources
- **Calico Kube Controllers**: manages the Calico resources in the K8s API

### 8.5 Fix the Pod CIDR

The default cidr in `custom-resources.yaml` is `192.168.0.0/16`; change it to the one we used at init:
```bash
kubectl get installation.operator.tigera.io default -o yaml
# change cidr: 192.168.0.0/16 to 10.244.0.0/16 (the value of --pod-network-cidr)
```

### 8.6 Verify

```bash
kubectl get nodes
# k8s-master   Ready   control-plane
```

All components Running (CoreDNS is up too, with a 10.244.x.x Pod IP):
```
calico-system     calico-apiserver-...        1/1  Running  10.244.235.199
calico-system     calico-node-...             1/1  Running  10.0.0.128
calico-system     calico-typha-...            1/1  Running  10.0.0.128
kube-system       coredns-...                 1/1  Running  10.244.235.195
kube-system       etcd / apiserver / ...      1/1  Running
tigera-operator   tigera-operator-...         1/1  Running
```

---

## 9. Join Worker Node

Generate the join command on the master (the token is valid for 24h by default; regenerate if expired):
```bash
kubeadm token create --print-join-command
# kubeadm join 10.0.0.128:6443 --token 5xgw95.xxx --discovery-token-ca-cert-hash sha256:xxx
```

Run on worker1 (note the trailing `\` on every line):
```bash
sudo kubeadm join 10.0.0.128:6443 \
  --token 5xgw95.9ap96urbcacm0pnc \
  --discovery-token-ca-cert-hash sha256:0cf88871422118a01804c1126e48a934784c6305a3d36d6400059802d2abe6e1
```

Success marker:
```
This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.
```

```bash
kubectl get nodes
# NAME         STATUS   ROLES           AGE   VERSION
# k8s-master   Ready    control-plane   ...
# k8s-worker1  Ready    <none>          ...
```

---

## 10. Verify Pod Network

Create two test Pods (on worker1):
```bash
kubectl run net-test --image=busybox:1.36 --restart=Never -- sleep 86400
kubectl run net-test-2 --image=busybox:1.36 --restart=Never -- sleep 86400
```

Pod → Pod cross-node connectivity:
```bash
kubectl exec net-test-2 -- ping -c 3 10.244.194.73
# 3 packets transmitted, 3 packets received, 0% packet loss
```

---

## 11. Next

Next episode: [02 - Service Networking: ClusterIP → NodePort → Public Access](02_service_network.md) — verify Pod → Service → Pod, then extend it all the way to NodePort, the public entry point, and Deployment multi-replica.

## Appendix: Questions / TODO

- [ ] Pod → Node / Node → Pod connectivity hasn't been verified yet (left blank in the original; move to §10 after supplementing the commands).
- Q: Why does `kubectl get pods -o wide` only list our two Pods (net-test and net-test-2) and not the kube-system Pods?
  A: `kubectl get pods` only looks at the **default namespace** by default. Other Pods live in `kube-system` / `calico-system` / `tigera-operator` etc.; use `kubectl get pods -A` (or `-n <namespace>`) to see them all.
