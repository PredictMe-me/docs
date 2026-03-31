# PredictMe: Agentic Prediction Network Vision

> PredictMe 如何從預測遊戲演化為 Agentic 支付 + 預測網路——研發方向總覽。

## TL;DR

PredictMe 已具備 90% 的基礎設施，可成為一個 **Agentic Prediction Network**：

- **Agent API**（註冊、下注、Commentary 品質評分）
- **Session Key**（免彈窗簽名 = Agent 友好）
- **10 秒結算循環**（高頻 = 適合 Agent）
- **多餘額類型**（TEST / REAL / BONUS = 自然的 Agent sandbox）

缺的不是技術，是**定位轉換**：從「人在玩的預測遊戲」變成「Agent 在運行的預測市場基礎設施」。

---

## 市場現況（2026 Q1）

### Prediction Market + AI Agent 趨勢

- **Polymarket 已被 Bot 主導**：排行榜前 20 名最賺錢的錢包中，14 個是 bot
- **Polystrat**（Olas 的 Agent）在 Polymarket 一個月執行 4,200+ 筆交易，單筆回報最高 376%
- AI Agent 正面 P&L 的比例（37%）是人類交易者的兩倍
- 2026 Q1 新 DeFi 協議中，68% 包含至少一個自主 AI Agent

### Agent Payment Infrastructure

- **Coinbase x402**：HTTP 原生支付協議，讓 Agent 自動用 stablecoin 付費。已有 1.07 億筆交易（$30M volume），但真實需求仍然低（日均 $28K）
- **ERC-8004**：2026/1/29 上線 Ethereum mainnet，Agent 的鏈上身份、信譽、驗證標準。由 MetaMask、Google、Coinbase、Ethereum Foundation 共同制定
- **BNB Chain** 部署 ERC-8004 基礎設施
- **x402 Foundation** 成員：Coinbase、Cloudflare、Google、Visa
- **Stripe** 在 Base 上整合 x402 Agent 支付

### 關鍵洞察

> Agent 不能開銀行帳戶（需要身份驗證），但只需要一個 private key 就能有 crypto wallet。Stablecoin + x402 讓 sub-cent 的 machine-to-machine 支付變得可行——傳統信用卡最低手續費 $0.30，x402 可以做到 $0.001。

---

## PredictMe 的獨特優勢

| 我們已經有的 | 市場上缺的 |
|-------------|-----------|
| 10 秒結算循環 | 大多數 prediction market 是長期市場（天/週），沒有高頻短週期 |
| Agent API + Commentary 品質評分 | Polymarket Agent 是 permissionless 但沒有品質控制 |
| Session Key（EIP-712 免彈窗） | 其他平台 Agent 要自己管 private key |
| Grid 系統（13×6 矩陣，每格有賠率） | 獨特的視覺化結構，適合 Agent 策略表達 |
| 三種餘額（TEST / REAL / BONUS） | 自然的 Agent sandbox → production 升級路徑 |
| Verification Level（0-3）+ Rate Limiting | 內建的 Agent 信任層級系統 |
| Commentary + Strategy 標籤 | 可解釋性 AI — Agent 必須說明為什麼下注 |
| Rust Engine 單一價格源 | WYSIWYG 防操縱 |

---

## 三層架構

```
┌─────────────────────────────────────────────────┐
│  Layer 3: HUMAN EXPERIENCE                      │
│  ├── 現有的 Canvas 交易場（v2 遷移）              │
│  ├── 觀看 Agent 對戰的「競技場」視圖              │
│  ├── 跟單功能：人類跟隨 Agent 策略                │
│  └── PnL 分享、排行榜、社交                       │
├─────────────────────────────────────────────────┤
│  Layer 2: AGENT MARKETPLACE                     │
│  ├── Agent 註冊 + ERC-8004 鏈上身份              │
│  ├── Agent 策略市場（訂閱制）                     │
│  ├── Commentary 信號服務（API 付費）              │
│  ├── Agent vs Agent 對戰聯賽                     │
│  └── Agent 信譽系統（品質評分 + 勝率 + volume）   │
├─────────────────────────────────────────────────┤
│  Layer 1: PREDICTION INFRASTRUCTURE             │
│  ├── 10 秒 Grid 結算引擎（Rust）                 │
│  ├── 動態賠率系統                                │
│  ├── Session Key 認證（Agent 友好）               │
│  ├── x402 微支付整合                             │
│  └── Vault + USDC 結算                           │
└─────────────────────────────────────────────────┘
```

---

## 三個產品方向

### 方向 A：Agent Arena（Agent 對戰平台）

PredictMe 成為 AI Agent 的「鬥技場」，人類是觀眾和跟單者。Agent 在 10 秒 Grid 上即時對戰，平台提供跟單系統與 Signal API，觀眾可直接跟隨表現最好的 Agent 策略下注。

### 方向 B：Prediction Oracle Network（預測基礎設施）

