# Set 4 可考知识点清单

- ISA 与微体系结构的区别：ISA 定义指令、编码、语义、字长、寄存器数量与地址可寻址性；微体系结构是实现细节（如加法器类型、控制/数据通路组织）。
- 冯·诺依曼模型与存储程序特性：指令/数据同一内存、PC 顺序取指。
- 地址空间与地址可寻址性（每个地址存多少位）；字节寻址 vs 字寻址。
- PC 顺序更新规则：32 位字节寻址 PC+4；32 位字寻址 PC+1。
- 大端/小端字节序与 32 位字装载结果计算。
- MAR/MDR 读写流程与“地址 vs 数据”的区分。
- ALU 与寄存器文件作用；ARM/MIPS/QuAC 寄存器数量与字长。
- 指令三大类：数据处理、数据移动、控制流；opcode 与 operands。
- ARM DP 指令格式关键位：cond、op、funct（含 I、S、cmd）、Rn、Rd、Src2；I=1 表示立即数。
- 立即数限制与编码取舍（imm8/imm12 等）；MOV 用于初始化寄存器。
- LDR/STR：基址+偏移寻址；有效地址 = 基址 + 偏移；内存为字节寻址但读取整字。
- ARM 条件执行：CPSR 的 N/Z/C/V 标志；CMP 或带 S 指令设置标志；条件助记符决定是否执行。
- 分支指令格式：imm24；BTA 计算步骤：imm24 左移 2、符号扩展、加 PC+8；Taken/Not-Taken 概念。
- if/if-else/switch 的 ARM 汇编实现（CMP + 条件分支或条件执行）。
- 单周期微体系结构：Datapath 与 Control Unit 分工；控制信号（RegWrite、MemWrite、MemtoReg、ALUSrc、ALUControl、ImmSrc、RegSrc、PCSrc）与条件执行“杀写”。
- Extend Block：ImmSrc=00 零扩展 imm8；ImmSrc=01 零扩展 imm12；ImmSrc=10 符号扩展 imm24 并左移 2。
- 性能公式：Execution time = N × CPI × clock period = N × CPI / f；CPI 由微体系结构决定，clock period 由关键路径决定。
