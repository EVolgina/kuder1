# Задание 1. Установка MicroK8S
- Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
```
vagrant@vagrant:~/kube$ sudo snap install kubeadm --classic
kubeadm 1.28.6 from Canonical✓ installed
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
vagrant@vagrant:~/kube$ sudo microk8s kubectl get all --all-namespaces
NAMESPACE     NAME                                             READY   STATUS    RESTARTS        AGE
kube-system   pod/coredns-864597b5fd-5flhg                     1/1     Running   10 (176m ago)   7d7h
kube-system   pod/calico-kube-controllers-77bd7c5b-kfndp       1/1     Running   10 (176m ago)   7d7h
kube-system   pod/dashboard-metrics-scraper-5657497c4c-6748c   1/1     Running   10 (176m ago)   7d7h
kube-system   pod/kubernetes-dashboard-54b48fbf9-b4wq6         1/1     Running   14 (176m ago)   7d7h
kube-system   pod/calico-node-vjcwj                            1/1     Running   16 (176m ago)   7d7h
kube-system   pod/metrics-server-848968bdcd-7n9jf              1/1     Running   16 (176m ago)   7d7h

NAMESPACE     NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes                  ClusterIP   10.152.183.1     <none>        443/TCP                  7d7h
kube-system   service/kube-dns                    ClusterIP   10.152.183.10    <none>        53/UDP,53/TCP,9153/TCP   7d7h
kube-system   service/metrics-server              ClusterIP   10.152.183.59    <none>        443/TCP                  7d7h
kube-system   service/kubernetes-dashboard        ClusterIP   10.152.183.110   <none>        443/TCP                  7d7h
kube-system   service/dashboard-metrics-scraper   ClusterIP   10.152.183.252   <none>        8000/TCP                 7d7h

NAMESPACE     NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/calico-node   1         1         1       1            1           kubernetes.io/os=linux   7d7h

NAMESPACE     NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns                     1/1     1            1           7d7h
kube-system   deployment.apps/kubernetes-dashboard        1/1     1            1           7d7h
kube-system   deployment.apps/dashboard-metrics-scraper   1/1     1            1           7d7h
kube-system   deployment.apps/calico-kube-controllers     1/1     1            1           7d7h
kube-system   deployment.apps/metrics-server              1/1     1            1           7d7h

NAMESPACE     NAME                                                   DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-864597b5fd                     1         1         1       7d7h
kube-system   replicaset.apps/kubernetes-dashboard-54b48fbf9         1         1         1       7d7h
kube-system   replicaset.apps/dashboard-metrics-scraper-5657497c4c   1         1         1       7d7h
kube-system   replicaset.apps/calico-kube-controllers-77bd7c5b       1         1         1       7d7h
kube-system   replicaset.apps/metrics-server-848968bdcd              1         1         1       7d7h
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

vagrant@vagrant:~/kube$ microk8s kubectl create namespace kubernetes-dashboard
namespace/kubernetes-dashboard created
vagrant@vagrant:~/kube$ microk8s kubectl apply -f dashboard-adminuser.yaml
serviceaccount/admin-user created
vagrant@vagrant:~/kube$ microk8s kubectl apply -f dashboard-adminuser-clusterrolebinding.yaml
clusterrolebinding.rbac.authorization.k8s.io/admin-user created

vagrant@vagrant:~/kube$ kubectl -n kubernetes-dashboard create token admin-user
eyJhbGciOiJSUzI1NiIsImtpZCI6IkZxQi1faGlCeVBrUXQ0dWVScG1VczVCNEc4N3FHOXI3WnVXYXg5Xy0zeTQifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTcwNzY1MDI0OSwiaWF0IjoxNzA3NjQ2NjQ5LCJpc3MiOiJodHRwczovL2t1YmVybmV0ZXMuZGVmYXVsdC5zdmMiLCJrdWJlcm5ldGVzLmlvIjp7Im5hbWVzcGFjZSI6Imt1YmVybmV0ZXMtZGFzaGJvYXJkIiwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImFkbWluLXVzZXIiLCJ1aWQiOiI1ZjI4MzZmYy00NjA2LTQ2YjEtODRkNC01MjI5OGRlOTk5MGYifX0sIm5iZiI6MTcwNzY0NjY0OSwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmVybmV0ZXMtZGFzaGJvYXJkOmFkbWluLXVzZXIifQ.W3kB41A13tPkaew2PWPSHAPvs4KE4RGuP6lYy9sbSt6vLQ_iR0t-Ow49McQwXCJJzjUeNoX7tbFHktQ_zrEdcMs2jByopKfcdnlBOpG3p6M74Ip_294G4ARkEjR21aIfQyj95LCfupGDVQ4zWeGmFEPnvIP7LpHXV8w3CfU9RSSjNt6R8xzXFNFQa1DJ_grWt6YQFZzB9zWhdqGXiOJmGyRCQ-8VRdroY7i_Lf1Fv51NdxaxYrP8hoYgdAcY2CP6GDOGcKfZZB2v4pEhkbQHznXxsZNX1n5ZfJr2PGTfuAmENcTU39Il1Qim5yYiaWSY69rmbnv5Hcv7WQOUs4coBA
vagrant@vagrant:~/kube$ microk8s kubectl describe secret -n kube-system microk8s-dashboard-token
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
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkZxQi1faGlCeVBrUXQ0dWVScG1VczVCNEc4N3FHOXI3WnVXYXg5Xy0zeTQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJtaWNyb2s4cy1kYXNoYm9hcmQtdG9rZW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjdhYjdhOTE0LWU3YWMtNDhhNy04OGZiLTJhYmUwZTBkZWE4MiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTpkZWZhdWx0In0.rMa8BFHBZR-4lXoCvz25691xbylK19X8k6kYODXAiSCc5h9CMi0IVlkmZ8YELlwDdSCymOMsh0FrZTxlrwRIbhtdw33G0-iNr4-lrPZ8pnRnFtNpR02h8SDnGsHPUVBoSTL7o-TGTG2vEGB7d2dAZCD8rSJU7u1o8YxHK_kPza3-AW6uBcDha3aVmI6LqV3pPDvJKGBR_D26ABeuACRcsPiRIop3VzzoGciaT6zgy_vHyILCD44fl-K6EgezLqTLWm_dfVWh41kbFVoy18Nk2T_YofQ_x2WWEw2gGCHE7_uZ5c9d8knKOrICyDe67I8s3qT_Up9YxWFNviCl0au5lg

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
- скопировала config на локальный пк, не могу разобраться с IP(моя виртуальная машина поднята через vagrant ubuntu 20.04 [config](https://github.com/EVolgina/kuder1/blob/main/config)
- откоректировала файл [csr.conf.template](https://github.com/EVolgina/kuder1/blob/main/csr.conf.template)
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
```
vagrant@vagrant:~/kube$ kubeadm token create --print-join-command - создала токен присоединения к кластеру
kubeadm join 10.0.2.15:16443 --token 5krwii.7eih1qb71hjwrrql --discovery-token-ca-cert-hash sha256:ef8b2cc9f70cb67c84dc6e33d520511992679dd1e4b6cb99700fdd52cd4ef5f0
```
