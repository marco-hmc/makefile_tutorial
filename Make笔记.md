## Makefiles笔记

[toc]

> 本文为阅读[source](https://makefiletutorial.com/#beginner-examples)所写的笔记。这里是对应的[仓库]()。

### 1. Getting started

* **为什么需要Makefiles？**

  现在的工程大多都是数十万行起，不可能写在一个文件下。根据功能，将代码模块化分成若干个文件是很显然的。因此必然需要一个工具能够串联连接不同文件，这就有了makefiles

* **还有必要学Makefiles吗？**

  Makefiles作为构建工具的一种，除了C/C++之外，java也是可以用makefiles的。而且Makefiles写法复杂，一般都用如下文提到的automake工具。

  现在主流的C/C++工程都用CMake、Bazel为构建工具。而VS甚至乎还有自己特殊的、windows专用的构建工具；Java则用Maven和Gradle为主；而解释性语言如python则因为其语言特性，是由解释器逐行解释的，不需要编译，也就不用make了。

  但是由于历史原因，C/C++的代码还是常能看到Makefiles的代码，因此只需要了解即可，并不需要深入。

### 2. Examples

#### 2.1 DEMO1 - Makefiles结构

* **Task**

  成功运行make，并执行命令。

* **prerequisites knowledge**

  ```shell
  TARGETS: PREREQUISITES
    COMMAND
  ```

  * `TARGETS`:

    ​	目标输出文件名字，一般来说，只会有一个

  * `PREREQUISITES`:

    ​	在COMMAND运行之前需要存在的文件名字，一般也叫作`dependencies`

  * `COMMAND`:

    ​	用来生成`TARGETS`的一系列命令，特别地，每句`COMMAND`之前用`tab`隔开而非空格。
    
    ​	不确定，但command里面的命令似乎与shell命令相同。在测试中Makefiles支持`ls` 和`cd`指令，故作上述猜测。

* **Code**

  ```shell
  hello:
  	echo "hello world"
  
  $ make
  >>>hello world
  ```

------



#### 2.2 DEMO2 - 单文件make

* **Task**

  完成最简单的单文件make。

* **Code**

  ```shell
  blah: blah.o
  	cc blah.o -o blah # runs third
  
  blah.o: blah.c
  	cc -c blah.c -o blah.o # runs second 
  
  blah.c:
  	echo "int main() {return 0;}" > blah.c # runs first
  
  $ make
  >>>生成blah.c文件;生成blah.o文件
  ```
  
  第`1`行依赖于`blah.o`，因此跳过，而第`2`行依赖于`blah.c`，也跳过，知道第`3`行，无依赖才执行。故执行顺序如上。
  
  而`cc`和`gcc`的区别可以看[这里](https://blog.csdn.net/sinat_28557957/article/details/87641491)，为什么就连Makefiles教程里面都会有`cc`，我感觉一是因为在`linux`里面`cc`和`gcc`等价，二也是因为历史原因，很多Makefiles教程就是用`cc`的，习惯于用`cc`。

------



#### 2.3 DEMO3 - 其他Makefiles syntax

* **Task**

  `makefile`中的变量定义、`clean`的语法

* **Prerequisites knowledge**

  ```shell
  # 将file1和file2赋值给变量名files
  # 以下两者相同
  files := file1 file2
  files = file1 file2 
  
  # 输出变量名
  # 以下两者相同，语法与shell类似
  $(files)
  ${files}
  
  # 删除make文件
  # 执行make clean就会运行clean处的comman代码
  clean:
    rm -f file1 file2
  ```

  * 注意Makefiles只会一定执行第一句make结构体，随后看第一句的`dependencis`，如果不满足，则执行`dependencies`的`target`那一行。

    因此`clean` 只有再执行`make clean`的时候才会运行，也不难得出把`clean`命名成`sdfzxc`也是可以的，因为`clean`不是保留的关键字。

* **Code1**

  ```shell
  files = file1 file2
  some_file: ${files}
  	echo "Look at this variable: " ${files}
  	touch some_file
  
  file1:
  	touch file1
  file2:
  	touch file2
  
  hhhh:
  	echo "hhhh"
  
  hhh:
  	rm -f file1 file2 some_file
  ```

* **Code2**

  ```shell
  all: one two three
  
  one:
  	touch one
  two:
  	touch two
  three:
  	touch three
  
  clean:
  	rm -f one two three
  ```


------



#### 2.4 DEMO4 - 变量通配符和Automatic变量

* **Task**

  使用通配符和automatic完成文本名称匹配。

* **Prerequisites knowledge**

  ```shell
  thing_wrong := *.o # Don't do this! '*' will not get expanded
  thing_right := $(wildcard *.o)
  
  hey: one two
  	echo $@ # refers to hey
  	echo $? # refers to all prerequisites those newer than target
  	echo $^ # refers to all prerequisites
  ```

* **Code**

  ```shell
  foo:=$(wildcard *.c)
  
  hey.c: one.c two.c
  	# Outputs "hey", since this is the first target
  	echo $@
  
  	# Outputs all prerequisites newer than the target
  	echo $?
  
  	# Outputs all prerequisites
  	echo $^
  
  	echo $(foo)
  	touch hey.c
  
  one.c:
  	touch one.c
  
  two.c:
  	touch two.c
  
  clean:
  	rm -f hey.c one.c two.c
  ```

------



#### 2.5 DEMO5 - Fancy rules

* **Task**

  make里面有一些fancy rules，似乎是专门为了C设置的。但实际上可以通过`COMMAND`那一行补充说明的。

* **prerequisites knowledge**

  ```shell
  CC = gcc # Flag for fancy rules. Using gcc as C complier
  CFLAGS = -g # Flag for fancy rules. Turn on debug info
  ```

* **Code**

  ```shell
  CC = gcc # Flag for implicit rules
  CFLAGS = -g # Flag for implicit rules. Turn on debug info
  
  # Implicit rule #1: blah is built via the C linker implicit rule
  # Implicit rule #2: blah.o is built via the C compilation implicit rule, because blah.c exists
  blah: blah.o
  
  blah.c:
  	echo "int main() { return 0; }" > blah.c
  
  clean:
  	rm -f blah*
  ```