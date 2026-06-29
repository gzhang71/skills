---
name: interview-repo-review
description: Quickly orient yourself in an unfamiliar codebase during a technical interview or take-home assessment. Use when you've just been handed a repo and need to understand its structure, tech stack, entry points, data flow, and key design patterns within minutes. Triggers on phrases like "review this repo", "understand this codebase", "what does this project do", "walk me through this code", "interview repo".
disable-model-invocation: true
---

# Interview Repo Review

You've just been handed an unfamiliar codebase. Your goal: build a working mental model in under 5 minutes, fast enough to ask good questions and start contributing.

## Arguments

`$ARGUMENTS` — optional focus area (e.g., "focus on auth", "explain the API layer"). If blank, do a full orientation.

## Step 1: Fingerprint the repo

Run these in parallel to gather raw facts:

```
!`ls -la`
!`cat README.md 2>/dev/null | head -80`
!`cat package.json 2>/dev/null || cat pyproject.toml 2>/dev/null || cat go.mod 2>/dev/null || cat Cargo.toml 2>/dev/null || cat pom.xml 2>/dev/null`
!`find . -maxdepth 3 -type f -name "*.md" | grep -iE "ARCH|DESIGN|OVERVIEW|CONTRIB" | head -5`
```

## Step 2: Map the structure

```
!`find . -maxdepth 2 -type d | grep -v ".git\|node_modules\|__pycache__\|.venv\|dist\|build" | sort`
```

Look for: `src/`, `app/`, `lib/`, `cmd/`, `internal/`, `api/`, `services/`, `components/`, `db/`, `migrations/`, `tests/`.

## Step 3: Find entry points

Depending on the stack:
- **Node/TS**: `main.ts`, `index.ts`, `server.ts`, `app.ts`; check `scripts` in `package.json`
- **Python**: `main.py`, `app.py`, `manage.py`, `__main__.py`
- **Go**: `cmd/*/main.go`
- **Java/Kotlin**: `*Application.java`, `Main.kt`
- **Rust**: `src/main.rs`

Read the entry point and trace 2–3 levels deep.

## Step 4: Identify key patterns

Scan for:
- **API routes**: grep for `router`, `@app.route`, `@Get`, `app.get`, `http.HandleFunc`
- **DB models**: grep for `@Entity`, `class.*Model`, `schema`, `prisma`, `SQLAlchemy`
- **Config**: `.env.example`, `config.yaml`, `settings.py`
- **Tests**: `__tests__/`, `spec/`, `test_*.py`, `*_test.go`
- **CI**: `.github/workflows/`, `.circleci/`, `Makefile`

## Step 5: Synthesize

Produce a structured summary:

```
## Repo at a Glance
**Stack**: [languages, frameworks, major libraries]
**Purpose**: [1 sentence — what problem this solves]
**Architecture**: [monolith / microservice / serverless / library]

## Structure
- `<dir>` — [what lives here]
- ...

## Entry Points
- [file:line] — [what it does]

## Data Flow (if applicable)
[request/event → handler → service → DB → response]

## Key Design Decisions
- [pattern or constraint worth noting]

## Good First Questions to Ask
- [question about unclear design choice]
- [question about testing/deployment]
- [question about known pain points]

## Potential Weak Points / Gotchas
- [anything surprising, missing, or worrying]
```

If `$ARGUMENTS` specifies a focus area, expand that section and compress the rest.
