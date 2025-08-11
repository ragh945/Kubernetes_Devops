# Kubernetes Scheduling & Workload Examples

## 1. nodeSelector
**Definition:** Easiest way to tell Kubernetes to run a pod **only on nodes with specific labels**.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nodeselector
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - name: myapp
    image: nginx
    image: nginx

💡 Requires the node to have label:
kubectl label nodes <node-name> disktype=ssd
```

## 2. nodeName
**Definition:** Directly schedule a pod on a specific node (bypasses scheduler).

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-nodename
spec:
  nodeName: worker-node-1
  containers:
  - name: myapp
    image: nginx
```
## 3. Node Affinity
**Definition:** More flexible & expressive version of nodeSelector, supports required/preferred rules.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: myapp
    image: nginx
```
## 4. ReplicaSet
**Definition:** Ensures a fixed number of identical pods are running at all times.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx
```
## 5. Deployment
**Definition:** Manages ReplicaSets and Pods, supports updates & rollbacks.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx:1.25

```
💡 Check rollout status:
```bash
kubectl rollout status deployment/my-deployment
```

## 6. DaemonSet
**Definition:** Ensures one pod runs on every node (or on specific nodes) in the cluster.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      app: daemon-app
  template:
    metadata:
      labels:
        app: daemon-app
    spec:
      containers:
      - name: daemon-container
        image: nginx
```
## 💡 Commonly used for:

- Logging agents

- Monitoring agents

- Node-level daemons (e.g., kube-proxy)
  
# Why they are used ?

```
1. nodeSelector
Why used? To run your pod only on nodes with a certain label.

Example: Put high-IO apps only on SSD nodes.

Analogy: “I want any house, but it must have a garden.”

2. nodeName
Why used? To force a pod onto a specific node by name.

Example: Troubleshooting or when a node has special hardware.

Analogy: “I want to live in this exact house, no matter what.”

3. Node Affinity
Why used? For advanced scheduling rules — can say must, should, or prefer not.

Example: Prefer GPU nodes, but still allow other nodes if GPUs are busy.

Analogy: “I prefer houses near a park, but I’ll take another if none are available.”

4. ReplicaSet
Why used? To keep a fixed number of pods running at all times.

Example: Web app always needs 3 copies for load balancing.

Analogy: “I always want 3 delivery drivers on duty — if one quits, hire another.”

5. Deployment
Why used? To manage updates to your app without downtime and automatically handle ReplicaSets.

Example: Upgrade Nginx from v1.25 to v1.26 safely.

Analogy: “I run the delivery team, hire new drivers, train them, and let old ones go — smoothly.”

```

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/58c1cab8-aff7-4700-bb2b-2cee7813b208" />


