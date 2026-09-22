# LIKHA-SIS — Fresh-Start Consolidated Truth & Lessons
**Document purpose:** consolidate the durable, truthful knowledge learned across the legacy LIKHA-SIS build, LIKHA-SIS 0.2, and Project TANAW so a fresh LIKHA build can start smaller, clearer, safer, and more worthwhile.

**Snapshot:** 2026-09-23  
**Canonical product name:** **LIKHA-SIS 0.2**  
**Important naming correction:** Earlier project documents sometimes called the project “LIKHA-SIS 2.0”. That was a historical working label, not the authoritative product version. The user explicitly established **0.2** as the canonical name; **1.0 is reserved for the official release**.

---

## 0. How to read this document

This is deliberately a **truth document**, not another masterplan.

Each statement should be understood as one of:

- **VERIFIED / IMPLEMENTED** — supported by repository/project records or a reported completed checkpoint.
- **ACCEPTED** — a durable architectural/product decision, but not necessarily fully implemented.
- **PROPOSED / PILOT** — explored or intended, but not production authority.
- **REFERENCE** — useful knowledge from another project/repository; not automatically adopted.
- **DEFERRED** — intentionally postponed.
- **REJECTED / DO NOT REPEAT** — a direction that created risk, complexity, noise, or poor fit.
- **UNRESOLVED** — explicitly not proven yet.

The most important lesson is:

> **Do not confuse a well-written plan with a verified implementation.**

Repository state, executed tests, authoritative DepEd sources, and explicit owner decisions outrank prompts, generated reports, assumptions, and old chat summaries.

---

# 1. The original problem LIKHA-SIS was trying to solve

LIKHA-SIS began as a school information system intended to reduce the operational burden of school records and reporting.

The recurring teacher problems identified across the project were:

- repeated entry of the same learner information;
- fragile spreadsheets;
- manual attendance and grading work;
- repetitive report generation;
- difficult learner lookup;
- official DepEd School Forms requiring high-fidelity output;
- work continuing even when internet connectivity is poor;
- different teachers having very different levels of comfort with technology;
- security risks when learner information is stored casually;
- school workflows being forced into generic SaaS/dashboard patterns.

The later architecture therefore moved toward:

**teacher task → local working data → reusable domain logic → official outputs → optional synchronization**

rather than:

**browser → cloud database → CRUD screens**.

That distinction is one of the most important lessons from the entire project.

---

# 2. The evolution of the project

## 2.1 Legacy LIKHA-SIS

The legacy application accumulated a large amount of useful domain knowledge.

It included or explored areas such as:

- learner workflows;
- enrollment;
- attendance;
- grades;
- report cards;
- LARDO;
- nutrition;
- anecdotal records;
- schedules;
- teacher loads;
- class programs;
- School Forms;
- certificates;
- IDs;
- grading/transmutation;
- school settings;
- authentication/session handling;
- print/export workflows;
- Firestore-based persistence;
- React-based screens;
- dark mode;
- teacher/admin views.

The legacy build was valuable because it exposed real DepEd workflows and edge cases.

It also exposed a major problem:

> **The accumulation of features happened faster than the architecture was simplified.**

The result was a system with substantial working knowledge but too much coupling, too many feature assumptions, and a growing cost of safely changing foundational behavior.

### Legacy knowledge worth preserving

- actual teacher workflows;
- actual school-form requirements;
- grading/transmutation rules;
- attendance semantics;
- learner lifecycle concepts;
- schedule and teacher-load relationships;
- print safety requirements;
- known edge cases;
- tests that encode real business rules;
- lessons from Firestore authorization;
- mistakes caused by broad data access;
- lessons from dark-mode/print interactions;
- existing naming and school-context requirements where still applicable.

### Legacy knowledge that should NOT automatically be preserved

- old component structure;
- old routing architecture;
- old cloud coupling;
- old database schema merely because it exists;
- old UI screens merely because they were built;
- old feature count as a measure of product maturity;
- old authentication assumptions;
- old Firestore-specific implementation details;
- large feature migrations performed wholesale.

---

# 3. The first major lesson: the application became too complicated

The current realization that the project is complicated is itself an important project finding.

The problem is not simply “too much code”.

The deeper problem is **too many simultaneous concerns**:

1. DepEd compliance
2. learner data
3. teacher workflows
4. school governance
5. offline storage
6. encryption
7. authentication
8. authorization
9. synchronization
10. cloud hosting
11. official forms
12. Android
13. Windows
14. accessibility
15. adaptive UX
16. AI-assisted development
17. agents
18. skills
19. MCPs
20. hooks
21. tests
22. reporting
23. migration from legacy behavior

Trying to solve all of these at once made the project feel like an enterprise platform before its smallest trustworthy workflow was proven.

### Fresh-start principle

The new LIKHA build should prove **one complete, useful, secure teacher workflow** before expanding horizontally.

The target is not:

> “Build an entire SIS.”

The target is:

> **“Build the smallest trustworthy school-information workflow that proves the architecture.”**

---

# 4. Canonical LIKHA-SIS product direction

## 4.1 Product identity

LIKHA-SIS is intended to be:

> **Professional enough to be worth thousands of dollars. Simple enough for every teacher to use.**

It is a DepEd-oriented, teacher-first SIS with:

