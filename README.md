**English** | [简体中文](README.zh-CN.md)

# Decision Dossier

> An agent skill that turns "should I…?" into a one-page, interactive decision dossier: explicit weights, evidence-graded scores, separate cost ledgers, a pre-mortem, a flip-point stress test and a verdict with tripwires.

Ask an AI agent to help you choose and you usually get a confident pick and a list of pros and cons. You cannot see what it weighted, which claims are facts and which are guesses, or how close the call really was. **Decision Dossier** gives the agent a disciplined method and a small tool, so the answer becomes a single offline HTML page you can read in two minutes and test yourself: drag a weight and watch whether the winner changes.

It works for technical choices (framework, database, CI, build vs buy, vendor) and personal ones (job offers, housing, money, study), in English and Chinese.

## Features

- **Eight-step method** in `SKILL.md`: frame, options, gates, weights, evidence-graded scores, ledgers, challenge, verdict.
- **Two-way vs one-way doors**: light analysis and quick experiments for reversible choices, full rigour for costly ones.
- **Must-pass gates** that remove an option from the ranking without hiding it.
- **Evidence grade on every score**: M (measured), E (estimated), J (judgment), plus optional low/high ranges.
- **Separate ledgers** for money, time and risk, never blended into one number.
- **Built-in challenge**: three outside lenses and a pre-mortem with early signals and mitigations.
- **Stress test**: flip points per criterion, dominated options, and worst-case vs best-case comparison of the top two.
- **Verdict with tripwires**, "what would change my mind" and a review date.
- **Interactive HTML**: live weight sliders, re-ranking, a stable / preference-sensitive indicator, light and dark mode, no network requests.
- **Zero dependencies**: one Python 3.8+ script using only the standard library.
- **Bilingual**: page labels in English or Chinese (`"lang": "zh-CN"`).

## Installation

Clone the repository into your agent's skills folder. The folder name should be `decision-dossier`.

**Claude Code**

```bash
# personal (all projects)
git clone https://github.com/fangdididi/decision-dossier ~/.claude/skills/decision-dossier
# or for one project
git clone https://github.com/fangdididi/decision-dossier .claude/skills/decision-dossier
```

**OpenAI Codex CLI**

```bash
git clone https://github.com/fangdididi/decision-dossier ~/.codex/skills/decision-dossier
```

**Cursor, Gemini CLI, OpenCode and other agents**

Clone into the agent's skills directory if it has one (for example `.cursor/skills/` or `.agents/skills/`). If your agent has no skill system, add a line to `AGENTS.md`, `CLAUDE.md` or your rules file:

```text
For decisions between options, follow ./decision-dossier/SKILL.md and build the dossier with its script.
```

Requirement: Python 3.8 or newer for the helper script. The method itself works without it.

## Usage

Just ask a decision question. The skill activates on phrases like "should I", "is it worth", "which one", "help me decide", "值不值", "要不要", "怎么选":

```text
Should I accept the remote startup offer or wait for the big company's offer? Here are the details…
We need to pick a CI setup for an 8-person team. Jenkins keeps breaking. Help me decide.
```

The agent asks only for missing facts, writes a JSON spec, stress-tests it and replies with a short summary plus the path to the HTML dossier.

You can also run the tool directly:

```bash
python3 scripts/dossier.py check  examples/job-offer.json   # validate and warn
python3 scripts/dossier.py stress examples/job-offer.json   # ranking, flip points, dominance
python3 scripts/dossier.py build  examples/job-offer.json -o job-offer.html
```

## Examples

Example output of `stress` for the job-offer spec:

```text
Ranking (weighted, 1-5 scale):
  1. Join the startup                 4.00
  2. Re-interview at the big company  3.55
  3. Stay where I am                  2.95
Excluded by gates: Go freelance
Flip points:
  growth: 30 -> 9 makes Re-interview at the big company win
  money: 25 -> 49 makes Re-interview at the big company win
  stability: 25 -> 39 makes Re-interview at the big company win
  life: 20 -> 5 makes Re-interview at the big company win
Worst case for Join the startup (3.45) vs best case for Re-interview at the big company (3.80): lead does NOT hold
```

Reading it: the startup leads, but the lead rests on uncertain stability and growth scores, so the verdict asks for two conversations with former employees and sets a runway tripwire.

| Example | Language | Type | Door |
|---|---|---|---|
| `examples/job-offer.json` → `.html` | English | Personal career choice | One-way |
| `examples/ci-migration.zh-CN.json` → `.html` | Chinese | Team tooling choice | Two-way |

Open the `.html` files in any browser to see the finished dossiers.

## File structure

```text
decision-dossier/
├── SKILL.md                        # the skill (English), loaded by the agent
├── SKILL.zh-CN.md                  # the skill (Chinese)
├── README.md                       # this file
├── README.zh-CN.md                 # Chinese README
├── LICENSE                         # MIT
├── scripts/
│   └── dossier.py                  # check / stress / build
└── examples/
    ├── job-offer.json              # English example spec
    ├── job-offer.html              # its dossier
    ├── ci-migration.zh-CN.json     # Chinese example spec
    └── ci-migration.zh-CN.html     # its dossier
```

## Principles

1. Weights before scores.
2. Every score says how it is known.
3. Different costs stay in different columns.
4. A close call is reported as a close call.
5. Every verdict comes with a way to notice it has gone wrong.

## License

[MIT](LICENSE) © 2026 fangdididi
