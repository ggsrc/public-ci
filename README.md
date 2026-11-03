# public-ci

Reusable GitHub Actions workflows for ggsrc and Alva repositories.

| Workflow | Use | Status |
|---|---|---|
| `golang-lint-v2_8.yml` | golangci-lint v2 with configurable `go-version` and `lint-version` | Supported |
| `golang-lint-v2.yml` | golangci-lint v2.5 | Supported; prefer `golang-lint-v2_8.yml` |
| `golang-build-check.yml` | buf generate + go build check | Supported |
| `cruft-update.yml` | Cruft template sync PRs | Supported (external callers) |
| `golang-lint.yml`, `golang-test.yml`, `remove-old-artifacts.yml` | — | Deprecated, no known callers |

## Usage

```yaml
jobs:
  lint:
    uses: ggsrc/public-ci/.github/workflows/golang-lint-v2_8.yml@<commit SHA> # v1
    with:
      go-version: "1.27"
      lint-version: v2.14.0
    secrets:
      # Only needed when the module depends on private Go modules (GOPRIVATE).
      CICD_DOCKER_BUILD_PAT: ${{ secrets.CICD_DOCKER_BUILD_PAT2 }}
```

- Pin a release tag or, better, its commit SHA instead of `@main`: a change
  merged here runs in every caller repository, with the secrets the caller
  passes.
- `CICD_DOCKER_BUILD_PAT` is optional. When it is set, it is used for
  `git` access to github.com so that private modules can be fetched.
- Third-party actions in these workflows are pinned by commit SHA; Dependabot
  proposes updates weekly.

## License

Apache-2.0, see [LICENSE](LICENSE).
