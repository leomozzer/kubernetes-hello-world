# kubernetes-hello-world

## 'kind' estructure

## Commands

- Using KinD to local development: `kind create cluster --name=kluster`
- Set context: `kubectl cluster-info --context kind-kluster`
- Start: kubectl apply -f {file_name_or_folder_where_files_are}
- Get all: `kubectl get all`

```bash
leomozzer@leo:~/projects/kubernetes-hello-world/kubernetes$ kubectl get all
NAME                                      READY   STATUS    RESTARTS     AGE
pod/mongo-express-5bcd46fcff-6d78q        1/1     Running   1 (3s ago)   5s
pod/mongodb-deployment-5d966bd9d6-vc56s   1/1     Running   0            9s

NAME                            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes              ClusterIP      10.96.0.1       <none>        443/TCP          72m
service/mongo-express-service   LoadBalancer   10.96.163.29    <pending>     8081:30000/TCP   24m
service/mongodb-service         ClusterIP      10.96.115.116   <none>        27017/TCP        27m
service/mongodb-services        ClusterIP      10.96.87.174    <none>        27017/TCP        55m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongo-express        1/1     1            1           5s
deployment.apps/mongodb-deployment   1/1     1            1           9s

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/mongo-express-5bcd46fcff        1         1         1       5s
replicaset.apps/mongodb-deployment-5d966bd9d6   1         1         1       9s
```

- Get all services: `kubectl get service`
- Get logs from a pod: kubectl logs pod/{pod_name}
- Forward the requests from port 30000 to 8081 of the service: `kubectl port-forward service/mongo-express-service 30000:8081`
- Delete all the deployments: `kubectl delete deployments --all`

## Requirements

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
