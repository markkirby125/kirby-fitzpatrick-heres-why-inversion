# Heres Why Inversion — Technical Operational Dispatcher

**Framework Author**: William Fitzpatrick (*Writer Science*)  
**Source Lecture**: [Writing Online Is Hard, Until Experts Do This](https://www.youtube.com/watch?v=j1JRSan9CYg)  
**Parent Collection**: [Master Collection](../../kirby-fitzpatrick-writers-collection/SKILL.md) | [Global Help](../../../kirby-help/SKILL.md)  

---

## 1. Cognitive Foundation: Verdict Before Journey

Every engineering artifact is written in the order the author solved the problem and consumed in the order the reader needs to use it. Those two orders are almost never the same. The author discovers the answer *last* — after the failed branch, the second failed branch, the profiling run, the stakeholder comment that reframed the ticket. The reader needs the answer *first*, because their only question is "what does this repository now do, and do I accept it?"

**Here's Why Inversion** flips the artifact without discarding the investigation. The verdict is emitted first, in the opening subject position; the hinge phrase (*here's why*, *because*) follows immediately; the rationale unrolls beneath it in descending order of decision weight. The chronological exploration log becomes raw material, never the published shape.

Five load-bearing definitions:

1. **Verdict** — the terminal state of the work, stated as a fact about the system, not as an activity the author performed. *"Retry backoff no longer resets per attempt"* is a verdict. *"Investigated the retry issue"* is a diary entry.
2. **Chronological Exploration Log (CEL)** — the un-inverted artifact: an append-only narrative of the search (*we tried X, then Y, then Z*). Its verdict, if it appears at all, is the last thing written and often absent entirely. A CEL is not dishonest; it is merely unread.
3. **The Hinge** — the explicit causal connector that binds verdict to warrant: *here's why*, *because*, *the reason*. The hinge is what separates inversion from assertion. A verdict with no hinge within three sentences is an opinion; reviewers treat it as one and ask the question the hinge was supposed to pre-empt.
4. **Inference Debt** — the number of abandoned branches the reader must hold in working memory to follow a piece of prose. A CEL charges the reader for the author's search cost. Verdict-first charges it once, in the *Rejected* block, at one line per branch.
5. **Time-to-Verdict (TTV)** — the number of sentences a competent reader must consume before they can state what changed. Target: **TTV = 1**. In the commit subject line, TTV = 0 — the verdict *is* the subject.

Why this binds harder in software than in essays: git is a verdict index, not a narrative. The commit subject is the only field that survives into `git log --oneline`, `git blame` headers, `git bisect` output, generated changelogs, release notes, and the triage view during an incident. A chronological subject line destroys the index for every future reader, including the same engineer at 03:00 with three candidates and no memory of the search.

```text
[ANTI-PATTERN: Chronological Exploration Log (CEL)]
  t0 ──────► t1 ──────► t2 ──────► t3 ──────► t4 ──────► t5   (discovery order)
  "looking     "tried      "X broke     "tried      "tuned      "turns out
   into the     raising      under        resetting   the         resetting
   flaky        the          load"        backoff"    jitter"     worked"
   retry"       backoff"

  reader walks the author's entire path
  TTV = 412 words        Inference debt = 4 abandoned branches still in RAM
  git log --oneline  ->  9 rows of "wip", "more retry stuff", "fixup"
  reviewer's next action: a question, not a review
Result: approval happens late, for social reasons; six months later the diff
        is readable and the intent is gone.

[HERES WHY INVERSION]
  T0  "Retry backoff stops resetting on every attempt."   <- terminal state
  T1  "Here's why:"                                       <- the hinge
  T2  three reasons, ordered by decision weight           <- rationale spine
  T3  Rejected: cap at 60s, jitter-only, vendor SDK knob  <- 1 line each
  T4  Evidence + blast radius + rollback                  <- appended, not led

  TTV = 1 sentence (6 words)        Inference debt = 0
  git log --oneline  ->  a navigable decision index
Result: the first line is auditable, so review time goes into the evidence.
```

**Inversion is an editing pass, not a drafting mode.** Exploration is generative and should stay permissive and out of order — notes, spikes, dead branches, half-formed verdicts. Publication is the inversion: find the verdict in the pile, hoist it to the top, keep the branches that carry adversarial value, delete the rest. Reversing this order — writing verdict-first *while* still exploring — produces confident prose about an unverified system, which is a far more expensive defect than a boring diary.

