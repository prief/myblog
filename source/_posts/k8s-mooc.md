---
title: k8s-mooc
date: 2019-09-09 22:37:42
tags:
---

# k8s快速入门
## 核心概念
- kubernetes 舵手
- container
- image
- pod
- replicaset
- deployment
## 架构设计
- master
  - etcd
  - apiServer
  - scheduler
  - controllerManager
- worker
  - kubelet
  - docker
## 认证授权
## 集群搭建方案对比
# kubeadm方式搭建
## 环境准备
## 高可用部署
## 可用性测试
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