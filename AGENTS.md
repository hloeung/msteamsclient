# Agent Instructions — msteamsclient

## Environment & Formatting

- Target: Go 1.26+.
- Use hard tabs for indentation and adhere strictly to standard `gofmt` / `goimports`.
- Enforce `golangci-lint` compliance:
  - `noinlineerr`: Never inline error assignment with checking (do not use `if err := ...; err != nil`). Always assign `err` on its own line first.
  - `wsl_v5`: Maintain proper statement separation, particularly ensuring empty lines/whitespace precede variable assignments (avoid "invalid statement above assign") and control blocks.

## Diff Hygiene & Scope

- Keep changes minimal, surgical, and scoped strictly to the task.
- Do not add, remove, or modify blank lines or whitespace in untouched lines to keep diffs clean and easy to review.
- Preserve existing comments and structural flow unless invalidated by the change.
- Never introduce speculative flexibility, broad refactoring, or unrelated cleanups.
- Avoid excessive defensive over-engineering where standard repository flow suffices.
- Keep public APIs backward compatible unless breaking changes are explicitly requested.
- Do not write or suggest adding unit tests or test cases unless specifically requested.

## Idioms, Networking & Performance

- Zero-Allocation Mindset: Aim for zero (or single) allocations in hot networking paths, line parsers, slice slicing, and protocol message transformations.
- Concurrency & Deadlocks:
  - Check lock ordering and mutex scopes rigorously to prevent deadlocks.
  - Ensure goroutines terminate cleanly on connection drops or context cancellation; never leak goroutines or leave channels unbuffered if blocking is fatal.
  - Prevent time-of-check to time-of-use (TOCTOU) race conditions on shared state, session maps, and channel metadata.
  - Verify that changes do not introduce regressions into daemon connection loops.
- Serialization: Prefer `json.Unmarshal([]byte)` over `json.NewDecoder(strings.NewReader())`.
- Comments: Document only intent, invariants, protocol quirks, and non-obvious constraints. Do not restate obvious code.
- Consistency: Match the existing package conventions for naming, options, error handling, and method signatures.

## matterclient Consistency

- [matterclient](https://github.com/matterbridge/matterclient) is the primary reference implementation, with [matrixclient](https://github.com/matterbridge/matrixclient) and [slackclient](https://github.com/matterbridge/slackclient) as secondary references. `msteamsclient` should mirror `matterclient`'s public API, semantics, logging levels (e.g. `SetLogLevel` / `SetLogAPICalls`, where `"warn"` shows API calls), and naming as closely as possible.
- Before changing or adding behaviour, check how matterclient does it and follow it unless the Microsoft Teams protocol requires otherwise; document any deliberate difference in a comment.
- Consumers (e.g. matterircd `bridge/msteams` vs `bridge/mattermost`) should be able to treat both clients the same way, so fix differences here rather than working around them downstream.
