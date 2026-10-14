# Harness 2.4.1 validation

Version 2.4.1, build 138, fixes main-thread stalls during window resizing and groups
the host icon, name and arrow in a native AppKit menu control. Public protocol 2,
session-host protocol 10 and terminal checkpoint schema 2 are unchanged.

## Local verification

- The full Swift suite with live socket and PTY fixtures executed 2,642 tests with
  59 skipped and no failures. Skips are opt-in benchmarks and unavailable fixtures.
- Renderer, terminal-kit and app suites also passed a focused run of 566 tests with
  four skipped before release preparation.
- All 40 resize tests pass with coverage enabled. The three queue-blocking cases
  also pass 20 consecutive runs each (60 checks) after making their hidden-window
  fixture wait for actual presentation and drive drawable retries.
- Five focused tab-spacing and dragging tests pass with the native host control.
- Debug preview build, changed-line lint and `git diff --check` pass.
- Isolated GUI stress runs cover idle and streaming terminals, split panes and deep
  history. Main-thread Metal drawable and GPU scheduling waits found in 2.4.0 are
  absent in the patched samples. See the [investigation](window-resize-stalls-2026-10-13.md)
  for methods, measurements and their limits.
- The native host control was checked in an isolated GUI preview, including the
  compact icon/name/arrow group and the selectable local-host menu row.

## Publication checks

The release workflow requires successful main-branch CI for the exact source commit,
verifies Developer ID signatures, notarizes and staples the app and DMG, checks
Gatekeeper acceptance, and tests an isolated installation of the DMG. It compares
public downloads with the tested artifacts before updating the signed feeds on
both supported domains.


## Publication receipts

- Release source: `8db95637146d6e1e4e0aabc1c092009d935e71dc`, tagged `v2.4.1`.
- [Shipping CI](https://github.com/robzilla1738/harness-terminal/actions/runs/38499279374)
  passed every required job, including the legacy-upgrade and session-survival
  checks. macOS executed 2,642 tests with 62 skipped and no failures; Linux executed
  1,972 tests with seven skipped and no failures. All three queue-blocking resize
  cases passed; one existing window-hosted presentation test skipped because its
  drawable was unavailable.
- The optional benchmark tests had no failures. Their non-blocking comparison
  against the stored baseline flagged 49 slower and 21 faster results among 76
  measurements. These hosted-runner comparisons do not establish a broad throughput
  improvement; the resize evidence is the blocked-worker tests and isolated GUI
  traces linked above.
- The [release workflow](https://github.com/robzilla1738/harness-terminal/actions/runs/38500289533)
  passed, including signing, notarization, the installed-DMG smoke test, public
  download comparison and both live-feed comparisons.
- An independent public download matched `SHA256SUMS` and GitHub's asset digest,
  passed `hdiutil verify`, and was accepted by Gatekeeper as notarized Developer ID.
  Both the DMG and app have valid stapled tickets; the app reports 2.4.1 (138).
- After website deployment, both `thebestterminal.com` and `harnesscli.dev` served
  byte-for-byte matches of the published signed appcast. The website download
  button points to the latest GitHub release.

`Harness.dmg` is 27,778,167 bytes. Its SHA-256 is:

```text
e170a8f2f519e2df3af37b74562f1ead90f1d853cee908c26de6538e7efbcf6e
```
