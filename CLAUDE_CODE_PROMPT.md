# IELTS CLB9 Quiz — Claude Code 開發指令

## 專案背景

這是一個 IELTS 寫作練習遊戲，目標是幫助使用者從 CLB 7 提升到 CLB 9。
目前已有可運行的 `index.html` 單頁應用，包含五種練習模式、XP 系統、每日簽到。

使用者：Owen，牙醫，目前 CLB 7，目標 CLB 8–9，同時準備加拿大 Express Entry 移民。

---

## 當前架構

```
index.html          ← 單頁應用，所有邏輯在一個檔案
                       包含：題庫資料、遊戲邏輯、UI、localStorage 儲存
```

---

## Phase 1 目標：拆分架構 + Claude API 評分

### 1. 拆分檔案結構

```
/
├── index.html
├── css/
│   └── style.css
├── js/
│   ├── app.js          ← 主邏輯
│   ├── quiz.js         ← 題目和模式邏輯
│   ├── storage.js      ← XP / 進度 / localStorage
│   └── api.js          ← Claude API 呼叫
├── data/
│   ├── en/
│   │   ├── vocab.json      ← 英文詞彙題庫
│   │   ├── grammar.json    ← 文法挑錯題庫
│   │   ├── translate.json  ← 中翻英題庫
│   │   ├── clb9.json       ← CLB9 辨別題庫
│   │   └── reading.json    ← 閱讀理解題庫
│   └── fr/             ← 法文版（Phase 3 預留）
│       └── .gitkeep
└── scripts/
    └── update-content.js   ← GitHub Actions 自動更新腳本（Phase 2）
```

### 2. 中翻英評分系統（核心升級）

用 Claude API 取代「自己對答案」的流程：

**流程：**
```
使用者輸入英文翻譯
→ 呼叫 Claude API 評分
→ 回傳：分數（1-9）+ 具體問題 + 修正版本
→ 如果分數 < 7：強制追加一題
  指定一個關鍵詞彙，要求用該詞重新造句
→ 再次評分
```

**API 呼叫格式（api.js）：**
```javascript
async function gradeTranslation(chineseOriginal, userEnglish, referenceAnswer) {
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': API_KEY,
      'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1000,
      messages: [{
        role: 'user',
        content: GRADE_PROMPT(chineseOriginal, userEnglish, referenceAnswer)
      }]
    })
  });
  return response.json();
}
```

**評分 Prompt 模板：**
```
You are an IELTS writing examiner. Grade the following translation attempt.

Chinese original: {chineseOriginal}
User's English: {userEnglish}
CLB 9 reference: {referenceAnswer}

Respond in JSON only:
{
  "score": <1-9>,
  "clb_level": <"CLB 6" | "CLB 7" | "CLB 8" | "CLB 9">,
  "issues": ["issue 1", "issue 2"],
  "corrected": "corrected version of user's sentence",
  "key_vocab": "one CLB9 word the user missed or misused",
  "follow_up_prompt": "Use the word [key_vocab] to translate: [simpler Chinese sentence]"
}
```

### 3. 追加題機制

```javascript
// 如果分數 < 7，自動出追加題
if (result.score < 7) {
  showFollowUpQuestion(result.key_vocab, result.follow_up_prompt);
}
```

---

## Phase 2 目標：自動更新題庫

### GitHub Actions 設定（.github/workflows/update-content.yml）

```yaml
name: Update Content
on:
  schedule:
    - cron: '0 0 * * 0'  # 每週日自動執行
  workflow_dispatch:       # 也可手動觸發

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run update script
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: node scripts/update-content.js
      - name: Commit and push
        run: |
          git config --global user.email "action@github.com"
          git config --global user.name "GitHub Action"
          git add data/
          git commit -m "Auto-update content" || exit 0
          git push
```

### 自動更新腳本邏輯（update-content.js）

```javascript
// 1. 抓取來源
const sources = [
  'https://www.ielts.org/about-the-test/sample-test-questions',
  'https://takeielts.britishcouncil.org/take-ielts/preparation'
  // Cambridge IELTS 官方範文
];

// 2. 用 Claude API 從範文提取
// - 新的 CLB 9 詞彙（加入 vocab.json）
// - 好句型（加入 translate.json）
// - 同義詞辨別題（加入 clb9.json）

// 3. 去重後寫入 data/en/*.json
// 4. GitHub Actions 自動 commit & push
```

---

## Phase 3 目標：法文 CLB7 版本

### 框架複用原則

- `js/app.js`、`js/storage.js`、`js/quiz.js` **完全不動**
- 只需新增 `data/fr/*.json` 題庫
- `index.html` 加一個語言切換按鈕

### 法文題庫結構（與英文相同格式）

```json
// data/fr/vocab.json
[
  {
    "zh": "你好",
    "fr": "Bonjour",
    "note": "最基本的問候語"
  }
]
```

---

## 環境變數設定

```bash
# .env（本地開發用，不要 commit）
ANTHROPIC_API_KEY=your_key_here

# GitHub Secrets（線上部署用）
# Settings → Secrets → ANTHROPIC_API_KEY
```

---

## GitHub Pages 部署

```bash
# 1. 建立 repository
# 2. push 所有檔案
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourname/ielts-quiz.git
git push -u origin main

# 3. Settings → Pages → Source: main branch / root
# 4. 網址：yourname.github.io/ielts-quiz
```

---

## 目前題庫規模（已有）

| 模式 | 題數 |
|------|------|
| 單字配對 | 44 題 |
| 文法挑錯 | 8 題 |
| 句型中翻英 | 5 題 |
| CLB9 辨別 | 8 題 |
| 閱讀理解 | 4 篇 × 3 題 |

所有題庫已在 `index.html` 的 JS 陣列中，Phase 1 第一步是把它們搬到 `data/en/*.json`。

---

## 開始指令

到 Claude Code 之後，第一件事：

```
請幫我把 index.html 裡的所有題庫資料（VOCAB_DATA、GRAMMAR_DATA、
TRANSLATE_DATA、CLB9_DATA、READING_DATA）提取出來，
分別存成 data/en/ 底下的 JSON 檔案，
並更新 index.html 改成從 fetch() 讀取這些 JSON 檔案。
```