---

## 2. Core Transformation Protocols

1. **Lead with the terminal state.** The first sentence of every commit subject, PR title, review comment, and ADR states what the system now does or must do. Never open with the activity: *investigating*, *looking into*, *spiking*, *cleaning up*.

2. **Bind the verdict with a hinge.** Within three sentences, a `because` / *here's why* must connect the claim to its mechanism. An orphan verdict is an assertion; assertions get challenged and the challenge costs more words than the hinge would have.

3. **Order by decision weight, not discovery time.** Ask of each paragraph: *if the reader stopped here, would they still hold the right conclusion?* If not, the paragraph is above its own ceiling and must move down.

4. **Demote dead ends; never narrate them.** A rejected branch survives only when it carries adversarial information — when it pre-empts an alternative a competent reader would plausibly raise. One line each: `Option — reason rejected — evidence — condition that would revive it`.

5. **Delete the reveal.** *It turns out*, *turns out that*, *finally figured out* are the fossil record of a chronological draft. They announce that the writer just learned the thing; the reader's model has no history and gains nothing from the writer's.

6. **Preserve causality, not chronology.** Chronology answers *when I learned it*; causality answers *what makes it true*. Only the second transfers to a reader who inherits the system.

7. **Make the verdict falsifiable.** *Improved reliability*, *cleaned things up*, *made it safer* are moods, not verdicts. A verdict names an observable and its target: a mechanism, a boundary, a measurement, or a contract.

8. **One verdict per artifact unit.** A compound subject (`fix retries, refactor client, bump deps`) buries three decisions under one line and guarantees that at least two rationales are lost. Split the commit or pick the load-bearing verdict and list the rest as scope.

9. **Invert the artifact, never the investigation.** Scratch buffers, spikes, and `BUG-*` notes stay chronological forever. Inversion applies at the boundary where the work becomes an artifact other people read.

10. **Escalate the verdict, descend the rationale.** Write as an inverted pyramid: title → subject → first paragraph → body. Truncation at any level must still leave a usable decision, because that is exactly how the artifact will actually be read.

11. **Keep the warrant adjacent.** No blockquote, table, screenshot, or long code block between the verdict and its first reason. Any interruption converts the reader's flow into a question.

12. **State the negative space.** Verdict-first prose says what is not included, not yet verified, and deliberately unchanged. Silence reads as *verified*, which is the most expensive misreading in engineering artifacts.

### 2.1 Canonical slot order of an inverted artifact

| Slot | Content | Budget | Failure if missing |
|---|---|---|---|
| V — Verdict | Terminal state, one sentence, subject position | ≤ 20 words | Reader reverse-engineers intent from the diff |
| H — Hinge | `because` / *here's why* + the load-bearing reason | 1 clause | Verdict reads as unearned authority |
| M — Mechanism | The code path, flag, schema, or algorithm that makes H true | 1–3 sentences | Reader absorbs a claim with no transferable model |
| E — Evidence | Benchmark, test, command, metric, incident ID | Table or 1 line | Approval becomes social, not evidential |
| C — Consequence | Blast radius, rollback, expiry, not-included list | 3 lines | The first reviewer question is *"is this reversible?"* |
| R — Rejected | Dead branches that carry adversarial value | 1 line each | A reviewer proposes the rejected option as a finding |

### 2.2 Transformation table: anti-patterns and clean replacements

| Anti-Pattern (chronological) | What it costs the reader | Clean Replacement (verdict-first) |
|---|---|---|
| "Looked into the flaky retry, tried raising the backoff, that made it worse, then tried resetting it, which worked." | Walks three branches; the verdict arrives after 30 words | "Retry backoff no longer resets on each attempt (`retry.go:64`), so a transient 503 can no longer exhaust the retry budget. Here's why the cap alone failed: …" |
| "After evaluating Kafka, NATS, and SQS, we ended up going with SQS." | The verdict is a suffix; the comparison has no axis | "We adopted SQS: it is the only candidate that meets the 50 ms p99 budget at our fan-out. Rejected — Kafka (operational cost), NATS (no durable replay)." |
| "Investigating the latency regression in reconciliation." | Zero information; TTV = never | "Reconciliation issues one batched query per batch instead of one query per invoice." |
| "Turns out the tenant filter was being applied twice." | Reveal marker; no verdict | "Tenant filtering now lives solely in `TenantRepo`; the duplicate filter at `handler.ts:88` is deleted." |
| "WIP: retry stuff, more retry stuff, fixup." | The log index is unusable; bisect gives nothing | "Stop resetting retry backoff on each attempt" |
| "Explored using advisory locks for payouts, then tried a Redis lease, then settled on SKIP LOCKED." | Reader must simulate the whole search to find the choice | "Payout jobs claim work with `SELECT … FOR UPDATE SKIP LOCKED`. Here's why the Redis lease was rejected: it adds a second failure domain." |
| "Refactored for clarity and also fixed the bug and bumped the SDK." | Three verdicts collide; at least two rationales evaporate | "Fix decimal rounding in invoice totals" + separate commits for the refactor and the SDK bump |
| "Might have performance implications." | Unfalsifiable hedge passes as diligence | "Unverified: no load test. Expected +1 round trip (`tenant.ts:12`), owner: author, before merge." |
| "See the commits for details." | Commits state mechanics; intent has no home | One-sentence verdict in the PR body + the rationale the diff cannot express |

