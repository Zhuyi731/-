发布时间 @2018.5.23   
博客链接
>[https://segmentfault.com/a/1190000016122700](https://segmentfault.com/a/1190000016122700)  
>
>[https://blog.csdn.net/qq_33207773/article/details/80419897](https://blog.csdn.net/qq_33207773/article/details/80419897)
>  


# 解决webpack不能匹配post请求的问题

webpack的dev-server只能匹配get请求，在本地做本地数据的时候会很不方便。   
可以使用如下两种办法解决：  

1.在webpack.config.js配置文件中的devServer字段加入 
		
	devServe:{
		setup: (app) => {    //解决post没响应的问题
			     app.post('/goform/**', function(req, res) {
			  	res.redirect(req.originalUrl); //重定向到对应路径
            });
       }
    }
    
 @webpack3.0以后的版本setup需要改成before

2.在node_modules里找到webpack-dev-server/lib/server.js中，在Server这个函数中，大约100行左右的地方加入如下代码。来拦截post请求。当然，路径要自己写,也可以写成上面那样。

	app.post('/goform/*', (req, res) => {
    res.setHeader('Content-Type', 'text/plain;charset=UTF-8');
    let filename = path.join(__dirname,'..','..','..',`public/${req.originalUrl}.txt`);

    fs.exists(filename, exists => {
      if(exists) {
        fs.createReadStream(path.join(__dirname,'..','..','..',`public/${req.originalUrl}.txt`)).pipe(res);
      }else {
        res.end(`${req.originalUrl}' <- <- 老铁，这个接口你还没写。`);
      }
    });