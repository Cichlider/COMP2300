# SetX Agent Workflow Instructions

## 目标
将任意一周课件 `setX.pdf` 统一处理为三类产物：
- `raw`：逐页原始提取文本（不删减）
- `notes`：中文整理与可考知识点清单
- `exam`：英文 LaTeX 试卷（问卷+答卷）及 PDF

本流程要求可直接复用于下一周课件，只需替换 `setX` 与 PDF 路径。

---

## 输入规范
- 输入 PDF：`wkYY/setX.pdf`
- `YY` 表示周次目录（如 `wk01`, `wk02`）
- `X` 表示 set 编号（如 `set1`, `set2`）

---

## 输出目录规范（固定）
在 `wkYY/` 下创建：

```text
wkYY/
  setX.pdf
  setX/
    raw/
      setX_full_extracted.txt
    notes/
      setX_中文完整复写.md
      setX_可考知识点清单.md
    exam/
      setX_exam_questions.tex
      setX_exam_solutions.tex
      setX_exam_questions.pdf
      setX_exam_solutions.pdf
```

---

## 执行步骤（Agent 必须按序执行）

### 1) 建目录
- 创建：`wkYY/setX/raw`、`wkYY/setX/notes`、`wkYY/setX/exam`

### 2) PDF 逐页文本提取（raw）
- 必须逐页提取，保留页边界。
- 输出文件：`wkYY/setX/raw/setX_full_extracted.txt`
- 要求：
  - 保留 `===== PAGE n =====` 页分隔格式
  - 只允许清理空字节（`\0`）等不可见破坏字符
  - 不允许删句、改写、重排

### 3) 中文完整复写（notes）
- 输出：`wkYY/setX/notes/setX_中文完整复写.md`
- 要求：
  - 以“模块化”组织（不要出现“第1页/第2页”标题）
  - 保留原翻译内容，不得删改已有翻译语义
  - 连贯内容可合并在同一模块
  - 纯图示/重复过渡页可简要概括
  - 知识点、考试相关、作业/lab相关内容需忠实翻译

### 4) 可考知识点清单（notes）
- 输出：`wkYY/setX/notes/setX_可考知识点清单.md`
- 只保留“可算、可考、作业/lab可能用到”的内容：
  - 数制与转换
  - 有符号/无符号、补码、溢出、符号扩展
  - 逻辑门、真值表、CMOS 结构
  - 位运算、位掩码、组合电路（如 MUX、Half Adder）
- 不写课程宣传、师资介绍、反馈感想类信息

### 5) 生成英文试卷（exam）
- 输出问卷：`wkYY/setX/exam/setX_exam_questions.tex`
- 输出答卷：`wkYY/setX/exam/setX_exam_solutions.tex`
- 规则：
  - 语言必须为英文
  - 概念型知识点可出选择题
  - 涉及计算的知识点必须出计算题
  - 计算题必须覆盖该 set 中所有可计算知识点
  - 答卷需给出结果与关键步骤

### 6) 渲染 PDF（exam）
- 编译出：
  - `setX_exam_questions.pdf`
  - `setX_exam_solutions.pdf`
- 推荐命令：
  - `latexmk -pdf -interaction=nonstopmode -halt-on-error <file>.tex`

### 7) 清理编译中间文件
- 仅保留：`.tex` 与 `.pdf`
- 推荐命令：
  - `latexmk -c setX_exam_questions.tex`
  - `latexmk -c setX_exam_solutions.tex`

---

## 质量验收清单（每次都要过）
- [ ] `raw/` 存在且含完整逐页文本
- [ ] `notes/` 两份 md 都存在
- [ ] 中文复写是模块化结构，无“第x页”标题
- [ ] 知识点清单仅含可考/可算/可用点
- [ ] `exam/` 包含问卷+答卷 `.tex` 与 `.pdf`
- [ ] 试卷中计算题覆盖全部可计算知识点
- [ ] `exam/` 无多余 LaTeX 中间文件

---

## 一键复用变量模板
将以下变量替换后执行同流程：

```text
WEEK_DIR = wkYY
SET_ID   = setX
PDF_PATH = wkYY/setX.pdf
```

文件命名自动套用：

```text
wkYY/setX/raw/setX_full_extracted.txt
wkYY/setX/notes/setX_中文完整复写.md
wkYY/setX/notes/setX_可考知识点清单.md
wkYY/setX/exam/setX_exam_questions.tex
wkYY/setX/exam/setX_exam_solutions.tex
wkYY/setX/exam/setX_exam_questions.pdf
wkYY/setX/exam/setX_exam_solutions.pdf
```

---

## 给 Agent 的标准任务指令（可直接复制）
请按 `SETX_AGENT_WORKFLOW` 执行 `setX.pdf` 全流程：
1. 建立 `raw/notes/exam` 目录结构  
2. 提取逐页原文到 `raw`  
3. 输出模块化中文完整复写到 `notes`  
4. 输出“可考知识点清单”到 `notes`  
5. 基于知识点生成英文 LaTeX 问卷+答卷到 `exam`  
6. 渲染 PDF 并清理中间文件，仅保留 tex/pdf  
7. 最终回报所有输出文件路径与覆盖的知识点范围。
