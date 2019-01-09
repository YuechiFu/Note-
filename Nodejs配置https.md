# NODEJS 配置 HTTPS 
- ### 申请证书
   阿里云申请到自己域名的免费证书。 Nginx 版本即可 。 下载至服务器。 
- ### 部署至服务器   
      //导入证书
      let httpsOption = {
      	key:fs.readFileSync('./https/https.key'),
      	cert:fs.readFileSync('./https/https.pem'),
      }
      
      //创建服务器
      const httpServer = http.createServer(app);
      const httpsServer = https.createServer(httpsOption,app)
      
      //开启服务器
      http.createServer(app).listen(80)
      https.createServer(httpsOption,app).listen(443) 


- ### 常见问题&&解决办法
   ##### 启动服务器后，页面一直在建立安全链接 
   这种情况多半是你没有给页面链接做路由，要么就是 你的页面中 可能包含localhost的hostname ,浏览器会默认当成不安全的行为来看待。 所以要注意。 
   ##### 提示不安全
   这种情况 可能是你的证书的签发机构来自于 第三方 ，有些可能是自己做加密什么的。 另一种情况就是页面可能包含http开头的媒体资源或者请求。 导致浏览器识别出这些路径 含有安全隐患。 


 - ###### 想到再补充........ 

