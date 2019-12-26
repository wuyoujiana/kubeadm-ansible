# Kubeadm Ansible Playbook
用ansible快速地部署kubernetes集群，现目前测试：
- centos7

# 现有功能
- 快速部署kubernetes集群
- 自动加入node节点
# Usage
在ansible主机添加需要部署的主机信息，保证ansible能正常可达。
能够正常通过安装kubernetes组件与docker-ce。
## 1.ansible主机添加部署主机信息
主机清单分为两部分：
- master: master主机
- new_node: 将要加入集群node节点的主机
- node: 已经加入集群node的节点
```
[master]
10.200.101.213 
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.214 
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
```
说明：
1. imageRepository：设置kubeadm拉取镜像的项目地址，这里设置为阿里云地址
2. version：设置K8S集群的版本
3. Repository：设置docker镜像仓库地址,可设置为公有或私有仓库地址，已加入docker信任仓库


## 2.拉取代码

```
git clone https://github.com/wuyoujiana/kubeadm-ansible.git
```

### 拷贝roles
cd kubeadm-ansible
mv roles/ /etc/ansible/roles

## 3.开始安装k8s集群

```
ansible-playbook install_k8s.yml
```
等待完成master与node节点安装

## 4.加入node节点
当需要新加入node节点时，需要将之前hosts文件中的new_node中的主机移动到node主机板块中，再添加需要加入集群的新主机到new_node中
```
[master]
10.200.101.213 
[master:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[new_node]
10.200.101.215
[new_node:vars]
imageRepository=registry.aliyuncs.com/google_containers
version=1.16.0
Repository=registry.aliyuncs.com

[node]
10.200.101.214 
```
执行ansible脚本：

```
ansible-playbook -t node_join node_join_master.yml
```
即可完成加入到当前master的集群中