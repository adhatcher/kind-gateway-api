# Introduction to Local Kubernetes Using Kind

## GitHub Repo

- [https://github.com/adhatcher/kind-gateway-api/tree/main/1-install-kind](https://github.com/adhatcher/kind-gateway-api/tree/main/1-install-kind)

## Background

This repo will creat a KinD cluster on a Mac and configure gateway api with cloud-provider-kind local load balancer to allow you to access the applications on the cluster.

You can find the Kind source code here:

- [https://github.com/kubernetes-sigs/kind](https://github.com/kubernetes-sigs/kind)

and the documentation here:

- [https://kind.sigs.k8s.io/](https://kind.sigs.k8s.io/)

Cloud Provider Kind loadbalancer:

- [https://kind.sigs.k8s.io/docs/user/loadbalancer/](https://kind.sigs.k8s.io/docs/user/loadbalancer/)
- [https://github.com/kubernetes-sigs/cloud-provider-kind(https://github.com/kubernetes-sigs/cloud-provider-kind)]

## Prerequisites

### General Knowledge

- `docker`
- `brew` (or your preferred package manager)
- YAML
- `helm`
- `kubectl`

### Install and Setup

#### Docker

Have `docker` installed and have a docker engine running.

You can check your `docker` version with the following command:

```sh
docker --version
```

```sh
Docker version 29.1.3, build f52814d
```

Make sure your `docker` engine is running.
You can check if it is with the following command:

```sh
docker info > /dev/null 2>&1
```

```sh
echo $?
```

```sh
0  # if it isn't running you should see a 1
```

#### `kind`

I recommend installing using a package manager such as `brew` for macOS:

```sh
brew install kind
which kind
```

Sample output

```sh
/opt/homebrew/bin/kind
```

Verify kind version:

```sh
kind --version
```

Sample output

```sh
kind version 0.30.0
```

## Creating a cluster with a yaml config file

- [https://kind.sigs.k8s.io/docs/user/configuration/](https://kind.sigs.k8s.io/docs/user/configuration/)

## Kind Cluster

This will create a kind cluster using a `kind-config.yml` file. This file will define the name of the cluster, as well as 3 nodes. One `control-plane` node and 2 `worker` nodes.

> At least one `control-plane` node is required. Failure to include a `control-plane` node will result in the following error:

 `ERROR: failed to create cluster: must have at least one control-plane node`

### Kind Config

Make sure to check the newest version, you can find the images here:

- [https://hub.docker.com/r/kindest/node/](https://hub.docker.com/r/kindest/node/)

I will create a file named `kind-config.yaml` and place the following contents in it:

```yaml
---
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: gwkc-1
nodes:
  - role: control-plane
    image: kindest/node:v1.34.0
  - role: worker
    image: kindest/node:v1.34.0
```

Here we are creating three nodes, one control-plane node and two worker nodes.

```sh
$ kind create cluster --config=./kind-config.yaml
...
```

<details>
  <summary>Kind cluster build details</summary>

```sh
Creating cluster "gwkc-1" ...
 ✓ Ensuring node image (kindest/node:v1.35.0) 🖼
 ✓ Preparing nodes 📦 📦 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
 ✓ Joining worker nodes 🚜 
Set kubectl context to "kind-gwkc-1"
You can now use your cluster with:

kubectl cluster-info --context kind-gwkc-1

Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
```

</details>

*You can use a tool such as [kubectx](https://github.com/ahmetb/kubectx) to ensure your context is set.*

```sh
kubectx
kind-gwkc-1
```

Notice how the name is prefixed with `kind-`

### See the nodes running

#### Use `docker ps` to see the nodes running

<details>
 <summary>Click to view docker containers</summary>

```sh
docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                       NAMES
43aafd94b5c6   kindest/node:v1.35.0   "/usr/local/bin/entr…"   4 minutes ago   Up 4 minutes                               gwkc-1-worker
fa96de369425   kindest/node:v1.35.0   "/usr/local/bin/entr…"   4 minutes ago   Up 4 minutes                               gwkc-1-worker2
f58ed12cd7cc   kindest/node:v1.35.0   "/usr/local/bin/entr…"   4 minutes ago   Up 4 minutes   127.0.0.1:59456->6443/tcp   gwkc-1-control-plane
```

</details>

#### Use `kubectl get nodes` to view the kubernetes nodes

<details>
 <summary>Click to view the Kubernetes Nodes</summary>
 
 ```sh
kubectl get nodes
NAME                   STATUS   ROLES           AGE     VERSION
gwkc-1-control-plane   Ready    control-plane   6m11s   v1.35.0
gwkc-1-worker          Ready    <none>          6m2s    v1.35.0
gwkc-1-worker2         Ready    <none>          6m2s    v1.35.0
```

</details>

If you would like to apply `ROLES` to the `worker nodes`, run the below command:

```sh
$ kubectl label nodes gwkc-1-worker node-role.kubernetes.io/worker=""
node/gwkc-1-worker labeled
$ kubectl label nodes gwkc-1-worker2 node-role.kubernetes.io/worker=""
node/gwkc-1-worker2 labeled

$ kubectl get nodes                                                   
NAME                   STATUS   ROLES           AGE     VERSION
gwkc-1-control-plane   Ready    control-plane   8m25s   v1.35.0
gwkc-1-worker          Ready    worker          8m16s   v1.35.0
gwkc-1-worker2         Ready    worker          8m16s   v1.35.0
```

### Updating the Cluster

> **Note**: You cannot update a cluster after it's created. If you need to make any changes, you will need to delete the cluster and recreate it with the updated config file.


### Setting up `cloud-provider-kind`

`cloud-provider-kind` allows you to use a [Kubernetes Gateway](https://kubernetes.io/docs/concepts/services-networking/gateway/#api-kind-gateway) resource and get an assignable IP address

#### Install

```sh
$ brew install cloud-provider-kind
$ which cloud-provider-kind
/opt/homebrew/bin/cloud-provider-kind
```

#### Run

You can either run this process in a separate terminal, or run it using nohup in the background.  **Note:** This needs to run via `sudo` based on the [README.md](https://github.com/kubernetes-sigs/cloud-provider-kind/blob/main/README.md)

```sh
$ sudo cloud-provider-kind --gateway-channel standard
```

To run in the background using nohup:

```sh
nohup sudo cloud-provider-kind --gateway-channel standard >/dev/null 2>&1 &
```

You can now see a [GatewayClass](https://kubernetes.io/docs/concepts/services-networking/gateway/#api-kind-gateway-class) was created for us in the cluster:

```sh
$ kubectl get GatewayClass
NAME                  CONTROLLER                            ACCEPTED   AGE
cloud-provider-kind   kind.sigs.k8s.io/gateway-controller   True       5m1s
```


### Deploying the Gateway Resource

I will create a file called `gateway.yaml` with the following contents:

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: gateway
---
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: gateway
  namespace: gateway
spec:
  gatewayClassName: cloud-provider-kind
  listeners:
    - name: http
      protocol: HTTP
      port: 80
      hostname: "kind.local"
      allowedRoutes:
        namespaces:
          from: All
```

Notice how the `gatewayClassName` references the `GatewayClass` that was created for us shown in the previous step.

Deploy with the following:

```sh
$ kubectl apply -f gateway.yaml
```

If you see this message:
```sh
error: resource mapping not found for name: "gateway" namespace: "gateway" from "gateway.yaml": no matches for kind "Gateway" in version "gateway.networking.k8s.io/v1"
ensure CRDs are installed first
```

Just run the apply command again, the `cloud-provider-kind` service may not have been running fully.

You can now see the `gateway` created in your cluster in the gateway namespace

```sh
kubectl get gateway -n gateway
NAME      CLASS                 ADDRESS      PROGRAMMED   AGE
gateway   cloud-provider-kind   172.18.0.5   True         114s
```

as well as the envoy-proxy container running in docker

```sh
$ docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                                                                  NAMES
da86a979198f   envoyproxy/envoy:v1.33.2   "/docker-entrypoint.…"   7 seconds ago   Up 6 seconds   0.0.0.0:59978->80/tcp, [::]:59978->80/tcp, 0.0.0.0:55017->10000/tcp, [::]:55017->10000/tcp   kindccm-gw-a73b0c99571a
...
```

To get the IP address for your gateawy:

```sh
$ kubectl get gateway/gateway -n gateway | awk '{print $3}' | awk NR==2
172.18.0.4
```

##### Use `curl` to hit the `Gateway`

```sh
$ curl http://172.18.0.5 -I
HTTP/1.1 404 Not Found
date: Thu, 15 Jan 2026 19:03:49 GMT
server: envoy
transfer-encoding: chunked
```

You can watch the log information when a Kubernetes `Gateway` is created or deleted.

### Configure `/etc/hosts`

Using the IP address we got from:
```sh
$ kubectl get gateway/gateway -n gateway | awk '{print $3}' | awk NR==2
```

We can update `/etc/hosts` to have a nice looking URL.

```sh
$ sudo vim /etc/hosts
...
172.18.0.5      kind.local
```

Now you can go to http://kind.local

### Deploy a Sample App and use the Gateway api

I will be using the [hashicorp http-echo](https://github.com/hashicorp/http-echo) server has my example app.

You can find the images [here](https://hub.docker.com/r/hashicorp/http-echo).

Create a file called `app.yaml` and add the following manifests in it:

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: app
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-echo-deployment
  namespace: app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: http-echo
  template:
    metadata:
      labels:
        app: http-echo
    spec:
      containers:
        - name: http-echo
          image: hashicorp/http-echo:1.0.0
          args: ["-text", "Hello from Kubernetes!", "-listen", ":8080"]
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: http-echo-service
  namespace: app
spec:
  selector:
    app: http-echo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: http-echo-route
  namespace: app
spec:
  parentRefs:
    - name: gateway
      namespace: gateway
  hostnames:
    - "kind.local"
  rules:
    - backendRefs:
        - name: http-echo-service
          port: 80
```

```sh
$ kubectl apply -f app.yaml
namespace/app created
deployment.apps/http-echo-deployment created
service/http-echo-service created
httproute.gateway.networking.k8s.io/http-echo-route created
```

You will now see the `HTTPRoute` that was created for the app.

```sh
NAME              HOSTNAMES        AGE
http-echo-route   ["kind.local"]   86s
```

You can view the page via [http://kind.local](http://kind.local) or via a curl call `curl http://kind.local`.  YOu can also access the page via curl call to the IP address, passing the kind.local header: `curl -H "Host: kind.local" http://172.18.0.5`

Go to http://kind.local and see the message!

### Cleaning up

Stop your `cloud-provider-kind` by Ctrl+C'ing the terminal or by looking the processes and stopping it. If you ran it in the background, kill it with `sudo pkill cloud-provider-kind`

Delete your KinD cluster:

```sh
$ kind delete cluster --name kind-gwkc-1
```

Remove the gateway ip from your `/etc/hosts` file.

### Deleting the cluster

```sh
$ kind delete cluster --name kind-gwkc-1
```

## TLDR

You can use the included `Taskfile.yml` to create the kind cluster using `go-task`.
Install `go-task`

```sh
$ brew install go-task/tap/go-task
```

Create

```sh
$ task
```

Delete
```sh
$ task clean
```
