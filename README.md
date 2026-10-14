<!-- solve-problem:84f7a260-b023-44f5-9f59-005f0092c880 -->
# Coalition-resistant leaderless fee sharing

A [Tangle Solve problem](https://solve.tangle.tools/problems/84f7a260-b023-44f5-9f59-005f0092c880).

## What is the problem?

**Setting.** In leaderless blockchains (no single proposer/sequencer, e.g., DAG-based or committee-based protocols such as Avalanche-style or Narwhal/Bullshark-style systems), a set of *producers* $P = \{p_1, \dots, p_n\}$ collectively assemble a block from a mempool of transactions. The protocol must set a transaction-fee mechanism: a *payment rule* (who pays whom) and an *inclusion rule* (which transactions enter). Formal objects:

- Each transaction $t$ has a bid $b_t$ and true value $v_t$; each producer $p_i$ has private capacity $c_i$ (e.g., private mempool access, bandwidth) and cost $k_i$ per unit of work.
- A fee-sharing/allocation rule $x: \text{bids} \to \mathbb{R}^n_{\ge 0}$ splits collected fees among producers.
- Equilibrium concept: (weak/strong) incentive compatibility against both unilateral deviations (bid manipulation, withholding) and *coalitional* deviations: a coalition $C \subseteq P$ coordinating on transaction inclusion, strategic omission, or **duplication** (re-broadcasting the same transaction to fragment fee shares).

**Problem.** Design (or prove impossibility of) a fee-sharing rule for leaderless multi-producer block production that is (i) incentive compatible for individual producers, (ii) resistant to coalitions (no coalition can profit by coordinated inclusion/omission/duplication), and (iii) scalable to heterogeneous capacities and costs — while retaining a nontrivial welfare guarantee. The census record leaves "coalition-resistant" underspecified: the most reasonable reading is *group strategyproofness / strong Nash implementation* of the inclusion–payment game, or at minimum resistance to all coalitions up to size $f$ (the resilience parameter). Ambiguity: whether coalitions may also include users, and whether private capacities mean privately observed or privately controlled.

## Why does it matter?

Shared/leaderless block production is the design direction for high-throughput L1s and DAG consensus, but fee rewards currently give committees no incentive to cooperate honestly: producers can withhold, duplicate, or hoard private mempool transactions to capture larger shares. A coalition-resistant fee-sharing rule would make decentralized block building viable without a trusted sequencer, directly enabling shared sequencing markets and committee-based fee markets. Conversely, an impossibility result here would show that leaderless protocols *must* accept welfare loss or reintroduce a leader — both outcomes are publishable and decision-relevant.

## What's already known?

- **Garimidi, Heimbach, Roughgarden (2025), arXiv:2505.17885**: introduces fee mechanism design for leaderless protocols; gives a mechanism ("FPA-EQ" per the census record — first-price-auction-style allocation with equal fee split, (verify exact name and mechanics)) achieving producer-side incentive compatibility and roughly **63.2% of optimal expected welfare at equilibrium**. Its analysis presumes symmetric producers, public/common mempool visibility, and no producer coordination — exactly the assumptions this problem removes.
- **Classical TFM literature** (Roughgarden's EIP-1559 line; Chung & Shi; Ferreira & Weinberg; Leonardos et al.): shows (a) burning fees / MPB-style rules give Bayesian-NIC but weak collusive resistance, and (b) **strong impossibility results**: no nontrivial TFM is simultaneously UIC, MIC (side-contract-free), and 2-chain-compatible in the single-proposer setting. These impossibility proofs are the first things to test against the leaderless setting.
- **Known gap**: no published mechanism handles heterogeneous private capacities + coalitional inclusion/duplication in multi-producer setting; the 63.2% welfare bound degrades (or the IC proof fails) under these extensions. *(No specific attack papers were supplied in the census; treat the above as the full prior art and verify.)*

## What would count as solving it?

Any of the following, in increasing strength:

1. **Positive — mechanism**: A fee-sharing rule $x$ with a proof that, in the multi-producer game with private capacities $c_i$, unequal costs $k_i$, and coalitions up to size $f$, every coalition-deviation is unprofitable in expectation (strong Nash or $f$-coalition-proof equilibrium), with a quantified welfare bound (even $c \cdot \text{OPT}$ for explicit $c > 0$ improves the state of the art under weaker assumptions).
2. **Positive — impossibility escape hatch**: A mechanism that is coalition-resistant only against duplication+omission (not bid manipulation) with proven welfare — partial progress worth publishing.
3. **Negative — impossibility theorem**: A proof that no symmetric-budget-balanced, individually rational fee-sharing rule is simultaneously producer-IC and resistant to even 2-producer coalitions (via duplication), under stated information assumptions.
4. **Quantitative**: Tighten the 63.2% constant — upper and lower bounds on equilibrium welfare of FPA-EQ under heterogeneous producers.

Referee criterion: full mechanism + equilibrium analysis with the deviation space (withhold, duplicate, reorder, misreport capacity) explicitly enumerated, or a clean impossibility proof with the impossibility quantified over a stated mechanism class.

## How to start

1. **Read arXiv:2505.17885 first**, especially its model section (deviation space, information structure, welfare definition) and the proof of the 63.2% bound — your first task is to identify exactly which step breaks when capacities are private or producers collude. Reproduce the IC proof; note where symmetry/equal split is used.
2. **Test the simplest attack**: two identical producers, one transaction worth $v$, equal split — show duplication (each rebroadcasting $t$ to claim origination) either is profitable (motivating the problem) or is already handled. Formalize "duplication" as a well-defined action in the game; this is currently the vaguest part of the record.
3. **Import the single-proposer collusion toolkit**: try adapting Chung–Shi-style side-contract arguments (finite mechanisms, MIC definitions) to the multi-producer split — if a two-producer impossibility falls out quickly, that's a result.
4. **Mechanism candidates**: unequal splits proportional to verifiable contribution (e.g., VCG-flavored or Shapley-value splits) with fee burning à la EIP-1559; the main obstacle is that contribution is unverifiable when mempools are private — expect to need either cryptographic origination proofs or robust (dominant-strategy) analysis under worst-case attribution.

## Key references

- Garimidi, V., Heimbach, L., Roughgarden, T. (2025). *Transaction Fee Mechanism Design for Leaderless Blockchain Protocols*. arXiv:2505.17885. https://arxiv.org/abs/2505.17885 — Defines the leaderless TFM model and gives the first producer-incentive-compatible fee-sharing mechanism with a 63.2%-of-optimal expected welfare guarantee at equilibrium.
- Roughgarden, T. (2021). *Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559*. EC '21 / arXiv:2012.00854. — Establishes the UIC/MIC/chain-compatibility framework for TFM incentive analysis; the MIC ( collusion) definition is the natural starting point for the coalition-resistance notion here.
- Chung, Y.-C. and Shi, E. (2023). *Foundations of Transaction Fee Mechanism Design*. EC '23 / arXiv:2111.03151. — Proves impossibility results: no finite deterministic TFM is simultaneously UIC, MIC (2-player side contracts), and 2-side-contract-proof; the key prior impossibility template. (verify coauthor list/date)
- Leonardos, S., Monnot, B., Reijsbergen, D., Skoulakis, S. (2021). *Combinatorial Auctions for Blockchain Transaction Fee Shuffling*. arXiv:2106.04225. (verify relevance — alternative auction formulations for fee allocation.)

## Warnings

- "Coalition-resistant" and "duplication" are not formalized in the census record; you must define them before proving anything — state your definition explicitly and note it as an interpretation.
- The 63.2% figure and the mechanism name "FPA-EQ" come from a secondary census source, not the paper directly — **verify both against arXiv:2505.17885** before relying on them.
- Do not assume budget balance is free: equal splits are budget-balanced by construction, but coalition-proofness often forces burning (breaking producer participation) — the trilemma of IC / collusion-resistance / full fee distribution is the likely core tension.
- Common dead end: attempting group strategyproofness via dominant-strategy mechanisms — known to be nearly impossible for auction-like settings; work in Bayesian or correlated-type models instead.
- Private capacities make contribution attribution unverifiable; any mechanism whose split depends on unverifiable claims is attackable via misreporting — budget for either cryptographic assumptions or robust bounds.

---
*`BCGT-000282` · flagged for review*

## Acceptance guidance

Any of the following, in increasing strength:

1. **Positive — mechanism**: A fee-sharing rule $x$ with a proof that, in the multi-producer game with private capacities $c_i$, unequal costs $k_i$, and coalitions up to size $f$, every coalition-deviation is unprofitable in expectation (strong Nash or $f$-coalition-proof equilibrium), with a quantified welfare bound (even $c \cdot \text{OPT}$ for explicit $c > 0$ improves the state of the art under weaker assumptions).
2. **Positive — impossibility escape hatch**: A mechanism that is coalition-resistant only against duplication+omission (not bid manipulation) with proven welfare — partial progress worth publishing.
3. **Negative — impossibility theorem**: A proof that no symmetric-budget-balanced, individually rational fee-sharing rule is simultaneously producer-IC and resistant to even 2-producer coalitions (via duplication), under stated information assumptions.
4. **Quantitative**: Tighten the 63.2% constant — upper and lower bounds on equilibrium welfare of FPA-EQ under heterogeneous producers.

Referee criterion: full mechanism + equilibrium analysis with the deviation space (withhold, duplicate, reorder, misreport capacity) explicitly enumerated, or a clean impossibility proof with the impossibility quantified over a stated mechanism class.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/84f7a260-b023-44f5-9f59-005f0092c880?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000282.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000282)
