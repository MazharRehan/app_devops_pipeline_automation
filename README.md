# DevOps Term Project — CI/CD with GitHub Actions and GitHub Pages

This repository implements a complete Git flow–based CI/CD pipeline for a static website using GitHub Actions, GitHub Environments, Branch Protection rules, and GitHub Pages. The DevOps focus is on reliable automation, environment isolation, and quality gates (linting/tests) across development, staging, and production.

- Repository: [MazharRehan/term-project-f24](https://github.com/MazharRehan/term-project-f24/tree/develop)
- Live (team) website: [term-project-f24](https://mazharrehan.github.io/term-project-f24/)

## Status

- Development CI: ![CI Development](https://github.com/MazharRehan/term-project-f24/actions/workflows/ci-development.yaml/badge.svg?branch=develop)
- Staging CI: ![CI Staging](https://github.com/MazharRehan/term-project-f24/actions/workflows/ci-staging.yaml/badge.svg?branch=release/1.0.0)
- Production CI: ![CI Production](https://github.com/MazharRehan/term-project-f24/actions/workflows/ci-production.yaml/badge.svg?branch=production)
- Development CD: ![CD Development](https://github.com/MazharRehan/term-project-f24/actions/workflows/cd-development.yaml/badge.svg?branch=develop)
- Staging CD: ![CD Staging](https://github.com/MazharRehan/term-project-f24/actions/workflows/cd-staging.yaml/badge.svg?branch=release/1.0.0)
- Production CD: ![CD Production](https://github.com/MazharRehan/term-project-f24/actions/workflows/cd-production.yaml/badge.svg?branch=production)

## Contents

- .github/workflows
  - [ci-development.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-development.yaml)
  - [cd-development.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-development.yaml)
  - [ci-staging.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-staging.yaml)
  - [cd-staging.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-staging.yaml)
  - [ci-production.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-production.yaml)
  - [cd-production.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-production.yaml)
- src
  - Static site source (HTML/CSS/assets)
- dist
  - Build output (artifacts)

## Branching Strategy (Git Flow)

Primary branches:
- develop — default branch for integration
- release — stabilization branch for staging
- production — immutable production branch

Supporting branches:
- feature/* — new pages/features
- bugfix/* — fixes targeting release
- hotfix/* — urgent fixes targeting production

Rules:
- No direct pushes to develop, release, production
- All changes via Pull Requests (PRs) with passing checks
- Release cycle: feature/* → develop → release → production

## Environments

Configured GitHub Environments (with protections and approvals as appropriate):
- development-env — for integrated previews from develop
- staging-env — for release testing/validation
- production-env — for the public site (requires manual approval to deploy)

## Workflows Overview

Workflows are separated by environment and responsibility (CI vs CD). File names reflect their role.

- Development
  - CI: [.github/workflows/ci-development.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-development.yaml)
    - Trigger: pull_request → develop
    - Steps (key):
      - Checkout
      - Lint HTML/CSS
        ```
        npx htmlhint "**/*.html"
        npx stylelint "**/*.css"
        ```
      - Build (Parcel)
        ```
        npx parcel build "./src/index.html" --dist-dir "./dist" --public-url "./"
        ```
  - CD: [.github/workflows/cd-development.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-development.yaml)
    - Purpose: build artifacts and deploy to development-env on changes integrated into develop

- Staging
  - CI: [.github/workflows/ci-staging.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-staging.yaml)
    - Purpose: validate bugfix PRs into release (tests/build/analysis)
  - CD: [.github/workflows/cd-staging.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-staging.yaml)
    - Purpose: build and deploy release branch to staging-env

- Production
  - CI: [.github/workflows/ci-production.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/ci-production.yaml)
    - Purpose: final test/lint/build on PRs from release → production
  - CD: [.github/workflows/cd-production.yaml](https://github.com/MazharRehan/term-project-f24/blob/develop/.github/workflows/cd-production.yaml)
    - Purpose: deploy to production-env after PR merge, with manual approval

## Quality Gates

- HTML linting: HTMLHint (at least 10 rules via .htmlhintrc)
- CSS linting: Stylelint (at least 10 rules via .stylelintrc)
- All PRs must pass CI before merging
- CD jobs only run after successful CI and branch protections

Local lint/build commands:
```bash
# HTML lint
npx htmlhint "**/*.html"

# CSS lint
npx stylelint "**/*.css"

# Build (Parcel)
npx parcel build "./src/index.html" --dist-dir "./dist" --public-url "./"
```

## Branch Protection

Recommended protection rules for develop, release, and production:
- Disallow direct pushes
- Require PR reviews (at least 1)
- Require status checks to pass (all CI jobs)
- Restrict who can push (maintainers/admins only)
- Require conversation resolution before merge

## Deployment Model

- Development: automated build and deploy to development-env when changes are integrated into develop
- Staging: automated build and deploy to staging-env from the release branch
- Production: gated deployment to production-env after a passing CI, PR review(s), and manual approval

Production hosting is via GitHub Pages. The team’s public site is available at:
- https://umansheikh0611.github.io/term-project-f24

## Contribution Workflow

1. Create a branch from develop:
   - feature/<name> for new work
   - bugfix/<name> for release-targeted fixes
   - hotfix/<name> for urgent production fixes
2. Commit with clear messages (reference issue/feature)
3. Open a PR:
   - feature/* → develop (triggers Development CI)
   - bugfix/* → release (triggers Staging CI)
   - release → production (triggers Production CI)
4. Resolve feedback, ensure all checks pass
5. Merge per Git flow:
   - develop → release for staging
   - release → production after final verification

## Team (Roles/Branches Worked)

- Mazhar Rehan (Team Lead): Project & Environment setup, feature/blog-page, hotfix/workflows, workflow/staging, workflow/production, workflow/cd-development
- Muhammad Uman: workflows, feature/contact-us, release, production, staging, hotfix/config
- Zain Bin Imran: feature/home-page
- Usama Bin Naseer: feature/services
- Hafiz Faizan: feature/portfolio
- Ali Hassan: feature/about-us

## Tooling

- GitHub Actions (CI/CD)
- GitHub Environments (development-env, staging-env, production-env)
- GitHub Pages (production hosting)
- HTMLHint, Stylelint (linting)
- Parcel (static build)

## Directory Structure

```
term-project-f24/
├── .github/
│   └── workflows/
│       ├── cd-development.yaml
│       ├── cd-production.yaml
│       ├── cd-staging.yaml
│       ├── ci-development.yaml
│       ├── ci-production.yaml
│       └── ci-staging.yaml
├── .gitignore
├── .htmlhintrc
├── .stylelintrc
├── package.json
├── repo-config.sh
├── assets/        # source assets
├── JS/
├── src/           # source HTML
├── styles/        # source CSS
└─ dist/           # build artifacts (generated)

```

## Acknowledgements

We are deeply grateful to our course instructor and mentor, [Imran Ashraf](https://github.com/imranucp), for his guidance, feedback, and constant support throughout this project.

## License

This repository is for learning purposes for the DevOps Fundamentals term project.