- native-first operation;
- local-first working data;
- offline capability;
- cloud synchronization as a separate subsystem;
- Windows as the full workstation;
- Android as a teacher-focused mobile client;
- Web/PWA as secondary convenience, not the architectural foundation.

## 4.2 Product surfaces

### Windows `.exe`
Primary full workstation.

Intended for:

- learner records;
- class management;
- grades;
- attendance;
- reports;
- official forms;
- administrative workflows;
- high-volume data entry;
- local/offline operation.

### Android `.apk`
Teacher-first mobile client.

Intended for:

- quick teacher workflows;
- attendance;
- learner lookup;
- selected class records;
- status/checking;
- lightweight updates;
- offline work.

Android should **not** be a shrunk copy of the Windows interface.

### Web/PWA
Secondary convenience surface.

It should never force LIKHA to abandon:

- local-first storage;
- native security;
- offline operation;
- provider-independent domain logic.

---

# 5. Canonical architecture

The durable architecture is:

```text
UI
  ↓
Application Services
  ↓
Domain
  ↓
Repositories / Ports
  ↓
Local Database / Platform Adapters
  ↓
SyncProvider
  ↓
Cloud Provider Adapter
```

## Rules

### UI
May understand presentation and user interaction.

Must not directly own:

- cloud queries;
- synchronization;
- authorization policy;
- core grading rules;
- database implementation details.

### Application layer
Coordinates use cases.

Examples:

- enroll learner;
- record attendance;
- submit grades;
- generate report;
- synchronize changes.

### Domain layer
Contains business rules.

Examples:

- grade calculations;
- learner status rules;
- term rules;
- report eligibility;
- attendance semantics;
- conflict rules.

### Repository ports
Define what the application needs.

They should not know whether the implementation is:

- SQLite;
- IndexedDB;
- Firestore;
- D1;
- Durable Objects;
- another provider.

### Infrastructure
Contains provider-specific implementation.

This is where Cloudflare, SQLite libraries, authentication SDKs, filesystem APIs, etc. belong.

### Sync
Must remain a separate subsystem.

Offline writes should succeed locally without waiting for the cloud.

---

# 6. Local-first is not an optional feature

The project repeatedly learned that “offline support” cannot simply mean:

> “Show an offline banner.”

The working database must be local.

Normal school work should continue when:

- internet is slow;
- internet is unavailable;
- cloud services are temporarily unavailable;
- synchronization is delayed.

The local database therefore becomes the device's working boundary.

### Expected behavior

```text
Teacher changes data
        ↓
Local transaction
        ↓
UI immediately reflects saved state
        ↓
Outbox/change record
        ↓
Sync later
```

Cloud availability should not determine whether an ordinary local school operation can be completed.

---

# 7. SQLite direction

SQLite became the preferred local database foundation.

The previous foundation plan established:

- a `LocalDatabase` port;
- ordinary SQLite as the compatibility baseline;
- migration-based schema evolution;
- repository isolation;
- restart persistence testing;
- transaction testing;
- migration idempotency testing.

### Critical distinction

Ordinary SQLite is **not automatically an acceptable production learner-PII database**.

Encryption at rest must be independently proven.

The earlier plan explicitly blocked real learner PII until:

- Windows at-rest protection is proven;
- Android at-rest protection is proven;
- OS-backed secure key storage is proven;
- logout/device-loss behavior is tested;
- copied database exposure is tested;
- backup exposure is tested;
- authorization scope is tested.

---

# 8. Security principles learned

Security is the highest product priority.

Priority order:

1. Privacy/security
2. Correctness
3. DepEd compliance
4. Teacher usability
5. Offline reliability
6. Maintainability
7. Zero billing
8. Performance
9. Implementation speed

## Non-negotiables

### Synthetic data only during development

Never use real learner PII in:

- tests;
- fixtures;
- screenshots;
- demos;
- AI prompts;
- development databases;
- sample exports;
- debugging output.

### Authorization must exist at a trusted boundary

Hiding UI elements is not authorization.

A user who is not allowed to access School A data must not be able to retrieve it through:

- direct API calls;
- repository manipulation;
- crafted requests;
- synchronization;
- alternate screens.

### School isolation

School A must never access School B data.

This must be enforced structurally, not merely by UI filtering.

---

# 9. Cloud architecture decisions

A major 10-scenario review was performed for the cloud direction.

## Recommended historical direction

**Cloudflare Worker + one SQLite-backed Durable Object per school**

Conceptually:

```text
Windows / Android
      ↓
SyncProvider
      ↓
Cloudflare Worker
      ↓
School-specific Durable Object
      ↓
SQLite
```

The reason this was attractive:

- school-level physical/architectural isolation;
- serialized coordination;
- transactional SQLite;
- a natural synchronization boundary;
- provider code can remain behind an adapter;
- free-tier behavior was considered preferable to silent billing.

## Next-best historical direction

**Cloudflare Worker + per-school D1**

This was retained as the runner-up if Durable Objects proved materially harder to maintain or scale for the actual synchronization model.

## Important status

This cloud design was **not supposed to be treated as production-proven**.

The project plan explicitly required a synchronization spike before adoption.

Required tests included:

- replay;
- duplicate delivery;
- revoked accounts;
- assignment changes;
- stale offline writes;
- partial failure;
- unauthorized pull filtering;
- realistic school traffic;
- free-tier limits.

---

# 10. Authentication

Better Auth was selected as the historical identity candidate for the cloud spike.

The intended boundary was:

