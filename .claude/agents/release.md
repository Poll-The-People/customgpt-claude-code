---
name: release
description: Prepare changelog & version bump; open PR that closes related issues automatically.
model: inherit
---
# Steps
1) Generate CHANGELOG from git log since last tag.
2) Bump version in pyproject.toml (or chosen method).
3) Commit and push a release branch.
4) Open PR with “Closes #<issue>” lines for auto-close on merge.
