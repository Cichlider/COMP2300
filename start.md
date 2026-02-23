# COMP2300/6300/ENGN2219 课程常见问题解答 (FAQ)

## 1. 开课准备 (Getting Started)

### 入门清单
开始这门课程前，请确保完成以下事项：
* 确保你能登录本课程的 **Ed 论坛**（如果不能，请尽快联系老师）。
* 仔细阅读课程的 **Policies（政策）页面**。
* 浏览本 FAQ 页面，了解重要问题。
* 在 MyTimetable 上注册一个 Lab（实验课）班级。
* 参加第一周的 Lecture（讲座）和 Lab，了解课程详情。

### 课程教材与资源
* **教材**：《Digital Design and Computer Architecture》。
* **书籍与链接**页面提供了许多优质的在线资源。

### 其他支持
* **选课许可 (Permission Code)**：如需许可码，请在指定页面填写申请表，**不要**直接给课程负责人发邮件。
* **教育访问计划 (EAP)**：如果你有 EAP 或其他影响课程访问的问题，可以在论坛提问或发邮件给课程负责人，我们会提供相应调整。
* **Office Hours（答疑时间）**：目前暂无。Lab 是获取面对面一对一帮助的最佳途径。

---

## 2. 论坛、讲座与实验课 (Forum, Lectures & Labs)

### 论坛 (Ed)
本课程使用 Ed 作为论坛。如果无法访问，请在 Lab 时间告诉助教，或尽快发送邮件联系。

### 实验课 (Labs)
* **第一周有 Lab 吗？** 没有。
* **Lab 注册与更改**：通过 MyTimetable 注册。前 4 周内可自行更改；4 周后更改较困难。
* **Lab 满员怎么办？** 受限于教室容量，如果满员，建议使用 MyTimetable 的候补 (waitlisting) 功能。前几周通常会有学生退课或换班。
* **可以去别的班级吗？** 只要有空位就可以去，但助教会优先帮助本班的注册学生。
* **Lab 算成绩吗？** 不直接计分，但对理解课程和完成作业**至关重要**。很多作业都是基于 Lab 内容构建的。
* **联系助教**：可以通过论坛私信助教。遇到无法与助教沟通的问题，请直接联系课程负责人。

---

## 3. 作业与评分 (Assignments & Marking)

### 提交规则
* **只能在 GitLab 提交**：无论你本地文件长什么样，**我们在 GitLab 服务器上 `main` 分支的最后一次 commit 就是你的最终提交**。
* **不要公开代码**：严禁在论坛上公开你的作业代码或电路图，也不能直接把代码发给老师/助教。有问题请在论坛提问，我们会引导你。
* **发现 Bug/Typo**：如果在批改时发现代码有 Bug，助教会进行基础的调试，但你的分数会因不符合要求而受影响。
* **语言与工具链**：必须使用作业要求的语言和指定的工具链（比如 Digital 中的作业必须用 Digital 批改）。使用其他工具概不负责且没有分数。

### 成绩与反馈
* **人工批改**：作业通常由助教人工批改，并提供书面反馈。
* **发布时间**：通常在截止日期后三周内发布。
* **查看反馈**：成绩和反馈将推送到你 GitLab 仓库中名为 `main-[assignment_name]-feedback` 的新分支。根目录下会有一个 `feedback.md` 文件。
* **对成绩有异议**：我们批改量很大，尽量保证公平。如果不理解扣分原因，可以在论坛提问。如果需要重新评分，请参考课程的“申诉政策 (Appeals Policy)”。

---

## 4. Git、GitLab 与 CI (持续集成)

### 什么是 Git 与 GitLab？
* **Git**：版本控制工具，用于给你的文件拍“快照”并追踪更改。
* **GitLab**：ANU 的代码托管服务器网站，用于存储你的 Git 仓库并供我们批改。

### 持续集成 (GitLab CI)
* **什么是 CI？** 每次你推送 (push) 代码到 GitLab，服务器会自动运行一系列检查（称为 CI jobs），规则写在根目录的 `.gitlab-ci.yml` 文件中（**严禁擅自修改此文件**，否则按学术不端处理）。
* **CI 检查什么？** 检查格式（如原创声明是否正确）、是否包含设计文档、代码能否编译、测试能否通过等。
* **Pipeline 失败怎么办？** 刚开始做作业时失败很正常。点进去查看失败原因并修复。这不代表作业提交失败，只是提醒你还有基础要求未达标。
* **运行时间**：高峰期（如截止日期前）CI 任务会排队，请耐心等待。**CI 运行时间不影响你的提交时间**（以推送到服务器的时间为准）。
* **关闭失败邮件通知**：在 GitLab 网站 -> Profile -> Preferences -> Notifications -> 选择 Custom -> 取消勾选 "Failed pipeline"。

### 常见 Git 问题
* **免密操作**：强烈建议配置 **SSH Keys**，这样 `git push` 时就不需要每次都输入密码了。
* **克隆错了模板库**：修改 Git 的 remote 即可。
* **弄坏了本地仓库**：如果本地没有重要代码，最简单的办法是备份后删除文件夹，重新从 GitLab 克隆 (clone) 一份。

---

## 5. 学术诚信与原创声明 (Academic Integrity & SoO)

### 学术诚信规则
* **可以使用别人的代码吗？** 可以（除非作业明确禁止），但**必须在原创声明中引用**，否则即为学术不端（抄袭）。就算你修改了别人的代码，也必须标明出处。
* **查重**：所有提交都会经过查重软件检测。
* **涉嫌学术不端怎么办？** 如果被调查，你会收到邮件。如果真的使用了未标记的代码，最好的做法是**坦白承认并道歉**；如果是你自己的代码，请准备好解释为什么你的代码和别人高度相似。

### 原创声明 (Statement of Originality, SoO)
每个作业必须提交一个名为 `statement-of-originality.md` 的 Markdown 文件。
* **强制要求**：必须阅读并理解其含义，**签名（填入你的名字和 UID）**。这是通过 CI 检查的硬性条件。
* **必须列出的内容**：
  * 与同学的任何合作与讨论。
  * 来自同学、Lab、互联网、StackOverflow 等任何地方的代码。
  * 任何非你原创的资产（图片、音频、数据等）。
* **如果没有借用任何东西**：签上你的名字，并**删除模板里的示例引用 (example citation)**。

### 示例 SoO 格式：
```yaml
---
declaration: >-
  I declare that everything I have submitted in this assignment is entirely my
  own work except for the references listed below
# 在这里签上你的名字和 UID
name: Monty Goatee
uid: u1234567
# 在下面列出带有编号的引用（用你自己的引用替换示例）
# 千万不要删除上下的 "---"！
---

## Code References
- [https://stackoverflow.com/questions/30086526/if-else-or-statement-in-assembly](https://stackoverflow.com/questions/30086526/if-else-or-statement-in-assembly)
  - labelX 处的 if/else 语句参考了 StackOverflow 上的回答

## Collaborations
- Student: Patrick Polyvinyl
  - Pat 和我讨论了处理信号值溢出的策略，但我们没有看过彼此的代码。