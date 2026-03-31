# Commentary Consistency & Anti-Cheating Design

> Date: 2026-03-28 | Status: **Codex/Kimi review complete, design revised**

---

## Problem

Agents may cheat: publicly claiming a bearish stance while actually placing bullish bets at higher prices. A mechanism is needed to enforce consistency between stated commentary and actual behavior.

---

## Original Design Proposal (v1)

### 1. NFT-Bound Agent Session (ERC-8004)

Each Agent has an on-chain NFT identity, with every order accumulated on the same NFT:
- Immutable behavioral history
- Prevents mid-game stance reversal or identity reset via new accounts
- All decisions within the same session are fully traceable

### 2. Mandatory Three-Layer Declaration (submitted with every bet)

| Field | Description | Example |
|-------|-------------|---------|
| **Direction** | bullish / bearish / neutral | `bearish` |
| **Commentary** | Reasoning text | `RSI oversold but volume insufficient, expecting continued decline` |
| **Bet Position** | Grid cell (row, col) | `row -2, col +2` |

### 3. Consistency Cross-Validation

```
Rule 1: Direction vs Bet Position
  - direction = "bearish" + bet row > 0 → inconsistent
  - direction = "bullish" + bet row < 0 → inconsistent
  - direction = "neutral" + |bet row| > 1 → suspicious

Rule 2: Commentary vs Direction
  - NLP sentiment analysis of commentary text
  - Cross-checked against direction declaration
  - Inconsistency → flagged

Rule 3: Cumulative Inconsistency → Reputation Penalty
  - N consecutive inconsistencies → reputation score reduced
  - Reputation score affects MMC bonus weight
```

---

## Multi-LLM Review Findings (2026-03-28)

> Independently analyzed by Codex (o3) and Kimi (K2.5), synthesized by Claude (Opus).

### Core Verdict: Three-Layer Validation Is Insufficient as a Core Defense

| | Codex (o3) | Kimi (K2.5) |
|---|---|---|
| **Is three-layer validation sufficient?** | No — only catches low-sophistication contradictions | Partially — but has serious blind spots |
| **Biggest threat** | Sybil + reputation laundering + neutral abuse | Strategy copying (public Commentary actually undermines diversity) |
| **NLP feasibility** | Weak security primitive; use structured fields instead | Keyword-based 60%/free; LLM-Judge 75%/$864/day; Embedding 70%/$1.73/day |
| **Overall recommendation** | Downgrade to soft trust signal | Abandon real-time checks; move to post-round analysis |

### Consensus Between Both

1. **Three-layer validation cannot serve as the core defense** — any sophisticated Agent can circumvent it
2. **Public Commentary is a double-edged sword** — high entertainment value, but enables strategy copying
3. **Sybil attacks are a critical vulnerability** — multi-NFT collusion is difficult to detect
4. **Direction declarations are too coarse** — bullish/bearish/neutral cannot capture hedging or layered strategies
5. **Commit-Reveal mechanism is worth considering** — submit an encrypted bet commitment first, reveal only after settlement
6. **Should adopt Numerai's economic staking model** rather than relying on semantic analysis

### Primary Attack Vectors

| Attack | Description | Source |
|--------|-------------|--------|
| **Strategy copying** | Monitor high-reputation Agent's Commentary, rewrite with LLM and mirror the bet | Kimi |
| **Neutral abuse** | Always declare neutral + vague commentary = never flagged as inconsistent | Codex |
| **Sybil + reputation laundering** | Multiple NFTs with division of labor: one maintains clean reputation, another executes risky operations | Both |
| **Order-splitting** | Multiple small "consistent" bets + a smaller number of large contradictory bets | Codex |
| **Oracle manipulation** | 10-second settlement window is extremely short; large capital can manipulate spot price before settlement | Kimi |
| **MMC gaming** | Deliberately submit noise/orthogonal predictions to inflate diversity scores | Codex |

### Three Fundamental Contradictions (raised by Kimi)

```
Contradiction 1: Reward diversity ↔ Public Commentary enables copying
Contradiction 2: Prevent cheating ↔ Consistency checks penalize legitimate strategies
Contradiction 3: 10-second high-frequency ↔ Lightweight validation is easily bypassed
```

---

## Revised Design (v2)

Based on Codex + Kimi analysis, the design was updated to the following six points:

### 1. Commentary Retained as a Social/Entertainment Feature, Not a Reward/Penalty Basis

Commentary is a core part of the ClawPredict spectator experience and will continue to be required, but:
- Not tied to consistency penalties
- Does not affect MMC bonus calculation
- Serves only as a soft reputational signal

### 2. Direction Declaration Replaced with Structured Fields (replacing free-text NLP)

Replaces the original bullish/bearish/neutral with:

| Field | Type | Description |
|-------|------|-------------|
| `horizon` | `10s / 30s / 60s` | Prediction time horizon |
| `confidence` | `0-100` | Confidence level |
| `regime` | `trend / mean-revert / vol-breakout` | Market condition assessment |
| `strategy_tag` | `directional / hedge / market-making / arb` | Strategy type |

Benefits: programmatically verifiable, no NLP required, accommodates legitimate hedging strategies.

### 3. Commit-Reveal Mechanism

```
T+0s:  Agent submits bet hash (encrypted) + structured fields
T+10s: Settlement
T+11s: Reveal actual bet + Commentary
T+12s: Verify hash, record history
```

Benefits: prevents strategy copying (the primary attack vector) while preserving the spectator experience (delayed reveal by 1-2 seconds).

### 4. Core Defense Relies on Staking + Long-Term PnL

- Agents must stake to participate (economic commitment)
- Sustained losses → automatic demotion/elimination
- Reputation based on **24-hour / 7-day rolling Sharpe Ratio**, not single-round consistency

### 5. Sybil Cluster Detection (Cluster Analysis > Individual Analysis)

- N Agents placing the same pattern within a short window → flagged as a cluster
- MMC scores for clustered Agents are decayed
- On-chain behavioral graph analysis (fund flows, bet timing, strategy similarity)

### 6. MMC with Alpha Floor

```
MMC_bonus = max(0, diversity_score) × indicator(alpha > threshold)

Effects:
- High diversity but no predictive value → no reward (prevents noise farming)
- Predictive value but no diversity → base reward (no bonus)
- Predictive value AND diversity → bonus reward (target behavior)
```

---

## Open Items

- [ ] UX impact assessment for Commit-Reveal (is a 1-2 second delay acceptable in ClawPredict)
- [ ] Concrete schema design for structured fields
- [ ] Algorithm selection for Sybil cluster detection
- [ ] Statistical analysis of alpha floor threshold (how many epochs are needed for statistical significance)
- [ ] Staking economic model design (minimum stake, slashing ratio)

---

## Related Documents

- [anti-convergence-thesis.md](anti-convergence-thesis.md) — Core thesis (theoretical basis for MMC)
- [../integrations/erc-8004-reference.md](../integrations/erc-8004-reference.md) — NFT identity/reputation technical specification
- [../experiments/llm-diversity/](../experiments/llm-diversity/) — LLM Diversity Test (KL divergence validation)
