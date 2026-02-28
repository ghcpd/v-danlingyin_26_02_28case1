# State Machine

A lightweight, dependency-free finite state machine for managing workflow transitions in JavaScript and TypeScript.

The implementation lives in `stateMachine.ts`, which contains the full public API.

This package provides a simple `StateMachine` class you can use to:

- Define states and allowed transitions
- Enforce valid transitions at runtime
- Track transition history
- Subscribe to transition events via hooks

---

## Installation

```bash
npm install state-machine-utils
```

---

## Quick Start

```ts
import { StateMachine } from "state-machine-utils";

const machine = new StateMachine();

machine.addState({ name: "idle", allowedTransitions: ["running"] });
machine.addState({ name: "running", allowedTransitions: ["paused", "stopped"] });
machine.addState({ name: "paused", allowedTransitions: ["running", "stopped"] });
machine.addState({ name: "stopped", allowedTransitions: [] });

machine.start("idle");

machine.onTransition((event) => {
  console.log(`Transitioned from ${event.from} -> ${event.to}`);
});

machine.transition("running");
console.log(machine.getCurrent()); // "running"

console.log(machine.getHistory());
```

---

## API Reference

### `StateMachine`

The core class that manages states and transitions.

#### Constructors

```ts
new StateMachine();
```

#### Methods

##### `addState(definition: StateDefinition): void`

Register a new state and its allowed transitions.

- `definition.name`: Unique state name.
- `definition.allowedTransitions`: Array of target state names that are permitted from this state.

```ts
machine.addState({ name: "draft", allowedTransitions: ["published"] });
```

##### `start(stateName: string): void`

Set the initial state of the machine. The state must already be registered via `addState`.

Throws an error if the start state is unknown.

```ts
machine.start("draft");
```

##### `transition(targetState: string): boolean`

Attempt to transition to `targetState`.

- Returns `true` if the transition is allowed and succeeds.
- Returns `false` if the transition is not allowed or the target state is not registered.
- Throws an error if the machine has not been started.

```ts
const ok = machine.transition("published");
```

##### `canTransition(targetState: string): boolean`

Check whether the machine can transition from its current state to `targetState`.

Returns `false` if the machine has not been started or if the transition is not allowed.

```ts
if (machine.canTransition("paused")) {
  machine.transition("paused");
}
```

##### `getCurrent(): string | null`

Get the current state name, or `null` if the machine has not been started.

```ts
console.log(machine.getCurrent());
```

##### `getHistory(): TransitionEvent[]`

Returns an array of all successful transitions that have occurred since the last `start()` or `reset()` call. Each entry includes:

- `from`: previous state name
- `to`: new state name
- `timestamp`: when the transition occurred (milliseconds since epoch)

```ts
const history = machine.getHistory();
```

##### `onTransition(hook: TransitionHook): void`

Register a hook that runs on every successful transition.

Hooks receive a `TransitionEvent` object and can be used for logging, metrics, or side effects.

```ts
machine.onTransition(({ from, to }) => {
  console.log(`Moved: ${from} -> ${to}`);
});
```

##### `reset(): void`

Reset the machine to an unstarted state and clear transition history.

```ts
machine.reset();
```

##### `getStates(): string[]`

Return a list of all registered state names.

```ts
console.log(machine.getStates());
```

---

## Types

### `StateDefinition`

```ts
export interface StateDefinition {
  name: string;
  allowedTransitions: string[];
}
```

Defines a state and which named states it is allowed to transition to.

### `TransitionEvent`

```ts
export interface TransitionEvent {
  from: string;
  to: string;
  timestamp: number;
}
```

Represents a successful state transition.

### `TransitionHook`

```ts
export type TransitionHook = (event: TransitionEvent) => void;
```

Hook function signature for listening to transitions.

---

## Recommendations

- Register all states before calling `start()`.
- Keep `allowedTransitions` minimal to ensure predictable flows.
- Use `canTransition()` to verify before attempting transitions when you need to guard against invalid attempts.
- Use `onTransition()` to attach side effects (logging, analytics, dispatch events) without coupling those concerns to your core state logic.
