# HANDOFF 2026-05-09 — 詞彙生產力升級

## 這次做了什麼

### 核心理念轉向
從「看選項能選對（Recognition）」→ 往「寫作時主動跳出（Production）」移動。
三個設計原則：
1. **概念綁定**：記 exacerbate social stratification 整組，不單背 exacerbate
2. **情境反問**：給微情境 + 強制裝詞彙，而非死背定義
3. **刻意替換**：練到寫作時不需停下來想，自動升級

---

### Bug 修復
- `showQuestion()` 的 reset block 有一行 `getElementById('selfGrade').className`，但 `#selfGrade` 元素已被刪除 → TypeError 導致所有題目空白。移除該行修復。

---

### vocab.json 擴充（79 → 140+ 詞）
新增 `category` 欄位，分為：

| category | 說明 |
|----------|------|
| `environment` | 碳排、保育、永續 |
| `economy` | 財政、薪資、職場 |
| `technology` | AI、數位、隱私 |
| `education` | 課程、思考、壓力 |
| `health` | 慢性病、預防、心理 |
| `society` | 司法、歧視、凝聚 |
| `task1` | 趨勢、數據描述詞 |
| `spoken_upgrade` | 口語→學術配對（18組，如 more and more → increasingly） |
| `peel` | Point/Explain/Example/Link 固定搭配（14組） |
| `general` | 通用學術詞 |

---

### UI：分類選擇器
- 點「單字配對」先跳出 bottom sheet 選分類
- 顯示每類詞數
- 選分類後，干擾選項只從同類詞抽取
- 口語升級／PEEL 使用客製化問法

---

### translate.json 重設計（8題 → 25題）
**舊格式**：給整句中文 → 自己寫英文（太難，容易放棄）

**新格式**：英文情境句 + `___[中文概念標籤]` 空格 → 填入 CLB9 詞彙

設計原則：
- 1–3 個空格，每格對應一個答案
- 中文標籤是概念方向，不是翻譯
- 每個詞的提示：純中文描述，不洩露英文詞形
- 字數資訊放進「💡 提示」按鈕，點才展開

題目涵蓋：六大主題、口語升級（→ exacerbate / disparities）、Task 1 詞彙

---

### UI：情境填空改版
- 每個空格獨立一格輸入，按序號標 1. 2.
- `💡 提示` 預設收起，點展開顯示：字數 ＋ 概念描述
- 答對後動態生成參考句（綠字標出正確詞）
- 每格獨立標色（綠＝對、紅＝錯）

---

## 改了哪些檔案

| 檔案 | 改動 |
|------|------|
| `data/en/vocab.json` | 全部加 category、補充至 140+ 詞 |
| `data/en/translate.json` | 完全重寫，25 題新格式 |
| `index.html` | 分類選擇器、情境填空 UI、submitTranslate 邏輯、CSS |

---

## 下一步（討論過但還沒做）

### 優先級高

**1. 主題開箱模式（Topic Unlock）**
- 選一個分類 → 先嘗試回憶 10 秒 → 逐張翻牌揭露詞彙+搭配
- 每張自評：✅ 有想到 ／ ❌ 沒想到
- 「沒想到」的詞標記 `productionDue = true`
- 不需新 JSON，用現有 vocab.json 的 category + 搭配句（note 欄）
- 核心價值：從「認識」跨到「主動提取」，模擬寫作前暖身

**2. Production SRS 軌道**
- 現有 SRS 只練 Recognition（選擇題）
- 新軌道：`productionDue = true` 的詞出現為**填空題**而非選擇題
- 需要：vocab.json 每個詞加 `sentence` 欄位（帶空格的例句）
- 例：`"sentence": "This policy may ___ the very problems it aims to solve."` → 填 exacerbate

**3. vocab.json 加 sentence 欄位**
- 為 140 個詞各寫一個情境例句
- 格式同 translate.json 的 template_en（`___` 標空格位置）
- 這是 Production SRS 的前置內容工作

### 優先級中

**4. 情境反問題型**
- 給定微情境（3–5 句英文描述）+ 強制使用 2 個指定詞彙寫一句話
- 比填空更接近考場大腦運作
- 需要新題庫 + UI（開放式輸入，自評）

**5. Grammar 模式降比重**
- 目前是選擇題，對寫作效益最低
- 可縮短至 5 題或改為選答（不計 SRS）

---

## 架構現況

```
data/en/
  vocab.json       ← 主詞庫，140+ 詞，有 category + distractors
  translate.json   ← 25 題情境填空（新格式）
  clb9.json        ← 12 題 CLB9 辨別（不動）
  task1.json       ← Task 1 數據描述（不動）
  task2.json       ← 5 題 Task 2 框架（不動）
  grammar.json     ← 文法挑錯（低優先）

SRS localStorage：
  ielts_v2_srs    ← { zh: { reps, interval, nextReview, ease } }
  計畫加：productionDue flag 在同一個 record 裡
```

---

## 學習路線設計（目標）

```
單字配對（Recognition）    ← 現在已有，按分類練
        ↓
情境填空（Cued Production） ← 本次完成
        ↓
主題開箱（Topic Recall）    ← 下一步
        ↓
Production SRS（弱點強化）  ← 需要 sentence 欄位
        ↓
考場寫作（Spontaneous）     ← 最終目標
```
