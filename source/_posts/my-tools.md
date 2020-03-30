---
title: my tools
date: 2019-08-11 11:13:12
tags:
- tools
---

# os
- git
- iterm
- wireshark

# fe
- chrome
- vscode
  - vscode-icons
  - markdown TOC
  - gitlens
  - debugger for chrome
  - code runner
  - Java Extension Pack
  - go
  - python
  - vetur
  - eslint
- node
  - 

# android
- as

# ios
- homebrew
- xcode

# linux
- split
  - -d 以数字自动命名
  - -b 按大小切分 100M split -b 1000M -d filename prefix
  - -l 按行数切分 1000 split -l 10000 -d filename prefix
- git
{% asset_img devopsGitFlow.png devopsGit工作流 %}

- 批量修改密码
  - yum -y install expect tcl
  - touch ~/ip.txt
    - ip root密码
    - 10.6.23.23 root123
  - touch ~/passwd.sh
  - touch ~/action.exp
  - chmod 755 ~/passwd.sh
  - chmod 755 ~/action.exp
  - sh ~/passwd.sh
```
# ~/passwd.sh
#! /bin/bash
for ip in `awk '{print $1}' ~/ip.txt`
do
  pass=`grep $ip ~/ip.txt | awk '{print $2}'`
  expect ~/action.exp $ip $pass
done

# ~/action.exp
#! /bin/expect
set ipaddr [lindex $argv 0]
set passwd [lindex $argv 1]
set timeout 30
spawn ssh root@$ipaddr
expect {
  "yes|no" {send "yes\r";exp_continue}
  "password" {send "$passwd\r"}
}
expect "#"
send "echo newpasswd | passwd --stdin root\r"
send "exit\r"
expect eof
```