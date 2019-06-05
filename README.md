# kubernetes_certificates
# How to renew kubernetes certificates, after expiration (1 year by default)
# centos 7 kubernetes 1.13
# dockerd-current[5080]: E0603 09:09:40.481894       1 authentication.go:65] Unable to authenticate the request due to an error: [x509: certificate has expired or is not yet valid, x509: certificate has expired or is not yet valid]

## In every node

Check /etc/kubernetes/kubelet.conf it should pint to the right client certificates, in our installation some worker nodes had the certificate included in the conf file.
We change the configuration to:

```
- name: default-auth
  user:
    client-certificate: /var/lib/kubelet/pki/kubelet-client-current.pem
    client-key: /var/lib/kubelet/pki/kubelet-client-current.pem
```

restart kubelet service

## In the master Node

First of all renew all the certificates in the master node:
> kubeadm alpha certs renew all

Even when you renew the certificates, you should regenerate the config file with the correct configuration.
Move admin.conf, scheduler.conf and controller-manager.conf to a backup dir. 
```
> mv /etc/kubernetes/admin.conf /backup
> mv /etc/kubernetes/controller-manager.conf /backup
> mv /etc/kubernetes/scheduler.conf /backup
```
Regenerate the files with the correct configuration.
```
> kubeadm init phase kubeconfig admin
> kubeadm init phase kubeconfig controller-manager
> kubeadm init phase kubeconfig scheduler
```
restart the master node
 
## kubectl
To recover kubectl command line, copy /etc/kubernetes/admin.conf to $HOME/.kube/config
