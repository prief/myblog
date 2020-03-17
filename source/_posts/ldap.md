---
title: ldap
date: 2020-02-12 12:12:40
tags:
---

## ldap
- lightweight directory access protocol
- 运行在TCP/IP上的目录访问协议，本质上是一个为只读访问而优化的非关系数据库
- ldap中的信息按照目录信息数组织结构
- 树中的一个节点称之为条目Entry，包含了节点的属性和属性值
- ldap服务的实现
  - windows的AD
  - openldap

## 术语
- Entry/Object 条目或对象，ldap中的每个单元都认为是条目
- DN 条目名称
- OU 组织名称
- DC 域组件，一般多个，如dc=baidu,dc=com
- CN 通用名称，如人名或对象名

## 部署

```
# 安装OpenLDAP
mkdir -p ~/openldap/{config,database}
docker pull osixia/openldap:1.2.2
docker run -d --name ldap-service --hostname ldap-service -p 389:389 -p 689:689 -v ~/openldap/database:/var/lib/ldap -v ~/openldap/config:/etc/ldap/slapd.d --env LDAP_ORGANISATION="test.com" --env LDAP_DOMAIN="test.com" --env LDAP_ADMIN_PASSWORD="password" --env LDAP_TLS=false --detach osixia/openldap:1.2.2

# 图形管理工具
docker pull osixia/phpldapadmin:0.7.2
docker run --name phpldapadmin-service -p 6443:443 -p 6680:80 --hostname phpldapadmin-service --link ldap-service:test.com --env PHPLDAPADMIN_LDAP_HOSTS=test.com --env PHPLDAPADMIN_HTTPS=false --detach osixia/phpldapadmin:0.7.2


# 访问 http://localhost:6680
# 账号 cn=admin,dc=test,dc=com
# 密码 password

```

## 操作
- 增加用户组
- 增加用户
- 更新用户属性
- 对接gitlab/jira系统
