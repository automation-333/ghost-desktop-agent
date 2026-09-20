# GHOST Architecture Notes

This document describes the current architectural direction of GHOST. It is intentionally implementation-oriented and separates what the system is trying to achieve from any single model or automation provider.

## Design goals

GHOST is being designed around several principles:

- user intent should be expressed in natural language
- the agent should reason from the current environment rather than replay fixed coordinates
- observation, planning and execution should be separate layers
- important actions should be verified
- failed actions should produce new information for replanning
- sensitive actions should be treated differently from ordinary navigation
- application-specific logic should be minimized where reusable primitives are possible

## Runtime loop

A task can be represented as a repeated state transition:

```
goal
  |
observe environment
  |
build structured state
  |
choose next action
  |
validate action
  |
execute action
  |
observe result
  |
verify progress
  |
complete / recover / replan
```

The loop should stop when the requested outcome has been verified, the user takes over, the system reaches a safety boundary or execution can no longer make reliable progress.

## Observation layer

The observation layer converts the live computer environment into structured state for the planner.

Potential sources include:

- Windows UI Automation
- accessibility trees
- browser DOM or browser automation state
- active window metadata
- visible text
- focus information
- application state
- action history
- visual context when structured data is not enough

The objective is not to send an entire raw desktop state to the planner. The objective is to construct the smallest useful representation of the current task context.

## Planner

The planner decides the next action from:

- task goal
- current observation
- available tools
- previous actions
- previous failures
- safety constraints

The planner should produce structured actions rather than free-form execution instructions.

Conceptually:

```json
{
  "action": "click",
  "target": {
    "role": "button",
    "name": "Submit"
  },
  "expected_result": "confirmation becomes visible"
}
```

This makes validation and execution more predictable.

## Target resolver

The target resolver maps a semantic target from the planner to a concrete element in the current environment.

Preferred order:

1. exact structured match
2. accessibility role/name match
3. DOM selector or browser-level semantic match
4. approximate semantic match
5. visual target
6. coordinate fallback

A target should ideally remain meaningful even when screen resolution or layout changes.

## Executor

The executor performs a constrained set of reusable operations.

Examples:

- application launch
- window focus
- browser navigation
- click
- text entry
- keyboard shortcut
- scrolling
- file interaction
- reading structured content
- waiting for a state transition

The executor should not decide strategy. It should execute a validated action and return a structured result.

## Verification

Verification asks whether the action produced the expected state.

Useful verification signals can include:

- expected element appears
- expected window becomes active
- URL changes
- text changes
- target application state changes
- file is created or updated
- expected data is present

A successful click is not the same as successful task progress.

## Recovery

Recovery can include:

- waiting and observing again
- retrying the same action
- resolving the target again
- switching to another action
- returning the failure to the planner
- requesting user intervention

Repeated blind retries should be avoided because they can make the system unsafe and harder to debug.

## Safety boundary

Actions can be assigned risk levels.

Low-risk examples:

- open a page
- switch a tab
- scroll
- read content

Higher-risk examples:

- send a message
- publish content
- purchase
- delete
- change permissions or security settings

Higher-risk actions can require explicit confirmation before execution.

## Logging

Each step should be observable during development:

- goal
- observation summary
- planner output
- resolved target
- executed action
- execution result
- verification result
- recovery decision

This makes failures reproducible and allows the system to be improved from real traces instead of demo-only behavior.

## Current engineering priority

The current priority is reliability of the core loop before broadening the list of supported applications.

A smaller set of actions that can observe, execute, verify and recover reliably is more useful than a large catalog of hardcoded demos.
