---
title: k8s
date: 2020-04-01 14:53:14
tags:
---

### 开篇
- 可扩展
  - CNI Container Network Interface
  - CSI Container Storage Interface
  - CRI Container Runtime Interface
- 整体架构
  - C/S
    - kubectl作为CLI操作server的master
    - master调度编排node
    - 生产环境的master要部署多个以保证高可用
  - master
    - api-server
    - controller-manager
    - scheduler
    - etcd
  - node
    - kubelet
    - kube-proxy
    - containerRuntime(docker)
    - pod
- 快速部署
  - KIND(k8s in docker)
  - Minikube
    - BIOS开启虚拟化
    - OS安装虚拟机virtualBox

### kubeadm生产可用部署
- 禁用swap
  - cat /proc/swap
  - swapoff -a #关闭swap
  - lsblk #查看设备属性，是否有swap
  - vi /etc/fstab #注释和swap相关的设备
  - free #查看是否还有swap
- 查看机器uuid和mac
  - cat /sys/class/dmi/id/product_uuid
  - ip a
  - 确保全集群唯一
- 查看端口是否被占用
  - yum install -y net-tools
  - netstat -tnlp | grep -E '6443|23[79,80]|1025[0,1,2]'
- 安装组件
  - kubeadm
  - kubelet
  - kubectl
- 配置kubelet由systemd管理
  - /etc/systemd/system/kubelet.service
  - /etc/systemd/system/kubelet.service.d/kubadm.conf
  - systemctl enable kubelet
- 安装前置依赖
  - CRI管理软件包cri-tools
    - crictl
    - critest
  - socat
    - 实现端口转发
    - yum install -y socat
- 初始化
  - kubeadm config images pull
  - 或k8s.gcr.io改为registry.aliyuncs.com/google_containers，docker pull下来后再改tag
  - kubeadm init
  - kubeadm join
- 配置kubectl
  - mkdir -p $HOME/.kube
  - cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  - chown $(id -u):$(id -g) $HOME/.kube/config
- 验证
  - kubectl cluster-info
  - kubectl get nodes
- 配置网络
  - sysctl net.bridge.bridge-nf-call-iptables=1
  - flannel需要kubeadm init时传递 --pod-network-cidr=10.244.0.0/16
  - 重置kubeadm reset
  - kubectl apply -f config.yaml
  - kubectl get pods --all-namespaces

### kubectl
- kubectl options
- kubectl [flags] [options]
- $HOME/.kube/config
  - 集群api地址
  - 用于认证的证书地址
- kubectl get nodes -o yaml
- kubectl get all
- kubectl api-resources
- kubectl explain 
- kubectl run NAME --image=image
- kubectl expose deploy/redis --port 6379 --protocol=TCP --target-port=6379 --name=redis-server --type=ClusterIP｜NodePort
- kubectl port-forward  svc/redis-server 6379:6379 #把内部ClusterIP的svc暴露外部访问
- kubectl scale deploy/redis --replicas=3

### 核心对象
- Pod
- Deployment
- ReplicaSet
- Service
  - ClusterIP 默认的service类型，仅集群内可访问的虚拟IP
  - NodePort 在node上绑定固定port(port范围在service-node-port-range配置，默认30000-32767)可以通过nodeIP:nodePort访问服务
  - LoadBalancer 通过云供应商CloudProvider创建一个外部负载均衡器，自动创建所需的nodePort或ClusterIP
  - ExternalName 通过将服务由DNS CNAME转发到指定的域名，需要kube-dns支持

### 认证与授权
- api安全保证
  - Authentication 识别用户身份认证
  - Authorization 控制用户对资源访问的授权
  - AdmissionControl 资源管理的准入控制
- 跳过安全保证
  - kubectl proxy可以直接通过http访问服务
  - 直接访问kube-apiserver上启动服务时配置的insecure-port默认8080
- k8s用户
  - 一般用户
    - 只能通过外部服务管理
    - 无法通过api添加到集群
  - serviceAccount
    - 由k8sApi管理的用户，与特定的namespace绑定
    - 会自动挂到pod容器中的/var/run/secrets/kubernetes.io/serviceaccount/目录中，其中包含namespace,token等
- k8sApi操作
  - 与serviceAccount相关
  - 匿名请求
    - kube-apiserver的--anonymous-auth参数控制是否开启，默认开启
    - 匿名用户默认的用户名 system:anonymous
    - 匿名用户默认的所属组 system:unauthenticated
