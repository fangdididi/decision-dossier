---
name: decision-dossier
description: Turn a hard choice into a decision dossier, a one-page interactive HTML file that shows the options, must-pass gates, weighted criteria with an evidence grade on every score, separate cost ledgers, a pre-mortem, a flip-point stress test and a verdict with tripwires. Use it whenever someone asks "should I…", "which one…", "is it worth it", "help me decide", compares jobs, tools, vendors, architectures, offers, cities or plans, or is stuck between options, including technical choices (framework, database, CI, build-vs-buy) and personal ones (career, money, housing, study).
---

# Decision Dossier

## What this skill is

Decision Dossier is a method plus a small tool for answering "what should I do?" questions honestly. Instead of a chat reply that picks a winner and lists pros and cons, you produce a **dossier**: a single self-contained HTML page the person can open, read in two minutes, and play with. Every number on it is traceable to a stated reason, and the page shows how fragile the conclusion is.

## The problem it solves

Advice from an agent usually fails in four quiet ways:

1. **Hidden weights.** The agent decides what matters most without saying so.
2. **Fake precision.** A guess and a measured fact look the same in the answer.
3. **Mixed currencies.** Money, time and risk get blended into one "overall" feeling, so a big risk can hide behind small savings.
4. **No exit plan.** The answer ends at the recommendation, with nothing that tells the person when the choice has stopped being right.

The dossier makes each of these visible: weights are explicit and adjustable, each score carries an evidence grade, costs stay in separate ledgers, and the verdict ends with tripwires and a review date.

## When to use it

Use it when any of these is true:

- The person is choosing between two or more real options.
- The question contains "should I", "is it worth", "which is better", "help me decide", "值不值", "要不要", "怎么选".
- Several factors pull in different directions (cost vs speed, safety vs growth).
- The choice is expensive or hard to undo.

Skip it for trivial or purely factual questions, and when the person only wants a quick opinion. Offer the dossier in one sentence instead of forcing it.

**Stop first, decide later.** If the question involves an emergency, immediate danger to someone's health or safety, thoughts of self-harm, or an active legal proceeding, do not build a dossier. Give the urgent next step (emergency number, crisis line, a lawyer) and only return to the method once the person is safe.

## How it works: eight steps

Work through the steps in order. Ask the person only for what you cannot find or reasonably infer; batch your questions into one message.

### 1. Frame the question
Write one sentence that names who decides, what is being decided and by when. Then classify it:
- **Two-way door**: easy to undo (a tool trial, a short lease). Keep the analysis light and favour a quick, cheap experiment.
- **One-way door**: costly to undo (a move, a hire, a large purchase, quitting). Do every step, including the pre-mortem.

### 2. List the options, including the boring ones
Aim for 3 to 5 concrete options. Always include a **status quo / do nothing / wait** option, and look for a **hybrid or staged** option (pilot first, split the difference, buy time). An option must be specific enough to score: "move to Berlin in March with the current employer" rather than "move abroad".

### 3. Apply must-pass gates
Gates are yes/no constraints that no score can compensate for: a budget ceiling, a legal requirement, "no relocation", "keeps six months of savings". Options that fail a gate stay visible on the page but drop out of the ranking. Keep gates few and truly non-negotiable.

### 4. Choose and weight criteria before scoring
Pick 3 to 7 criteria that do not overlap ("ROI" and "profit" are the same thing). Give them weights that sum to 100, **before** you look at how options score, so the weights cannot be bent toward a favourite. Write anchors for each criterion: what a 1, a 3 and a 5 look like in plain words.

### 5. Score with evidence grades
Score each option on each criterion from 1 to 5 using the anchors. Tag every score:
- **M (measured)**: from data, a quote, a contract, a benchmark, a trial run.
- **E (estimated)**: a reasoned estimate from partial data.
- **J (judgment)**: opinion or gut feel.

Add a short note for each score, and a `low`/`high` range when you are unsure. If more than half the scores are J, say so and suggest the one or two facts that would most improve the decision. Never invent numbers, statistics or sources; mark the gap instead.

### 6. Keep separate ledgers
Record what each option costs in its own currency: money, time, health or energy, risk (the worst plausible outcome). Do **not** convert these into one number. Seeing "+18% salary" next to "company may fold within 18 months" is the point.

