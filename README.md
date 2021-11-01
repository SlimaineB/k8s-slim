# k8s-slim : A Full Kubernetes cluster in less than 9 minutes

## Description

This project allow you to create with one command a fully kubernetes cluster (version 1.22) running Ubuntu 21-04.
It uses Vagrant with VirtualBox and Ansible Provisionner and Helm Chart.


## How to

### Deploy and run the nodes

```sh
vagrant up
```

### Configure the k8s cluster

You can configure your k8s cluster by editing the `CONFIGURATION VARIABLES` available in the `Vagrantfile`

### Intercat with your cluster

The kubeconfig is automatically copied from master to your local machine.
If it is not the case, you can copy it with below command : 

```sh
scp -r vagrant@192.168.50.10:/home/vagrant/.kube $HOME/
password = vagrant
```

Test your config like the example bellow :

```sh
kubectl get nodes
```

Result : 

```
NAME       STATUS   ROLES                  AGE   VERSION
master     Ready    control-plane,master   22m   v1.22.3
worker-1   Ready    <none>                 18m   v1.22.3
worker-2   Ready    <none>                 16m   v1.22.3
```

### Connect to the master via ssh

Either you are at the same level as your Vagrantfile, in this case you run the following command :

```sh
vagrant ssh master
```

Either you are in another folder :

```sh
ssh -r vagrant@192.168.50.10
password = vagrant
```
