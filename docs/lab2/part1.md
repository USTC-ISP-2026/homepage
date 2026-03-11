---
title: 章节一：Make简介与Makefile编写
weight: 21
---

!!! Note

    在上一章，我们还在手动输入 gcc main.c -o main。如果项目有 100 个源文件，手动编译不仅是“体力活”，更是“精细活”——改动一个文件，该重编哪些？漏掉一个怎么办？make 工具因此诞生。它不仅能一键完成所有工作，还能聪明地只处理“变动过”的部分。

## 1.1 为什么需要 Make？

在大型 C/C++ 项目中，通常会有许多源文件。
1. **自动化**：一键完成编译、链接甚至打包。
2. **增量编译**：`make` 会检查文件的修改时间。如果 `func.c` 没变，它就不会重新编译 `func.o`，大大节省了大型项目的构建时间。

## 1.2 Makefile 核心语法

`make` 本身并不知道如何编译 C 语言。你需要通过一个名为 `Makefile` 的文本文件告诉它规则。

### 1.2.1 规则 (Rules)
Makefile 的核心是**规则**，其格式如下：

```makefile
target: dependencies
	command
```

1. Target (目标)：你想生成的东西（如 `main` 程序、`tool.o` 文件）。也可以是一个动作名（如 `clean`）。
2. Dependencies (依赖)：生成目标需要哪些“原材料”（源文件或其他目标）。
3. Command (命令)：具体怎么做（通常是 `gcc` 命令）。

!!! Note 

    Makefile 诞生于 1976 年，当时的作者为了方便，规定命令前必须是 \t (Tab)。这个“设计缺陷”保留了 50 年。
    所以：命令前必须是一个 **Tab 字符**，不能是空格！

!!! Example "这里的核心逻辑是：递归检查"

    当你告诉 `make` 去生成 `target` 时，它会启动一个**递归过程**：

    1. **检查依赖是否存在？** 如果 `dependencies` 里的某个文件不存在，`make` 会在 Makefile 中寻找是否有其他规则可以生成这个“原材料”。
    2. **检查是否需要更新？** 如果 `dependencies` 中任何一个文件的“最后修改时间”比 `target` 更新，`make` 就会重新执行 `command`。


### 1.2.2 简单示例
假设项目结构如下：
* `main.c` (调用了 `tool.h` 中的函数)
* `tool.c` (函数的实现)
* `tool.h` (函数声明)

```makefile
# 1. 最终目标(规则 A)：将两个 .o 文件链接成可执行文件
main: main.o tool.o
    gcc -o main main.o tool.o

# 2. 中间目标(规则 B)：将 main.c 编译成 main.o
# 注意：如果 tool.h 变了，main.o 也必须重编，因为它 #include 了 tool.h
main.o: main.c tool.h
    gcc -c main.c

# 3. 中间目标(规则 C)：将 tool.c 编译成 tool.o
tool.o: tool.c tool.h
    gcc -c tool.c

# 4. 伪目标：清理现场
# 执行 'make clean' 即可删除所有中间产物，还你一个干净的目录
.PHONY: clean
clean:
    rm -f main *.o
```

!!! Example "编译过程？"

    **当你输入 `make` 时，幕后发生了什么？**
    
    1. `make` 看到第一个目标是 `main`，它发现 `main` 依赖 `main.o` 和 `tool.o`。
    2. 它去找 `main.o`，发现有**规则 B**。它检查 `main.c` 和 `tool.h` 是否比 `main.o` 新。如果是，执行 `gcc -c main.c`。
    3. 它再去处理 `tool.o`，发现有**规则 C**。同样检查时间戳，必要时执行 `gcc -c tool.c`。
    4. 当“原材料” `main.o` 和 `tool.o` 都准备好（或更新完）后，它最后执行**规则 A** 里的链接命令。



## 1.3 变量与自动变量

在上面的例子中，如果我们想把编译器从 `gcc` 换成 `clang`，或者增加编译选项 `-O2`（优化级别），我们需要修改每一行命令。这显然不符合程序员“偷懒”的美德。我们可以使用变量和模式匹配来简化。

### 1.3.1 使用变量 (Variables)

变量就像 C 语言里的宏，方便统一修改。

```makefile
CC = gcc             # 指定编译器
CFLAGS = -Wall -g    # 编译选项：显示所有警告，保留调试信息
TARGET = main        # 最终产物名
OBJS = main.o tool.o # 对象文件列表

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJS)

```

!!! Note "Tip"

    变量名通常约定俗成用大写。`CC` 代表 C Compiler，`CFLAGS` 代表 C Flags（编译参数）。

### 1.3.2 模式规则与自动变量 (Pattern Rules)

对于几十个 `.c` 文件，我们不可能每个都写一遍规则。Makefile 提供了一种通配符 `%`（类似于正则表达式中的 `*`）。

在模式规则中，由于文件名是动态确定的，我们无法写死文件名。因此，Makefile 提供了三个**自动变量**：

* **`$@`**：代表当前的**目标**（Target）。比如上面的 `main.o`。
* **`$<`**：代表**第一个依赖文件**。比如上面的 `main.c`。
* **`$^`**：代表**所有的依赖文件**（以空格分隔）。

```makefile
# 这里的 % 相当于通配符。这条规则的意思是：
# 任何一个 .o 文件都依赖于对应的 .c 文件
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

```

!!! Example "发生了什么？"


    假设 `make` 现在正在处理 `main.o` 这个目标：

    1. **模式匹配**：`%.o` 匹配到了 `main.o`，于是 `%` 就是 `main`。
    2. **依赖推导**：`%.c` 自动变成了 `main.c`。
    3. **变量代换**：
      * **`$@`** (目标) -> `main.o`
      * **`$<`** (第一个依赖) -> `main.c`
    4. **最终生成的命令**：`gcc -Wall -g -c main.c -o main.o`


### 1.3.3 演示

我们将 1.2.2 的冗长代码简化为“工业级”写法：

```makefile
CC = gcc
CFLAGS = -Wall -g
TARGET = main
OBJS = main.o tool.o

# 链接阶段：使用 $^ 包含所有 .o 文件
$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^

# 编译阶段：使用模式规则
# 对于每一个 .o 文件，都去找对应的 .c 文件
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

```

### 1.3.4 伪目标

有些目标（如 `clean`）并不对应实际的文件，而是一个动作的名字。为了避免和同名文件冲突，我们可以使用 `.PHONY` 声明：

```makefile
.PHONY: clean
clean:
    rm -f $(TARGET) $(OBJS)
```


## 1.4 Q&A

??? Question "**Q: 我输入 `make` 提示 `make: 'main' is up to date.` 是什么意思？**"

    A: 这说明你自上次编译以来没改过代码。如果你非要重编，可以先 `make clean` 再 `make`。


??? Question "**Q: 为什么提示 `missing separator. Stop.`？**"
    
    A: 检查一下！你的命令前面是不是用了空格而不是 **Tab**？


??? Question "**Q: 为什么头文件 `.h` 不需要写在 `gcc -c` 命令里，却要写在依赖列表里？**"

    A: 编译器会自动去找 `#include` 的头文件，但 `make` 需要你显式告诉它：如果 `.h` 变了，对应的 `.o` 也得重编。

