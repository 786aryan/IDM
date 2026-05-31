# India Drought Monitor — Change Summary (v9: drought-classification bug fix)

This revision fixes a serious data bug you caught in the chatbot, and hardens the
question-answering pipeline so answers stay grounded in the data.

## The bug: wrong drought percentages (per-state + chatbot)

The per-state breakdown classified each CDI grid cell by **normalising to that week's
min/max** instead of the real CDI thresholds. That inverted the picture — e.g. Rajasthan
showed as "98.3% in drought" when it is actually one of the LEAST affected states. The
chatbot then sometimes read a single state's value and reported it as the national figure
("98.3% of India is in drought"), which is what you saw.

### Fix
`classify()` now uses the SAME absolute CDI thresholds as the map engine and the WCL legend:

  > -0.5 Normal · -0.5..-0.8 D0 · -0.8..-1.3 D1 · -1.3..-1.6 D2 · -1.6..-2.0 D3 · <= -2.0 D4

Applied in BOTH places that used the old normalisation:
- `assets/ai/idm-ai.js` (the chatbot's `drought_state_latest` table)
- `data-tables.html` (the per-state table page — it had the same bug and was also wrong)

After the fix the numbers reconcile with the national series: nationally ~15.3% is in
drought (84.7% normal); the most-affected states are small/hilly regions (Delhi ~52%,
Arunachal/J&K ~38-40%), and Gujarat / West Bengal / Rajasthan are correctly near 0%.

## Hardening the chatbot so it doesn't "free-form"

You also noticed it answered without really using a query. Three changes make answers
strictly data-grounded:

1. **Clearer table-selection rules in the schema.** National questions ("how much of India
   is in drought") MUST use `drought_timeseries` filtered to the latest week ('in drought'
   = d0_pct, 'normal' = normal_pct); the rules now explicitly say to NEVER read a national
   figure from the per-state table, and never present one state's value as national.
2. **Stricter answer prompt.** The model is told every number in its answer must appear
   verbatim in the returned rows — no estimating, recalling, or inferring from general
   knowledge — and to say it doesn't have a figure rather than guess.
3. The read-only single-SELECT guard and one-retry behaviour are unchanged.

Verified headless: for "How much of India is in drought right now?" the pipeline now runs
`SELECT normal_pct, d0_pct FROM drought_timeseries ORDER BY date DESC LIMIT 1`, passes
`{normal_pct: 84.7, d0_pct: 15.3}` to the answer step, and replies "about 15.3% of India is
in drought, 84.7% normal."

## Files touched

- `assets/ai/idm-ai.js` — absolute-threshold `classify()`; clarified schema rules; stricter answer system prompt.
- `data-tables.html` — same `classify()` fix (removes the inverted per-state numbers).

## Validation

All 17 pages load with zero JS errors and the chatbot present. Per-state numbers now match
the national series; the chatbot answers the national drought question correctly end-to-end
(mocked model, since the LAN Ollama server isn't reachable from the build environment).

## Note

The LAN model still can't be exercised from here, so the live answer quality depends on
qwen3.5:4b following the (now much stricter) prompts. If you ever see it stray, the cleanest
next step is a tiny deterministic guard for the few most-common questions; say the word and
I'll add it.