### 2.3 Forbidden exploration tokens

| Banned token | Why it fails | Required replacement |
|---|---|---|
| *explored*, *looked into*, *spiked*, *dug into* | Names an activity, not a system state | The terminal state, stated as fact |
| *tried X, then Y*, *first we… then we…* | Serializes the author's search into the reader's clock | `Rejected:` block, one line per branch |
| *ended up*, *eventually*, *after some digging* | Signals an appended verdict | Hoist the verdict to the subject position |
| *it turns out*, *turns out that* | Reveal marker; reader has no prior model to correct | State the fact and its mechanism |
| *started by*, *began with*, *to give context* | Pre-verdict runway | Verdict first; context after the hinge |
| *wanted to*, *was thinking about* | Author's intent as history | The decision, in the past tense |
| *some*, *a bit*, *roughly* (unmeasured) | Falsifiability erodes to nothing | Bound the quantity, or delete the claim |
| *and also cleaned up* | Second verdict smuggled into the first | Separate commit, separate verdict |

### 2.4 Failure diagnostics

| Symptom | Inversion diagnosis | Fix |
|---|---|---|
| "What does this PR actually do?" | Verdict absent; body opens on context or activity | Write the terminal state into subject and body line 1 |
| "Why did you choose this over X?" | Rejected block missing | One line per branch: option, reason, evidence, revival condition |
| Reviewer reads the diff to guess intent | Intent lived only in the search | Verdict + hinge above the first code block |
| Author answers in DM, patch never lands | Rationale has no durable home | Write the answer into the artifact, then re-read the subject line |
| `git log --oneline` cannot be skimmed | Chronological subjects | Rewrite subjects at squash/rebase; one verdict each |
| Commit body opens with *Turns out…* | Reveal marker survived editing | Delete the marker; the fact is the verdict |
| Thread becomes a terminology argument | Verdict was an assertion | Add the hinge and the mechanism in the same breath |
| ADR cited but nobody can say what was decided | Context-first ADR with a buried decision | Decision section first, drivers next, context last |

**Related dispatchers.** Carry the inverted verdict into a full proposal arc with the [3-Part Proposal Engine](../../kirby-fitzpatrick-3part-proposal-engine/SKILL.md); bind the verdict to evidence under an un-briefed reader's audit with [Cold Reader PR Auditor](../../kirby-fitzpatrick-cold-reader-pr-auditor/SKILL.md); keep the verdict reachable in a 15-second skim with the [Skim Test Outliner](../../kirby-fitzpatrick-skim-test-outliner/SKILL.md); strip hedges and zero-information intensifiers from the verdict with the [Lexical Anti-Bloat Filter](../../kirby-fitzpatrick-lexical-anti-bloat-filter/SKILL.md) and [Empty Verb Extractor](../../kirby-fitzpatrick-empty-verb-extractor/SKILL.md); announce the slot order before its parts with [Cathedral Taxonomy](../../kirby-fitzpatrick-cathedral-taxonomy/SKILL.md); calibrate the verdict across Uninformed, Indifferent, and Skeptical readers with the [3-Persona Stakeholder Framer](../../kirby-fitzpatrick-3persona-stakeholder-framer/SKILL.md).

---

## 3. Engineering Application Scenarios

### 3.1 Code Reviews — Verdict-First Comments and Verdict-First Responses

Two inversions apply. The reviewer's comment is an artifact with the same TTV budget as the code under review. The author's response to review is the highest-leverage inversion in the whole workflow, because a verdict-first reply collapses a five-message thread into one auditable message.

