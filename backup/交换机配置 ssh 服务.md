拓扑图:
![image](https://github.com/user-attachments/assets/86eaa311-d458-470a-af93-4ab90ad6de9b)

和配置 telnet 服务差不多, 唯一不同的是, ssh 服务, 只可以设置密码类型为 `scheme` , 因为安全


### 开启 ssh 服务
```bash
ssh server enable
```

### 设置允许登录数量
```bash
user-interface vty 0 4
```

### 设置密码类型
```bash
authentication-mode scheme
```

### 创建用户
有默认权限, 默认
```bash
local-user h1
```

### 设置用户密码
```bash
password simple asdf1234!@#$
```

### 设置用户访问的服务
```bash
service-type ssh
```

最后在 pc 机上 ssh 192.168.1.254 输入用户名和密码就可以连接了