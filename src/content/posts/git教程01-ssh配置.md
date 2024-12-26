---
title: git教程01-ssh配置
date: 2024-12-26
summary: git的ssh配置
category: git教程
tags: [git, 工具]
comments: true
---

## SSH配置及克隆仓库

### 一、创建一个自己的仓库

![image-20241226152738777](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261527855.png)

### 二、创建密钥

#### 1. 打开`C:/Users/用户名/.ssh`文件夹

![image-20241226154259629](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261542677.png)

#### 2. 打开cmd终端并创建ssh密钥

```c
ssh-keygen -t rsa -b 4096
```

![image-20241226160537032](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261605101.png)

此时回到.ssh文件夹下，可以看到多了两个文件，这就是我们生成的密钥

![image-20241226161006261](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261610300.png)

#### 3. 复制公钥文件内容到github

记事本打开.pub文件复制所有内容

![image-20241226161859311](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261618346.png)

打开github右上角setting页面，新建ssh密钥

![image-20241226162220057](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261622165.png)

![image-20241226162358208](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261623272.png)

创建成功

![image-20241226162443045](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261624101.png)

#### 4. 创建config文件

添加congfig文件，访问github.com时，指定使用ssh下的哪个密钥

为了管理多个 github 账户需要这么做，你需要一个 config 文件。可以右键新建txt文件而后更改名称。在里面对刚刚新建的秘钥进行配置（注意保存）。

```
Host github.com

HostName github.com

PreferredAuthentications publickey

User allintky

IdentityFile ~/.ssh/first
```

创建完成后可以在终端中使用以下命令测试是否完成

```c
ssh -T git@first.github.com
```

![image-20241226164324760](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261643796.png)

### 使用ssh命令克隆git仓库

![image-20241226165630910](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202412261656949.png)

至此，git的ssh配置成功
