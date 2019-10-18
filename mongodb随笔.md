####　检验安装成功！

使用

```
git bash 这个命令行界面，使用which mongod命令,检测是否安装成功，然后使用mongod启动mongodb服务
```

#### 解决无法启动mongodb数据库服务的办法

*   先在D:/MongoDB/data/db目录下面删除==mongod.lock==和==storage.bson==
*   然后使用cmd或者powershell运行输入`mongod  --dbpath D:\MongoDB\data\db `命令启动mongodb服务。

```
mongod.exe  服务器  运行该执行文件可以设置mongodb的端口号，数据文件存储目录等 

mongo.exe  客户端 用来连接mongodb并进行相关数据的查询
```

```
curl是一个linux命令，curl -d 'name=zz&age=22' http://localhost:3000/addPerson （-d代表post命令，‘’里面是数据项，最后是post请求的地址）最好用git Bash命令行界面。
```

----

```
给数据库做一个表，在models文件夹下创建了一个表（例如person.js），在这个表里面创建所有的字段，用const person=mongoose.schema({name:String, age:Number})创建描述（字段类型定义），描述好了就相当于有了字段之后，这只是一种声明，需要导出mongoose.models('person',person)这个模型，这里面封装了增删改查，想要真正操作数据库，要实例化model（直接用model模型Person不用new实例化也行）就可以操作数据库了。
```

