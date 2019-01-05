# MongoDB 那些事


## centos 下 MongoDB的搭建

#### 1.获取[MongoDB的下载包](https://www.mongodb.com/download-center?jmp=nav#community "MongoDB的下载包")
	cd /usr
	wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.0.5.tgz 	//下载
	tar -xvzf mongodb-linux-x86_64-4.0.5.tgz 								//解压
	mv mongodb-linux-x86_64-4.0.5.tgz mongodb 							   //重命名
#### 2.添加环境变量
	export PATH=/usr/mongodb/bin:$PATH
#### 3.创建数据库目录 和 日志目录
	cd mongodb
	mkdir data && cd data 	 //创建data路径
	mkdir db && mkdir logs   //创建数据库文件夹和日志文件夹
	cd logs && touch mongodb.log //创建日志文件
#### 4.配置mongodb.conf
	cd data
	vi mongodb.conf

###### 加入相关配置
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
	
#### 5.启动配置
	[root@CentOS bin]# ./mongod --config /usr/mongodb/data/mongodb.conf




## MongoDB 导入 & 导出 数据
#### 1. 导入数据
    mongoimport --port 27030 -u sa -p Expressin@0618 -d mapdb -c bike_bak --type=json --file bike.csv
    
###### 参数说明    
    h,--host ：代表远程连接的数据库地址，默认连接本地Mongo数据库；
    --port：代表远程连接的数据库的端口，默认连接的远程端口27017；
    -u,--username：代表连接远程数据库的账号，如果设置数据库的认证，需要指定用户账号；
    -p,--password：代表连接数据库的账号对应的密码；
    -d,--db：代表连接的数据库；
    -c,--collection：代表连接数据库中的集合；
    -f, --fields：代表导入集合中的字段；
    --type：代表导入的文件类型，包括csv和json,tsv文件，默认json格式；
    --file：导入的文件名称
    --headerline：导入csv文件时，指明第一行是列名，不需要导入；
#### 2.  导出数据
##### 示例 :
###### 导出类型为json，数据库：mapdb,集合：bike 字段：bikeId,lat,lng,current_time,source ，条件为source字段为ofo第一条数据

    mongoexport --port 27030 -u sa -p Expressin@0618 -d mapdb -c bike - f bikeId,lat,lng,current_time,source --type=json -o bike.csv --query='{"source":"ofo"}' --limit=1
###### 参数说明
    -h,--host ：代表远程连接的数据库地址，默认连接本地Mongo数据库；
    --port：代表远程连接的数据库的端口，默认连接的远程端口27017；
    -u,--username：代表连接远程数据库的账号，如果设置数据库的认证，需要指定用户账号；
    -p,--password：代表连接数据库的账号对应的密码；
    -d,--db：代表连接的数据库；
    -c,--collection：代表连接数据库中的集合；
    -f, --fields：代表集合中的字段，可以根据设置选择导出的字段；
    --type：代表导出输出的文件类型，包括csv和json文件；
    -o, --out：代表导出的文件名；
    -q, --query：代表查询条件；
     --skip：跳过指定数量的数据；
    --limit：读取指定数量的数据记录；
    --sort：对数据进行排序，可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而-1是用于降序排列,如sort({KEY:1})。


















