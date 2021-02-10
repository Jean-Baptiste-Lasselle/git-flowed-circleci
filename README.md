# The Git flowed Circle CI

This repo defines a typical Circle CI Pipeline definition compatible with the Git Flow

In this repo, the git flow is used, with all default configuration :
* this repo was git flow initialized with `git flow init --defaults`

## Specs

* pipeline has a Circle CI Workflow named `build_test_and_publish`, which :
  * builds / compiles the app
  * runs the unit tests
  * builds the OCI contianer image
  * push the OCI contianer image to a Contaienr Registry like quay.io / Dockerhub / https://github.com/goharbor/harbor / Portus
* Should not trigger any Circle CI pipeline execution when git push a new commit on any git branch managed by the git flow :
  * `master`
  * `develop`
  * any branch which names starts with the string `feature/`
  * any branch which names starts with the string `support/`
  * any branch which names starts with the string `feature/`
  * any branch which names starts with the string `bugfix/`
  * any branch which names starts with the string `hotfix/`
  * any branch which names starts with the string `release/`
* Should trigger a Circle CI Pipeline Execution, executing `the build_test_and_publish` Circle CI Workflow, only when a git flow release is finished (on the created tag on `master`)
