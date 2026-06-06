---
name: codesignal-ica-mock
description: >
  Spins up a mock CodeSignal Industry Coding Assessment (ICA) with 4 progressive levels,
  realistic problem specs, stub files, and failing unit tests. Use this skill whenever the
  user mentions CodeSignal, ICA, industry coding assessment, progressive filesystem assessment,
  wants to practice multi-file coding problems, or wants to simulate a timed coding interview.
  Also trigger when a user says things like "set up a practice assessment", "give me a coding
  challenge with multiple levels", or "help me prep for a CodeSignal test".
---

# CodeSignal ICA Simulator Skill

## Overview

This skill simulates a CodeSignal Industry Coding Assessment (ICA) Progressive Filesystem
assessment. It generates a realistic 4-level problem set in a chosen domain and language,
with proper scaffolding: problem specs, stub files, and failing unit tests.

## Assessment Structure (from official CodeSignal spec)

| Level | Focus | Target Time | Approx Lines |
|-------|-------|-------------|--------------|
| 1 | Basic implementation, null returns for not-found | 10–15 min | ~15–20 |
| 2 | Data processing, aggregations, filtering, sorting | 20–30 min | ~30–45 total |
| 3 | Refactoring, encapsulation, design patterns | 30–60 min | ~90–130 total |
| 4 | Extend without breaking, optimal patterns | 30–60 min | ~110–160 total |

Each level builds on the previous codebase. The user unlocks levels one at a time (default)
or receives all at once (on request).

## Scoring Context

- Scores range from 200–600
- ~500+ corresponds to ~85th percentile — safe target for most company screenings
- Partial credit exists — passing Levels 1–2 solidly is better than rushing to Level 4 and breaking things
- **Modularity is explicitly scored** — OOP and encapsulation matter, not just correctness
  - Encapsulate data in classes, not plain dicts/objects
  - Think about extensibility from Level 1 — Levels 3 and 4 will punish messy early code
  - SOLID principles are relevant: single responsibility, open/closed

---

## Step 1: Wizard — Gather Preferences

Ask the user these questions (can be combined into one message):

1. **Assessment type** — ICA (Industry Coding Assessment) or GCA (General Coding Assessment)?
   See differences below — ask if they're unsure.
2. **Language** — JavaScript (default), TypeScript, Python, or other?
3. **Mode** — Reveal levels one at a time (simulates real assessment) or generate all 4 upfront?
4. **Domain** — Let Claude pick randomly (recommended, simulates real unpredictability) or choose?

If the user says "surprise me" or doesn't specify domain, pick randomly. Do NOT always default
to the same domain — rotate across: bank system, in-memory database, cloud storage, task manager, hotel booking, event ticketing, inventory system, ride sharing, subscription billing, social feed, file versioning, restaurant ordering.

---

## Assessment Type Reference

### ICA — Industry Coding Framework
- **Target:** Mid-to-senior software engineers
- **Duration:** 90 minutes
- **Format:** 1 project-based question, 4 progressive levels building on each other
- **Levels unlock one at a time** — code carries over between levels
- **Score:** 200–600, partial credit, not expected to finish all 4 levels
- **Retake policy:** 2 attempts per 180-day rolling window (new invitation required)
- **Style:** Real-world feature development, data processing, design patterns
- **This skill primarily simulates the ICA**

### GCA — General Coding Framework
- **Target:** Early career / new grad screening
- **Duration:** 70 minutes
- **Format:** 4 standalone algorithm-style questions, all visible from the start
- **Completable in any order** — questions are independent
- **Style:** Data structures, algorithms, implementation, problem solving
- **Shareable score** — one result can be submitted to multiple companies
- **More like LeetCode** — not progressive filesystem style

> If the user is preparing for a GCA, note that this skill is optimized for ICA-style
> progressive filesystem problems. For GCA prep, standard algorithm practice (LeetCode,
> HackerRank) is more appropriate. You can still generate standalone algorithm-style
> problems if they want GCA practice, but skip the multi-level progressive structure.

---

## Step 2: Generate the Assessment

### Domain Design Principles

Each domain needs:
- A core entity (e.g. Account, Booking, Item)
- 3–4 operations that build naturally in complexity
- Level 3 should introduce a meaningful new capability (users, permissions, events, or strategies)
- A Level 4 extension that stresses the architecture

