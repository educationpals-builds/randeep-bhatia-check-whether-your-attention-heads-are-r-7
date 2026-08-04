# Split-Walk Prompt Pack: Ticket Bot Attention Audit

Five standalone prompts for interrogating whether your ticket bot's attention heads are really splitting the work. Each prompt walks one split and ends with the per-head measurement it demands.

Use any chat model. Paste one prompt at a time with your own specimen details.

---

## Worked Example (Calibration)

**Specimen:** Support ticket bot that tags and routes issues

**What goes wrong:** Wrong person gets the ticket; the real bug waits another day

**Standard:** When someone says "it", the bot finds the earlier ticket about that same thing

**Reality:** Busy support chat; short replies; lots of "it / that / again"

**Test sentences:**
- it broke again after you fixed it yesterday
- same error on the invoice page, still open from Tuesday
- can you reopen #44821 — that one never cleared

**Source:** Live support tickets from this week

**Decisive crack:** ablation

**Severity story:** Nobody has checked whether the part responsible for ticket matching actually resolves "it broke again after you fixed it yesterday" correctly on its own — all anyone has looked at is the bot's final routed tag. If that part is broken but gets masked by other parts sometimes guessing right, the team won't know until the next batch of "it"

**Call:** Hold. Priya can't sign off on this routing path without first testing the matching part alone against the three pasted tickets — shipping on aggregate tag accuracy alone risks hiding a broken part behind lucky overall results.

**Tripwire:** Watch standalone match-part accuracy on a fixed test set of "it/that/again" messages, tested weekly outside the full pipeline. If it drops below 80% alone, Priya blocks further rollout, since aggregate accuracy can hide this drop.

---

## Prompt 1: Room

```
I need to audit whether the attention heads in my system have enough capacity for the job.

My specimen: [describe the tool you're auditing — what it does, what inputs it handles]

The real inputs look like: [describe the messy reality — length, style, ambiguity]

Paste three real messages where it fails:
[message 1]
[message 2]
[message 3]

Walk me through the room check:
- How many distinct things does each input ask the attention to track simultaneously?
- Count: pronouns needing resolution, entities needing linking, temporal references needing ordering
- For each of my three messages, list what the attention must hold at once

Then tell me the per-head measurement that would confirm a room problem:
What is the maximum simultaneous tracking load per head, and at what count does performance degrade?

Give me a number I can measure, not a feeling.
```

---

## Prompt 2: Copies

```
I need to audit whether my system has redundant attention heads doing the same work.

My specimen: [describe the tool you're auditing — what it does, what inputs it handles]

The real inputs look like: [describe the messy reality — length, style, ambiguity]

Paste three real messages where it fails:
[message 1]
[message 2]
[message 3]

Walk me through the copies check:
- Which attention patterns appear in multiple heads?
- For each of my three messages, identify where two or more heads attend to the same token pairs
- Are any heads doing identical work that could be consolidated or is wasting capacity?

Then tell me the per-head measurement that would confirm a copies problem:
What is the correlation coefficient between head attention patterns, and above what threshold are heads redundant?

Give me a number I can measure, not a feeling.
```

---

## Prompt 3: Unowned

```
I need to audit whether any critical subtask has no attention head responsible for it.

My specimen: [describe the tool you're auditing — what it does, what inputs it handles]

The real inputs look like: [describe the messy reality — length, style, ambiguity]

Paste three real messages where it fails:
[message 1]
[message 2]
[message 3]

Walk me through the unowned check:
- List every subtask the system must perform to handle these inputs correctly
- For each subtask, identify which head (if any) owns it
- Flag any subtask with no clear owner or with ownership split across heads in a way that creates gaps

Then tell me the per-head measurement that would confirm an unowned problem:
For each critical subtask, what percentage of relevant tokens does the assigned head attend to, and below what percentage is the subtask effectively unowned?

Give me a number I can measure, not a feeling.
```

---

## Prompt 4: Stitch

```
I need to audit whether attention heads are handing off information to each other correctly.

My specimen: [describe the tool you're auditing — what it does, what inputs it handles]

The real inputs look like: [describe the messy reality — length, style, ambiguity]

Paste three real messages where it fails:
[message 1]
[message 2]
[message 3]

Walk me through the stitch check:
- Which heads must pass information to downstream heads for the task to succeed?
- For each of my three messages, trace the information flow: what does head A resolve that head B needs?
- Where are the handoff points, and what evidence shows the handoff succeeds or fails?

Then tell me the per-head measurement that would confirm a stitch problem:
What is the information transfer rate between dependent heads, and below what percentage does the downstream head fail to use upstream resolution?

Give me a number I can measure, not a feeling.
```

---

## Prompt 5: Ablation

```
I need to audit whether each attention head actually contributes to the final output, or whether some heads are broken but masked by others.

My specimen: [describe the tool you're auditing — what it does, what inputs it handles]

The real inputs look like: [describe the messy reality — length, style, ambiguity]

Paste three real messages where it fails:
[message 1]
[message 2]
[message 3]

Walk me through the ablation check:
- If I disable each head one at a time, which heads cause the output to break?
- For each of my three messages, predict what happens when the head responsible for [the critical subtask] is removed
- Is there a head that appears important but whose removal changes nothing — meaning other heads are compensating?
- Is there a head whose solo performance has never been tested because we only look at aggregate output?

Then tell me the per-head measurement that would confirm an ablation problem:
What is each head's standalone accuracy on the critical subtask when tested in isolation, and below what percentage is that head broken but masked?

Give me a number I can measure, not a feeling.

---

For the ticket bot example, the ablation check revealed: Nobody has checked whether the part responsible for ticket matching actually resolves "it broke again after you fixed it yesterday" correctly on its own — all anyone has looked at is the bot's final routed tag. If that part is broken but gets masked by other parts sometimes guessing right, the team won't know until the next batch of "it"

The measurement demanded: standalone match-part accuracy on a fixed test set of "it/that/again" messages, tested weekly outside the full pipeline. Threshold: 80% alone. Owner: Priya.
```

---

## How to Use This Pack

1. Pick the split most relevant to your concern (or run all five)
2. Fill in your specimen, reality, and three failing messages
3. Paste into any chat model
4. Collect the per-head measurement it returns
5. Run that measurement on your actual system
6. If the number crosses the threshold, you have a confirmed finding

The worked example above shows a ticket bot where ablation was the decisive crack. Your system may break on a different split. Run all five if you're unsure which head is failing.
