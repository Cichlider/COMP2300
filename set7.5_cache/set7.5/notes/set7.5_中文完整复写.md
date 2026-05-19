# Set 7.5 中文完整复写

## 模块 1：locality 是 memory hierarchy 的起点
- 这套课件从 `Principle of Locality` 出发。
- 课件强调：程序倾向于访问“最近访问过的地址”或者“与最近访问地址很接近的地址”。
- 这对应两类经典 locality：
  - temporal locality：最近用过的数据或指令很可能再次被用到；
  - spatial locality：与当前地址相邻的数据或指令也很可能被访问。

## 模块 2：为什么 locality 值得被利用
- 一旦程序真的表现出 locality，就没有必要每次访问都跑到最慢、最大、最便宜每 bit 的那层存储。
- 这就是 memory hierarchy 的根本动机：
  - 把“最常用的一小部分数据”放在更快但更小的存储里；
  - 把“大部分不常用的数据”放在更慢但更大的存储里。
- 软件与硬件都在共同利用 locality，但 cache 主要是由硬件自动管理。

## 模块 3：memory hierarchy 的基本规律
- 课件强调了一个长期有效的硬件规律：
  - 更快的存储通常更贵、容量更小、功耗更高；
  - 更慢的存储通常更便宜、容量更大。
- 因此现实系统常见层级是：
  - registers
  - L1 / L2 / L3 caches
  - main memory
  - disk / other storage

## 模块 4：cache 的定义
- cache 是一个更小、更快的存储设备，用来缓存更大、更慢设备中的一部分数据。
- 课件将它描述为一个 staging area。
- 从 CPU 视角看，cache 让“常见访问”可以更快完成，而不必每次都触碰主存。

## 模块 5：cache 的基本工作方式
- cache 通常以 block / line 为单位搬运数据，而不是按单个字节随意拷贝。
- 一次访问的结果只有两种：
  - hit：需要的数据已经在 cache 中；
  - miss：需要的数据不在 cache 中，需要从下一层取回。
- cache line 会同时保存：
  - data block
  - tag
  - 以及有效位等元数据

## 模块 6：hit rate / miss rate 的意义
- 课件明确给出：
  - `miss rate = misses / accesses`
  - `hit rate = 1 - miss rate`
- 更关键的是：hit 和 miss 的代价差距可能是数量级上的。
- 因此，哪怕 hit rate 只提升几个百分点，也可能显著影响平均访问时间。

## 模块 7：placement、replacement 与 associativity
- cache 不只是“有没有命中”，还要回答：
  - 一个 block 能放到哪些位置？
  - 这些位置满了以后替换谁？
- 课件在这一部分把 set、line、tag、block 和 associativity 联系起来讲。
- 这也是 direct-mapped 和 set-associative 区别的基础。

## 模块 8：direct-mapped cache
- direct-mapped cache 可视为每个 set 只有一条 line。
- 优点：
  - 结构简单；
  - 查找快；
  - 硬件成本较低。
- 缺点：
  - 某个 block 只有唯一可放位置；
  - 因此容易出现 conflict miss。
- 课件通过带有地址字段的例子说明：
  - 地址可拆成 tag / set index / block offset。

## 模块 9：2-way set associative cache
- 在 2-way set associative cache 中，每个 set 有两条 line 可选。
- 这样一个 block 落到某个 set 后，不再只有唯一位置。
- 好处是降低 direct-mapped 中的冲突问题。
- 代价是硬件更复杂，因为要比较多个 tag，并处理替换策略。

## 模块 10：write policies
- 课件把写策略单独拿出来讲，因为写命中和写缺失牵涉“多份数据副本一致性”。
- 常见组合包括：
  - write-through：命中时立刻把更新写到更低层；
  - write-back：先只更新 cache，等 line 被替换时再写回；
  - no-write-allocate：写 miss 时不把 block 拉进 cache；
  - write-allocate：写 miss 时先把 block 拉入 cache 再更新。
- 这些策略是 cache 设计的高频概念题来源。

## 模块 11：为什么要有多级 cache hierarchy
- 课件不是只停留在抽象概念，而是展示了 Intel、AMD、ARM 实例。
- 重点不是死记某个处理器参数，而是理解：
  - 更靠近 CPU 的层更小更快；
  - 更远的层更大更慢；
  - 多级 hierarchy 是在性能、容量、成本之间折中。

## 模块 12：从 cache 过渡到 storage elements
- 课件后半段把视角从“系统级 memory hierarchy”缩回到“底层如何存 bit”。
- 也就是说，它不仅问“为什么需要 cache”，还问“这些不同层级本身是用什么存储电路做出来的”。

## 模块 13：flip-flops 与 generalized storage element
- 最基础的同步存储元素是 latch / flip-flop。
- 它们非常快，但每 bit 成本很高，因此不适合拿来做大容量主存。
- 课件随后抽象出 generalized storage element：
  - wordline 用于选择 bit cell；
  - bitline 用于读或写该 bit。

## 模块 14：memory organization
- 多个 bit cell 组合成一行、一列，配合 decoder 和输出选择逻辑，就形成更大的 memory array。
- 地址经过 decoder 选中某一行；
- bitlines 负责在选中的存储单元与外围电路之间传递数据。
- tri-state buffer 在这里也被回顾，用来解释如何让多路信号共享导线。

## 模块 15：SRAM bit cell
- `SRAM` 用交叉耦合反相器保存状态。
- 它的特点是：
  - 非常快；
  - 读出不需要周期性刷新；
  - 每 bit 晶体管数量相对更多，因此面积更大、成本更高。
- 这正好解释了为什么 cache 常用 SRAM。

## 模块 16：RAM 的随机访问语义
- 课件明确 `RAM` 的“random”不是随机内容，而是随机地址可独立访问。
- 任意地址的数据都可以在相近方式下被访问，而不是像磁带那样必须顺序扫过去。
- 这也是 RAM 与 tape / rotating storage 在访问模式上的关键差别。

## 模块 17：顺序访问 vs 随机访问存储介质
- 课件用磁带和硬盘举例来帮助建立直觉：
  - 有的设备更像 sequential access；
  - 有的设备更像 random access；
- 这里的重点是访问路径和延迟模型不同，不是“有没有地址”这么简单。

## 模块 18：DRAM bit cell
- `DRAM` 用“电容上是否有电荷”来表示 bit。
- 单个 bit cell 结构更简单，密度更高，适合做大容量主存。
- 但它的代价是：
  - 读操作会破坏存储内容；
  - 电荷会泄漏；
  - 因此需要 restore 和 refresh。

## 模块 19：DRAM read / write / refresh
- 课件强调：
  - DRAM read 是 destructive 的；
  - 读完之后要把值重新写回；
  - 即使不读，内容也要定期 refresh。
- 这就是 DRAM 比 SRAM 慢、却更便宜更高密度的重要原因。

## 模块 20：不同存储技术的比较
- 课件对 flip-flops、SRAM、DRAM 做了系统对比：
  - speed
  - cost per bit
  - transistor / cell complexity
  - whether refresh is needed
- 记忆方法不是孤立背表，而是把它们放回 hierarchy：
  - registers 常用 flip-flops；
  - caches 常用 SRAM；
  - main memory 常用 DRAM。

## 模块 21：本讲收束
- 这套课件其实把两层问题连接起来了：
  - 高层：为什么程序受益于 cache 和 hierarchy；
  - 低层：这些不同层的 bit 究竟如何被硬件保存。
- 从考试角度，既要会算 cache 的基本映射与 hit/miss，也要能解释 SRAM 与 DRAM 的结构差异与工程取舍。
