#### git回退到指定版本

##### 回退到上个版本

```shell
git reset --hard HEAD^
```

##### 回退到3次提交之前

```shell
git reset --hard HEAD~3
```

> 注意：以此类推，回退到n次提交之前

##### 回退到指定的commit id

```shell
git reset --hard commitId[这里是具体的commit id]
```

#### git回退之后需要强制提交代码，否则会被拒绝提交

```shell
git push origin master -f
```

> 注意：如果有其他的同时 也需要回退到该版本中

#### git新增忽略文件

* 在.gitignore文件中增加相应的文件

  > 注意：.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。
  >
  > 解决方法就是先把本地缓存删除（改变成未track状态），然后再提交:

* 执行以下代码来更新

  ```shell
  git rm -r --cached .
  git add .
  git commit -m 'update .gitignore'
  ```

#### git 新增分支

```shell
git branch ki
```

#### git向空仓库中push代码

> 直接新建空仓库
>
> 本地代码
>
> ```shell
> git init
> git add .
> git commit -m ''
> git remote add ki '仓库地址'
> git push ki master
> ```

<h4>linux添加SSH</h4>

```shell
ssh-keygen -t rsa -C "邮箱"    //这里是生成公钥私钥 然后一直回车 大概三个
cd ~/.ssh  //进入生成的公钥私钥文件夹
ssh-add id_rsa  //为本机配置私钥 [这里执行这条命令会报错,需要先执行 ssh-agent bash]
复制id_rsa.pub 里面的公钥[全部]到GitHub用户配置的 setting=>ssh,然后添加公钥
验证是否配置成功
ssh -T git@github.com
```

