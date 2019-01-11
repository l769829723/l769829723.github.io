---
title: nginx通过ldap方式验证
date: 2019-01-11 17:37:50
tags: nginx, ldap
---

## 需求：

> 在没有认证功能的web应用上添加ldap认证方式，能够实现登陆功能的同时又能够不用管理账号。

## 前提条件

> 安装一个ldap模块在Nginx上，由于Nginx本身并没有自带认证功能，所以需要重新编译源代码并安装。

```shell
# 下载ldap模块到本地目录
git clone https://github.com/kvspb/nginx-auth-ldap/archive/master.zip
# 解压缩
unzip master.zip
# 如果没有unzip工具，就安装一个，例如（ubuntu系统）：sudo apt install -y unzip
```

## 编译安装

```shell
# 确认版本是不是没有安装过该扩展，没有包含nginx-auth-ldap-master的即需要安装扩展
nginx -V
# --user=nginx --group=nginx --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-http_gzip_static_module --with-http_stub_status_module --with-http_ssl_module --with-pcre --with-file-aio --with-http_realip_module --without-http_scgi_module --without-http_uwsgi_module --without-http_fastcgi_module --with-http_auth_request_module --add-module=${HOME}/nginx-auth-ldap-master

# 编译
./configure --user=nginx --group=nginx --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-http_gzip_static_module --with-http_stub_status_module --with-http_ssl_module --with-pcre --with-file-aio --with-http_realip_module --without-http_scgi_module --without-http_uwsgi_module --without-http_fastcgi_module --with-http_auth_request_module --add-module=${HOME}/nginx-auth-ldap-master

# --add-module=后面添加上之前unzip解压之后的路径

# 安装
make && sudo make install
```

## 配置Nginx

> 我这里贴一个配置示例，然后说明下各项的作用

```conf
ldap_server ldap_srv1 {
  url ldaps://LDAP_SERVER:PORT/OU=Users,OU=IT,OU=department,DC=example,DC=local?sAMAccountName?sub?(objectClass=person);
  binddn "operator_account@example.local";
  binddn_passwd "secret";
  group_attribute uniquemember;
  group_attribute_is_dn of;
  require valid_user;
  referral: off;
}

server {
  listen 8080;
  server_name localhost;

  auth_ldap "Forbidden";
  auth_ldap_servers ldap_srv1;

  location / {
    proxy_pass http://your_real_app_address/;
  }
}
```

## 配置项的作用

选项 | 值
--|--
url|ldap 服务器的`url`地址，这里有可能是ldaps或者是ldap，要具体看情况。 `OU`一般是一个组织，例如：一个部门或者一个子公司，DC一般指域名，例如：`example.com` 就是`dc=example,dc=com`。`？`后后面的全部复制就好了。
binddn|这里一般要填一个`有操作权限的账号`，这个地方一开始挺疑惑的。
binddn_passwd|这个就填操作权限的那个账号的密码
group_attribute|这个默认`uniquemember`就可以了
group_attribute_is_dn|这个如果选择了`on`是比较严谨的，就是说你登陆的那个账号必须是属于你指定的`OU`里面的用户才行，`off`自然就是不验证是不是属于指定的`OU`里面的用户。关于这个`OU`可以参考url里面的那个`OU`，决定是`on`还是`off`，推荐选`off`。
require|默认值`valid_user`就可以了
referral|如果是`微软的ldap`，推荐选择`on`，`openldap`可以设置为`off`。

## 验证和测试

> 打开浏览器，在地址栏输入`http://your_nginx_server_address:8080`，在跳出的对话框里面填上你的`ldap`账号和密码测试登陆，登陆成功之后会跳转到你的应用，如果登陆失败需要确认账号和密码并检查确认配置。
