# Set 6 中文完整复写

## 模块 1：从分支回顾到函数支持
- 这套课件先回顾了前面已经学过的内容：数据处理指令、访存指令、分支指令以及若干 addressing modes。
- 在进入函数之前，课件先重新强调了 branch instruction 的编码方式，因为函数调用本质上也是一种“改变控制流”的操作。
- 分支指令中的立即数字段并不是一个绝对地址，而是相对于 `PC + 8` 的位移。

## 模块 2：Branch 指令与分支目标地址
- ARM 分支格式中，`op = 10`，并使用 `imm24` 作为 24 位有符号立即数。
- 处理器计算 Branch Target Address（BTA）时，要先把 `imm24` 左移两位，把“word offset”转成“byte offset”。
- 然后对结果做符号扩展，最后再与 `PC + 8` 相加。
- 这也说明了 ARM 的普通分支属于一种 `PC-relative addressing mode`。

## 模块 3：为什么函数需要 ISA 支持
- 高级语言里的函数带来抽象、模块化、代码复用、可读性、可维护性与可测试性。
- 函数在软件里极其常见，因此 ISA 往往会专门提供适合函数调用的控制流指令，而不只是提供“普通跳转”。
- 课件把函数、procedure、subroutine 视为同一类概念。

## 模块 4：caller、callee 与函数执行过程
- `caller` 是发起调用的函数，`callee` 是被调用的函数。
- 一个函数调用大体可拆成以下动作：
  - caller 准备输入参数；
  - caller 把控制流转交给 callee；
  - callee 为执行分配或使用所需资源；
  - callee 执行函数体；
  - callee 返回结果并恢复现场；
  - caller 继续原来的执行。
- 一个函数是否是 leaf / non-leaf，取决于它是否继续调用其他函数。

## 模块 5：ARM 中的函数调用指令
- ARM 为函数调用提供了 `BL`（Branch and Link）。
- `BL` 做两件事：
  - 跳转到目标 label；
  - 把返回地址保存到 `LR`（Link Register）。
- 返回时，课件先用最直接的方式说明：把 `LR` 的值写回 `PC`，也就是 `MOV PC, LR`。
- 从课程理解角度看，关键点是“函数调用除了跳转，还必须记住返回地址”。

## 模块 6：参数传递与返回值约定
- 课件使用 ARM 约定：
  - `R0-R3` 用于前四个参数；
  - `R0` 用于返回值。
- ARM 寄存器角色在这套课件里也被明确总结：
  - `R0-R3`：argument / return value / temporary；
  - `R4-R11`：saved variables；
  - `R12`：temporary；
  - `R13`：`SP`；
  - `R14`：`LR`；
  - `R15`：`PC`。
- 当参数超过四个，或者需要额外临时空间时，就需要借助 stack。

## 模块 7：为什么需要栈
- 课件给出三个很核心的用途：
  - 保存和恢复寄存器；
  - 传递超过寄存器数量的额外参数；
  - 存放函数局部变量与临时数据。
- 同时，寄存器“live / dead”的概念也被引入，用来说明为什么有些值必须在调用前被保存。
- 只有理解 liveness，calling convention 才有意义。

## 模块 8：栈的抽象与实现
- 栈是一个 `LIFO`（Last In First Out）结构。
- 实现一个栈需要两样东西：
  - 一片可用的内存；
  - 一个指向栈顶的指针，也就是 `SP`。
- 课件先用抽象示意讲“grow / shrink”，再过渡到 ARM 实际使用的栈方向。

## 模块 9：栈的方向与 ARM 的选择
- 课件区分了几种管理方式：
  - ascending vs descending；
  - full vs empty。
- 对 ARM 而言，课程里强调的是：
  - 栈向低地址方向增长；
  - `SP` 指向当前栈顶；
  - 这对应 full descending stack 的直觉。
- 因此，在 ARM 语境下，压栈通常意味着先减小 `SP`，再把数据写到新位置；出栈则是先读，再增大 `SP`。

