---
title: 实验二：基础工具——make/cmake与git的使用
weight: 20
---

## 实验简介

在之前的学习中，我们通过手动调用 `gcc` 编译器完成了简单的单文件程序编译。然而，随着项目规模的增长，源码文件会增加到几十甚至上千个，手动编译不仅低效，且难以管理文件间的依赖关系。

本次实验旨在带大家脱离“人肉编译”时代，掌握 C/C++ 开发中的**工业级构建工具**。我们将学习如何使用 **Make** 自动化编译流程，利用 **CMake** 应对跨平台和复杂项目的配置，并使用 **Git** 记录代码的每一次“进化”，为后续的大型实验（如模拟器或内核开发）打下坚实的基础。

## 实验目的

1. **掌握 Makefile 的基本语法**：理解目标（Target）、依赖（Dependency）和命令（Command）的概念，学会使用自动化变量提高效率。
2. **理解 CMake 的构建逻辑**：学会编写 `CMakeLists.txt`，掌握外部构建（Out-of-source Build）的标准流程。
3. **熟练使用 Git 进行版本管理**：掌握工作区、暂存区、本地仓库的概念，熟悉 `add`, `commit`, `log`, `checkout` 等常用操作。
4. **建立良好的开发习惯**：学会编写 `.gitignore` 过滤中间文件，保持代码仓库的整洁。

## 实验时间安排

* **3.13 晚实验课**：3月13日（周五）晚 19:30~22:30。
* **地点**：电三楼 516/519。

---

## 实验内容

### 章节预览

#### 1. Make 简介与 Makefile 编写

* **核心痛点**：解决“修改一个文件就要重编整个项目”的低效问题。
* **重点概念**：隐式规则、伪目标（`.PHONY`）、变量定义、自动化变量（`$@`, `$<`, `$^`）。

#### 2. 现代构建系统：CMake

* **核心痛点**：Makefile 在大型项目中过于冗长，且难以跨平台。
* **重点概念**：`CMakeLists.txt`、`add_executable`、库的链接、构建目录的分离。

#### 3. 版本控制工具：Git

* **核心痛点**：代码改崩了找不回来？多人协作代码冲突？
* **重点概念**：Git 的三个区域（Working Tree, Index, Repository）。

### 学习路径

- [章节一：Make简介与Makefile编写](./part1)
- [章节二：现代构建系统：CMake](./part2)
- [章节三：版本控制工具：Git](./part3)
- [实验二：Git 与 CMake 基础实践操作指南](./experiment-guide)

## 实验要求与检查

!!! warning "草案说明"

   当前页面中“实验要求与检查”为草案，具体时间与提交口径以课程公告和教学平台为准。

### 1. 实验任务清单

本次实验要求完成以下四个步骤：

1. 账号准备：前往 [Gitee](https://gitee.com/) 注册账号。
2. 代码构建（CMake）：按统一模板创建 `myproject`，并使用 `CMakeLists.txt` 成功构建可执行程序 `myapp`。
3. 版本控制（Git）：
   - 在本地初始化仓库（`git init`）。
   - 完成多次代码修改并进行暂存与提交（`git add`、`git commit`）。
   - 在 Gitee 上创建同名仓库，并将本地代码推送到远端（`git remote add`、`git push`）。
4. 作业提交：按照要求整理截图并提交到课程平台。

### 2. 标准目录结构

为便于管理和评阅，本实验统一使用以下目录结构：

```text
myproject/
├── main.c
├── utils.c
├── utils.h
└── CMakeLists.txt
```

建议在项目根目录额外创建 `build/` 用于外部构建。

### 3. 统一 CMake 配置与构建流程

`CMakeLists.txt` 示例：

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp)

set(SOURCES main.c utils.c)

add_executable(myapp ${SOURCES})
```

构建命令（推荐外部构建）：

```bash
mkdir build
cd build
cmake ..
make
```

你也可以使用：

```bash
cmake --build .
```

来代替 `make`。

### 4. 截图作业说明（按课程平台要求提交）

请提交一份 PDF，内容需包含以下关键截图：

1. CMake 编译截图：终端执行 `cmake ..` 与 `make`（或 `cmake --build .`）的成功过程。
2. Git 历史截图：`git log --oneline --graph --all` 输出。
3. Gitee 仓库截图：仓库主页，清晰显示项目名称与提交记录。
4. 目录结构截图：清晰展示 `myproject` 下 `main.c`、`utils.c`、`utils.h` 与 `CMakeLists.txt`。

!!! Note "建议补充"

    若课程要求过程可追溯，建议补充一张包含 `git status`、`git add`、`git commit`、`git push` 的连续终端截图。

---

### 助教提示 (TIPS)

* **关于环境**：建议在 Linux 环境（Ubuntu/WSL2）下完成实验，避免 Windows 换行符（CRLF）带来的脚本报错。
* **关于 Git**：请务必配置好 `user.name` 和 `user.email`，这是作为系统程序员的基本素养。
* **禁止事项**：严禁将编译生成的二进制文件（如 `.o`, `a.out`）提交到 Git 仓库，请合理配置 `.gitignore`。
