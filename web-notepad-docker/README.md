> 本项目根据[minimalist-web-notepad](https://github.com/pereorga/minimalist-web-notepad)开源项目稍加微调开发而成，现源代码已开源至我的GitHub上。

### 介绍

一款免登录，可分享，实时保存的纯文本记事本，既可用于临时记录文本，也可真正的充当你的记事本。

### 镜像安装

1.新建`/root/data/notepad`文件夹充当云记事本源目录，下载源代码至该目录下

2.解压压缩包，进入`web-notepad-docker`目录，把当前目录源码编译成镜像文件

```shell
docker build -t web-notepad .
```

3.运行镜像文件

```shell
docker run -d -it --restart=always --name web-notepad -v /root/data/notepad/notepad-data:/var/www/html/_tmp -p 9001:80 web-notepad
注:9001代表本地映射端口，80代表容器映射端口
```

4.Nginx配置反向代理以及SSL证书

```shell
upstream note{
	server 127.0.0.1:9001; # 本地地址
}

server {
        listen 443 ssl;
        server_name 域名;

        ssl on;
        ssl_certificate  pem文件路径;
        ssl_certificate_key key文件路径;
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        location / {
            proxy_pass http://note;
            proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
}

```

