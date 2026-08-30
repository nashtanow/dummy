# NastaNow — Gemini / Antigravity AI Development Workflow

> **This is the GLOBAL OPERATING RULES file and the single home for every shared definition.**
> `STEP_CREATION.md`, `IMPLEMENTATION.md`, and `PROJECT_PROGRESS.md` reference the sections here instead of re-defining rules. When another file needs a shared rule, it cites `README §N` — it does not restate it. If any file disagrees with this one on a shared rule, **this file wins** for process; **the architecture wins** for product truth (§1).

NastaNow is built by autonomous AI coding agents (primarily Gemini-family models in Antigravity), across many sessions, with no shared chat memory between them. These four files are the **control system** that lets any fresh agent safely continue from files alone.

**Architecture answers "WHAT IS NASTANOW?" — Workflow files answer "HOW DO GEMINI AGENTS SAFELY BUILD IT?"** The workflow files MUST NOT become a second architecture, a second roadmap, or a second source of product truth.

The four modules of this control system:

| File | Role | Answers |
|---|---|---|
| `README.md` | Global rules + shared definitions | The rules every agent obeys |
| `STEP_CREATION.md` | Planning engine | "What is the next safe, executable batch?" |
| `IMPLEMENTATION.md` | Execution engine | "How do I safely execute the authorized batch?" |
| `PROJECT_PROGRESS.md` | Shared execution state / memory | "Exactly where are we right now?" |

**Section map for fast lookup.** §1 hierarchy · §2 product boundary · §3 roles · §4 statuses · §5 problem-report format · §6 severity · §7 communication · §8 credentials · §9 stop conditions + error budget · §10 completion standard · §11 progress-is-not-proof · §12 scope + FUTURE firewall · §13 conflict detection · §14 domain guardrails · §15 reading protocol · §16 session-end report · §17 operating loop · **§18 pre-flight · §19 risk paths · §20 error classes · §21 autofix/user-decision gates.**

---

## 1. Source-of-truth hierarchy (CANONICAL)

Higher levels win. Never resolve a conflict by silently editing a higher level.

| Level | Source | Authority |
|---|---|---|
| **L1** | `NastaNow_FINAL_ARCHITECTURE.md` | **The only product/system authority.** Business rules, scope, actors, permissions, surfaces, workflows, state machines, DB/source-of-truth, APIs, security, RLS/RBAC, delivery, financial invariants, testing, deployment, future scope, acceptance criteria. |
| **L2** | Existing code | Evidence of what is built. Does **not** override architecture. |
| **L3** | `PROJECT_PROGRESS.md` | Execution state (planned/authorized/done/blocked). Never overrides architecture and is **never proof by itself** (§11). |
| **L4** | Workflow files (`README`, `STEP_CREATION`, `IMPLEMENTATION`) | Process discipline only. MUST NOT invent product requirements. |

**Conflict rules — MANDATORY:**

- Workflow rule vs architecture → **ARCHITECTURE WINS.**
- Code vs architecture → **STOP, report to user (§5), STATUS = NEEDS_REVIEW.**
- Progress vs code → **STOP, report to user (§5).** Do not silently rewrite history.
- Architecture internally contradictory → **STOP, report to user (§5).** Do not choose an interpretation.

---

## 2. Canonical product boundary (summary only — architecture is authoritative)

Node.js + TypeScript (strict) + Fastify; PostgreSQL/Supabase as the sole business source of truth; five surfaces (Customer, Restaurant, Delivery Panel, Admin Control Tower, Super Admin); **Phase-1 delivery is Admin-operated** through the Delivery Panel; **Delivery Partner automation is FUTURE** (§12 firewall); commission is **Admin-configurable per restaurant with immutable historical snapshots** (§14). This paragraph is a pointer, not a spec — for any real decision, read the relevant architecture section.

---

## 3. Roles (CANONICAL)

