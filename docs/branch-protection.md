# Branch Protection Requirements

Task: #393 Configure branch protection requiring CI to pass before merging to main

Branch protection is a GitHub repository setting, so it must be enabled by a
repository admin or maintainer. Apply the following rule to the protected
`main` branch.

## Backend Repository

Repository: `bashkimhashani/ecommerce-backend`

Required rule:

- Branch name pattern: `main`
- Require a pull request before merging: enabled
- Required approvals: at least `1`
- Require status checks to pass before merging: enabled
- Require branches to be up to date before merging: enabled
- Required status checks:
  - `Lint`
  - `Test`
  - `Docker Build`
- Do not allow force pushes: enabled
- Do not allow deletions: enabled

The required checks above come from the backend GitHub Actions workflow:
`.github/workflows/ci.yml`.

## Frontend And Infra Repositories

Repositories:

- `bashkimhashani/ecommerce-frontend`
- `bashkimhashani/ecommerce-infra`

These repositories do not currently define their own GitHub Actions workflows.
Do not require status checks on their `main` branches until CI workflows exist
there, otherwise pull requests may be blocked with no check available to pass.

Recommended temporary rule for both repositories:

- Branch name pattern: `main`
- Require a pull request before merging: enabled
- Required approvals: at least `1`
- Do not allow force pushes: enabled
- Do not allow deletions: enabled

After frontend and infra CI workflows are added, enable "Require status checks
to pass before merging" and select the new workflow job checks for each repo.

