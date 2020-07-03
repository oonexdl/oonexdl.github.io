分布式系统


不存在中心节点、各节点可以相互"通信"、数据隔离


版本控制


存储文件、追踪改动、支持回滚



Git = 分布式系统 + 版本控制


大部分操作都是离线的，无需网络亦可工作


每个客户端节点都保存有整个文件树，冗余意味着无惧单点失败


节点间通信依赖一个共享的"中心"服务器


<img src="/img/git-distributed-system.png" style="background-color: white"/>



[快速开始](https://github.github.com/training-kit/downloads/github-git-cheat-sheet/)



### 分支策略


<img src="/img/main-branches.png" width="300px" height="500px"/>
<img src="/img/feature-branchs.png" width="200px" height="500px"/>
<img src="/img/hotfix-branches.png" width="300px" height="500px"/>
<p>branchs</p>


```sh
$ git checkout -b myfeature develop
Switched to a new branch "myfeature"

$ git checkout develop
Switched to branch 'develop'

$ git merge --no-ff myfeature
Updating ea1b82a..05e9557

$ git branch -d myfeature
Deleted branch myfeature (was 05e9557).

$ git push origin develop
```


<img src="/img/merge-without-ff.png" width="500px" height="500px"/><p>not Fast-forwarded when merging</p>



### git merge vs rebase 


<img src="/img/branch-merge-origin.svg" style="background-color: white; margin-bottom: 0px"/><p>origin</p>
<img src="/img/branch-merged.svg" style="background-color: white; margin-top: 0px"/><p>after merged</p>


<img src="/img/branch-rebase.svg" style="background-color: white; margin-bottom: 0px"/><p>after rebase</p>
```sh
$ git branch
master
*login
$ git rebase master
First, rewinding head to replay your work on top of it...
Fast-forwarded login to master.
```


### so?


- using **rebase** when u'r working on a feature branch and you need changes from the main master branch
- using **merge** when u want to merge a feature branch back into your master branch



### tips

```sh
$ git config --global alias.s status
$ git config --global alias.d diff
$ git config --global alias.c checkout
...
```



### Q&A