```text
Client
  ↓
authenticated session
  ↓
Worker authorization
  ↓
school-scoped data
```

The client must not receive database credentials.

Authentication and authorization are separate concerns:

- authentication = who is the user?
- authorization = what may that user do?

The second is the more important boundary for school data.

---

# 11. Zero-billing discipline

A durable project rule emerged:

> **Do not introduce paid infrastructure or billing-enabled services without explicit owner approval.**

Preferred:

- free/open-source;
- free quotas;
- no payment method where possible;
- hard failure rather than silent billing.

Do not assume today's free-tier limits are permanent.

Capacity planning must re-check current official pricing/limits before deployment.

---

# 12. Teacher Experience System

One of the strongest LIKHA decisions was the adaptive interface system.

Every major interactive workflow should support:

### Efficient
For experienced users who want speed.

Characteristics:

- compact;
- keyboard-friendly;
- fewer interruptions;
- fast entry;
- dense but readable information.

### Comfortable — default
Balanced presentation.

Characteristics:

- clear hierarchy;
- normal target sizes;
- visible context;
- helpful but non-intrusive guidance.

### Guided
For users who need more assistance.

Characteristics:

- larger targets;
- larger text;
- clearer grouping;
- fewer simultaneous choices;
- persistent help;
- stronger confirmation;
- step-by-step flow where useful.

## Critical rule

Modes must have **functional parity**.

Changing mode must not:

- remove authorized features;
- change permissions;
- change business logic;
- infer age;
- infer competence;
- infer role;
- infer years of service.

The teacher chooses the presentation.

---

# 13. Premium design lessons

The project repeatedly rejected the idea that premium means decorative.

Premium LIKHA should come from:

- typography;
- spacing;
- hierarchy;
- information architecture;
- accessibility;
- speed;
- predictable interactions;
- calm visual language;
- strong empty/error/loading states;
- appropriate density.

Avoid:

- excessive gradients;
- glassmorphism;
- giant headings;
- decorative motion;
- dashboard-card spam;
- generic SaaS templates;
- “spreadsheet in a browser” interfaces.

### Teacher-first design question

Before designing a screen, ask:

> **“What is the teacher trying to finish?”**

Then remove everything that does not help complete that task.

---

# 14. Official DepEd forms

Official forms are compliance-sensitive.

A key lesson from the legacy system:

> Do not recreate authoritative official layouts merely because HTML/CSS looks easier.

When an authoritative Excel/PDF template exists, the authoritative template is the fidelity reference.

The preferred Windows pathway was:

```text
Tauri
  ↓
scoped local sidecar
  ↓
Java
  ↓
Apache POI / HSSF
  ↓
official .xls template
```

The populated native document should remain the fidelity authority.

Generated PDF is a representation, not the authority.

### Existing historical evidence

SF1 had been the first pilot of the template-driven form architecture.

Other forms remained on their existing renderer until explicitly migrated.

---

# 15. Legacy form and reporting lessons

Important legacy logic worth preserving only after independent verification:

- do not duplicate grading/transmutation logic;
- centralize grade computation;
- preserve official attendance semantics;
- preserve report-card requirements;
- preserve LARDO workflows;
- preserve nutrition relationships;
- preserve schedule/teacher-load relationships;
- preserve print safety;
- preserve deterministic report generation.

A particularly important lesson:

> **A calculation should exist in one authoritative place.**

Do not copy business formulas into multiple UI components.

---

# 16. Print safety

The legacy build exposed an easily missed problem:

Dark-mode UI and printable official documents are different visual worlds.

Official printables should remain appropriate for printing regardless of screen theme.

Historical print safety rules required components such as:

- ReportCard;
- CertificateGenerator;
- IDGenerator;
- SF1;
- SF2;
- SF4;
- NutritionStatus;
- NutritionConsolidator;
- ClassProgramGenerator

to render with a white print surface.

The audit also discovered that print CSS may live in a child component rather than the page component.

Lesson:

> **Audit the actual rendering boundary, not the file name that seems to own it.**

---

# 17. Accessibility lessons

Accessibility became part of product quality, not a late compliance checkbox.

Major workflows should be checked for:

- keyboard-only Windows operation;
- Android-sized touch targets;
- 200% text resizing/reflow;
- strong contrast;
- reduced motion;
- readable hierarchy;
- recovery from errors;
- understandable status states.

The UI acceptance gate that was planned for every milestone included:

- Efficient/Comfortable/Guided parity;
- keyboard verification;
- Android touch verification;
- 200% resize/reflow;
- offline/sync/error states;
- age-inclusive UX audit.

---

# 18. Development governance

The project evolved from ad-hoc AI prompting toward a project-brain model.

The durable hierarchy became:

```text
CLAUDE.md
    ↓
AGENTS.md / project operating rules
    ↓
Project memory
    ↓
Current handoff
    ↓
Active plan
    ↓
ADRs
    ↓
Source registry
    ↓
Skills
    ↓
Agents
    ↓
Hooks / MCPs / tools
```

The exact files changed between generations, but the principle is durable:

> **Do not keep critical project knowledge only in chat.**

---

# 19. Project memory architecture

The LIKHA foundation established a compact project-memory pattern.

Important documents included:

- `PROJECT-MEMORY.md`
- `CURRENT-HANDOFF.md`
- `ACTIVE.md` / active plan
- `SOURCE-REGISTRY.md`
- `DECISIONS.md`
- architecture documentation
- scenario rubric
- ChatGPT project handoff

