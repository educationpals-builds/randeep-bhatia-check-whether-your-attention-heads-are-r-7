# The Five Splits: How to Audit Attention in a Ticket-Routing Bot

When your support ticket bot tags the wrong issue because a customer says "it broke again," you need a method for checking whether the attention mechanism is actually splitting the work correctly—or whether one part is broken and getting masked by others.

This file spells out the five principles. The acronym appears only here; the interrogator and audit files use the concepts without the letters.

---

## P — Partition the Space

Each attention head gets a slice of the embedding dimension. For a model with d_model = 512 and h = 8 heads, each head works with 64 dimensions. The question: is each head's 64-dimensional slice actually learning something distinct, or are they all doing the same job?

For the ticket bot, this means asking: does the head responsible for resolving "it" to a prior ticket have its own partition, or is it sharing space with heads that handle other tasks?

---

## R — Run in Parallel

All heads process the input simultaneously. They don't wait for each other. This parallelism is the point—but it also means a broken head can hide behind working ones.

When a customer writes "it broke again after you fixed it yesterday," multiple heads fire at once. If the aggregate output looks correct, you might never notice that the head responsible for matching "it" to the prior ticket is failing—because other heads compensated.

---

## I — Individuate the Pattern

Each head should specialize. One head might track syntactic dependencies. Another might handle coreference (what "it" refers to). Another might attend to temporal markers ("yesterday," "Tuesday").

The audit question: can you identify what pattern each head has learned? If you can't, or if multiple heads seem to do the same thing, the model may be wasting capacity—or worse, no head is reliably handling the critical task.

---

## S — Stitch the Spectra

After heads run in parallel, their outputs get concatenated and projected back to the full dimension. This stitching step combines the specialized views into a single representation.

The risk: if the stitching layer learns to ignore a broken head, the final output can look fine even when a critical component is failing. Aggregate accuracy hides component failure.

---

## M — Map What Each Head Sees

The only way to know if heads are splitting the work is to look at each head's attention pattern individually. For the ticket bot, this means:

- Take a message like "it broke again after you fixed it yesterday"
- For each head, visualize or measure what tokens it attends to
- Ask: is there a head that reliably attends from "it" to the prior context that would identify the earlier ticket?

If no head does this job, or if the head that should do it fails on your test cases, you've found the crack.

---

## The Anti-Pattern: Collapse to Monochrome

The failure mode is treating all heads as interchangeable—looking only at the final output and never checking whether individual heads are doing their jobs.

This is "collapse to monochrome": you see the aggregate grayscale image but never check the individual color channels. The ticket bot routes correctly 85% of the time, so you ship it. But the head responsible for resolving "it" to prior tickets is broken. It's getting masked by other heads that sometimes guess right based on other signals.

When the next batch of "it/that/again" messages arrives, the broken head can't be masked anymore, and the bot starts opening new tickets instead of finding old ones.

---

## Applying the Method to the Ticket Bot

The builder's audit (see charter.md) rated the five splits:

| Split | Rating | What It Asks |
|-------|--------|--------------|
| Partition (room) | 0 | Does each head have enough dimensions? |
| Parallel (copies) | 0 | Are heads redundant? |
| Individuate (unowned) | 0 | Is any critical pattern unassigned? |
| Stitch | 0 | Does stitching hide failures? |
| Ablation | 4 | What happens when you test one part alone? |

The decisive split is **ablation**: nobody has tested whether the matching part resolves "it broke again after you fixed it yesterday" correctly on its own. The aggregate tag accuracy looks fine, but the component responsible for the critical job has never been isolated and measured.

The tripwire: standalone match-part accuracy on "it/that/again" messages, tested weekly, must stay above 80%. If it drops, Priya blocks rollout—because aggregate accuracy can hide this drop.
