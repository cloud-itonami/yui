# yui 結 — operator quickstart

Everything below was executed on 2026-09-05 against `d75520f` and the
superproject at `a6f7ac8`. Every command shows the output it actually
produced. **If a step here does not reproduce, the step is the bug** — this
file is meant to be walked, not read.

## 0. What you are operating

yui is an *identification* actor: it ranks empowerment interventions and
records evidence. It commits no resources and moves no value (charter gates
G5 / G6). Operating it means running two things and reading their output:

| | what | where the code lives |
|---|---|---|
| **the model** | five-domain participation, OASIS XMILE 1.0 | superproject `90-docs/system-dynamics/yui/` |
| **the loop** | co-scientist Generate→Review→Rank→Evolve→Meta | superproject, *and* a second copy in this repo (see §5) |

⚠ **This repo cannot run its own model.** The XMILE model and both runners
live in the superproject `com-junkawasaki/root`, not here. `dependencies.edn`
states this; the commands below are the concrete form of it. A standalone
clone of `cloud-itonami/yui` gets you §2 only.

## 1. Prerequisites

```bash
node --version    # v26.7.0
npm --version     # 11.19.0
nbb --version     # nbb v1.5.212
```

`nbb` is the only script host (ADR-2607173000). **`bb` is retired** — it may
still be on your PATH (`babashka v1.12.218` here), but nothing in this repo
uses it and there is no `bb.edn`.

## 2. Run this repo's tests

From the repo root:

```bash
nbb --classpath src:test test/yui/coscientist_test.kotoba
```

```
Testing yui.coscientist-test

Ran 7 tests containing 12 assertions.
0 failures, 0 errors.
```

Exit code 0. These are the charter-gate tests: they assert that a forbidden
mechanism is vetoed (G-mechanism), that a candidate with no model parameter
is vetoed (G-measured), that a candidate with no prediction is vetoed
(G-falsifiable), that the whole shipped catalog survives, and that ranking is
deterministic and gain-ordered.

## 3. Run the model (superproject)

From the **superproject root** (`com-junkawasaki/root`), not from this repo:

```bash
SD_OUT=/tmp/yui-out nbb --classpath \
  "90-docs/system-dynamics/nbb-shim:orgs/kotoba-lang/org-oasis-open-xmile/src:90-docs/system-dynamics/yui" \
  90-docs/system-dynamics/yui/yui-run.cljs
```

```
═══ 結 (yui) five-domain participation — OASIS XMILE 1.0 ═══
measured weekly door reach: 3376 (murakumo 785 + kotobase 1386 + isekai 627 + itonami 578) — kotoba.cloud publishes NO funnel numbers (unmeasured door)
measured visit→signup: 0.0065263 (kotobase 31/4750, 2026-09-02)
measured isekai viral coefficient: 0 — the R loop has never fired

scenario                  valid  signed_up  active    contributors  rep       crossing-week
S6-combined-empowerment   yes    175        349.8     2952.9        28        never in 104w
S1-double-visit-signup    yes    167.4      557.8     2354.7        22.3      never in 104w
S2-double-active-contrib  yes    84.3       168.6     1439.3        13.5      never in 104w
S3-halve-active-churn     yes    84.3       337.3     1415.5        13.5      never in 104w
S5-referral-strong        yes    91.9       305.5     1259.4        12.2      never in 104w
S4-referral-weak          yes    85.7       285.4     1199.5        11.4      never in 104w
S0-base-measured          yes    84.2       280.7     1185.1        11.2      never in 104w
```

**Read the ordering, not the headcounts.** S0 is the only all-measured row;
everything else is an ASSUMPTION scenario (charter gate G-honesty). Note that
*no* scenario crosses budget independence within 104 weeks — including the
combined one. That is the model's actual finding.

### ⚠ Why `SD_OUT`

`yui-run.cljs` writes two files into its output directory, and one of them is
**append-only**:

- `yui-five-domain-participation.xmile` — rewritten, **idempotent**. Measured
  across two consecutive runs: `sha1 d7ea2f5fc0cffc2bb006eeebce6e331510bc8a61`
  both times, and byte-identical to the copy committed in the superproject.
