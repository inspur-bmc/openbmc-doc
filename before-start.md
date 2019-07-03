## 邮件列表

https://lists.ozlabs.org/listinfo/openbmc 

## 文档

https://github.com/openbmc/docs

## 首先要阅读的文档

https://github.com/openbmc/docs/blob/master/CONTRIBUTING.md

## 代码审阅平台

https://gerrit.openbmc-project.xyz/q/status:open

## gerrit注册

https://github.com/openbmc/docs/blob/master/development/gerrit-setup.md

## gerrit工具

https://docs.openstack.org/infra/git-review/usage.html

## git 常用命令

- git add 
- git commit -s    // 加签名
- git commit --amend  //修改patch时一定要加
- git push gerrit HEAD:refs/for/master
- git rebase -i HEAD~3   // edit  reword  drop ....
- git rebase --continue   

