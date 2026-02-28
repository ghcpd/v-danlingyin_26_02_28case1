# State Machine

A lightweight, dependency-free state machine for managing workflows in TypeScript/JavaScript.

This repository contains a single source file, `stateMachine.ts`, which exports a fully typed `StateMachine` class and related types.

---

## Install

```bash
npm install state-machine-utils
```

> Note: This package is intentionally minimal. All core logic lives in `stateMachine.ts`.

---

## Quick Start (JavaScript / TypeScript)

```ts
import { StateMachine } from "state-machine-utils";

const machine = new StateMachine();

machine.addState({ name: "idle", allowedTransitions: ["running"] });
machine.addState({ name: "running", allowedTransitions: ["completed", "failed"] });
machine.addState({ name: "completed", allowedTransitions: [] });
machine.addState({ name: "failed", allowedTransitions: [] });

machine.start("idle");

machine.onTransition((event) => {
  console.log(`Transitioned from ${event.from} → ${event.to} at ${new Date(event.timestamp).toISOString()}`);
});

if (machine.canTransition("running")) {
  machine.transition("running");
}

console.log(machine.getCurrent());
console.log(machine.getHistory());
```

---

## API Reference (public)

All public APIs are implemented in `stateMachine.ts`.

### Types & Interfaces

#### `StateDefinition`

Describes a state that can be registered with the machine.

- `name: string` – Unique identifier for the state.
- `allowedTransitions: string[]` – List of other state names that are valid targets for a transition from this state.

```ts
interface StateDefinition {
  name: string;
  allowedTransitions: string[];
}
```

#### `TransitionEvent`

Represents a single successful transition from one state to another.

- `from: string` – The source state name.
- `to: string` – The destination state name.
- `timestamp: number` – Unix epoch milliseconds when the transition occurred.

```ts
interface TransitionEvent {
  from: string;
  to: string;
  timestamp: number;
}
```

#### `TransitionHook`

A callback invoked after every successful transition.

```ts
type TransitionHook = (event: TransitionEvent) => void;
```

---

### `StateMachine` class

#### `addState(definition: StateDefinition): void`

Register a new state and its allowed transitions.

- Throws: nothing.
- Notes: Calling `addState` multiple times with the same `name` overwrites the previous definition.

#### `start(stateName: string): void`

Set the initial state of the machine.

- `stateName` must match a registered state.
- Resets transition history.
- Throws if `stateName` is not registered.

#### `transition(targetState: string): boolean`

Attempt to move from the current state to `targetState`.

- Returns `true` if the transition succeeds.
- Returns `false` if the transition is not allowed or the target state is not registered.
- Throws if the machine has not been started.

#### `getCurrent(): string | null`

Get the current state name, or `null` if the machine has not been started.

#### `getHistory(): TransitionEvent[]`

Get a copy of the transition history (order of transitions performed).

#### `onTransition(hook: TransitionHook): void`

Register a callback that runs after every successful transition.

#### `canTransition(targetState: string): boolean`

Check whether a transition from the current state to `targetState` is allowed.

Returns `false` if the machine is not started, the current state is missing, or `targetState` is not in `allowedTransitions`.

#### `reset(): void`

Reset the machine to its unstarted state and clear transition history.

#### `getStates(): string[]`

Return all registered state names.

---

## Recommended Usage Patterns

### Building a workflow

- Define all states first (for readability).
- Call `start()` once with the initial state.
- Use `canTransition()` before `transition()` to guard transitions.
- Register `onTransition()` hooks for logging, side effects, or metrics.

### Example: Simple approval workflow

```ts
import { StateMachine } from "state-machine-utils";

const workflow = new StateMachine();

workflow.addState({ name: "draft", allowedTransitions: ["review"] });
workflow.addState({ name: "review", allowedTransitions: ["approved", "rejected"] });
workflow.addState({ name: "approved", allowedTransitions: [] });
workflow.addState({ name: "rejected", allowedTransitions: [] });

workflow.start("draft");

workflow.onTransition((event) => {
  console.log(`Moved from ${event.from} to ${event.to}`);
});

workflow.transition("review");
workflow.transition("approved");
```

---

## File reference

Source implementation: `stateMachine.ts`
