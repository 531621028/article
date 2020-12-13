[toc]

# Git本地多账号的使用配置

## 1.前言

​		在工作中，我们有时希望将工作中和自己的学习使用不同的账户来区分开来，保持工作和学习独立，但有时候我们需要使用同一个电脑，所以在同一个电脑对使用多个账号来对应不同的仓库就显得非常有必要，所以本文主要说明如何在本地配置使用多个git账号。

​		我们都是知道访问git仓库主要有两种方式，一种是http，另一种是ssh。下面我们对这两种方式分别进行讲解

## 2.http的方式

1. 使用```git config --global --unset credential.helper```去除掉全局的密码管理配置
2. 在github上使用AccountOne账号上创建一个远程仓库名称为repoOne,对应的http的链接应该是 https://github.com/AccountOne/repoOne.git
3. 在本地clone下刚刚创建的仓库,使用```git remote set-url origin https://AccountOne@github.com/AccountOne/repoOne.git```设置远程仓库的地址，**注意在域名的前面添加了AccountOne@**目的是指定不同的用户
4. 在本地执行```git push -u origin main```的时候会要求输入密码，输入密码之后就可以吧代码push上去了
5. 但是这样每次可能都要输入密码，使用```git config --local credential.helper osxkeychain```(Mac)为每一个仓库创建一个秘钥管理，这样账号密码就只针对当前仓库保存，对其他仓库没有影响了；```windows可以使用git config --local credential.helper windows```
6. 针对第二个仓库重复步骤2-5，为第二个仓库创建
7. 关于git更多密码管理可以参考[Git-工具-凭证存储](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8)

## 3.SSH的方式

1.使用```ssh-keygen -t rsa -C "XXXXX@XXX.com" ```生成两个不同的秘钥，在输入名字的时候并指定不同的名字，其他的步骤直接回车即可。

2.在不同GitHub账号的设置-》SSH and GPGKey中新建一个sshkey，并将对应的公钥复制进去

3.配置~/.ssh/config文件

```shell
##可缺省，此时ssh -T git@github.com,默认就是和拥有id_rsa.pub的github账号对接。
#github server one
Host AccountOneAlias              #域名地址的别名
Hostname github.com      #这个是真实的域名地址
User git                     #配置使用用户名
IdentityFile ~/.ssh/id_rsa   #这里是id_rsa的地址
#github server two
Host AccountTwoAlias
Hostname github.com
User git
IdentityFile ~/.ssh/id_rsa_two
```

4.使用```git clone git@AccountOneAlias:AccountOne/repoOne.git```来下载不同的仓库的代码并在本地进行管理

