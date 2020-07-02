分布式系统


去中心化、各自为政


- 各系统节点不用实时互联，除非通信需要
- 数据隔离，不会牵一发而动全身



版本控制


存储文件、记录改动、支持回滚



Git = 分布式系统 + 版本控制


大部分操作都是离线的，无需网络亦可工作


每个客户端节点都保存有整个文件树


至少有一个客户端节点保存着当前最新的完整文件树


冗余意味着无惧单点失败



[快速开始](https://github.github.com/training-kit/downloads/github-git-cheat-sheet/)



### 分支模型


<img src="/img/main-branches.png" width="300px" height="500px"/>
<img src="/img/feature-branchs.png" width="200px" height="500px"/>
<img src="/img/hotfix-branches.png" width="300px" height="500px"/>
<p>branch models</p>


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


<img src="/img/merge-without-ff.png" width="500px" height="500px"/><p>force not Fast-forwarded when merging</p>



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



### 资源链接

- https://git-scm.com/docs
- https://www.google.com