# kubernetes-hello-world

### Commands

- Installed [kind](https://kind.sigs.k8s.io/docs/user/quick-start/) `choco install kind` in Windows
- Installed [k8](https://www.letscloud.io/community/how-to-install-kubernetesk8s-and-docker-on-ubuntu-2004) on Ubuntu
- `kind create cluster --name=esquenta`
- `kubectl cluster-info --context kind-esquenta`

```bash
    leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl get nodes
    NAME                     STATUS   ROLES           AGE     VERSION
    esquenta-control-plane   Ready    control-plane   9m24s   v1.26.
```

- Create a new file called pod.yaml

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
  name: nginx
  labels:
      name: nginx
  spec:
  containers:
  - name: nginx
      image: nginx
      ports:
      - containerPort: 80

  ```

```bash
    leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl apply -f pod.yaml
    pod/nginx created
    leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl get pods
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          23s
    leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl port-forward pod/nginx 9090:80
    Forwarding from 127.0.0.1:9090 -> 80
    Forwarding from [::1]:9090 -> 80
    leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl delete pod nginx
    pod "nginx" deleted
    leomozzer@leo:~/projects/kubernetes-hello-world$
```

- ReplicaSet: create a new file called ReplicaSet.yaml
  - The structure will be the same as the but it's like a definition of pods that must be available when running the k8
  ```yaml
  apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
    name: nginx
    spec:
    replicas: 2 #Number of replicas
    selector:
        matchLabels:
        app: nginx
    template:
        metadata:
        labels:
            app: nginx
        spec:
        containers:
        - name: nginx
            image: nginx
            ports:
            - containerPort: 80
  ```

```bash
leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl apply -f ReplicaSet.yaml
replicaset.apps/nginx created
leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl get pod
NAME          READY   STATUS    RESTARTS   AGE
nginx-ttcrq   1/1     Running   0          44s
## Get RepliacaSet
leomozzer@leo:~/projects/kubernetes-hello-world$ kubectl get rs
NAME    DESIRED   CURRENT   READY   AGE
nginx   1         1         1       83s
```

- Create the deployment that will responsible to manage the repliacaset

```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: nginx
    spec:
    replicas: 2
    selector:
        matchLabels:
        app: nginx
    template:
        metadata:
        labels:
            app: nginx
        spec:
        containers:
        - name: nginx
            image: nginx
            ports:
            - containerPort: 80
```
