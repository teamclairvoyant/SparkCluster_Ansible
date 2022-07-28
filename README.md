This repository defines multiple ansible roles to help deploying standalone mode of a Spark cluster.

# Requirements

You will need a driver machine with ansible installed and a clone of the current repository:

* If you are running on cloud (public/private network)
  * Install ansible on the edge node (with public ip)


### Updating Ansible configuration

In order to have variable overriding from host inventory, please add the following configuration into your ~/.ansible.cfg file

```
[defaults]
host_key_checking = False
```

### Supported/Tested Platform

* Ubuntu 20.04.3
* Ansible 2.9.6


# Defining your cluster deployment metadata (host inventory)

Ansible uses 'host inventory' files to define the cluster configuration, nodes, and groups of nodes
that serves a given purpose (e.g. master node).

Below is a host inventory sample definition:

```
[all:vars]
ansible_connection=ssh
ansible_become=true
gather_facts=True
gathering=smart
install_temp_dir=/tmp/ansible-install
spark_history_dir=/tmp/spark-events
python_version=2

[master]
<fqdn>   ansible_host=<private ip>   ansible_host_private=<private ip>  ansible_host_id=1

[nodes]
<fqdn>   ansible_host=<private ip>   ansible_host_private=<private ip>  ansible_host_id=2
<fqdn>   ansible_host=<private ip>   ansible_host_private=<private ip>  ansible_host_id=3
<fqdn>   ansible_host=<private ip>   ansible_host_private=<private ip>  ansible_host_id=4

```

Some specific configurations are:

* install_java=True : install/update java 8
* install_temp_dir=/tmp/ansible-install : temporary folder used for install files
* install_dir=/opt/spark : where packages are installed (e.g. Spark)
* python_version=2 : python version to use

# Deploying Spark standalone

In this scenario, a Standalone Spark cluster will be deployed with a few optional components.

### Related ansible roles

* **Common**  Deploys Java and common dependencies
* **Spark** Deploys Spark in Standalone mode using slave nodes as workers

Custom configs can be set in roles/spark/defaults/main.yml


### Deployment playbook

```
- name: spark setup
  hosts: all
  remote_user: root
  roles:
    - role: common
    - role: spark

```
Deploying Playbook:

```ansible-playbook spark_setup.yml -i inventory```
