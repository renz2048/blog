---
layout: post
title: 在ubuntu下使用命令行管理vmware虚拟机
author: renz
date: 2019-12-13 16:41
tags: [PKI, QKD, 量子通信]
mathjax: true
comment: true
published: true
---
# 环境

VMware软件所在操作系统：Ubuntu 18.04.3 LTS

VMware版本：VMware Workstation 15.0.2 build-10952284

# 基本操作

- start 开启
- stop 关闭
- reset 重置
- suspend 挂起
- pause 暂停
- unpause 取消暂停

示例：

```
➜ vmrun -T ws -gu renzheng -gp renzheng start /media/renzheng/D6609591609578C7/work/SSL-5.2.4_SP123456/SSL-5.2.4.vmx nogui
```

- **-T  (ws|fusion||player)**：设置虚拟机类型，ws 表示 workstation
- **-gu renzheng**：设置操作系统的用户名
- **-gp renzheng**：设置操作系统的用户组
- start后跟虚拟机vmx文件路径
- **nogui**：设置后台运行

# 快照操作

- listSnapshots 列出所有快照
- snapshot 创建快照
- deleteSnapshot 删除快照
- revertToSnapshot 切换到快照

示例：

```
➜ vmrun -T ws -gu renzheng -gp renzheng listSnapshots /media/renzheng/D6609591609578C7/work/SSL-5.2.4_SP123456/SSL-5.2.4.vmx
Total snapshots: 27
...
sp6init
➜ vmrun -T ws -gu renzheng -gp renzheng revertToSnapshot /media/renzheng/D6609591609578C7/work/SSL-5.2.4_SP123456/SSL-5.2.4.vmx sp6init
```

- **sp6init**：SSL-5.2.4_SP123456虚拟机的一个快照名
