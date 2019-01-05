
# centos FTP&vsftp 安装配置
##### 安装
    yum -y install vsftpd       //-y 表示无需确定输入 直接安装
##### 设置开机启动
    systemctl enable vsftpd
##### 启动FTP
    systemctl start vsftpd.service  //推荐
    //或者
    service vsftpd start
##### 端口及防火墙相关配置
   
    firewall-cmd --zone=public --add-port=21/tcp --permanent //开启21端口
    firewall-cmd --add-service=ftp --permanent  //永久开放 ftp服务
    firewall-cmd --reload //重载防火墙
###### 其他
    firewall-cmd --permanent --list-port        查看端口列表
    systemctl start firewalld                   启动防火墙服务
    firewall-cmd --add-service=ftp              暂时开放ftp服务
    firewall-cmd --state                        检测防火墙状态
    firewall-cmd --list-all                     显示防火墙应用列表
    firewall-cmd --query-service ftp            查看服务的启动状态

##### 配置文件介绍
  
| /etc/vsftpd  |  配置目录  |
| - | - |
| /etc/vsftpd/vsftpd.conf   |  vsftpd 服务配置，大部分配置只要修改这个。  |
| /etc/vsftpd/ftpusers   | 配置禁止访问 FTP 服务器的用户列表  |
| /etc/vsftpd/user_list | 配置用户访问控制 |


##### vsftpd.conf 配置参数介绍
| 配置 | 说明 | 推荐参数 | 
| -- | -- |-- |
| anonymous_enable=YES | 允许匿名用户登录|
| local_enable=YES| 允许本地用户登录 | 
| write_enable=YES|开放本地用户写权限 |  
| #anon_upload_enable=YES|此项设置允许匿名用户上传文件 | 
| #anon_mkdir_write_enable=YES|开启匿名用户的写和创建目录的权限 | 
| xferlog_enable=YES| | 
| connect_from_port_20=YES| | 
| #xferlog_file=/var/log/xferlog| | 
| xferlog_std_format=YES|使用标准的ftpd xferlog日志格式 | 
| #nopriv_user=ftpsecure| | 
| #banned_email_file=/etc/vsftpd/banned_emails| | 
| #chroot_local_user=YES| | 
| #chroot_list_enable=YES| | 
| #chroot_list_file=/etc/vsftpd/chroot_list| | 
| #ls_recurse_enable=YES| | 
| listen=NO| | 
| listen_ipv6=YES| | 
| local_umask=022|设置本地用户生成文件的掩码为022 |
| #ascii_download_enable=YES|启用ascii_download_enable选项会让恶意远程用户们在ASCⅡ模式下用“SIZE/big/file” | 
| #ascii_upload_enable=YES|ascii_download_enable=YES，默认情况下服务器会假装接受ASCⅡ模式请求但实际上是忽略这样的请求，启用上述的两个选项可以让服务器真正实现ASCⅡ模式的传输 | 
| #async_abor_enable=YES| 强烈建议不要启用该选项，否则将可能导致出错| 
| #chown_username=whoever| 当启用chown_uploads=YES时，所指定的属主用户账号，此处的whoever自然要用合适的用户账号来代替| 
| #chown_uploads=YES| 启用此项，匿名上传文件的属主用户将改为别的用户账户| 
| #deny_email_enable=YES| 创建一个文件保存某些匿名电子邮件的黑名单，以防止这些人使用Dos攻击 | 
| #ftpd_banner=Welcome to blah FTP service.| 自定义FTP用户登录的欢迎信息| 
| dirmessage_enable=YES| 允许为目录配置显示信息，显示每个目录下面的message_file文件的内容| 
| #idle_session_timeout=600| 可以设定默认的空闲超时时间，用户超过这段时间不动作将被服务器踢出| 
| #data_connection_timeout=120| 设定默认的数据连接超时时间 | 
pam_service_name=vsftpd| | 
userlist_enable=YES| | 
tcp_wrappers=YES| | 



   
  
   

     
