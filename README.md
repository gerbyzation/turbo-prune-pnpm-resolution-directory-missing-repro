# Turbo prune: pnpm injected local lib missing resolution directory

When using `turbo prune` on a workspace that has a local dependency injected
the generated lockfile has an invalid `resolution` field. When proceeding to install
the pruned repo this will cause the following error:

```
❯ pnpm i
Scope: all 3 workspace projects
Lockfile is up to date, resolution step is skipped
Packages: +8
++++++++
 ERR_INVALID_ARG_TYPE  The "path" argument must be of type string. Received undefined

pnpm [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string. Received undefined
    at new NodeError (node:internal/errors:372:5)
    at validateString (node:internal/validators:120:11)
    at Object.join (node:path:1172:7)
    at directoryFetcher (/Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:102008:36)
    at fetcher (/Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:125428:22)
    at ctx.requestsQueue.add.priority.priority (/Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:125324:78)
    at run (/Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:124809:90)
    at PQueue._tryToStartAnother (/Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:124757:13)
    at /Users/gerbenneven/.fnm/node-versions/v16.15.1/installation/lib/node_modules/pnpm/dist/pnpm.cjs:124822:16
    at new Promise (<anonymous>)
Packages are copied from the content-addressable store to the virtual store.
  Content-addressable store is at: /Users/gerbenneven/Library/pnpm/store/v3
  Virtual store is at:             node_modules/.pnpm
Progress: resolved 1, reused 0, downloaded 0, added 0
```

The original lockfile has the following entry for `lib-a`:
```yaml
  file:libraries/lib-b:
    resolution: {directory: libraries/lib-b, type: directory}
    name: lib-b
    version: 1.0.0
    dev: false
```

The pruned repo generates the following:
```yaml
  file:libraries/lib-b:
    resolution:
      type: directory
    name: lib-b
    version: 1.0.0
    dev: false
```
Note the missing directory field on the resolution object.

## Repro steps

```
pnpm exec turbo prune --scope lib-a
cd out
pnpm install
```