- 认证机制
  - X509客户端证书
    - 默认/etc/kubernetes/pki/ca.crt
    - 认证时使用证书subject的CN(common name)域作为用户名，O(organization)域作为组名
  - 引导token
    - kubeadm init后会显示token
    - 非kubeadm需要设置 enable-bootstrap-token-auth=true
  - 静态token文件
    - 启动kube-apiserver时配置--token-auth-file=FILE
    - 请求时加上 Authorization: Bearer TOKEN请求头
  - 静态密码文件
    - 启动kube-apiserver时配置--basic-auth-file=FILe
    - 请求时加上 Authorization: Basic BASE64ENCODED(USER:PASS)
  - serviceAccountToken
  - OpenID:提供了OAuth2的认证支持,云厂商也支持
  - 认证代理:配合身份验证代理进行使用,比如提供通用的授权网关
  - webhook:提供webhook配合远端服务器使用
- 授权机制
  - 支持多种授权机制
    - ABAC(attribute-based access control) 基于属性的访问控制
    - RBAC(role-based access control) 基于角色的访问控制，类似mongoDB
    - Node 特殊的授权机制，专门用于kubelet发出的api请求做授权验证
    - Webhook 使用外部服务进行
    - AlwaysAllow 允许全部，这个是默认配置
    - AlwaysDeny 禁止全部，通常用于测试
  - 默认的授权都是拒绝
  - 当某种授权拒绝或通过后便会返回，不再请求其他授权机制
  - 当所有授权机制都未通过便返回403
- 角色
  - 分类
    - Role 一组权限的集合，但被限制在namespace内
    - ClusterRole 对于集群级别的资源是不被namespace限制的，并且还有一些非资源类的请求
  - 绑定
    - rolebinding
    - clusterrolebinding
- 查看kubectl用户权限
  - kubectl config current-context
  - kubectl config view users -o yaml
  - kubectl config view users --raw -o jsonpath='{ .users[?(@.name == "kubernetes-admin")].user.client-certificate-data}' |base64 -d  #显示证书内容
  - kubectl config view users --raw -o jsonpath='{ .users[?(@.name == "kubernetes-admin")].user.client-certificate-data}' |base64 -d |openssl x509 -text -noout #显示证书
- 实践：创建权限可控的用户
  - 创建ns
    - kubectl create namespace work
  - 创建用户
    - mkdir work && cd work
    - openssl genrsa -out backend.key 2048 #创建私钥
    - openssl req -new -key backend.key -out backend.csr -subj "/CN=backend/O=dev" #生成csr
    - openssl x509 -req -in backend.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out backend.crt -days 365 #CA签名
    - openssl x509 -in backend.crt -text -noout #查看证书文件，观察subject的CN和O
  - 添加context
    - kubectl config set-credentials backend --client-certificate=/root/work/backend.crt --client-key=/root/work/backend.key #添加用户
    - kubectl config set-context backend-context --cluter=kubernetes --namespace=work --user=backend #创建context
  - 测试访问
    - kubectl --context=backend-context get pods -n work -v 5
  - 创建role和rolebinding
    - kubectl create -f backend-role.yaml
    - kubectl get roles -n work -o yaml
    - kubectl create -f backend-rolebinding.yaml
    - kubectl get rolebinding -n work -o yaml
  - 测试用户权限
    - kubectl --context=backend-context get pods -n work -v 5
    - kubectl --context=backend-context get deploy -n work
    - kubectl --context=backend-context get ns

```
# backend-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: work
  name: backend-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]


# backend-rolebinding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: backend-rolebinding          
  namespace: work
subjects:      
- kind: User
  name: backend
  apiGroup: ""     
roleRef:    
  kind: Role 
  name: backend-role
  apiGroup: ""

```

- k8s内置调试/验证机制
  - kubectl auth can-i list pods -n work --as="backend"
  - kubectl auth can-i list deploy -n work --as="backend"
  - 也可以使用~/.kube/config中的配置进行调试

### 实战发布
- vi Dockerfile
- vi .dockerignore
- docker build -t repo/name:tag .
- docker login / $HOME/.docker/config.json
- docker push repo/name:tag
- docker-compose发布
  - vi docker-compose.yaml
  - docker-compose up
  - docker-compose ps
