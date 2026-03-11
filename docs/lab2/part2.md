---
title: 章节二：现代构建系统：CMake
weight: 22
---

在上一章节中，我们手动编写了 Makefile。虽然对于几个文件的项目来说还算轻松，但如果项目引入了复杂的文件夹结构、外部库，或者需要从 Linux 迁移到 Windows，手写 Makefile 就会变成一场噩梦。

这一节，我们将学习现代 C/C++ 开发的标准工具——**CMake**。

---

## 2.1 什么是 CMake？

**CMake (Cross-Platform Make)** 并不是直接替代 `make` 的编译器，而是一个**构建系统生成器 (Generator)**。

* **Make**：像是一个**具体的食谱**，规定了哪一分钟下肉，哪一分钟撒盐。
* **CMake**：更像是一个**厨政主管**。你只需要告诉它：“我要做一个宫保鸡丁”，它会根据你现在的厨房环境（是 Linux 的 `gcc` 厨房，还是 Windows 的 `Visual Studio` 厨房），自动为你写好那份详细的“食谱”（Makefile 或项目文件）。

---

## 2.2 基础 CMakeLists.txt

CMake 的所有指令都写在一个名为 `CMakeLists.txt`（注意大小写和拼写）的文件中。

### 2.2.1 四行核心指令

一个最基础的 `CMakeLists.txt` 如下：

```cmake
# 1. 声明要求的最低版本（保证语法兼容性）
cmake_minimum_required(VERSION 3.10)

# 2. 定义项目名称和版本
project(ISP_Lab2 VERSION 1.0)

# 3. 指定编译选项（这里开启了所有警告并使用 C11 标准）
set(CMAKE_C_STANDARD 11)
add_compile_options(-Wall -Wextra -g)

# 4. 告诉 CMake：我要生成一个叫 hello 的程序，源码是这些
add_executable(hello main.c tool.c)

```

!!! Note 

    **CMake 的优势**：你发现了吗？我们**没有**在这里手动写 `.h` 的依赖关系。CMake 会在生成构建系统时，自动扫描源码中的 `#include`，并帮你处理好依赖树。

---

## 2.3 标准构建流程：外部构建 (Out-of-source Build)

在运行 CMake 时，会产生大量的中间缓存文件（如 `CMakeCache.txt`, `CMakeFiles/`）。如果直接在源码目录下运行，你的代码文件夹会瞬间变得极其混乱。

因此，所有专业的开发者都会使用 **“外部构建”**：

### 构建命令：

```bash
mkdir build    # 1. 创建一个独立的构建目录
cd build       # 2. 钻进这个目录
cmake ..       # 3. “隔空取物”：让 CMake 读取上一级目录(..)的 CMakeLists.txt，并在当前目录生成 Makefile
make           # 4. 此时 Makefile 已在 build 中生成，执行 make 即可编译

```

**为什么执行 `cmake ..`？**
`..` 代表父目录。CMake 需要知道 `CMakeLists.txt` 在哪，同时也知道要把生成的“垃圾文件”留在当前的 `build/` 文件夹里。

---

## 2.4 进阶：如何处理头文件？

如果你的项目结构变得复杂，例如头文件放在 `include/` 目录下：

```text
.
├── CMakeLists.txt
├── include/
│   └── tool.h
└── src/
    ├── main.c
    └── tool.c

```

你需要告诉 CMake 去哪里找头文件，否则编译时会报 `fatal error: tool.h: No such file or directory`。

```cmake
# 添加头文件搜索路径
target_include_directories(hello PUBLIC include)

# 指定源码路径
add_executable(hello src/main.c src/tool.c)

```

---

## 2.5 为什么我们要用 CMake？（对比总结）

| 特性 | Make / Makefile | CMake |
| --- | --- | --- |
| **层级** | 底层，直接控制编译动作 | 高层，抽象描述项目需求 |
| **头文件依赖** | **必须手动维护**，漏写会导致更新不及时 | **自动扫描**，无需操心 |
| **跨平台** | 换个系统（如 Win）基本要重写 | 同样的文件，生成对应的工程文件 |
| **复杂度** | 文件多了之后，维护极其困难 | 支持嵌套，大型项目的工业标准 |

---

## 2.6 Q&A 与避坑指南

??? Question "**Q: `cmake ..` 报错 `CMakeLists.txt not found`？**"

    A: 检查你是否在 `build` 目录下，且 `..` 路径是否正确指向了包含 `CMakeLists.txt` 的那个文件夹。


??? Question "**Q: 我修改了代码，需要重新运行 `cmake ..` 吗？**"

    A: 通常不需要。直接在 `build` 目录下运行 `make` 即可。生成的 Makefile 非常聪明，它如果检测到 `CMakeLists.txt` 变了，会自己触发 CMake 更新。


??? Question "**Q: 为什么生成的 `build` 目录下有几百个文件？**"

    A: 别担心，这就是为什么我们要建 `build` 文件夹的原因。如果你想“彻底清理”编译产物，直接 `rm -rf build` 即可，完全不会伤及源码。

