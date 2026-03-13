## 一、实验目标

!!! warning deadline

    2026年3月20日23:59

通过本实验掌握以下基础技能：

- 注册并使用代码托管平台
- 掌握 Git 的基本操作流程
- 使用 CMake 构建简单项目
- 学会将代码提交到远程仓库

## 二、实验内容

### 1. 注册 Gitee 账号

访问：

- <https://gitee.com>

完成以下操作：

- 注册账号
- 登录账号
- 创建一个新的仓库（Repository）

仓库要求：

- 仓库名称：`myproject`
- 公开仓库
- 初始化 `README`

提交截图。

截图内容需包含：

- Gitee 主页
- 你的仓库页面
- 仓库名称

### 2. 在本地创建项目

创建如下目录结构：

```text
myproject
|
|-- CMakeLists.txt
|-- main.c
|-- utils.c
└-- utils.h
```

示例代码：

`main.c`

```c
#include <stdio.h>
#include "utils.h"

int main() {
    printf("Hello, ");
    print_world();
    return 0;
}
```

`utils.c`

```c
#include <stdio.h>
#include "utils.h"

void print_world() {
    printf("World!\\n");
}
```

`utils.h`

```c
#ifndef UTILS_H
#define UTILS_H

void print_world();

#endif
```

`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.10)

project(MyApp)

set(SOURCES main.c utils.c)

add_executable(myapp ${SOURCES})
```

### 3. Git 本地仓库操作

在项目目录中执行以下 Git 命令：

初始化仓库：

```bash
git init
```

添加文件：

```bash
git add .
```

提交代码：

```bash
git commit -m "first commit"
```

关联远程仓库：

```bash
git remote add origin 仓库地址
```

例如：

```bash
git remote add origin https://gitee.com/用户名/myproject.git
```

推送代码：

```bash
git push -u origin master
```

或：

```bash
git push -u origin main
```

### 4. CMake 编译项目

创建 `build` 目录：

```bash
mkdir build
cd build
```

执行：

```bash
cmake ..
```

然后：

```bash
make
```

或：

```bash
cmake --build .
```

运行程序：

```bash
./myapp
```

输出应为：

```text
Hello, World!
```

## 三、提交要求（只提交截图）

提交以下截图：

1. Gitee 仓库截图

    包含：

    - 仓库名称
    - 文件列表

2. Git 命令执行截图

    需包含以下命令执行记录：

    - `git init`
    - `git add`
    - `git commit`
    - `git push`

3. CMake 编译截图

    包含：

    - `cmake ..`
    - `make`
    - `./myapp`

## 四、说明

- 本实验以基础流程实践为主，提交材料仅需截图。
- 如遇 Git 身份未配置问题，请先执行：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

- 若远程默认分支为 `main`，请使用 `git push -u origin main`。

