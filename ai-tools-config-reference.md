# AI 編程助手 Prompt / Skills / MCP 路徑對照表

> 整理日期：2026年3月3日｜涵蓋 Claude CLI、GitHub Copilot、OpenAI Codex CLI、Google Gemini CLI

---

## 快速對照總表

| 工具 | 全局指令檔 | 專案指令檔 | 全局 Skills | 專案 Skills | MCP 全局配置 | MCP 專案配置 |
|------|-----------|-----------|------------|------------|------------|------------|
| **Claude CLI** | `~/.claude/CLAUDE.md` | `./CLAUDE.md` | `~/.claude/skills/<n>/SKILL.md` | `.claude/skills/<n>/SKILL.md` | `~/.claude.json` | `./.mcp.json` |
| **GitHub Copilot (VS Code)** | `settings.json`（`github.copilot.chat.instructions`） | `.github/copilot-instructions.md` | `~/.copilot/skills/<n>/SKILL.md`（⚠️ Experimental / unofficial） | `.github/skills/<n>/SKILL.md` | `~/.config/Code/User/mcp.json` | `.vscode/mcp.json` |
| **Codex CLI** | `~/.codex/AGENTS.md`（偏好）/ `~/.codex/instructions.md`（舊） | `./AGENTS.md` | —（未正式文件化） | `.codex/skills/<n>/SKILL.md` | `~/.codex/config.toml` | `./AGENTS.md` |
| **Gemini CLI** | `~/.gemini/GEMINI.md` | `./GEMINI.md` | `~/.gemini/skills/<n>/SKILL.md` | `.gemini/skills/<n>/SKILL.md` | `~/.gemini/settings.json` | `./.gemini/settings.json` |

> **格式趨同**：Claude、Copilot、Codex、Gemini 的 Skill 格式正逐漸向 AgentSkills-style（`SKILL.md`）慣例收斂（YAML frontmatter + Markdown），核心格式相容、跨工具可移植；此為 ecosystem convention，非官方標準組織，各工具的支援程度不一。

---

## 1. Claude CLI（Anthropic）

### 全局配置目錄

| 平台 | 路徑 |
|------|------|
| Linux / macOS | `~/.claude/` |
| Windows | `%USERPROFILE%\.claude\` |

### 提示／指令檔案

| 類型 | 路徑 | 說明 |
|------|------|------|
| 全局用戶指令 | `~/.claude/CLAUDE.md` | 適用於所有專案 |
| 全局規則目錄 | `~/.claude/rules/*.md` | 個人跨專案規則 |
| 專案級（共享） | `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 提交到版控 |
| 專案級（不共享） | `./CLAUDE.local.md` | 自動加入 `.gitignore`，個人本機設定 |
| 專案規則目錄 | `./.claude/rules/*.md` | 支援 YAML frontmatter 以路徑限定規則範圍 |
| 組織管理級 (Linux) | `/etc/claude-code/CLAUDE.md` | 需系統管理員部署（路徑視 deployment 而定） |
| 組織管理級 (macOS) | `/Library/Application Support/ClaudeCode/CLAUDE.md` | 同上（路徑視 deployment 而定） |
| 組織管理級 (Windows) | `C:\Program Files\ClaudeCode\CLAUDE.md` | 同上（路徑視 deployment 而定） |

### Auto Memory（自動記憶）

```
~/.claude/projects/<project_hash>/memory/
├── MEMORY.md           # 每次 session 載入前 200 行
├── debugging.md        # 各主題檔案（按需載入）
└── ...
```

### MCP 配置

| 作用域 | 路徑 | 說明 |
|--------|------|------|
| User / Local scope | `~/.claude.json` | `mcpServers` 欄位 |
| Project scope（共享） | `./.mcp.json` | 提交到版控 |
| 組織管理 (Linux) | `/etc/claude-code/managed-mcp.json` | 系統管理員部署（路徑視 deployment 而定） |
| 組織管理 (macOS) | `/Library/Application Support/ClaudeCode/managed-mcp.json` | 同上（路徑視 deployment 而定） |
| 組織管理 (Windows) | `C:\Program Files\ClaudeCode\managed-mcp.json` | 同上（路徑視 deployment 而定） |
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json`（舊版為 `Claude Desktop/`，視安裝版本而定） | Desktop App |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` | Desktop App |

**`~/.claude.json` 範例：**

```json
{
  "mcpServers": {
    "my-server": {
      "type": "http",
      "url": "https://my-mcp.example.com/mcp"
    }
  }
}
```

**`.mcp.json` 範例：**

```json
{
  "mcpServers": {
    "shared-server": {
      "command": "/path/to/server",
      "args": [],
      "env": {}
    }
  }
}
```

### Skills（自訂可重用技能）

| 範圍 | 路徑 | 說明 |
|------|------|------|
| 全局 | `~/.claude/skills/<name>/SKILL.md` | 個人跨專案技能，`/name` 觸發 |
| 專案 | `.claude/skills/<name>/SKILL.md` | 提交到版控共享 |
| 全局（舊格式，相容） | `~/.claude/commands/<name>.md` | 即將合併進 skills |
| 專案（舊格式，相容） | `.claude/commands/<name>.md` | 同上 |

**SKILL.md frontmatter 格式：**

```markdown
---
name: fix-issue
description: Fix a GitHub issue
allowed-tools: Read, Grep, Bash(git *)
argument-hint: [issue-number]
---

Fix GitHub issue $ARGUMENTS following our coding standards.
```

**觸發**：`/name [args]` 或 Claude 根據 `description` 自動判斷載入。

### 其他設定檔

| 路徑 | 說明 |
|------|------|
| `.claude/settings.json` | 專案設定 |
| `.claude/settings.local.json` | 本機設定（不提交） |

---

## 2. GitHub Copilot（VS Code 擴充套件）

### 提示／指令檔案

| 類型 | 路徑 | 說明 |
|------|------|------|
| 全局自訂指令 | `settings.json` → `github.copilot.chat.instructions` | 用戶設定檔，適用所有 repo（`codeGeneration.instructions` 為舊 Preview key，部分版本已失效） |
| 專案級（全 repo） | `.github/copilot-instructions.md` | 提交到版控，最廣泛支援 |
| 路徑指定指令 | `.github/instructions/<name>.instructions.md` | YAML frontmatter `applyTo:` 欄位指定生效路徑 |

**VS Code `settings.json` 全局指令設定：**

```json
{
  "github.copilot.chat.instructions": [
    { "text": "Always use TypeScript strict mode" }
  ]
}
```

**`.github/instructions/api.instructions.md` 範例：**

```markdown
---
applyTo: "src/api/**/*.ts"
---
All API handlers must include input validation.
```

### MCP 配置

| 類型 | 路徑 | 說明 |
|------|------|------|
| 專案級 | `.vscode/mcp.json` | 提交到版控共享 |
| 用戶全局（預設） | `~/.config/Code/User/mcp.json`（Linux） | 跨工作區，或執行 `MCP: Open User Configuration` 命令 |
| 用戶全局（啟用 Profile 時） | `~/.config/Code/User/profiles/<profile_id>/mcp.json`（Linux） | 僅在啟用 Profile 功能後出現 |
| 遠端環境 | — | 執行 `MCP: Open Remote User Configuration` 命令 |

**`.vscode/mcp.json` 範例：**

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp"
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-server-playwright"]
    }
  }
}
```

