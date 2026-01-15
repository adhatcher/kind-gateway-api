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

Create the Kind Cluster

```sh
$ task
```

Delete the cluster using `task`
```sh
$ task clean
```
