---
title: conventions
type: note
permalink: conventions
---

## Structure — three layers, three mutation patterns

### Topic files — current state, edited in place
- Partition by problem domain. Notes that share a web of reasoning live in one
  file; strands that don't cross-reference live apart.
  - The technical artifact is one domain: decisions about its mechanics,
    electronics, physics cross-reference, so they consolidate together
    (e.g. `bench.md`), not one file per component.
  - The commercial/administrative strand is a separate domain: payment,
    contracts, work estimates, scheduling each stand alone
    (e.g. `commercial.md`), kept out of the technical files.
  - Genuinely independent technical sub-systems are separate domains too —
    partition by what shares reasoning, not by the label "technical".
- Within a domain, default to one evolving file. Split a sub-topic into its own
  file only once it has earned it: substantial content and its own relations
  straining the parent. Subdivision, not preemptive fragmentation.
- Notes evolve: update and supersede in place, don't append below stale versions.
- Each note: a clear heading, one concern. Decisions carry rationale — what was
  chosen, over what alternative, why.
- Link related notes with wiki-links instead of repeating content.

### Timeline — append-only chronological log
- One entry per working session or material event, newest at top, dated YYYY-MM-DD.
- Records what changed and why on that date: decisions, direction shifts, facts
  established. Points to the topic files it touched via wiki-links.
- Never edited after the fact — it is the audit trail. Corrections are new
  entries, not rewrites.

### Tasks — open / done, edited in place
- Two sections, Open and Done. A task moves from one to the other in place; it is
  not duplicated or logged twice.
- Each task is a single actionable item with enough context to act on without
  re-reading the conversation. Link the topic file or requirement it serves.
- Tasks hold work to do; decisions and rationale live in topic files, history
  lives in the timeline.

## Voice
- Plain language, expert register. State conclusions; carry the rationale, drop
  the deliberation.
- Declarative and terse — no hedging, no filler, no meta-commentary.
- Every decision records the alternative it beat and why.
- Link, don't repeat: one fact has one home, everything else points to it.
- Written for a reader who knows the project but not this conversation.
