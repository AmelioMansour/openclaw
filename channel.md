# PR #182085 — invalid channel drafts block Save

- Integrated head: `32e6b9e0621e700aba2935aa474b07e6c874535b`.
- Integration base: `b01ec056ade05bad61475ba7af3305cabb9740af`.
- Route: `/settings/channels`, Discord details, production build on a real isolated Gateway. Discord was disabled and had no bot credential; no real service was contacted.

## Observed behavior

1. Set the existing `mentionAliases.primary` text field from synthetic `111` to `invalid-alias`. The invalid text stays visible and focused, `aria-invalid=true`, an inline constraint error appears, Save is disabled, Reload remains enabled. A disabled Save click does not persist the invalid draft; the file still contains `111`.
2. Correct it to `555`. The error clears and the Gateway-acknowledged persisted file contains `555`. **Valid edits auto-persisted on this current runtime; by the subsequent explicit Save attempt, the button was disabled because no dirty config remained. This is not an assertion that an enabled Save request was exercised.**
3. Set `maxLinesPerMessage` to invalid `0`: text remains focused/visible, Save is disabled, Reload is enabled. Invoke Reload while invalid. It remains usable but does not discard the local invalid draft when the backing numeric value is unchanged. Correct to `21`; the persisted file contains `21`.

![Invalid alias retained with inline error](images/channel-invalid.jpg)

## Checks

- Pinned-head `view.config.browser.test.tsx`: 1 Chromium regression test passed (another dirty field cannot be saved while a channel scalar draft is invalid).
- Exact-head production Vite build passed.
- Independent read-only diff reviews found no confirmed introduced correctness findings.

No claim is made that all repository tests or newly triggered CI have completed. JSON-invalid and additional rejection-path coverage remain useful optional extensions; only the concrete cases above were exercised through the shipped route.

## Exact-head review and bounded followthrough

ClawSweeper reviewed32e6b9e0621e700aba2935aa474b07e6c874535b on October31,2026: findings0, exact-headCI green, but explicit enabledSave proof remains required. Review: https://github.com/openclaw/openclaw/pull/182085#issuecomment-6378559101

Two bounded, native-interaction attempts did not satisfy the prerequisites and are NOT passing evidence. The separated nativeEnter diagnostic confirmed Name/scalar/Save in the real novalidate form and viewport, but its submission snapshot took1505ms, beyond the unchanged800ms autosave debounce. The numeric scalar read1(valid), not requested0; one config.set was already sent. The runner stopped before the corrected submission. This does not isolate invalid explicitSave and establishes no introduced product defect. No claim that the patch disables normal autosave. Fresh isolated fixtures exited cleanly and their listener absence was verified. Original screenshot limitations above remain unchanged. No corrective source change or new merge/rereview request is justified by these unsuccessful proof attempts.
