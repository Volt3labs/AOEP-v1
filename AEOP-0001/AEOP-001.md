# AOEP-001 — Community Balancing with Dual-Stage Axie Score Voting Mechanism Proposal

## Preamble

- **AOEP Number:** AOEP-0001  
- **Title:** Community Balancing with Dual-Stage Axie Score Voting Mechanism Proposal
- **Description:** A lightweight governance and experimentation process allowing allowlisted contributors to submit Rune and Charm balance proposals in JSON format, select the top two proposals via Axie Score–weighted voting, trial both during the off-season, and finalize one for official season deployment through a second Axie Score vote.  
- **Author:** Nova/Psylo @Volt3labs
- **Status:** Draft  
- **Type:** Governance / Game Balance Process  
- **Created:** 2026-01-16  

---

## Abstract

This proposal defines a low-resource, off-season balance governance loop for Axie Infinity in which selected contributors submit validated Rune and Charm JSON proposals. The community selects the top two proposals using Axie Score–weighted voting. Both proposals are deployed sequentially during the off-season as live trials. A second Axie Score–weighted vote selects the final winning proposal, which becomes the canonical balance configuration for the next official season.

## Update #1 — Technical Feasibility & Primary Constraints

After discussion with Sky Mavis and community members, there is broad conceptual support for the idea of a community-driven balance council and off-season experimentation process.

However, several technical constraints were identified as blockers.

### 1. Risk of Major Regressions from Small Balance Changes

The primary concern is that even minor numerical adjustments can introduce severe bugs, despite not changing game logic directly. The exact failure modes were not detailed, but may include client instability, state corruption, desynchronization, or unintended interaction chains.

> “The reason for no balancing right now is due to the fact that major bugs can appear with balance changes and given the current resources for the game, it might not be feasible to make changes.” — Jaaster

Without strong automated safeguards, each balance iteration would require extensive manual QA and engineering validation, making frequent changes too costly.

This implies that the first prerequisite for a low-resource Balance Council is: **Making the client and server resilient to frequent balance configuration changes through rigorous automated testing (data validation, simulation, integration, and regression).**

Only if balance patches can be deployed safely, repeatedly, and cheaply does a community-driven iteration loop become operationally realistic.

### 2. Balance Data Coupling to Client Build and Multiple Services

A second constraint is architectural: balance data is not currently isolated to a single hot-swappable server configuration.

> “In theory it’s doable. In practice it’s not as simple as just changing config here and there. The client has its own data that needs rebuild / recompile. It also affects other services like marketplace, minting, depositing… so any changes made would require a non-trivial amount of effort.” — Hellscream

This means that a balance update can require:
- Client rebuilds and redeployments  
- Coordinated updates across multiple backend services  
- Multi-team validation and release orchestration  

For the proposed process to be lightweight, balance data must become:
- Versioned  
- Centrally defined  
- Automatically propagated to all dependent systems via tooling (release scripts, shared artifacts, runtime-loaded configs)

Without this decoupling and automation, even a perfect voting and testing process would still result in high operational overhead.

### 3. Information Asymmetry and Market Fairness

A third issue raised by both Sky Mavis and community members is the risk of **unfair market advantage**:

- Early knowledge of upcoming balance changes could be used for:
  - Axie accumulation  
  - Rune and charm speculation  
  - Market manipulation  

This creates asymmetry between insiders and regular players and must be mitigated through strict disclosure timing, snapshot rules, and possibly delayed economic activation. This concern is orthogonal to the technical


---

## Pre-requisite — Client Hardening & Automated Balance Safety Pipeline

To make frequent community-driven balance updates feasible without scaling QA and engineering cost, three technical foundations are required:

1. **Light Client / Server Hardening**  
2. **Automated Balance Safety Pipeline**  
3. **Automated Balance Propagation Across Client & Services**

### 1. Light Client / Server Hardening (Minimal Code Changes)

Small, targeted engineering work is needed to make the game safely testable and robust to data changes:

- Ensure runes and charms are fully data-driven (no hidden hardcoded assumptions)
- Add runtime assertions for:
  - Invalid stat ranges
  - Illegal state transitions
  - Cooldown, energy, and turn-order invariants
