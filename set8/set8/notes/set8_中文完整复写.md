# Set 8 中文完整复写

## 模块 1：从流水线重叠走向 ILP
- 这套课件从 `Instruction-Level Parallelism (ILP)` 开始。
- 核心定义非常直接：
  - 把多条指令的执行重叠起来，就是 ILP。
- 我们前面学过的标量有序流水线已经能提供一部分 ILP，但它还远远不是极限。

## 模块 2：dependence 与 hazard 的再次区分
- 课件开头再次强调了一个极其重要的概念区分：
  - dependence 是程序性质；
  - hazard 是微体系结构性质。
- 要想更激进地挖掘 ILP，必须分清“程序语义上真的不能交换”的约束，和“某种实现方式碰巧卡住了”的约束。

## 模块 3：三类依赖
- 课件分别讲了：
  - true dependence
  - anti dependence
  - output dependence
- 对应到经典术语：
  - true dependence $\rightarrow$ `RAW`
  - anti dependence $\rightarrow$ `WAR`
  - output dependence $\rightarrow$ `WAW`
- 其中只有 true dependence 直接反映了“后者真的需要前者的值”。

## 模块 4：有序流水线为什么不够
- 在简单 in-order pipeline 中，指令按程序顺序 fetch、decode、execute。
- 如果前面某条指令因为长延迟事件卡住，后面很多本来独立的指令也会一起被拖住。
- 课件用带 data cache miss 的例子说明：
  - 问题不只是依赖；
  - 即使后续指令独立，如果机器坚持“前面的没走完，后面的不能超车”，吞吐也会很差。

## 模块 5：更激进的 in-order 场景仍然暴露瓶颈
- 课件故意构造了一个“更激进”的 in-order 机器：
  - 多个 ALU；
  - memory 也能并行处理多个请求；
  - decode 和 register read 分离。
- 即便如此，只要整体仍然保持严格 in-order，独立指令仍然会被较老的非就绪指令挡住。

## 模块 6：OOO 的核心想法
- `Out-of-Order` 的核心不是“随便乱执行”。
- 它的关键约束是：
  - 如果有 `RAW`，后者必须等；
  - 但若后面的指令不依赖当前卡住的那条，就可以先走。
- 所以 OOO 的目标是让“非就绪的老指令”不要阻塞“就绪的年轻指令”。

## 模块 7：issue queue / reservation stations
- 为了实现这一点，课件引入了一个新结构：
  - issue queue
  - 也被称为 reservation stations / scheduler / scheduling window
- 思想是：
  - 停住的指令先排进队列；
  - 只要操作数 ready，就可以从队列里发射到执行单元。
- 这样，fetch 和前端不必被单条非就绪指令完全堵死。

## 模块 8：dynamic scheduling
- 课件把 scheduling 定义为“决定哪条指令接下来执行”。
- 若这个决定由硬件在运行时完成，就是 dynamic scheduling。
- 相比静态调度，它的优势在于：
  - 能根据运行时 miss、ready 状态和真实数据依赖做选择；
  - 不需要编译时就完全知道延迟。

## 模块 9：scoreboard 版本的 OOO（v1）
- 课件借 CDC 6600 引出早期 scoreboard 模型。
- 新增阶段大意是：
  - Dispatch / Allocate：把指令送入 issue queue；
  - Issue / Schedule：挑 ready 的指令去执行。
- scoreboard 跟踪寄存器是否 ready / busy，并帮助决定哪些指令还必须等待。

## 模块 10：scoreboard 的能力与局限
- scoreboard 已经能做很多事：
  - 打破“前面一停，后面全停”的局面；
  - 利用 issue queue 发掘更多 ILP；
  - 让 independent instructions 越过卡住的 older instruction。
- 但它也有明显缺点：
  - 无法优雅处理 misspeculation；
  - exceptions 不精确；
  - `WAR` / `WAW` 仍可能阻塞前进。

## 模块 11：precise exception 为什么重要
- 课件专门花了一段讲 precise exceptions。
- 定义是：
  - faulting instruction 之前的指令都已经完成并可见；
  - faulting instruction 本身尚未部分提交破坏状态；
  - faulting instruction 之后的年轻指令也还没有污染 architectural state。
- 这对调试、操作系统和恢复机制都很关键。

## 模块 12：为什么早期 OOO 会产生不精确异常
- 如果指令乱序执行且直接把结果写进 architectural register file，
  那么较新的指令可能先把状态改掉。
- 一旦更老的指令随后抛出异常，就很难恢复到“出错前最后一个精确状态”。
- 这就是仅有 scoreboard 的 OOO v1 的核心问题之一。

## 模块 13：硬件推测的四个组成件
- 课件随后把系统升级为更现代的硬件推测模型，并给出四个关键点：
  - register renaming
  - dynamic branch prediction
  - dynamic scheduling
  - in-order retirement / recovery support
- 其中 register renaming 是解决假依赖的核心。

## 模块 14：register renaming
- rename 的本质是：
  - 给每个写目的寄存器的结果分配一个唯一新名字；
  - 让后续指令引用这个新名字，而不是直接把 architectural register 当唯一版本。
- 这样可以消除：
  - `WAR`
  - `WAW`
