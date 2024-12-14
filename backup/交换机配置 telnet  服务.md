使用一个交换机做成 telnet 服务, telnet 可以使用指定端口开启三层交换机, 用于与 pc 互通, 也可以使用自带的 vlan1 设置 ip 然后达到互通, 因为华三的交换机端口默认是 access 口, 默认带 vlan1 , 直接设置 vlan1 的 ip 也就可以实现互通

## 实现互通
互通的两种方式
### 设置 vlan1 的 ip
端口默认带 access , vlan1 , 直接进入, 然后设置 ip
```bash
int vlan 1
ip a 192.168.1.254 24
```

### 设置端口 ip
因为交换机是二层, 需要端口开启三层交换机
```bash
# 开启三层交换机
int g1/0/1
prot link-mode route
# 设置 ip
ip a 192.168.1.254 24
```

# 三种不同密码类型登录
拓扑图
![image](https://github.com/user-attachments/assets/46e75716-98f2-47fc-8924-1b3d28ff20bf)

## none 登录
顾名思义, 就是不需要密码就可以登录

### 设置端口:
```bash
int g1/0/1
port link-mode route
ip a 192.168.1.254 24
```

### 开启 telnet
默认是不开启服务的, 先设置开启服务
```bash
telnet server enable
```

### 设置远程登录端口
0 - 4 是允许 5 个用户同时登录
```bash
user-interface vty 0 4
```

### 设置密码类型
三个类型简单区别
authentication-mode none # 无密码登录
authentication-mode password # 有密码登录, 只有一个密码
authentication-mode scheme # 多用户登录, 有多个密码

使用 password 登录
```bash
authentication-mode none
```


### 设置用户接入的协议类型
protocol inbound telnet # telnet 协议
protocol inbound ssh # ssh 协议
protocol inbound all # 所有

使用 telnet 协议
```bash
protocol inbound telent
```

### 设置登录级别
默认登录级别很低, 能操作的功能很少, 下面列出几种级别, 15 最高, 默认级别是 `network-operator` 也可以不用设置
```bash
[H3C-line-vty0-4]user-role ?
  STRING<1-63>      User role name
  network-admin
  network-operator
  level-0
  level-1
  level-2
  level-3
  level-4
  level-5
  level-6
  level-7
  level-8
  level-9
  level-10
  level-11
  level-12
  level-13
  level-14
  level-15
  security-audit
  guest-manager
```

设置 level-15 级吧
```bash
user-role level-15
```

然后就 ok 了, 可以在 pc 端连接, 要在普通模式下登录
```bash
telnet 192.168.1.254
```
登录成功后就可以看到和 SW 上操作的效果一样


## password登录
拓扑图:
![image](https://github.com/user-attachments/assets/0e0ef1f4-8cd5-4990-9dd9-12647466b16c)

### 设置端口:
```bash
int g1/0/1
port link-mode route
ip a 192.168.1.253 24
```
### 开启 telnet
默认是不开启服务的, 先设置开启服务
```bash
telnet server enable
```

### 设置远程登录端口
0 - 4 是允许 5 个用户同时登录
```bash
user-interface vty 0 4
```

### 设置密码类型
三个类型简单区别
authentication-mode none # 无密码登录
authentication-mode password # 有密码登录, 只有一个密码
authentication-mode scheme # 多用户登录, 有多个密码

使用 password 登录
```bash
authentication-mode password
```

### 设置加密方式
这里有两种加密方式
set authentication password simple # 简单的密码
set authentication password hash # hash 加密

使用简单密码加密
```bash
set authentication password simple asdf1234
```

### 设置用户接入的协议类型
protocol inbound telnet # telnet 协议
protocol inbound ssh # ssh 协议
protocol inbound all # 所有

使用 telnet 协议
```bash
protocol inbound telent
```

### 设置登录级别
默认登录级别很低, 能操作的功能很少, 下面列出几种级别, 15 最高
```bash
[H3C-line-vty0-4]user-role ?
  STRING<1-63>      User role name
  network-admin
  network-operator
  level-0
  level-1
  level-2
  level-3
  level-4
  level-5
  level-6
  level-7
  level-8
  level-9
  level-10
  level-11
  level-12
  level-13
  level-14
  level-15
  security-audit
  guest-manager
```

设置 level-15 级吧
```bash
user-role level-15
```

然后就 ok 了, 可以在 pc 端连接, 要在普通模式下登录
```bash
telnet 192.168.1.254
```
登录成功后就可以看到和 SW 上操作的效果一样

### 清除登录用户信息
最后, 如果想要去除起初允许了哪些范围内的用户可以登录 (以什么协议类型登录, 以什么级别登录, 以什么密码登录) , 可以使用 undo 去除这些权限, 自然会解除这些范围内的用户登录到交换机, 因为创建这个范围用户且没有设置这些权限的时候, 这些用户还算不上可以登录到交换机, `di th` 也自然看不到哪些范围内用户可以登录的信息

## scheme登录
拓扑图:
![image](https://github.com/user-attachments/assets/ca0f3b8b-21c2-49dd-a38f-ad51f56b3949)

### 设置端口:
```bash
int g1/0/1
port link-mode route
ip a 192.168.1.252 24
```

### 开启 telnet 服务
```bash
telnet server enable
```

### 设置远程登录端口
```bash
user-interface vty 0 4
```

### 设置密码类型
```bash
authentication-mode scheme
```

### 创建用户
需要回到 sy 界面, 也就是系统配置界面, 添加的用户 `h1` 并加入到 manage 组中, 默认是会添加到 `manager` 组中, 也可以后面加 `class manager` 添加到组中
```bash
local-user h1 
```

### 创建用户密码
添加的密码(长度为10), 也可以选择 hash 加密, 这里使用的是 simple
```bash
password simple asdf1234!@#$
```

### 设置用户接入的协议
该用户通过什么服务接入到交换机
```bash
service-type telnet
```

### 设置用户登录级别
该用户以什么权限接入交换机, 默认权限是 `network-operator` , 下面的可以不执行
```bash
authorization-attribute user-role level-15
```

最后 pc4 就可以通过 telnet 连接交换机了


## ### 清除登录用户信息
最后, 如果想要去除起初允许了哪些范围内的用户可以登录 (以什么协议类型登录, 以什么级别登录, 以什么密码登录) , 可以使用 undo 去除这些权限, 自然会解除这些范围内的用户登录到交换机, 因为创建这个范围用户且没有设置这些权限的时候, 这些用户还算不上可以登录到交换机, `di th` 也自然看不到哪些范围内用户可以登录的信息

#### 比如:
查看这 5 个用户有哪些权限
```bash
<SW1>sy
System View: return to User View with Ctrl+Z.
[SW1]user-interface vty 0 4
[SW1-line-vty0-4]di th
#
line aux 0
 user-role network-operator
#
line con 0
 user-role network-admin
#
line vty 0 4
 authentication-mode none
 user-role network-operator
 protocol inbound telnet
#
line vty 5 63
 user-role network-operator
#
return
```

可以看到权限如下:
```bash
line vty 0 4
 authentication-mode none
 user-role network-operator
 protocol inbound telnet
```

然后清除对应的权限
```bash
[SW1-line-vty0-4]undo authentication-mode
[SW1-line-vty0-4]undo protocol inbound
[SW1-line-vty0-4]undo user-role
```

再次查看权限:
```bash
[SW1-line-vty0-4]di th
#
line aux 0
 user-role network-operator
#
line con 0
 user-role network-admin
#
line vty 0 63
 user-role network-operator
#
return
```
发现已经没了