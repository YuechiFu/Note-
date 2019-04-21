# centos7 相关服务搭建和配置

## 端口&防火墙

### 防火墙操作

#### firewalld 的基本使用

`yum install firewalld`

| 命令                                   | 作用               | 备注 |
| -------------------------------------- | ------------------ | ---- |
| `firewall-cmd --state`                 | 查看防火墙状态     |      |
| `systemctl start firewalld`            | 开启防火墙         |      |
| `systemctl stop firewalld`             | 关闭防火墙         |      |
| `systemctl disable firewalld`          | 禁止开机启动       |      |
| `systemctl restart firewalld.servic`   | 重启防火墙         |      |
| `firewall-cmd --permanent --list-port` | 查看端口列表       |      |
| `firewall-cmd --list-all`              | 显示防火墙应用列表 |      |
| `firewall-cmd --query-service ftp`     | 查看服务的启动状态 |      |
| `firewall-cmd --add-service=ftp`       | 暂时开放 ftp 服务  |      |

#### firewall 开启和关闭端口

| 命令                                                         | 作用                   | 备注             |
| ------------------------------------------------------------ | ---------------------- | ---------------- |
| `firewall-cmd --zone=public --add-port=8080/tcp --permanent` | 开启端口(以 8080 为例) |                  |
| `firewall-cmd --remove-port=80/tcp --permanent`              | 关闭端口               |                  |
| `firewall-cmd —reload`                                       | 重新载入配置           | 修改->重启->重载 |
| `firewall-cmd --query-port=80/tcp`                           | 检查端口是否已经开启   |                  |

**参数介绍**

**—zone=public**：表示作用域为公共的；

**—add-port=8080/tcp**：添加 tcp 协议的端口 8080；

**—permanent**：永久生效，如果没有此参数，则只能维持当前服务生命周期内，重新启动后失效；

## centos 安装 shadowsocks

### 1.安装

```
yum update //非必须
yum install python-setuptools && easy_install pip
pip install shadowsocks
```

### 2.配置 shadowsock.json

#### 多用户

```json
{
  "server": "0.0.0.0",
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "port_password": {
    "8989": "******",
    "8988": "******",
    "8987": "******",
    "9001": "******",
    "9002": "******",
    "9003": "******",
    "9004": "******"
  },
  "timeout": 300,
  "method": "rc4-md5",
  "fast_open": false
}
```

#### 单用户

```json
{
  "server": "0.0.0.0",
  "server_port": 8388,
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "password": "mypassword",
  "timeout": 300,
  "method": "rc4-md5"
}
```

### 3.启动 ss

#### 根据 shadowsock.json 文件中启动

| 命令                                                                             | 作用           | 备注      |
| -------------------------------------------------------------------------------- | -------------- | --------- |
| `ssserver -c /etc/shadowsocks.json -d start`                                     | 直接启动       |           |
| `ssserver -c /etc/shadowsocks.json --log-file /var/log/shadowsocks.log -d start` | 带记录日志启动 | 推荐·常用 |
| `tail -f /var/log/shadowsocks.log`                                               | 常看日志       |           |

## 原版 Google BBR && 魔改版 Google BBR

参考 https://www.vultrcn.com/5.html

### 原版 Google BBR

#### 安装

`wget —no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh`

#### 检查是否安装成功

**安装完需要确认重启，重启后需要输入以下命令来确认是否安装成功**

`sysctl net.ipv4.tcp_available_congestion_control`

执行后输出值需为：`net.ipv4.tcp_available_congestion_control = reno cubic bbr`。

`sysctl net.ipv4.tcp_congestion_control`

执行后输出值需为：`net.ipv4.tcp_congestion_control = bbr`。

`sysctl net.core.default_qdisc`

执行后输出值需为：`net.core.default_qdisc = fq`。

**以上三条命令的输出值正确后则说明原版 Google BBR 已经成功安装并开机自启动。**

### 魔改版 Google BBR

魔改版 Google BBR 是在原版 Google BBR 的基础上修改了一些参数，所以两者的性能会有所不同，具体效果则需要大家可以分别进行测试

#### 安装