### What belongs in memory

- durable facts;
- accepted architecture;
- important constraints;
- reusable lessons;
- known risks;
- verified tooling decisions;
- current checkpoint.

### What does not belong in memory

- transcript history;
- every command;
- temporary debugging output;
- speculative ideas;
- stale implementation details.

---

# 20. ADR discipline

Architecture decisions should be recorded as ADRs.

A decision should capture:

1. problem;
2. alternatives;
3. evidence;
4. decision;
5. consequences;
6. revisit trigger;
7. status.

Do not rewrite history to make it look as though an old decision was always correct.

If evidence changes, change the status and create a new decision.

---

# 21. 10-scenario decision discipline

For material architecture decisions, the project adopted a structured process:

1. Research authoritative sources.
2. Generate 10 viable scenarios internally.
3. Evaluate them against LIKHA priorities.
4. Challenge the strongest options.
5. Choose the recommended option.
6. Choose the next-best option.
7. Present only those two unless the owner asks for the full matrix.
8. Record the durable decision.

This applies particularly to:

- database;
- cloud;
- sync;
- authentication;
- hosting;
- framework;
- repository architecture;
- major dependencies;
- security architecture.

The point is not to produce a giant comparison document.

The point is to avoid falling in love with the first technically attractive solution.

---

# 22. External source registry

The project developed an Adopt / Pilot / Reference / Reject discipline.

### Adopt
Approved for production use.

### Pilot
Allowed only in a bounded experiment.

### Reference
Learn from it; do not couple production to it.

### Reject
Not a default choice.

Historical examples:

| Source / pattern | Historical status | Lesson |
|---|---|---|
| Tauri | Adopt | Strong native foundation |
| Tauri official plugins | Selective adopt | Minimum capabilities |
| Cloudflare Workers | Cloud target | Gateway/authorization boundary |
| Cloudflare SQLite Durable Objects | Target / spike required | School-scoped coordination |
| Cloudflare D1 | Runner-up | Conventional alternative |
| Better Auth | Pilot / intended adopt | Open identity candidate |
| rusqlite + SQLCipher research | Pilot | Encryption investigation |
| Official Tauri SQL | Compatibility baseline | Not sufficient alone for PII encryption |
| PowerSync | Reference | Useful sync patterns |
| Electric / PGlite | Reference | Useful local/reactive patterns |
| Turso/libSQL | Reserve | Alternative if architecture changes |
| Superpowers | Reference/adapt | Workflow ideas, not wholesale vendor |
| Anthropic skills | Reference/adapt | Progressive skill structure |
| Trail of Bits skills/config | Reference/adapt | Security/tool governance |
| wshobson agents | Reference/adapt | Narrow specialist agent patterns |
| huge community skill packs | Reject default | Context/trust/maintenance cost |
| global browser MCPs | Reject default | Persistent privilege/schema/context cost |

---

# 23. Skills

Skills were intended to hold **procedures**, not product truth.

Examples that became important:

- SQLite migrations;
- data privacy;
- age-inclusive teacher UI;
- architecture scenario decisions;
- memory curation;
- print safety;
- form safety;
- domain-specific auditing.

The durable lesson:

> **A skill should teach an agent how to perform a repeatable task. It should not become a second project constitution.**

Keep skills narrow.

---

# 24. Agents

Specialist agents were explored for expensive or repetitive reviews.

Useful conceptual roles:

- architecture reviewer;
- security/privacy reviewer;
- teacher UX reviewer;
- age-inclusive UI auditor;
- accessibility reviewer;
- reliability/testing reviewer;
- memory curator;
- reporting/evidence reviewer.

The project learned to avoid creating agents merely because agents are available.

Every agent adds:

- context cost;
- maintenance;
- possible conflicting instructions;
- another source of authority confusion.

Therefore:

> **Use a small number of narrow specialists where they materially improve quality.**

---

# 25. Hooks

Hooks were useful for enforcing narrow mechanical safeguards.

Examples explored/used:

- secret protection;
- targeted tests;
- architecture checks;
- completion verification;
- documentation nudges.

Important lesson from Project TANAW:

A hook is not automatically a repository-wide security scanner.

For example, a Claude `PreToolUse` secret hook may protect Claude Write/Edit flows but cannot automatically prove that:

- shell redirects;
- Git staging;
- generated files;
- arbitrary files

were scanned.

Therefore:

> **Do not interpret a hook's successful exit code as proof of a broader property than its documented input contract provides.**

---

# 26. MCP lessons

MCPs can be powerful but expensive in context, privilege, and maintenance.

The project learned to prefer:

- CLI;
- narrow skills;
- scoped tools;
- explicit workflows

when persistent MCP state is unnecessary.

Avoid installing a large collection of MCP servers simply because they exist.

MCPs should have a clear reason:

- what task becomes materially better?
- what data does it access?
- what permissions does it need?
- what is its failure mode?
- does it introduce another security boundary?
- can the same job be done more simply?

---

# 27. AI development environment history

The project historically used multiple development approaches.

The legacy project had a Strategist/Cline-style handoff.

The 0.2 foundation later established:

> **Claude Code CLI as the primary and authoritative development environment.**

That eliminated a separate prompt-generation handoff as the normal architecture.

However, the current user workflow has also established an important operational distinction:

