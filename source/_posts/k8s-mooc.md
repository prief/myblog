---
title: k8s-mooc
date: 2019-09-09 22:37:42
tags:
---

# k8s快速入门

## 核心概念
- kubernetes 舵手
- image
- container
- pod
  - 包含1个或多个container
  - container之间共享网络，ip
- replicaset
- deployment
- service
  - 通过labelSelector选择后端服务
  - 通过clusterIp暴露服务
- https://k8s.imroc.io/

## 架构设计
- master
  - etcd
  - apiServer
  - scheduler
  - controllerManager
- worker
  - kubelet
  - kube-proxy
  - docker

## 认证授权
- https
  - ssl/tls
  - CA(浏览器的公共CA和自有CA)
- k8s认证
  - 集群外访问apiServer
    - 客户端证书认证(kubectl,tls双向认证)
    - BearerToken认证(apiServer内置了可信任的token)
  - 集群内pod访问apiServer
    - serviceAccount
      - 三部分(namespace,token,ca)
      - 上面的信息通过目录挂载到pod中，应用通过指定目录使用
- 授权
  - ABAC
  - webHook
  - RBAC(最新的主流方案，roleBasedAccessControl)
    - User
      - user
      - serviceAccount
    - Role
      - namespace
      - name
      - resource
      - verbs
    - Authority
      - resource
      - verbs
    - RBAC
      - Role/RoleBinding
      - ClusterRole/ClusterRoleBinding
- 准入控制
  - AdmisionControl，类似java中的filter
  - AlwaysAdmit
  - AlwaysDeny
  - ServiceAccount
  - DenyEscolatingExec

## 集群搭建方案对比
- kubeadm
  - 优雅，几乎所有组件都在pod中
  - 简单
  - 支持高可用
  - 升级方便
- 二进制
  - 容易维护
  - 灵活
  - 支持高可用
  - 升级方便
  - 证书、配置等需要一步步操作

# kubeadm方式搭建

## 环境准备
- 统一主机名
  - hostname
  - hostnamectl set-hostname name
  - vi /etc/hosts
- 安装依赖包
  - wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
  - yum install -y epel-release && yum clean all && yum makecache
  - yum update -y
  - yum install -y conntrack ipvsadm ipset jq sysstat curl iptables libseccomp ntpdate
  - ntpdate ip
- 主机配置
  - 关闭防火墙 systemctl stop firewalld && systemctl disable firewalld
  - 关闭swap  swapoff -a && sed -i '/swap/s/^\(.*\)$/# \1/g' /etc/fstab
  - 重置iptables iptables -F && iptables -X && iptables -F -t nat && iptables -X -t nat && iptables -P FORWARD ACCEPT
  - 关闭selinux setenforce 0 && sed -i 's/^SELINUX=.*$/SELINUX=disabled/' /etc/selinux/config
  - 关闭dnsmasq systemctl stop dnsmasq && systemctl disable dnsmasq
  - 配置k8s系统文件 vim /etc/sysctl.d/kubernetes.conf && sysctl -p /etc/sysctl.d/kubernetes.conf
```
cat > /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
vm.swappiness=0
vm.overcommit_memory=1
vm.panic_on_oom=0
fs.inotify.max_user_watches=89100
EOF

```
- 安装docker
  - mkdir -p /opt/kubernetes/docker && cd /opt/kubernetes/docker
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-selinux-17.03.1.ce-1.el7.centos.noarch.rpm
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-debuginfo-17.03.1.ce-1.el7.centos.x86_64.rpm
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-17.03.1.ce-1.el7.centos.x86_64.rpm
  - yum remove -y docker* container-selinux
  - yum localinstall -y *.rpm
  - systemctl enable docker
  - df -h # 查看空间较大的分区并在分区下创建dockerdata目录准备存放docker数据
  - #设置docker数据目录并启动docker服务 systemctl start docker
```
cat <<EOF > /etc/docker/daemon.json
{
    "graph": "/var/lib/docker",
    "registry-mirrors": ["https://4vo01fev.mirror.aliyuncs.com"],
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2",
    "storage-opts": [
        "overlay2.override_kernel_check=true"
    ]
}
EOF


cat <<EOF > /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=" "HTTPS_PROXY="
EOF

```
- 安装必要工具
  - kubeadm/kubelet/kubectl
  - 配置yum源
  - yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
  - systemctl enable kubelet && systemctl start kubelet