- Expose deterministic battle simulation mode:
  - Fixed RNG seeds
  - Headless execution
  - Full state logging
- Add test hooks:
  - Load arbitrary balance JSON at startup
  - Fast-forward battles
  - Capture replays for regression testing

### 2. Automated Balance Safety Pipeline

Every balance proposal must pass an automated pipeline before voting or deployment:

- **Data validation:** schema, ranges, referential integrity, diff vs canonical
- **Battle simulation:** thousands of headless matches, no crashes, no infinite loops, no NaNs
- **Client smoke tests:** launch client, open key screens, start and finish battles
- **Exploit guards:** detect infinite stacking, zero-cost loops, stat overflows
- **CI gating & rollback:** hard pass/fail, versioning, one-click revert

### 3. Automated Balance Propagation Across Client & Services

Balance changes currently affect not only the game server but also the client build and multiple backend services (marketplace, minting, deposits, indexing). To avoid manual, multi-team coordination for each iteration, an automated propagation system is required.

- Establish a **single, versioned source of truth** for balance data (`runes.json`, `charms.json`, `balanceVersion`)
- Generate per-service derived artifacts from this source:
  - Client bundle or runtime-loaded asset
  - Marketplace metadata
  - Minting and deposit validation tables
  - Indexing / analytics mappings
- Implement a **release script** that:
  1. Validates balance data
  2. Builds all derived artifacts
  3. Publishes them with the same version tag
  4. Triggers reload / cache refresh in all dependent services
  5. Runs basic smoke tests
  6. Registers rollback to previous version

If balance data is currently embedded in the client and requires rebuild/recompile, it must be moved to a runtime-loaded or versioned asset system. This decoupling is necessary so that off-season balance experiments can be deployed without shipping a new client for each iteration.


## Mitigation for Market fairness — Information Asymmetry with Anonymous Voting

To keep the system low-resource while allowing open debate and advocacy, the mitigation can rely on **uncertainty of outcome rather than enforcement**.

#### Core Principle: Uncertain Final Outcome

- At least **two balance proposals** must always reach the off-season trial.
- Proposers are free to:
  - Publicly explain their ideas
  - Defend their design
  - Advocate for their proposal
- However, **all votes are anonymous** and only final tallies are published.

This means:

- No proposer can know with certainty:
  - Which proposal is leading
  - Whether their proposal will win Vote #1
  - Whether it will win Vote #2
- Even with strong community support, the final outcome remains probabilistic until the vote closes.

#### Why This Reduces Market Manipulation

- A proposer may believe their balance change is good, but:
  - Another proposal with opposite economic impact is competing
  - Anonymous voting prevents reliable prediction of the winner
- Any attempt to pre-position in the market becomes speculation, not privileged arbitrage.
- There is no guaranteed “inside information advantage,” only personal belief.

#### Operational Simplicity

This approach requires only:

- Anonymous voting (no voter identity disclosure, only aggregate results)
- Minimum of two competing proposals
- Simultaneous public release of trial data
- Final balance reveal only after Vote #2

No trading locks, no asset audits, no compliance enforcement, and no monitoring infrastructure are required, keeping the process lightweight and scalable.

---

## Balance council rationale

Balance decisions in competitive games often lack transparency, experimental rigor, and community legitimacy. This system formalizes balance iteration as a scientific process: hypothesis (proposal), validation, experiment (off-season trial), and conclusion (final vote).