- **Atria-CC is the Claude Code runtime being used for Project TANAW.**
- Do not assume Anthropic/Claude models are actually being used when the user says Atria-CC is running.
- For TANAW execution prompts, the user requested **Atria-CC prompts only**.
- ChatGPT's role in that workflow is audit/review/validation, not direct project execution.

This lesson can be transferred to LIKHA:

> **Always identify the actual execution runtime/model before designing an AI workflow around it.**

---

# 28. Project TANAW — what it taught LIKHA

Project TANAW is the district evolution of the TNHS SMEA Consolidator.

It is not LIKHA-SIS, but it contains several governance lessons directly useful to LIKHA.

## TANAW core governance chain

```text
Teacher
  ↓
Subject Coordinator
  ↓
School SMEA Coordinator
  ↓
School Head
  ↓
District MEA Coordinator
```

State model:

```text
Submit
  ↓
Certify
  ↓
Finalize
  ↓
Endorse
  ↓
Approve
  ↓
Lock
```

Evidence packet model:

```text
Evidence
  ↓
Subject MEA Packet
  ↓
School MEA Packet
  ↓
District MEA Packet
  ↓
Division Outputs
```

### Important TANAW semantic rules

- Missing is not zero.
- Submitted is not approved.
- Do not infer later governance state merely because data exists.
- Only approved evidence should enter official consolidation.
- Governance state must be explicit.
- Return-for-correction must be a real workflow.
- Auditability matters.
- No default ranking/league-table UX.

These are directly relevant to LIKHA.

---

# 29. TANAW's strongest conceptual lesson for LIKHA

TANAW exposed a broader principle:

> **School systems are not just databases. They are governed evidence systems.**

A learner record is data.

A grade submission is data plus an actor and time.

An approved report is data plus:

- provenance;
- state;
- authority;
- version;
- audit trail.

Therefore LIKHA should distinguish:

```text
Data
Evidence
Draft
Submitted
Reviewed
Approved
Final
Locked
```

rather than treating every stored row as equally authoritative.

---

# 30. TANAW's reporting lessons

The TNHS reference logic worth preserving in the reporting domain included:

- report-ready assignment:
  `status !== "waived"` and `report_version_id` exists;
- approved ECR metrics from `submission_versions.metrics`;
- GSA as arithmetic average of class/subject `metrics.gsa`;
- MPS learner-weighted using SPP learner count;
- MAPEH MA / PEH handled separately and then equal-averaged;
- proficiency bands:
  - 90–100
  - 80–89
  - 75–79
  - 65–74
  - 0–64;
- combined Term Assessment formula;
- BLIC replacing CIGP;
- Reading Gaps remain blank until authoritative evidence exists;
- PPTX must remain editable and deterministic;
- never guess Division workbook shapes.

These should not automatically become LIKHA features.

They should become **domain evidence** for deciding which reporting capabilities actually belong in LIKHA.

---

# 31. TANAW's security/tooling lessons

TANAW established useful operating rules:

- Auto Mode OFF during sensitive setup/audit;
- manual approvals for sensitive operations;
- never choose “don't ask again” casually;
- never force push;
- never expose secrets;
- never perform destructive hosted database operations without owner approval;
- keep project isolation;
- do not silently reuse LIKHA/TNHS settings;
- use read-only verification before mutation;
- use exact paths for mutations;
- stop if a command preview is visibly corrupted;
- if an approach fails 2–3 times, change methods;
- record **Issue → Workaround → Record**.

These are strong candidates for LIKHA's fresh governance.

---

# 32. Issue → Workaround → Record

This became one of the most useful process lessons.

Whenever a problem occurs:

```text
Issue
  ↓
Workaround
  ↓
Record
```

Example:

### Issue
A long shell/heredoc command became corrupted in an approval preview.

### Workaround
Use a small helper file, read it back, syntax-check it, execute it only after validation, then remove it if temporary.

### Record
Never approve a visibly corrupted command even when the intended operation appears harmless.

This prevents the same failure from being rediscovered later.

---

# 33. Another TANAW lesson: don't confuse checks

Several verification mistakes were identified.

Examples:

- current untracked-file counting was initially described as staging simulation;
- a Claude hook was nearly reused as a general secret scanner;
- `.env.example` was discussed as if verified before its final output was captured;
- a skill's installation integrity was verified, but CLI discoverability remained unresolved.

The general rule:

> **A check proves exactly what the command actually checked — nothing more.**

Use precise language:

- “syntax checked”
- “targeted test passed”
- “working tree clean”
- “staging preview showed 13 paths”
- “secret scan found zero real hits”
- “not verified”

Do not collapse these into “everything is good.”

---

# 34. Current 0.2 foundation status

The earlier 0.2 foundation successfully established substantial project infrastructure.

Historical completed foundation items included:

- concise project authority;
- architecture boundaries;
- specialist agents;
- skills;
- hooks;
- external source registry;
- React/TypeScript/Vite foundation;
- Tauri 2 skeleton;
- teacher experience discipline;
- Efficient/Comfortable/Guided system;
- adaptive teacher-workspace shell;
- global comfort switching;
- offline status presentation;
- task-first navigation;
- Guided-mode simplification;
- age-inclusive teacher UI skill;
- read-only UI age-inclusion auditor;
- compact project memory;
- source registry;
- current handoff;
- memory protocol;
- ChatGPT project handoff;
- ten-scenario decision discipline;
- memory-curator workflow;
- cloud target selection.

