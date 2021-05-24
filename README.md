# A few playbooks for Kubernetes test / PoC deployments  

 The playbook will currently run only on CentOS 7. 

TODOS: Convert this to run on something more relevant like CentOS 8 or Ubuntu. 
TODOS: Refactor and verify so that multi-master deployments would run smoothly

## Deploying the cluster

Install hosts will have to be set set to the hosts file found in inv/hosts: 

```
[masters]
master1 ansible_host=10.10.10.10
[workers]
node1 ansible_host=10.10.10.11
node2 ansible_host=10.10.10.12
```

1 + 2 is just a minimal configuration and this could be run on any number of nodes. 

The install was initiated with a command (CIDR netmask of the cluster is given as an extra var): 

```
$ ansible-playbook -i inventories/hosts  -u kube -e cidr_mask=10.10.0.0/16 run-install-cluster.yml
```

run-install-cluster.yml will first run these playbooks an all nodes: 

- preinstall-check.yml
- prepare-environment.yml
- install-docker-ce.yml
- install-kubernetes.yml

After that it will configure the cluster, configuring master server(s) separately and all worker found in the hosts file. 

After the install all nodes should show "Ready"

```
[kube@master1 ~]$ kubectl get nodes
NAME                 STATUS   ROLES    AGE   VERSION
master1.example.com  Ready    master   58s   v1.17.1
node1.example.com    Ready    <none>   36s   v1.17.1
node2.example.com    Ready    <none>   33s   v1.17.1
```