```

cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
EOF


docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.17.0 
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.17.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.17.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.17.0
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0
docker pull coredns/coredns:1.6.5

docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.17.0 k8s.gcr.io/kube-apiserver:v1.17.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.17.0 k8s.gcr.io/kube-controller-manager:v1.17.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.17.0 k8s.gcr.io/kube-scheduler:v1.17.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.17.0 k8s.gcr.io/kube-proxy:v1.17.0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0 k8s.gcr.io/etcd:3.4.3-0
docker tag coredns/coredns:1.6.5 k8s.gcr.io/coredns:1.6.5

docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.17.0 
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.17.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.17.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.17.0
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.1
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.3-0
docker rmi coredns/coredns:1.6.5

```
## 高可用部署
- 实现apiServer的高可用与负载均衡，也可只用keepalived
  - yum install -y socat keepalived haproxy ipvsadm
  - systemctl enable haproxy && systemctl enable keepalived
  - systemctl start haproxy && systemctl start keepalived
  - journalctl -f -u keepalived 
  - ip a # 查看虚拟ip
```
# /etc/keepalived/keepalived.conf
global_defs {
   router_id master01|master02|master03
}

vrrp_script check {
    script /etc/keepalived/check_haproxy.sh｜check_apiserver.sh 
    interval 3
    # weight -2 如果脚本非零退出，则权重-2，可动态配置权重,与check_apiserver.sh 搭配
}

vrrp_instance VI_1 {
    state MASTER|BACKUP|BACKUP
    interface ens160|ens160|ens160
    virtual_router_id 80
    priority 100|90|80
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        10.18.3.200
    }
    track_script {   
        check
    }
}


#/etc/keepalived/check_haproxy.sh 
#!/bin/bash
NUM=`ps -C haproxy --no-header |wc -l`
if [ $NUM -eq 0 ];then
    systemctl stop keepalived
fi

#/etc/keepalived/check_apiserver.sh 
#!/bin/bash
errorExit(){
    echo "*** $*" 1>&2
    exit 1
}
curl --silent --max-time 2 --insecure https://localhost:6443/ -o /dev/null || errorExit "Error https://localhost:6443"
if ip addr | grep -q 10.18.3.200; then
    curl --silent --max-time 2 --insecure https://10.18.3.200:6443 -o /dev/null || errorExit "Error https://10.18.3.200:6443"
fi

#/etc/haproxy/haproxy.cfg
global
    log         127.0.0.1 local3
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     32768
    user        haproxy
    group       haproxy
    daemon
    nbproc      1
    stats socket /var/lib/haproxy/stats

defaults
    mode                    tcp
    log                     global
    option                  tcplog
    option                  dontlognull
    option                  redispatch
    retries                 3
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout check           10s

listen stats
    mode   http
    bind :8888
    stats   enable
    stats   uri     /admin
    stats   auth    admin:admin
    stats   admin   if TRUE

frontend  k8s_https *:8443
    mode      tcp
    maxconn      2000
    default_backend     https_sri

backend https_sri
    balance      roundrobin
    server master1-api 10.18.3.178:6443  check inter 10000 fall 2 rise 2 weight 1
    server master2-api 10.18.3.179:6443  check inter 10000 fall 2 rise 2 weight 1
    server master3-api 10.18.3.180:6443  check inter 10000 fall 2 rise 2 weight 1

```
- 部署master01
  - kubeadm config print init-defaults > ~/kubeadm_master01.yaml #默认配置文件
  - kubeadm config images list --config  ~/kubeadm_master01.yaml #查看所需镜像
  - kubeadm config images pull --config  ~/kubeadm_master01.yaml #拉取所需镜像，也可手动拉取
  - kubeadm init --control-plane-endpoint "LOAD_BALANCER_DNS:LOAD_BALANCER_PORT" --pod-network-cidr 10.96.0.0/16 --upload-certs
  - mkdir -p $HOME/.kube
  - sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  - sudo chown $(id -u):$(id -g) $HOME/.kube/config
  - kubectl get pods --all-namespaces #测试
- 部署master02|master03
  - kubeadm join 10.18.3.200:8443 --token XXX --discovery-token-ca-cert-hash XXX --control-plane --certificate-key XXX
  - mkdir -p $HOME/.kube
  - sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  - sudo chown $(id -u):$(id -g) $HOME/.kube/config
  - kubectl get pods --all-namespaces #测试