- `yui-xmile-ledger.edn` — **appended**. Measured: 1 line → 2 lines after a
  second run.

So running without `SD_OUT` appends a duplicate ledger line to the
superproject working tree every time anyone walks this quickstart. Point
`SD_OUT` at a scratch directory unless you intend to record an observation.

## 4. Run the co-scientist iteration (superproject)

```bash
nbb --classpath "90-docs/system-dynamics/yui" \
  90-docs/system-dynamics/yui/yui-iteration.cljs
```

```
═══ 結 (yui) co-scientist iteration 1 — 2026-09-02 ═══

-- Review (charter gates) --
surviving: 6  vetoed: 0

-- Ranking (deterministic Elo; fitness = measured sim gain) --
  1. yui-h1-self-serve-onboarding-per-door
  2. yui-h2-contributor-starter-kits
  3. yui-h3-active-retention-windows
  4. yui-h5-reputation-public-ledger
  5. yui-h4-publish-funnel-per-domain
  6. yui-h6-work-for-credits-handbook

-- Evolved --
  yui-evolved-yui-h1-self-serve-onboarding-per-door+yui-h2-contributor-starter-kits
  mechanisms: onboarding-fix → empowerment-tooling
```

This runner needs **only** the yui directory on the classpath. The header
comment inside `yui-iteration.cljs` also lists `nbb-shim`, `org-oasis-open-xmile`
and `dynamics`; measured, the output is byte-identical with or without them,
because `yui-coscientist` requires nothing but `clojure.string`.

### Checking the loop against the model

The gains in `yui-iteration.cljs` are hardcoded, so they can silently drift
from §3. Reconcile them by hand against the S0 base of 1185.1:

| candidate | scenario | §3 value | − S0 | hardcoded |
|---|---|---|---|---|
| h1 self-serve-onboarding | S1 | 2354.7 | 1169.6 | 1169.6 ✓ |
| h2 contributor-starter-kits | S2 | 1439.3 | 254.2 | 254.2 ✓ |
| h3 active-retention-windows | S3 | 1415.5 | 230.4 | 230.4 ✓ |
| h5 reputation-public-ledger | S5 | 1259.4 | 74.3 | 74.3 ✓ |

All four agree as of this walk. **Nothing enforces that** — there is no gate
tying the two files together, so redo this table whenever the model's
measured constants change.

## 5. Two copies of the co-scientist — know which one you ran

| | ns | who runs it | who tests it |
|---|---|---|---|
| `src/yui/coscientist.kotoba` (this repo) | `yui.coscientist` | nobody | §2 |
| `90-docs/system-dynamics/yui/yui_coscientist.cljs` | `yui-coscientist` | §4 | nobody |

The namespaces differ, so the files are **not** interchangeable on a
classpath. They are otherwise near-identical — measured 2026-09-05, they
differ by exactly one three-line comment and nothing else.

**The consequence is that §2 does not test the code §4 executes.** Treat a
green §2 as evidence about this repo's copy only. Reunifying them is real
work that has not been done; it is recorded as a gap below, not fixed here.

## 6. Verify the design ADR parses

```bash
nbb 90-docs/system-dynamics/yui/verify-adr.cljs
```

```
:adr/id adr-2609022000-yui-five-domain-participation-empowerment-bot
:adr/status accepted
:title-len 84
:body-len 7795
:has-measured true
ADR-EDN-OK
```

`90-docs/adr/2609022000-yui-five-domain-participation-empowerment-bot.edn` is
the design record. This repo carries no `docs/adr/` of its own.

## 7. The two fleet gates

CI for this repo is **murakumo fleet, never GitHub Actions** (ADR-2607300900).
Two gates are registered in the superproject's `scripts/fleet-ci/gates.edn`.
Both live superproject-side, and you can run either one directly — pass the
tree as the **first** argument, before any flags:

```bash
nbb scripts/fleet-ci/gates/yui-charter-gate-check.cljs .
nbb scripts/fleet-ci/gates/yui-funnel-coverage-check.cljs . --min 3
```

