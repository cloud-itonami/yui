# yui 結

**結 (yui — to bind/join)** is the empowerment bot of the itonami fleet: its
one job is to move people across the five-domain shared participation funnel
(murakumo.cloud → kotobase.net → isekai.network → kotoba.cloud → itonami.cloud,
one economy, five doors) and to measure, honestly, whether the moves worked.
This is the standalone west repository `cloud-itonami/yui`. EDN is canonical
for identity, manifest, ontology, schema, seed data, and the append-only
observation ledger.

## Why "yui" (名前が機能を示さないので冒頭で名乗る)

結ぶ = to bind. The bot's domain IS binding: five doors into one funnel,
contributors into reputation, reputation back into organic reach (the R loop
of its system-dynamics model). It does not grow traffic; it ties stages
together. Everything else in this README follows from that one verb.

## Charter gates (G1–G6, enforced in code, not prose)

| gate | rule | where enforced |
|---|---|---|
| G1 G-mechanism | only `aligned-mechanisms` may enter the intervention catalog; engagement-maximizing / ad-targeting / extraction are UNREPRESENTABLE | `src/yui/coscientist.cljs` `review` |
| G2 G-empower | every candidate must move a participant UP a funnel stage or reduce churn — raw traffic alone is not empowerment | review |
| G3 G-measured | every candidate names the SD-model parameter it acts on + the real datum constraining it | review |
| G4 G-honesty | unmeasured parameters may run as scenarios; their outputs are ranked, never forecast. Measured/assumed is labelled on every number | `90-docs/system-dynamics/yui/yui-run.cljs` |
| G5 G-leash | yui identifies and ranks; committing resources (credits, deploys, spend) is outside the identification layer | this README + manifest |
| G6 G-no-fiat | yui never mints or moves value; BOT/KUMO/YATA economics are ADR-2608291009's alone | review + governor |

## What it runs

```bash
bb test          # charter gates + deterministic tournament tests
nbb 90-docs/system-dynamics/yui/yui-run.cljs        # (in superproject) the XMILE scenario run
nbb 90-docs/system-dynamics/yui/yui-iteration.cljs  # co-scientist iteration
```

- The five-domain participation model: OASIS XMILE 1.0 via
  `kotoba-lang/org-oasis-open-xmile` (same engine as observatory-cadence).
  Scenario ranking is the valid read; absolute headcounts are not.
- The co-scientist loop: Generate → Review (charter gates) → Rank
  (deterministic Elo, fitness = measured sim gain) → Evolve → Meta-review.
  The judge is a simulation, never an LLM debate.

## Honesty invariants (non-negotiable)

- Uniques without stage conversion is NOT growth. The model exists to stop
  that reading.
- The referral loop has never fired (isekai viral-coefficient is literally 0,
  2026-09-02). Base scenarios set it to 0. Any run that raises it is labelled
  UNMEASURED-MECHANISM.
- kotoba.cloud publishes no funnel numbers. It enters the model as an
  unmeasured door. Publishing them is the meta-hypothesis (`publish-funnel-
  per-domain`) that converts unmeasured parameters into measured ones.
- 1,958 bots with 2 price tags (ADR-2608291009 measured B) is the standing
  warning: participation without a revenue side is not an economy. yui ranks
  interventions; it does not issue anything.
