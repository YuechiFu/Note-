# nodejs & MongoDB 安装配置

---

## nodejs

### 安装

**1.直接通过 yum 来安装**

`yum install nodejs -y install npm -y`

这是最直接的方式了。 但是这种方式 安装的 node 版本不会是很新的，也许会遇到一些语法 是不能识别的 比如
promise class async 等

**2.通过编译好的压缩包安装**

如果 有通过 yum 安装的老版本的 node 需要先卸载

`yum remove nodejs`

node 版本列表 http://nodejs.org/dist/latest/

如果要选择下载好的 要记得选后面有带主机型号的如**node-v11.14.0-linux-x64.tar.gz**

```
cd /usr

//下载所需的版本
wget http://nodejs.org/dist/latest/node-v11.14.0-linux-x64.tar.gz

//解压
tar xvzf node-v11.14.0-linux-x64.tar.gz

//改名
mv node-v11.14.0-linux-x64 ./node-v11.14.0

//进入命令环境目录
cd node-v11.14.0/bin

//局部运行命令
./node -v
//v11.14.0
```

这时候 我们要添加环境变量 让全局都能用

**添加环境变量**

**其余三种修改环境变量的方法 见底部**

`vi ~/.bashrc`

在最后一行添上：

`export PATH=/usr/node-v11.14.0/bin:$PATH`

保存后 `source ~/.bashrc` 就能生效

此时 npm 也是包括在内 不需要另外安装

---

## MongoDB

### 安装

**1.下载**

获取[MongoDB 的下载包](https://www.mongodb.com/download-center?jmp=nav#community "MongoDB的下载包")

```
cd /usr

//下载
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.0.5.tgz

//解压
tar -xvzf mongodb-linux-x86_64-4.0.5.tgz

//重命名
mv mongodb-linux-x86_64-4.0.5.tgz mongodb

```

**2.添加环境变量**

**其余三种修改环境变量的方法 见底部**

`vi ~/.bashrc`

在最后一行添上：

`export PATH=/usr/mongodb/bin:$PATH`

保存后 `source ~/.bashrc` 就能生效

输入`mongo`试试有没有效果

**3.创建数据库目录 和 日志目录**

```
cd mongodb

//创建data路径
mkdir data && cd data

//创建数据库文件夹和日志文件夹
mkdir db && mkdir logs

//创建日志文件
cd logs && touch mongodb.log
```

**4.配置 mongodb.conf**

```
cd data
vi mongodb.conf
```

**加入相关配置**

```
#端口号
port = 27017

#数据目录
dbpath = /usr/mongodb/data/db

#日志目录
logpath = /usr/mongodb/data/logs/mongodb.log

#设置后台运行
fork = true

#日志输入方式
logappend = true

#开启认证
#auth = false

#绑定IP 默认的为 127.0.0.1
bind_ip = 0.0.0.0
```

**5.启动配置**

`mongod --config /usr/mongodb/data/mongodb.conf`

### MongoDB 导入 & 导出 数据

**1. 导入数据**

`mongoimport --port 27030 -u sa -p Expressin@0618 -d mapdb -c bike_bak --type=json --file bike.csv`

**参数说明**

h,--host ：代表远程连接的数据库地址，默认连接本地 Mongo 数据库；

--port：代表远程连接的数据库的端口，默认连接的远程端口 27017；

-u,--username：代表连接远程数据库的账号，如果设置数据库的认证，需要指定用户账号；

-p,--password：代表连接数据库的账号对应的密码；

-d,--db：代表连接的数据库；

-c,--collection：代表连接数据库中的集合；

-f, --fields：代表导入集合中的字段；

--type：代表导入的文件类型，包括 csv 和 json,tsv 文件，默认 json 格式；

--file：导入的文件名称

--headerline：导入 csv 文件时，指明第一行是列名，不需要导入；

**2. 导出数据**

**示例:**

导出类型为 json，数据库：mapdb,集合：bike 字段：bikeId,lat,lng,current_time,source ，条件为 source 字段为 ofo 第一条数据

`mongoexport --port 27030 -u sa -p Expressin@0618 -d mapdb -c bike - f bikeId,lat,lng,current_time,source --type=json -o bike.csv --query='{"source":"ofo"}' --limit=1`

**参数说明**

-h,--host ：代表远程连接的数据库地址，默认连接本地 Mongo 数据库；

--port：代表远程连接的数据库的端口，默认连接的远程端口 27017；

-u,--username：代表连接远程数据库的账号，如果设置数据库的认证，需要指定用户账号；

-p,--password：代表连接数据库的账号对应的密码；

-d,--db：代表连接的数据库；

-c,--collection：代表连接数据库中的集合；

-f, --fields：代表集合中的字段，可以根据设置选择导出的字段；

--type：代表导出输出的文件类型，包括 csv 和 json 文件；

-o, --out：代表导出的文件名；

-q, --query：代表查询条件；

--skip：跳过指定数量的数据；

--limit：读取指定数量的数据记录；

--sort：对数据进行排序，可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而-1 是用于降序排列,如 sort({KEY:1})。

---

## 添加环境变量(四种方式)

参考 https://blog.csdn.net/dongheli/article/details/83987092

查看 PATH：`echo $PATH`
以添加 mongodb server 为列

#### 1: export PATH

`export PATH=/usr/local/mongodb/bin:\$PATH`

配置完后可以通过`echo $PATH`查看配置结果。

生效方法：立即生效

有效期限：临时改变，只能在当前的终端窗口中有效，当前窗口关闭后就会恢复原有的 path 配置

用户局限：仅对当前用户

#### 2:通过修改.bashrc 文件(推荐~！！)

`vi ~/.bashrc`

在最后一行添上：

`export PATH=/usr/local/mongodb/bin:$PATH`

生效方法：（有以下两种）

1、关闭当前终端窗口，重新打开一个新终端窗口就能生效

2、输入`source ~/.bashrc`命令，立即生效

有效期限：永久有效

用户局限：仅对当前用户

#### 3:通过修改 profile 文件:

`vi /etc/profile`

/export PATH //找到设置 PATH 的行，添加

`export PATH=/usr/local/mongodb/bin:$PATH`

生效方法：系统重启

有效期限：永久有效

用户局限：对所有用户

#### 4:通过修改 environment 文件:

`vim /etc/environment`

在

`PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"`

中加入

`:/usr/local/mongodb/bin`

生效方法：系统重启

有效期限：永久有效

用户局限：对所有用户
