---
name: code-pruning
description: >-
  Use whenever the user asks to remove, delete, drop, or get rid of anything in code: a feature, page, component, endpoint, setting, dependency, or file. Removes the target completely and safely with zero leftovers: every reference, import, route, UI entry, config key, style, asset, test, doc, and dependency that existed only for it, while keeping the app building and working.
author: "Armin Dashti"
uuid: 2c699555-9c5c-40b1-aa40-df0341cfee00
---
# Code Pruning

## Goal

When the user asks to remove something, it must be gone **completely**: no dead code, no dangling references, no orphan files, no unused imports, config, styles, assets, tests, or docs. The app must still build, run, and pass tests afterwards.

## Rules

1. **No leftovers.** Remove every trace of the target (see checklist). A removal with leftovers is not done.
2. **Remove callers too.** Code that uses the target is updated so it no longer does: delete the call, the menu item, the route, the button, the condition. Do not leave stubs, `TODO`s, commented-out code, or empty handlers.
3. **Shared code stays.** If something is also used by code that is not being removed, keep it and only remove the target's usage of it.
4. **Never break the app.** After removal, the project builds, lints, and tests pass (or failures are explained and unrelated).
5. **Ask when unsure.** If the target is ambiguous, dynamic usage can't be ruled out, removal touches a public API, a database schema / data, or would change behavior beyond the target, ask the user first.
6. **Plan first for big removals.** When more than a few files are touched, show the removal plan and wait for a yes.
7. **Respect boundaries.** Don't touch generated or vendor code, secrets, or `.gitignore`d local files unless the user targets them. Don't reformat unrelated code.

## Leftover checklist

Search for the target's names, paths, keys, and strings (case variants included) and clear every hit:

- [ ] Source files, classes, functions, components, hooks, types, constants
- [ ] Imports, exports, re-exports, barrel / index files
- [ ] Call sites, event handlers, conditions, feature-flag branches
- [ ] Routes, navigation, menus, buttons, links, page titles
- [ ] DI / plugin / service registration, middleware, jobs, cron, CLI commands
- [ ] Config and env keys, settings UI, defaults, `.env.example`
- [ ] API endpoints, client calls, DTOs / schemas, validation
- [ ] Styles, CSS classes, icons, images, fonts, translations / i18n strings
- [ ] Tests, fixtures, mocks, snapshots, stories
- [ ] Docs, README, changelog references, comments describing it
- [ ] Package dependencies used only by the target (and lockfile)
- [ ] Build, CI, Docker, deployment manifests
- [ ] Database migrations / tables (ask first; never drop data without approval)
- [ ] Empty folders and empty files left behind

## Steps

1. **Identify the target** exactly. Confirm with the user if it's ambiguous.
2. **Map it.** Search the repo (names, paths, string keys, dynamic `require` / `import()` / reflection) and list everything belonging to the target, sorted into "remove" and "shared, keep".
3. **Plan.** For bigger removals, show the plan and wait for approval.
4. **Remove.** Delete files (`git rm` in git repos), update callers, clean imports, config, styles, assets, tests, docs, and dependencies. Match the surrounding code style.
5. **Sweep.** Re-search every name, path, and key from step 2; there must be zero hits outside intentional history (git log, changelog entries the user wants kept).
6. **Verify.** Build, lint, and run tests. Run the app if practical and check the affected screens / flows.
7. **Report.**

## Report template

One grid with exactly one data row. Every cell is a number: how many files were added, edited, and deleted, and how many lines were added, edited, and deleted in total.

```markdown
### Removal Report: <target>

| Added file | Edited file | Deleted file | Added line | Edited line | Deleted line |
|------------|-------------|--------------|------------|-------------|--------------|
| 0 | 4 | 3 | 2 | 6 | 241 |
```

Under the grid, add one line each only when relevant: **Kept (shared)**, **Verification** (build / lint / tests result), **Needs your decision**.