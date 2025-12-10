# Migrating legacy multimedia workloads

``` shell
export GITHUB_PAT=YOUR_TOKEN
echo $GITHUB_PAT | oras login ghcr.io -u $GITHUB_USERNAME --password-stdin
```

oci-scan → produces canonical relative paths (or reuses your hand-curated list)
sync → generates .gitignore entries like src/web-apps/...
untrack → removes those from the git index without double-prefix bugs
purge-assets → deletes the actual files under src/...
clean → just nukes the tar + list, not your assets

du -ah . | sort -hr
