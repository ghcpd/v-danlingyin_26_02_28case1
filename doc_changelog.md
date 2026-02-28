# Documentation Changelog

## What was missing
- The original `GUIDE.md` contained only a title and install command, but did not document the **state machine API** at all.
- There was **no coverage** of the exported types and interfaces (`StateDefinition`, `TransitionEvent`, `TransitionHook`).
- The guide lacked a **usage example**, making it hard for a reader to quickly understand how to use `StateMachine`.

## What was added / fixed
- Created `GUIDE_backup.md` as an exact copy of the original `GUIDE.md` (required by tests).
- Rewrote `GUIDE.md` to include:
  - A concise introduction and install section
  - A working Quick Start example using the `StateMachine` class
  - Complete API documentation for every public method and exported type in `stateMachine.ts`
  - A reference to `stateMachine.ts` as the source implementation
- Added `api_report.json` capturing the missing-documentation findings and classifying severity levels.
- Added `doc_changelog.md` explaining what was missing and what was fixed.
