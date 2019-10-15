*   在远程github上创建版本库
*   然后本地`git init`创建本地版本库
*   ==重要的是==两者相关联建立连接，例如：` git remote add origin https://github.com/zz-want2sleep/review1.git`
*    it push与git pull是一对推送/拉取分支的git命令。 
    **git push 使用本地的对应分支来更新对应的远程分支。** 

` $ git push <远程主机名> <本地分支名>:<远程分支名> `

**git pull 获取并合并其他的厂库，或者本地的其他分支。**

git pull 与 git push操作的目的相同，但是操作的目标相反。命令格式如下：

```
git pull <远程主机> <远程分支>:<本地分支>
```

`