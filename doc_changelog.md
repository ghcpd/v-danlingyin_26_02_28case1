# Documentation Changelog

## What was wrong

- `GUIDE.md` contained only a title and an install command; it did not document the package API at all.
- There was no explanation of how to initialize or use the `StateMachine`, which is the core export of this package.
- Key methods and types (`addState`, `start`, `transition`, `canTransition`, `getHistory`, `onTransition`, `reset`, etc.) were missing usage guidance and examples.
- No information was provided about error conditions or expected return values.

## What was fixed

- Backed up the original `GUIDE.md` to `GUIDE_backup.md` before making changes.
- Rewrote `GUIDE.md` to include:
  - Installation instructions
  - A quick start example showing state registration, starting, transitioning, and hooks
  - A full API reference covering every public method and type
  - Clear explanations of return values, error behavior, and recommended usage patterns
- Added `api_report.json` documenting each missing documentation item, including severity levels and descriptions.
- Added `doc_changelog.md` to explain the changes made and why.

## Result

Documentation now fully describes the public API surface of `state-machine-utils` and provides actionable examples for developers to use the library correctly.