`wget --no-check-certificate https://raw.githubusercontent.com/nanqinlang-tcp/tcp_nanqinlang/master/General/CentOS/bash/tcp_nanqinlang-1.3.2.sh && bash tcp_nanqinlang-1.3.2.sh`

**安装提示使用功能，选择 1，升级内核**

**安装新内核完成后，按照下图提示，我们输入“reboot”，然后回车即可重启服务器以应用新内核。**

#### 开启服务

`bash tcp_nanqinlang-1.3.2.sh`

回车后系统会自动运行脚本。安装提示，输入“2”（即开启魔改版 Google BBR 算法），然后回车继续即可。

## FTP&vsftp 安装配置

### 安装

`yum -y install vsftpd` **-y 表示无需确定输入 直接安装**

### 设置开机启动

`systemctl enable vsftpd`

### 启动 vsftpd

`systemctl start vsftpd.service` **推荐**

或者

`service vsftpd start`

### 配置端口及防火墙

**开启 21 端口**

`firewall-cmd --zone=public --add-port=21/tcp --permanent`

**永久开放 ftp 服务**

`firewall-cmd --add-service=ftp --permanent`

**重载防火墙**

`firewall-cmd --reload`

### 修改配置文件

配置文件说明见底部

**备份配置文件**

```
cd /etc/vsftpd
cp vsftpd.conf  vsftpd.conf.back
vi vsftpd.conf
```

### 创建帐号

**创建用户 ftpuser 指定 `/home/vsftpd` 目录**

`useradd -g root -M -d /home/vsftpd -s /sbin/nologin ftpuser`

**修改密码**

`passwd ftpuser`

**把 /home/vsftpd 的所有权给 ftpuser.root**

`chown -R ftpuser.root /home/vsftpd`

### vsftpd 配置目录说明

| /etc/vsftpd             | 配置目录                                  |
| ----------------------- | ----------------------------------------- |
| /etc/vsftpd/vsftpd.conf | vsftpd 服务配置，大部分配置只要修改这个。 |
| /etc/vsftpd/ftpusers    | 配置禁止访问 FTP 服务器的用户列表         |
| /etc/vsftpd/user_list   | 配置用户访问控制                          |

### vsftpd.conf 配置参数介绍

