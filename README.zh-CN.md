# session-lessons

[English](README.md) | 简体中文

一个 Claude Code skill：把会话中的**教训**（约束未来行为的错误与规矩）沉淀进项目文档，让 AI 后续会话不再重复犯错。

## 解决什么问题

- AI 在一个项目里犯过的错，换个会话又犯一遍
- 教训散落在对话里，没有落进任何「生效位置」
- 复盘结论只停留在口头，下个会话全部丢失

## 工作流程

```
盘点会话 → 列候选教训（编号）
    ↓
用户勾选 ←────── 不勾不写
    ↓
写 LESSONS.md（固定格式条目）
    + AGENTS.md/CLAUDE.md 加指针（检测式，无则跳过）
    + 同步相关 rules/strategy/docs 正文（逐条列目标文件与改动，一并勾选）
    ↓
协作习惯类教训双写内建记忆；回看台账，≥3 次的提议升级
```

## 条目格式

```markdown
### 2026-11-15｜发布正文不得残留 markdown 符号
- 触发：SH024 正文带 22 个反引号 + 20 个星号上线
- 根因：配文源文件是 markdown，回填时原样带入
- 规矩：配文一律纯文本
- 状态：已升级（→ preflight.js 硬拦截）
```

## 设计原则（四条铁律）

1. **永远提案-审批**：AI 列候选教训 → 用户勾选 → 才落盘。任何写入（台账、指针、记忆）都不跳过这道关
2. **单一真相源**：规矩正文写在它生效的文件（rules、主约束文件、脚本门禁），台账 `LESSONS.md` 只放一行指针。文档不抄代码里的数字——抄了必漂移
3. **升级纪律**：同一教训重复出现 3 次 → 提议升级为硬规则，写入项目主约束文件（AGENTS.md/CLAUDE.md 或 rules 结构），台账条目标「已升级」留指针
4. **台账轻薄**：只追加不改历史；被硬规则完全覆盖的条目定期清理

## 安装

**方式一：npx skills（推荐）**

```bash
npx skills add chenxuhl/session-lessons -g
```

**方式二：git clone + 拷贝**

```bash
git clone https://github.com/chenxuhl/session-lessons.git
mkdir -p ~/.claude/skills/session-lessons
cp session-lessons/SKILL.md ~/.claude/skills/session-lessons/SKILL.md
```

**方式三：手动复制** `SKILL.md` 到 `~/.claude/skills/session-lessons/SKILL.md`

## 使用

- 说「沉淀教训」「复盘教训」触发；AI 会先列候选清单等你勾选
- 建议时机：一次发布/重大变更完成后、事故复盘后
- 台账语言跟随项目现有文档语言

## 许可

MIT
