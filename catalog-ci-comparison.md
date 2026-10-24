## PR #182023: focused CI attribution comparison

Pinned base: `40172b9e4ff2f2f47def87b8105370d915a669b1`. Pinned head: `802eb01bfa05d4e7935810a7ce0ae72307a3a3c9`.

Four whole failing suites retained their original case order: `chat-workspace-directory-race.e2e.test.ts`, `chat-file-links.e2e.test.ts`, `chat-workspace-downloads.e2e.test.ts`, and `session-management.operator-scopes.e2e.test.ts`. No test, production source, assertions, timeout, or dependency versions changed. Independent read-only review established no introduced line defect. Its presentation/pin hypotheses remain unproven; they did not reproduce in these comparisons.

| Prepared Windows environment | Head | Base |
|---|---|---|
| Node24.22.1, maxWorkers1 | 4 files / 16 tests passed;131.86s |4 files /16 tests passed;121.23s|
| Node24.22.1, maxWorkers3 |4 files /16 tests passed;100.35s|4 files /16 tests passed;99.15s|

Each invocation performed a fresh production Vite build from its checked-out source and used synthetic Gateway fixtures and isolated Chromium contexts. The checkout was restored clean to the pinned head after both baseline comparisons. Portable Node executable SHA256: `D702DAA815EABBFF763C7D3808E235CF031CF2CA70211B56847BC72F51E240E5`.

Command (substitute pinned portable Node24.22.1; run at each commit):
```text
node scripts/run-vitest.mjs run --config test/vitest/vitest.ui-e2e.config.ts --configLoader runner ui/src/e2e/chat-workspace-directory-race.e2e.test.ts ui/src/e2e/chat-file-links.e2e.test.ts ui/src/e2e/chat-workspace-downloads.e2e.test.ts ui/src/e2e/session-management.operator-scopes.e2e.test.ts --maxWorkers=3
```
The3worker comparisons set `OPENCLAW_UI_E2E_SKIP_REAL_GATEWAY=1`; all comparisons provided an installed Chromium executable.

Original hosted CI failures, run40042290366 attempt1:
- Directory-race: locator **reports** absent after inventory reader already opened; older deferred directory response not yet released.
- File-links: **Filter files: All** not visible after Show in Files listing request.
- Downloads: second loop iteration could not find **large.txt** after first small-file save completed; no large download request yet.
- Session scopes: final pinned sidebar count0, expected1 in SESSION_WRITE.

**Limitations:** 64 passing test executions demonstrate focused local base/head equivalence for these two worker settings, not Linux/full weighted-shard equivalence or the cause of the hosted failures. No claim that CI is green or proven unrelated. CI job display labels use /9, but actual logged shard count is8. Full original weighted shard reruns have not been simulated locally. One failed-jobs hosted retry is warranted after retaining this attribution evidence; its result must be reported separately.