### Skills（自訂可重用技能）

| 類型 | 範圍 | 路徑 | 說明 |
|------|------|------|------|
| Agent Skills | 全局 | `~/.copilot/skills/<name>/SKILL.md`（⚠️ Experimental / unofficial） | 個人跨專案，`/name` 觸發；屬 Agent Preview 行為，非 stable feature |
| Agent Skills | 專案 | `.github/skills/<name>/SKILL.md` | 提交到版控共享 |
| Prompt Files | 全局 | VS Code Profile `prompts/` 資料夾 | 手動 `/name` 觸發 |
| Prompt Files | 專案 | `.github/prompts/<name>.prompt.md` | 手動 `/name` 觸發 |
| Path Instructions | 專案 | `.github/instructions/<name>.instructions.md` | 依 `applyTo:` glob 自動附加 |

**`.github/prompts/<name>.prompt.md` frontmatter 格式：**

```markdown
---
name: create-react-form
description: Scaffold a React form
agent: agent
tools: [githubRepo, terminal]
argument-hint: [formName]
---

Create a React form named ${input:formName}.
```

---

## 3. OpenAI Codex CLI

### 全局配置目錄

| 平台 | 路徑 |
|------|------|
| Linux / macOS | `~/.codex/` |
| Windows | `%USERPROFILE%\.codex\` |

### 提示／指令檔案

| 類型 | 路徑 | 說明 |
|------|------|------|
| 全局指令（偏好） | `~/.codex/AGENTS.md` | 新版 Agent runtime 採用 |
| 全局指令（舊版） | `~/.codex/instructions.md` | Early Codex CLI legacy，仍相容 |
| 專案級 | `./AGENTS.md` | 專案根目錄，提交到版控 |
| 子目錄 | 任何子目錄中的 `AGENTS.md` | 最近的檔案優先 |

> `AGENTS.md` 為開放格式（[agents.md](https://agents.md/)），被多數 Agent-style CLI（如 Gemini CLI）相容採用；GitHub Copilot 目前未正式採用 AGENTS.md，部分 Agent Preview 行為可能出現相容情境，但不屬官方支援格式。

### MCP 配置

| 路徑 | 說明 |
|------|------|
| `~/.codex/config.toml` | 全局配置，含 MCP 伺服器設定 |

**`~/.codex/config.toml` 範例：**

```toml
[mcp_servers.my-server]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-filesystem", "/path"]

