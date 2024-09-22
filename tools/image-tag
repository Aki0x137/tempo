#!/usr/bin/env bash

set -o errexit
set -o nounset
set -o pipefail

REPO_ROOT=$(git rev-parse --show-toplevel)

is_valid_semver() {
      local version=$1
      # regex taken from https://semver.org/
      if [[ $version =~ ^v(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)(?:-((?:0|[1-9]\d*|\d*[a-zA-Z-][0-9a-zA-Z-]*)(?:\.(?:0|[1-9]\d*|\d*[a-zA-Z-][0-9a-zA-Z-]*))*))?(?:\+([0-9a-zA-Z-]+(?:\.[0-9a-zA-Z-]+)*))?$ ]] ;then
            return 1
      else
            return 0
      fi
}

# Check if there is a VERSION file
if [ -f "${REPO_ROOT}/VERSION" ]; then
      VERSION=$(cat "${REPO_ROOT}/VERSION" )
      if is_valid_semver "$VERSION"; then
            echo "$VERSION"
            exit 0
      fi
fi

WIP=$(git diff --quiet || echo '-WIP')
BRANCH=$(git rev-parse --abbrev-ref HEAD | sed 's#/#-#g')
# When 7 chars are not enough to be unique, git automatically uses more.
# We are forcing to 7 here, as we are doing for grafana/grafana as well.
SHA=$(git rev-parse --short=7 HEAD | head -c7)

# If not a tag, use branch-hash else use tag
TAG=$((git describe --exact-match 2> /dev/null || echo "") | sed 's/v//g')

if [ -z "$TAG" ]
then
      echo ${BRANCH}-${SHA}${WIP}
else
      echo ${TAG}
fi
