---
title: 实验要求与检查
weight: 100
---

> [!CAUTION] 暂未最终确定，最好先不要按这个做，等正式发布后再仔细阅读。


## 1. 实验任务清单

本次实验要求你完成以下四个具体的步骤：

1. **账号准备**：前往 [Gitee](https://gitee.com/) 注册账号。由于国内访问 GitHub 可能不太稳定，Gitee 是一个更适合国内用户的选择。
2. **代码构建 (CMake)**：编写一个包含 2-3 个源文件（例如 `main.c`, `math_utils.c`, `math_utils.h`）的小项目，并编写 `CMakeLists.txt` 实现成功编译。
3. **版本控制 (Git)**：
* 在本地初始化仓库 (`git init`)。
* 完成多次代码修改并进行暂存与提交 (`git add`, `git commit`)。
* 在 Gitee 上创建同名仓库，并将本地代码推送到远端 (`git remote add`, `git push`)。
4. **作业提交**：按照要求整理截图，上传至 **BB 系统**。

---

## 2. 标准目录结构

为了方便管理和助教评阅，请务必统一使用以下目录结构（以项目名 `Lab2_Project` 为例）：

```text
Lab2_Project/
├── .git/                # Git 仓库元数据（执行 git init 后自动生成）
├── .gitignore          # 忽略文件，务必包含 build/ 和可执行文件
├── CMakeLists.txt      # CMake 配置文件
├── include/            # 存放所有 .h 头文件
│   └── utils.h
├── src/                # 存放所有 .c 源文件
│   ├── main.c
│   └── utils.c
├── build/              # 构建目录（所有编译产物都在这里，不进入 Git）
└── README.md           # [可选] 简要说明你的项目

```

## 3. 截图作业说明（上交 BB）

请提交一份 PDF 格式的文档，内容需包含以下关键截图：

1. **CMake 编译截图**：在终端执行 `make` 成功生成可执行文件的过程。
2. **Git 历史截图**：执行 `git log --oneline --graph --all` 后的终端输出，展示你的提交历史。
3. **Gitee 仓库截图**：浏览器打开你的 Gitee 项目主页，清晰显示项目名称及提交次数。
4. **目录结构截图**：执行 `tree` 命令（或在文件管理器中）展示你的项目目录结构。

---

**祝大家实验顺利！如有问题请在课程群及时反馈。**

