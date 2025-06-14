我们都知道 C++  代码的编译过程分为：==预处理==、==编译==、==汇编==和==链接==四个阶段，最终生成我们可以执行的可执行文件。以下是各个阶段的详细内容：

---
## 预处理

该阶段的主要任务是在编译阶段前将源代码中的一些内容处理得到一个没有预处理指令、没有注释、头文件展开后的 ”干净“ 的 C++ 文本文件。

主要任务有：
1. `#include` 处理：头文件展开。需要防止头文件循环包含。
2. `#define` 宏展开：替换所有的宏定义。
3. 条件编译 (`#ifdef`, `#ifndef`, `#if`, `#endif`, `#else`, `#elif`): 根据条件保留或删除代码块。
4. 删除注释内容。
5. 添加行标记 (`#line`)：为调试器和错误消息保留源文件行号信息。
6. 处理 `#pragma`指令：编译器特定的指令（如优化提示）。

---
## 编译

将经过预处理器处理的源码翻译成对应的汇编语言文件(.s)。也是整个代码编译过程中最容易报错和耗时阶段。

主要任务有：
1. 词法分析：将字符流分解成有意义的词法单元（Tokens），识别其中的关键字，标识符，常量，运算符和分隔符等。
2. 语法分析：根据语法规则，将 Tokens 组合成抽象语法树（AST)。检查基本的语法错误。
3. 语义分析：进行更深入的检查，如类型检查、作用域解析、检查语法规则。
4. 代码优化。
5. 代码生成：将优化后的中间代码转换成特定目标处理器的==汇编语言==代码。

---
## 汇编

将汇编代码翻译成机器可识别的二进制机器指令，得到一个或多个目标文件 (.o, .obj)。

---
## 链接

此阶段会将所有的目标文件最终链接成一个可执行文件或一个库文件。

主要任务有：
1. 符号解析：链接器会扫描所有输入的目标文件和库文件，确保每个符号有且仅有一个定义。
2. 地址和空间分配：链接器将所有输入的目标文件中的同类型段合并。
3. 重定位：遍历所有目标中的重定位条目，计算符号的最终运行时的地址。
4.  解析库依赖：对于静态链接会从静态库中提取实际被引用的目标文件，将它们的内容完全复制到可执行文件中（这也增加了可执行文件的体积）。对于动态链接，链接器会在可执行文件中记录它依赖哪些动态库以及哪些符号，设置程序的动态段，供运行时加载器可以正确加载并解析它们。