**Comment rule.** Lead with the demanded terminal state, then the hinge, then the consequence, then the falsifiable check. Never narrate the reading order (*"I opened handler.ts, then noticed…"*). The author needs the state you are asking for, not the path you walked.

**Before — the comment is itself a chronological log:**

> So I was looking at the query in handler and it seems like it bypasses the repo layer, which is why tenant scoping may not apply. I tried grepping and found a couple of places. Might be worth routing it through TenantRepo?

**After — verdict-first comment:**

```markdown
**Blocking — route this read through `TenantRepo`.**
`handler.ts:88` queries `orders` directly; every other read goes through `TenantRepo`
(`src/repo/tenant.ts:12`), so tenant filtering now exists in two places and the next
divergence drops it silently. Check: `grep -rn "from orders" src/orders/` → 1 hit outside
the repo layer today. Fix: `TenantRepo.findOrders()`, one commit, no behavior change.

**Non-blocking — the batched query may not be free.** Unverified on my side; I am asking
for the shape rather than asserting an outcome (`make bench-orders`, p95, median of 5).
```

**Author's response rule (the strong form of the inversion):** reply with the amended verdict, not the deliberation. The message *"You're right, I changed it — the query now goes through `TenantRepo.findOrders()` (commit `c4a91f2`)"* ends the loop. The message that opens with *"Initially I thought the repo layer wouldn't work because…"* restarts it, because the reviewer must now audit a search that no longer exists in the code.

**Thread acceptance rule.** Every resolved thread ends with a state, not a sentiment: what the code does now, in which commit, verified by which check. Approval that cannot be traced to a state is social approval wearing a review comment's clothes.

### 3.2 PR Descriptions — "We adopted Y because…" Replacing "We tried X, then Y"

The PR body is the durability layer of the change: the diff records mechanics forever, intent only until the tab closes. This is the canonical Here's Why Inversion surface, and the rewrite is mechanical: find the last paragraph of the draft — the one that finally states the decision — and move it to the top.

**Before — squash subject and body are a search log:**

```text
Subject: reconciliation perf work + retry cleanup + wip

Looked at the reconciliation path because of the timeout reports. First tried
adding an index on invoice_id, which didn't help. Then I tried bumping the pool
size locally. Then I ran a profile and noticed the N+1 fetch, so I changed it to
batch the fetches, and that took p95 from 480 to 210. Also removed some legacy
code while I was in there. The debounce constant was flaky too so I reset it.
```

Diagnostics: TTV = 240+ words; three verdicts in one subject; the index attempt carries no adversarial value (a reviewer would not propose it after reading the mechanism) and the legacy removal has no rationale at all.

**After — verdict, hinge, mechanism, evidence, rejected, scope:**

```markdown
Subject: Batch reconciliation queries to cut p95 56%

## Verdict
Reconciliation issues one batched query per batch instead of one query per invoice.

## Here's why
The N+1 fetch was 91% of reconcile latency at 100k invoices (`artifacts/reconcile-pprof.txt`),
so latency grew with invoice count, not with concurrent load. Indexing `invoice_id` could not
help: the cost was round trips, not scan time (retry of that path: `EXPLAIN` shows index hit,
p95 unchanged at 478 ms).

## Evidence
| Metric | Baseline `main @ 4f9c1ab` | This PR `3a77e02` |
|---|---|---|
| p50 | 120 ms | 88 ms |
| p95 | 480 ms | 210 ms |
| queries / reconcile | 1,240 | 41 |

`make bench-reconcile WORKLOAD=prod-sample-100k RUNS=5`, same runner, median of 5, spread ±6 ms.

## Rejected
- **Connection-pool increase** — addresses concurrency, not round trips; p95 unchanged when spiked.
  Revive if p99 concurrency exceeds 400 (`grafana/billing-pool`).
- **Response cache** — hides the multiplier instead of removing it; deferred to PR #830.

## Scope / rollback / not included
Read path only; writes, refunds, and payouts untouched. Revert = this single commit; no schema,
config, or flag migration. Not included: the debounce reset (separate bug, PR #829) and the
`LegacyReconciler` removal, which is cosmetic and unrequested here.
```

**Commit-sequence hygiene.** Inversion is enforced at the rebase, not at the keyboard. Before merge, each commit subject in the sequence must be a verdict that stands alone in `git log --oneline`; `wip`, `fixup`, and `more retry stuff` are removed by squash or reword. A sequence that reads as a decision index costs nothing and pays for every future bisect.