- k8s发布
  - 交互方式
    - 命令式 kubectl run redis --image=''
    - 命令式对象配置 kubectl create -f 
    - 声明式对象配置 kubectl apply -f
  - vi namespace.yaml && kubectl apply -f namespace.yaml
  - vi redis-deployment.yaml && kubectl apply -f redis-deployment.yaml
  - kubectl -n work get all
  - kubectl -n work exec -it HASH bash && redis-cli
  - vi redis-service.yaml && kubectl apply -f redis-service.yaml
  - kubectl -n work get svc
  - vi backend-deployment.yaml && kubectl apply -f backend-deployment.yaml
  - vi backend-svc.yaml && kubectl apply -f backend-svc.yaml
  - vi fe-deployment.yaml && kubectl apply -f fe-deployment.yaml
  - vi fe-svc.yaml && kubectl apply -f fe-svc.yaml

### helm
- helm是k8s的包管理器，类比yum/apt/homebrew/pip
- 简化包分发/安装/版本管理等操作流程
- CS架构
  - 客户端helm
  - 服务端tiller
- helm
  - helm version
  - 默认会读取$HOME/.kube/config 中的集群地址
- tiller
  - 直接执行tiller默认启动44134(和helm通信)和44135(用于探活)端口的服务
  - 使用客户端连接tiller时要设置HELM_HOST环境变量(44134服务)
  - 确保$HOME/.kube/config正确配置
- 默认安装
  - helm init [--tiller-image 国内tiller镜像]
  - 会在k8s集群中部署tiller和初始化helm的默认目录$HELM_HOME默认$HOME/.helm
  - 默认会生成kube-system命名空间下的tiller-deploy
- 核心概念
  - chart
    - helm所管理的包，类似yum的rpm
    - 包含应用要部署到集群上必须的所有资源
  - release
    - chart在集群部署后的实例
    - chart每次部署都会产生一次release
  - repository
    - 存储chart的仓库
    - 默认情况下helm初始化时有2个仓库:stable和local
  - config
    - 部署时可以自定义的配置
    - 真正执行部署时会把config和chart进行合并
- 工作原理
  - helm通过gRPC将chart发送至tiller
  - tiller通过内置的k8s客户端库与api-server交互，默认采用ClusterIP的service部署chart，通过socat端口转发数据
  - 生成release用于管理
- 实践
  - helm create name
  - tree -a name
    - Chart.yaml
    - charts
    - .helmignore
    - templates
      - Notes.txt #在helm install完成后会回显，用于使用说明
    - values.yaml
  - helm package name 
  - helm install name|path-to-name-version.tgz

### kube-apiserver
- 整个集群的统一入口
- 默认6443端口，可通过--secure-port配置
- 也可配置--insecure-port，通过这个端口访问无需认证，生产环境应该配置为0以禁用此功能
- kubectl version -v 8 #8是指logLevel
- curl -k https://172.17.0.99:6443/version
- kubectl proxy & # 使用KUBECONFIG中的配置在本地和集群之间创建一个代理
- 核心功能
  - 认证
  - 授权
  - 准入控制(默认开启以下插件)
    - NamespaceLifecycle
      - 保证正在终止的namespace不允许创建对象
      - 不允许请求不存在的namespace
      - 保证default和kube-system的namespace不被删除
    - LimitRanger 为pod设置默认请求资源的限制
    - ServiceAccount 可按预设规则创建sa
    - DefaultStorageClass 为PVC设置默认StorageClass
    - DefaultTolerationSeconds 设置pod默认的forgiveness toleration为5分钟
    - ResourceQuota 限制pod请求配额
    - AllwaysPullImages 总是拉取镜像
    - AllwaysAdmit 总是接受所有请求
  - CRUD处理

### etcd
- 概述
  - 分布式，高可用，强一致的键值存储，还有监听机制
  - go语言编写
  - Raft协议作为一致性算法，需要部署奇数个节点3，5，7
  - 常用于分布式系统中的服务注册发现或存储系统的关键数据
- 操作
  - kubectl -n kube-system get pods ｜ grep etcd
  - kubectl -n kube-system exec -it NAME sh
  - ETCDCTL_API=3 etcdctl --key=/etc/kubernetes/pki/etcd/server.key  --cert=/etc/kubernetes/pki/etcd/server.crt  --cacert=/etc/kubernetes/pki/etcd/ca.crt member list
  - ETCDCTL_API=3 etcdctl --key=/etc/kubernetes/pki/etcd/server.key  --cert=/etc/kubernetes/pki/etcd/server.crt  --cacert=/etc/kubernetes/pki/etcd/ca.crt get /registry --prefix --keys-only #集群元信息基本都是在/registry下
  - ETCDCTL_API=3 etcdctl --key=/etc/kubernetes/pki/etcd/server.key  --cert=/etc/kubernetes/pki/etcd/server.crt  --cacert=/etc/kubernetes/pki/etcd/ca.crt get /registry/namespaces --prefix --keys-only #查看ns
  - kubectl create  ns NAME #创建ns并再次查看

