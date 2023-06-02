---
title: 操作系统lab问题总结
date: 2022.9.17
author: Xav1er
categories:	
  - Course
tags:
  - 操作系统
  - 课内
---

# Lab1 问题

## 环境问题

最好在Ubuntu运行，安装虚拟机或WSL（教程很多）



## 执行问题

### 下载

从 http://web.cecs.pdx.edu/~harry/Blitz/BlitzBin/Ubuntu64/blitztools.tar 下载可执行二进制文件

```bash
wget http://web.cecs.pdx.edu/\~harry/Blitz/BlitzBin/Ubuntu64/blitztools.tar
```



之后使用以下命令解压

```bash
tar -xf blitztools.tar
```



### 执行

64位Ubuntu无法直接运行，需要先安装32位依赖库

通过

```bash
sudo apt install lib32stdc++6
```

安装依赖，之后应当能正常执行

在配置好环境变量后（项目指导的pdf有相关内容），输入

```bash
asm 
```

应该输出

```bash
Must use -o option when input is from stdin
```



## 编译(STEP 9 kpl)

可能会遇到问题 `Illegal character 0x0d` 

主要是 `.c` 和 `.h` 行尾换行符导致的语法解析错误

课程仓库中代码行尾换行符为 `CRLF` 而 解析器认为合法的换行符为 `LF` ，多出了 `\r` (`0x0d`) 需要改变换行符

1. **方法一**

   使用vscode在右下角将 `CRLF` 替换为 `LF`

2. **方法二**

   Linux下使用工具 `dos2unix` 

   ```bash
   sudo apt install dos2unix
   ```

   安装工具后，按照 `dos2unix <filename>` 的方式，如 `dos2unix HelloWorld.c`运行工具，改变换行符

将所有以 `.c` 和 `.h` 为后缀的文件修改后，就可以正常运行了。



## 作业提交

按照word中操作，

1. 首先从课程仓库fork

2. 进入fork出的个人仓库（找不到的话，链接格式为 https://gitee.com/username/osai22/ 或 https://edu.gitee.com/hliang-bupt/repos/username/osai22/sources）将其中的username替换为个人用户名

3. 切换分支到develop

4. 熟悉git的可以直接`clone`仓库，再复制报告到文件夹后 `pull` 并  `push`

   不熟悉的同学可以 **在 `lab1-Upload address` 文件夹内** 点击上传文件，上传自己的作业文件

5. 上传完成后 **在自己的仓库内** 点击 **请求评审** 或 **pull request** (分别对应高校版和普通版) 

   从**自己仓库的 develop 分支** 合并到 **课程仓库的develop分支** 