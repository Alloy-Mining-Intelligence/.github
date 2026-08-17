# .github

Organization-wide defaults for Alloy Mining Intelligence. GitHub serves the files in this repository to every other repository in the org that does not carry its own copy.

## ⚠️ Everything here is world-readable

**This repository must be public, and nothing here takes effect until it is.** A private `.github` repository serves *no* default community health files at all, and internal is not sufficient either:

| Artifact | Public | Internal | Private |
|---|---|---|---|
| `SECURITY.md`, `CONTRIBUTING`, `SUPPORT`, `CODE_OF_CONDUCT`, `GOVERNANCE`, `FUNDING` | Works | Works | **No** |
| Issue and PR templates | Works | **No** | **No** |
| Workflow templates | Offered to all repo types | Internal + private only | Private only |

Public is the only setting that covers all three. `SECURITY.md` also invites vulnerability reports from outside the org, which only works if outsiders can read it.

**Write for a public audience.** No internal hostnames, no cloud account IDs, no client names or engagement details, no staff names beyond those already public. Deployment identifiers belong in repository variables (`vars.*`), never in a template committed here.

## Layout

```
.
├── README.md                          this file
├── SECURITY.md                        org-wide vulnerability disclosure policy
├── .github/
│   ├── PULL_REQUEST_TEMPLATE.md       default PR template
│   └── ISSUE_TEMPLATE/
│       ├── bug.yml
│       ├── feature.yml
│       └── config.yml
├── workflow-templates/                offered under Actions → New workflow
│   ├── ci.yml            + ci.properties.json
│   ├── release.yml       + release.properties.json
│   └── deploy.yml        + deploy.properties.json
├── templates/
│   └── dependabot.yml                 copy-me, NOT active anywhere
└── profile/
    └── README.md                      renders the public org profile page
```

### ⚠️ The nested `.github/` directory is deliberate

Community health files may live in the repository root, in `.github/`, or in `docs/` — GitHub checks in that order. **Issue templates and their `config.yml` are the exception: they must be in `.github/ISSUE_TEMPLATE/`.** No other location is honoured, so a root-level `ISSUE_TEMPLATE/` in this repository silently offers nothing in the New Issue chooser. The PR template is kept alongside it for the same reason.

## How the defaults behave

A repository with no `SECURITY.md` of its own displays the one here. A repository *with* its own copy wins — inheritance is per-file and all-or-nothing, never a merge.

**Defaults are served, not copied.** They do not appear in the other repository's file browser, git history, clones, or release archives. Anything that must travel with the code has to be committed to that repository directly.

## What does not belong here

| File | Why not |
|---|---|
| `CODEOWNERS` | Not a community health file. Does not inherit. Per-repository only. |
| `dependabot.yml` | Not a community health file. Per-repository only — hence `templates/`. |
| `LICENSE` | Cannot be defaulted by design, so it travels with clones and packages. |
| Policies and procedures | Documentation lives in Notion, not in a public repository. |

## Using the copy-me templates

`templates/dependabot.yml` is **not** at `.github/dependabot.yml` on purpose. Putting it there would activate Dependabot on *this* repository and would still not configure any other one, because `dependabot.yml` does not inherit. Copy it into the target repository at `.github/dependabot.yml` and trim the ecosystems down to what that repository actually uses.

## Workflow templates and SHA pinning

The org Actions policy sets **`sha_pinning_required: true`** and allows only GitHub-authored and Marketplace-verified actions. Every `uses:` in `workflow-templates/` is pinned to a full 40-character commit SHA with the release named in a trailing comment.

⚠️ **A moving tag such as `@v4` does not fail a step — the run never starts.** It is recorded as `startup_failure` with no log output and no error naming the policy, which reads exactly like a GitHub outage or malformed YAML. Resolve a SHA over git, which needs no token:

```bash
git ls-remote --tags https://github.com/actions/checkout | grep 'refs/tags/v7.0.1$'
```

Pin the SHA of a *specific* release and name that release in the comment. Pinning whatever a major tag points at today produces a pin whose comment goes stale silently.
