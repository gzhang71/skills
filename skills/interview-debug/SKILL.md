---
name: interview-debug
description: Systematically debug a bug or failing test during a technical interview or coding session. Use when something is broken and you need to find and fix the root cause efficiently. Triggers on "debug this", "fix this bug", "test is failing", "something's broken", "error I can't figure out", "interview debugging".
disable-model-invocation: true
---

# Interview Debugging

Diagnose and fix bugs with methodical speed. The goal: find the root cause, not just silence the error.

## Arguments

`$ARGUMENTS` — describe the bug (error message, failing test name, unexpected behavior). Paste stack traces directly.

## Step 1: Reproduce first

Never debug what you haven't confirmed. Run it:

```
!`npm test 2>/dev/null || pytest 2>/dev/null || go test ./... 2>/dev/null || cargo test 2>/dev/null`
```

If `$ARGUMENTS` has a specific test or command, run that.

**Is it reproducible?** If not, note environment differences (OS, Node version, env vars, DB state).

## Step 2: Read the error carefully

Work through the stack trace top-to-bottom:
1. **What is the error type?** (TypeError, NullPointerException, 404, assertion failure)
2. **Where did it originate?** (first frame in YOUR code, not in a library)
3. **What was the expected vs actual value?**

Common patterns:
- `undefined is not a function` → called a method on null/undefined
- `Cannot read properties of null` → missing null check
- `ENOENT` / file not found → path issue, wrong working directory
- `401/403` → auth/permission issue
- `ECONNREFUSED` → service not running, wrong port
- Assertion `expected X received Y` → look at the setup/teardown for stale state

## Step 3: Bisect to the root cause

Start at the error origin, then work backwards:

1. **Add minimal logging** at the point of failure — what is the actual value?
2. **Trace backwards** — where was this value set? Is it what you expect?
3. **Check assumptions** — is the function being called with what you think?

```python
# Quick inspection pattern — add near the failure
print(f"DEBUG: {variable=}")  # Python
console.log('DEBUG:', { variable })  // JS/TS
fmt.Printf("DEBUG: %+v\n", variable)  // Go
```

## Step 4: Form a hypothesis

Before changing anything, state:
> "I think the bug is [X] because [Y]. I expect fixing [Z] will resolve it."

This keeps you from cargo-culting fixes.

## Step 5: Fix it

Make the minimal change that addresses the root cause:
- Fix the actual problem, not the symptom
- Don't add error-swallowing try/catch just to make the test green
- Don't remove the failing assertion — understand why it fails

## Step 6: Verify the fix

```
!`npm test 2>/dev/null || pytest 2>/dev/null || go test ./... 2>/dev/null`
```

- Does the originally failing test now pass?
- Do previously passing tests still pass?

## Output

```
## Bug Report

**Root cause**: [one clear sentence]

**Why it happened**: [the logical reason — off-by-one, race condition, missing null check, etc.]

**Fix applied**: [what changed and where — file:line]

**How to prevent this class of bug**: [optional but shows senior thinking]

**Tests now passing**: ✅ / ❌ [list any remaining failures]
```

## Common Interview Bug Patterns (quick reference)

| Symptom | Likely cause |
|---------|-------------|
| Works locally, fails in CI | Env var missing, path separator, timezone |
| Flaky test | Shared mutable state, async timing, test ordering |
| Off-by-one | Array indexing, pagination, date ranges |
| Infinite loop | Missing base case, mutating loop variable |
| Stale data | Missing await, cache not invalidated, transaction not committed |
| Wrong type | JSON parse, implicit coercion, `===` vs `==` |
