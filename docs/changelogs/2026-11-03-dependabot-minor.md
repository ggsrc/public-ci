# chore(dependabot): propose only minor and patch action updates

Tracking: alva-ai/mono-meta#1115.

## 1. Current state

- #7 pinned every action by commit SHA within its current major version and
  added Dependabot with no version limit.
- Its first PR, #9, moved seven actions across major versions at once
  (checkout 4 → 7, setup-go 5 → 7, golangci-lint-action 6/7 → 9,
  create-pull-request 4 → 8, github-script 7 → 9, upload-artifact 4 → 7,
  setup-python 4 → 7). Callers run these workflows from `@main`: 10 Alva
  repositories (lint) and 3 external ones (cruft-update) would get all of
  them untested. golangci-lint-action v9 also requires golangci-lint v2,
  which the deprecated `golang-lint.yml` does not use.

## 2. Behavior after this change

- Dependabot proposes minor and patch updates only, still as one weekly
  grouped PR.
- A major version is adopted on purpose, in its own PR.
- #9 is closed.

## 3. Changes

- `.github/dependabot.yml`: `ignore` semver-major updates for all actions.
- `README.md`: the pinning note says so.

## 4. Verification

- Configuration follows the Dependabot `ignore` / `update-types` schema;
  GitHub validates it when the PR is opened (no error on the PR).

## 5. Outcome

- [x] #9 closed.
- [x] Merged on 2026-11-04 through the `main-protect` bypass, which Yumin
  approved for this PR.
- No new tag: the workflows that callers run do not change.
