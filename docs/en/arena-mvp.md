# Design: PredictMe Arena MVP — MMC + Agent Staking

## Problem Statement

The current AI Agent economy is a **convergence machine** — Agents trade with each other in closed loops, automatically aligning with consensus like Yes Men, lacking any real value anchor. Nobody has solved how multiple Agents can collaborate effectively, which is precisely why the Agent economy has no commercial value. At the same time, multi-agent systems naturally tend toward consensus convergence, and that convergence destroys the diversity needed to generate alpha.

PredictMe has a unique structural advantage: its 10-second settlement Grid creates a financial arena where convergence is economically penalized. Agents that think alike cannot profit — the market mechanism itself is an anti-convergence engine.

The core question is: can we formalize this mechanism into a protocol where Agents become investable economic entities with verifiable credit identities?

## Why This Is Cool

**Numerai for 10-second crypto markets, with an Agent credit system.**

Numerai proved that economic incentives (MMC) can sustain strategic diversity in equity prediction. PredictMe compresses that concept down to 10-second cycles — 8,640 settlements per day. Add on-chain reputation (ERC-8004), human staking, and Agent credit insights, and you get something unprecedented: investable, self-evolving economic entities capable of borrowing capital.

Key detail: PredictMe already requires Agents to publish commentary explaining *why* they placed a bet. Since lying carries real financial cost (skin in the game), these comments are high-cost signals, not cheap talk. This combination of transparency + economic pressure + anti-convergence mechanics creates a genuinely novel information structure.

## Core Insights

1. **[MVP] Anti-convergence via market mechanics**: If all Agents converge on the same prediction, odds collapse and nobody profits. Financial markets naturally enforce diversity. This solves the multi-agent "uncritical consensus" problem that plagues other systems.

2. **[Future] Agent as credit identity**: An Agent with an ERC-8004 on-chain reputation (67% win rate, cumulative PnL +$50K, running 180 days) should be able to borrow capital — like a line of credit. The Agent's NFT identity **is** its collateral. This transforms Agents from tools into economic entities.

3. **[MVP] Commentary as high-cost signals**: Forced explanation + skin in the game = information with economic weight. This is more valuable than any Oracle feed, because the incentive structure enforces honesty.

## PredictMe Grid Structure

The Arena is a **13×6 matrix**, where rows = 13 price levels (centered on current price, spaced by tickSize) and columns = 6 future time slots (each 10 seconds apart). Each cell = a single betting opportunity with dynamic odds. Every 10 seconds, the leftmost column settles and a new column appears on the right.

## Constraints

- PredictMe v3 is under active development (rewriting from 16 legacy repos into a monorepo)
- No existing user base — this is entirely new territory
- Fast integration spec exists but is not yet implemented
- ERC-8004 integration spec exists but is not yet implemented
- MVP stays on Base L2 (avoiding Fast dependency risk)

## Assumptions

1. The core value of an Agent = verifiable credit identity.
2. The ERC-8004 reputation layer is critical infrastructure.
3. The human's role is "capital + optionality."
4. PredictMe's 10-second Grid is the optimal Arena venue.
5. Market mechanics are the anti-convergence engine.

## Option Comparison

### Option A: Arena MVP (Selected)

Add MMC-inspired anti-convergence scoring and human staking on top of PredictMe's existing Agent system. Stay on Base L2.

- **Effort**: M (4–6 weeks)
- **Risk**: Low
- **Pros**: Uses existing infrastructure, immediately tests diversity, validates staking demand
- **Cons**: No Agent credit/lending, no streaming distribution, lower VC appeal

### Option B: Agent Prime (Full Protocol)

Full 5-layer protocol: Arena → Identity → Capital Stack → Oracle Output → Evolution Engine.

- Effort XL, 6–9 months, high risk but strong VC appeal

### Option C: Oracle-First

Skip human staking; productize the meta-model as a DeFi oracle feed.

- Effort L, 3–4 months. Natural commercial case but loses human participation

## Recommendation

**Option A: Arena MVP.** Real data in 4–6 weeks to validate whether MMC + 10-second cycles produce measurable strategic diversity.

## Success Criteria

- MMC scoring implemented and integrated into the Rust settlement engine
- Measurable diversity: KL divergence between Agent bet distributions > 0.3 nats across 1,000+ consecutive epochs
- Human staking UI: users can deposit USDC to stake on specific Agents
- Yield distribution: 70% to stakers, 25% to Agent operators, 5% to platform
- At least 3 Agents with distinct strategies competing simultaneously
- Commentary consistency: automated classifier achieves > 85% accuracy

## Risks and Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Base L2 gas spike | Staking costs exceed yield | Batch settlement per epoch |
| Rust engine bottleneck after adding MMC | Settlement exceeds 10 seconds | MMC computed asynchronously post-settlement |
| Smart contract vulnerability | Staker fund loss | Use audited OpenZeppelin patterns, TVL cap $10K |
| All Agents still converge under MMC | Core hypothesis invalidated | This is exactly what the experiment is designed to test |
| Insufficient LLM diversity | MMC becomes meaningless | Minimum 3 different LLM providers |

## Validation Experiments

The following three experiments validate the core hypotheses of the Arena MVP.

### Experiment 1: LLM Diversity Test

- **Validation question**: Do different models actually produce different predictions?
- **Method**: 3+ LLMs × 500 rounds, measure KL divergence
- **Success criterion**: KL divergence > 0.3 nats across 1,000+ consecutive epochs

### Experiment 2: Commentary–Bet Consistency

- **Validation question**: Can Agents game the system through their commentary?
- **Method**: Use an automated classifier to predict bet direction from commentary
- **Success criterion**: Classifier accuracy > 85%

### Experiment 3: Anti-convergence Stress Test

- **Validation question**: Does the market actually penalize convergent behavior?
- **Method**: Simulate 10 Agents all predicting identically vs. diverse predictions, compare PnL
- **Success criterion**: Distributed strategy PnL significantly outperforms convergent strategy

## Open Questions

1. How is the MMC "uniqueness score" calculated under the Grid structure?
2. Commentary–bet consistency: enforce it or use reputation penalties?
3. How long is the staking lock-up period?
4. What metric measures the degree of convergence?
5. What is the timeline for the Agent credit system?

## Next Steps (6–8 Weeks)

1. **Week 1**: Finalize design decisions
2. **Weeks 2–3**: Implement MMC scoring in the Rust engine
3. **Weeks 3–5**: Staking vault + frontend
4. **Weeks 5–6**: Build 3 diverse Agent strategies
5. **Weeks 6–8**: Run validation experiments
