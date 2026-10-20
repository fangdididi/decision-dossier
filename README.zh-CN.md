[English](README.md) | **简体中文**

# 决策档案（Decision Dossier）

> 一个智能体 skill：把“我该不该……？”变成一页可交互的决策档案——权重写明、每个分数标注证据等级、成本分开记账、事前验尸、翻转点压力测试，最后给出带警戒线的结论。

让 AI 智能体帮你做选择，通常会得到一个很笃定的答案和一串优缺点。你看不到它给什么加了权重，哪些是事实、哪些是猜测，也看不出这个选择到底有多接近。**决策档案**给智能体一套有纪律的方法和一个小工具，把回答变成一个离线 HTML 页面：两分钟能看完，还能自己验证——拖动一个权重，看第一名会不会换人。

技术选择（框架、数据库、CI、自研还是采购、供应商）和个人选择（offer、住房、理财、升学）都适用，支持中英文。

## 功能特点

- **八步方法**（见 `SKILL.zh-CN.md`）：说清问题、列选项、过门槛、定权重、带证据打分、分开记账、挑战领先者、写结论。
- **区分双向门和单向门**：可撤回的选择轻量分析、快速试验；代价高的选择全套严格分析。
- **硬性门槛**：把选项移出排名，但不把它藏起来。
- **每个分数都有证据等级**：M（实测）、E（估算）、J（判断），可选 low/high 区间。
- **分开记账**：钱、时间、风险各记各的，绝不揉成一个数。
- **内置挑战**：三个局外视角，加上带早期信号和应对办法的事前验尸。
- **压力测试**：每个标准的翻转点、被全面压制的选项、前两名“最坏 vs 最好”的对比。
- **带警戒线的结论**，外加“什么情况会让我改主意”和复盘日期。
- **可交互 HTML**：权重滑块实时重排、显示结论是否稳定、支持深浅色模式、不发任何网络请求。
- **零依赖**：一个只用标准库的 Python 3.8+ 脚本。
- **双语**：页面标签可选英文或中文（`"lang": "zh-CN"`）。

## 安装

把仓库克隆到智能体的 skills 目录，文件夹名用 `decision-dossier`。

**Claude Code**

```bash
# 个人级（所有项目可用）
git clone https://github.com/fangdididi/decision-dossier ~/.claude/skills/decision-dossier
# 或只给某个项目
git clone https://github.com/fangdididi/decision-dossier .claude/skills/decision-dossier
```

**OpenAI Codex CLI**

```bash
git clone https://github.com/fangdididi/decision-dossier ~/.codex/skills/decision-dossier
```

**Cursor、Gemini CLI、OpenCode 等其他智能体**

如果有 skills 目录（例如 `.cursor/skills/` 或 `.agents/skills/`），克隆进去即可。没有 skill 机制的智能体，在 `AGENTS.md`、`CLAUDE.md` 或规则文件里加一行：

```text
在多个选项之间做决定时，按 ./decision-dossier/SKILL.zh-CN.md 的方法执行，并用其中的脚本生成档案。
```

环境要求：辅助脚本需要 Python 3.8 或更高版本；方法本身不依赖脚本也能用。

## 使用方法

直接提一个决策问题即可。出现“该不该”“值不值”“要不要”“哪个好”“怎么选”“帮我决定”，或英文 “should I”“is it worth”“help me decide” 等说法时，skill 会被触发：

```text
远程创业公司的 offer 和还没下来的大厂 offer，我该接哪个？具体情况是……
我们 8 个人的团队要选 CI 方案，Jenkins 老出问题，帮我决定一下。
```

智能体只会问缺的信息，然后写一份 JSON 规格、做压力测试，最后回复一段简短总结和 HTML 档案的路径。

也可以直接运行工具：

```bash
python3 scripts/dossier.py check  examples/ci-migration.zh-CN.json   # 校验并给出提醒
python3 scripts/dossier.py stress examples/ci-migration.zh-CN.json   # 排名、翻转点、压制关系
python3 scripts/dossier.py build  examples/ci-migration.zh-CN.json -o ci.html
```

## 示例

CI 迁移示例的 `stress` 输出：

```text
Ranking (weighted, 1-5 scale):
  1. 托管编排 + 自建 Runner                 3.80
  2. 先不动，半年后再议                        3.20
  3. 继续用 Jenkins，花两周整修                3.00
Excluded by gates: 全部迁到托管 CI
Flip points:
  upkeep: 30 -> 4 makes 继续用 Jenkins，花两周整修 win
  cost: 20 -> 51 makes 先不动，半年后再议 win
  migration: 20 -> 39 makes 先不动，半年后再议 win
Worst case for 托管编排 + 自建 Runner (3.30) vs best case for 先不动，半年后再议 (3.20): lead holds
```

怎么读：混合方案领先，而且即使按最坏情况算也仍然胜出；只有在“费用”或“迁移风险”被看得极重时才会输给“先不动”。这是双向门，所以结论是先迁 3 个仓库试点两周。

| 示例 | 语言 | 类型 | 可逆性 |
|---|---|---|---|
| `examples/job-offer.json` → `.html` | 英文 | 个人职业选择 | 单向门 |
| `examples/ci-migration.zh-CN.json` → `.html` | 中文 | 团队工具选择 | 双向门 |

用任意浏览器打开 `.html` 文件，就能看到完整的档案。

## 文件结构

```text
decision-dossier/
├── SKILL.md                        # skill 本体（英文），由智能体加载
├── SKILL.zh-CN.md                  # skill 本体（中文）
├── README.md                       # 英文说明
├── README.zh-CN.md                 # 本文件
├── LICENSE                         # MIT
├── scripts/
│   └── dossier.py                  # check / stress / build
└── examples/
    ├── job-offer.json              # 英文示例规格
    ├── job-offer.html              # 生成的档案
    ├── ci-migration.zh-CN.json     # 中文示例规格
    └── ci-migration.zh-CN.html     # 生成的档案
```

## 原则

1. 先定权重，再打分。
2. 每个分数都说明是怎么知道的。
3. 不同的代价放在不同的列里。
4. 势均力敌就如实说势均力敌。
5. 每个结论都附带一个发现“它已经不对了”的办法。

## 许可证

[MIT](LICENSE) © 2026 fangdididi
