# Instructions for installing ArgoCD on a KinD Cluster

These instructions are for installing ArgoCD on a KinD cluster running on Docker on a Mac, using cloud-provider-kind for the loadbalancer.

## Requirements

Have a Kind cluster up and running with gateaway api and a `cloud-provider-kind` loadbalancer configured.  Example can be found here:[https://github.com/adhatcher/kind-gateway-api](https://github.com/adhatcher/kind-gateway-api)

## Create the argocd namespace

```sh
$ k create namespace argocd
namespace/argocd created

% kubens argocd
Context "kind-gwkc-1" modified.
Active namespace is "argocd".
```

## Install ArgoCD

```sh
$ kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<details>
  <summary>Click to view results</summary>

```sh
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io serverside-applied
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io serverside-applied
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io serverside-applied
serviceaccount/argocd-application-controller serverside-applied
serviceaccount/argocd-applicationset-controller serverside-applied
serviceaccount/argocd-dex-server serverside-applied
serviceaccount/argocd-notifications-controller serverside-applied
serviceaccount/argocd-redis serverside-applied
serviceaccount/argocd-repo-server serverside-applied
serviceaccount/argocd-server serverside-applied
role.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-dex-server serverside-applied
role.rbac.authorization.k8s.io/argocd-notifications-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-redis serverside-applied
role.rbac.authorization.k8s.io/argocd-server serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-server serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-dex-server serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-redis serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-server serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-server serverside-applied
configmap/argocd-cm serverside-applied
configmap/argocd-cmd-params-cm serverside-applied
configmap/argocd-gpg-keys-cm serverside-applied
configmap/argocd-notifications-cm serverside-applied
configmap/argocd-rbac-cm serverside-applied
configmap/argocd-ssh-known-hosts-cm serverside-applied
configmap/argocd-tls-certs-cm serverside-applied
secret/argocd-notifications-secret serverside-applied
secret/argocd-secret serverside-applied
service/argocd-applicationset-controller serverside-applied
service/argocd-dex-server serverside-applied
service/argocd-metrics serverside-applied
service/argocd-notifications-controller-metrics serverside-applied
service/argocd-redis serverside-applied
service/argocd-repo-server serverside-applied
service/argocd-server serverside-applied
service/argocd-server-metrics serverside-applied
deployment.apps/argocd-applicationset-controller serverside-applied
deployment.apps/argocd-dex-server serverside-applied
deployment.apps/argocd-notifications-controller serverside-applied
deployment.apps/argocd-redis serverside-applied
deployment.apps/argocd-repo-server serverside-applied
deployment.apps/argocd-server serverside-applied
statefulset.apps/argocd-application-controller serverside-applied
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-redis-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-server-network-policy serverside-applied

% kubectl get all 
NAME                                                    READY   STATUS              RESTARTS   AGE
pod/argocd-application-controller-0                     0/1     ContainerCreating   0          18s
pod/argocd-applicationset-controller-6455d7fb95-4ltpm   0/1     ContainerCreating   0          18s
pod/argocd-dex-server-7fbf8d44c9-59hw2                  0/1     Init:0/1            0          18s
pod/argocd-notifications-controller-d468945d4-t5ct4     0/1     ContainerCreating   0          18s
pod/argocd-redis-544d668bf8-8c7kg                       0/1     Init:0/1            0          18s
pod/argocd-repo-server-797c498f68-hjbf7                 0/1     Init:0/1            0          18s
pod/argocd-server-8f496c9d7-nzz26                       0/1     ContainerCreating   0          18s

NAME                                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/argocd-applicationset-controller          ClusterIP   10.96.233.59    <none>        7000/TCP,8080/TCP            18s
service/argocd-dex-server                         ClusterIP   10.96.158.49    <none>        5556/TCP,5557/TCP,5558/TCP   18s
service/argocd-metrics                            ClusterIP   10.96.22.41     <none>        8082/TCP                     18s
service/argocd-notifications-controller-metrics   ClusterIP   10.96.157.138   <none>        9001/TCP                     18s
service/argocd-redis                              ClusterIP   10.96.6.47      <none>        6379/TCP                     18s
service/argocd-repo-server                        ClusterIP   10.96.107.139   <none>        8081/TCP,8084/TCP            18s
service/argocd-server                             ClusterIP   10.96.252.103   <none>        80/TCP,443/TCP               18s
service/argocd-server-metrics                     ClusterIP   10.96.3.39      <none>        8083/TCP                     18s

NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller   0/1     1            0           18s
deployment.apps/argocd-dex-server                  0/1     1            0           18s
deployment.apps/argocd-notifications-controller    0/1     1            0           18s
deployment.apps/argocd-redis                       0/1     1            0           18s
deployment.apps/argocd-repo-server                 0/1     1            0           18s
deployment.apps/argocd-server                      0/1     1            0           18s

NAME                                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-6455d7fb95   1         1         0       18s
replicaset.apps/argocd-dex-server-7fbf8d44c9                  1         1         0       18s
replicaset.apps/argocd-notifications-controller-d468945d4     1         1         0       18s
replicaset.apps/argocd-redis-544d668bf8                       1         1         0       18s
replicaset.apps/argocd-repo-server-797c498f68                 1         1         0       18s
replicaset.apps/argocd-server-8f496c9d7                       1         1         0       18s

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   0/1     18s
aaron@Aaron-Air argocd % k get services
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.96.233.59    <none>        7000/TCP,8080/TCP            26s
argocd-dex-server                         ClusterIP   10.96.158.49    <none>        5556/TCP,5557/TCP,5558/TCP   26s
argocd-metrics                            ClusterIP   10.96.22.41     <none>        8082/TCP                     26s
argocd-notifications-controller-metrics   ClusterIP   10.96.157.138   <none>        9001/TCP                     26s
argocd-redis                              ClusterIP   10.96.6.47      <none>        6379/TCP                     26s
argocd-repo-server                        ClusterIP   10.96.107.139   <none>        8081/TCP,8084/TCP            26s
argocd-server                             ClusterIP   10.96.252.103   <none>        80/TCP,443/TCP               26s
argocd-server-metrics                     ClusterIP   10.96.3.39      <none>        8083/TCP                     26s
```

</details>

## Get the secret to login in

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
echo
```

********

## Create gateway for argocd-server

```YAML
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
      hostname: "*.local"
      allowedRoutes:
        namespaces:
          from: All
    - name: https
      protocol: HTTPS
      port: 443
      hostname: "*.local"
      allowedRoutes:
        namespaces:
          from: All
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: argocd-route
  namespace: argocd
spec:
  parentRefs:
    - name: gateway
      sectionName: http
      namespace: gateway
  hostnames:
    - "argocd.local"
  rules:
    - matches:
      - path:
          type: PathPrefix # Matches any path starting with /coffee
          value: "/"
      backendRefs:
        - name: argocd-server
          port: 443
```

## Disable TLS

```sh
kubectl patch configmap argocd-cmd-params-cm -n argocd -p '{"data":{"server.insecure":"true"}}'
```

## TLDR

Run `Taskfile.yml` to install argocd

```sh
$ task
```

<details>
  <summary>Click to see task run results</summary>

```sh
task: [check-docker-running] docker info > /dev/null 2>&1
task: [check-cluster-running] kind get clusters | grep gwkc-1
task: [check-cloud-provider-kind-running] pgrep sudo cloud-provider-kind
39373
39375
39376
gwkc-1
task: [install-sample-app] kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
echo
kubectl patch configmap argocd-cmd-params-cm -n argocd -p '{"data":{"server.insecure":"true"}}'
task: [get_password] echo "Password is: " xxxxxx-xxxxx

Password is:  xxxxxx-xxxxx

configmap/argocd-cmd-params-cm patched
task: [configure-httproute] kubectl apply -f httproute.yml
httproute.gateway.networking.k8s.io/argocd-route created
task: Task "update-gateway" is up to date
task: [open-app] open http://argocd.local
```

</details>