### 3.3 Architecture RFCs / ADRs — Inverting the Context-First Document

The conventional ADR template — Context → Decision → Consequences — is structurally chronological: it makes the reader reconstruct a situation before learning what was chosen, and it is why so many ADRs are cited by title and never read. Invert the document, keep the sections.

**Required order:** `Status / Decision` → `Here's why (drivers)` → `Rejected alternatives` → `Consequences, expiry, rollback` → `Context (background)`. Context becomes an appendix that supports the decision rather than a corridor the reader must walk first.

**Drivers are ordered by decision weight, not by the order they surfaced.** If a driver was discovered in month three of the spike, it still goes first when it is the reason the decision holds. Each driver is one line and, where possible, bound to an artifact.

**Rejected alternatives are load-bearing, not decorative.** Each one carries the axis that killed it and the condition that would revive it, so a future reader knows when the decision has expired instead of rediscovering it during an incident.

```markdown
# ADR-014 — Batch reconciliation queries

## Status
Accepted — 2026-04-02 · Owner: billing-svc · Supersedes: none · Reverses if batch-size
limits drop below 10k IDs in a future Postgres upgrade (see Open questions).

## Decision
`billing-svc` issues one batched query per batch on the reconcile read path, retaining the
existing payload assembly.

## Here's why
1. Round trips, not scan time, dominate: 91% of p95 at 100k invoices (`reconcile-pprof.txt`).
2. Cost grew with invoice count, not concurrency — a pool increase cannot address it.
3. Payload assembly is untouched, so the change is invisible to every downstream consumer.

## Rejected
| Option | Axis measured | Revive if |
|---|---|---|
| Compound index on `invoice_id` | p95 unchanged (478 ms) | Workload shifts to single-invoice reads |
| Connection-pool increase | Concurrency-bound, not round-trip-bound | p99 concurrency > 400 |
| Response cache | Masks the multiplier; stale-payout risk | Read-only reporting endpoints appear |

## Consequences / rollback
Measured: p95 480 ms → 210 ms; queries 1,240 → 41 (`make bench-reconcile`, baseline
`main @ 4f9c1ab`). Blast radius: read path only. Rollback: revert the commit.

## Context (background)
`billing-svc` pinned `reconcile@2.4.1`; on-call logged 4 timeouts in 3 weeks (INC-4501, INC-4522).
```

**RFC variant.** A long-form RFC opens with a proposal summary block — `Decision`, `Status`, `Owner`, `Date`, `Blast radius`, `Reviewers` — before any motivation. The motivation section then becomes the hinge: it exists to make the verdict defensible, not to lead the reader through the design journey. Design journals, spike logs, and meeting transcripts stay out of the RFC; anything the reader genuinely needs from them is compressed into the drivers list or the rejected table, one line per item.

**Cross-surface consistency rule.** The verdict must be byte-compatible across surfaces: PR subject, PR body verdict, changelog entry, and ADR decision. If the ADR says "batch queries" and the subject says "perf improvements", the reader cannot tell whether they describe the same change, and every number in both documents loses credibility.

---

## 4. Verification Checklist

- [ ] **Time-to-Verdict is 1 sentence.** The artifact's first sentence names the terminal state of the system (not an activity) in ≤ 20 words; the commit subject alone states it in ≤ 72 characters, so `git log --oneline` reads as a decision index rather than a diary.
- [ ] **Every verdict has an adjacent hinge and a mechanism.** Within three sentences of each verdict, a `because` / *here's why* connects it to the code path, flag, schema, or algorithm that makes it true; no assertion stands alone.
- [ ] **Chronology is purged or demoted.** Zero instances of *explored*, *looked into*, *tried … then*, *ended up*, *eventually*, *it turns out*, *started by* survive outside a labeled `Rejected` block, and every rejected branch carries option + reason + evidence + revival condition on one line.
- [ ] **One verdict per unit, with the negative space stated.** No compound subject smuggles a second decision (a refactor, an SDK bump, a cosmetic cleanup) into the artifact; scope, not-included items, blast radius, rollback, and any unverified claim with owner and date are explicit.
- [ ] **Verdict parity holds across surfaces.** PR subject, PR body verdict, ADR decision line, and changelog entry state the same terminal state, and the artifact's verdict matches what the diff actually does — no drift between headline, body, and evidence.