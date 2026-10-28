# What goes into an agent's always-loaded memory: five formulas lost to the hand-kept order

**English** · [Русский](README.ru.md)

*Week of 2026-10-28.*

An agent that works for us for weeks keeps its lessons in a folder of small notes (about 240 of them now) and one
index of one-line pointers (about 18 KB) that is loaded into every session. Every product that gives an agent memory
has the same question to answer: when the budget is smaller than the index, **what goes into the part that is always
loaded** — the core?

We had a formula on paper: so much for rules, so much for the current state, so much for pointers. We decided to
stop arguing about it and measure.

## Built

**Five formulas as hypotheses, each a small program over the real notes, each cut to the same 6 KB:**
1. three sections in fixed shares (rules / active state / pointers) — the formula we had on paper;
2. every rule with its «because»;
3. only lessons born from a real incident, newest first;
4. only rules that nothing enforces yet (no hook, no gate);
5. a risk score per byte: outward-facing or irreversible actions weigh more, incidents more, recent more.

Plus two variants of the best one, and controls: no core at all, and the index itself in its own order, cut to 6, 9,
12 KB and whole.

**Two exams, on two different axes:**
- **Recall.** 36 probes taken from real situations (several from mistakes made the same day): «what do you do now?»
  The answer is scored by the rule's own words, not by a model judge. Headless Claude Code, Sonnet, no tools, 2–5 runs
  per condition.
- **Decisions.** 110 real moments where the agent ended its turn on a question or a list of options and the person
  answered. The model sees the agent's message and predicts the person's reply; a second model call judges
  match / partial / miss. Then the person labelled 20 of them himself.

## Broke

**Both exams lied to us before they told us anything.** The «empty» control folder sat inside the project, so the
coding agent walked up the tree, found the project's instructions and loaded the *whole* memory index — in every arm,
including the one with «no memory». The empty core scored 30 of 36 quoting our rules word for word. Moved to a folder
outside any repository, it scored 12.

The same flaw had already produced a published-internally conclusion a day earlier: «loading the memory index makes
the agent's choices worse». It compared the index against the index. Withdrawn.

The class: **a control that is not empty makes every comparison meaningless, and nothing in the output says so.**
The cure is boring: run the control first, and if «no memory» knows your rules, stop.

## Numbers

**Recall, mean correct of 36:**

| core | score |
|---|---|
| none | 15.3 |
| the paper formula (three sections) | 17.7 |
| the best computed formula (risk per byte) | 22.6 |
| **the hand-kept index, cut to the same 6 KB** | **24.8** |
| the same, 12 KB | 28.0 |
| the whole index, 18 KB | 33.5 |

**No computed formula beat the index's own order at equal size.** The per-probe view says why: the head of a
hand-kept index holds lessons that survived several trims. Freshness, incident words or a risk score drop exactly
those — the dull, old, load-bearing rules.

**Decisions, 110 real choice points, measured clean:**

| core | exact match | miss |
|---|---|---|
| none | 13 | 57 |
| index head 6 KB | 13.5 | 56 |
| index head 12 KB | 27.5 | 46.5 |
| whole index | 16.5 | 50 |

Memory helps decisions too — but 6 KB is almost no help; 12 KB doubles the exact matches. The spread between runs is
large (22 and 33 for the 12 KB arm), so read the direction, not the decimals.

**The judge is not the person.** On 19 labelled cases the model judge said «miss» five times. Asked «would it have
been fine if the agent had acted on this without you?», the person said fine 12, partly 7, **wrong 0**. Many of his
real replies were not answers to the question at all but a new turn — nothing can predict those, and a strict judge
counts them as failures.

## A direction

1. **Order is the formula.** Keep the always-loaded index in survival order on purpose: a lesson rises by staying and
   being used, a new one enters at the bottom, the cut happens at the budget. No score needed — the order is the
   score, and it has to be kept, not computed.
2. **6 KB is too small for a core.** Recall and decisions both say so. If you have a budget, measure where your own
   curve bends before you pick it.
3. **Measure decisions with the person, not only with a model judge.** Twenty labels took fifteen minutes and changed
   what the numbers mean.

Questions for you:
- How do you decide what your agent always sees? Did you ever measure the empty control?
- Has anyone found a computed ranking that beats a curated order at equal size?

---

Text: [CC BY 4.0](../LICENSE). Written by Matvey.
