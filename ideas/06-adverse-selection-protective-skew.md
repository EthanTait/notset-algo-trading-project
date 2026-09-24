# 06 · Adverse selection / protective skew (PARKED)

**Status:** parked. Kept for the record and as a possible application slide.
**One line:** use cross-asset (BTC-perp) flow to predict when alt quotes go stale, and skew/widen quotes to reduce adverse selection.

## Why it was attractive

It maps directly onto real desk work: futures-led protective skew on RFQ quotes in rates/FX, recast as BTC-perp leading alt-perps.

## Why it's parked

1. **No way to verify success without client or fill data.** Any RFQ-desk simulation invents the client stream, and the result then depends on those assumptions.
2. **Replaying real taker flow as synthetic RFQs** fixes some of that (the information content is real), but still requires acceptance, hedging and competition models. That's a desk rebuild, not a research question.
3. **It changes the project's identity** from signal research to maker-desk simulation, which is too tied to one desk's workflow for a two-person class project.
4. **Markout-only analysis** reduces to a predictive-power distribution plus a few large jumps, which is exactly the event trap `docs/EVALUATION.md` is designed to avoid.

## What survives into other ideas

- **Markouts** (`q/lib` `markout` via `aj`) are a useful generic tool for any idea.
- The cross-asset lead signal lives on in **01**, market-neutral and without the maker framing.
- A single "how a desk would use this" slide can come at the end of the presentation, without being built.

## Revisit if

- The course offers fill-level or counterparty-attributed data (e.g. Hyperliquid wallet-attributed trades could serve as "clients" with persistent identity), or
- 01 finds a strong, fast lead, making a skew overlay a small add-on rather than the thesis.
