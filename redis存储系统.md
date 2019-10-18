```
服务端的程序用session去识别客户端的状态，服务端session来保持用户的状态，客户端浏览器用Cookies保存服务器的session信息，服务端把session种植到客户端的Cookies，从而达到身份认证的目的。
```

*   运行redis数据库服务

*    进入cmd窗口 输入>d:

*   cd  redis\redis-server.exe redis.windows.conf

*    这时候另启一个 cmd 窗口，原来的不要关闭，不然就无法访问服务端了。 

*   ```
    redis-cli.exe -h 127.0.0.1 -p 6379
    这一步请看下面，分割线后面的文字。
```
    
*   设置键值对:

    ```
    set myKey abc
    ```

    取出键值对:

    ```
    get myKey
    ```

##### [REDIS启动出错CREATING SERVER TCP LISTENING SOCKET 127.0.0.1:6379: BIND: NO ERROR](https://www.cnblogs.com/lixihuan/p/6815730.html)

windows下安装[Redis](http://lib.csdn.net/base/redis)第一次启动报错：

[2368] 21 Apr 02:57:05.611 # Creating Server TCP listening socket 127.0.0.1:6379: bind: No error

解决方法：在命令行中运行

*   redis-cli.exe

*   127.0.0.1:6379>shutdown

*   not connected>exit

然后重新运行`redis-server.exe redis.windows.conf`，启动成功！

-----

*   当然我是用的是koa2框架，使用两个==中间件==来使用redis比原生的用法更方便开发。
*   koa-generator-session
*   koa-redis
*   可以一起下载 ==npm（cnpm) install koa-generic-session koa-redis==
*   koa-generator-session用来操作==session==
*   koa-redis和mongoose类似，用来连接数据库（redis)

```
一般来说session的信息量特别大，一般情况下不直接使用内存，而是使用redis数据库来处理session。
```

----

---



访问redis还是得用原生代码，

```
redis-cli 启动客户端程序，进入客户端界面127.0.0.1:6379>，连接到了redis服务上。
```

```
注：koa-redis这个插件有默认值会自动配Host：127.0.0.1和port：6379，比mongoose简化了koa连接操作。
```

```
想看redis里面的键(key)，在客户端界面使用`keys *`命令查看redis中的所有keys(键名)。
如果想看redis里面值(value)，在客户端命令行中使用`get （上面key *中出现的键名之一）`
两者配合使用
```

