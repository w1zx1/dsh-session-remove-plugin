# Local changes to better-dsh-session-deletetool 0.5.3

This folder is a modified copy of the upstream package
[`better-dsh-session-deletetool@0.5.3`](https://www.npmjs.com/package/better-dsh-session-deletetool)
(MIT, © Lzcdebear). Only `client.js` and `host.js` differ from the published package.

## Changes

### `host.js`

- **Blank sessions are hidden from the bulk list.** DSH creates an empty session when a
  workspace is added or a new session is started and never used. The sidebar hides those
  rows; the dialog used to list them under their directory name, so they read as a
  duplicate of the workspace. `blankSessionIds()` reads the `sessionListMetadata.blank`
  flag from the `session_projcache` projection (no log I/O) and filters those sessions
  out of the catalog and its counters. If the cache row is missing or malformed the
  session stays listed.

### `client.js`

- **Language binding fixed.** The plugin used to call `ctx.get('locale')` once at apply
  time and fall back to the built-in `zh` dictionary when the service was not ready yet,
  so a dialog opened right after startup could render in Chinese. The client half now
  injects `locale`, and picks the dictionary from the page's active locale at call time:
  Chinese only when the app itself is Chinese, English otherwise.
- **`open` badge removed** from bulk rows. Rows only carry activity badges ("running
  turn", "running background task", "running subagent").
- **Stopping work is unconditional.** The "confirming stops the unfinished work" checkbox
  is gone; both the single and the bulk delete always send `stop: true`. The confirm
  button reads "Stop and remove N" only when a selected session is actually running.
- **Selection uses the DSH accent colour.** Checkbox `accent-color` is
  `--dsw-alias-state-business-primary` instead of the error red.
- **Wording:** "delete conversation" became "remove session" everywhere in the English
  dictionary and the session menu row is now **Remove session**.
- **Menu position:** the row is registered with `order: 425`, directly after
  *Archive session* and before *Copy Session ID* (was `500`, i.e. last).
- Activity family labels (`running turn` / `running subagent` / `running background
  task`) are translated client-side from the Host's `kind` instead of using its
  hard-coded Chinese label.

## Installing this copy

The profile already runs an installed copy at
`%USERPROFILE%\.dsh\profiles\desktop\node_modules\better-dsh-session-deletetool`.
To use this folder instead, remove the installed package first and link this one:

```
dsh plugin --profile desktop remove better-dsh-session-deletetool
dsh plugin --profile desktop add link:C:\Users\sanechka\Documents\deepseek-harness\default-workspace\better-dsh-session-deletetool
```

Both halves load at start-up; the client bundle is cached per Host process, so a full
restart of DSH is required after any change. Note that a plugin update from npm would
overwrite an in-place patch, so keep this folder as the source of truth.
