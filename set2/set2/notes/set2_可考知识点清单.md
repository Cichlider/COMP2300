# set2 可考知识点清单（可算/可考/lab可用）

## 1. 组合电路 vs 时序电路
- 组合电路：输出仅由当前输入决定。
- 时序电路：输出由当前输入 + 历史状态决定。
- 高频判断题：是否有状态（memory）是关键区分。

## 2. 组合逻辑标准设计流程
- English spec -> truth table -> Boolean equation -> simplification -> gate-level schematic。
- 必会：从题干描述提取输入/输出含义并写全真值表。

## 3. 布尔术语与优先级
- Literal、Implicant、Minterm、Maxterm。
- 运算优先级：`NOT > AND > OR`。
- Canonical form 不等于 minimal form。

## 4. SOP / POS 规范表示
- SOP：把输出为 1 的所有 minterm 求和（OR）。
- Sigma 表示：`F(A,B,C)=Σm(i,j,...)`。
- POS：把输出为 0 的所有 maxterm 求积（AND）。
- 常考：真值表 <-> SOP/POS 双向转换。

## 5. MUX（多路复用器）
- 2:1 MUX 方程：`Y = S'D0 + SD1`。
- 4:1 MUX 由 2:1 MUX 级联实现。
- LUT 观点：`2^N:1` MUX 可实现任意 `N` 输入逻辑函数（数据端接 0/1）。

## 6. Half Adder / Full Adder
- Half Adder：
  - `S = A ⊕ B`
  - `Cout = AB`
- Full Adder：
  - `S = A ⊕ B ⊕ Cin`
  - `Cout = AB + Cin(A ⊕ B)`
- 高频：由真值表推导加法器方程，或由方程反推电路。

## 7. Ripple Carry Adder（RCA）与延迟
- N 位 RCA 由 N 个 Full Adder 串联。
- 关键路径穿过进位链，延迟随 N 增大。
- 典型表达：`t_RCA ≈ N * t_FA`（抽象模型）。

## 8. Carry Lookahead Adder（CLA）
- Carry Generate：`G_i = A_i B_i`
- Carry Propagate：`P_i = A_i + B_i`（或教材中等价定义）
- 进位递推：`C_{i+1} = G_i + P_i C_i`
- 核心考点：为什么 CLA 更快；代价是什么（面积/功耗）。

## 9. Decoder
- 例：2:4 decoder 的 one-hot 输出特性。
- 应用：控制单元中的指令译码、地址选择。
- 高频题：给输入写输出方程，或反向识别解码器功能。

## 10. PLA
- 两级可编程结构（AND 平面 + OR 平面）实现 SOP。
- 可编程优势 vs 冗余成本的权衡。

## 11. ALU 指令控制与 Add/Sub 统一实现
- 控制示例：`00 Add, 01 Sub, 10 AND, 11 OR`。
- `A - B = A + (B' + 1)`。
- 常考：为何 Sub 可复用加法器（控制低位进位 + B 取反）。

## 12. ALU Flags（N/Z/C/V）
- `N`：结果 MSB。
- `Z`：结果全 0。
- `C`：进位信号（通常针对算术路径）。
- `V`：有符号溢出。
- 高频：给输入与结果，判断各标志位。

## 13. 溢出判定（算术题核心）
- 有符号加法溢出：同号相加得异号。
- 等价实现：符号位相关 XOR/XNOR 逻辑。
- 常见误区：把 carry-out 直接当作有符号溢出。

## 14. 组合电路时序与关键路径
- 传播延迟：输入变化到输出稳定时间。
- 关键路径：最长延迟路径。
- 常考：给元件延迟表，计算不同功能路径总延迟并找最慢路径。

## 15. Tri-State Buffer
- 三态输出：`0/1/Z`，`Z` 为高阻态。
- 共享总线原则：任一时刻最多一个驱动器使能。
- 考点：总线冲突、浮空线风险。

## 16. 布尔代数最小化
- 常用定律：
  - `X + 0 = X`，`X·1 = X`
  - `X + 1 = 1`，`X·0 = 0`
  - `X + X = X`，`X·X = X`
  - `X + X' = 1`，`X·X' = 0`
  - `PA + PA' = P`
- 化简目标：优先减少 implicant 数，再减少 literal 数。

## 17. De Morgan 与 Bubble Pushing
- ` (AB)' = A' + B' `
- ` (A+B)' = A'B' `
- 泡泡推动会把 AND/OR 门体互换并保持逻辑等价。

## 18. Priority Circuit 与 Don’t Care
- 优先编码/仲裁题：高优先级请求会屏蔽低优先级输出。
- Don’t Care 用于减少真值表约束、帮助方程化简。

## 19. 逻辑完备性
- `{AND, OR, NOT}` 完备。
- `NAND` 单独也完备（可构造 NOT/AND/OR）。
- 高频概念题：为何工业实现可以大量使用 NAND。

## 20. 建议重点练习题型（set2）
- 从自然语言写真值表，再写最简 SOP。
- 用 2:1 MUX 实现给定布尔函数。
- Half/Full adder 方程推导。
- RCA/CLA 延迟与关键路径比较。
- Decoder/PLA 结构题。
- ALU 控制 + N/Z/C/V 标志位判断。
- De Morgan 与 bubble pushing 电路等价变换。
