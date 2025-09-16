# CI/CD Setup Added Automatically

I inspected your uploaded project and added the following CI/CD helpers into the repo copy:

1. `.github/workflows/flake8.yml` - runs flake8 on pushes and PRs to `dev`.
2. `.github/workflows/unit-tests.yml` - runs pytest on PRs to `test`.
3. `.github/workflows/auto-pr-to-master.yml` - on push to `test` branch, creates an automatic PR to `master` (using `peter-evans/create-pull-request` action).
4. `Jenkinsfile` - pipeline to build Docker image and push to Docker Hub, and notify admin by email.

Placeholders to update before use:
- Edit `Jenkinsfile` and set `DOCKERHUB_REPO` and `ADMIN_EMAIL`.
- Create Jenkins credentials: `dockerhub-creds` for Docker Hub username/password.
- Configure Jenkins to use the repository and create a webhook (or use Multibranch Pipeline).
- Set branch protection rules on GitHub for `dev`, `test`, and `master` (require PRs and checks).

How to test locally:
- Create a `dev` branch and push a change -> flake8 will run on GitHub (once workflow is present in the repo).
- Create PR from `dev` to `test` -> unit-tests action runs.
- After merging to `test`, a PR to `master` will be auto-created. When merged to `master`, Jenkins should be triggered (hook) to build docker and push.