### Confirmed Real ICA Domain Examples

Use these as reference for the structure and progression style. Do NOT copy them verbatim — generate new domains.

**Bank System**
- L1: `create_account`, `deposit`, `transfer` — returns None on failure, balance on success
- L2: `top_spenders(n)` — rank by outgoing transactions, format: `"accountId(total)"`, tie → alphabetical
- L3: `pay` with 2% cashback after 24hrs (timestamp-based), `get_payment_status` → `"IN_PROGRESS"` / `"CASHBACK_RECEIVED"`
- L4: `merge_accounts` (transfer balance + cashback + history), `get_balance(time_at)` — point-in-time balance lookup

**Cloud Storage**
- L1: `ADD_FILE`, `GET_FILE_SIZE`, `DELETE_FILE` — returns `"true"`/`"false"`/`""` (empty string for not found)
- L2: `GET_N_LARGEST(prefix, n)` — top N files by size with prefix match, tie → lexicographic order, format: `"name(size), ..."`
- L3: `ADD_USER(userId, capacity)`, `ADD_FILE_BY(userId, name, size)` — per-user capacity limits, `MERGE_USER`
- L4: `BACKUP_USER`, `RESTORE_USER` — file-level backup/restore per user, conflicts ignored

**In-Memory Database**
- L1: `set(key, field, value)`, `get(key, field)`, `delete(key, field)` — returns `""` for not found, `"true"`/`"false"` for delete
- L2: `scan(key)`, `scan_by_prefix(key, prefix)` — returns all fields sorted lexicographically, format: `"field(value), ..."`
- L3: TTL support — `set_at_with_ttl(key, field, value, timestamp, ttl)`, `get_at`, `scan_at`, `delete_at` — fields expire at `timestamp + ttl`
- L4: `backup(timestamp)` → returns count of non-expired records; `restore(timestamp, timestampToRestore)` — recalculates TTLs from current time

**Workers / HR System**
- L1: `ADD_WORKER(id, position, compensation)`, `REGISTER(id, timestamp)` toggle enter/leave, `GET(id)` → total office time
- L2: `TOP_N_WORKERS(n, position)` — top by time spent, tie → alphabetical, format: `"id(time), ..."`
- L3: `PROMOTE(id, newPosition, newCompensation, startTimestamp)`, `CALC_SALARY(id, start, end)` — salary across position changes
- L4: Double-pay time periods (not yet in this repo — good extension point)

**Key patterns across all domains:**
- Empty string `""` for not-found (not null/None in JS context)
- Boolean-like returns as `"true"`/`"false"` strings in command-style problems
- Top-N queries with ties broken lexicographically are very common at Level 2
- Level 3 almost always introduces either: time-based expiry, per-user scoping, or scheduled future events
- Level 4 almost always involves: backup/restore, merging entities, or point-in-time queries

### Important: Return Values, Not Exceptions

The real ICA rarely uses thrown errors. Return sentinel values instead:
- Not found → return `null` (Python/JS) or `""` (empty string in command-style problems)
- Action failed → return `false`, `null`, or `""` depending on the problem style
- Success → return the result value, or `true`/`"true"` for confirmations
- String-command style problems (storage, workers) use `""` empty string for failures
- Object-style problems (bank, database) use `null`/`None` for not-found
- Never throw errors for expected failure cases (duplicate, not found, insufficient funds)
- Match the style of the real spec exactly — the test format tells you which style to use

### Level 1 — Core Implementation
- Single file, blank implementation stub
- 3–5 methods on one class
- Basic CRUD + null/false returns for failure cases
- ~15–20 lines of implementation expected
- No design patterns yet
- Tests: 12–18 unit tests covering happy path + failure cases

### Level 2 — Data Processing
- Extends Level 1 (same file)
- Adds: filtering by prefix/property, top-N queries, sorting
- Sorting ties broken by lexicographic order (common real ICA pattern)
- Tests: 15–20 new tests, all Level 1 tests still pass

### Level 3 — New Capability + Encapsulation
- Introduces a meaningful new entity or system (users, permissions, event emitter)
- May add a second file
- All previous tests still pass
- Tests: 20–30 new tests covering new capability + integration