### controller-manager
- 实际包含2部分
  - kube-controller-manager 嵌入了k8s核心控制循环的守护进程
  - cloud-controller-manager 为各云厂商提供抽象封装便于使用自己的provide
- 作用
  - 通过apiserver提供的信息持续监控集群状态，并尝试将集群调整至预期状态
  - 通过默认的10252端口提供健康检查的/healthz和监控的/metrics接口

### kube-scheduler
- 集群调度器，将预期的pod资源调度到最佳的node上
- 是一个策略丰富，拓扑感知的调度程序，显著影响可用性，性能和容量
- 处理阶段
  - computing predicates 解决pod能否调度到node上的问题(pod亲和性/是否可调度等)
  - prioritizing 解决上个阶段得到的nodes中哪个是最优的，得到一个优先级列表
  - selecting host 最终选择pod调度到哪台node上

### kubelet
- 负责node和pod的相关管理任务
- 作用
  - node管理
    - 向master注册node并上报机器信息
    - 可配置node上的maxPods数，健康检查机制，认证授权机制，驱逐策略等
    - 清理磁盘等工作
  - pod管理
    - 接收scheduler的调度并确保pod按预期工作
    - 确保pod的健康检查(livenessProbe和readinessProbe)
    - 确保资源监控(cAdvisor)

### kube-proxy
- 运行于node上的网络代理组件，提供了TCP/UDP的连接转发的支持
- 查看svc后端具体pod的服务可以查看endpoints对象
- 工作模式
  - userspace 效率不高不推荐
  - iptables 默认，问题是给机器增加很多规则
  - ipvs 为了解决iptables的性能问题而引入，采用增量方式更新

### containerRuntime(docker)
- k8s中的CRI即可支持kubelet使用不同的runtime，而不需要重新编译
- 当前最广泛的使用就是docker

### troubleshoot
- kubectl describe
- kubectl get events
- kubectl get endpoints #svc是通过endpoints来工作的，这里可以看到pod的端口等
- 修复
  - 版本控制yaml文件变更后重新apply
  - kubectl edit #不推荐使用，不会保留历史

### dashboard
- kubectl apply -f dashboard.yaml
- 访问
  - 默认的部署方式svc使用了ClusterIP无法正常访问
  - kubectl -n kube-system port-forward pod/NAME 8443 # 利用socat进行端口转发
  - kubectl -n kube-system get serviceaccount -l k8s-app=kubernetes-dashboard -o yaml # 查找secrets
  - kubectl -n kube-system describe secrets NAME # 通过secrets获取token
- 参考dashboard服务可以基于k8s开发云平台

### coreDNS
- 是一个独立的DNS项目，可集成k8s，也可单独使用
- 扩展性很强，很多功能都是通过插件完成
  - 内置插件
  - 丰富的三方插件
- 验证
  - kubectl run alpine -it --rm --restart='Never' --image='alpine' sh
  - apk add --no-cache bind-tools
  - dig @PODIP SVCNAME.NS.svc.cluster.local +noall +answer
- 特性
  - coreDNS的解析是全局的，跨ns的，但不代表网络互通
  - 域名有特定的格式 SVCNAME.NS.svc.cluster.local
  - 使用configMap方式进行的配置，如果更改了配置需要重启pod才能生效 kubectl -n kube-system get cm coredns -o yaml

### ingress
- kubectl explain ingress
- 是一组允许外部请求进入集群的路由规则的集合，可以给svc提供集群外访问的url/ssl终止/负载均衡等
- 起到了智能路由的角色，外部流量到达ingress，再由它按制定好的规则分发到不同的后端
- ingress与loadBalancer类型的svc区别
  - ingress不是一种svc类型
  - ingress可以由多种控制器(实现方式)
    - nginxIngressController是使用cm存储nginx的配置实现的(支持会话保持和动态配置)
    - google的GLBC
    - 第三方的实现
      - 基于envoy的contour
      - F5的F5 BIG-IPController
      - 基于haproxy的haproxy-ingress
      - 基于istio的traffic