### 7. Challenge the leader
- **Three lenses**: write a short view from a skeptic, from the person five years later, and from an outsider who knows them (or a new team member, for technical choices).
- **Pre-mortem** (required for one-way doors): assume the leading option failed a year from now. Write how it failed, the earliest warning signal, and a mitigation.
- **Stress test**: run `scripts/dossier.py stress` to see flip points (how far a weight must move before the winner changes), dominated options, and whether the leader still wins in its worst case against the runner-up's best case.

If a plausible weight change flips the winner, call the decision **preference-sensitive** and name the trade-off plainly. Do not tune weights until your favourite wins.

### 8. Write the verdict and build the page
State the choice and the reason in two or three sentences, then add:
- **Tripwires**: concrete, observable conditions that trigger a rethink ("runway below 9 months", "median build time above 8 minutes").
- **What would change my mind**: facts that, if learned, would reverse the verdict.
- **Review date**.

Then build the dossier:

```bash
python3 <skill-dir>/scripts/dossier.py check  decision.json
python3 <skill-dir>/scripts/dossier.py stress decision.json
python3 <skill-dir>/scripts/dossier.py build  decision.json -o decision.html
```

Reply with a short summary (choice, main reason, how robust it is, the first tripwire) and the path to the HTML file.

## The spec file

The tool reads one JSON file. Set `"lang": "zh-CN"` for Chinese labels. Minimal shape:

```json
{
  "title": "Rent or buy?",
  "question": "Should we buy the flat on Elm Street or keep renting for two more years?",
  "lang": "en",
  "reversibility": "one-way",
  "deadline": "2026-12-01",
  "gates": [{"id": "deposit", "label": "Leaves an emergency fund"}],
  "options": [
    {"id": "status-quo", "name": "Keep renting", "gates": {"deposit": true}},
    {"id": "buy", "name": "Buy the flat", "gates": {"deposit": true}}
  ],
  "criteria": [
    {"id": "cost", "label": "5-year cost", "weight": 50, "anchors": {"1": "Much more", "3": "Same", "5": "Much less"}},
    {"id": "freedom", "label": "Flexibility", "weight": 50}
  ],
  "scores": {
    "status-quo": {"cost": {"score": 3, "evidence": "M", "note": "Current rent"}, "freedom": {"score": 5, "evidence": "J"}},
    "buy": {"cost": {"score": 4, "evidence": "E", "low": 2, "high": 4}, "freedom": {"score": 2, "evidence": "J"}}
  },
  "ledgers": [{"id": "cash", "label": "Money"}],
  "costs": {"status-quo": {"cash": "rent rises ~4%/yr"}, "buy": {"cash": "deposit + fees"}},
  "lenses": [{"name": "The skeptic", "view": "…"}],
  "premortem": [{"option": "buy", "story": "…", "signal": "…", "mitigation": "…"}],
  "verdict": {"choice": "status-quo", "why": "…", "tripwires": ["…"], "what_would_change_my_mind": ["…"], "review_on": "2027-06-01"}
}
```

Full examples: `examples/job-offer.json` (English, personal, one-way) and `examples/ci-migration.zh-CN.json` (Chinese, technical, two-way). Each has a pre-built `.html` beside it.

## Examples

**"Should I take the startup offer?"** Options: stay, startup, freelance, re-interview elsewhere. Gate: keeps six months of savings, which removes freelance from the ranking while keeping it visible. Stress test shows the startup leads, but stability above weight 39 hands the win to the big company, so the verdict says "verify runway with two ex-employees" and sets a runway tripwire.

**"Postgres or a document database for the new service?"** Two-way door if the data layer is behind a repository interface, so the verdict is a one-week spike on the riskiest query, with a tripwire on p95 latency.

**"Which of these three laptops?"** Low stakes, two-way door: three criteria, quick scores, no pre-mortem, a page in five minutes.

## Tips

- Weights first, scores second. If you notice you want to change a weight after scoring, say so openly and record why.
- Fewer criteria beat more. Seven is a ceiling, not a target.
- A dominated option (no better anywhere, worse somewhere) can be dropped from the discussion, but keep it on the page so the person sees why.
- Ranges matter more than point scores for the top two options; spend your research there.
- For reversible choices, the best answer is often "run the cheapest test that would change the ranking".
- Write in the person's language; keep notes short enough to fit in a table cell.
- The script uses only the Python 3.8+ standard library and writes one offline HTML file with no external requests.