```
yui-charter-gate: OK — 6 candidates pass the charter gates, injected forbidden mechanisms are unrepresentable

kotobase.net  machine-readable  (200 application/json)  https://kotobase.net/api/funnel
murakumo.cloud  no-endpoint  (404 text/html)  https://murakumo.cloud/api/funnel
isekai.network  machine-readable  (200 application/octet-stream)  https://isekai.network/feed/fork-stats.edn
kotoba.cloud  no-endpoint  (404 text/html)  https://kotoba.cloud/api/funnel
itonami.cloud  machine-readable  (200 application/json; charset=utf-8)  https://itonami.cloud/api/funnel

machine-readable funnel doors: 3/5 (spa-fallback 200-but-not-data: 0, no-endpoint 404: 2, probe-failed: 0)
yui-h4 work list (doors needing a real funnel surface): murakumo.cloud, kotoba.cloud
yui-funnel-coverage: OK — 3 >= --min 3
```

Both exit 0 as of this walk. Two things to know about them:

- **`yui-funnel-coverage` makes live HTTPS probes**, so it is not hermetic —
  it reads content types rather than status codes, because a 200 from an SPA
  fallback is not data. Its output *is* the yui-h4 work list: murakumo.cloud
  and kotoba.cloud publish no machine-readable funnel. Raise `--min` when a
  door starts publishing.
- **`yui-charter-gate` carries its own copy of the charter vocabulary inline**,
  because the fleet ships the tree of the repo named in the gate entry and
  this repo's tree is not that tree. So the gate and `schema/yui.edn` /
  `src/yui/coscientist.kotoba` can drift; what catches the drift is the gate's
  own third invariant (a mechanism that is both aligned and forbidden).

Both entries are `:cd false`, which in `tick.cljs` means a green result does
**not** advance the west pin — they report, they do not promote. Separately,
the current `manifest/fleet-ci.edn` receipt contains no yui check at all, so
whether a fleet node has ever executed either gate is **unmeasured from here**
(that file holds the latest batch, not a history).

## 8. What is NOT wired up

Measured on 2026-09-05 by reading the tree, not by inference:

- **No identity, no CACAO, no kotobase writes.** `identity.edn` and
  `kotoba.app.edn` are both a single `:note` key describing what a future task
  will implement. There is no key material, no `load-or-create`, and nothing
  under `src/` touches a network — `yui.coscientist` requires `clojure.string`
  and nothing else.
- **No publication plane.** `manifest.edn` names an itonami.cloud bots-status
  surface; nothing here publishes to it.
- **No dependencies resolved.** `dependencies.edn` is a `:dependencies/note`
  listing intended deps in prose. There is no `deps.edn`, no `package.json`,
  and no lockfile. §2 works because the co-scientist has no dependencies.
- **The `data` bundle entry does not exist.** `actor.edn` lists `"data"` in
  `:actor/bundle`; there is no `data/` directory.
- **No append-only observation ledger in this repo**, despite the README
  naming one. The only ledger that exists is the superproject's
  `yui-xmile-ledger.edn` from §3.
- **The weekly cadence is not walkable, and is not weekly.** A resident
  runner does exist superproject-side — `scripts/yui-weekly.cljs`, an
  observe→evaluate→decide→act→record cycle. It is **deliberately absent from
  the steps above**, for a measured reason: its header documents
  `Env: SD_OUT to redirect report dir`, but `SD_OUT` appears nowhere else in
  the file. The script writes `90-docs/system-dynamics/yui/reports/` and
  appends to the real `yui-xmile-ledger.edn` unconditionally, so there is no
  dry run — walking it would forge an evidence line. Fixing that is the
  prerequisite for it appearing here.
  A cron entry named `yui-weekly-resident` is registered in
  `scripts/hermes-cron-jobs/hermes-cron-jobs.json`, but its schedule `kind` is
  `once` (fires 2026-09-09), not recurring — "weekly" is its name, not its
  cadence — and it sits under a profile named `itonami.bak-localmain2-20260904`.
  That file is a definition ledger which states it carries no state, so
  whether the job is live is **unmeasured from here**. `reports/` does not
  exist, so no run has completed its act step.

This is an R0 scaffold plus a working model and two passing gates. Everything
in §0–§7 is real and was walked; everything in §8 is not.
