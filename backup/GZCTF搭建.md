参考文章: [[先知](https://xz.aliyun.com/news/14509?time__1311=eqUxuiitKGqDqAIxGNyjDAEDjxYwLq8kDSbD&u_atoken=f314412bb20985b7ac65201ee06ba452&u_asig=0a472f4317417620256342055e0035)](https://xz.aliyun.com/news/14509?time__1311=eqUxuiitKGqDqAIxGNyjDAEDjxYwLq8kDSbD&u_atoken=f314412bb20985b7ac65201ee06ba452&u_asig=0a472f4317417620256342055e0035)

创建目录, 进入目录创建文件, 然后使用 docker-compose.yml 文件来部署平台, 首先创建需要的 json 和 yml 文件, 内容可以根据自己需求更改, 也可以不更改

```
mkdir GZCTF
cd GZCTF
touch appsettings.json
touch docker-compose.yml
```

编辑 appsettings.json 文件

```json
{
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "Database": "Host=db:5432;Database=gzctf;Username=postgres;Password=NBplX8Dg6B"       //必须项
  },
  "EmailConfig": {
    "SendMailAddress": "a@a.com",
    "UserName": "",
    "Password": "",
    "Smtp": {
      "Host": "localhost",
      "Port": 587
    }
  },
  "XorKey": "123456",    //必须项
  "ContainerProvider": {
    "Type": "Docker", // or "Kubernetes"
    "PortMappingType": "Default", // or "PlatformProxy"
    "EnableTrafficCapture": false,
    "PublicEntry": "", // or "xxx.xxx.xxx.xxx" ！必须项, 双引号中放入服务器的公网ip, 用于访问启动的容器
    // optional
    "DockerConfig": {
      "SwarmMode": false,
      "Uri": "unix:///var/run/docker.sock"
    }
  },
  "RequestLogging": false,
  "DisableRateLimit": true,
  "RegistryConfig": {
    "UserName": "",
    "Password": "",
    "ServerAddress": ""
  },`
  "CaptchaConfig": {
    "Provider": "None", // or "CloudflareTurnstile" or "GoogleRecaptcha"
    "SiteKey": "<Your SITE_KEY>",
    "SecretKey": "<Your SECRET_KEY>",
    // optional
    "GoogleRecaptcha": {
      "VerifyAPIAddress": "https://www.recaptcha.net/recaptcha/api/siteverify",
      "RecaptchaThreshold": "0.5"
    }
  },
  "ForwardedOptions": {
    "ForwardedHeaders": 5,
    "ForwardLimit": 1,
    "TrustedNetworks": ["192.168.12.0/8"]        //必须项
  }
}
```

编辑 docker-compose.yml 文件

```yml
version: "3.0"
services:
  gzctf:
    image: gztime/gzctf:latest
    restart: always
    environment:
      - "LANG=zh_CN.UTF-8" # choose your backend language `en_US` / `zh_CN` / `ja_JP`
      - "GZCTF_ADMIN_PASSWORD=2580@Zcx"
    ports:
      - "80:8080"
    volumes:
      - "./data/files:/app/files"
      - "./appsettings.json:/app/appsettings.json:ro"
      # - "./kube-config.yaml:/app/kube-config.yaml:ro" # this is required for k8s deployment
      - "/var/run/docker.sock:/var/run/docker.sock" # this is required for docker deployment
    depends_on:
      - db


  db:
    image: postgres:alpine
    restart: always
    environment:
      - "POSTGRES_PASSWORD=NBplX8Dg6B"
    volumes:
      - "./data/db:/var/lib/postgresql/data"
```

创建完这些文件之后, 直接开始部署
```bash
docker-compose up -d
```

最后直接访问自己的 ip 就行了, 端口默认是 80 , 如果部署在云服务器, 那么就访问公网 ip , 如果访问不了, 就看看云服器上的策略组或叫安全组是否开放 80 端口

如果遇到如下的本地浏览器报错

![Image](https://github.com/user-attachments/assets/5b57a4c9-ee8f-47de-87c3-f6f1556b7a1a)

清理历史记录中的前一个小时的信息即可

![Image](https://github.com/user-attachments/assets/a4897f68-9719-4ad9-803e-9ba90712f93c)

最后重新访问地址就可以了