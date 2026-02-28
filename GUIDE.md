# State Machine

A lightweight state machine for managing workflows. This module exports a `StateMachine` class for handling state transitions, along with supporting interfaces and types.

## Source File

See [stateMachine.ts](stateMachine.ts) for the complete implementation.

## Getting Started

```bash
npm install state-machine-utils
```

## Core Types & Interfaces

### StateDefinition

Defines a state and its allowed outgoing transitions.

```typescript
interface StateDefinition {
  name: string;                    // The name of the state
  allowedTransitions: string[];    // Array of state names this state can transition to
}
```

### TransitionEvent

Represents a state transition that occurred.

```typescript
interface TransitionEvent {
  from: string;       // The state we transitioned from
  to: string;         // The state we transitioned to
  timestamp: number;  // Unix timestamp of when the transition occurred
}
```

### TransitionHook

A callback function that executes whenever a successful transition occurs.

```typescript
type TransitionHook = (event: TransitionEvent) => void;
```

## StateMachine Class

The main class for managing state machine operations.

### Constructor

```typescript
const machine = new StateMachine();
```

Creates a new, uninitialized state machine with no states registered.

### addState(definition: StateDefinition): void

Registers a new state with its allowed transitions.

**Parameters:**
- `definition` - A `StateDefinition` object specifying the state name and allowed target states

**Example:**
```typescript
machine.addState({
  name: 'idle',
  allowedTransitions: ['running', 'paused']
});

machine.addState({
  name: 'running',
  allowedTransitions: ['paused', 'stopped', 'idle']
});

machine.addState({
  name: 'paused',
  allowedTransitions: ['running', 'stopped']
});

machine.addState({
  name: 'stopped',
  allowedTransitions: ['idle']
});
```

### start(stateName: string): void

Sets the initial state of the machine. Must be called with a registered state name before any transitions can occur.

**Parameters:**
- `stateName` - The name of the state to start in

**Throws:** Error if the state has not been registered

**Example:**
```typescript
machine.start('idle');  // Begin in the 'idle' state
```

### transition(targetState: string): boolean

Attempts to transition to a new state if the transition is allowed from the current state.

**Parameters:**
- `targetState` - The name of the state to transition to

**Returns:** `true` if the transition succeeded, `false` if it was not allowed or the target state doesn't exist

**Example:**
```typescript
const success = machine.transition('running');
if (success) {
  console.log('Transitioned to running');
} else {
  console.log('Transition not allowed');
}
```

### getCurrent(): string | null

Returns the name of the current state, or `null` if the machine hasn't been started.

**Returns:** The current state name or `null`

**Example:**
```typescript
console.log(machine.getCurrent());  // 'running'
```

### getHistory(): TransitionEvent[]

Returns a copy of the complete transition history.

**Returns:** Array of all `TransitionEvent` objects that have occurred, in order

**Example:**
```typescript
const history = machine.getHistory();
history.forEach(event => {
  console.log(`${event.from} -> ${event.to} at ${event.timestamp}`);
});
```

### onTransition(hook: TransitionHook): void

Registers a callback function that will be invoked after every successful transition.

**Parameters:**
- `hook` - A function that receives the `TransitionEvent` and can perform side effects

**Example:**
```typescript
machine.onTransition((event) => {
  console.log(`Transitioned from ${event.from} to ${event.to}`);
  logToDatabase(event);
});
```

### canTransition(targetState: string): boolean

Checks whether a transition from the current state to the target state is allowed, without actually performing the transition.

**Parameters:**
- `targetState` - The name of the state to check

**Returns:** `true` if the transition is allowed and the target state exists, `false` otherwise

**Example:**
```typescript
if (machine.canTransition('paused')) {
  machine.transition('paused');
}
```

### reset(): void

Resets the machine to its uninitialized state and clears all transition history. States remain registered, but the current state becomes `null`.

**Example:**
```typescript
machine.reset();
console.log(machine.getCurrent());  // null
console.log(machine.getHistory());  // []
```

### getStates(): string[]

Returns an array of all registered state names.

**Returns:** Array of state name strings

**Example:**
```typescript
const states = machine.getStates();
console.log(states);  // ['idle', 'running', 'paused', 'stopped']
```

## Complete Usage Example

```typescript
import { StateMachine } from './stateMachine';

// Create a state machine
const machine = new StateMachine();

// Register states
machine.addState({
  name: 'idle',
  allowedTransitions: ['running']
});

machine.addState({
  name: 'running',
  allowedTransitions: ['paused', 'stopped']
});

machine.addState({
  name: 'paused',
  allowedTransitions: ['running', 'stopped']
});

machine.addState({
  name: 'stopped',
  allowedTransitions: ['idle']
});

// Set up a transition hook
machine.onTransition((event) => {
  console.log(`Transitioned: ${event.from} -> ${event.to}`);
});

// Start the machine
machine.start('idle');

// Make transitions
machine.transition('running');   // Transitioned: idle -> running
machine.transition('paused');    // Transitioned: running -> paused
machine.transition('running');   // Transitioned: paused -> running

// Check current state
console.log(machine.getCurrent());  // 'running'

// Check allowed transitions
if (machine.canTransition('stopped')) {
  machine.transition('stopped');
}

// View history
machine.getHistory().forEach(event => {
  console.log(`${event.from} -> ${event.to}`);
});
```