A later Wave 3F relay base was also committed historically:

- commit `61950ae`;
- branch `claude/likha-sis-wave3f-relay-base`;
- seven files changed;
- Claude workflow/harness/relay scripts/tests included.

That branch was pushed to its remote, but this should not be confused with the main product being production-ready.

---

# 35. Important historical 0.2 milestone state

At an earlier checkpoint, the 0.2 project had:

- M0–M18 implemented;
- account lockout;
- idle timeout;
- Audit Log schema/migration 15 and five tests;
- repository integration and audit instrumentation still unfinished at that checkpoint;
- reported Rust/frontend test counts;
- build success reported but not necessarily rerun at every later checkpoint.

Because the project evolved afterward, these numbers should be treated as **historical checkpoint evidence**, not current truth.

The fresh project must inspect the actual repository and rerun verification before claiming any of them remain true.

---

# 36. What should be carried into a fresh LIKHA build

## Carry forward as principles

### Product
- teacher-first;
- native-first;
- local-first;
- offline-capable;
- DepEd-aware;
- official-form fidelity;
- premium but calm;
- simple enough for every teacher.

### Architecture
- UI → Application → Domain → Repository → Infrastructure;
- provider-independent domain;
- local SQLite working database;
- separate synchronization subsystem;
- explicit authorization boundary.

### Security
- synthetic data only;
- encryption gate before real PII;
- OS-backed secrets;
- school isolation;
- trusted-boundary authorization;
- auditability.

### UX
- Efficient;
- Comfortable;
- Guided;
- user-selectable;
- functional parity;
- accessibility built in.

### Governance
- explicit workflow states;
- explicit authority;
- evidence provenance;
- audit logs;
- no implicit approval;
- no “missing = zero”.

### Engineering
- inspect first;
- small reversible changes;
- TDD for important logic;
- targeted verification;
- exact claims;
- durable memory;
- ADRs;
- source registry;
- Issue → Workaround → Record.

---

# 37. What should NOT be carried forward blindly

Do not start the fresh project by copying:

- the whole legacy repository;
- every legacy component;
- every old dependency;
- every old page;
- every agent;
- every skill;
- every MCP;
- every hook;
- every dashboard;
- every cloud integration;
- every form renderer;
- every old database table.

Do not use “we already built it” as proof that it belongs in the new product.

---

# 38. The biggest product simplification opportunity

The project should stop treating “SIS” as a requirement to build everything a school could possibly need.

Instead, define the smallest **teacher work loop** that delivers undeniable value.

A possible conceptual nucleus is:

```text
My Class
  ↓
Learners
  ↓
Record something once
  ↓
Reuse it everywhere
  ↓
Produce the required output
```

The product earns expansion only when this loop is:

- fast;
- correct;
- secure;
- offline;
- understandable;
- reusable.

---

# 39. The “single source of truth” principle

A major future simplification should be:

> **Enter information once; derive everything else.**

For example:

```text
Learner
   ├── enrollment
   ├── class membership
   ├── attendance
   ├── grades
   ├── interventions
   └── official forms
```

The teacher should not re-enter the same learner data for every output.

Likewise:

```text
Grade data
   ├── report card
   ├── class summary
   ├── analytics
   ├── official form
   └── SMEA evidence
```

should derive from a common authoritative domain model.

---

# 40. Another major simplification: generate instead of collect

Before creating a new form or workflow, ask:

1. Can this be generated from existing data?
2. Can the teacher avoid entering it?
3. Can an existing record be reused?
4. Can the system calculate it?
5. Can the system validate it automatically?
6. Can the workflow be reduced to one decision?
7. Can a required report be generated directly from authoritative data?

This “out-of-the-box” question should happen before optimization.

---

# 41. Recommended fresh product boundary

The fresh LIKHA should probably be organized around a few durable domains rather than dozens of pages.

A clean conceptual domain map:

```text
1. Identity & School
2. Learners
3. Classes / Teaching Loads
4. Attendance
5. Assessment & Grades
6. Records / Interventions
7. Reports & Official Forms
8. Governance / Audit
9. Sync / Device
```

Everything else should justify its existence against these domains.

---

# 42. The learner pattern

The learner should become a reusable architectural pattern.

Conceptually:

```text
Learner
 ├─ Identity
 ├─ Enrollment
 ├─ Class membership
 ├─ Attendance
 ├─ Assessment
 ├─ Grades
 ├─ Interventions
 ├─ Documents
 └─ Audit history
```

The UI should expose the parts relevant to the teacher's current task rather than forcing the teacher to navigate a giant learner profile.

---

# 43. Teacher workspace pattern

The teacher workspace should answer:

> **“What do I need to finish today?”**

Instead of displaying a generic dashboard full of cards.

Potential information hierarchy:

1. current class/task;
2. unfinished work;
3. urgent exceptions;
4. quick actions;
5. recent work;
6. deeper reports only when needed.

This is consistent with both LIKHA and TANAW lessons.

---

# 44. Governance should be explicit, not decorative

For sensitive records, a status should be meaningful.

Example:

```text
Draft
Submitted
Reviewed
Approved
Final
Locked
Returned
```

Each state should define:

- who can create it;
- who can transition it;
- what data becomes immutable;
- what audit event is recorded;
- what downstream reports may use it.

A green “Approved” badge without an enforced state transition is not governance.

