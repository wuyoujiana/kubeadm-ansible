### 中文说明在后面(＾＿＾）
# Kubeadm Ansible Playbook
Quickly build kubernetes cluster using Ansible with ansible. Tested:
- centos7

# Features
- Quickly deploy a kubernetes cluster
- Quickly joining of new node
# Steps for usage
## 1.preparation before deployment
- Ansible host operates node host through the root
```
yum install -y ansible
```

- Node hosts can install kubernetes components and docker-ce through yum
> Alibaba repo file for use in mainland China

```
[kubernetes]
name=kubernetes Repo
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgcheck=0
enabled=1
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
```

## 2.add host information 
The / etc / ansible / hosts host list is divided into three parts:
- master: master node host
- new_node: will be added to the node host
- node: he node host that has joined the cluster

For example:
> master:10.200.101.213

> worknode:10.200.101.214

```
[master]
10.200.101.213 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.214 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
```

> note：
> - imageRepository,version,Repository must have correct values
> - imageRepository：you can use the default address k8s.gcr.io to set the imageRepository address of the kubeadm.If you are in mainland China,you can set Alibaba address as your imageRepository
> - version：version of the K8S cluster
> - Repository：Docker mirror repository address, which can be set to a public or private repository,if you are not in mainland China, you can use the default address docker.io 


## 3.git

```
git clone https://github.com/wuyoujiana/kubeadm-ansible.git
```

### Copy roles folder

```
cd kubeadm-ansible
mv roles/* /etc/ansible/roles/
```


## 4.install cluster

```
ansible-playbook install_k8s.yml
```
master and node will be installed

# Add new node
/etc/ansible/hosts

**you need move hosts in the new_node to the node section, then add the newhost that needs to be added to the cluster to new_node.**

For example: 
> Move host 101.214 to node,add newhost 101.215 to new_host

```
[master]
10.200.101.213 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.215 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
10.200.101.214 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
```
Execute ansible script：

```
ansible-playbook -t node_join node_join_master.yml
```
You can finish joining the cluster of the master

---
# 中文说明

# Kubeadm Ansible Playbook
用ansible快速地部署kubernetes集群，现已测试：
- centos7

# 现有功能
- 快速部署kubernetes集群
- 新node节点快速加入
# 使用步骤
## 1.部署前准备
- ansible通过root用户操作节点主机
```
yum install -y ansible
```

- 节点主机可通过yum安装kubernetes组件与docker-ce
> 阿里云repo文件，在中国大陆使用

```
[kubernetes]
name=kubernetes Repo
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
gpgcheck=0
enabled=1
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
```

## 2.ansible主机添加部署主机信息
/etc/ansible/hosts主机清单分为三部分：
- master: master节点主机
- new_node: 将要加入node节点主机
- node: 已经加入集群的节点主机

例:
> master:10.200.101.213

> worknode:10.200.101.214

```
[master]
10.200.101.213 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.214 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
```

> 说明：
> - imageRepository,version,Repository必须有对应正确的值
> - imageRepository：设置kubeadm拉取镜像的项目地址，这里设置为阿里云地址；如果你不在中国大陆，你可以使用默认地址 k8s.gcr.io
> - version：设置K8S集群的版本
> - Repository：设置docker镜像仓库地址,可设置为公有或私有仓库地址，已加入docker信任仓库；如果你不在中国大陆，你可以使用默认地址  docker.io


## 3.拉取代码

```
git clone https://github.com/wuyoujiana/kubeadm-ansible.git
```

### 拷贝roles文件夹内容

```
cd kubeadm-ansible
mv roles/ /etc/ansible/roles
```


## 4.开始安装k8s集群

```
ansible-playbook install_k8s.yml
```
等待完成master与node节点安装

# 新加入node节点
> /etc/ansible/hosts

新加入工作节点node节点时，**需要将之前hosts文件中的new_node中的主机移动到node主机板块中，再添加需要加入集群的新主机到new_node中**

列: 
> 将101.214主机移到node主机板块，新添加主机101.215写入new_host板块。

```
[master]
10.200.101.213 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.215 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
10.200.101.214 ansible_ssh_user=root nansible_ssh_pass=YOUR_PASSWORD
```
执行ansible脚本：

```
ansible-playbook -t node_join node_join_master.yml
```
即可完成加入到当前master的集群中
