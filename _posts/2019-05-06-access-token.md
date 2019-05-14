---
title: Windows访问控制模型
layout: post
date: '2019-05-06'
categories: 高效
permalink: "/archivers/2019-05-06/1"
description: 深入了解一下Windows访问控制模型
---

## 0x00 安全标识符(Security Identifier,SID)

每个参与权限决策的角色都拥有一个SID，这个SID为了保证角色的唯一性。

### SID具体表现形式

`S-[修订级别]-[权值]-[标识符]`

SID分为两种：

- 内置SID
- 自动分配SID

内置SID有：

- S-1-5-18 (LocalSystem)
- S-1-5-19 (LocalService)
- S-1-5-20 (NetworkService)
- S-1-5-32-544 (Administrators)
- S-1-5-32-545 (Users)
- S-1-5-32-550 (PrintOperators)

### 相对标识符(Relative Identifer, RID)

`S-[修订级别]-[权值]-[标识符]-[相对标识符]`

例如：

- S-1-5-21-xxxx-xxx-500 (Administrator) 本地管理员
- S-1-5-21-xxxx-xxx-501 (Guest) 本地来宾用户
- S-1-5-21-xxxx-xxx-1004 (Workstaion) 本地工作站

## 0x01 访问令牌(Access Token)



