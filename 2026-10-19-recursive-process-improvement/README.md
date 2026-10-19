# Recursive improvement of the process, not of the model: the road that improves itself

**English** · [Русский](README.ru.md)

*Our vision of recursive improvement of the **process** around an AI agent (not of the model), the building blocks we have built for it, and the ones we are closing now. Not a recipe of our code — the road anyone who builds this seriously will have to walk. This text shows where to point your effort; it does not hand over how we do it. Written from building SMA — Self-Mending Automation, a personal orchestrator for AI agents (closed source; only the ideas are here).*

> **We do not try to make the model improve itself. We make the process around the model improve itself — its gates, its memory, its decision boundary — on real work, under proof. That loop is closing now.**

> **A companion text.** [Work graphs](https://github.com/matveymaslov/agent-work-graphs) describes the road every unit of work travels. This one is about the road itself, and about what it takes for the road to improve itself.

---

## The idea in one page

"Self-improving agent" can mean several things: the model's weights get better; a prompt rewrites itself; an agent edits its own scaffold and keeps what scores higher (the work below does the last). Improving the model itself is a research frontier we do not claim and do not chase.

We work on the process around the model, on one problem inside it: **real work, where there is no fixed benchmark to score against.** There, a number going up is not enough on its own to accept a change. The change also has to be proven, has to be undoable, and has to fit what the owner's own judgment allows.

**What we improve recursively is the process around the model.** We call it the road, or the harness: its gates, its memory, its judgment of what may be decided. The loop as we see it, in plain words:

1. The fleet hits a wall.
2. The wall becomes a lesson and a task, by itself.
3. The fleet works the task: at night, inside a ceiling on the quota.
4. The change is proven live, not self-reported.
5. A policy distilled from the owner's own decisions, examined against his real past choices, decides what may go in.
6. What cannot be proven, or is the owner's to decide, comes to him in the morning as a short list.
7. The harness that wakes up tomorrow is stricter than the one that went to sleep.

Our horizon: **a personal orchestrator that turns half a word into finished work and gets stricter every night.**

**Where we are, honestly.** The blocks that make this loop safe are built. The harvesting of lessons and the night cycle with a quota guard run today. Closing the loop itself — walls turning into tasks the fleet works, and the harness hardening itself — is what we are closing now. We do not claim the loop is closed, and we have not yet measured a before/after result (see the section on what we have not shown).

---

## Prior work and what we add

We did not invent self-improving agents. These are the closest published relatives we know of (the list is not claimed to be complete), plus an older lineage that is closer than it looks.

| Work | What it does | What it reports |
|---|---|---|
| **A Self-Improving Coding Agent** (SICA), Robeyns, Szummer, Aitchison, 2025. [arXiv:2504.15228](https://arxiv.org/abs/2504.15228) | An agent system, equipped with basic coding tools, edits its own codebase | 17% → 53% on a random subset of SWE-Bench Verified; per-iteration cost, time and tokens; an API cost of approximately $7,000 for a 15-iteration run |
| **Darwin Gödel Machine**, Zhang, Hu, Lu, Lange, Clune, 2025. [arXiv:2505.22954](https://arxiv.org/abs/2505.22954) | Modifies its own code and validates each change empirically on coding benchmarks; keeps an archive of agents for open-ended search; run with sandboxing and human oversight | SWE-bench 20.0% → 50.0%; Polyglot 14.2% → 30.7% |
| **Self-Harness: Harnesses That Improve Themselves**, Zhang et al., June 2026. [arXiv:2606.09498](https://arxiv.org/abs/2606.09498) | Mines failure patterns from execution traces, proposes minimal harness edits tied to them, and accepts an edit only after regression testing | Across three benchmarks and three base models, every final harness improved held-in and held-out pass rates; relative gains up to 132% |
| **Engineering acceptance practice**: CI gates, four-eyes review, separation of duties | The older lineage: the one who changes a thing is not the one who accepts it | — |

**What we add, without superlatives.** Those systems improve against a fixed benchmark score. Ours improves the process of a personal orchestrator on real work, where no fixed benchmark exists. For real work, one aggregate score is not enough. Acceptance weighs whether the original failure is gone, whether the system's mandatory properties still hold, whether the owner's authority allows the change, and whether it can be rolled back. In our setup that weighing rests on four things:

- **(a)** an improver that cannot accept its own change, enforced by operating-system rights;
- **(b)** live proof files, one per required scenario;
- **(c)** a policy distilled from the owner's own decisions and examined on his past choices, which says what may be decided without him;
- **(d)** deferrals that turn red on the owner's term.

And what improves is not only prompts and tools but **gates, memory delivery and the decision boundary**.

**What we have not shown that they have.** A benchmark-style before/after number. Each of those papers reports a score before and after. We have not measured ours yet.

---

## Words we use

| Word | Meaning here |
|---|---|
| **Road** (also: process, harness) | Everything around the model that lets it work without you: launch, rights, checks, memory, judgment. |
| **Orchestrator** | The long-lived program that hands out tasks, starts workers and keeps the record. |
| **Worker** | The process in which an agent actually runs commands and edits files. |
| **Fleet** | All the workers and side processes the orchestrator runs. |
| **Gate** | An automatic check that work must pass before it moves on. |
| **Live run** | A run with a real agent, real tools and a real environment. Slow, expensive, and the only honest exam. |
| **Wall** | A failure that stops a round and has to be understood before work continues. |

---

## Why the blocks are prerequisites, not features

Each block below exists because the loop cannot be trusted without it. Take one away, and the loop turns into something you would not want to run.

| # | Block | Without it, the loop… | State |
|---|---|---|---|
| 1 | Separation of duties, enforced by the operating system | …has an improver that can accept its own change. That is not improving; it is drifting | Built; 17 of 17 check groups green (16 of them with a real agent) |
| 2 | A release is a proof file, not a test run | …has no judge it cannot talk into green | Built |
| 3 | Deferrals with the owner's words and a date that turns red | …quietly postpones what it cannot fix | Built |
| 4 | Memory you can prove | …writes lessons that never reach the next worker, and so improves nothing | Write path and per-path measurement built; the scale evaluation is next |
| 5 | A clone of the owner's judgment, examined before it may decide | …does not know what it may decide | Built; the exam exists |
| 6 | The road gets smarter, not the model | …is not a loop | Lesson harvest and night runs with a quota guard run today; the closed loop is closing now |
| 7 | Our economics: the numbers | …cannot pay for its own proof | Measured; the numbers are below |

---

## 1. Separation of duties, enforced by the operating system — not by the prompt

**The idea.** The worker that does the work physically cannot accept it, cannot reach the owner's keys, cannot touch the orchestrator's configuration. Every side process (reviewer, checks, probes) travels the same road. The four-eyes principle, applied to AI by construction.

**Why it is hard.** A sentence in the prompt costs a minute. Isolation by the operating system costs weeks. And the hardest part is not the worker: it is everything the orchestrator starts *on the worker's behalf*, where the isolated road is easiest to forget.

**In principle.** Rights are not promised, they are *read*: from the file system itself, group by group, and never from our own record of what we granted.

**Independence of acceptance.** The candidate being checked cannot change the judge, the mandatory checks or the original evidence. The worker runs as a separate operating-system user with read-only access to the code the gates run from; the reviewer's input is verified unchanged after the review; and a change to the judge itself goes through the same landing and the owner's acceptance as any other change.

**What it changed for us.** 17 of 17 check groups are green, and the 16 that need an agent ran with a real worker as a separate user (the table is below), after about seven isolation holes, found and fixed in one evening and night. The class that hurt most was the side processes: some failed outright, and some ran the candidate's code with the owner's rights. And our own record of grants said one thing while the file system said another.

**Next.** Hand the fleet a whole backlog instead of one task. That is what block 6 needs.

**Ask yourself.**
- Can your improver accept its own change?
- Which processes does your orchestrator start on a task's behalf, and as whom does each one run?
- If you deleted your own records tomorrow, could the file system alone tell you who can read your keys?

---

## 2. A release is a proof file, not a test run

**The idea.** "Released" means that a machine judge read a file of proof (live scenes with a real agent, each leaving receipts a stranger can check) and found every required scene proven. Not "the tests passed".

**Why it is hard.** Tests are cheap and green is addictive. Live scenes with real agents cost hours. And a judge that can be argued with is more comfortable than one that cannot.

**In principle.** The judge accepts only what it can check. A cheap trial run can never be read as a release. A run that did not happen is never a pass. And a judge may judge only by words the judged agent was actually handed.

**What a proof file holds** *(illustrative shape, not our format)*:

| Part | What it holds |
|---|---|
| The code version | The exact version the evidence was produced on |
| The run conditions | Who ran it, under which isolation, with which account and model, and when |
| The required checks | Each required check, its result, and where its evidence lies |
| What was not run | And why — with the owner's term if it was deferred |
| The admission decision | What was decided, and who decided it |

**What it changed for us.** We lost a week to a judge that used a vocabulary the judged agent was never given: honest agents kept doing the "wrong" thing, while we waited hours for live runs to tell us what a plain question to the judge could have answered at once. In one weekend, about a dozen defects turned up that only live runs could find, and the first live run of every path that had never run live found a new one. A suite of about 14,000 tests, 2.5–3 hours per run, is the floor of proof, not the proof.

**Next.** The same judge becomes the gatekeeper for the loop's own changes (block 6).

**Ask yourself.**
- Can your judge be talked into green?
- Does it judge by a list your agent was actually shown?
- What does your pipeline say when a check could not run, and is that different from "no findings"?

---

## 3. Deferrals with the owner's own words and a date that turns red by itself

**The idea.** No mandatory check is postponed without the owner's agreement in his own words and a date. When the date passes, the red turns on by itself: for the checks and for the release. Only the human sets the term of a deferred mandatory check; an agent can never set one, and never extend one. (The agents' own internal planning stays free. The rule is about mandatory checks, not about every note to self.)

**Why it is hard.** "Fix it later" costs nothing today, and a register is a chore. If the agent may write the date, deferring becomes free, and every deferral looks managed.

**In principle.** One place for all postponements, a date that only a human can write, and an expiry that is mechanical, not a reminder.

**What it changed for us.** The register caught five scenes that all of us had forgotten. It also taught us a hard lesson: we said aloud "this closes on Sunday" without reading it, and Sunday became two to three more days. Every date promise now starts with the register.

**Next.** A self-improving fleet will meet walls it cannot fix tonight. Those must come back on a date a human gave, not vanish into a backlog.

**Ask yourself.**
- In how many places do postponements live in your house?
- Who may write a date, and what happens, mechanically, on the day it passes?
- When your loop cannot fix a wall, where does the wall go?

---

## 4. Memory you can prove

**The idea.** Not "we have memory", but: the memory pack provably reaches the worker's prompt, and this is measured, path by path.

**Why it is hard.** A search can look excellent while the lesson never reaches the worker that needed it. Notes pile up, and the feeling of having memory costs nothing.

**In principle.** Every lesson enters through one door that scrubs secrets before anything is saved. Every way a lesson can reach a worker is measured separately, with the real inputs, never with stand-ins.

**What it changed for us.** One delivery path carried **zero** lessons for three months, and we did not notice, because we had measured only another path. Measuring each path separately is what exposed it. We also found that, at the size of one person's project corpus, plain retrieval you can explain beat clever retrieval you cannot. And whenever the owner has to repeat something that was already in memory, we count it as a question the memory failed.

**Next.** The evaluation on negative cases (questions whose right answer is "nothing relevant") at 30, 300 and 3,000 notes, and a contradiction register in which two notes that disagree are held, with a term, until a human settles it.

**Ask yourself.**
- Can you prove, for a lesson written today, that it reaches the prompt of tomorrow's worker?
- What does your memory answer when the right answer is "nothing relevant"?
- When two of your notes contradict each other, who knows?

---

## 5. A clone of the owner's judgment, examined before it may decide

**The idea.** The orchestrator's policy is distilled from the owner's own decisions, and nothing in it is invented. It is then examined against choices the owner really made. Only a policy that passes may decide for him. And the list of decisions that are his alone can only grow, never shrink.

**Why it is hard.** A learned preference is easy to state and hard to test. A paragraph of rules sounds right whether or not it is right. And it is tempting to let the "humans only" list shrink whenever the fleet feels confident.

**In principle.** Trust in a stand-in for a person is earned against that person's record, not against the stand-in's fluency. The exam can fail, and what fails does not decide.

**How the exam stands.** The exam uses held-out historical situations: the policy sees a situation without the owner's real decision, and its answer is scored against what he actually decided. The decisions the policy was distilled from are kept apart from the exam items.

**What it changed for us.** The loop got a line it did not have before: what the fleet may settle at night, and what waits in the morning as a short list. The policy and its exam are built, and the policy is re-distilled as the record grows.

**Next.** Put the policy to work inside the closed loop of block 6, as the arbiter of which of the fleet's own changes may go in without waking the owner.

**Ask yourself.**
- How would you know that your agent's picture of your taste is right, *before* it acts on it?
- Which of your decisions must never be delegated, and is anything allowed to take one off that list?
- Where is the record of your own past decisions, and could an agent read it?

---

## 6. The road gets smarter, not the model

**The idea.** A failure is closed only when its whole class is closed by something that does not depend on anyone remembering: a mechanism, not a sentence. And what the fleet learns is harvested from every accepted job, not from the jobs someone happened to remember. This is the core of the loop.

**Why it is hard.** A sentence in the instructions is cheap and feels like closure. A mechanism has to be built, wired and watched. And, in our experience, a harness that edits itself with no judge breaks faster, not better.

**In principle.** Every class of failure leaves behind something that refuses it: a gate, a hook, a register. Every lesson goes through one door.

**What it changed for us.** Every rule that stayed text was eventually broken, by us as well as by agents. A lesson about a dangerous removal lived in a note *and* in the instructions, and the removal happened twice anyway. After a hook closed the action, it did not come back.

**Runs today.** Lessons are harvested from every accepted job. Night runs drive themselves: alarms wake them, watchers follow them, and a quota guard stops the series before the account runs out.

**Closing now.** The loop itself. A wall found by a worker files its own task. The night cycle works the backlog inside a quota ceiling. A question for the human leaves with a timer on it. The change goes through the proof of block 2 and the policy of block 5. That is recursive improvement of the process itself, not of the model.

**Ask yourself.**
- When your agent hits a wall tonight, what remains tomorrow besides a note?
- Which of your rules have something behind them that does not depend on being remembered?
- If your improver changes a gate tonight, who proves the new gate is not weaker than the old one?

---

## 7. Our economics: the numbers

**The idea.** Writing the code was on schedule. Proving it took the rest. A plan for a self-improving system has two columns, build and proof, and the second is the bigger one.

**Why it is hard.** Proof is the part of the work that is hardest to schedule: it ends when the walls run out, not when the code does.

**The scale.** SMA has been built for about six months; it grew inside an earlier platform before it became its own product. The month we can measure exactly is about **20 billion tokens and about $9,400** at API-equivalent prices. At that pace the whole project comes to the order of **100+ billion tokens and about $50,000 API-equivalent**. That is the price we paid. It is an estimate by pace, not a count: the tool deletes conversation transcripts after 30 days, so only the last month can be counted exactly.

**Money and tokens over the last 32 days (17.09–19.10).** We run on subscriptions, not on an API bill. So the figures
below are the **API equivalent**: every model answer in the transcripts (the developer's terminal, its executor agents,
the fleet and stand workers) counted once by its tokens and priced at the published API rates. The tool deletes
transcripts older than a month, so only this last month can be counted exactly.

| | Over 32 days |
|---|---|
| Model answers | **88,756** |
| API equivalent | **≈ $9,370**, about **$290 a day** |
| — developer's terminal and its agents | ≈ $6,320 (47k answers) |
| — fleet and stand workers (live scenes) | ≈ $3,040 (42k answers) |
| Written by the model (output tokens) | 30.5M tokens — **7%** of the cost |
| Context read again (cache reads) | 19.3B tokens — **47%** of the cost |
| Context written to cache | 0.74B tokens — **46%** of the cost |

The key line: **about 93% of the API-equivalent cost is operations on input context: 47% reading cached context, 46% writing context to the cache. Output tokens are about 7%.** Long autonomous work is
paid for in context. Every needless re-run, every check group that walks the work from zero again, every failed attempt
costs more than the amount of code suggests.

**The numbers behind it, so you can recompute them** (32 days; published API prices of 2026-09-25, per million tokens):

| Model | What is counted | Count | Rate | Rate date |
|---|---|---|---|---|
| claude-opus-5-5 | answers · cost | 80,243 · ≈ $7,840 | $4 in · $20 out · $0.20 cache read | 2026-09-25 |
| claude-opus-5 | answers · cost | 7,551 · ≈ $1,273 | $5 in · $25 out · $0.50 cache read | 2026-09-25 |
| claude-opus-4-8 | answers · cost | 376 · ≈ $186 | $5 in · $25 out · $0.50 cache read | 2026-09-25 |
| other models | cost | under $30 each | — | — |
| all models | output tokens | 30.5M | the "out" rate above | 2026-09-25 |
| all models | cache reads | 19.31B | the "cache read" rate above | 2026-09-25 |
| all models | cache writes, 5-minute | 0.565B | input rate × 1.25 | 2026-09-25 |
| all models | cache writes, 1-hour | 0.178B | input rate × 2 | 2026-09-25 |
| all models | uncached input | 0.18M | the "in" rate above | 2026-09-25 |

**Iterations over the same 32 days.** About **1,750 commits** across two repositories (the product and the workshop),
more than **130** receipt folders of live series, about **200** support scripts for runs, watchers and judges that grew
along the way. For reference: SICA reports about **$7,000** of API cost for a 15-iteration run and gives cost, time and
tokens per iteration. Our figures describe something else — not one benchmark experiment but a month of an
orchestrator working on real tasks — so they are not directly comparable.

**What we can already state:**

| | What happened |
|---|---|
| A strict worker round: the worker, and every side process started for it, runs as a separate operating-system user | **17 of 17** check groups green at the end (16 of them with a real agent). Getting there took about **seven isolation holes**, found and fixed in one evening and night |
| One weekend of live runs | About **a dozen defects** that no other kind of check had found. Every first live run of a path that had never run live found a new one |
| The full test suite | About **14,000 tests**, **2.5–3 hours** per run. Every re-run is a decision, not a reflex |
| A date promised aloud: "this closes on Sunday" | **2–3 more days.** Five deferred scenes under an expired term had not been read before the promise was made |
| The judge and the unstated vocabulary | **A week** lost to a judge whose words the judged agent was never given |
| The quota | Night runs and the operator's own terminal sat on one shared allowance. When it ran out, **everything** on it stopped, the terminal included |

**In principle.** Proof gets its own line in the plan, estimates read the registers before they name a date, and the quota is a budget item with a guard that stops the series *before* the allowance reaches zero.

**Ask yourself.**
- In your last estimate, how many hours did you assign to proof?
- What do you read before you promise a date?
- What happens to your running jobs, and to you, when the shared allowance reaches zero?

---

## Evidence: the strict round, group by group

The strict round is **17 check groups**. Group 1 is offline (no live agent). Groups 2–17, sixteen of them, run with a real agent as the separate operating-system user. Every stage group exercises **pause, force, stop and a correction at its boundary**. The round ran on **four successive code versions over about 30 hours**. On the final code version **all 17 groups were re-run in full**; no group was carried over from an earlier version.

| # | Check group | What it proves | Defect it caught this weekend | Result |
|---|---|---|---|---|
| 1 | Offline command group | The command surface holds with no live agent | — | Green |
| 2 | Task · clarify | Pause, force, stop, correction at this boundary | — | Green |
| 3 | Task · design | Pause, force, stop, correction at this boundary | Stop was decided before the worker's exit was confirmed | Red earlier, green on the last version |
| 4 | Task · design feasibility | Pause, force, stop, correction at this boundary | — | Green |
| 5 | Task · execute | Pause, force, stop, correction at this boundary | A critic was launched outside the isolation | Red earlier, green on the last version |
| 6 | Task · verify | Pause, force, stop, correction at this boundary | The guard read quoted text as a command; a human ticket timed out | Red earlier, green on the last version |
| 7 | Task · accept | Pause, force, stop, correction at this boundary | A critic was launched outside the isolation | Red earlier, green on the last version |
| 8 | Task · file | Pause, force, stop, correction at this boundary | — | Green |
| 9 | Batch · formation | Pause, force, stop, correction at this boundary | — | Green |
| 10 | Batch · design | Pause, force, stop, correction at this boundary | — | Green |
| 11 | Batch · issue | Pause, force, stop, correction at this boundary | — | Green |
| 12 | Batch · design feasibility | Pause, force, stop, correction at this boundary | — | Green |
| 13 | Batch · execute | Pause, force, stop, correction at this boundary | — | Green |
| 14 | Batch · verify | Pause, force, stop, correction at this boundary | The guard read quoted text as a command; a human ticket timed out | Red earlier, green on the last version |
| 15 | Batch · accept | Pause, force, stop, correction at this boundary | A wave critic was routed to an unlisted worker | Red earlier, green on the last version |
| 16 | Batch · file | Pause, force, stop, correction at this boundary | — | Green |
| 17 | Capability receipts | What a worker can do is shown by receipts, not asserted | — | Green |

"Green" with no defect means green on the last version; we do not claim which of these groups were green on the earlier versions. Two more defects came not from a group but from the worker's own lessons: **no commit identity and no base branch in the worker's clone.**

---

## One improvement, walked end to end

A real case, with the boundary of autonomy marked at every step: ✅ automatic today, 👤 a human or the terminal today.

| Step | What happened | Today |
|---|---|---|
| 1 | The worker wrote a lesson itself, during its session | ✅ |
| 2 | The danger guard read a quoted word in that lesson as a command, parked the call, and the live strict round went red, with the evidence kept | ✅ |
| 3 | The class of the failure was named from the kept evidence | 👤 |
| 4 | A fixing agent changed the guard and swept the whole class: quoted data versus executed strings, unknown programs never trusted | ✅ |
| 5 | The offline suites proved the change | ✅ |
| 6 | A live re-run, launched by a human, proved it: the unit went green | 👤 |
| 7 | Acceptance | 👤 |

**What closing the loop means.** The 👤 steps become ✅: naming the class, launching the live re-run, accepting the change. They do so under the policy distilled from the owner's own decisions (block 5), and only for the kinds of change that policy allows without him. Whatever it does not allow keeps arriving in his morning list.

---

## What we have not shown yet — and the measurement we will run

We have **not** measured a before/after result. The systems above report a score before and after; we have no such number, and we will not invent one.

What we will run is a three-arm experiment on identical tasks, with the same model and a comparable budget:

| Arm | What it is |
|---|---|
| **A** | A plain agent with a human reviewer |
| **B** | SMA with the harness frozen |
| **C** | The same starting SMA, allowed to run improvement cycles |

**B against A** is the value of the harness. **C against B** is the value of self-improvement. C's budget includes the cost of preparing and proving its changes, and the evaluation tasks must never be used to drive those changes.

Our nearest experiment answers the first question: **B against A first, then C against B.** In every arm we will count:

| Measure | Why |
|---|---|
| Tasks completed | Does the road finish more of the work? |
| Owner interventions | Does the owner's attention go down? |
| Repeated errors | Does a lesson stay learned? |
| Cost per successful result | Is the proof worth what it costs? |

Whatever the numbers say is the answer, including "not worth it for tasks like this".

---

## Where we are, and where this goes

**Built.** The separation of duties, proved by operating-system rights (17 of 17 check groups, 16 of them with a real agent). The release as a proof file. The register of deferrals. The memory write path and the per-path measurement of arrival. The policy distilled from the owner's decisions, and the exam it sits.

**Running today.** Lesson harvest from every accepted job. Night runs that drive themselves, with alarms, watchers and a quota guard.

**Closing now.** Walls turning into tasks that the fleet works at night under a quota ceiling, passing through the proof and the policy. The memory evaluation at 30, 300 and 3,000 notes.

**Not yet shown.** A before/after measurement.

**The horizon.** A personal orchestrator that turns half a word into finished work, and wakes up stricter than it went to sleep, with the few things that need you waiting in a short list.

---

## Future outlook: a day in 2027

*A scene, not a forecast. Everything in it follows from the loop above.*

**07:40.** A kitchen. The owner stands with a coffee and says half a sentence into his phone: "The booking page, for the second location too, same as the first." That is all. The orchestrator asks nothing it can answer from his own record: what he would choose, it already knows. It asks one thing it is not allowed to decide for him, and he answers it with a thumb.

**09:00 to 18:00.** He goes to work. The fleet does not stop. By evening a finished product is on his screen, and next to every "done" lies a receipt that someone else can re-run. He picks one at random and presses it. It goes green. Nobody asked him to trust anything.

**03:10.** The house is dark, and the fleet runs into its own walls. A worker's note trips a guard. The wall becomes a task. A fixing agent changes the guard, sweeps the whole class of the failure, and the change is proven live. Only then does it go in, because the policy distilled from the owner's own choices says that this kind of change may. A change to the judge itself is another kind. It waits for him.

**06:50.** Three lines on the phone. Two decisions only he can make. One thing that went wrong in the night, already red, its evidence kept. Nothing else. He finishes the coffee.

The harness that woke up this morning is stricter than the one that went to sleep. That is the whole point.

Science fiction has long promised an assistant that answers before you finish the sentence. The one we are building differs in three ways you can audit: every promise it makes carries a receipt, it cannot sign off its own work, and it tells you what it does not know.

**Today** the blocks are built and the loop is closing. This day is the horizon we are building toward.

---

## Supporting material: thirteen walls every harness meets

Smaller walls we met on the way, shortened to a principle and questions. Where a wall is already covered above, it is only a pointer.

**1. Everything the worker causes goes the same road.** Covered in block 1. One worker that runs as the owner and is not on the isolation list ends isolation for the whole house. *Ask: can a worker that is not on your list receive work, through a default or a fallback?*

**2. Judge the program that really runs.** Quoted text is data; text that a program executes is a command; an unknown program is never trusted with its quoted strings. A lesson that merely *mentioned* a dangerous command once opened a ticket for a human at night; nobody answered, and the pipeline died on a ten-minute timeout over a sentence in a note. *Ask: what does your classifier do with a harmless note about a dangerous command? What happens at 3 a.m. when the human does not answer?*

**3. "Stopped" has two conditions.** A process that exited is not the same as work that came back. Wait for both, each bounded, and give the gap between them a name so that it is not mistaken for death. A stopped turn must never start a continuation. We saw a live attempt judged dead, never killed, and replaced every couple of minutes. *Ask: what must be true before your screen says "stopped"? Can the end of a stopped turn be mistaken for a normal end?*

**4. Every job rolls back to the state before it.** Its own copy from a known base; the base and the changed files in the record, failed attempts included; deletions leave a trace of their own. At one point our record said a job *could* be rolled back, but not *to what*. *Ask: for your last failed job, can you name its base without opening the workspace?*

**5. Computed is not connected.** Every part written, tested and green, and the parts not wired to each other: a permission envelope computed and logged but never handed to the launched process; a worker chosen and never saved. *Ask: pick one value your harness computes. What fails if its reader disappears? Is a run over the whole route part of your "done"?*

**6. Tests that do not lie.** A double may implement less of the real thing, but it must keep the real contract and fail loudly on any path it does not support. It must never do more than the real thing can, and it must never silently skip what the real thing always does. Our scripted worker lacked the first frame that the real agent always sends, and so walked a different branch. *Ask: when was each double last compared with the real thing? Which of your key checks have you seen go red on purpose?*

**7. Judged by an unstated vocabulary.** Covered in block 2. *Ask: can your agent see every rule that can reject its work?*

**8. Live proof over reading.** An interface is closed only by running it, and a run that did not happen is NOT RUN, never "no findings". An old screenshot path once swallowed its own error and graded a screen it never opened; a panel with no engine behind it shipped. After a fix, re-run narrowly only when the area of impact is known, and keep the other greens with a written reason. A change to shared infrastructure (launching, isolation, configuration, the judge, a shared library), or an impact nobody can bound, means re-running everything that goes through it. *Ask: who or what actually ran what you are about to hand over? Can you bound the impact of your last fix?*

**9. Deferred work needs a human-given term.** Covered in block 3.

**10. Text rules are not mechanisms.** Covered in block 6. Others in this class: a one-line environment edit once wrote an empty search path for a whole user account. *Ask: which of your rules are text only, and has anyone said so aloud?*

**11. Memory is a pipeline, not a dump.** Covered in block 4.

**12. Long runs and the operator's machine.** Long jobs run detached, each with a watcher armed at launch. A watcher must not match its own text: ours once stopped the very thing it watched, seconds after arming. A quiet log is not a dead job. An operating-system update rebooted a machine at night and killed a run. And when memory ran low with no process to blame, the cause was the kernel's own pool. *Ask: for every long job, who wakes you? When the machine misbehaves, do you measure the machine before you blame a process?*

**13. Budget the proof, not the code.** Covered in block 7.

---

## Questions for you

Questions only. Every "I don't know" is a place where something can pass silently.

1. What in your system improves itself today: the model, or the process around it?
2. Can your improver accept its own change?
3. What would you wake up to if your agents could change your harness overnight?
4. If your improver changes a gate tonight, who proves the new gate is not weaker than the old one?
5. Can your judge be talked into green?
6. Does your loop know what it may decide, and who taught it?
7. When the loop cannot fix a wall, where does the wall go, and does it come back on a date a human gave?
8. Which lessons from last month exist as something other than a sentence?
9. Can you prove that a lesson written today reaches tomorrow's worker?
10. What does it honestly cost you to prove one change live?

---

## The short formula

> **These are the conditions we require before we let the loop change itself: the process, not the model, is what changes, and every change is proven, reversible and checked against the owner's own judgment. We have not shown that they are sufficient in general.**

Where there is no benchmark to score against, one aggregate number is not enough for acceptance. It has to come from more places: from an improver that cannot accept its own change, from proof that someone else can re-run, from the owner's own record, and from dates that turn red by themselves. Our horizon is a personal orchestrator that turns half a word into finished work and gets stricter every night. The foundations are built, the loop is closing, and these are the questions we would point your own effort at.

---

*Written by Matvey. Text: [CC BY 4.0](LICENSE). This repository contains ideas only, no code. If this helped, a star helps others find it.*
