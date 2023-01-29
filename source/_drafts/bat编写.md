---
title: bat编写
categories:
  - Tech
tags:
  - windows
  - bat
  - cmd
---

# 什么是bat

bat文件是dos下的批处理文件。批处理文件是无格式的文本文件，它包含一条或多条命令。它的文件扩展名为 `.bat` 或 `.cmd`。

# 常用命令

## echo

echo表示显示后续内容，类似于打印

```powershell
echo hello world
```

### 关闭回显

#### 全部关闭

bat脚本执行时，会将执行的指令回显到屏幕，

如果不希望脚本打印回显内容，可以加入命令

```powershell
echo off
```

会关闭该条指令之后的命令的回显

```powershell
echo "Hello World"
echo "Hello World"
echo "Hello World"
```

输出

```

F:\Tool\scripy>echo "Hello World"
"Hello World"

F:\Tool\scripy>echo "Hello World"
"Hello World"

F:\Tool\scripy>echo "Hello World"
"Hello World"
```

```powershell
echo off
echo "Hello World"
echo "Hello World"
echo "Hello World"
```

输出

```

F:\Tool\scripy>echo off
"Hello World"
"Hello World"
"Hello World"
```

#### 单独关闭

在每行命令前加 `@` 可以单独关闭该指令的回显，可以在最开始的 `echo off` 前加上 `@` 关闭所有回显

```powershell
@echo off
echo "Hello World"
echo "Hello World"
echo "Hello World"
```

输出

```
"Hello World"
"Hello World"
"Hello World"
```

## 注释

### REM

`REM` 命令可以表示注释该行，如

```cmd
rem echo Comments
```

### ::

连续的两个冒号 `::` 同样可以注释

```cmd
:: echo Comments
```

## 命令行参数

输入脚本可能有命令行参数，可以采用`%[1-9]`的方式获取 

```cmd
:: 1.bat
echo %1
echo %2
```

如果执行 `./1.bat a b` 那么会输出

```
a
b
```

## goto

跳转到指定位置(label) `goto label`

label格式 `:label`

常与if一起使用实现条件的跳转

```cmd
if %1 == 'a' {} goto alabel
if %1 == 'b' {} goto blabel

:alabel
echo a
goto end

:blabel
echo b
goto end

:end
echo end
```