[mcp_servers.remote-server]
url = "https://my-server.example.com/sse"
```

**CLI 管理命令：**

```bash
codex mcp add my-server -- npx -y some-mcp-package
codex mcp list
codex mcp remove my-server
```

### Skills（自訂可重用技能）

| 範圍 | 路徑 | 說明 |
|------|------|------|
| 專案 | `.codex/skills/<name>/SKILL.md` | 與 Claude/Copilot 相同格式 |
| 全局 | 未有正式文件化路徑 | — |

格式實務上與 Claude Code 的 `SKILL.md` 相容（YAML frontmatter + Markdown），但尚未作為 Codex CLI 的正式標準文件化格式。

---

## 4. Google Gemini CLI

### 全局配置目錄

| 平台 | 路徑 |
|------|------|
| Linux / macOS | `~/.gemini/` |
| Windows | `%USERPROFILE%\.gemini\` |

### 設定檔優先順序（高→低）

| 路徑 | 說明 |
|------|------|
| `/etc/gemini-cli/settings.json` (Linux) | 系統管理員控制，最高優先（僅 enterprise / managed 安裝才存在） |
| `C:\ProgramData\gemini-cli\settings.json` (Windows) | 同上（僅 managed 安裝） |
| `/Library/Application Support/GeminiCli/settings.json` (macOS) | 同上（僅 managed 安裝） |
| `.gemini/settings.json` | 專案設定 |
| `~/.gemini/settings.json` | 全局用戶設定 |
| `/etc/gemini-cli/system-defaults.json` (Linux) | 系統預設，最低優先 |

### 提示／指令檔案

| 類型 | 路徑 | 說明 |
|------|------|------|
| 全局 | `~/.gemini/GEMINI.md` | 所有專案的預設指令 |
| 專案及父目錄 | `./GEMINI.md` | 從 cwd 向上搜尋至 `.git` 或 home |
| 子目錄（按需載入） | 任何子目錄中的 `GEMINI.md` | JIT 載入 |

> 可在 `settings.json` 中透過 `context.fileName` 自訂讀取的檔案名稱：
>
> ```json
> { "context": { "fileName": ["AGENTS.md", "GEMINI.md", "CONTEXT.md"] } }
> ```

執行 `/memory add` 命令可直接向 `~/.gemini/GEMINI.md` 追加內容。

### MCP 配置

| 路徑 | 說明 |
|------|------|
| `~/.gemini/settings.json` | 全局 MCP 設定（user scope） |
| `./.gemini/settings.json` | 專案 MCP 設定 |
| `~/.gemini/mcp-oauth-tokens.json` | OAuth token 儲存 |
| `~/.gemini/mcp-server-enablement.json` | 伺服器啟用／停用狀態 |

**`~/.gemini/settings.json` 範例：**

```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "env": { "API_KEY": "$MY_API_KEY" }
    },
    "remote-server": {
      "httpUrl": "https://api.example.com/mcp",
      "headers": { "Authorization": "Bearer ${TOKEN}" }
    }
  }
}
```

**CLI 管理命令：**

```bash
gemini mcp add -s user my-server python server.py
gemini mcp add --transport http -s project api-server https://api.example.com/mcp
gemini mcp list
gemini mcp remove my-server
```

### Skills（自訂可重用技能）

Gemini CLI 有兩種技能機制：

#### a. SKILL.md（Agent-style Skills 慣例格式，與 Claude / Copilot 生態實務相容）

| 範圍 | 路徑 | 說明 |
|------|------|------|
| 全局 | `~/.gemini/skills/<name>/SKILL.md` | 個人跨專案，`/name` 觸發或自動 |
| 專案 | `.gemini/skills/<name>/SKILL.md` | 提交到版控共享 |

#### b. Custom Commands（Gemini 獨有，TOML 格式）

| 範圍 | 路徑 | 說明 |
|------|------|------|
| 全局 | `~/.gemini/commands/<name>.toml` | `/name` 觸發 |
| 專案 | `.gemini/commands/<name>.toml` | `/name` 觸發 |
| 命名空間 | `.gemini/commands/git/commit.toml` | `/git:commit` 觸發 |

**`.gemini/commands/<name>.toml` 格式（TOML，非 Markdown）：**

```toml
description = "Generates a Git commit message"
prompt = """
Based on the staged diff below, write a commit message:

```diff
!{git diff --staged}
```
"""
# {{args}} 注入使用者參數，shell 指令用 !{...}，檔案注入用 @{path}
```

執行 `/commands reload` 可熱重載所有自訂指令。
