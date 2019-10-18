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

Windows访问令牌是用于描述进程或线程的一个安全上下文对象。令牌中的信息包括与进程或线程关联的用户帐户的标识和特权。当用户登录时，系统通过将用户密码与存储在安全数据库中的信息进行比较来验证用户的密码。如果密码已通过身份验证，则系统会生成访问令牌。代表此用户执行的每个进程都具有此访问令牌的副本。

Access Token主要拥有两个部分：

- 用户标识符与用户所属组标识符
- 用户权限列表

Access Token的具体组成：

![2019-06-05-19-00-39](https://rvn0xsy.oss-cn-shanghai.aliyuncs.com/2ea782129468248acb339b2ae2473019.png)

不同用户创建的令牌都是不同的：

![2019-06-05-19-02-28](https://rvn0xsy.oss-cn-shanghai.aliyuncs.com/68921016b1681a80f9fab25b498a8058.png)

其中，Privs描述了当前进程拥有的权限和权限状态。

```
特权名                                    描述                               状态
========================================= ================================== ======
SeIncreaseQuotaPrivilege                  为进程调整内存配额                 已禁用
SeSecurityPrivilege                       管理审核和安全日志                 已禁用
SeTakeOwnershipPrivilege                  取得文件或其他对象的所有权         已禁用
SeLoadDriverPrivilege                     加载和卸载设备驱动程序             已禁用
SeSystemProfilePrivilege                  配置文件系统性能                   已禁用
SeSystemtimePrivilege                     更改系统时间                       已禁用
SeProfileSingleProcessPrivilege           配置文件单一进程                   已禁用
SeIncreaseBasePriorityPrivilege           提高计划优先级                     已禁用
SeCreatePagefilePrivilege                 创建一个页面文件                   已禁用
SeBackupPrivilege                         备份文件和目录                     已禁用
SeRestorePrivilege                        还原文件和目录                     已禁用
SeShutdownPrivilege                       关闭系统                           已禁用
SeDebugPrivilege                          调试程序                           已禁用
SeSystemEnvironmentPrivilege              修改固件环境值                     已禁用
SeChangeNotifyPrivilege                   绕过遍历检查                       已启用
SeRemoteShutdownPrivilege                 从远程系统强制关机                 已禁用
SeUndockPrivilege                         从扩展坞上取下计算机               已禁用
SeManageVolumePrivilege                   执行卷维护任务                     已禁用
SeImpersonatePrivilege                    身份验证后模拟客户端               已启用
SeCreateGlobalPrivilege                   创建全局对象                       已启用
SeIncreaseWorkingSetPrivilege             增加进程工作集                     已禁用
SeTimeZonePrivilege                       更改时区                           已禁用
SeCreateSymbolicLinkPrivilege             创建符号链接                       已禁用
SeDelegateSessionUserImpersonatePrivilege 获取同一会话中另一个用户的模拟令牌 已禁用
```

## 0x02 安全描述符

理解了访问令牌的概念，就要了解被访问对象本身必须存在的一个属性：安全描述符（Security descriptor）。

进程权限检查过程：操作系统获取进程令牌信息与安全描述符进行比较。

安全描述符的组成：

![2019-06-05-19-37-53](https://rvn0xsy.oss-cn-shanghai.aliyuncs.com/7e1f8f62c3eaa4c57dd824523e878935.png)

- SIDs：所有用户、组的SID
- DACL (Discretionary Access Control List) 自主访问控制列表
- SACL （System Access Control List） 系统访问控制列表
- ACE （Access Control List）控制项

DACL通过一系列ACE定义了所有被允许或者禁止的安全对象的访问者。

其中SACL描述了系统应该审核的内容。

