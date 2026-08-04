# Audit Charter: Support Ticket Bot Attention Split

## Specimen

**What tool is broken?**  
Support ticket bot that tags and routes issues

**What goes wrong if this never gets fixed?**  
Wrong person gets the ticket; the real bug waits another day

**Attention arithmetic:**  
When the bot processes a message like "it broke again," the model's attention layer divides its representation space (d_model) across multiple heads (h). Each head gets d_model ÷ h dimensions to work with. If d_model = 768 and h = 12, each head operates in a 64-dimensional subspace. The question is whether the head responsible for resolving "it" to a prior ticket is actually doing that job in its 64 dimensions — or whether the final routing accuracy is masking a broken part.

---

## Standard

**How will you know it is fixed?**  
When someone says "it", the bot finds the earlier ticket about that same thing

---

## Pasted Sentence Set

**What the real inputs look like:**  
Busy support chat; short replies; lots of "it / that / again"

**Three real messages where it fails:**

1. it broke again after you fixed it yesterday
2. same error on the invoice page, still open from Tuesday
3. can you reopen #44821 — that one never cleared

**Source:**  
Live support tickets from this week

---

## Five Split Findings

| Split | Rating | Notes |
|-------|--------|-------|
| Room | 0 | No evidence the heads lack dimensional capacity for this task |
| Copies | 0 | No evidence of redundant heads duplicating the same pattern |
| Unowned | 0 | No evidence of patterns falling through without a responsible head |
| Stitch | 0 | No evidence the heads fail to combine their outputs coherently |
| Ablation | 4 | **Critical.** The matching part has never been tested in isolation |

**Which check decides:**  
ablation

---

## Severity Story

Nobody has checked whether the part responsible for ticket matching actually resolves "it broke again after you fixed it yesterday" correctly on its own — all anyone has looked at is the bot's final routed tag. If that part is broken but gets masked by other parts sometimes guessing right, the team won't know until the next batch of "it"

---

## The Call

Hold. Priya can't sign off on this routing path without first testing the matching part alone against the three pasted tickets — shipping on aggregate tag accuracy alone risks hiding a broken part behind lucky overall results.

---

## Tripwire

Watch standalone match-part accuracy on a fixed test set of "it/that/again" messages, tested weekly outside the full pipeline. If it drops below 80% alone, Priya blocks further rollout, since aggregate accuracy can hide this drop.

---

*This charter is the worked example embedded in the Head-Map Interrogator. When a stranger audits their own attention setup, the tool walks them through the same five splits and demands the same specificity: a real sentence, a per-head measurement, a call with an owner, and a tripwire with a threshold.*