---

# 45. Offline synchronization should be domain-aware

Do not use one generic conflict rule for everything.

For example:

- a draft note may tolerate one conflict strategy;
- attendance may require another;
- a finalized grade may require a much stricter rule;
- an official form snapshot may be immutable.

Therefore:

> **Conflict policy belongs to the domain.**

Generic last-write-wins should not be the default for sensitive records.

---

# 46. Official forms should be treated as adapters

The domain should not become shaped around an Excel cell layout.

Instead:

```text
Domain data
   ↓
Form mapping
   ↓
Official template adapter
   ↓
.xls / .xlsx / PDF / other required output
```

This prevents official forms from contaminating the core domain model.

---

# 47. Development workflow for the fresh build

Use:

```text
Understand
  ↓
Inspect
  ↓
Research
  ↓
Specify
  ↓
Plan
  ↓
Implement
  ↓
Test
  ↓
Independent Review
  ↓
Update Memory
  ↓
Stable Checkpoint
```

Not:

```text
Prompt
  ↓
Generate huge feature
  ↓
Fix errors
  ↓
Add another feature
```

---

# 48. Fresh-start milestone structure

A better sequence is:

## Phase 0 — Product truth
Define:

- what LIKHA is;
- what it is not;
- first teacher problem;
- smallest useful workflow;
- non-negotiable constraints.

## Phase 1 — Project brain
Establish only:

- `CLAUDE.md`;
- project memory;
- current handoff;
- active plan;
- ADRs;
- source registry;
- minimal skills;
- minimal hooks.

## Phase 2 — Shell
Prove:

- Tauri;
- React/TypeScript;
- Windows;
- Android target;
- adaptive UX;
- navigation;
- accessibility baseline.

## Phase 3 — Local data
Prove:

- SQLite;
- migration;
- repository;
- synthetic learner;
- transaction;
- restart persistence;
- recovery.

## Phase 4 — Security
Prove:

- encryption;
- key storage;
- device loss;
- copied DB exposure;
- authorization.

## Phase 5 — First vertical slice
Build one complete teacher workflow.

## Phase 6 — Offline synchronization
Only now add:

- outbox;
- sync envelope;
- cursor;
- tombstone;
- conflicts;
- cloud authorization.

## Phase 7 — Official forms
Add one authoritative form end-to-end.

## Phase 8 — Expand domains
Only after the architecture survives real use.

---

# 49. What “done” should mean

A milestone is not done because:

- files exist;
- code compiles in theory;
- an AI agent says PASS;
- a screenshot looks good;
- a prompt says it was implemented.

A milestone is done when:

1. the implementation exists;
2. targeted tests run;
3. the tests pass;
4. important edge cases are checked;
5. UI behavior is reviewed;
6. security assumptions are tested;
7. the result is documented;
8. project memory is updated;
9. Git state is understood;
10. the exact next task is known.

---

# 50. Fresh project governance rules

These should become the core constitution.

## Rule 1
**Repository truth outranks conversation memory.**

## Rule 2
**Executed verification outranks claims.**

## Rule 3
**Authoritative DepEd sources outrank assumptions.**

## Rule 4
**Synthetic data only until production privacy gates pass.**

## Rule 5
**No paid infrastructure without explicit approval.**

## Rule 6
**No provider-specific code in domain/UI.**

## Rule 7
**No cloud dependency for ordinary offline work.**

## Rule 8
**No hidden authorization in UI-only logic.**

## Rule 9
**No large feature migration without a narrow vertical slice.**

## Rule 10
**No new agent/skill/MCP without a specific job.**

## Rule 11
**No “PASS” without an actual check.**

## Rule 12
**No destructive Git/database operation without explicit approval.**

## Rule 13
**Use Issue → Workaround → Record.**

## Rule 14
**If an approach fails repeatedly, change the method.**

## Rule 15
**Prefer eliminating a workflow over making it faster.**

---

# 51. What the fresh LIKHA should avoid becoming

The project should explicitly avoid these failure modes.

### 1. The “everything SIS”
A huge application where every DepEd workflow is represented before the core workflow is excellent.

### 2. The “dashboard product”
Beautiful cards and charts without reliable underlying evidence.

### 3. The “cloud-first SIS”
Teachers cannot work because the server is unavailable.

### 4. The “form-shaped application”
The internal database mirrors every Excel cell.

### 5. The “AI-generated architecture”
Large quantities of agents, skills, MCPs, and prompts become the system instead of supporting it.

### 6. The “legacy museum”
Every old feature is preserved because deleting it feels like losing work.

### 7. The “security theater”
Hooks, badges, and scanners exist but actual authorization/encryption boundaries are not proven.

### 8. The “free until it bills”
A cloud service silently moves from free to paid.

### 9. The “teacher must learn the software”
The software's complexity becomes the teacher's problem.

### 10. The “perfect architecture before value”
Months of architecture work pass without a teacher completing a real task.

---

# 52. The most important lessons from the build

## Lesson 1
**Start smaller than feels necessary.**

## Lesson 2
**Architecture should reduce future work, not create more documents.**

## Lesson 3
**Local-first is a product behavior, not a database choice.**

## Lesson 4
**Security must be proven at boundaries.**

## Lesson 5
**DepEd fidelity matters more than visual approximation.**

## Lesson 6
**A beautiful UI cannot compensate for uncertain data authority.**

