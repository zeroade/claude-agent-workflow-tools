---
name: prompt-validate
description: Prompt validation. Must invoke before modifying or designing any text that affects AI behavior. Runs three-stage isolation tests (challenge→respond→evaluate), minimum 10 rounds.
---

# Prompt Validation Flow

Invoke this skill before modifying or designing any text requiring AI judgment (= prompt).

## Phase 0: Define Expected Outcomes

Before any modification, answer:
1. **Goal**: What should this prompt achieve?
2. **Verifiable criteria**: How to judge success? (e.g., "blocks when X detected", "output contains Y")
3. **Baseline**: What is the current prompt? Record as V1.
4. **Target environment and noise level**:
   - **Sub-agent** — clean context, no noise. For prompts given to spawned agents.
   - **Hook injection** — light noise. For prompts injected via hooks, competing with other hook injections.
   - **Main node** (default) — heavy noise. For prompts in the main conversation, competing with CLAUDE.md, MEMORY.md, system reminders, conversation history, and all hook injections.

   If unsure, use main node.

## Phase 1: Sequential Iteration (Meet Criteria)

Each round runs a three-stage isolation test. The isolation matters: if the challenge agent sees the prompt, it designs easy tests; if the response agent sees expected answers, it pattern-matches instead of reasoning.

1. **Spawn challenge agent**: Give it the prompt's goal description (NOT the prompt content). Ask it to design test scenarios with traps.
2. **Spawn response agent**: Give it the prompt under test + challenge agent's scenarios (NOT the expected answers). Let it respond.

   **Noise injection** (skip for sub-agent environment):
   - **Hook injection level**: Prepend 2-3 paragraphs of unrelated hook-style instructions (skill evaluations, engineering reframes, goal derivation reminders).
   - **Main node level**: Prepend a realistic context block — system instructions summary (~500 chars), memory index excerpt (~500 chars), 2-3 hook injections, and a short simulated conversation exchange. Total noise: 1500-2500 chars. The noise should be thematically unrelated to the prompt under test.

   Generate noise fresh each round rather than reusing a fixed template — real context varies, so test noise should too.

3. **Coordinator evaluates**:
   1. What criteria did Phase 0 list? (enumerate them)
   2. Which sentence in the response maps to which criterion? (sentence-by-sentence mapping)
   3. Any criterion without a match, or matched but violated? (yes = invalid; all matched and not violated = valid)

Iterate per round until criteria are met. Record per round: version, direction, valid/invalid, reason.

When crafting new variants, read `references/prompt_techniques.md` for driving mechanisms, proven strategies, and anti-patterns.

## Phase 2: Parallel Exploration (Optimize)

Cumulative minimum 10 rounds (including Phase 1). Each variant must change the prompt's driving mechanism (see reference), not parameter tuning within the same mechanism. All variants must be tested at the target noise level.

**Stop condition**: 3 consecutive rounds where best metric does not exceed historical best = stop. Or 15-round cap.

## Phase 3: Record

Output report to `prompt_validation_reports/YYYY-MM-DD-<name>.md`.

Report format:
```
---
target: file path + specific location
goal: Phase 0 expected outcome
total_rounds: N
result: final version
review_after: SXX
---

## Validation Process
| Round | Phase | Direction | Valid? | Reason |
|-------|-------|-----------|--------|--------|
| V1 | sequential | baseline (control) | — | comparison baseline |
...

## Conclusions
- Effective approaches + why
- Ineffective approaches + why
- Final version vs baseline diff

## Post-deploy Tracking
(fill when review_after is due)
```

Record discovered patterns:
- Ineffective approaches → document as bad topology
- Effective approaches → document as good topology with applicable scenarios

## Phase F: Failure Path

After 10 rounds, historical best still below Phase 0 numeric criteria → enter Phase F. No numeric criteria → coordinator judges and records reasoning.

Bad example: "subjective 7/10 feels close enough, keep going" — continuing without recorded reasoning = flow violation.

Upon entering Phase F: assess whether criteria are unrealistic → revise criteria or abandon (keep original). Record failure reasons in report.