There are exactly **two** operating roles. **The model brand never determines the role** — Gemini Flash, Gemini Pro, or any model follows whichever role file the user invoked.

| Role | Follows | Does | MUST NOT |
|---|---|---|---|
| **Planning Agent** | `STEP_CREATION.md` | Determine the next small safe batch; classify steps; promote READY work; update progress; report problems; stop. | Implement code. Create a giant roadmap. Bypass or overwrite an active batch. |
| **Implementation Agent** | `IMPLEMENTATION.md` | Execute only the authorized batch; inspect real code; test; verify; update progress; report problems; stop. | Plan the next batch. Expand scope. Implement FUTURE features. |

`README.md` governs both. `PROJECT_PROGRESS.md` records both. Only **one** role is active per session.

---

## 4. Status vocabularies (CANONICAL — defined only here)

Two vocabularies exist because planning and execution are different lifecycles. Do **not** invent other statuses (no "almost done", "waiting", "partially ready", "pending-ish", "probably complete").

**Planning statuses** (used by `STEP_CREATION.md`, planning tables in progress):

| Status | Meaning |
|---|---|
| `READY` | Safe to authorize for implementation. |
| `BLOCKED` | An external dependency prevents safe work. |
| `NEEDS_REVIEW` | Ambiguity or conflict requires a human decision. |
| `DONE` | Historical marker only. |

**Execution statuses** (used by `IMPLEMENTATION.md`, the authorized batch):

| Status | Meaning |
|---|---|
| `PLANNED` | Authorized, not started. |
| `IN_PROGRESS` | Implementation started, not verified complete. |
| `COMPLETE` | Implementation **and** verification satisfied (§10). |
| `BLOCKED` | Cannot safely continue. |
| `NEEDS_REVIEW` | Human/architecture review required. |

---

## 5. Problem Report format (CANONICAL — the only format)

**Every meaningful problem, in every file and every chat reply, uses exactly these fields, in this order.** No file defines its own variant. Put the report **early** in the response — never bury it after a long narrative.

```text
PROBLEM:      [one sentence]
AFFECTED FILE: [README / STEP_CREATION / IMPLEMENTATION / PROJECT_PROGRESS / code / architecture]
AFFECTED STEP: [Step ID or N/A]
SEVERITY:     [CRITICAL / HIGH / MEDIUM / LOW / INFORMATION]   (see §6)
ERROR CLASS:  [SAFE_AUTOFIX / USER_DECISION / EXTERNAL_DEPENDENCY / ARCHITECTURE_CONFLICT / SECURITY_CRITICAL / FINANCIAL_CRITICAL / DATA_INTEGRITY_CRITICAL / ENVIRONMENT_FAILURE / VERIFICATION_FAILURE / INFORMATION]   (see §20)
EXACT EVIDENCE: [real error, test output, log line, command result, or the two conflicting statements]
CURRENT SAFE STATE: [what is definitely complete and verified right now]
IMPACT:       [what cannot safely continue]
USER INPUT / ACCESS / DECISION REQUIRED: [the exact thing needed from the user, or NONE]
RECOMMENDED NEXT ACTION: [the single best safe action]
STATUS:       [BLOCKED / NEEDS_REVIEW / INFORMATION]
```

`SEVERITY` and `ERROR CLASS` are **orthogonal and both required**: severity says *how bad it is* (§6, drives stop-vs-continue); error class says *what kind of problem it is and who resolves it* (§20, drives the response procedure). These are the **only** two problem vocabularies — do not invent a third.

When a problem is logged in `PROJECT_PROGRESS.md`, the same fields are used (see PROJECT_PROGRESS §7). The chat report and the file entry MUST match.

---

## 6. Severity taxonomy & escalation (CANONICAL)

Classify every problem before communicating. Classification decides **whether you stop** and **how loud you are**. Communicate everything except truly harmless `INFORMATION`.

