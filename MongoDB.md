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
















