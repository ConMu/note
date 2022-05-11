部门开发参考文档：https://github.com/xirong/my-git/blob/master/git-workflow-tutorial.md

##### 常用命令集

```bash
$ git status # 查看本地仓库的修改状态
$ git add # 暂存文件
$ git commit # 提交文件
$ git push origin master # 提交远程master仓库
$ git pull --rebase origin master # 合并上游并rebase 
$ git rebase --abort # 回滚rebase执行 
$ git checkout -b marys-feature dev # 检出一个基于dev名为marys-feature的分支 
$ git push -u origin marys-feature # 这条命令push marys-feature分支到中央仓库（origin），-u选项设置本地分支去跟踪远程对应的分支。 
$ git branch -d release-0.1 # 删除release-0.1这个分支
```

##### 配置

查看所有配置

```bash
$ git config --list (--show-origin) 
```

用户信息

```bash
$ git config --global user.name "John Doe" 
$ git config --global user.email johndoe@example.com
```

帮助

```bash
$ git help <verb> $ git <verb> -h # -h 获取简明的help输出
```

获取git仓库

```bash
$ git init # 初始化 创建一个.git
```

将本地的仓库关联到git hub上

```bash
$ git remote add origin https://自己的仓库url地址
```

查看某个远程仓库的详细信息

```bash
$ git remote show [remote-name] # 例 ： git remote show origin
$ git remote -v # 仓库路径查询查询
```

删除分支

```bash
$ git push origin --delete <branchName> # 删除远程仓库分支
$ git branch -d <branchName> # 删除本地仓库分支
```

撤销commit

```bash
$ git reset --mixed HEAD^ # 不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
$ git reset --soft HEAD^ # [常用] HEAD^的意思是上一个版本 HEAD~2: 上两次commit撤销 不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
$ git reset --hard HEAD~1 # 删除工作空间改动代码，撤销commit，撤销git add . 恢复到了上一次的commit状态
$ git commit --amend # 修改commit注释
```

