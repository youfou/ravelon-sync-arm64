# ravelon-sync-arm64

Unofficial mirror builder: watches [ravelon-app/ravelon-sync](https://github.com/ravelon-app/ravelon-sync) and publishes an **ARM64** Docker image to GHCR, because upstream only publishes `linux/amd64` ([issue](https://github.com/ravelon-app/ravelon-sync/issues/37)).

## Image

```
ghcr.io/youfou/ravelon-sync-arm64:latest
```

Also tagged per upstream commit: `ghcr.io/youfou/ravelon-sync-arm64:sha-<shortsha>`.

## Use it

On an ARM64 host, replace the local build with the prebuilt image:

```yaml
services:
  sync:
    image: ghcr.io/youfou/ravelon-sync-arm64:latest
```

Then `docker compose pull && docker compose up -d`. No more `git pull` + local build on every update.

## How it works

A scheduled workflow runs every hour (`.github/workflows/build-arm64.yml`):

1. Gets upstream `main` HEAD via `git ls-remote`.
2. Compares it with `last-built-sha.txt`. If unchanged, nothing happens.
3. If updated, checks out upstream at the new SHA and builds upstream's root `Dockerfile` for `linux/arm64` with Buildx + QEMU, then pushes to GHCR.
4. Records the new SHA in `last-built-sha.txt` so the next run skips it.

You can also trigger a run manually from the Actions tab (Run workflow).

## Notes

- Unofficial project, not affiliated with Ravelon. Upstream code is built unmodified from the upstream repo at the recorded commit.
- QEMU-emulated arm64 builds are slow (tens of minutes); the job timeout is 120 minutes.
- GitHub may pause scheduled workflows after 60 days without repository activity; each successful build commits a SHA update, which counts as activity.