- 部署worker
  - kubeadm join 10.18.3.200:8443 --token XXX --discovery-token-ca-cert-hash XXX
- 部署网络插件
  - #任意主节点下载配置文件并更改cidr
  - kubectl apply -f calico.yaml
  - kubectl get pods --all-namespaces #测试
- 检测
  - kubectl get nodes
  - kubectl cluster-info
  - kubectl get cs
  - kubectl exec -n kube-system etcd-m1 -- etcdctl --cacert="/etc/kubernetes/pki/etcd/ca.crt" --cert="/etc/kubernetes/pki/etcd/peer.crt" --key="/etc/kubernetes/pki/etcd/peer.key" --endpoints=https://10.18.3.178:2379 member list #查看etcd集群
  - journalctl -f #查看日志
## 可用性测试
- kubectl apply -f nginx-ds.yaml  
- ping podIp
- curl svcIp:svcPort
- curl nodeIp:nodePort

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-ds-svc
  labels:
    app: nginx-ds-svc
spec:
  type: NodePort
  selector:
    app: nginx-ds
  ports:
  - name: http
    port: 80
    targetPort: 80
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-ds
  labels:
    addonmanager.kubernetes.io/mode: Reconcile
spec:
  selector:
    matchLabels:
      app: nginx-ds
  template:
    metadata:
      labels:
        app: nginx-ds
    spec:
      containers:
      - name: my-nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

```
- kubectl apply -f pod-nginx.yaml
- kubectl exec -it nginx bash
- cat /etc/resolve.conf #查看DNS
- ping svc-name #如果不通可以按如下修改proxy模式为ipvs
- kubectl logs kube-proxy-hash -n kube-system #查看proxy模式
- kubectl edit cm kube-proxy -n kube-system #默认kube-proxy模式为iptables，可修改mode为ipvs，同时需要ipvs模块配置

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
        app: nginx-ds
spec:
  containers:
  - name: my-nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80

# ipvs模块配置
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash 
modprobe -- ip_vs 
modprobe -- ip_vs_rr 
modprobe -- ip_vs_wrr 
modprobe -- ip_vs_sh 
modprobe -- nf_conntrack_ipv4 
EOF

#配置开机自加载并执行生效
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules

#查看是否加载
lsmod | grep -e ip_vs -e nf_conntrack_ipv4

#重启kube-proxy
kubectl get pod -n kube-system | grep kube-proxy |awk '{system("kubectl delete pod "$1" -n kube-system")}'

```

## dashboard
- kubectl apply -f dashboard.yaml
- bash dashboardToken.sh # 默认只支持token登陆
```
# dashboardToken.sh
#!/bin/bash
kubectl describe secret -n kubernetes-dashboard $(kubectl get secret -n kubernetes-dashboard | grep dashboard-token | awk '{print $1}') | grep -E '^token' | awk '{print $2}'

```

# 二进制方式搭建
## 环境准备
- 统一主机名
  - hostname
  - hostnamectl set-hostname name
  - vi /etc/hosts
- rhel更换yum
  - rpm -qa | grep yum | xargs rpm -e --nodeps
  - mkdir yumrpm && cd yumrpm
  - wget https://mirrors.aliyun.com/centos/7/os/x86_64/Packages/yum-3.4.3-163.el7.centos.noarch.rpm
  - wget https://mirrors.aliyun.com/centos/7/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm
  - wget https://mirrors.aliyun.com/centos/7/os/x86_64/Packages/yum-utils-1.1.31-52.el7.noarch.rpm
  - wget https://mirrors.aliyun.com/centos/7/os/x86_64/Packages/yum-updateonboot-1.1.31-52.el7.noarch.rpm
  - wget https://mirrors.aliyun.com/centos/7/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-52.el7.noarch.rpm
  - rpm -ivh yum*
- 安装依赖包
  - wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
  - yum install -y epel-release && yum clean all && yum makecache
  - yum update -y
  - yum install -y conntrack ipvsadm ipset jq sysstat curl iptables libseccomp ntpdate
  - ntpdate ip
