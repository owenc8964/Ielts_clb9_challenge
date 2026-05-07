# HANDOFF — 2026-05-07

## 這次做了什麼

### Phase 1 Step 1 — 題庫拆分
- 從 index.html 提取五個題庫至 `data/en/*.json`
- index.html 改用 `fetch()` + `Promise.all` 非同步載入
- 建立 `data/fr/.gitkeep`（Phase 3 法文版預留）

### UI/UX 改進
- **文法挑錯**：
  - 題目預設不標記，選項對比正確答案標出差異（粗斜體 `.opt-diff`）
  - 答題後：題目錯誤部分才顯示紅色（`.error-highlight`）
- **中翻英**：
  - 移除自評按鈕，改為自動比對 `key_vocab` 關鍵詞
  - 有用到關鍵詞 → ✓ 正確；沒用到 → △ 不太一樣 + 提示
  - 新增「一鍵 AI 深度分析」按鈕：複製 prompt → 開 Claude.ai
- **放棄測驗**：橙色按鈕，隨時可退出
- **字體放大**：所有題目、選項字體提升 2px
- **閱讀文章**：4 篇 → 7 篇（新增都市化、氣候變遷、人口老化）

### 基礎設施
- GitHub repo：`owenc8964/Ielts_clb9_challenge`
- GitHub Pages：`https://owenc8964.github.io/Ielts_clb9_challenge`
- SSH key 設定完成，`git push` 不需密碼

---

## 改了哪些檔案

```
index.html              — 所有 UI/UX 邏輯更新
data/en/vocab.json      — 44 題單字
data/en/grammar.json    — 8 題文法（新增 error_span 欄位）
data/en/translate.json  — 5 題中翻英（新增 key_vocab、hint 欄位）
data/en/clb9.json       — 8 題 CLB9 辨別
data/en/reading.json    — 7 篇閱讀理解
data/fr/.gitkeep        — Phase 3 預留
HANDOFF.md              — 本文件
```

---

## 系統背景（重要）

Owen 是牙醫，準備 Express Entry 移民加拿大，目標 CLB 9。
**學習閉環**：每天寫 Task 2、每三天寫 Task 1 → 批改檢討 → 每五天整理成 MD → 提供給 Claude 更新 JSON 題庫。
這個遊戲是「錯誤強化訓練」層，不是主要學習途徑。

---

## 下一步建議

### 近期（下次 session）
- **每五天**：Owen 提供新 MD，Claude 解析後更新對應 JSON 檔案

### Phase 1.2（待決定）
- Claude API 自動評分（中翻英）
- API key 安全方案：建議用「使用者自填 key，存 localStorage」
- 不要直接寫進程式碼（public repo 會洩漏）

### Phase 2
- GitHub Actions 自動更新題庫（見 CLAUDE_CODE_PROMPT.md）

### Phase 3
- 法文 CLB7 版本（`data/fr/` 已預留）

---

## 本機開發指令

```bash
cd "/Users/owen/Documents/Claude/Project/CLB test/Ielts CLB9"
npx serve . -p 3000   # 啟動本機伺服器
git push              # SSH 已設定，直接 push
```
