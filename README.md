# pnpm deploy and frozen lockfile

## Reproduce:

```bash
CI=true pnpm --filter="{packages/p2}" deploy dist
```

### Result

```
Packages are copied from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/user/Library/pnpm/store/v3
  Virtual store is at:             dist/node_modules/.pnpm
Lockfile is up-to-date, resolution step is skipped
 ERR_PNPM_OUTDATED_LOCKFILE  Cannot install with "frozen-lockfile" because pnpm-lock.yaml is not up-to-date with packages/p2/package.json

Note that in CI environments this setting is true by default. If you still need to run install in such cases, use "pnpm install --no-frozen-lockfile"
```