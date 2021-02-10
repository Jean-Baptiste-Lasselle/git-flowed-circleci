# The Git flowed Circle CI

This repo defines a typical Circle CI Pipeline definition compatible with the Git Flow

In this repo, the git flow is used, with all default configuration :
* this repo was git flow initialized with `git flow init --defaults`
* And the basic work cycle is  :

```bash
export FEATURE_ALIAS="pull_requests_management"
export COMMIT_MESSAGE="feat.(${FEATURE_ALIAS}): adding Circle CI Workflow to manage Pull Requests, with docuemntation"

# create feature branch
git flow feature start "${FEATURE_ALIAS}" && git push -u origin HEAD
export SQUASHING_ORIGIN_COMMIT_ID=$(git rev-parse --short=40 HEAD)

# N times per cycle : edit repos files, and git commit and push
git add --all && git commit -m "${COMMIT_MESSAGE}" && git push -u origin HEAD

# squash commits on feature beanch, before creating Pull Request / Merge request
# this will be interactive :
#   --> (edit the rebase file) : prefix with "squash" all commits but the last at the bottom of the commit list
#   --> [Ctrl + O], and press Enter key
#   --> [Ctrl + X], and press Enter key
#
git rebase --interactive ${SQUASHING_ORIGIN_COMMIT_ID}
# when finished, git commits ahve locally been squashed : we need to push them to remote
git push -ff -u origin HEAD

echo "create pull request / merge request from [feature/${FEATURE_ALIAS}] git branch to [develop] git branch "
# => creating the pull/merge request should trigger integration tests, and if thgey pass, then acceptance tests
export PULL_REQUEST_HTTP_LINK=https://github.com/Jean-Baptiste-Lasselle/git-flowed-circleci/pull/196
# If all tests pas,then I accept Pull/ Merge request from command line like this :

#
# Interactive // Merge Message: "Accepting ${PULL_REQUEST_HTTP_LINK}" :
git flow feature finish "${FEATURE_ALIAS}"
#
git push -u origin --all

```

* You run the above cycle as many times as necessary to add all features fro the next release, and then you make your rrelease like this, after the last Pull / Merge Request was accpeted (so you begin from develop) :

```bash

# you may start by squashing commits on develop, from the last git tag (which was merged back to [develop])

# ----
# --- Release
export RELEASE_VERSION_NUMBER=0.0.7
git flow release start ${RELEASE_VERSION_NUMBER}

# [-s] option : to GPG sign the git release tag
# Interactive
git flow release finish -s ${RELEASE_VERSION_NUMBER}

#
git push -u origin --all && git push -u origin --tags

```

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