| Severity | Definition | Required action |
|---|---|---|
| `CRITICAL` | Architecture, security, financial, data-loss, or irreversible issue. | **STOP immediately.** Report (§5) before any further work. |
| `HIGH` | Potential correctness / security / financial / scope issue. | **STOP the affected work.** Report immediately. |
| `MEDIUM` | May affect quality. | Continue **only if** architecture clearly permits; record it; mention it in the session-end report (§16). |
| `LOW` | Minor, safe to continue. | Record; mention in session-end report. |
| `INFORMATION` | Does not affect safe execution (e.g., a harmless warning). | Optional; interpret, do not dump raw noise. |

**Always CRITICAL/HIGH → immediate STOP + report (never silently downgraded for velocity):**
authentication/authorization bypass; RLS failure; cross-tenant access; IDOR; secret exposure; privilege escalation; replay vulnerability; state-machine bypass; insecure/destructive migration; **and any issue touching** payment state, commission, ledger, order total, settlement, refund, COD, immutable historical snapshot, or the zero-sum ledger invariant.

**Severity ↔ error-class mapping (fixed — do not reinterpret):** `SECURITY_CRITICAL`, `FINANCIAL_CRITICAL`, `DATA_INTEGRITY_CRITICAL`, and `ARCHITECTURE_CONFLICT` are **always** CRITICAL or HIGH → immediate stop. `EXTERNAL_DEPENDENCY` and `USER_DECISION` are **at least** HIGH for the work they block. `SAFE_AUTOFIX` is LOW/MEDIUM by definition — if a candidate "safe autofix" would touch a CRITICAL/HIGH domain (§19), it is **not** a safe autofix (§21).

**Frequency rule:** Report a CRITICAL/HIGH problem the moment it is discovered — **not** at the end of the step, the end of the batch, or the end of the session. The moment the issue is understood: STOP the affected work and tell the user. The user must never first learn of a major problem after an agent spent an hour working around it.

---

## 7. User communication rules (CANONICAL — NON-NEGOTIABLE)

The workflow **forces** agents to surface real problems. An agent MUST NOT silently: work around a blocker; fabricate/guess a credential or env var; skip or weaken a failing test; downgrade a security or financial issue; change architecture, business behavior, or scope; assume an external API, migration, or prior agent's work is correct; or mark work COMPLETE without evidence.

Whenever any of the following occurs, classify it (§6) and, unless it is harmless `INFORMATION`, report it (§5): missing/invalid/expired credential or env var (incl. `OPENAI_API_KEY`, Google Maps, Supabase, Vercel, payment providers); provider outage / quota / rate limit / network failure; auth / authz / RLS failure; database, migration, or schema issue; dependency/package/build failure; test failure or uncertain test result; any contradiction among architecture, code, and progress; unexpected existing behavior or code state; destructive migration decision; unclear business rule or ownership; unexpected scope expansion; tool/environment/deployment failure; partial or unverifiable work; **or any situation that can materially affect correctness, scope, security, finance, data, architecture, or timeline.**

For MEDIUM/LOW non-blocking issues: record them and continue only if safe, but still surface them in the session-end report (§16).

---

## 8. Credential & secret safety (CANONICAL)

**Credentials are configuration, not workflow state.** They never belong in these four files, logs, source comments, or commits.

MUST NOT: fabricate, guess, print, log, commit, or store any secret; claim an external integration "works" without a real, configured, validated credential.

When a required credential/env var is missing or invalid: mark the affected work **BLOCKED**, report (§5) exactly what needs it, and do **not** claim the dependent functionality was verified.

