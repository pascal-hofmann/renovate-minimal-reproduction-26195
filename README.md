# Obsolete PRs are not auto-closed

## How to reproduce
1. Revert the commit `Remove version constraints for postgres and mysql`
2. Run renovate once. It will create a PR for `lock file maintenance`
3. Undo (revert) the revert.
4. Run renovate again. The PR will stay open with conflicts.

I struggled to reproduce this behavior until I noticed that the broken PR in our prod repository
was for "lock file maintenance". Once I added config for lock file maintenance to renovate.json, 
deleted existing PRs and branches and retried I was able to reproduce.



This was the command I used to run renovate:
```bash
#!/usr/bin/env bash
set -euo pipefail

export GITHUB_COM_TOKEN=$RENOVATE_TOKEN

docker run \
    --rm \
    -e LOG_LEVEL="debug" \
    -e RENOVATE_TOKEN \
    -e GITHUB_COM_TOKEN \
    renovate/renovate \
    --dry-run="false" \
    --schedule= --pr-hourly-limit= --force-cli \
    --autodiscover \
    --autodiscover-filter "pascal-hofmann/renovate-minimal-reproduction-26195"
```

## Current behavior
PR stays open with conflicts.

## Expected behavior
PR is auto-closed.
