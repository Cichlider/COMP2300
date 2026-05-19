# Set 7 中文完整复写

## 模块 1：为什么需要流水线
- 课件先从并行性的两个基本方向切入：
  - `spatial parallelism`：复制硬件，让多个任务同时做；
  - `temporal parallelism`：把一个任务拆成多个阶段，让不同任务在不同阶段重叠执行。
- 流水线的重点是后者，也就是 temporal parallelism。
- 目标不是缩短单条指令的语义复杂度，而是提高单位时间内完成的指令数，也就是 throughput。

## 模块 2：流水线不会降低单条任务的本质延迟
- 课件通过饼干和洗衣服的例子强调：
  - 流水线可以提高 throughput；
  - 但不一定降低单个任务的 latency。
- 对电路也是一样：
  - 一条指令仍然要经过全部阶段；
  - 只是不同指令可以同时占据不同阶段。

## 模块 3：从单周期到多级阶段
- 单周期实现把 IF、ID、EX、MEM、WB 全部塞在一个时钟周期里。
- 流水线把这一长组合路径切分成多个较短阶段，并在阶段之间插入寄存器。
- 每一级的工作结果都要保存在 pipeline register 中，供下一周期下一阶段使用。
- 这也是为什么课件强调 `PPR`（pipeline registers）是流水线的核心基础设施之一。

## 模块 4：经典 5 级 ARM pipeline
- 课程里的主线 pipeline 仍然沿用我们已经熟悉的 5 个阶段：
  - Fetch
  - Decode / RF Read
  - Execute
  - Memory
  - Writeback
- 多条指令可以同时在流水线中，最多分别占据不同阶段。
- 这一点和多周期 CPU 很不同：多周期在同一时刻通常只推进同一条指令，而流水线允许跨指令重叠。

## 模块 5：构建 pipelined datapath
- 课件从单周期 datapath 出发，在合适位置插入 pipeline registers。
- 这不是简单“切一刀”，因为必须确保：
  - 所有后续阶段需要的控制信号被一路带过去；
  - 所有后续阶段需要的数据也被保存下来。
- 因为多条指令并行在飞，错误地复用某个控制位或目的寄存器字段，会导致把结果写错地方。

## 模块 6：修正数据通路中的 bug
- 课件专门展示了一个典型错误：如果目的寄存器编号没有被正确带到后面的阶段，那么后续写回可能写到错误寄存器。
- 这也是流水线实现里非常常见的考点：
  - 不是算术逻辑本身出错；
  - 而是“本该和这条指令同行的信息没有被一路保留到正确阶段”。

## 模块 7：流水线控制与时序图
- 控制单元仍然根据指令字段生成控制信号，但这些信号不再只作用于“当前唯一的一条指令”。
- 它们需要随着对应指令一起流经各个 pipeline registers。
- 时序图在这一讲里非常重要，因为它能直观看到：
  - 每个 cycle 哪条指令在哪一级；
  - 哪些阶段空闲；
  - hazard 发生时哪里被停住、哪里被冲刷。

## 模块 8：性能分析的正确视角
- 课件用 6 条指令、10 个 cycle 的例子说明：
  - `CPI = cycles / instructions = 10/6 = 1.66`
  - `IPC = instructions / cycles = 6/10 = 0.6`
- 理想流水线通常追求 `IPC` 接近 1（对单发射标量流水线）。
- 但真实系统还要面对 fill time、drain time、hazards 和寄存器开销。

## 模块 9：流水线的收益与代价
- 收益：
  - 更高 throughput；
  - 时钟周期可比单周期更短；
  - 让硬件在时间维度被更充分利用。
- 代价：
  - 单条指令 latency 通常不会下降，甚至会因 pipeline register overhead 略增；
  - 硬件控制复杂度上升；
  - 必须认真解决 hazards。

## 模块 10：hazards 的三大类
- 课件明确区分三类 hazard：
  - structural hazard：资源争用；
  - data hazard：数据相关导致的读写时序问题；
  - control hazard：控制流改变导致的取指不确定性。
- 需要记住的一点是：
  - dependence 是程序性质；
  - hazard 是某种微体系结构在该 dependence 下暴露出来的问题。

## 模块 11：RAW 与数据冒险
- 本课程在有序标量流水线里最核心的 data hazard 是 `RAW`（read-after-write）。
- 一条较新的指令需要读取一条较老指令尚未写回的结果时，就会有问题。
- 单周期 CPU 不会暴露这类问题，因为所有读写被压在一个周期语义里；流水线把不同指令错开以后，问题才显性出现。

## 模块 12：软件互锁与重排
- 课件先介绍最朴素的应对方法：
  - 插入 `NOP`；
  - 或者把独立指令往前移动，填补空隙。
- 这是 software interlocking 的思路。
- 缺点也很明显：
  - 需要编译器/程序员配合；
  - 会拉低性能；
  - 面对运行时事件（如 cache miss）不够灵活。

