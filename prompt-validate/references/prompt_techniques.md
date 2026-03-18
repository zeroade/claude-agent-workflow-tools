# Prompt Design Techniques Reference

Read this when crafting or improving prompt variants during Phase 1/2.

## Driving Mechanisms (each variant should change mechanism, not just parameters)

| Mechanism | How it works | Best for |
|-----------|-------------|----------|
| **Instruction** | Direct commands telling AI what to do | Clear, simple tasks |
| **Example / Few-shot** | Provide concrete input→output examples for AI to learn from | Pattern replication, format control |
| **Counter-example** | Show bad outputs and ask AI to analyze what's wrong | Preventing known failure modes |
| **Question chain (CoT)** | Step-by-step questions forcing sequential reasoning | Detection, evaluation, complex judgment |
| **Constraint** | Define boundaries — what AI cannot do, must do first, etc. | Output format, safety, scope control |
| **Role / Persona** | Assign identity that drives behavior from within | Tone, expertise framing, perspective |

## Proven Strategies (validated across multiple scenarios)

| Strategy | Effect | When to use |
|----------|--------|-------------|
| **Bad specimen analysis** (give bad output to analyze) | Strongest activation of critical judgment | When known failure patterns exist |
| **Progressive question chain** (list → match → find violations) | Forces step-by-step decomposition, blocks fuzzy matching | Detection sentences, evaluation criteria |
| **Single hard constraint** ("must derive from X") | Focuses attention on one axis | When one quality dimension matters most |
| **Concrete material > abstract instruction** | AI reasons better with something to read than rules to follow | Almost always — default to this |
| **Prompt shape > prompt content** | Structure of the prompt matters more than word choice | All prompt design |

## Advanced Patterns

- **Step-Back Prompting**: Before answering, first reason about the higher-level concept or principle. Improves depth.
- **Self-Consistency**: Generate multiple responses via different paths, compare for agreement. Improves reliability.
- **Tree of Thought (ToT)**: Explore multiple reasoning branches before committing. Good for creative/divergent tasks.
- **ReAct**: Alternate between reasoning and action (tool use). For tasks needing external information.
- **XML/JSON structuring**: Use structural markup to increase instruction precision and output control.

## Anti-patterns (bad prompt topologies)

| Bad pattern | What it looks like | Why it fails |
|-------------|-------------------|--------------|
| Empty action as instruction | "Compare output vs criteria" | Has a verb but no judgment method — reader fills in their own |
| Undefined threshold | "No improvement for 3 rounds" | Looks like a condition but "improvement" is undefined |
| State description as trigger | "After 10 rounds, still not meeting criteria" | Describes a state but doesn't point to an action |
| Undefined granularity constraint | "Explore different directions" | Looks like a constraint but "different" is undefined |
| Single-dimension analysis | Analyzing only the object of a sentence, ignoring the verb boundary | Incomplete analysis pretending to be complete |
