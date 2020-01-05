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
  - DenyEscalatingExec
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


modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
#查看是否加载
lsmod | grep ip_vs
#配置开机自加载
cat <<EOF>> /etc/rc.local
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod +x /etc/rc.d/rc.local


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
## dashboard
# 二进制方式搭建
## 环境准备
## 高可用部署
## 可用性测试
## dashboard
# 业务迁移到k8s
## harbor
## 服务发现
## 部署ingress-nginx
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