- 主机配置
  - 关闭防火墙 systemctl stop firewalld && systemctl disable firewalld
  - 关闭swap  swapoff -a && sed -i '/swap/s/^\(.*\)$/# \1/g' /etc/fstab
  - 重置iptables iptables -F && iptables -X && iptables -F -t nat && iptables -X -t nat && iptables -P FORWARD ACCEPT
  - 关闭selinux setenforce 0 && sed -i 's/^SELINUX=.*$/SELINUX=disabled/' /etc/selinux/config
  - 关闭dnsmasq systemctl stop dnsmasq && systemctl disable dnsmasq
  - 配置k8s系统文件 vim /etc/sysctl.d/kubernetes.conf && sysctl -p /etc/sysctl.d/kubernetes.conf
```
cat > /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
vm.swappiness=0
vm.overcommit_memory=1
vm.panic_on_oom=0
fs.inotify.max_user_watches=89100
EOF

```
- 安装docker
  - mkdir -p /opt/kubernetes/docker && cd /opt/kubernetes/docker
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-selinux-17.03.1.ce-1.el7.centos.noarch.rpm
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-debuginfo-17.03.1.ce-1.el7.centos.x86_64.rpm
  - wget http://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-17.03.1.ce-1.el7.centos.x86_64.rpm
  - yum remove -y docker* container-selinux
  - yum localinstall -y *.rpm
  - systemctl enable docker
  - df -h # 查看空间较大的分区并在分区下创建dockerdata目录准备存放docker数据
  - #设置docker数据目录并启动docker服务 systemctl start docker
```
cat <<EOF > /etc/docker/daemon.json
{
    "graph": "/var/lib/docker",
    "registry-mirrors": ["https://4vo01fev.mirror.aliyuncs.com"],
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2",
    "storage-opts": [
        "overlay2.override_kernel_check=true"
    ]
}
EOF


cat <<EOF > /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=" "HTTPS_PROXY="
EOF

```
- 配置发布机免密登陆
```
ssh-keygen -t rsa
ssh-copy-id .ssh/id_rsa.pub user@ip
```
- 准备二进制文件
  - master
    - etcd
    - etcdctl
    - kube-apiserver
    - kube-controller-manager
    - kube-scheduler
    - kubeadm
    - kubectl
  - worker
    - kubelet
    - kube-proxy
- 分发到各节点并配置PATH
  - scp 
  - echo "PATH=$PATH:/opt/k8s/bin" >> ~/.bashrc
- 配置各服务参数

## 高可用部署
- CA证书
  - 安装工具，cfssl非常好用的CA工具，用来生成证书和密钥文件
  - 生成根证书，后续所有的证书都由根证书签名
```
# getCfssl.sh
#!/bin/bash
mkdir -p ~/cfssl/bin
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 -O ~/cfssl/bin/cfssl
wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64 -O ~/cfssl/bin/cfssljson
chmod +x ~/cfssl/bin/cfssl ~/cfssl/bin/cfssljson
echo "PATH=$PATH:~/cfssl/bin/" >> ~/.bashrc
source ~/.bashrc
cfssl version

# genCA.sh
#!/bin/bash
mkdir -p ~/cfssl/my/ && cd ~/cfssl/my/
# 生成证书ca.pem和私钥ca-key.pem
# csr.json可参考官网
cfssl genkey -initca csr.json | cfssljson -bare ca
# 把根证书拷贝到主节点
ssh root@MASTER_IP "mkdir -p /etc/kubernetes/pki/"
scp ca*.pem root@MASTER_IP:/etc/kubernetes/pki/

```
- etcd集群(3台master)
```
wget 'https://github.com/etcd-io/etcd/releases/download/v3.3.18/etcd-v3.3.18-linux-amd64.tar.gz'

# 生成etcd的证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes etcd-csr.json | cfssljson -bare etcd

# 分发到每个etcd节点
scp etcd*.pem root@MASTER_IP:/etc/kubernetes/pki/

# 配置service 文件
scp etcd.service MASTERIP:/etc/systemd/system/

# 启动服务
systemctl daemon-reload && systemctl enable etcd && systemctl restart etcd

# 查看日志
journalctl -f -u etcd

# 查看端口
netstat -tunlp ｜ grep 23(79|80)

```

