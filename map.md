# PR #182063 — collection drafts survive sibling edits

- Locally integrated head: `e1382fde346f345695d70ff0eff6199b3b91d88f`.
- Integration base: `b01ec056ade05bad61475ba7af3305cabb9740af`.
- Route: `/settings/advanced`, Discord mention-alias map, exact-head production Vite build served by a real isolated Gateway. No Discord connection was enabled.

## Observed behavior

1. Existing alias: `primary: "111"`. Open Add Entry and type pending `secondary: "222"`.
2. Edit the existing primary value to `333`, then `444`. After each successive edit, the pending key and value remain unchanged in the mounted entry editor.
3. Commit the pending addition. The Gateway-acknowledged persisted file contains `{primary:"444", secondary:"222"}`, and pending-entry controls close.
4. Open a new pending entry `unfinished: "777"`. In a **second real browser owner** on the same isolated Gateway, replace the primary value with `999`. The original mounted map receives the acknowledged external replacement, shows `{primary:"999", secondary:"222"}`, and the unfinished draft inputs disappear. The persisted file matches; `unfinished` was not written. No navigation/remount or app-state injection was used to manufacture the reset.

![Original mounted map after externally acknowledged replacement](images/map-reset.jpg)

## Checks and limits

- Exact-head production Vite build passed.
- Independent static reviewers found no confirmed introduced correctness findings.
- Focused browser test startup encountered an archive-native `resvg` dependency-optimization error; later attempts stalled before reporting any test assertions. A final recovery quarantined only the private archive optimizer caches (no shared dependency targets or source edits) and retried with narrow discovery; it again stalled for 120 seconds before any assertion and exited nonzero. Direct Vitest and the supported alternate config loader also timed out during startup. **The map regression suite is not passing/verified. The refreshed map branch is therefore not pushed; the remote PR head remains `f663fb18bd149f1825570c934f747c9d80b12587`. This is a local test-infrastructure blocker, not an observed failing assertion.**
- Rejected child-patch coverage is still a useful extension and was not exercised by the shipped-route sequence above.
