
# Claude Code（非官方原始碼提取）

追蹤我：https://x.com/paidev



> **這不是 Anthropic 的官方儲存庫。**

本儲存庫包含從 [Anthropic 的 Claude Code](https://www.anthropic.com/) CLI 工具中提取的 TypeScript 原始碼。Claude Code 是 Anthropic 官方推出的命令列介面工具，讓您可以直接在終端機中與 Claude 互動，執行軟體工程任務，例如編輯檔案、執行指令、搜尋程式碼庫、管理 Git 工作流程等。

原始碼是透過解包官方發佈的 npm 套件中附帶的 source map（`cli.js.map`）所取得。

- **npm 套件：** [@anthropic-ai/claude-code v2.1.88](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88)
- **官方首頁：** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

## 如何洩露的

原始碼洩露由 [Chaofan Shou (@Fried_rice)](https://x.com/Fried_rice) 發現，並於 2026 年 3 月 31 日公開發佈：

> *「Claude Code 的原始碼透過 npm registry 中的 map 檔案洩露了！」*
>
> — [@Fried_rice](https://x.com/Fried_rice)，2026 年 3 月 31 日

已發佈的 npm 套件（`@anthropic-ai/claude-code`）包含了一個 source map 檔案（`cli.js.map`），其中含有完整且未經混淆的 TypeScript 原始碼。該 source map 的 `sourcesContent` 欄位保存了所有被打包進 `cli.js` 的原始 `.ts`/`.tsx` 檔案，使得整個程式碼庫可以輕易地被提取出來。

## 為什麼會有這個儲存庫？

Anthropic 將 Claude Code 以打包後的 JavaScript CLI 形式發佈在 npm 上。發佈的套件中包含一個 source map 檔案（`cli.js.map`），其中包含原始的 TypeScript 原始碼。本儲存庫僅僅是提取並保存這些原始碼，以便於閱讀和參考。

## 如何自行取得

### 複製本儲存庫

```bash
git clone git@github.com:chatgptprojects/claude-code.git
cd claude-code
```

### 或從 npm 自行提取

1. **安裝套件：**

```bash
mkdir claude-code-extract && cd claude-code-extract
npm pack @anthropic-ai/claude-code@2.1.88
tar -xzf anthropic-ai-claude-code-2.1.88.tgz
cd package
```

2. **執行解包腳本：**

建立一個名為 `unpack.mjs` 的檔案：

```js
import { readFileSync, writeFileSync, mkdirSync } from "fs";
import { dirname, join } from "path";

const mapFile = join(import.meta.dirname, "cli.js.map");
const outDir = join(import.meta.dirname, "unpacked");

console.log("Reading source map...");
const map = JSON.parse(readFileSync(mapFile, "utf-8"));

const sources = map.sources || [];
const contents = map.sourcesContent || [];

console.log(`Found ${sources.length} source files.`);

let written = 0;
let skipped = 0;

for (let i = 0; i < sources.length; i++) {
  const src = sources[i];
  const content = contents[i];

  if (content == null) {
    skipped++;
    continue;
  }

  const outPath = join(outDir, src.replace(/^\.\.\//g, ""));
  mkdirSync(dirname(outPath), { recursive: true });
  writeFileSync(outPath, content);
  written++;
}

console.log(`Done! Wrote ${written} files to ${outDir}`);
if (skipped > 0) console.log(`Skipped ${skipped} files with no content.`);
```

3. **執行腳本：**

```bash
node unpack.mjs
```

提取出的原始碼將位於 `unpacked/` 目錄中。

## 專案結構

```
src/
├── cli/           # CLI 進入點與參數解析
├── commands/      # 指令實作
├── components/    # UI 元件（Ink/React）
├── constants/     # 應用程式常數與設定
├── context/       # 上下文管理
├── hooks/         # React Hooks
├── ink/           # 終端機 UI（Ink 框架）
├── services/      # 核心服務
├── skills/        # 技能定義
├── tools/         # 工具實作（檔案編輯、搜尋等）
├── types/         # TypeScript 型別定義
├── utils/         # 工具函式
├── main.tsx       # 主應用程式進入點
├── query.ts       # 查詢處理
└── ...
```

## 免責聲明

本儲存庫中的所有程式碼皆為 [Anthropic](https://www.anthropic.com/) 的智慧財產權。本儲存庫僅供**教育與參考用途**。有關使用限制，請參閱 Anthropic 的[授權條款](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88)。

## Star 歷史

<a href="https://www.star-history.com/?repos=chatgptprojects%2Fclaude-code&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/image?repos=chatgptprojects/claude-code&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/image?repos=chatgptprojects/claude-code&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/image?repos=chatgptprojects/claude-code&type=date&legend=top-left" />
 </picture>
</a>

本儲存庫與 Anthropic **無任何關聯、背書或支持關係**。