| 配置                                         | 说明                                                                                                                                                                                                                                                                                                   |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| anonymous_enable=YES                         | 允许匿名用户登录                                                                                                                                                                                                                                                                                       |
| local_enable=YES                             | 允许本地用户登录                                                                                                                                                                                                                                                                                       |
| write_enable=YES                             | 开放本地用户写权限                                                                                                                                                                                                                                                                                     |
| pam_service_name=vsftpd                      | vsftpd 设置 PAM 认证服务的配置文件名称，该文件存放在/etc/pam.d/                                                                                                                                                                                                                                        |
| userlist_enable=YES                          | 此项配置/etc/vsftpd.user_list 中指定的用户也不能访问服务器，若添加 userlist_deny=No,则仅仅/etc/vsftpd.user_list 文件中的用户可以访问，其他用户都不可以访问服务器。如过 userlist_enable=NO,userlist_deny=YES,则指定使文件/etc/vsftpd.user_list 中指定的用户不可以访问服务器，其他本地用户可以访问服务器 |
| tcp_wrappers=YES                             | 在 VSFTPD 中使用 TCP_Wrappers 远程访问控制机制，默认值为 YES                                                                                                                                                                                                                                           |
| #anon_upload_enable=YES                      | 此项设置允许匿名用户上传文件                                                                                                                                                                                                                                                                           |
| #anon_mkdir_write_enable=YES                 | 开启匿名用户的写和创建目录的权限                                                                                                                                                                                                                                                                       |
| xferlog_enable=YES                           | 启用记录上传/下载活动日志功能。                                                                                                                                                                                                                                                                        |
| connect_from_port_20=YES                     | 启用 FTP 数据端口的连接请求                                                                                                                                                                                                                                                                            |
| #xferlog_file=/var/log/xferlog               | 可以自定义日志文件的保存路径和文件名，默认是/var/log/vsftpd.log                                                                                                                                                                                                                                        |
| xferlog_std_format=YES                       | 使用标准的 ftpd xferlog 日志格式                                                                                                                                                                                                                                                                       |
| #nopriv_user=ftpsecure                       | 指定一个安全用户账号，让 FTP 服务器用作完全隔离和没有特权的独立用户。这是 vsftpd 系统推荐选项                                                                                                                                                                                                          |
| #chroot_local_user=YES                       | chroot_local_user=YES，则指定该列表(chroot_local_user)的保存路径(默认是/etc/vsftpd.chroot_list)                                                                                                                                                                                                        |
| #chroot_list_enable=YES                      | 可以用一个列表限定哪些本地用户只能在自己目录下活动，如果 chroot_local_user=YES，那么这个列表里指定的用户是不受限制的                                                                                                                                                                                   |
| #chroot_list_file=/etc/vsftpd/chroot_list    | 如果 chroot_local_user=YES，则指定该列表(chroot_local_user)的保存路径(默认是/etc/vsftpd.chroot_list)。                                                                                                                                                                                                 |
| #ls_recurse_enable=YES                       |                                                                                                                                                                                                                                                                                                        |
| listen=NO                                    | 指明 VSFTPD 以独立运行方式启动                                                                                                                                                                                                                                                                         |
| listen_ipv6=YES                              |                                                                                                                                                                                                                                                                                                        |
| #banned_email_file=/etc/vsftpd/banned_emails | 指定包含拒绝的 e-mail 地址的文件                                                                                                                                                                                                                                                                       |
| local_umask=022                              | 设置本地用户生成文件的掩码为 022                                                                                                                                                                                                                                                                       |
| #ascii_download_enable=YES                   | 启用 ascii_download_enable 选项会让恶意远程用户们在 ASCⅡ 模式下用“SIZE/big/file”                                                                                                                                                                                                                       |
| #ascii_upload_enable=YES                     | ascii_download_enable=YES，默认情况下服务器会假装接受 ASCⅡ 模式请求但实际上是忽略这样的请求，启用上述的两个选项可以让服务器真正实现 ASCⅡ 模式的传输                                                                                                                                                    |
| #async_abor_enable=YES                       | 强烈建议不要启用该选项，否则将可能导致出错                                                                                                                                                                                                                                                             |
| #chown_username=whoever                      | 当启用 chown_uploads=YES 时，所指定的属主用户账号，此处的 whoever 自然要用合适的用户账号来代替                                                                                                                                                                                                         |
| #chown_uploads=YES                           | 启用此项，匿名上传文件的属主用户将改为别的用户账户                                                                                                                                                                                                                                                     |
| #deny_email_enable=YES                       | 创建一个文件保存某些匿名电子邮件的黑名单，以防止这些人使用 Dos 攻击                                                                                                                                                                                                                                    |
| #ftpd_banner=Welcome to blah FTP service.    | 自定义 FTP 用户登录的欢迎信息                                                                                                                                                                                                                                                                          |
| dirmessage_enable=YES                        | 允许为目录配置显示信息，显示每个目录下面的 message_file 文件的内容                                                                                                                                                                                                                                     |
| #idle_session_timeout=600                    | 可以设定默认的空闲超时时间，用户超过这段时间不动作将被服务器踢出                                                                                                                                                                                                                                       |
| #data_connection_timeout=120                 | 设定默认的数据连接超时时间                                                                                                                                                                                                                                                                             |

## 一键搭建 ss(以前的方法)

```
wget --no-check-certificate HYPERLINK "https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev.sh"

//或者

git clone https://github.com/YuechiFu/vpn.git


chmod +x shadowsocks-libev.sh

./shadowsocks-libev.sh 2>&1 | tee shadowsocks-libev.log




//查看是否安装成功

ps -ef | grep ss-server | grep -v ps | grep -v grep


//启动ss

service shadowsocks start


//停止ss

service shadowsocks stop


//重启ss

shadowsocks restart


//更改 shadowsock 的配置

vi /etc/shadowsocks-libev/config.json
```

## centos 一键搭建 L2TP 服务器

```
wget --no-check-certificate HYPERLINK HYPERLINK "https://raw.githubusercontent.com/teddysun/across/master/l2tp.sh"

//或者

git clone https://github.com/YuechiFu/vpn.git


chmod +x l2tp.sh

./l2tp.sh


//验证

ipsec setup restart

xl2tpd -D

ipsec verify

```