Full DAO-style governance and on-chain voting are considered but can be disregarded if too slow or too complex. Pure Sky Mavis balancing is not possible due to limited resources and insufficient time to properly research, test, and iterate on evolving metas (all resources focused on Atia's Legacy). The dual-stage voting model combines expert filtering with broad participation while ensuring that only empirically tested changes can reach the official season.

The off-season is chosen as the experimentation window because instability is expected and risk is contained. JSON-based parameterization allows numerical tuning without client updates, minimizing engineering overhead and enabling rapid iteration.

---

## Specification

### 1. Roles

- **Allowlisted Proposers:** Submit balance proposals in canonical JSON format. Allowlisted Proposers can be Top players, Analyst, Streamers, anyone recognized for his deep knowledge of Axie Origins game mechanics.
- **Voters:** Axie Score–eligible accounts participating in weighted voting.
- **Operators:** Small internal team responsible for validation, deployment, and vote tallying.

### 2. Proposal Format

Each proposal includes:
- `runes.json` (see runes.json example in the folder)
- `charms.json` (I can also prepare it if you ask me)
- `summary.md` (proposer explains goals, expected impact, risks, evaluation metrics)

All numeric parameters are isolated in a `stats` object to allow tuning without modifying mechanics.

### 3. Validation

A single automated script performs:
- JSON schema validation
- Referential integrity checks
- Change magnitude limits
- Diff generation against the current canonical version

Only validated proposals proceed to voting.

(This is a draft proposal - I have generated a script but did not test it so I won't include here - I can test it and share it here on demand)

### 4. Vote #1 — Top-2 Selection

- Axie Score snapshot taken at vote start.
- Formula can be:
--Weight function: `min(cap, sqrt(AxieScore))`. Square root softens whale dominance - Instead of influence growing linearly (Score = 100 → weight 100), it grows slower: Score 100 → weight 10 & Score 10,000 → weight 100
--Or we can axieScore*axsStaked like governance portal
- Voting method: single-choice or ranked-choice.
- Output: Two proposals with highest weighted totals.

### 5. Off-Season Trial

- Deployment via seasonal configuration switch. No client patch, we just change the JSON file that is called by the client and return by the server
- Schedule:
  - Week 1: Proposal A active.
  - Week 2: Proposal B active.
- Metrics collected from existing telemetry:
  - Win rate
  - Pick rate
  - Meta diversity
  - Exploit detection
  - Player sentiment

### 6. Vote #2 — Final Selection

- Same electorate and weighting rules.
- Options: Proposal A vs Proposal B.
- Winner becomes the official season balance.

### 7. Canonical Release

Winning JSON is versioned and published as the next season’s canonical balance configuration and can be rolled back if critical issues are detected.


## Reference Implementation

A minimal implementation consists of:

1. JSON Schema + validation script  
2. Axie Score snapshot (existing already)
3. Vote tally script with weight function  (can be hosted on Governance portal or on my website)
4. Seasonal config switch for JSON versions  (upload new JSON script)

All components are off-chain and can be implemented within one week by a small team or a single dev.

---

## Security Analysis

Primary risks include:

- **Vote manipulation:** Mitigated through Axie Score snapshotting, weight caps, and eligibility thresholds.
- **Malicious proposals:** Prevented via schema validation, diff review, and change limits.
- **Deployment errors:** Reduced through versioned artifacts and fast rollback.

---

## Economic Analysis

This system improves balance legitimacy and player trust at minimal operational cost. By enabling controlled experimentation and community participation, it can increase long-term retention and competitive integrity while avoiding the overhead of full on-chain governance or complex live-ops tooling. Regular, transparent meta shifts driven by this process can also stimulate economic activity by renewing demand for Axies as players adapt to evolving competitive environments.

## AOEP-001 Diagram

```mermaid
flowchart TD
  A[Allowlisted Proposers<br/>Top players / Top Analysts] --> B[Submit Proposal<br/>runes.json + charms.json + summary.md]
  B --> C[Validation Script<br/>schema + placeholders/stats + safety limits + diff]
  C -->|Valid| D[Vote #1<br/>Axie Score weighted<br/>Select Top 2]
  C -->|Invalid| X[Fix errors<br/>Resubmit]

  D --> E[Off-Season Trial]
  E --> E1[Week 1: Proposal A<br/>Season switch -> A JSON]
  E --> E2[Week 2: Proposal B<br/>Season switch -> B JSON]
  E1 --> F[Telemetry + Metrics]
  E2 --> F

  F --> G[Vote #2<br/>Axie Score weighted<br/>A vs B]
  G --> H[Winner -> Official Season Patch<br/>Publish canonical JSON]
  H --> I[Rollback possible<br/>Re-point season.json]
