# Задание 1. Установка MicroK8S
- Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
```
vagrant@vagrant:~/kub$ sudo chown -f -R vagrant ~/.kub
vagrant@vagrant:~/kub$ newgrp microk8s
vagrant@vagrant:~/kub$ microk8s status --wait-ready
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    dns                  # (core) CoreDNS
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
  disabled:
    cert-manager         # (core) Cloud native certificate management
    cis-hardening        # (core) Apply CIS K8s hardening
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    kube-ovn             # (core) An advanced network fabric for Kubernetes
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    minio                # (core) MinIO object storage
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    rook-ceph            # (core) Distributed Ceph storage using Rook
    storage              # (core) Alias to hostpath-storage add-on, deprecated
```
- Установить dashboard.
```
vagrant@vagrant:~/kub$ microk8s enable dashboard
Infer repository core for addon dashboard
Enabling Kubernetes Dashboard
Infer repository core for addon metrics-server
Enabling Metrics-Server
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
clusterrolebinding.rbac.authorization.k8s.io/microk8s-admin created
Metrics-Server is enabled
Applying manifest
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
secret/microk8s-dashboard-token created
If RBAC is not enabled access the dashboard using the token retrieved with:
microk8s kubectl describe secret -n kube-system microk8s-dashboard-token
Use this token in the https login UI of the kubernetes-dashboard service.
In an RBAC enabled setup (microk8s enable RBAC) you need to create a user with restricted
permissions as shown in:
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

vagrant@vagrant:~/kub$ microk8s kubectl describe secret -n kube-system microk8s-dashboard-token
Name:         microk8s-dashboard-token
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: 7ab7a914-e7ac-48a7-88fb-2abe0e0dea82

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1123 bytes
namespace:  11 bytes
```
- Сгенерировать сертификат для подключения к внешнему ip-адресу. vagrant@vagrant:~/kub$ microk8s config. Ключи скопировала
```
vagrant@vagrant:~/kube$ microk8s config > ~/.kube/config
vagrant@vagrant:~/kube$ kubectl get nodes
NAME      STATUS   ROLES    AGE     VERSION
vagrant   Ready    <none>   3h35m   v1.28.3
```
- Проверила службу дашборда Kubernetes:
```
vagrant@vagrant:~/kube$ microk8s kubectl get svc/kubernetes-dashboard -n kube-system
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes-dashboard   ClusterIP   10.152.183.110   <none>        443/TCP   4h3m
vagrant@vagrant:~/kube$ microk8s kubectl get pods -n kube-system
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-5657497c4c-6748c   1/1     Running   0          4h6m
kubernetes-dashboard-54b48fbf9-b4wq6         1/1     Running   0          4h6m
calico-node-vjcwj                            1/1     Running   0          4h17m
coredns-864597b5fd-5flhg                     1/1     Running   0          4h17m
calico-kube-controllers-77bd7c5b-kfndp       1/1     Running   0          4h17m
metrics-server-848968bdcd-7n9jf              1/1     Running   0          4h7m
```
- проброс порта для подключения локально - я сделала в vagrantfile ---  config.vm.network "forwarded_port", guest: 10443, host: 10443
- скопировала config на локальный пк, не могу разобраться с IP(моя виртуальная машина поднята через vagrant ubuntu 20.04 [config](https://github.com/EVolgina/kuder1/blob/main/config.docx)
- откоректировала файл [csr.conf.template](https://github.com/EVolgina/kuder1/blob/main/csr.conf.template.docx)
```
vagrant@vagrant:~/kube$ sudo microk8s refresh-certs --cert front-proxy-client.crt ---обновила сертификаты
Taking a backup of the current certificates under /var/snap/microk8s/6089/certs-backup/
Creating new certificates
Signature ok
subject=CN = front-proxy-client
Getting CA Private Key
Restarting service kubelite.

vagrant@vagrant:~/kube$ microk8s kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:16443
CoreDNS is running at https://127.0.0.1:16443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
vagrant@vagrant:~/kube$ microk8s kubectl get nodes
NAME      STATUS   ROLES    AGE     VERSION
vagrant   Ready    <none>   4h57m   v1.28.3
vagrant@vagrant:~/kube$ microk8s kubectl get pods --all-namespaces
NAMESPACE     NAME                                         READY   STATUS    RESTARTS      AGE
kube-system   kubernetes-dashboard-54b48fbf9-b4wq6         1/1     Running   1 (14m ago)   4h55m
kube-system   dashboard-metrics-scraper-5657497c4c-6748c   1/1     Running   1 (14m ago)   4h54m
kube-system   coredns-864597b5fd-5flhg                     1/1     Running   1 (14m ago)   5h5m
kube-system   calico-kube-controllers-77bd7c5b-kfndp       1/1     Running   1 (14m ago)   5h5m
kube-system   calico-node-vjcwj                            1/1     Running   2 (12m ago)   5h5m
kube-system   metrics-server-848968bdcd-7n9jf              1/1     Running   1 (14m ago)   4h55m
vagrant@vagrant:~/kube$ microk8s kubectl get services --all-namespaces
NAMESPACE     NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes                  ClusterIP   10.152.183.1     <none>        443/TCP                  5h6m
kube-system   kube-dns                    ClusterIP   10.152.183.10    <none>        53/UDP,53/TCP,9153/TCP   5h5m
kube-system   metrics-server              ClusterIP   10.152.183.59    <none>        443/TCP                  4h55m
kube-system   kubernetes-dashboard        ClusterIP   10.152.183.110   <none>        443/TCP                  4h55m
kube-system   dashboard-metrics-scraper   ClusterIP   10.152.183.252   <none>        8000/TCP                 4h55m
```
