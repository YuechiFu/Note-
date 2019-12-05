
## centos 下 puppetter 的安装办法

### node 环境搭建

(参考[nodejs & MongoDB 安装配置](https://github.com/YuechiFu/Note-/blob/master/linux/centos7%20nodejs%26MongoDB%E6%90%AD%E5%BB%BA.md))

### 升级 gcc 

``` yum install gcc```

### puppetter 安装

这里推荐镜像安装.直接 npm install 容易被墙

```
npm install -g cnpm --registry=https://registry.npm.taobao.org  //安装cnpm
cnpm i puppeteer
```



### 安装依赖


#### 依赖库 (必须,不然跑起来也会报错)

```
yum install pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 -y
```
上下顺序不一致(都可以用)

```
yum install pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 -y
```


字体库(非必须)

```
yum install ipa-gothic-fonts xorg-x11-fonts-100dpi xorg-x11-fonts-75dpi xorg-x11-utils xorg-x11-fonts-cyrillic xorg-x11-fonts-Type1 xorg-x11-fonts-misc -y
```

### 代码修改

如果这时候 你已经 安装完成了所有东西,但是 执行的时候出现这个错误

```
(node:30559) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: Failed to connect to chrome!

(node:30559) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.

```
说明 你还需要做一件事

```
const browser = await puppeteer.launch();
```
需要添加一些参数

```
puppeteer.launch({args: ['--no-sandbox', '--disable-setuid-sandbox']})
```

END...