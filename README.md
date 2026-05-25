# andrej-karpathy-skills

源自 [Andrej Karpathy 的觀察](https://x.com/karpathy/status/2015883857489522876) 關於 LLM 編碼陷阱的總結。

一個單一的 `CLAUDE.md` 文件，用於改善 Claude Code 的行為，靈感來自 Andrej Karpathy 對 LLM 編碼缺陷的觀察。

[![Stars](https://img.shields.io/github/stars/multica-ai/andrej-karpathy-skills?style=flat)](https://github.com/multica-ai/andrej-karpathy-skills/stargazers)
[![Forks](https://img.shields.io/github/forks/multica-ai/andrej-karpathy-skills?style=flat)](https://github.com/multica-ai/andrej-karpathy-skills/forks)

## 問題背景

Andrej Karpathy 指出 LLM 在編碼時存在的問題：

> "模型會為你做出錯誤的假設並且直接執行而不檢查。它們不會管理自己的困惑、不會尋求澄清、不會呈現不一致、不會呈現權衡取捨、不會在應該反對時反對。"

> "它們非常喜歡過度複雜化代碼和 API，膨脹抽象層，不清理死代碼... 實現一個超過 1000 行的複雜結構，而 100 行就夠了。"

> "它們仍然有時會更改/移除它們理解不足的註釋和代碼作為副作用，即使這與任務正交。"

## 解決方案：四大原則

一個檔案包含四個直接解決這些問題的原則：

| 原則 | 對應問題 |
|------|----------|
| **Coding 前先思考 (Think Before Coding)** | 錯誤假設、隱藏困惑、遺漏權衡 |
| **簡潔優先 (Simplicity First)** | 過度複雜、膨脹抽象 |
| **精準改動 (Surgical Changes)** | 正交編輯、觸碰不該碰的程式碼 |
| **目標導向執行 (Goal-Driven Execution)** | 透過測試優先、驗證成功標準來槓桿化 |

### 1. Coding 前先思考

**不要假設。不要隱藏困惑。呈現權衡取捨。**

- **明確陳述假設** — 如果不確定，問而不是猜
- **呈現多種解釋** — 當存在歧義時不要默默選擇
- **在合理時反對** — 如果存在更簡單的方法，說出來
- **困惑時停下** — 說明不清楚的地方並尋求澄清

### 2. 簡潔優先

**最少的程式碼解決問題。不要投機。**

- 不要添加超出要求的功能
- 不要為單次使用的程式碼建立抽象
- 不要添加未請求的「靈活性」或「可配置性」
- 不要為不可能的場景添加錯誤處理
- 如果 200 行可以變成 50 行，就重寫

**測試標準：** 如果資深工程師說這太複雜了嗎？如果是，就簡化。

### 3. 精準改動

**只觸碰必須的。只清理自己的爛攤子。**

- 不要「改進」相鄰的程式碼、註釋或格式
- 不要重構沒有壞掉的東西
- 匹配現有風格，即使你會用不同方式
- 如果注意到無關的死程式碼，提到它 — 不要刪除

**測試標準：** 每個變更的行都應該可以直接追溯到用戶的請求。

### 4. 目標導向執行

**定義成功標準。持續迴圈直到驗證。**

將命令式任務轉化為可驗證的目標：

| 不要這樣說... | 改成這樣... |
|---------------|-------------|
| "添加驗證" | "為無效輸入撰寫測試，然後讓它們通過" |
| "修復 bug" | "撰寫重現它的測試，然後讓它通過" |
| "重構 X" | "確保測試在重構前後都通過" |

對於多步驟任務，陳述簡短計劃：
```
1. [步驟] → 驗證: [檢查]
2. [步驟] → 驗證: [檢查]
3. [步驟] → 驗證: [檢查]
```

## 安裝方式

### 選項 A：Claude Code Plugin（推薦）

在 Claude Code 中，先添加 marketplace：
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

然後安裝插件：
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

這會將指南安裝為 Claude Code 插件，使其可在所有專案中使用的技能。

### 選項 B：CLAUDE.md（每專案）

新專案：
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

現有專案（附加）：
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## 與 Cursor 一起使用

此倉庫包含一個已提交的 Cursor 專案規則（[`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)），因此當你在 Cursor 中打開專案時，相同的指南也適用。詳見 **[CURSOR.md](CURSOR.md)** 了解設定方法、在其他專案中使用規則，以及與 Claude Code 的關係。

## 關鍵洞察

Andrej Karpathy 說：

> "LLM 在迴圈直到滿足特定目標方面非常擅長... 不要告訴它要做什麼，給它成功標準然後看它發揮。"

「目標導向執行」原則捕捉了這一點：將命令式指令轉化為帶有驗證迴圈的宣告性目標。

## 如何知道它是否有效

這些指南正在起作用如果你看到：

- **diff 中不必要的變更更少** — 只有請求的變更出現
- **因為過度複雜而重寫更少** — 代碼第一次就很簡潔
- **澄清問題在實作之前出現** — 不是錯誤之後
- **乾淨、最小的 PR** — 沒有順手重構或「改進」

## 自訂

這些指南設計為與專案特定指令合併。將它們添加到你現有的 `CLAUDE.md` 或建立新的。

對於專案特定規則，添加如下部分：
```markdown
## 專案特定指南

- 使用 TypeScript strict 模式
- 所有 API 端點必須有測試
- 遵循 `src/utils/errors.ts` 中現有的錯誤處理模式
```

## 權衡注意

這些指南偏向**謹慎而非速度**。對於瑣碎任務（簡單的拼字錯誤修復、明顯的一行程式），使用判斷力 — 不是每個變更都需要完整的嚴謹性。

目標是減少非簡單工作中的代價高昂的錯誤，而不是減慢簡單任務的速度。

## License

MIT