## Lesson 7
**A stored record is not necessarily an approved record.**

## Lesson 8
**A skill is not a governance system.**

## Lesson 9
**An agent is not automatically useful.**

## Lesson 10
**An MCP is not automatically worth its context and permissions.**

## Lesson 11
**Memory should preserve decisions, not transcripts.**

## Lesson 12
**Tests should protect domain behavior, not merely increase test count.**

## Lesson 13
**One excellent reusable pattern is worth more than ten incomplete modules.**

## Lesson 14
**The teacher's workflow is the primary unit of product design.**

## Lesson 15
**If the product feels complicated after a few days away, the architecture and product boundary probably need simplification.**

---

# 53. Proposed fresh-start success test

Before expanding LIKHA beyond its first vertical slice, a teacher should be able to:

1. open the Windows app;
2. work without internet;
3. find a synthetic learner;
4. perform the chosen teacher task;
5. save it locally;
6. close the app;
7. reopen it;
8. see the saved result;
9. understand its state;
10. generate the required output;
11. reconnect;
12. synchronize safely;
13. see that authorization remains correct;
14. recover from a failed sync.

If this sequence is excellent, LIKHA has a real foundation.

If this sequence is confusing, more modules will only multiply the problem.

---

# 54. Truth status summary

| Area | Truth status |
|---|---|
| Teacher-first product direction | **ACCEPTED** |
| Native-first direction | **ACCEPTED** |
| Tauri 2 | **ACCEPTED / foundation built historically** |
| React + TypeScript | **ACCEPTED** |
| SQLite local working database | **ACCEPTED / foundation planned** |
| Offline-first behavior | **ACCEPTED** |
| Efficient/Comfortable/Guided | **ACCEPTED / foundation built historically** |
| Cloudflare Worker + per-school SQLite DO | **RECOMMENDED TARGET / NOT PRODUCTION-PROVEN** |
| Cloudflare Worker + per-school D1 | **NEXT BEST / NOT PRODUCTION-PROVEN** |
| Better Auth | **PILOT / CANDIDATE** |
| Local encryption strategy | **UNRESOLVED / MUST BE PROVEN** |
| Real learner PII in development | **PROHIBITED** |
| Official template-driven forms | **ACCEPTED** |
| Apache POI/HSSF Windows path | **ACCEPTED DIRECTION** |
| Generic last-write-wins | **REJECTED FOR SENSITIVE RECORDS** |
| Huge agent packs | **REJECTED DEFAULT** |
| Global browser MCPs | **REJECTED DEFAULT** |
| Claude Code as primary historical LIKHA development environment | **ACCEPTED HISTORICALLY** |
| Atria-CC as TANAW execution runtime | **CURRENT TANAW OPERATIONAL FACT** |
| TANAW governance model | **REFERENCE / STRONG LESSON FOR LIKHA** |
| Full legacy feature set | **NOT AUTOMATICALLY CARRIED FORWARD** |

---

# 55. Final fresh-start thesis

The previous project was not wasted.

It produced something more valuable than a collection of screens:

- a clearer understanding of teacher workflows;
- a large amount of DepEd domain knowledge;
- awareness of official-form fidelity;
- security lessons;
- local-first architecture;
- adaptive teacher UX;
- synchronization requirements;
- governance concepts;
- AI-development governance;
- evidence-state semantics;
- a substantial record of what not to repeat.

The mistake would be to respond to that complexity by building another large system from all the accumulated pieces.

The correct response is to **compress the knowledge**.

The fresh LIKHA should therefore begin with:

> **One teacher. One class. One real job. One local database. One trustworthy workflow.**

Then prove:

> **secure → offline → recoverable → synchronized → reportable**

Only after that should LIKHA earn the right to become a full SIS.

---

# 56. Suggested fresh project authority files

A fresh repository should eventually have a deliberately small project brain:

```text
CLAUDE.md
AGENTS.md

docs/
  PROJECT-MEMORY.md
  CURRENT-HANDOFF.md
  ACTIVE-PLAN.md
  ADR/
  SOURCE-REGISTRY.md

.claude/
  skills/
  agents/
  hooks/
```

Keep the root authority short.

Put specialized knowledge in the appropriate document.

Do not recreate a huge collection of project instructions merely because the previous project had one.

---

# 57. First fresh-start checkpoint

The first fresh LIKHA checkpoint should NOT be “start coding all modules.”

It should answer, in writing:

### Product
- What exact teacher problem are we solving first?
- What is deliberately outside the first release?

### Domain
- What are the minimum entities?
- What is authoritative?
- What is derived?

### Security
- What is the smallest trusted boundary?
- How is local data protected?
- How is school isolation enforced?

### Offline
- What must work with zero internet?
- What is allowed to wait for synchronization?

### Governance
- Which records have states?
- Who can transition them?

### Forms
- What is the first official output?
- What is the authoritative template?

### UX
- What does Efficient look like?
- What does Comfortable look like?
- What does Guided look like?

### Engineering
- What is the first vertical slice?
- What test proves it?
- What evidence allows us to proceed?

If these answers are concise, LIKHA is ready to restart.

If these answers require dozens of pages, the scope is still too large.

---

## Closing principle

**Do not build the LIKHA-SIS we imagined.**

Build the **smallest LIKHA-SIS that the accumulated evidence says is worth building**.

Then let verified usefulness—not ambition—determine what comes next.