- apiserver集群(3台master)
```
# 生成证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kubernetes-csr.json | cfssljson -bare kubernetes

# 分发到每个etcd节点
scp kubernetes*.pem root@MASTER_IP:/etc/kubernetes/pki/

# 配置service 文件
scp kube-apiserver.service MASTERIP:/etc/systemd/system/

# 启动服务
systemctl daemon-reload && systemctl enable kube-apiserver && systemctl restart kube-apiserver

# 查看日志
journalctl -f -u kube-apiserver

# 查看端口
netstat -tunlp ｜ grep 6443

```
- 部署keepalived(apiserver高可用)
```
# 一般部署一主一备2个节点即可
yum install -y keepalived

# 创建配置文件 /etc/keepalived/keepalived.conf
global_defs {
   router_id master01|master02
}

vrrp_script check {
    script /etc/keepalived/check_apiserver.sh 
    interval 3
    weight -2 如果脚本非零退出，则权重-2，与check_apiserver.sh 搭配
}

vrrp_instance VI_1 {
    state MASTER|BACKUP
    interface ens160|ens160
    virtual_router_id 80
    priority 100|90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        VIP
    }
    track_script {   
        check
    }
}

# 创建监控脚本 /etc/keepalived/check_apiserver.sh
#!/bin/bash
errorExit(){
    echo "*** $*" 1>&2
    exit 1
}
curl --silent --max-time 2 --insecure https://localhost:6443/ -o /dev/null || errorExit "Error https://localhost:6443"
if ip addr | grep -q VIP; then
    curl --silent --max-time 2 --insecure https://VIP:6443 -o /dev/null || errorExit "Error https://VIP:6443"
fi

# 启动服务
systemctl daemon-reload && systemctl enable keepalived && systemctl restart keepalived

# 查看日志
journalctl -f -u keepalived

# 访问服务
curl --insecure https://VIP:6443/


```
- kubectl
  - 是k8s集群的命令行管理工具
  - 默认从 ~/.kube/config文件读取apiserver的地址、证书、token等信息
```
# 创建admin证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes admin-csr.json | cfssljson -bare admin

# 创建kubeconfig文件
# 设置集群参数
kubectl config set-cluster kubernetes
# 设置客户端认证参数
kubectl config set-credentials admin
# 设置上下文参数
kubectl config set-context kubernetes
# 设置默认上下文
kubectl config use-context kubernetes
# 分发到目标节点
scp kube.config root@MASTERIP:~/.kube/config

# 授予k8s证书访问kubeletAPI的权限
kubectl create clusterrolebinding kube-apiserver:kubelet-apis --clusterrole=system:kubelet-api-admin --user kubernetes

# 测试
kubectl cluster-info
kubectl get all --all-namespaces
kubectl get cs|componentstatuses
```
- controller-manager
  - 启动后通过竞选产生一个leader，其他节点阻塞
  - 当leader不可用后剩余节点再竞选，保证高可用
```
# 创建证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes cm-csr.json | cfssljson -bare controller-manager

# 分发到目标节点
scp controller-manager*.pem root@MASTERIP:/etc/kubernetes/pki/

# 创建kubeconfig文件
# 设置集群参数
kubectl config set-cluster kubernetes
# 设置客户端认证参数
kubectl config set-credentials system:kube-controller-manager
# 设置上下文参数
kubectl config set-context system:kube-controller-manager
# 设置默认上下文
kubectl config use-context system:kube-controller-manager
# 分发到目标节点
scp controller-manager.kubeconfig root@MASTERIP:/etc/kubernetes/

# 创建service文件
# vi /etc/systemd/system/kube-controller-manager.service

# 启动服务
systemctl daemon-reload && systemctl enable kube-controller-manager && systemctl restart kube-controller-manager

# 查看日志
journalctl -f -u kube-controller-manager

# 查看leader
kubectl get endpoints kube-controller-manager -n kube-system -o yaml
```
- scheduler
  - 启动后通过竞选产生一个leader，其他节点阻塞
  - 当leader不可用后剩余节点再竞选，保证高可用
