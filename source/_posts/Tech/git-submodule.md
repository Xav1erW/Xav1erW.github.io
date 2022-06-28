---
title: git-submodule
categories:
  - Tech
  - git
tags:
  - git
  - git-.submodule
date: 2022-06-28 15:53:10
---


# 什么是Submodule

项目中有时会使用到其他仓库的代码，如果直接clone进项目的子目录，git会在提交更改的时候提示当前git仓库嵌套有一个git仓库，而这是不允许的。

```
warning: adding embedded git repository: foo2
hint: You've added another git repository inside your current repository.
hint: Clones of the outer repository will not contain the contents of
hint: the embedded repository and will not know how to obtain it.
hint: If you meant to add a submodule, use:
hint:
hint:   git submodule add <url> foo2
hint:
hint: If you added this path by mistake, you can remove it from the
hint: index with:
hint:
hint:   git rm --cached foo2
hint:
hint: See "git help submodule" for more information.
```

还有另一种方法，直接把文件复制进项目目录，而不包含 `.git` 文件夹。这虽然可行，但是无法跟踪依赖仓库的更新。

这时就需要git submodule了。

submodule能够让另一个仓库作为本仓库的子模块，在clone是可以自动拉取最新的仓库内容，同时修改时也可以很方便的同步修改submodule对应的仓库内容。

# 如何使用

使用命令

```bash
git submodule add <url> path/of/submodule
```

命令会自动clone仓库，并将另一个git仓库转化为该仓库的子模块

```bash
git submodule add git@github.com:Xav1erW/Foo.git foo2
Cloning into '/path/to/foo/foo/foo2'...
remote: Enumerating objects: 115, done.
remote: Counting objects: 100% (115/115), done.
remote: Compressing objects: 100% (68/68), done.
remote: Total 115 (delta 39), reused 105 (delta 35), pack-reused 0
Receiving objects: 100% (115/115), 3.68 MiB | 313.00 KiB/s, done.
Resolving deltas: 100% (39/39), done.
warning: LF will be replaced by CRLF in .gitmodules.
The file will have its original line endings in your working directory
```

此时项目根目录会出现文件 `.gitmodules` ，内容为子模块的信息

```
[submodule "foo2"]
	path = foo2
	url = git@github.com:Xav1erW/Foo.git
```

如果需要保存，子模块的目录和 `.gitmodules` 都需要提交保存。

# 对submodule修改

直接生成的子模块只对应一个 `commit id` 而不属于任何一个submodule的分支，因此如果需要修改

1. 先 `git checkout` 到需要修改的分支上；
2. 修改后，在submodule内提交其修改；
3. 之后还需要回到父仓库下提交submodule的修改。

# 更新submodule

可以直接在父仓库内使用命令

```bash
git submodule foreach git pulll
```

或者在submodule内进行`git pull` 操作 。

# clone包含submodule的仓库

## 递归参数

直接克隆不会自动克隆仓库内的submodule，因此需要带上递归参数

```bash
git clone git@github.com:Xav1erW/Foo1.git --recursive
```

## submodule init

还可以先clone父仓库，之后在父仓库内使用

```bash
git submodule init
```

这会将submodule先进行注册

接着更新submodule

```bash
git submodule update
```