- 但不能消除 `RAW`，因为真正的数据流依赖仍然存在。

## 模块 15：ROB 与 ARF
- 课件在 OOO v2 中引入 `ROB`（Reorder Buffer）。
- 同时，architectural state 被拆成两层理解：
  - `ARF`：已提交、非 speculative 的 architectural registers；
  - `ROB`：尚未提交、可能 speculative 的新版本结果。
- 这样做的目的是：
  - 允许乱序执行；
  - 但最终按程序顺序提交。

## 模块 16：rename stage 的工作
- 新指令进入 rename 阶段后，会做几件典型事情：
  - 在 ROB 中分配一个新 entry；
  - 这个 entry 的 tag 变成该指令目的寄存器的新名字；
  - 源操作数被改写成它们当前应引用的 tag 或已有值。
- 这一步把“寄存器名字”和“当前该看哪一版值”分离了。

## 模块 17：读取源操作数时看哪里
- 课件给出了一种很重要的运行时视角：
  - source value 可能来自 ARF；
  - 也可能来自 ROB 中尚未退休的较新版本；
  - 也可能来自 forwarding。
- 所以 register read 在现代 OOO 里并不是“只查一个寄存器文件”那么简单。

## 模块 18：writeback、retirement 与恢复
- 在 ROB 模型下，执行单元完成计算后，不是直接更新 ARF，而是先把结果写到 ROB entry。
- 只有当该指令来到 ROB head，且所有更老指令都已经可以按序提交时，才会 retire。
- retire 时，结果才真正写入 ARF，成为 architectural state 的正式版本。
- 这就是“OOO execution + in-order commit”。

## 模块 19：为什么 ROB 能解决前面的问题
- 有了 ROB：
  - 可以在 misprediction 或 exception 时丢弃 speculative younger instructions；
  - 可以保持 precise exceptions；
  - 可以配合 renaming 消除假依赖。
- 因此 ROB 是从“能跑起来的 OOO”走向“现代可恢复 OOO”的关键结构。

## 模块 20：load miss 场景与 OOO 的优势
- 课件用长延迟 load miss 场景做了详细时序例子。
- 结论是：
  - 依赖 load 结果的指令必须等；
  - 但后面与它无关的指令可以继续前进、进入队列、甚至执行。
- 这就是 OOO 真正提高性能的主要来源之一。

## 模块 21：branch、speculation 与 recovery
- 现代 OOO 不只处理 data dependence，还必须配合 branch prediction 做 speculation。
- 错了怎么办？
  - 借助 ROB 等结构回滚错误路径上的 speculative 指令；
  - 恢复到正确的 architectural state。
- 这也是为什么“没有 recovery 能力的动态调度”在现代意义上还不够。

## 模块 22：OOO 的 loads / stores 与 LSQ
- 课件进一步指出：load 和 store 也可以乱序执行，但必须小心地址别名问题。
- 若 load 和较老的 store 访问不同地址，就可能安全重排。
- 若地址相同，乱序就可能破坏语义。
- 因此引入 `LSQ`（load-store queue）来追踪这些关系，并支持 speculative load execution。

## 模块 23：Tomasulo 脉络
- 课件把 ARF+ROB 与 Tomasulo 历史脉络连起来：
  - Tomasulo 的思想是 renaming + dynamic scheduling；
  - 现代实现加入 ROB 后，更容易保证精确异常与投机恢复。

## 模块 24：现代处理器设计中的 ILP
- 后半段把 OOO 放回更大的架构图景中：
  - 通过 renaming 去掉假依赖；
  - 通过 branch prediction 把 future work 预先灌进窗口；
  - 通过 issue queue / schedule 让 ready instructions 尽早执行；
  - 通过 in-order retirement 保持软件可见语义正确。

## 模块 25：静态调度、动态调度与 superscalar 分类
- 课件总结了几类 ILP 利用方式：
  - statically scheduled superscalar
  - dynamically scheduled superscalar
  - 更广义的 hardware speculation 体系
- 核心差异在于：
  - 指令顺序优化是谁做；
  - 是否能运行时重排；
  - 是否具备 speculation / recovery。

## 模块 26：MLP 与系统级并行
- OOO 处理器不只在指令层面挖 ILP，也会自然推动 `MLP`（memory-level parallelism）。
- 因为它会产生更多在飞的 load misses，而后端 cache / memory system 也必须能并行响应。
- 这就是为什么 later slides 把 OOO、non-blocking caches、parallel DRAM subsystem 放在一起看。

## 模块 27：更广泛的并行与副作用
- 课件最后还把视角扩展到：
  - multicore
  - hyperthreading
  - speculative-execution-related security issues
- 重点不是要求大家立即掌握安全攻击细节，而是理解：
  - 这些高性能技术虽然带来吞吐提升；
  - 也会带来更复杂的实现、功耗和安全代价。

## 模块 28：本讲收束
- 从考试与复习角度，这讲真正要吃透的是：
  - RAW / WAR / WAW 与真假依赖；
  - 为什么 in-order 不够；
  - issue queue 和 dynamic scheduling 做了什么；
  - renaming 为什么能去掉假依赖；
  - ROB 为什么能保证 in-order retirement 与 precise exceptions；
  - load/store 乱序为什么需要 LSQ。