```
# 创建证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes scheduler-csr.json | cfssljson -bare kube-scheduler

# 分发到目标节点
scp kube-scheduler*.pem root@MASTERIP:/etc/kubernetes/pki/

# 创建kubeconfig文件
# 设置集群参数
kubectl config set-cluster kubernetes
# 设置客户端认证参数
kubectl config set-credentials system:kube-scheduler
# 设置上下文参数
kubectl config set-context system:kube-scheduler
# 设置默认上下文
kubectl config use-context system:kube-scheduler
# 分发到目标节点
scp kube-scheduler.kubeconfig root@MASTERIP:/etc/kubernetes/

# 创建service文件
# vi /etc/systemd/system/kube-scheduler.service

# 启动服务
systemctl daemon-reload && systemctl enable kube-scheduler && systemctl restart kube-scheduler

# 查看日志
journalctl -f -u kube-scheduler

# 查看leader
kubectl get endpoints kube-scheduler -n kube-system -o yaml

```
- kubelet(worker节点)
  - 下载所需镜像
```
# 创建bootstrap配置文件
# 创建token
export BOOTSTRAP_TOKEN=$(kubeadm token create ) 
# 设置集群参数
kubectl config set-cluster kubernetes
# 设置客户端认证参数
kubectl config set-credentials kubelet-bootstrap
# 设置上下文参数
kubectl config set-context default 
# 设置默认上下文
kubectl config use-context default 
# 分发到目标节点
scp kubelet-bootstrap.kubeconfig root@WORKER:/etc/kubernetes/kubelet-bootstrap.kubeconfig
# 分发CA到worker节点
scp ca.pem root@WORKER:/etc/kubernetes/pki/

# 分发kubelet配置文件
scp kubelet.config.json root@WORKER:/etc/kubernetes/

# 设置kubelet服务文件
vi /etc/systemd/system/kubelet.service

# bootstrap授权
kubectl create clusterrolebinding kubelet-bootstrap --clusterrole=system:node-bootstrapper --group=system:bootstrappers

# 启动服务
systemctl daemon-reload && systemctl enable kubelet && systemctl restart kubelet

# master上通过请求
kubectl get csr
kubectl certificate approve <name>

# worer节点日志
journalctl -f

```
- kube-proxy（所有节点）
```
# 创建证书和私钥
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy 

# 创建kubeconfig文件
# 设置集群参数
kubectl config set-cluster kubernetes
# 设置客户端认证参数
kubectl config set-credentials kube-proxy
# 设置上下文参数
kubectl config set-context default
# 设置默认上下文
kubectl config use-context default
# 分发到目标节点
scp kube-proxy.kubeconfig root@NODEIP:/etc/kubernetes/

# 创建service文件
# vi /etc/systemd/system/kube-proxy.service

# 启动服务
systemctl daemon-reload && systemctl enable kube-proxy && systemctl restart kube-proxy

# 查看日志
journalctl -f -u kube-proxy
```
- 部署插件
  - calico
  - coredns

## 可用性测试
- kubectl apply -f nginx-ds.yaml
- ping podIp
- curl svcIp:svcPort
- curl nodeIp:nodePort

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-ds-svc
  labels:
    app: nginx-ds-svc
spec:
  type: NodePort
  selector:
    app: nginx-ds
  ports:
  - name: http
    port: 80
    targetPort: 80
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-ds
  labels:
    addonmanager.kubernetes.io/mode: Reconcile
spec:
  selector:
    matchLabels:
      app: nginx-ds
  template:
    metadata:
      labels:
        app: nginx-ds
    spec:
      containers:
      - name: my-nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

```
- kubectl apply -f pod-nginx.yaml
- kubectl exec -it nginx bash
- cat /etc/resolve.conf #查看DNS
- ping svc-name #如果不通可以按如下修改proxy模式为ipvs
- kubectl logs kube-proxy-hash -n kube-system #查看proxy模式
- kubectl edit cm kube-proxy -n kube-system #默认kube-proxy模式为iptables，可修改mode为ipvs，同时需要ipvs模块配置

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
        app: nginx-ds
spec:
  containers:
  - name: my-nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80

# ipvs模块配置
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash 
modprobe -- ip_vs 
modprobe -- ip_vs_rr 
modprobe -- ip_vs_wrr 
modprobe -- ip_vs_sh 
modprobe -- nf_conntrack_ipv4 
EOF

#配置开机自加载并执行生效
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules

#查看是否加载
lsmod | grep -e ip_vs -e nf_conntrack_ipv4

#重启kube-proxy
kubectl get pod -n kube-system | grep kube-proxy |awk '{system("kubectl delete pod "$1" -n kube-system")}'

```
## dashboard
- kubectl apply -f dashboard.yaml
- bash dashboardToken.sh # 默认只支持token登陆
```
# dashboardToken.sh
#!/bin/bash
kubectl describe secret -n kubernetes-dashboard $(kubectl get secret -n kubernetes-dashboard | grep dashboard-token | awk '{print $1}') | grep -E '^token' | awk '{print $2}'

```
# 业务迁移到k8s
## harbor
- 采用比较简单的高可用方案
  - nginx做负载均衡
  - harbor原生安装方式部署2个节点提供服务
