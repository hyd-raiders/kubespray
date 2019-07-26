## kubespray

[origin docs](./README.origin.md)

## 特别说明

由于国内情况比较特殊，某些大文件下载会出现问题，故在原项目基础下修改，以满足国内需求




## install
```bash
# firewalld
systemctl disable firewalld
systemctl stop firewalld
# pip3 install first  
# https://github.com/apporoad/eploy/blob/master/docs/python3.md

## ssh免密登录 https://github.com/apporoad/eploy/blob/master/docs/sshLogin.md


```
[getting-start](https://github.com/hyd-raiders/kubespray/blob/master/docs/getting-started.md)

```bash
cd kubespray

pip install -r requirements.txt

cp -r inventory/sample inventory/mycluster
# here is ips 
declare -a IPS=(xxxx xxxx)
CONFIG_FILE=inventory/mycluster/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# apply
ansible-playbook -i inventory/mycluster/hosts.yml cluster.yml -b -v \
  --private-key=~/.ssh/id_rsa -c paramiko
```




## qa

```bash
# libse 组件问题
cp -r /usr/lib64/python2.6/site-packages/selinux /root/.pyenv/versions/2.7.14/lib/python2.7/site-packages/
```





## 改版说明

1. 采用国内镜像

​      roles\download\defaults\main.yml

```yaml
# 修改，变成国内资源
kubeadm_download_url: "http://devops.dcjet.com.cn:52000/mirrors/kubeadm"
hyperkube_download_url: "http://devops.dcjet.com.cn:52000/mirrors/hyperkube"
etcd_download_url: "http://devops.dcjet.com.cn:52000/mirrors/etcd-v3.3.10-linux-amd64.tar.gz"
cni_download_url: "http://devops.dcjet.com.cn:52000/mirrors/cni-plugins-linux-amd64-v0.8.1.tgz"
calicoctl_download_url: "http://devops.dcjet.com.cn:52000/mirrors/calicoctl-linux-amd64"
crictl_download_url: "https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.14.0/crictl-v1.14.0-{{ ansible_system | lower }}-amd64.tar.gz"
```



2. 修改加速器

   inventory\sample\group_vars\all\docker.yml

   ```yaml
   ## An obvious use case is allowing insecure-registry access to self hosted registries.
   ## Can be ipaddress and domain_name.
   ## example define 172.19.16.11 or mirror.registry.io
   docker_insecure_registries:
     - f1361db2.m.daocloud.io
   
   ## Add other registry,example China registry mirror.
   docker_registry_mirrors:
     - https://registry.docker-cn.com
     - https://mirror.aliyuncs.com
   ```

3.  inventory\sample\group_vars\k8s-cluster\k8s-cluster.yml

   ```yaml
## NVIDIA driver installer images. Change them if you have trouble accessing gcr.io.
   nvidia_driver_install_centos_container: atzedevries/nvidia-centos-driver-installer:2
   nvidia_driver_install_ubuntu_container: registry.aliyuncs.com/google-containers/ubuntu-nvidia-driver-installer@sha256:7df76a0f0a17294e86f691c81de6bbb7c04a1b4b3d4ea4e7e2cccdc42e1f6d63
   ## NVIDIA GPU device plugin image.
   nvidia_gpu_device_plugin_container: "mirrorgooglecontainers/nvidia-gpu-device-plugin@sha256:0842734032018be107fa2490c98156992911e3e1f2a21e059ff0105b07dd8e9e"
   
   ```
   
   
   
4. 修改镜像资源

   k8s.gcr.io  替换为 mirrorgooglecontainers

   gcr.io  替换为 registry.aliyuncs.com

   其他资源修改