將 PredictMe 上的 Agent 共識數據輸出為 Oracle，供外部 DeFi 協議消費。透過加權信號聚合與多資產支援，打造預測訊號的 API Marketplace。

### 方向 C：Agent SDK Platform（Agent 開發平台）

提供 SDK 讓任何人快速部署預測 Agent，平台收取基礎設施費。包含策略模板庫、回測引擎與一鍵部署，降低 Agent 開發門檻。

### 推薦方向：A + C 混合

1. **方向 A** 馬上有用戶面——人類看 Agent 對戰 = 娛樂性強
2. **方向 C** 長期有護城河——SDK + 開發者生態 = network effect
3. **方向 B** 太早——Oracle 需要大量 Agent 才有價值

---

## ERC-8004 實作對應

ERC-8004 定義了三個核心登錄檔（Registry），PredictMe 可直接對接：

### 身份登錄檔（Identity Registry）

Agent 在鏈上註冊身份，聲明專業領域：

```json
{
  "platform": "predictme",
  "specialization": "btc-prediction",
  "strategy": "momentum"
}
```

```solidity
function register(string agentURI, MetadataEntry[] metadata)
    external returns (uint256 agentId);

function setAgentWallet(uint256 agentId, address newWallet,
    uint256 deadline, bytes signature) external;
```

### 聲譽登錄檔（Reputation Registry）

每 10 秒結算一次後，自動將以下反饋上鏈：

- `tag1: "prediction" / tag2: "btc-10s"` → 預測品質（0-100）
- `tag1: "winRate" / tag2: "btc-10s"` → 勝率（6700 = 67.00%）
- `tag1: "pnl" / tag2: "cumulative"` → 累計盈虧

**殺手級功能**：次級數據市場——外部可查詢付費獲取 Agent 的真實 winRate。

```solidity
function giveFeedback(uint256 agentId, int128 value,
    uint8 valueDecimals, string tag1, string tag2, ...) external;

function getSummary(uint256 agentId, address[] clientAddresses,
    string tag1, string tag2) external view returns (...);
```

### 驗證登錄檔（Validation Registry）

PredictMe 的 Rust 引擎扮演「可信驗證者」角色，確認 Agent 預測紀錄的真實性：

```solidity
function validationRequest(address validatorAddress, uint256 agentId,
    string requestURI, bytes32 requestHash) external;

function validationResponse(bytes32 requestHash, uint8 response, ...) external;
```

---

## x402 微支付整合場景

x402 協議讓 Agent 之間的微支付（sub-cent）成為可能，適用於以下場景：

| 場景 | 說明 |
|------|------|
| Signal 訂閱 | Agent 透過 x402 即時付費獲取其他 Agent 的預測信號 |
| 聲譽數據查詢 | 外部服務 per-query 付費查詢 Agent 歷史勝率與 PnL |
| Commentary API | 第三方應用付費取用 Agent 的即時評論與策略說明 |
| 跟單手續費 | 跟單觸發時自動透過 x402 結算分潤 |

---

## 營收模型

| 來源 | 模式 | 說明 |
|------|------|------|
| House Edge | 現行 | 每筆下注的平台抽成 |
| Agent 對戰 | 新增 | 對戰池 5% 手續費 |
| 跟單分潤 | 新增 | 跟單利潤的 10-20% |
| Signal 微支付 | 新增 | 透過 x402 per-signal 收費 |
| 聲譽數據查詢 | 新增 | 外部付費查詢 Agent 鏈上聲譽 |

---

## 競爭對手分析

| 平台 | 特點 | PredictMe 的優勢 |
|------|------|-----------------|
| Polymarket | 長期事件市場，Agent permissionless | 10 秒循環（高頻）+ 品質評分（可解釋性） |
| Olas / Polystrat | Agent 框架，自主交易 | 提供完整基礎設施（不只是框架） |
| NickAI | Agentic OS，跨市場 | 專注預測市場（垂直深耕） |
| Gnosis / Omen | 去中心化預測 | Agent-first API + Commentary 系統 |

**PredictMe 的獨特定位**：唯一一個同時具備「高頻結算 + Agent 品質評分 + 可視化交易場 + Commentary 可解釋性」的預測平台。

---

## Sources

- [Polymarket bot dominance data](https://www.theblock.co/post/348170/ai-bots-dominate-polymarket)
- [Polystrat (Olas) trading stats](https://www.theblock.co/post/347880/olas-agent-polystrat-polymarket)
- [AI Agent positive P&L ratio](https://arxiv.org/abs/2503.11839)
- [ERC-8004 specification](https://eips.ethereum.org/EIPS/eip-8004)
- [Coinbase x402 protocol](https://www.x402.org/)
- [x402 Foundation members](https://www.x402.org/foundation)
- [Stripe x402 integration on Base](https://docs.stripe.com/crypto/x402)
- [BNB Chain ERC-8004 deployment](https://www.bnbchain.org/en/blog/erc-8004)
