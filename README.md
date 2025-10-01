# GitHub Actions Collection

Actions:
 - commit-version: update version string(s) in a file and commit the change.
 - docker-build: build & push a single-platform image; outputs a digest artifact.
 - docker-merge: create a multi-architecture manifest from previously built digests.
 - docker-merge-stage: download digests, compute stage tag, merge multi-arch, output tag.
 - docker-merge-prod: download digests, compute prod tag (conditional by event), merge multi-arch, output tag.

For full inputs, outputs, and examples see the README (if present) or action.yml inside each directory.