### Level 4 — Extension Under Pressure
- 1–2 new methods (backup/restore, merging, bulk operations)
- Must not break Level 1–3 tests
- Designed to reward clean architecture from earlier levels
- Tests: 10–15 new tests

---

## Step 3: File Structure to Generate

The real CodeSignal environment uses Mocha as the test runner with a `tests/` folder.
Mirror this structure:

```
assessment/
├── README.md              ← Full problem spec (see format below)
├── tests/
│   ├── level1Tests.js     ← Mocha tests, locked after passing
│   ├── level2Tests.js     ← Added when level 2 unlocks
│   └── sandboxTests.js    ← Empty file user can use for scratch testing
├── src/
│   └── solution.js        ← User's implementation file (blank stub)
├── package.json           ← Mocha setup
└── run_single_test.sh     ← Shell script to run one test by name
```

### README.md Format

Model after the real assessment format. Include these sections in this order:

---

**# [Domain Name] System**

**## Instructions**

*[2–3 sentences describing the overall task. Written in second person: "Your task is to..."]*

*Solving this task consists of several levels. Subsequent levels are opened when the current
level is correctly solved. You always have access to the data for the current and all previous levels.*

*You are not required to provide the most efficient implementation. Any code that passes the
unit tests is sufficient.*

*You can execute a single test case by running:* `bash run_single_test.sh "<test_case_name>"`

**## Requirements**

