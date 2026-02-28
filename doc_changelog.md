# Documentation Changelog

The original `GUIDE.md` contained only a title and a single installation snippet. None of the exported interfaces, types, or `StateMachine` methods were documented. This left users without guidance on how to use the library.

## What was missing

* No description of `StateDefinition`, `TransitionEvent`, or `TransitionHook`.
* The `StateMachine` class and all of its public methods (`addState`, `start`, `transition`, `getCurrent`, `getHistory`, `onTransition`, `canTransition`, `reset`, `getStates`) were absent from the docs.
* No examples or usage instructions beyond the install command.

## What was added/fixed

* Backed up the original guide to `GUIDE_backup.md` to preserve the empty stub.
* Rewrote `GUIDE.md` with a comprehensive overview covering:
  * installation instructions
  * detailed descriptions of every exported interface, type, and method
  * field explanations (e.g. `allowedTransitions`)
  * a full usage example with code blocks
  * notes and source reference to `stateMachine.ts`.
* Created `api_report.json` summarizing the missing documentation, including severity ratings and detailed descriptions of each gap.
* Added this changelog file to explain what was wrong and how the documentation now remedies those issues.

The state machine's public API is now fully documented, making the package usable without inspecting source code directly.