##### 远程仓库与本地合并产生问题

 https://komodor.com/learn/how-to-fix-fatal-refusing-to-merge-unrelated-histories-error/

```bash
$ fatal: refusing to merge unrelated histories
$ solution 1: git pull origin master --allow-unrelated-histories
```

##### git pull  --rebase 发生冲突

https://www.cnblogs.com/kevingrace/p/5896706.html

```bash
$ solution 1: 
	- 解决冲突
	- git add
	- git rebase --continue
$ solution 2:
	- git rebase --abort # 终止rebase，回滚到rebase前的状态
```

