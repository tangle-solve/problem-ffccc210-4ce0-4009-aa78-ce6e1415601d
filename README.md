<!-- solve-problem:ffccc210-4ce0-4009-aa78-ce6e1415601d -->
# Spam-resistant zero-fee inclusion

A [Tangle Solve problem](https://solve.tangle.tools/problems/ffccc210-4ce0-4009-aa78-ce6e1415601d).

## What is the problem?

Design a transaction-fee mechanism for a blockchain in which some transactions can be included at **zero fee** (subsidized or free inclusion), while satisfying three competing guarantees:

1. **Meaningful zero-fee access:** a honest user without budget can get included within bounded expected time.
2. **Sybil-resistance:** an adversary splitting into many identities cannot gain disproportionate inclusion probability or throughput relative to a single honest user — i.e., per-*user* (not per-*transaction*) fairness.
3. **Paid service preservation:** under sustained adversarial spam of free transactions, paying users still receive near-baseline inclusion latency/service quality (no denial-of-service via the free tier).

Formal setting: a block-builder/validator chooses an inclusion policy mapping an arrival stream of transactions (each with an optional fee bid and a pseudonymous identity) to block slots, subject to a block-size (admission scarcity) constraint. The adversary controls an unbounded number of identities and can flood free transactions indefinitely, possibly also colluding with the validator. There may be a bounded subsidy budget funding free inclusion. The goal is a mechanism with provable guarantees on (a) expected inclusion time for zero-fee honest users, (b) ratio of adversary-to-honest inclusion share, and (c) latency for fee-paying users, as functions of adversarial load. The census leaves the exact guarantee quantifiers (bounds, budget model, adversary budget) informal — a solver must fix a precise formalization and state it.

## Why does it matter?

Feeless or subsidized inclusion matters for financial-inclusion use of blockchains and for protocols (L2s, RPC relays, mempool services) offering free tiers. Without Sybil bounds, free inclusion collapses into a free denial-of-service vector: spam crowds out paying users and exhausts subsidy budgets. A mechanism achieving all three guarantees would let protocols subsidize access without subsidizing attack — the "subsidized access without subsidized DoS" framing — and would be a building block for fair public-resource allocation under pseudonymity more broadly.

## What's already known?

- Classical fee mechanism design (first-price auctions, EIP-1559 as in Roughgarden 2020's transaction-fee mechanism analysis) gives incentive compatibility for *paying* users but nothing for zero-fee users.
- The cited key work, Damle, Srivastava and Gujar (2024), constructs a randomized fairness mechanism for feeless settings — roughly, fair inclusion via random selection so that no adversary can predict/manipulate ordering, achieving some notion of fairness without fees. (Read it to extract exact guarantees; this is the baseline to extend.)
- Known gap: randomized fairness treats transactions/identities symmetrically, so a Sybil adversary multiplying identities gains linear advantage; and free admission under load degrades paid service. Prior fairness work (order fairness à la Aequitas/Themis, time-chain fairness) addresses ordering, not admission scarcity or Sybil bounds.
- Related literature on fair queueing / admission control with unknown identities (e.g., identity-free rate limiting) exists in networking but not integrated with fee mechanisms and subsidy budgets.

## What would count as solving it?

Any of the following, each with a fully specified model:
- **Positive result:** a mechanism with proofs that (i) honest zero-fee users are included in expected time polylogarithmic (or otherwise bounded) in adversarial load; (ii) an adversary with k Sybils gets at most O(1) (or specified sublinear in k) times a single honest user's inclusion probability; (iii) paying users' latency degrades by at most a bounded factor under any adversarial free-tier flood; (iv) subsidy budget consumption is bounded.
- **Lower bound / impossibility:** a proof that some subset of the guarantees is unachievable simultaneously (e.g., meaningful free access + Sybil resistance requires identity assumptions or unbounded subsidy), which would formally delimit the design space.
- **Intermediate:** trade-off curves quantifying how much Sybil advantage or paid-service degradation is unavoidable given a subsidy budget and scarcity level.

## How to start

1. Read arXiv:2402.04634 carefully — extract its exact threat model, fairness definition, and randomization construction; identify precisely where Sybil multiplication breaks its guarantee (this is your formalization seed).
2. Fix a model: define identities, adversarial flood rate λ vs. block capacity c, subsidy budget B, and quantified guarantees (expected latency, share ratios). Ambiguity in the census means *you* set the standard — state it explicitly.
3. Consider known Sybil-mitigation primitives as ingredients: stake/deposit-bounded free tiers, proof-of-personhood oracles, per-identity verifiable delay / cost functions, or lottery-based admission (each identity gets equal lottery weight). Analyze which preserves paid throughput.
4. Main obstacle: without any identity cost, an adversary with unbounded identities and you with only per-user fairness axioms typically yields impossibility — so either import weak identity assumptions or aim for a trade-off theorem. Try the impossibility proof first; it is likely tractable and sharpens the positive target.

## Key references

- Damle, K., Srivastava, A., Gujar, S. (2024). *No Transaction Fees? No Problem! Achieving Fairness in Transaction Fee Mechanism Design.* arXiv:2402.04634. https://arxiv.org/abs/2402.04634 — Proposes a randomized transaction fee mechanism achieving fairness guarantees without requiring users to pay fees; the direct baseline whose Sybil and load robustness must be strengthened. (Verify exact results from the paper.)
- Roughgarden, T. (2020). *Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559.* arXiv:2012.00854. https://arxiv.org/abs/2012.00854 — Formalizes transaction-fee mechanism design (UIC, MIC, OCA-proofness) for EIP-1559; supplies the incentive-compatibility framework and definitions any extension must preserve.
- Fair ordering / order-fairness literature (Aequitas, Themis) — establishes what "fairness" has meant in blockchain consensus (ordering, not admission); useful to distinguish from admission fairness. (verify specific citations)

## Warnings

- The census record is programmatic, not a precise problem statement; the quantifiers (how much Sybil advantage is "bounded," what latency degradation is acceptable) are unspecified and must be fixed by the solver.
- Do not assume the 2024 paper already handles Sybils or adversarial load — re-derive its guarantees rather than trusting the title.
- Beware definitional traps: "fairness per transaction" vs. "per identity" are very different under Sybils; state which.
- A trivially "safe" solution (make free inclusion effectively impossible) satisfies DoS-resistance vacuously — your formalization must rule this out by requiring non-vacuous free-tier access.
- Subsidy budget sustainability interacts with adversarial load; ignoring the budget gives misleading positive results.

---
*`BCGT-000286` · flagged for review*

## Acceptance guidance

Any of the following, each with a fully specified model:
- **Positive result:** a mechanism with proofs that (i) honest zero-fee users are included in expected time polylogarithmic (or otherwise bounded) in adversarial load; (ii) an adversary with k Sybils gets at most O(1) (or specified sublinear in k) times a single honest user's inclusion probability; (iii) paying users' latency degrades by at most a bounded factor under any adversarial free-tier flood; (iv) subsidy budget consumption is bounded.
- **Lower bound / impossibility:** a proof that some subset of the guarantees is unachievable simultaneously (e.g., meaningful free access + Sybil resistance requires identity assumptions or unbounded subsidy), which would formally delimit the design space.
- **Intermediate:** trade-off curves quantifying how much Sybil advantage or paid-service degradation is unavoidable given a subsidy budget and scarcity level.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ffccc210-4ce0-4009-aa78-ce6e1415601d?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000286.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000286)
