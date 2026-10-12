# 0003: Spark lean mastery — a path is done when its skills are shown

- **Status:** Proposed — for review
- **Date:** 9 October 2026
- **Context:** [Spark personalisation roadmap](../spark-personalisation-roadmap.md), Phase 3 (lean version);
  builds on [0002: the AI proposes, code decides](0002-spark-ai-proposes-code-decides.md)

Today a learning path is done when its steps are done (3, 5 or 7). A student can finish every step and
still not be able to do what the path was for, and "4 of 5 steps" tells a mentor or parent nothing
about what the student learned. Mastery changes the finish line: **a path promises a few skills, and
it isn't finished until the student has shown each one** (or the safety cap is reached).

This is the lean version: the core loop only. The end-of-path summary, skipping known material,
skills in progress cards and skills shared across paths are left for later and need no rework.

## Decision

### 1. Every mastery path has a skill list

- When Spark plans a path it writes its skills: **2 for Quick, 3 for Standard, 4 for Deep**, each one
  sentence starting "Able to…" (e.g. *Able to spot a leading question*), at most 120 characters.
- Each outline step names the skill or skills it builds.
- Stored on the path (`bloom_paths.skills`, a short list with an id and the sentence) and on each step.
  Skills are fixed once the path is planned; nobody edits them afterwards.

### 2. Check questions point at a skill

- Each check question Spark writes names the skill it tests (by id), as it already names the idea.
- Code accepts only an id from the path's list; anything else is dropped (the question still counts
  for the learner state, just not for a skill).

### 3. When a skill counts as shown (the rule)

**A skill is shown when one check question for it was answered and reviewed "nailed".**

- "Nearly", "not yet" and blank answers don't count. Once shown, a skill stays shown in that path.
- The status is **worked out from the stored checks and reviews every time** (a pure, tested
  function), not stored separately, so it can never drift from the evidence.
- Same trust level as the rest of Spark: it's the student's own learning record, reviewed by Spark
  under code rules, not a grade.

### 4. Open skills get practice — with a cap

- When the student finishes the last step before the final reflection and a skill is still open,
  code adds **one practice step** for the open skills, placed before the reflection. Spark writes it
  like any adapted step (re-teach in a new way, then check that skill again).
- **At most 2 practice steps per path.** If a skill is still open after that, the path completes
  normally and shows *"1 skill not shown yet"*: the mentor sees it on the path and steps in. Nobody
  is stuck in a loop.

### 5. Progress is counted in skills

- Students see "By the end you'll be able to…" at the top of the path, each skill ticked when shown.
- Path pages, cards and lists for students, mentors and parents show **"2 of 3 skills shown"**.

### 6. Behind a switch, off by default

- A new feature switch **Mastery** (per group, built-in default **off**). It applies only where
  Adaptive Spark (`bloom_v2`) is on, because it needs steps written one at a time.
- Paths planned without mastery have no skills and work exactly as before, even if the switch is
  turned on later. Turning it off stops new skill lists; existing ones keep showing.

## Who proposes, who decides

| The AI proposes | Code decides |
|---|---|
| The skill list (wording for the age group) | How many skills (by depth); that every one is used by a step |
| Which skill each step and question targets | That the target is a real skill of the path (else dropped) |
| The practice step's content | Whether a practice step is needed, where it goes, and the cap of 2 |
| | When a skill is shown, and when the path is done |

## Measured, not assumed

- One log line per planned path and per practice step: `spark.skills` with skills planned, shown,
  practice steps added and skills left open at the end. Vague skill lists or many open skills are the
  quality signals to watch.
- The pilot's findings on check questions apply here too, because mastery reuses the same checks.

## Consequences

- A path can run one or two steps longer for a student who needs it: that is the point.
- "Students showed N% of the skills in their paths" becomes a real, explainable number.
- Recommendations (Phase 5) can later use shown and open skills.
- One small database change (a skills list on paths); no change to existing paths.
