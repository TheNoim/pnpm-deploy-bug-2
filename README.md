# pnpm deploy and frozen lockfile

## How to reproduce:

```bash
CI=true pnpm --filter="{packages/p2}" deploy dist
```

## Result

```log
Packages are copied from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/user/Library/pnpm/store/v3
  Virtual store is at:             dist/node_modules/.pnpm
Lockfile is up-to-date, resolution step is skipped
 ERR_PNPM_OUTDATED_LOCKFILE  Cannot install with "frozen-lockfile" because pnpm-lock.yaml is not up-to-date with packages/p2/package.json

Note that in CI environments this setting is true by default. If you still need to run install in such cases, use "pnpm install --no-frozen-lockfile"
```

## The source of the issue

pnpm tries to modify the lockfile which results in these changes:

```diff
diff --git a/pnpm-lock.yaml b/pnpm-lock.yaml
index 7a04ea2..194e09e 100644
--- a/pnpm-lock.yaml
+++ b/pnpm-lock.yaml
@@ -12,4 +12,15 @@ importers:
     specifiers:
       p1: workspace:*
     dependencies:
-      p1: link:../p1
+      p1: file:packages/p1
+    dependenciesMeta:
+      p1:
+        injected: true
+
+packages:
+
+  file:packages/p1:
+    resolution: {directory: packages/p1, type: directory}
+    name: p1
+    version: 1.0.0
+    dev: false
```

The reason for this change is from this part of the deploy plugin: [hook](https://github.com/pnpm/pnpm/blob/main/packages/plugin-commands-deploy/src/deploy.ts#L101)