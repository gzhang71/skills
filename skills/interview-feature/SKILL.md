---
name: interview-feature
description: Implement a feature quickly during a technical interview, coding assessment, or take-home. Use when asked to add functionality to an existing codebase under time pressure. Triggers on "implement this feature", "add X to the codebase", "build X", "coding challenge", "interview task", "take-home assignment".
disable-model-invocation: true
---

# Interview Feature Implementation

Deliver a clean, working feature fast. The goal is: correct first, complete second, polished third.

## Arguments

`$ARGUMENTS` — the feature to implement. Be specific if you can (e.g., "add pagination to GET /users", "implement user auth with JWT").

## Phase 1: Understand before touching code (2–3 min)

Answer these before writing a single line:

1. **What exactly is the feature?** Restate it in one sentence.
2. **Where does it fit?** Which files/modules will change?
3. **What are the inputs and outputs?** (API shape, function signature, UI trigger)
4. **What already exists?** Is there a pattern to follow (look for similar features)?
5. **What are the edge cases?** (empty input, auth required, pagination, errors)

```
!`git log --oneline -10`
```

Look at recent commits to understand how features are typically added.

## Phase 2: Plan (write it out, don't skip this)

Write a short implementation plan:

```
1. [File to create/edit] — [what to add]
2. [File to create/edit] — [what to add]
3. Tests: [what to test]
```

If the feature is ambiguous, state your assumptions explicitly — interviewers appreciate this.

## Phase 3: Implement

Follow the existing patterns in the codebase. Do NOT introduce new abstractions, libraries, or patterns unless necessary.

### Checklist as you code:
- [ ] Follow naming conventions (look at 2–3 existing files first)
- [ ] Handle errors the same way the rest of the codebase does
- [ ] No placeholder TODOs — implement it or explicitly state it's out of scope
- [ ] Input validation where other endpoints/functions validate
- [ ] Return the same shape as similar existing features

## Phase 4: Test

Run existing tests first to establish baseline:
```
!`npm test 2>/dev/null || pytest 2>/dev/null || go test ./... 2>/dev/null || cargo test 2>/dev/null`
```

Write at minimum:
- 1 happy-path test
- 1 error/edge-case test

Follow the existing test file structure — don't introduce a new testing pattern.

## Phase 5: Self-review (1 min)

Before calling it done, answer:
- Does it actually do what was asked?
- Does it break any existing tests?
- Are there obvious edge cases unhandled?
- Is there any leftover debug code?

## Output

Summarize what you built:
```
## Implemented: [feature name]

**Files changed**:
- `path/to/file.ts` — [what changed]

**How it works**: [2–3 sentences]

**Assumptions made**: [list if any]

**Not implemented / out of scope**: [list if any]

**To run**: [command]
**To test**: [command]
```
