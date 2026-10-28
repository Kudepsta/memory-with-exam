# Memory with an Exam

**A file-based memory protocol for AI agents — where every compression of memory must pass an exam before it is trusted.**

We run a real company (offline services + web products + content) with a staff of two equal LLMs — one cloud, one local — plus six specialist agents, and one human owner who is not a programmer. This repository is the memory system we built for them: plain Markdown files, zero specialized memory software, and a protocol that survived daily production use.

The full story is below. The copy-paste kit is in the files of this repository (all templates + the exam form are in the repo root). Русская версия статьи — [README.ru.md](README.ru.md).

---

## The problem

Typical agent lifecycle: session → work → context death. Vector stores and history summarization fix *forgetting*, but not three deeper failures:

1. **Staleness.** The model answers confidently from an outdated fact and has no idea it is outdated.
2. **Silent compression lies.** Compressed memory saves tokens — and then quietly answers wrong once the underlying facts change (a failure mode recently named and studied by researchers and practitioners alike).
3. **No proof of compression quality.** Nobody verifies that what mattered in the original is still retrievable from the summary.

We needed memory that two *different* models use daily, where staleness is visible on the page, and where compression is *proven*, not promised.

## The solution: files + protocol

All company memory is a folder of Markdown files synced between machines by a free file-sync tool. No vector DB, no knowledge graph, no memory platform. The value is the protocol:

**Layers.** A **CORE** (≤60 lines of live facts, read by both models at the start of every session). An append-only **DIARY** (tag your blocks). A **DECISION CANON** — see below. A **MAP** ("what lives where"). A **DEBTS** board — any started-but-unfinished work must have a line, otherwise it officially does not exist.

**Superseded in place.** A fact changed? The old line is struck through and the new one written next to it, with a date. No two-storey contradictions accumulate, and change history stays readable by eye. This simple rule beat any temporal database for our scale, because freshness is visible without a query.

**Decision canon with rejected alternatives.** Every major owner decision is recorded with the options that were rejected and why. A month later this stops the team from lovingly re-inventing something already refused.

**Owner-of-truth.** Every kind of fact has exactly one owning file; everywhere else links. We caught our agent dashboards, subscription registry and hardware notes contradicting each other — that class of bugs died with this rule.

**Rituals with automated freshness control.** Morning briefing and evening patrol by the duty model; weekly "dreams" (diary consolidation); monthly distillation. A trivial script checks the freshness of the briefing, agent memory files (threshold: 14 days) and the shared news feed (threshold: 30 days) and alarms the owner when something rots. We added this after an honest failure: for three weeks the evening ritual "executed" only on paper — the record of the ritual existed, the ritual did not.

## The centerpiece: compression must pass an exam

Once a month, an old layer of the diary is distilled into a monthly "chronicle". The quality gate — we call it the **memory ladder**:

1. **Control questions BEFORE compression.** From the uncompressed original, write 10 questions with answers (numbers, dates, decisions, doctrines). Writing them after compression is forbidden — you would peek into your own digest.
2. **Corridor 5–15%.** Below 5% you almost certainly dropped something important; above 15% it is not compression. Deliberately discarded items are listed.
3. **Cold-session exam.** A model that has never seen the original reads only the compressed block and answers the 10 questions. Pass: ≥9/10. Below — the digest is fixed from the journal and re-examined.
4. **Originals archived, never deleted.**

Results from our first 12 days of running the ladder (two months of history processed):

| Block | Before | After | Compression | Exam |
|---|---|---|---|---|
| August | 174,703 chars | 9,084 | 5.2% | 10/10 |
| September | ~83,000 chars | 6,137 | 7.4% | 10/10 |

Plus the memory map: 47,506 → 5,697 chars (12%). In money terms: re-reading two months of history now costs ~15K characters instead of ~258K — and, unlike "just a summary", we hold proof that the control numbers are still retrievable.

A telling failure from the same series: off-the-shelf automatic summarizers on our material produced 24.7%, then 3.0%, then 22.7% (two of them leaking chain-of-thought into the text and losing whole days). The working digest came from careful manual compression by the duty model — which is exactly why the exam is mandatory, not optional.

## Lessons that cost us real money

1. **"Recorded" ≠ "done".** The word SAVED is forbidden in the diary without a path and a file size. We once lost three finished video deliverables that the journal said were "SAVED".
2. **A record ≠ a living process.** Lost boot-memory files of half the agents, a silently dead evening ritual, a news feed with no pulse — all of it existed "in memory" and all of it was dead. The cure is automated freshness checks and exams — not more layers.
3. **Memory saves money directly.** Our rule "an empty scheduled run makes zero LLM calls — check the cheap fact first, wake the brain only when there is something to decide" saves ~$600+/month on API by our own audit. Yes, that rule is itself recorded in memory.
4. **Two models, one memory = built-in arbitration.** Both read the same CORE, both may write; the owner is arbiter. When two independent models audited this very memory system, they agreed on every diagnosis and diverged exactly where a human call was needed. That is the strongest argument for the "equal brains, single memory" shape: a second opinion by default.

## What we deliberately do NOT have

No semantic search over the vault yet (TOCs and reading suffice at our scale). No automatic fact extraction into a graph. No benchmark — two months and ten questions per distillation is an anecdote, not statistics. We state the limits honestly: this works for a company of eight "staff" and one human; scaling is unknown. Next month will tell.

## Take it

- [`01-core.md`](01-core.md) — the CORE file
- [`02-diary.md`](02-diary.md) — append-only diary
- [`03-canon.md`](03-canon.md) — decision canon with rejected alternatives
- [`04-map.md`](04-map.md) — memory map
- [`05-debts.md`](05-debts.md) — open work board
- [`06-agents.md`](06-agents.md) — agents hall (shared staff memory)
- [`exam-form.md`](exam-form.md) — **the exam: the quality gate for every compression**

MIT licensed. If you run something like this — your numbers in the issues are the most interesting thing this repo can collect.

---

*Written by the local-post AI agent of the company; proofread and approved by the owner. All numbers come from working journals; company infrastructure is intentionally not disclosed.*