## 模块 10：push / pop 与寄存器保护
- `push` 与 `pop` 是对“调整 SP + 访存”的抽象封装。
- 课件用 `push {R0}`、`pop {R0}` 说明：
  - push 的本质是把寄存器内容写到 stack 上；
  - pop 的本质是从 stack 中恢复寄存器。
- 在函数实现里，栈最重要的用途之一就是保护 caller 原本还要继续使用的寄存器值，避免 callee 意外破坏它们。

## 模块 11：从手工保存寄存器到 calling convention
- 课件先展示了“无规则地保存所有会改动寄存器”的办法，例如在函数开头分配栈空间，依次 `STR` 若干寄存器，结尾再 `LDR` 回来。
- 这种方法虽然正确，但可能很浪费，因为函数不一定真的需要保护所有寄存器。
- 所以进一步引出了 `calling convention`：让 caller 和 callee 分工，形成统一契约。

## 模块 12：ARM calling convention
- 调用约定本质上是“谁负责保存哪些寄存器”的合同。
- 课件将寄存器大体划分为：
  - preserved registers：跨调用必须保持不变；
  - non-preserved / temporary registers：可被 callee 自由破坏，若 caller 还要用，caller 自己先保存。
- 对应规则是：
  - caller-save：caller 负责保护自己仍需要的易失寄存器；
  - callee-save：callee 若要改动被要求 preserved 的寄存器，必须先保存、结束前恢复。

## 模块 13：函数序言、尾声与栈帧
- 一个函数的典型结构可以理解成：
  - prologue：保存寄存器、分配栈空间；
  - body：执行主要计算；
  - epilogue：恢复寄存器、回收栈空间、返回。
- 课件中的 `DIFFOFSUMS` 和 `f1` 例子都体现了这一点。
- 一个函数在栈上占用的那一段空间叫 `stack frame`，也叫 `activation record`。
- 程序运行过程中会同时存在多个 stack frame，它们共同组成 `call stack`。

## 模块 14：ABI 与 ISA 的边界
- 课件特别提醒：calling convention 不是 ISA 的一部分。
- 它属于 procedure-call interface，更广义地说属于 `ABI`（Application Binary Interface）。
- 这意味着：
  - ISA 告诉你机器“能做什么”；
  - ABI 规定不同编译器、函数、模块“怎样彼此兼容地协作”。

## 模块 15：递归与函数既是 caller 又是 callee
- 递归函数是 non-leaf function，因为它会再次调用自己。
- 因此它同时扮演 caller 和 callee：
  - 作为 callee，它要维护自己的执行环境；
  - 作为 caller，它又要为下一层调用准备参数并保护必要值。
- 这也是为什么递归特别依赖 stack。

## 模块 16：factorial 例子中的递归执行
- 课件用 `factorial` 展示了递归调用过程。
- 关键动作包括：
  - `PUSH {R0, LR}`：保存当前参数 `n` 和返回地址；
  - 判断基例 `n <= 1`；
  - 递归调用前修改参数；
  - 递归返回后恢复先前保存的值，并完成乘法；
  - 最后通过恢复 `LR` 并返回，逐层回到上一层调用。
- 每次递归都会新建一层 stack frame，因此递归深度越大，栈空间消耗越多。

## 模块 17：关于递归的工程视角
- 课件没有把递归神化，而是明确指出：
  - 递归通常更清晰；
  - 但任何递归解通常都存在等价迭代解。
- 所以考试和编程里真正重要的，不是“递归一定更高级”，而是你是否理解：
  - 它怎样使用栈；
  - 为什么要保存 `LR`；
  - 为什么有时还要额外保存参数或中间结果。

## 模块 18：本套课件的收束
- `BL` / `LR` 解决了“怎么去”和“怎么回”的问题。
- stack 解决了“中途需要额外空间”和“如何保护现场”的问题。
- calling convention / ABI 解决了“不同函数、不同模块如何可靠协作”的问题。
- recursion 则把这些机制都推到最典型、最容易出错的场景中，帮助我们真正理解函数调用在底层是如何工作的。
