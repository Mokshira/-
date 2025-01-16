# X-UI + Cloudflare + Nginx 简易实现科学上网

本文不作过多解释，因为不会，仅仅作为一个过程记录。

前提条件：

- 一个域名（设为example.com）
- 一台带有公网IP的廉价VPS（设公网IP为1.1.1.1）
- 一个Cloudflare账号

## Cloudflare 配置

DNS - 添加记录

名称自己想填什么填什么，IP地址填VPS的公网IP，代理打开，如图。
![image](https://github.com/user-attachments/assets/3c71e586-dd21-435f-a174-daa718f0dd12)


## X-UI配置

部署好X-UI，进入面板。

入站列表 - 添加入站 - 传输选择ws - 路径与Nginx配置文件一致（后文提及），我添加的入站点如图。

![Uploading image.png…]()


其他不会改就不改。

## Nginx配置

装好Nginx，修改配置文件在http块。我的做法是直接备份原始Nginx配置文件，新建一个。

```
http {
    server {
        listen 80;  # Nginx的监听端口
        server_name example.com; # 你的域名或服务器 IP

        location /v2 { # 和上面添加的入站节点路径对应
            proxy_pass http://localhost:27558; # 服务器IP地址和节点入站端口
            proxy_redirect             off;
            proxy_http_version         1.1;
            proxy_set_header Upgrade   $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host      $http_host;
        }
    }
}
```

重新加载Nginx配置文件即可。

