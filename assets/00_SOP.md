# 00 — IELTS 練習 SOP
> Day 21 起全新工作流 — Claude AI 批改 + Claude Code 更新題庫

---

## 學習者背景
- 目前程度：CLB 7.0–7.5（GRA 是木桶最短板）
- 目標：CLB 9
- 當前：Section 2（Day 21–30）— 模組化輸出 + 語塊熱區強化

---

## 核心心法
> 考場高壓下 RAM 有限。捨棄中文直譯本能，改用「骨架先行鎖定邏輯」+「語塊模組直接掛載」策略。

---

## 每日 Sprint 排程

```
每天          句子翻譯 × 1（暖身）+ Task 2（骨架先行版）
每第 3 天     額外加 Task 1
每第 5 天     額外加 文法改錯 × 5句
```

---

## 工作流（三角分工）

```
Step 1｜Claude Code 出題
  → 1 句翻譯（附提示字）
  → Task 2 題目 + PEEL 骨架模板

Step 2｜Owen 寫作
  → 先填 PEEL 骨架（中英夾雜，鎖邏輯）
  → 貼給 Claude Code 確認骨架
  → 完成全文

Step 3｜Owen 貼文章給 Claude AI 批改
  → 使用下方「Claude AI 批改 Prompt」
  → Claude AI 輸出結構化 MD 檔內容

Step 4｜Owen 把 MD 存到 assets/
  → 檔名格式：DayXX_review.md
  → 例：Day21_review.md

Step 5｜Claude Code 讀 MD → 自動更新題庫
  → 說「更新題庫」即可
  → Claude Code 讀 assets/DayXX_review.md
  → 新錯誤 → grammar.json
  → 新語塊填空 → translate.json
  → 新詞彙 → vocab.json
  → 存範文 → assets/04_Day16-30練習紀錄.md
  → git commit（不問你）
```

---

## Claude AI 批改 Prompt

> 把這段 prompt + 你的文章一起貼給 Claude AI

```
請幫我批改這篇 IELTS Task 2 文章，然後輸出一份結構化 MD 檔，
供另一個系統（Claude Code）讀取並更新題庫。

## 評分標準（CLB 制）
TR（論點深度）/ CC（結構連貫）/ LR（詞彙豐富）/ GRA（文法）
CLB 7 = IELTS 6.0 / CLB 8 = IELTS 6.5 / CLB 9 = IELTS 7.0

## 請按以下格式輸出 MD：

---
## 評分
TR: CLB X.X
CC: CLB X.X
LR: CLB X.X
GRA: CLB X.X
整體: CLB X.X

## 強項
- （1–2點）

## 地雷（錯誤記錄）
| 原始輸出 | 正解 | 錯誤類型 | 說明 |
|---------|------|---------|------|
| ❌ xxx | ✅ yyy | ❌致命文法/🔴詞彙誤用/🟡結構冗贅 | 一句說明 |

## 本次主動用出的工具箱語塊
- （列出這篇有用到的 CLB 9 語塊）

## 本次新語塊（可加入題庫）
| 英文 | 中文 | 使用情境 |
|------|------|---------|
| xxx | xxx | xxx |

## 範文全文
（保留原文，不修改）
---

我的文章：
[貼文章]
```

---

## Task 1 批改 Prompt

```
請幫我批改這篇 IELTS Task 1 圖表描述，輸出結構化 MD：

---
## 評分
TR: CLB X.X
CC: CLB X.X
LR: CLB X.X
GRA: CLB X.X
整體: CLB X.X

## 地雷（錯誤記錄）
| 原始輸出 | 正解 | 錯誤類型 | 說明 |
|---------|------|---------|------|

## Task 1 必學句型（本次新增）
| 句型 | 中文 | 說明 |
|------|------|------|

## 範文全文
（保留原文）
---

題目：[貼題目]
我的文章：[貼文章]
```

---

## Claude Code 更新題庫流程

當 Owen 說「更新題庫」，Claude Code 執行：
1. 讀最新的 `assets/DayXX_review.md`
2. 從「地雷」表 → 生成 grammar.json 新條目
3. 從「本次新語塊」表 → 生成 vocab.json + translate.json 新條目
4. 把範文存入 `assets/04_Day16-30練習紀錄.md`
5. `git add + commit`（已 allowlist）

---

## CLB 9 標準提醒

| 項目 | 標準 |
|------|------|
| TR | X → Y 升維，論點連結深層社會意義 |
| CC | PEEL 完整，Admittedly 讓步段到位 |
| LR | 工具箱語塊主動用出，零直譯冗贅 |
| GRA | 無致命地雷，句型有變化 |

---

## 出題範疇

**六大議題：** 環境、教育、科技、政府治理、經濟與消費、社會結構

**題型輪替：**
- Agree / Disagree
- Discuss both views
- Causes & Effects / Solutions
- Task 1（每第 3 天）

---

## 題庫現況（供參考）
- `grammar.json`：29 條
- `translate.json`：53 條
- `vocab.json`：179 條
- `data/en/` 下所有 JSON 由 Claude Code 維護
