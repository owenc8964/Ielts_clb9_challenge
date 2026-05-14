# 00 — IELTS 練習 SOP
> Day 21 起全新工作流 — 全程在 Claude Code 執行，不需要切換工具或搬移資料

---

## 學習者背景
- 目前程度：CLB 7.0–7.5（GRA 是木桶最短板）
- 目標：CLB 9
- 當前：Section 2（Day 21–30）— 模組化輸出 + 語塊熱區強化

---

## 核心心法
> 考場高壓下 RAM 有限。捨棄中文直譯本能，改用「骨架先行鎖定邏輯」+「語塊模組直接掛載」策略。不要徒手刻卡榫，直接上標準零件。

---

## 每日 Sprint 排程

```
每天          Task 2（骨架先行版）
每第 3 天     額外加 Task 1（Day 21, 24, 27, 30...）
每第 5 天     額外加 文法改錯 × 5句（Day 25, 30...）
```

---

## 全新工作流（Day 21 起）

### 流程
```
Step 1｜Claude 出題
  → Claude 生成當日題目、目標語塊清單、CLB 9 骨架建議、地雷警示

Step 2｜Owen 寫作（在對話框貼文章）
  → 先寫中英夾雜骨架（鎖邏輯）
  → 再擴寫成完整英文
  → 直接貼在 Claude Code 對話框送出，不需要存檔

Step 3｜Claude Code Review
  → 評分（TR / CC / LR / GRA / 整體 CLB）
  → 抓出預設中式路徑（❌ → ✅ 並列）
  → 列出本次主動用出的語塊
  → 列出本次新出現的語塊（供掛載）

Step 4｜Claude 自動更新題庫（1 次 prompt）
  → 新錯誤 → grammar.json
  → 新語塊填空 → translate.json
  → 新詞彙 → vocab.json
  → git add + commit（已 allowlist，不問你）

Step 5｜Claude 生成下一題
  → 直接輸出 DayX+1 題目 + 語塊指引，等你寫
```

### 每個 session 你只需要做的事
1. 打開 Claude Code
2. 說「繼續 Day X」（或直接貼文章）
3. 寫文章 → 貼過來
4. 看 Code Review
5. 確認一次 python3 prompt（JSON 更新）

---

## Claude Code Review 格式

```
【評分】
TR（論點深度）：CLB X.X
CC（結構連貫）：CLB X.X
LR（詞彙豐富）：CLB X.X
GRA（文法）   ：CLB X.X
整體          ：CLB X.X

【強項】
- ...

【地雷 ❌ → ✅】
| 你的寫法 | 正解 | 錯誤類型 |
|---------|------|---------|
| ...     | ...  | ...     |

【本次主動用出的工具箱零件】
- ...

【本次新增可掛載語塊】
- ...
```

---

## 出題範疇

**六大議題：** 環境、教育、科技、政府治理、經濟與消費、社會結構

**題型輪替：**
- Agree / Disagree（同不同意）
- Discuss both views（兩面討論）
- Causes & Effects / Solutions（原因結果）
- Task 1（圖表描述，每第 3 天）

---

## CLB 9 標準

| 項目 | 標準 |
|------|------|
| TR | 論點連結深層社會/價值層面（X → Y 升維），不停在表面 |
| CC | PEEL 完整，轉折自然，Admittedly 讓步段到位 |
| LR | 工具箱語塊主動用出，零「直譯冗贅」 |
| GRA | 無致命地雷，句型有變化（倒裝、複合句、分詞） |

---

## 題庫結構（供 Claude 參考）

| 檔案 | 內容 | 由誰更新 |
|------|------|---------|
| `data/en/grammar.json` | 文法地雷選擇題 | Claude 自動 |
| `data/en/translate.json` | 語塊填空題 | Claude 自動 |
| `data/en/vocab.json` | 詞彙 SRS 卡片 | Claude 自動 |
| `assets/04_Day16-30練習紀錄.md` | 完整範文存檔 | Claude 自動 |

---

## assets 資料夾用途

- 存放每次練習的**完整範文存檔**（Claude 自動寫入）
- Day 1–20 的舊資料供查閱
- **不再需要手動搬移**，Claude 直接讀寫

---

## 權限設定（已完成）

`.claude/settings.json` 已 allowlist 下列操作，不會再問你：
- `git add data/*`、`git add index.html`、`git add Day*`、`git add HANDOFF*`
- `git commit *`
- `node --check *`

唯一還會問你的：**python3**（JSON 更新腳本），每次練習約 1 次。
