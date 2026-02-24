# COMP2300/6300/ENGN2219 README（S1 2026）

> Last updated: 2026-02-24 (Canberra Time)
>
> 本页根据你提供的课程信息整理；课程官网部分页面仍在更新中（Under Construction），请持续关注官方最新通知。

## 1. 课程核心内容（What this course is about）

这门课的目标是让你从底层理解“计算机如何运行”，分为两部分：

- 前半：硬件视角。使用 `Digital` 逻辑电路模拟器，从逻辑门与连线开始逐步搭建 CPU。
- 后半：软硬结合。学习 ARM 汇编语言，理解高级语言代码如何被翻译为底层指令并执行。

## 2. Assessment Scheme（已更正）

所有时间均为 Canberra Time。

| Assessment Item | Release Date | Due Date | Weighting |
| --- | --- | --- | --- |
| Assignment 1 | Week 6 | Week 9 | 20% |
| Midsemester Exam | 20 April 2026 (Week 7) | - | 30% |
| Final Exam | TBD | - | 50% |

### 更正说明（非常重要）

- 关于“有没有期中考试”：有。
- 已由课程 staff 在 Ed 论坛确认：`Midsemester Exam 在 Week 7，日期为 2026-04-20`。
- 课程官网 assessments 页面仍可能显示往年信息，不能作为本学期唯一依据。

## 3. 你最关心的问题（Assignment / Mid / Final / Lab）

## 3.1 Assignment 是什么？做什么？怎么做？用到什么知识点？

已确认：

- 本学期至少有 `Assignment 1`，权重 20%，Week 6 发布，Week 9 截止。
- 提交方式是 GitLab：`main` 分支截止前最后一次 commit 作为最终提交。
- 必须遵守作业工具链要求（例如指定必须用 Digital，就按 Digital 提交与批改）。

通常会做的事（按课程结构推断）：

- 与 Digital 电路设计和/或 ARM 汇编相关的实现任务。
- 需要把 lab 中练习过的方法整合成完整可评估的产出。

做法建议：

- 先完整过当周 lab page，再开始作业。
- 第一时间跑 CI，尽早修复格式、声明、编译等基础问题。
- 每天小步提交，避免最后一天堆积风险。

核心知识点（高频）：

- 组合逻辑与时序逻辑、寄存器与数据通路、控制信号。
- 指令执行流程、ARM 基本指令、分支与内存访问。
- 调试方法、测试用例设计、Git/GitLab 基础操作。

## 3.2 Midsemester 考什么？怎么做？on-paper 还是机考？

已确认：

- 有 Midsemester Exam，时间是 `2026-04-20`，权重 30%。

目前未公开（以官方发布为准）：

- 具体考试形式（纸笔 / 机考）。
- 题型分布与覆盖范围细则。

准备建议：

- 以 Week 1 到 Week 6/7 的 lecture + lab 核心知识为主线复习。
- 针对 Digital 与 ARM 两部分都做“可手写/可推导”的练习。
- 重点训练：给定电路或指令序列，能解释执行过程与结果。

## 3.3 Final 考什么？怎么做？on-paper 还是机考？

已确认：

- Final Exam 权重 50%。
- 时间目前 `TBD`。

目前未公开（以官方发布为准）：

- 具体日期与时段。
- 考试形式（纸笔 / 机考）。
- 题型细则与权重拆分。

准备建议：

- 从学期中后段开始做“累计复习”，不要只看期末前两周。
- 把 lab/assignment 中犯过的错误整理成错题本，期末前回刷。

## 3.4 Lab 是什么内容？是否记分？

已确认：

- Lab 为线下进行（in-person），Week 2 开始，Week 1 无 lab。
- 需要在 MyTimetable 注册 lab。
- Lab 对作业完成非常关键。

关于“是否记分”：

- 你提供的信息中有两种口径：
- 口径 A（FAQ 说法）：Lab 不直接计分。
- 口径 B（Indicative Assessment 说法）：`Labs and Quizzes (10%)`。

因此当前结论：

- 课程存在“可能与 lab/quiz 相关的 10% 比例”。
- 但“纯 lab 出勤是否单独计分”未被一致确认。
- 请以本学期正式 Assessment 页面和 Ed 置顶公告为最终标准。

## 4. 开课准备清单（Getting Started）

- 确认可登录 Ed 论坛。
- 阅读课程 Policies 页面。
- 在 MyTimetable 注册一个 lab。
- 参加第一周 lecture；lab 从第二周开始。
- 浏览 FAQ 与课程链接资源（含教材）。

## 5. GitLab、CI 与提交流程（必须遵守）

- 只认 GitLab 提交：截止时服务器 `main` 分支最后 commit 为准。
- 不要公开作业代码或电路图。
- 不要擅自改 `.gitlab-ci.yml`。
- CI 失败不等于提交失败，但代表你还有基础要求没满足。

## 6. 学术诚信与 SoO

每次作业必须包含 `statement-of-originality.md`：

- 填写姓名与 UID（签名）。
- 列出所有非原创内容来源（代码、素材、讨论协作）。
- 没有引用也要签名，并删除模板示例引用。

未正确声明引用会被视为学术不端风险。

## 7. 目前仍待官方发布的信息

- Assignment 详细题目说明、rubric、具体提交格式。
- Midsemester / Final 的确切考试形式与题型。
- Final 的具体考试日期。
- Lab pages 的完整周次内容与 quiz 细节（如适用）。

