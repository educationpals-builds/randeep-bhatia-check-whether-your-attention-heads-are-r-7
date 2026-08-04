# Head-Map Interrogator

## What This Tool Does

This conversational auditor helps you check whether the attention heads in your ticket-routing system are actually splitting the work correctly—or whether one broken part is hiding behind lucky aggregate results.

You describe your attention setup, paste a few real messages where routing fails, and the interrogator walks you through five splits. For each split, it proposes candidate per-head findings and names the measurement that would confirm each. You finish with a scored audit: a severity story on one of your pasted inputs, a ship/hold call, and a tripwire with a threshold.

---

## Worked Example: Support Ticket Bot Audit

This interrogator was built from an audit of a real system. The builder's findings, call, and tripwire are embedded below as calibration.

### The Specimen

**What tool is broken?**
Support ticket bot that tags and routes issues

**What goes wrong if this never gets fixed?**
Wrong person gets the ticket; the real bug waits another day

**How will you know it is fixed?**
When someone says "it", the bot finds the earlier ticket about that same thing

**What the real inputs look like:**
Busy support chat; short replies; lots of "it / that / again"

### Real Messages Where It Fails

```
it broke again after you fixed it yesterday
same error on the invoice page, still open from Tuesday
can you reopen #44821 — that one never cleared
```

**Source:** Live support tickets from this week

---

## The Five Splits

Walk each split for the stranger's specimen. For every finding you propose, name the per-head measurement that would confirm it.

### 1. Room
Does each head have enough capacity for its assigned job?

**Measurement demanded:** Parameter count or embedding dimension allocated to the head vs. minimum required for the subtask.

### 2. Copies
Are multiple heads doing the same work redundantly?

**Measurement demanded:** Cosine similarity of attention patterns across heads on the same input; flag if two heads exceed 0.9 similarity.

### 3. Unowned
Is any necessary subtask not assigned to any head?

**Measurement demanded:** Coverage map showing which subtask each head handles; list any subtask with zero heads assigned.

### 4. Stitch
Do heads hand off to each other correctly, or do outputs get dropped between them?

**Measurement demanded:** Trace a single input through the head sequence; log intermediate outputs at each handoff; flag any null or malformed pass.

### 5. Ablation
Does each head actually contribute, or would removing it change nothing?

**Measurement demanded:** Run the target input with each head disabled in isolation; compare output to baseline; flag heads whose removal changes output less than threshold.

---

## Builder's Ratings

| Split     | Rating |
|-----------|--------|
| room      | 0      |
| copies    | 0      |
| unowned   | 0      |
| stitch    | 0      |
| ablation  | 4      |

**Decisive split:** ablation

---

## Severity Story

Nobody has checked whether the part responsible for ticket matching actually resolves "it broke again after you fixed it yesterday" correctly on its own — all anyone has looked at is the bot's final routed tag. If that part is broken but gets masked by other parts sometimes guessing right, the team won't know until the next batch of "it"

---

## The Call

Hold. Priya can't sign off on this routing path without first testing the matching part alone against the three pasted tickets — shipping on aggregate tag accuracy alone risks hiding a broken part behind lucky overall results.

---

## The Tripwire

Watch standalone match-part accuracy on a fixed test set of "it/that/again" messages, tested weekly outside the full pipeline. If it drops below 80% alone, Priya blocks further rollout, since aggregate accuracy can hide this drop.

---

## How to Use This Interrogator

### For a Stranger Auditing Their Own Attention Setup

1. **Describe your specimen.** What system are you auditing? What breaks when it fails? How will you know it's fixed?

2. **Paste three real messages where it fails.** Not synthetic examples—actual inputs from your logs or tickets.

3. **Walk the five splits.** For each split, the interrogator will propose candidate findings about your setup. For every finding, it names the per-head measurement that would confirm it.

4. **Rate each split** (0–4) based on how much that check matters for your specimen.

5. **Pick the decisive split.** Which one determines whether you ship?

6. **Write your severity story.** Take one of your pasted inputs. Walk through exactly what goes wrong, what output you get, and who acts on that wrong output.

7. **Make your call.** Ship / ship-with-conditions / hold. If conditions, name checkable actions with owners.

8. **Set your tripwire.** What metric will you watch after release? What number means trouble? Who watches it?

---

## Output Format

Your audit result includes:

- **Specimen:** The system under audit
- **Stakes:** What breaks if unfixed
- **Standard:** The pass condition
- **Reality:** What real inputs look like
- **Failed messages:** Three real examples
- **Split ratings:** 0–4 for each of room, copies, unowned, stitch, ablation
- **Decisive split:** The one that decides
- **Severity story:** A specific failure on a pasted input, with wrong output and who acts on it
- **Call:** Ship / ship-with-conditions / hold, with reasoning
- **Tripwire:** Metric + threshold + owner

---

## Calibration Note

This interrogator applies the same discipline its builder used on the support ticket bot. The ablation split was decisive there: the matching part had never been tested in isolation, so aggregate accuracy could mask a broken component. Your audit may find a different split decisive—but you'll walk all five, and every finding will name its confirming measurement.