**OpenAI (and any external provider):** if `OPENAI_API_KEY` (or the provider's key) is missing/invalid, BLOCK + report. Use the provider's official, secure configuration mechanism (e.g., the OpenAI Platform setup flow) — never copy a key into chat, code, or these files. Record the *dependency* without recording the *secret*. If the architecture does not authorize an integration, do not introduce it.

---

## 9. Stop conditions & error budget (CANONICAL)

**STOP and report (§5) immediately when:** a CRITICAL/HIGH problem is found (§6); the pre-flight check fails (§18); a required credential is missing (§8); a source-of-truth conflict is found (§1); the architecture is silent on a required business decision; a conflict-detection trip fires (§13); the changed-file firewall trips on unrelated files (`IMPLEMENTATION §2` stage E); or safe continuation is impossible.

**Error budget — no infinite retries.** For a **normal technical error** (ERROR CLASS `SAFE_AUTOFIX`, `ENVIRONMENT_FAILURE`, or `VERIFICATION_FAILURE`), the agent gets **at most 3 autonomous diagnostic/fix attempts**, and:

- **Every attempt MUST carry a new hypothesis or a materially different diagnostic action.** Repeating the same change hoping for a different result does **not** consume a legitimate attempt — it is prohibited outright. `attempt 1: change A` → `attempt 2: change A again` is a violation, not a budget item.
- Track the count in `PROJECT_PROGRESS §0` (`autofix_attempts`).
- **After 3 bounded attempts fail → STOP** and report (§5), including: attempts made; what changed between them; what stayed identical; the evidence; and why further autonomous retries are unlikely to help or unsafe.

**CRITICAL/HIGH issues get NO budget** — zero autonomous attempts. `SECURITY_CRITICAL`, `FINANCIAL_CRITICAL`, `DATA_INTEGRITY_CRITICAL`, `ARCHITECTURE_CONFLICT`, and `USER_DECISION` mean **STOP IMMEDIATELY** and report (§20). Never "try a fix first" on these.

**Active-batch lock.** Only **one** implementation batch may be active. If one exists, an agent MUST NOT create a second, overwrite it, or append parallel authorized work — wait until it is closed.

---

## 10. Completion standard (CANONICAL)

A step is `COMPLETE` **only when all** of the following hold: implementation exists; acceptance criteria are satisfied; required tests actually **ran**; relevant verification succeeded; architecture is still satisfied; no unresolved CRITICAL/HIGH discrepancy remains; progress is updated; a commit/change reference exists; and the agent can state exactly what was verified.

**These do NOT, by themselves, prove completion:** a file exists; an endpoint returns 200; code compiles; TypeScript passes; lint passes; a migration file exists; a test was *written*; the UI renders. Compile-only is insufficient for any API / DB / RLS / RBAC / financial / state-machine work.

If evidence is insufficient → `NEEDS_REVIEW`. If safe continuation is impossible → `BLOCKED`. Never claim more than the evidence supports.

---

## 11. Progress is not proof (CANONICAL)

`PROJECT_PROGRESS.md` is shared memory, **not** truth. Before relying on a prior `COMPLETE` entry: inspect the relevant code/files, confirm migrations, and run the strongest practical check. If progress says `COMPLETE` but code/tests disagree → **STOP, report (§5)**; do not silently rewrite history.

---

## 12. Scope discipline & FUTURE-feature firewall (CANONICAL)

MUST NOT: redesign the product; create a second architecture or parallel roadmap; implement FUTURE features early; silently add features; refactor unrelated modules; or broaden an authorized step. If the architecture is silent on a required business decision → **STOP and ask (§5).**

**A workaround is permitted only if** it changes no architecture, weakens no security, changes no business truth, changes no scope, is temporary and explicitly recorded, is authorized by the architecture, and does not hide the original error. Otherwise → **STOP and ask.**

**FUTURE-feature firewall — Phase 1 MUST NOT contain any of these** (they belong to the deferred Delivery Partner era; if any appears in a plan, step, or code, STOP and flag it):

automatic/partner assignment · partner accept/reject offers · partner capacity or 3-order cap · proximity/clustering/batching · GPS live tracking · geofence · partner account lifecycle · partner reassignment/handoff · partner wallet / payout / KYC · route optimization.

**Phase-1 delivery is Admin-operated manual delivery on the Delivery Panel** (§14).

---

## 13. Conflict-detection checklist (CANONICAL)

At planning and implementation start, and whenever something looks off, actively check for these. **On any trip: STOP the affected work, report (§5), record it. Do not silently reconcile.**

architecture ↔ code mismatch · architecture ↔ progress mismatch · progress ↔ code mismatch · step-dependency mismatch · duplicate Step IDs · duplicate/parallel batches · stale architecture version (`PROJECT_PROGRESS §3`) · active-batch overwrite (§9) · unauthorized implementation · FUTURE-feature leakage (§12) · permission-string mismatch · RLS mismatch · API-contract mismatch · schema mismatch · test-expectation mismatch.

---

## 14. Domain guardrails (CANONICAL — single home for these rules)

Other files reference this section; they do not restate it.

**Order state machine.** `PLACED → ACCEPTED → READY → OUT_FOR_DELIVERY → COMPLETED`, plus `REJECTED / EXPIRED / CANCELLED / DELIVERY_FAILED`. Only the canonical `transition_order()` writes `orders.status`; the DB trigger + `order_transition_rules` allow-list enforce legality. Do **not** create a second state machine in TypeScript.

**Delivery (Phase 1).** Restaurant responsibility ends at `READY`; restaurants never execute delivery. Admin executes delivery on the Delivery Panel as the normal path. Queue is server-ordered oldest-eligible-first. Execution ownership is claimed when an Admin starts `READY → OUT_FOR_DELIVERY`; two Admins must not physically execute the same order; completion/failure is guarded and idempotent. All partner automation is FUTURE (§12).

**Money & ledger.** All money is integer paise (`BIGINT`). `ledger_entries` is append-only and zero-sum. Order commercial snapshots are immutable after creation. Phase-1 payments are COD + restaurant-owned UPI QR only — no gateway.

**Commission.** The **current** commission configuration is **mutable** and Admin-controlled (platform default + optional per-restaurant override, resolved override-then-default inside the business transaction). **Historical** commission applied to a committed order is **immutable** — orders snapshot the rate/amount/config-version and all downstream math reads the snapshot. **⚠ Never write "commission is immutable" unqualified** — that is true only of historical snapshots, never of the current config. Any file or comment that says otherwise is a defect → flag it (§13).

**Configuration & downstream systems.** Every business parameter lives in `configurations` (tiered: operational/financial/structural/security/emergency), never hardcoded. Redis, Realtime, notifications, maps, and AI are downstream and **never authoritative**; business truth must remain correct even when they fail. Distinguish provider failure vs application failure vs database failure vs configuration failure — never weaken a business invariant to force a green result.

---

## 15. Session-start reading protocol (CANONICAL — context economy)

Read **only** what the role needs. Do not re-ingest the entire architecture every session.

**Always read fully:** this `README.md`; `PROJECT_PROGRESS.md` (start with its CURRENT STATE block).
**Read selectively:** the architecture **sections relevant to the active step/batch**; the **specific** code files touched by dependencies or the active step.
**Read the full architecture only when:** performing a broad architecture audit, or the work is high-risk — architecture, financial, security, migration, or state-machine changes — or an ambiguity/conflict requires wider context.

**Efficiency rule:** this section optimizes context only. It never authorizes skipping verification, testing, error reporting, authorization checks, or the broader architecture review that high-risk work requires. Remove repetition, never safety.

---

## 16. Session-end report (CANONICAL)

Every session ends with this exact block. Claim nothing beyond the evidence.

```text
SESSION RESULT
ROLE:            PLANNING / IMPLEMENTATION
PRE-FLIGHT:      PASS / FAIL          (§18)
BATCH:           [range or NONE]
EXECUTION PATH:  FAST / FULL SAFETY / MIXED   (§19)
COMPLETED:       [verified step IDs, or NONE]
VERIFICATION:    [actual tests/checks run]
RECONCILIATION:  [RECONCILED step IDs, or N/A]
COMPLETION GATE: PASS / FAIL / N/A    (IMPLEMENTATION §11)
BLOCKED:         [step IDs or NONE]
NEEDS_REVIEW:    [step IDs or NONE]
PROBLEMS ENCOUNTERED: [short list, each with SEVERITY + ERROR CLASS, or NONE]
AUTOFIX ATTEMPTS USED: [count, or 0]
USER INPUT REQUIRED:  [exact thing(s) needed, or NONE]
FILES CHANGED:   [files or NONE]
NEXT SAFE ACTION: [one action]
```

---

## 17. Final operating loop

`PRE-FLIGHT (§18) → READ AUTHORITY → UNDERSTAND STATE → VERIFY REALITY → CLASSIFY RISK (§19) → PLAN → AUTHORIZE → IMPLEMENT → VERIFY → RECONCILE → RECORD → COMMUNICATE → STOP → NEXT SESSION.`

Every transition is explicit and reconstructable from files alone — **no agent should ever need the previous chat transcript.**

When uncertain, do not guess. When blocked, do not hide it. When credentials are missing, do not fabricate them. When security, money, or architecture is ambiguous, **STOP and inform the user.** The goal is safe, traceable, truthful, incremental delivery — **not** maximum autonomous activity.

---

# OPERATIONAL HARDENING GATES (§18–§21)

> §1–§17 define **what the rules are**. §18–§21 define **how fast an agent may move under those rules**. They add speed for low-risk work and hard stops for high-risk work. They **remove no rule above.**

---

## 18. PRE-FLIGHT CHECK (MANDATORY — before planning or implementation)

Run this **once at session start, before any new work.** It is short, deterministic, and machine-readable — do not turn it into an investigation. Answer each item `PASS` / `FAIL` / `N/A`.

```text
PRE-FLIGHT
[ ] 1  Canonical architecture file exists (NastaNow_FINAL_ARCHITECTURE.md)
[ ] 2  Architecture identity/version is known (PROJECT_PROGRESS §0/§3)
[ ] 3  PROJECT_PROGRESS.md is readable and its §0 block parses
[ ] 4  No conflicting active implementation batch (§9 lock)
[ ] 5  Current role is known (PLANNING or IMPLEMENTATION)
[ ] 6  Required dependencies/packages available
[ ] 7  Required environment variables present
[ ] 8  Required credentials available for the intended work (§8)
[ ] 9  Working tree / repository state is safe (no unexpected uncommitted changes)
[ ] 10 No unresolved CRITICAL/HIGH blocker affects the intended work
[ ] 11 Intended step is consistent with the current architecture
RESULT: PASS / FAIL
```

**On any FAIL affecting the intended work: DO NOT BEGIN NEW WORK.** Classify it (§6 severity + §20 error class) → report to the user (§5) → record it in `PROJECT_PROGRESS §7` and `§14` → continue only after the blocker is safely resolved.

Items that are `N/A` to the intended work (e.g. an unused credential) do not block. Record the one-line result in `PROJECT_PROGRESS §14`; do not paste the whole checklist into chat unless something failed.

---

## 19. RISK-BASED EXECUTION PATHS

Every step runs on exactly **one** path. Planning assigns it (`STEP_CREATION §5`); implementation honors it. This is a **workflow classification only — it changes no product rule.**

### FAST PATH — low-risk work, lighter verification, move quickly

Permitted for: isolated UI changes · formatting · harmless internal refactors · non-business developer-experience improvements · safe type corrections · clearly local implementation defects.

Verification may be proportionate (type-check + lint + build + the directly relevant tests) rather than exhaustive. Completion still requires real evidence (§10) — FAST PATH lightens *how much* is checked, never *whether* it is checked.

### FULL SAFETY PATH — MANDATORY, full verification, no shortcuts

**Required whenever the work touches any of:** money · payments · commission · ledger · database schema · migrations · order state machine · authentication · authorization · RLS · RBAC · privacy · delivery ownership · concurrency · security · financial calculations · configuration · data integrity · source-of-truth changes · destructive or irreversible changes.

**FAST PATH MUST NEVER be used for any item in that list.** If a step starts on FAST PATH and turns out to touch one of them, **stop, re-classify to FULL SAFETY PATH, and restart verification** — do not finish on the light path. When in doubt about which path applies, use FULL SAFETY PATH.

FULL SAFETY PATH requires the complete verification set applicable to the step (`IMPLEMENTATION §2` stage H) plus the high-risk reading protocol (§15).

---

## 20. PROBLEM CLASSIFICATION (ERROR CLASS)

Classify every meaningful problem as **exactly one** class before responding. The class determines the response; the severity (§6) determines the urgency. Record both in the report (§5).

| ERROR CLASS | Required response |
|---|---|
| `SAFE_AUTOFIX` | Agent may repair autonomously within the §21 boundary and the §9 budget. |
| `USER_DECISION` | **STOP** affected work and ask the user (§21). |
| `EXTERNAL_DEPENDENCY` | Missing/invalid credential, API key, quota, provider outage, external account access. **STOP** affected work, mark `BLOCKED`, tell the user (§8). |
| `ARCHITECTURE_CONFLICT` | **STOP immediately.** Do **not** choose an interpretation. Report (§1, §13). |
| `SECURITY_CRITICAL` | **STOP immediately.** Never downgraded for velocity (§6). |
| `FINANCIAL_CRITICAL` | **STOP immediately.** |
| `DATA_INTEGRITY_CRITICAL` | **STOP immediately.** |
| `ENVIRONMENT_FAILURE` | Diagnose safely within the §9 budget; if unresolved, STOP and report. |
| `VERIFICATION_FAILURE` | **Never mark the step COMPLETE.** Diagnose within the §9 budget (`IMPLEMENTATION §3`); report if unresolved. |
| `INFORMATION` | Record when useful; do not interrupt execution. |

Distinguish provider failure vs application failure vs database failure vs configuration failure before classifying (§14) — never weaken a business invariant to force a green result.

---

## 21. AUTOFIX BOUNDARY & USER-DECISION GATE

This section exists to **maximize speed while preserving user control.** Get it right and the agent moves fast on the small stuff and never quietly decides the big stuff.

### AGENT DECIDES — bounded autonomous repair (SAFE_AUTOFIX)

The agent may repair autonomously **only when the fix is unambiguously local and does not alter business truth**: import errors · obvious TypeScript errors · obvious null/type mismatches · formatting · lint issues · straightforward local implementation defects · obvious test-setup defects · clearly deterministic API-contract mismatches · clearly local non-business implementation mistakes.

Autonomy is additionally conditional on the decision being: implementation-level · local · reversible · clearly implied by the architecture · non-financial · non-security · non-business · non-destructive. Subject to the §9 error budget (max 3 new-hypothesis attempts).

### USER DECIDES — MUST STOP AND ASK (USER_DECISION)

The agent MUST NOT change any of these on its own initiative; **STOP and ask the user** (§5):

business logic · architecture · database schema · migration strategy · order state transitions · payment semantics · ledger behavior · commission semantics · configuration ownership · authentication · authorization · RLS · RBAC · security boundaries · source of truth · retention · recovery semantics · external provider or account choice · irreversible data transformations · data loss · scope.

**Tie-breaker:** if a fix is not clearly inside the AGENT DECIDES list, it belongs to USER DECIDES. Ambiguity resolves toward asking, never toward acting. A fix that is "probably fine" in any domain from §19's FULL SAFETY PATH list is a `USER_DECISION`, not a `SAFE_AUTOFIX`.
