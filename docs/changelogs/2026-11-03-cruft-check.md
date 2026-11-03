# fix(cruft-update): open update PRs only when the template has changed

Tracking: alva-ai/mono-meta#1115 (decision G15 = B: keep ggsrv-layout as
the service template and fix its sync). Merged together with the hardening
in #7.

## 1. Current state

- `cruft check` exits 1 both when the project is out of date and when it
  fails. The workflow treated every non-zero exit as "update available".
- The token was configured only for `cruft update`, after the check, so a
  private template could never be checked.
- Result in alva-backend, alva-gateway and alfs since March: the check fails
  to clone the private ggsrv-layout (403), the update step then fails the
  same way, and every weekly run is red. Earlier runs had opened 20 update
  PRs, none merged.

## 2. Behavior after this change

- The token is configured before the check, so a private template can be
  cloned when the token can read it.
- Up to date: nothing happens. Out of date (cruft's "cruft is out of date"
  message): the two PRs are opened as before. Any other failure fails the
  job with an error annotation and opens nothing.
- The token reaches the shell through an environment variable instead of
  being expanded into the script.
- cruft is pinned to 2.16.0.

Callers keep the same interface (`CICD_DOCKER_BUILD_PAT`).

## 3. Verification

- actionlint passes.
- The check step, run with a fake `cruft` for each outcome: up to date →
  `has_changes=0`, exit 0; out of date → `has_changes=1`, exit 0; clone error
  → exit 1, no output; no `.cruft.json` → `has_changes=0`.

## 4. Remaining work

- Resolved 2026-11-03: ggsrv-layout is now public, so the check needs no
  token to read it. Its history was restarted, so the commits recorded in
  the generated projects' `.cruft.json` no longer exist there: until a
  project is re-linked (ggsrv-layout `docs/changelogs/2026-11-03-public-repository.md`),
  its check fails with `SHA ... could not be resolved`, which this change
  reports as an error instead of opening update PRs.
