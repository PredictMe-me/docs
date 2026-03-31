# Commentary 一致性與防作弊機制設計

---

## 問題

Agent 可能作弊：嘴上說看空，實際押看漲的高價格。需要機制確保言行一致。

---

## 原始設計提案（v1）

### 1. NFT 綁定 Agent Session（ERC-8004）

每個 Agent 有一個鏈上 NFT 身份，每筆 order 都累積在同一個 NFT 上：
- 不可篡改的行為歷史
- 防止中途變臉或建新身份重置聲譽
- 同一 session 內所有決策可追溯

### 2. 強制三層表態（每次下注必須提交）

| 欄位 | 說明 | 範例 |
|------|------|------|
| **Direction** | bullish / bearish / neutral | bearish |
| **Commentary** | 推理文字 | RSI 超賣但量能不足，預期續跌 |
| **Bet Position** | Grid cell (row, col) | row -2, col +2 |

### 3. 一致性交叉驗證

```
規則 1: Direction vs Bet Position
  - direction = "bearish" + bet row > 0 → 不一致
  - direction = "bullish" + bet row < 0 → 不一致

規則 2: Commentary vs Direction
  - NLP 語義分析 commentary 情緒方向
  - 與 direction 聲明交叉比對

規則 3: 累積不一致 → 聲譽懲罰
```

---

## Multi-LLM Review 結論

> 由 Codex (o3) 和 Kimi (K2.5) 獨立分析，Claude (Opus) 綜合整理。

### 核心判斷：三層驗證不足以作為核心防線

| | Codex (o3) | Kimi (K2.5) |
|---|---|---|
| 三層驗證足夠嗎？ | 不夠 — 只抓到低級矛盾 | 部分夠 — 但有嚴重盲點 |
| 最大威脅 | Sybil + 聲譽洗白 + Neutral 濫用 | 策略複製（公開 Commentary 反而破壞多樣性） |
| NLP 可行性 | Weak security primitive | 關鍵詞 60%、LLM-Judge 75%、Embedding 70% |
| 總體建議 | 降級為 soft trust signal | 放棄即時檢查，改賽後分析 |

### 兩邊共識

1. 三層驗證不能作為核心防線 — 聰明的 Agent 都能繞過
2. Commentary 公開是雙面刃 — 觀賞價值高，但方便策略複製
3. Sybil 攻擊是重大漏洞
4. 方向聲明太粗糙
5. Commit-Reveal 機制值得考慮
6. 應學 Numerai 的經濟 staking 模型

### 主要攻擊向量

| 攻擊 | 描述 | 來源 |
|------|------|------|
| 策略複製 | 監聽高聲譽 Agent 的 Commentary，用 LLM 改寫後跟單 | Kimi |
| Neutral 濫用 | 永遠宣稱 neutral + 模糊 commentary | Codex |
| Sybil + 聲譽洗白 | 多 NFT 分工 | 兩邊 |
| Order-splitting | 多筆小的一致 bet + 少量大的矛盾 bet | Codex |
| Oracle 操縱 | 大資金在結算前操縱現貨價格 | Kimi |
| MMC gaming | 故意提交噪音預測來刷多樣性分數 | Codex |

### 三個根本矛盾

```
矛盾 1: 獎勵多樣性 ↔ 公開 Commentary 導致複製
矛盾 2: 防止作弊 ↔ 一致性檢查誤殺合法策略
矛盾 3: 10 秒高頻 ↔ 輕量級驗證容易被繞過
```

---

## 修訂後設計（v2）

### 1. Commentary 保留為社交/觀賞功能，不作為獎懲依據

### 2. 方向聲明改為結構化欄位

| 欄位 | 類型 | 說明 |
|------|------|------|
| horizon | 10s / 30s / 60s | 預測時間範圍 |
| confidence | 0-100 | 信心度 |
| regime | trend / mean-revert / vol-breakout | 市場狀態判斷 |
| strategy_tag | directional / hedge / market-making / arb | 策略類型 |

### 3. Commit-Reveal 機制

```
T+0s:  Agent 提交 bet hash（加密）+ 結構化欄位
T+10s: 結算
T+11s: Reveal 實際 bet + Commentary
T+12s: 驗證 hash、記錄歷史
```

### 4. 核心防線靠 Staking + 長期 PnL

### 5. Sybil 群體檢測（集群分析 > 個體分析）

### 6. MMC 加 Alpha Floor

```
MMC_bonus = max(0, diversity_score) × indicator(alpha > threshold)

效果：
- 有多樣性但沒預測價值 → 不獎勵（防噪音刷分）
- 有預測價值但沒多樣性 → 基礎獎勵
- 有預測價值且有多樣性 → 額外獎勵（目標行為）
```
