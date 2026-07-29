# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库性质

这是一个**内容型仓库**，不是可构建的应用程序。它收录了 100+ 个 AI「人格蒸馏」Skill（基于 [nuwa-skill / 女娲造人术](https://github.com/alchaincyf/nuwa-skill) 方法论），目标是把名人/作家/自媒体大V/导演的思维与表达风格封装成可导入 Claude Code / Cursor / OpenClaw / StepClaw 的 Agent Skill。

- 全仓库**没有构建系统、没有 lint、没有 CI**。绝大多数内容是 Markdown。
- **唯一含可执行代码的模块是 `tianya-gods-framework/`**（Python 多 Agent 编排器 + bash 脚本）。涉及命令、测试、依赖时几乎都指向这里。
- 用户与全部内容均为**简体中文**，新增/修改文档请用中文。

## 常用命令

仓库级命令不存在；以下命令均在 `tianya-gods-framework/` 目录下执行（bash 环境，Windows 下需 Git Bash / WSL）：

```bash
# 安装（复制到 ~/.stepclaw/skills 并建立命令/符号链接）
bash install.sh

# 安装完整性自检（校验文件/目录/可执行权限/Python 语法/config.json）
bash test.sh

# 运行多 Agent 编排器
python3 scripts/team-orchestrator.py "我现在该不该买房？"
tianya-gods "你的问题"        # install.sh 安装后的全局命令

# 单独校验（test.sh 内部即调用这两条）
python3 -m py_compile scripts/team-orchestrator.py
python3 -c "import json; json.load(open('config.json'))"
```

注意：Python 脚本文件名是连字符 `team-orchestrator.py`，无法用模块名直接 import；README 中 `from scripts.team_orchestrator import ...` 的 API 用法与文件名不一致，实际以脚本直接运行为准。

## 整体架构

### 两类 Skill 原型

1. **单人格 Skill（绝大多数）** —— 一个文件夹 = 一个人格，遵循 nuwa-skill 固定模板。分布在：
   - `Novelists/`：作家（liucixin、moyan、yuhua、zhangailing 等，每人一个 `*-skill/` 或 `*-perspective/` 目录）
   - `zimeiti/`：自媒体大V（baoyu、lijigang、liangziwei 等）
   - `EQ/`：EQ 情商训练、SEL 社会化训练（结构略不同，见下）

2. **多 Agent 编排系统（2 个）** —— 一个 `SKILL.md` 充当协调者，再调度众多子人格文件：
   - `DirectorAgents-1/`：31 位导演。`SKILL.md` 是 ClawTeam 编排器（定义顺序链/辩论投票/主席团三种协作模式），`directors/*.md` 是各导演的深度风格分析（**不是** nuwa 模板，而是散文式影评分析）。
   - `tianya-gods-framework/`：20 位天涯大神。唯一真正用 Python 代码实现并行编排的模块；`gods/*.md` 是可独立使用的单人格 Skill。

### 单人格 Skill 的固定文件结构

```
某人-skill/
├── SKILL.md                  # 主文件（YAML frontmatter + 固定章节）
└── references/research/      # 调研底稿，6 个固定编号文件
    ├── 01-writings.md        # 著作调研
    ├── 02-conversations.md   # 对话/访谈
    ├── 03-expression-dna.md  # 表达 DNA
    ├── 04-external-views.md  # 他者视角
    ├── 05-decisions.md       # 决策记录
    └── 06-timeline.md        # 时间线
```

`SKILL.md` 的 YAML frontmatter 字段：`name`、`description`（内含**触发词**）、`version`、`type`（`skill` 或 `perspective`）、`author`、`created`。正文按固定章节组织：身份卡 → 回答工作流（Agentic Protocol，含 Step 1 问题分类表）→ 心智模型（通常 5 个）→ 决策启发式 → 表达 DNA → 时间线 → 价值观与反模式 → 智识谱系 → 诚实边界 → 调研来源。**修改/新建单人格 Skill 时务必沿用这套章节顺序与命名**，保持一致性。

`EQ/` 子目录是变体：references 用 `01-core-research.md`/`02-training-methods.md` 等主题命名而非作家六件套，且每个 Skill 配一份「完整指南」长文。

### 总目录文件

- `persona-skills.md`：全网 100+ 外部人格 Skill 的**分类清单**（表格 + GitHub 链接），本身不是 Skill，而是资源索引。新增外部资源收录于此。
- `README.md` / `README-EN.md`：仓库主页与七大核心板块说明。

## 创建新人格 Skill（蒸馏工作流）

新 Skill 由 nuwa-skill 驱动生成，而非手写：

```bash
npx skills add alchaincyf/nuwa-skill   # 1. 安装女娲 skill
# 2. 在 OpenClaw/StepClaw 中指令蒸馏，例如：
#    "用 nuwa-skill 蒸馏作家莫言，先查其全部资料与书写风格，构造一个最像他的"
# 3. 运行环境（StepClaw）逐步执行并反馈
# 4. 将生成的文件夹（含 SKILL.md 与 references/research/）保存到对应分类目录
```

人工新增/补全时，对照 `Novelists/` 下已有 Skill 复制其结构与章节，确保六件套调研文件齐全、触发词写入 frontmatter 的 `description`。

## 约定与注意事项

- License 为 MIT；二次创作需保留仓库出处。SKILL.md 末尾通常保留 `> 本Skill由 女娲·Skill造人术 生成` 署名。
- 「触发词」是 Skill 被调用的关键，必须写在 frontmatter `description` 中（如「某某视角」「像某某那样写」）。
- 子模块各自带 README（`Novelists/README.md`、`zimeiti/README.md`、`DirectorAgents-1/README.md`、`tianya-gods-framework/README.md`），改动某板块前先读对应 README。