*[1–2 sentences about what the system does. Bold any important constraints, e.g.:*
***Note that this system should be in-memory: you don't need to work with a real database.***]*

Plan your design according to the level specifications below:

- **Level 1:** [one sentence summary]
- Level 2: [one sentence summary]
- Level 3: [one sentence summary]
- Level 4: [one sentence summary]

*To move to the next level, you need to pass all the tests at this level when submitting.*

**### Note**

*[Any important guarantees or constraints, e.g. "It is guaranteed that inputs will always be valid strings."]*

**## Level 1**

*[One sentence describing what this level adds.]*

- `methodName(param1, param2)` — description of what it does, what it returns on success,
  what it returns on failure (null/false/etc)

**### Examples**

*The example below shows how these operations should work:*

| Queries | Explanations |
|---------|-------------|
| `methodName("arg1", 5)` | returns true; explanation |
| `methodName("arg1", 5)` | returns false; already exists |
| `getSize("arg1")` | returns 5 |
| `delete("missing")` | returns null; does not exist |

---

*Repeat Level N section for each unlocked level, keeping locked levels greyed out in description only.*

### Interface file (src/[Domain]Interface.js) — locked, DO NOT MODIFY:
- Defines all method signatures with default empty return values
- `// 🔒 DO NOT MODIFY THIS FILE` header
- `module.exports = { [Domain]Interface }`

### Implementation file (src/[Domain].js) — user edits this:
- Extends the interface class
- Has stub methods to override (empty bodies)
- `// your implementation here` in constructor
- `module.exports = { [Domain] }`
- Tests import from this file only

### Test files (tests/levelNTests.js):
- Use Mocha/Chai style: `describe`, `it`, `expect(...).to.equal(...)`
- `// DO NOT MODIFY THIS FILE` header  
- Group tests by method using `describe` blocks
- Edge cases + failure cases
- For Level 3+: integration test at the bottom

### run_single_test.sh:
```bash
#!/bin/bash
npx mocha tests/ --grep "$1"
```

### package.json:
```json
{
  "name": "codesignalsim-[domain]",
  "scripts": {
    "test:l1": "mocha tests/level1Tests.js --reporter spec",
    "test:l2": "mocha tests/level1Tests.js tests/level2Tests.js --reporter spec",
    "test:l3": "mocha tests/level1Tests.js tests/level2Tests.js tests/level3Tests.js --reporter spec",
    "test:l4": "mocha tests/level*Tests.js --reporter spec",
    "test:sandbox": "mocha tests/sandboxTests.js --reporter spec"
  },
  "devDependencies": {
    "mocha": "^10.0.0",
    "chai": "^4.0.0"
  }
}
```

Each `test:lN` command runs that level AND all previous levels — catching regressions
without including tests for levels the user hasn't unlocked yet.

---

## Step 4: Delivery

### Always deliver the full zip upfront
Include all 4 LEVEL{N}.md files, all 4 test files, both src files, README.md,
package.json, and run_single_test.sh. The honor system handles progression —
the user opens LEVEL{N}.md files and runs `test:lN` commands only as they unlock each level.

The README.md (navigation guide, no level content) is always included.
Each LEVEL{N}.md contains the full spec header + all 4 summaries + only that level's detail.

### Commands to include in the delivery message:

```bash
# Install dependencies
pnpm install

# Run tests for your current level (also runs all previous levels)
pnpm test:l1   # working on Level 1
pnpm test:l2   # working on Level 2
pnpm test:l3   # working on Level 3
pnpm test:l4   # working on Level 4

# Full output with test names and pass/fail (increase head number if cut off)
pnpm exec mocha tests/level1Tests.js --reporter spec 2>&1 | head -60

# Run a single test by name
bash run_single_test.sh "test name here"
```

---

## Step 5: Coaching Mode

When the user is working through a level and asks for help:

**NEVER give the answer directly.** Always:
1. Ask a leading question that points toward the issue
2. If they're still stuck after 2 nudges, give a smaller hint (not the solution)
3. Only reveal the answer if they explicitly say "just tell me"

**Common coaching patterns:**
- Stuck on data structure → "What operations do you need to be fast? Think about lookup vs ordering separately."
- Filter vs mutation confusion → "Does `filter` change the original array, or return a new one?"
- Missing `this.` → "Are you sure you're accessing the instance property, not a local variable?"
- Wrong return type in comparator → "What type does a sort comparator need to return? A boolean won't work."
- `if (array.filter(...))` → "What does `filter` return when nothing matches — and is an empty array falsy?"
- Invisible character parse error → "Check for invisible/zero-width characters — this happens when copy-pasting. Retype that line manually."
- `localeCompare` for lexicographic sort → "For string comparison in a sort, what does `>` return vs what a comparator needs?"

**Observation log:** Keep a mental note of mistakes or gaps the user shows during coaching.
After they pass a level, offer: "I noticed a couple of things while you were working — want a quick tips summary before moving to the next level?"

---

## Step 6: Tips & Gotcha Tracking

Offer a tips summary after each level. Include only things observed in the session, plus
universally relevant ones:

**Always include:**
- `filter` returns a new array — reassign back to update the original
- `some()` for existence checks, not `filter()`
- Always use `this.` to access class properties
- Sort comparators must return a number, not a boolean
- Two data structures is fine — use the right tool for each job
- For lexicographic tie-breaking in sort: use `a.name.localeCompare(b.name)`, not `a.name > b.name`

**Add session-specific gotchas** based on what the user struggled with.

Format as a skimmable bullet list the user can print or reference.

---

## Step 7: Prep Reference (on request only)

If the user asks for a prep sheet or reference guide, generate a markdown file covering:
- Assessment scoring (200–600, ~500 = 85th percentile safe target, partial credit)
- **Read all level summaries before writing a single line** — the spec shows all 4 level summaries upfront; use this to design extensible data structures from the start
- ICA vs GCA differences
- Level time targets
- What to optimize for vs ignore
- Note-taking framework (start of level / while coding / after passing)
- Key design principles (2-level max nesting, arrays for filtering, objects for lookup)
- Debug checklist including invisible character gotcha
- Sanity check tips

---

## Quality Checklist Before Delivering

- [ ] Return values use `null`/`false` for failures, not thrown errors (unless spec says otherwise)
- [ ] README examples table uses real input/output pairs that match test expectations
- [ ] Root README.md is navigation-only (no level content)
- [ ] Each LEVEL{N}.md has full header + all 4 summaries + only that level's detail
- [ ] Levels already passed are bolded in summaries, future levels are plain text
- [ ] Level 1 tests run independently (no imports from other levels)
- [ ] All previous level tests still pass in later levels
- [ ] Stubs have NO method signatures — user infers from README
- [ ] No working implementation anywhere in delivered files
- [ ] Uses Mocha + Chai, not Jest
- [ ] `run_single_test.sh` is included and executable
- [ ] Level 3+ includes integration test at the bottom of test file
- [ ] Zip excludes `node_modules/`
- [ ] Zip named `codesignalsim-{domain-slug}.zip`
- [ ] package.json scripts are cumulative (`test:l2` runs levels 1+2, etc.)
