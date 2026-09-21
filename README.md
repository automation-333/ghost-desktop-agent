# GHOST Desktop AI Agent

<p align="center">
  <img src="./assets/ghost-ui.webp" alt="GHOST Desktop AI Agent interface" width="1000">
</p>

GHOST is a Windows **computer-use agent** built as a personal R&D project around one core idea:

`Observe -> Plan -> Act -> Verify -> Replan`

Instead of replaying fixed macros, the system is designed to interpret a user's goal, inspect the current environment, choose the next action, verify what actually happened and recover when execution does not match the expected result.

## Recruiter quick view

| | |
|---|---|
| **Project type** | Personal R&D / desktop agent |
| **Platform** | Windows |
| **Core stack** | C#, .NET, WPF |
| **Focus** | Agent architecture, desktop/browser automation, semantic UI targeting, verification and recovery |
| **Interaction model** | Natural-language goals -> structured actions -> execution -> verification |
| **Current stage** | Working prototype under active development |
| **Public repository** | Architecture, technical documentation and UI materials |

> The implementation source is not currently published in this repository. This public repository is intended to document the product direction, architecture and engineering decisions without presenting unfinished capabilities as production-ready.

## What is working

GHOST is an active prototype rather than a finished product.

| Area | Status |
|---|---|
| Natural-language task input | Working prototype |
| Browser interaction | Working in selected scenarios |
| Desktop interaction | Working in selected scenarios |
| Dynamic planning | Active development |
| Semantic UI targeting | Active development |
| Result verification | Active development |
| Recovery / replanning | Active development |
| Sensitive-action confirmation | Architecture in progress |
| Voice interaction | Planned |

The current priority is **reliability of the execution loop**, not adding a large list of hardcoded application demos.

## Architecture

<p align="center">
  <img src="./assets/architecture.svg" alt="GHOST agent architecture" width="1000">
</p>

A task is handled as a repeated state transition:

```text
User goal
   |
Observe environment
   |
Build structured state
   |
Plan next action
   |
Validate
   |
Execute
   |
Observe result
   |
Verify progress
   |
Complete / Recover / Replan
```

The system is split into layers so reasoning and execution are not tightly coupled.

### 1. Observation

The observation layer builds structured context from the current environment.

Potential signals include:

- active applications and windows
- visible UI elements
- accessibility information
- browser state
- focused controls
- visible text
- recent actions and results

### 2. Planning

The planner reasons from:

- the user's requested outcome
- current environment state
- available actions
- previous execution history
- previous failures
- safety constraints

The intended output is a structured action rather than unrestricted execution text.

### 3. Target resolution

UI targets are resolved semantically where possible.

Preferred order:

1. structured UI match
2. accessibility role/name
3. browser DOM or semantic browser data
4. approximate semantic match
5. visual target
6. coordinates as a fallback

This is intended to reduce dependence on fixed screen positions.

### 4. Execution

The executor works with constrained reusable primitives such as:

- launch or focus an application
- navigate the browser
- click a target
- enter text
- use keyboard shortcuts
- scroll
- read structured content
- interact with files
- wait for state changes

### 5. Verification and recovery

An action being executed is not treated as proof that the task progressed.

After meaningful actions, GHOST can observe the environment again and compare the result with the expected state.

Recovery may include:

- waiting and observing again
- resolving the target again
- retrying
- choosing another action
- replanning
- requesting user intervention

## Technology

- C#
- .NET
- WPF
- Windows UI Automation
- browser automation
- accessibility APIs
- LLM APIs
- structured tool execution
- semantic UI targeting
- AI-assisted development with Codex

## Engineering problems explored

The project is mainly about solving reliability problems that appear when software has to operate changing interfaces rather than a stable API:

- translating natural-language intent into constrained actions
- keeping observation, reasoning and execution separate
- locating UI elements without relying only on coordinates
- verifying whether an action produced the expected state
- recovering from partial or failed execution
- distinguishing ordinary navigation from sensitive actions
- keeping execution traces understandable enough to debug

## Safety model

Computer-use agents can trigger actions with real consequences.

Higher-risk operations should be distinguishable from ordinary navigation and can require explicit confirmation before execution, especially for actions such as:

- sending messages
- publishing content
- purchases
- deleting data
- security or permission changes
- other irreversible actions

## Documentation

For a more implementation-oriented breakdown, see:

**[Architecture notes](./docs/ARCHITECTURE.md)**

## Development direction

Current work is focused on:

- stronger environment observation
- semantic UI understanding
- browser and desktop interaction
- dynamic planning
- execution verification
- recovery after failed actions
- safe handling of sensitive actions
- reducing dependence on fixed coordinates
- structured execution logs

Planned later:

- persistent task context
- reusable skills
- richer browser understanding
- stronger desktop application support
- voice interaction

## Why this project exists

Traditional automation works well when every step is predictable.

GHOST explores the harder case: workflows where the interface changes, context matters, and software has to decide what to do next instead of replaying a recorded sequence.

The goal is not to build a larger macro recorder. The goal is to explore a reliable execution layer for natural-language computer control.

---

**Developer:** [automation-333](https://github.com/automation-333)  
**Telegram:** [@DMD_user](https://t.me/DMD_user)
