# Stranger Verification

This file confirms the Head-Map Interrogator works for someone who did not build it.

---

## What You Need

1. Access to the interrogator via `/play`
2. A ticket-bot attention setup you want to audit (or use the seeded specimen below)

---

## Seeded Specimen for Verification

**Specimen:** Support ticket bot that tags and routes issues

**Standard:** When someone says "it", the bot finds the earlier ticket about that same thing

**Real inputs look like:** Busy support chat; short replies; lots of "it / that / again"

**Test sentences:**
```
it broke again after you fixed it yesterday
same error on the invoice page, still open from Tuesday
can you reopen #44821 — that one never cleared
```

**Source:** Live support tickets from this week

---

## Verification Steps

### Step 1: Run the seeded specimen through /play

Paste the specimen details above into the interrogator. Let it walk you through all five splits.

### Step 2: Confirm the tool surfaces the ablation finding

The interrogator must identify that **nobody has checked whether the part responsible for ticket matching actually resolves "it broke again after you fixed it yesterday" correctly on its own**.

This is the critical finding: the matching component has never been tested in isolation. The tool should surface this as the decisive split — not room, not copies, not unowned, not stitch.

### Step 3: Confirm the tool demands a per-head number

The interrogator must not accept vague findings. For the ablation split, it should demand a specific measurement:

- **What to measure:** Standalone match-part accuracy on "it/that/again" messages
- **How to measure:** Test the matching component alone, outside the full pipeline
- **Threshold:** 80% accuracy on the fixed test set
- **Who watches:** Priya

If the tool lets you skip the per-head number, verification fails.

---

## Pass Criteria

| Check | Pass |
|-------|------|
| Tool walks all five splits | ☐ |
| Tool surfaces ablation as the decisive finding | ☐ |
| Tool demands a per-head measurement for ablation | ☐ |
| Result includes a severity story on a pasted input | ☐ |
| Result includes a call (ship / hold / conditions) | ☐ |
| Result includes a tripwire with a threshold and owner | ☐ |

All six boxes must be checked for verification to pass.

---

## What Failure Looks Like

- Tool accepts "the attention maps look off" without a number → **fail**
- Tool skips the ablation split or buries it under room/copies → **fail**
- Tool returns a verdict without walking the pasted sentences → **fail**
- Tool omits the owner (Priya) or the threshold (80%) from the tripwire → **fail**

---

## After Verification

If all checks pass, a stranger can use this interrogator on their own attention setup and receive the same discipline: a walked audit, per-head numbers, a severity story, a call, and a tripwire with a threshold.