- 部署
```
# 服务节点
wget 'https://github.com/goharbor/harbor/releases/download/v1.9.4/harbor-offline-installer-v1.9.4.tgz'
tar xzvf harbor-offline-installer-v1.9.4.tgz
cd harbor
# 修改hostname为当前ip
vi harbor.cfg
# 查看镜像等文件目录是否在主机最大空间目录下
vi docker-compose.yml 
sh install.sh


# nginx
vi nginx.conf
user nginx;
worker_processes 1;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
  worker_connections 1024;
}

stream {
  upstream hub {
    server 10.18.3.181:80;
  }
  server {
    listen 80;
    proxy_pass hub;
	  proxy_timeout 300s;
	  proxy_connect_timeout 5s;
  }
}
# restart.sh
#!/bin/bash
docker stop harbornginx && docker rm harbornginx
docker run -itd --net=host --name harbornginx -v /root/harbor/nginx.conf:/etc/nginx/nginx.conf nginx

```
- 使用harbor
  - 本地配置host文件，域名指向ip
  - 登陆harbor并新建kubernetes项目
  - docker tag nginx:latest 域名/项目名/镜像名:TAG
  - docker push 域名/项目名/镜像名:TAG
  - 报错https则在/etc/docker/daemon.json中添加insecure-registries:["域名"]并重启docker
  - 配置harbor的同步规则，使2个仓库进行实时同步

## 服务发现
- 集群内相互访问
  - ClusterIPService通过DNS把name解析成CluseterIP访问集群pod
  - 客户端通过headlessService拿到集群endpoints然后客户端自己实现
- 集群内访问集群外
  - 直接配置ip+port
  - 空的service + 同名的endpoint自动绑定，endpoint里配置外部服务
- 集群外访问集群内
  - NodePortService
  - HostPortService(HostNetwork)
  - Ingress
    - Ingress(host+path到service的配置信息)
    - IngressController(Ingress-nginx)
## 部署ingress-nginx
- wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.27.0/deploy/static/mandatory.yaml
- kubectl apply -f ingress-nginx-mandatory.yaml
- kubectl get all -n ingress-nginx
- wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.27.0/deploy/static/provider/baremetal/service-nodeport.yaml
- kubectl apply -f service-nodeport.yaml #暴露NodePort的80/443
- kubectl label node m1 app=ingress #通过label限制HostPort
- vi ingress-nginx-mandatory.yaml #暴露HostPort，deployment的spec.hostNetwork=true,spec.nodeSelector={app:ingress}
- kubectl get all -n ingress-nginx
- kubectl apply -f ingress-demo.yaml #测试
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-demo
spec:
  selector:
    matchLabels:
      app: tomcat-demo
  replicas: 1
  template:
    metadata:
      labels:
        app: tomcat-demo
    spec:
      containers:
      - name: tomcat-demo
        image: tomcat:8.0.51-alpine
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: tomcat-demo
spec:
  selector:
    app: tomcat-demo
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
    
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: tomcat-demo
spec:
  rules:
  - host: tomcat.test.com
    http:
      paths:
      - path: /
        backend:
          serviceName: tomcat-demo
          servicePort: 80

```
## 定时任务迁移
## springboot迁移
## dubbo迁移
## 传统web迁移
# cicd
## git
## maven
## docker build
## harbor
## 服务发布
## 健康检查
# 重要资源对象
## namespace
## resources
## label
# 调度与编排
## 健康检查
## scheduler
## 部署策略
## pod
# 落地实践
## ingress
## 共享存储pv/pvc/sc
## statefulset
## k8sApi
# 日志和监控
## 日志方案
## 监控入门
## 部署实战
## 监控落地
# istio
## 架构原理
## 核心组件
## 智能路由
## 指标收集和查询
## 分布式追踪
## 总结