## 模块 13：forwarding / bypassing
- 更常见的硬件解法是 forwarding。
- 核心思想：
  - 不必等结果真的写回寄存器文件；
  - 只要在更早阶段已经算出来，就可以直接从后面阶段旁路到前面需要它的地方。
- 课件给出 forwarding 的必要条件，本质上是比较：
  - Execute 阶段的源寄存器编号；
  - Memory / Writeback 阶段的目的寄存器编号。

## 模块 14：为什么 load-use 仍然要 stall
- 即使有 forwarding，`LDR` 之后紧跟使用该结果的指令，仍然可能不够早。
- 原因是 load 的数据直到 `MEM` 阶段末才真正得到。
- 所以依赖它的那条指令如果太早进入 `EX`，就没有数据可拿。
- 这就是典型 `load-use hazard`，也是这讲最重要的 stall 场景。

## 模块 15：stall 与 bubble
- 课件把 stall 的实现拆得很清楚：
  - stall 当前阶段；
  - stall 它之前的阶段；
  - 向它之后的阶段注入 bubble。
- bubble 本质上是一条“空操作占位”，随着流水线往后流。
- 这类题目最适合用 cycle-by-cycle timing diagram 理解。

## 模块 16：load-use hazard 的检测逻辑
- 课件给出了比较典型的检测思路：
  - 看 Decode 阶段的源寄存器是否与 Execute 阶段 load 的目的寄存器相同。
- 若匹配且 Execute 阶段确实是 load，则：
  - 停 Fetch；
  - 停 Decode；
  - 冲刷流水线中相应的 Execute 寄存器，制造 bubble。

## 模块 17：control hazard 与最朴素的办法
- 分支或写 `PC` 会让“下一条该取什么指令”变得不确定。
- 最简单但最保守的办法是：
  - 发现分支后直接 stall，直到分支结果和目标地址明确。
- 这种方法实现容易，但浪费很多周期。

## 模块 18：静态分支预测
- 课件先介绍静态策略：
  - always untaken
  - always taken
- `predict-always-untaken` 的优点是实现简单，且若预测正确，流水线能继续向前。
- 缺点是预测错时必须 flush 已经错误取入的指令。

## 模块 19：提前分支解析
- 如果把 branch target address 和分支判定更早地放到 `EX` 阶段解决，就能比“等到 WB 才知道”减少 penalty。
- 课件中这一优化使 branch misprediction penalty 从 4 cycles 减到 2 cycles。
- 所以考试里常见问法是：
  - 早解析发生在哪一级？
  - 相比原设计节省了几拍？

## 模块 20：stall / flush 组合逻辑
- 当系统同时支持 load-use stall 与 branch flush 时，控制逻辑不再是单一条件。
- 课件给出了合成后的信号思路，例如：
  - `StallF`
  - `StallD`
  - `FlushD`
  - `FlushE`
- 此外，普通指令写 `PC` 也会制造类似 control hazard，因此也要纳入 stall / flush 逻辑。

## 模块 21：从标量流水线走向 superscalar
- 课件后半段把思路扩展到 superscalar：
  - 一拍发射多条；
  - 复制更多 datapath 资源；
  - 理想情况下 `IPC` 可大于 1。
- 但代价是：
  - 依赖关系更密集；
  - register file、ALU、memory ports 都更昂贵；
  - branch misprediction 的代价通常更高。

## 模块 22：dynamic branch prediction
- 在 superscalar 和更深流水线里，静态预测往往不够好，于是课件引入动态预测。
- 动态预测的核心要素包括：
  - branch identification：通常由 branch 的 PC 来索引；
  - recent behavior：根据最近历史预测未来方向。

## 模块 23：one-bit predictor
- 最简单的动态预测器是 one-bit predictor。
- 通常通过 `BHT`（Branch History Table）记录某分支最近一次是 taken 还是 not taken。
- 问题在于：
  - 对 loop 这类“长时间 taken，最后一次 not taken”的模式，它往往会在方向翻转时连续犯错。

## 模块 24：Smith predictor（2-bit saturating counter）
- James E. Smith 的关键洞察是给预测器加“惯性”。
- 2-bit predictor 有四个状态：
  - strongly not taken
  - weakly not taken
  - weakly taken
  - strongly taken
- 这样分支行为偶发反转时，不会立刻把预测完全翻面。
- 这也是课程里分支预测状态机的高频考点。

## 模块 25：BTB 与相关预测
- 只知道 taken / not taken 还不够；若预测 taken，CPU 还需要快速得到 target address。
- 这就是 `BTB`（Branch Target Buffer）的作用。
- 课件还进一步提到 correlating predictors，说明真实程序里不同分支之间可能存在行为相关性。

## 模块 26：本讲收束
- 这讲的主线其实很完整：
  - 先建立流水线的收益模型；
  - 再展示 hazards 为什么不可避免；
  - 然后逐步引入 forwarding、stalling、flushing 和 branch prediction；
  - 最后把视角扩展到 superscalar 与更现实的高性能处理器。
- 从考试角度，最重要的是把时序图、hazard 类型、stall/flush 条件和 branch predictor 状态机真正算清楚。
