## AOEP Design Guidelines

AOEP proposals must follow these core principles:

1. **Minimal Engineering Cost**
   - Proposals must be implementable with existing Sky Mavis infrastructure.
   - Preference is given to solutions based on configuration, scripts, and data (JSON, flags, snapshots), not new platforms or complex services.
   - Target implementation time: **≤ 1 week** for a small team.

2. **Non-Invasive to Core Gameplay**
   - The core game loop, combat logic, and client mechanics must remain unchanged.
   - AOEP focuses on *orchestration, governance, and data flow*, not rewriting systems.

3. **Small Architectural Leverage, Large Systemic Impact**
   - Changes should be “life-changing” in outcome, not in code size.
   - Examples: validation layers, voting pipelines, configuration versioning, experiment gating.
   - Avoid proposals that require refactoring battle engines, card logic, or UI foundations.

4. **Data-Driven Over Feature-Driven**
   - Prefer schema, parameters, and process improvements over new gameplay features.
   - Mechanics stay in code; numbers, rules, and selection logic live in data.

5. **Off-Season and Optional by Design**
   - All experiments must be isolatable to off-season or opt-in environments.
   - No disruption to ranked or core season stability.

6. **Reversible and Safe**
   - Every AOEP must support:
     - Versioning
     - Rollback
     - Auditability
   - No irreversible migrations, no one-way state transitions.

7. **Governance Without Bureaucracy**
   - Community signal is used, but process must remain lightweight.
   - No complex smart contracts, no heavy UI requirements.

8. **Author-Assisted Implementation**

AOEP authors are expected to reduce implementation cost by providing all reusable assets and technical material required to execute the proposal, including when applicable:

- **JSON configurations** (schemas, example payloads, diff-ready files)
- **Design mockups** (UI flow, diagrams, state machines)
- **Animation or visual references** (if presentation or clarity is impacted)
- **Code blocks / scripts** (validation, diffing, voting logic, config switches)
- **Deployment instructions** (step-by-step, copy-paste runnable)

A high-quality AOEP should allow a small Sky Mavis team to implement the proposal with minimal interpretation, minimal back-and-forth, and minimal new development, using the provided artifacts as a starting point or direct reference.

In short:

> AOEP is not about changing the game.  
> It is about inserting a thin layer of intelligence, validation, and legitimacy around how the game evolves.
> An AOEP author does not only propose an idea.  
> They provide the building blocks to ship it.

The intent is that an AOEP should be:
- Not only conceptually sound,
- But *operationally actionable*.