- 使用
  - 创建ns
  - 创建cm，主要是给controller使用
  - 创建Role/RoleBinding
  - 部署nginxIngressController
  - 检查 kubectl -n ingress-nginx get all
  - 将nginxIngressController暴露至集群外(这里选择nodePort方式创建svc)
  - 测试 curl NODE:PORT
  - 部署ingress
  - 测试 
    - kubectl -n work get ingress
    - 访问DOMAIN:暴露ingress时创建的svc的端口
```
# ns
apiVersion: v1
kind: Namespace
metadata:
  name: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

# cm
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx-configuration
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

---
kind: ConfigMap
apiVersion: v1
metadata:
  name: tcp-services
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

---
kind: ConfigMap
apiVersion: v1
metadata:
  name: udp-services
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

---

# Role/Rolebinding
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: nginx-ingress-clusterrole
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
rules:
  - apiGroups:
      - ""
    resources:
      - configmaps
      - endpoints
      - nodes
      - pods
      - secrets
    verbs:
      - list
      - watch
  - apiGroups:
      - ""
    resources:
      - nodes
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - services
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "extensions"
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - ""
    resources:
      - events
    verbs:
      - create
      - patch
  - apiGroups:
      - "extensions"
    resources:
      - ingresses/status
    verbs:
      - update

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: nginx-ingress-role
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
rules:
  - apiGroups:
      - ""
    resources:
      - configmaps
      - pods
      - secrets
      - namespaces
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - configmaps
    resourceNames:
      - "ingress-controller-leader-nginx"
    verbs:
      - get
      - update
  - apiGroups:
      - ""
    resources:
      - configmaps
    verbs:
      - create
  - apiGroups:
      - ""
    resources:
      - endpoints
    verbs:
      - get

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: nginx-ingress-role-nisa-binding
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: nginx-ingress-role
subjects:
  - kind: ServiceAccount
    name: nginx-ingress-serviceaccount
    namespace: ingress-nginx

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: nginx-ingress-clusterrole-nisa-binding
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: nginx-ingress-clusterrole
subjects:
  - kind: ServiceAccount
    name: nginx-ingress-serviceaccount
    namespace: ingress-nginx

---

# nginxIngressController
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-ingress-controller
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: ingress-nginx
      app.kubernetes.io/part-of: ingress-nginx
  template:
    metadata:
      labels:
        app.kubernetes.io/name: ingress-nginx
        app.kubernetes.io/part-of: ingress-nginx
      annotations:
        prometheus.io/port: "10254"
        prometheus.io/scrape: "true"
    spec:
      serviceAccountName: nginx-ingress-serviceaccount
      containers:
        - name: nginx-ingress-controller
          image: taobeier/nginx-ingress-controller:0.21.0
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
            - --tcp-services-configmap=$(POD_NAMESPACE)/tcp-services
            - --udp-services-configmap=$(POD_NAMESPACE)/udp-services
            - --publish-service=$(POD_NAMESPACE)/ingress-nginx
            - --annotations-prefix=nginx.ingress.kubernetes.io
          securityContext:
            capabilities:
              drop:
                - ALL
              add:
                - NET_BIND_SERVICE
            # www-data -> 33
            runAsUser: 33
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
          livenessProbe:
            failureThreshold: 3
            httpGet:
              path: /healthz
              port: 10254
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /healthz
              port: 10254
              scheme: HTTP
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1

# 暴露至集群外
apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
    - name: https
      port: 443
      targetPort: 443
      protocol: TCP
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx

# ingress
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: name
  namespace: work
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - host: DOMAIN
    http:
      paths:
      - path: /
        backend:
          serviceName: SVCNAME
          servicePort: 80


```

### 监控
- 监控点
  - node情况
  - k8s集群自身状态
  - 部署在集群内的应用的状态
- prometheus
  - 非常灵活易于扩展
  - 通过各种exporter暴露数据
  - 由prometheus server定时拉取数据然后存储
  - 自带简单的前端界面可以使用promQL进行查询
- 安装
  - 推荐prometheusOperator
  - 具体过程
    - 创建ns
    - 创建role/rolebinding
    - 创建prometheus的配置文件cm
    - 创建部署文件deploy
    - 执行完毕后查看情况 kubectl -n monitoring get all
    - 使用svc将服务暴露/也可使用ingress
    - 安装nodeExporter(daemonSet)
