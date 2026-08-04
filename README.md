# The Head-Map Interrogator

**For teams auditing whether their ticket bot's attention heads actually split the work — before shipping routing logic that hides broken parts behind lucky aggregate results.**

---

## The Specimen

Support ticket bot that tags and routes issues

When a customer says "it broke again," the bot opens a new ticket instead of finding the old one. The failure mode: wrong person gets the ticket; the real bug waits another day.

---

## The Verdict

**Hold.** Priya can't sign off on this routing path without first testing the matching part alone against the three pasted tickets — shipping on aggregate tag accuracy alone risks hiding a broken part behind lucky overall results.

---

## The Tripwire

Watch standalone match-part accuracy on a fixed test set of "it/that/again" messages, tested weekly outside the full pipeline. If it drops below 80% alone, Priya blocks further rollout, since aggregate accuracy can hide this drop.

---

## One-Paste Rebuild

Copy this block to rebuild the interrogator from the builder's audit:

```
SPECIMEN: Support ticket bot that tags and routes issues
STANDARD: When someone says "it", the bot finds the earlier ticket about that same thing
REALITY: Busy support chat; short replies; lots of "it / that / again"

TEST SENTENCES (from Live support tickets from this week):
- it broke again after you fixed it yesterday
- same error on the invoice page, still open from Tuesday
- can you reopen #44821 — that one never cleared

DECIDER: ablation (rated 4)
CALL: Hold
TRIPWIRE: 80% standalone match-part accuracy, weekly, Priya owns
```

---

## What This Tool Does

A stranger describes any attention setup they're about to rely on — config, task, real inputs — and pastes a few of their own sentences. The tool interviews them for specimen, stakes, standard, and reality, walks the five splits conversationally, proposes candidate per-head findings and the measurement that would confirm each, and returns a scored audit with a severity story, a call, and a tripwire.

The builder's own audit — the ticket-bot routing failure above — is embedded as the worked example, so the tool interrogates heads the way its builder does.

---

## Files

| File | Contents |
|------|----------|
| [charter.md](charter.md) | Full audit: specimen, standard, pasted sentences, all five split findings, severity story, call, tripwire |
| [METHOD.md](METHOD.md) | The five-split framework spelled out letter by letter |
| [VERIFY.md](VERIFY.md) | Stranger verification steps |

<!-- educationpals-build-verified -->
