# Claude Code

## 1. Windows 配置

### 1.1 安装 Node.js

点击网址 <https://nodejs.org>

![Node.js 官网首页](images/image_1.png)

![Node.js 下载页](images/image_2.png)

下载好之后安装就行，全部选默认的参数就行。

---

### 1.2 安装 Git

点击网址 <https://git-scm.com/install/windows>

![Git 下载页](images/image_3.png)

点击对应的安装包，下载完成安装就行。

---

### 1.3 安装 Claude Code

打开 PowerShell 或 CMD，运行以下命令：

```bash
npm install -g @anthropic-ai/claude-code
```

安装完成后，输入以下命令检查 Node.js、Git、Claude Code 是否安装成功：

```bash
node --version
npm --version
claude --version
```

如下图所示，如果显示版本号，恭喜你！Claude Code 已经成功安装了。

![版本号验证截图](images/image_4.png)

---

### 1.4 配置环境变量

> 最好下面两个一起配置

#### 1.4.1 配置 settings.json 文件

创建（如果不存在）或编辑 `C:\Users\用户名\.claude\settings.json`，输入以下值并保存：

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "替换为您的API Key",
    "ANTHROPIC_BASE_URL": "https://www.fucheers.top",
    "CLAUDE_CODE_MAX_OUTPUT_TOKENS": "12000"
  },
  "permissions": {
    "allow": [],
    "deny": []
  }
}
```

---

#### 1.4.2 配置环境变量

`setup_claude_env.bat`：

```bat
@echo off
echo ========================================
echo   Claude Code Environment Setup
echo ========================================
echo.

set /p API_KEY=Please enter your API Key:

if "%API_KEY%"=="" (
    echo Error: API Key cannot be empty!
    pause
    exit /b 1
)

echo.
echo Setting environment variables...

setx ANTHROPIC_AUTH_TOKEN "%API_KEY%"
setx ANTHROPIC_BASE_URL "https://www.fucheers.top"
setx CLAUDE_CODE_MAX_OUTPUT_TOKENS "12000"

echo.
echo ========================================
echo   Setup Complete!
echo ========================================
echo.
echo Environment variables set:
echo   ANTHROPIC_AUTH_TOKEN = %API_KEY%
echo   ANTHROPIC_BASE_URL = https://www.fucheers.top
echo   CLAUDE_CODE_MAX_OUTPUT_TOKENS = 12000
echo.
echo Please restart your terminal for changes to take effect.
echo.
pause
```

下载这个 bat 文件，输入 API Key 回车即设置完成。

---

### 1.5 打开 Claude Code 终端

打开终端输入 `claude` 回车即可正常使用 Claude。

![Claude Code 终端界面](images/image_6.png)

---

<br/>

> [!NOTE]
> **以上为 PDF 原文教程内容。** 以下为补充资料，包含一键安装脚本、Claude Code 模型配置以及 VS Code 扩展设置说明，可独立参考。

---

## 2. 完整安装脚本（Windows）

```powershell
# 打开 Windows 终端中的 PowerShell 终端
# 右键按 Windows 按钮，点击「终端」

# 然后依次执行下面的
winget install --id Git.Git -e --source winget # 或者参考 https://git-scm.com/install/windows 用其他办法安装 Git
winget install OpenJS.NodeJS # 或者参考 https://nodejs.org/zh-cn/download 用其他办法安装 Node.js
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

# 然后关闭终端窗口，新开一个终端窗口

# 安装 claude-code
npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com

# 初始化配置
node --eval "
    const homeDir = os.homedir();
    const filePath = path.join(homeDir, '.claude.json');
    if (fs.existsSync(filePath)) {
        const content = JSON.parse(fs.readFileSync(filePath, 'utf-8'));
        fs.writeFileSync(filePath, JSON.stringify({ ...content, hasCompletedOnboarding: true }, null, 2), 'utf-8');
    } else {
        fs.writeFileSync(filePath, JSON.stringify({ hasCompletedOnboarding: true }), 'utf-8');
    }"
```

---

## 3. 配置 Claude Code 模型

完成 Claude Code 安装后，请按照以下方式设置环境变量使用 Claude Code 模型。

### macOS 与 Linux

```bash
export ANTHROPIC_BASE_URL=https://api.kimi.com/coding/
export ANTHROPIC_API_KEY=sk-kimi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  # 这里填在会员页面生成的 API Key

claude
```

### Windows

```powershell
$env:ANTHROPIC_BASE_URL="https://api.kimi.com/coding/";
$env:ANTHROPIC_API_KEY="sk-kimi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"  # 这里填在会员页面生成的 API Key

claude
```

### 确认环境变量是否生效

启动 Claude Code 后，在命令输入框输入 `/status`，确认模型状态。

接下来就可以使用 Claude Code 进行开发了！

---

## 4. VS Code 扩展与 Claude Code CLI

### 扩展设置

`~/.claude/settings.json` 中的 Claude Code 设置，在 VS Code 和 CLI 之间共享，用于配置环境变量、hooks 和 MCP servers。有关详细信息，请参阅 [Settings](https://code.claude.com/docs/en/settings)。

> 💡 在 `settings.json` 中添加 `"$schema": "https://json.schemastore.org/claude-code-settings.json"` 可以获得自动补全和内联验证。

![扩展设置表格（上半部分）](images/vscode_settings_top.png)

![扩展设置表格（下半部分）与 CLI 对比](images/vscode_settings_bottom.png)

| 设置 | 默认值 | 描述 |
|---|---|---|
| `selectedModel` | `default` | 新对话的默认模型，使用 `/model` 按命令切换 |
| `useTerminal` | `false` | 在终端里使用而不是编辑器面板中启动 Claude |
| `initialPermissionMode` | `default` | 初始权限模式：`default`（需要同意）、`plan`、`acceptEdits` 或 `bypassPermissions` |
| `preferredLocation` | `panel` | Claude 打开的位置：`sidebar`（右侧）或 `panel`（新标签页） |
| `autosave` | `true` | 在 Claude 读取或写入文件前自动保存文件 |
| `useCtrlEnterToSend` | `false` | 使用 Ctrl/Cmd+Enter 而不是 Enter 发送提示 |
| `enableNewConversationShortcut` | `true` | 启用 Cmd/Ctrl+N 以开始新对话 |
| `hideOnboarding` | `false` | 隐藏入门引导卡片（生产环境推荐） |
| `respectGitIgnore` | `true` | 从文件搜索中排除 `.gitignore` 匹配项 |
| `environmentVariables` | `[]` | 为 Claude 进程设置环境变量。共享配置请使用 Claude Code 设置 |
| **`disableLoginPrompt`** | **`false`** | **跳过身份验证提示（用于第三方提供商设置）** |
| `allowDangerouslySkipPermissions` | `false` | 跳过所有权限检查请求，**谨慎使用** |
| `claudeProcessWrapper` | `-` | 用于启动 Claude 进程的可执行文件路径 |

### 关键设置：Disable Login Prompt

> [!IMPORTANT]
> 使用第三方 API（如 Claude Code）时，必须在 VS Code 中勾选此选项，否则扩展会反复弹出 Anthropic 登录提示。

在 VS Code 中搜索 `@ext:Anthropic.claude-code`，找到 **Claude Code: Disable Login Prompt** 选项并勾选：

- **作用**：当设置为 `true` 时，扩展中不再提示登录/认证，适用于认证由外部处理的场景
- **推荐同时勾选**：
  - ✅ Claude Code: **Autosave** — 自动保存文件
  - ✅ Claude Code: **Enable New Conversation Shortcut** — Cmd/Ctrl+N 快捷键

---

### VS Code 扩展 vs. Claude Code CLI

Claude Code 同时提供 VS Code 扩展（图形界面）和 CLI（终端命令行）两种使用方式。部分功能仅在 CLI 中可用。如需使用 CLI 专属功能，可在 VS Code 集成终端中运行 `claude`。

| 功能 | CLI | VS Code 扩展 |
|---|---|---|
| 命令和技能 | [全部](https://code.claude.com/docs/en/interactive-mode#built-in-commands) | 部分（输入 `/` 查看可用命令） |
| MCP 服务器配置 | ✅ | ❌（通过 CLI 配置后在扩展中使用） |
| Checkpoints（检查点） | ❌ | ✅ |
| `!` bash 快捷方式 | ✅ | ❌ |
| Tab 补全 | ✅ | ❌ |
| 自定义 slash 命令 | ✅ | ✅ |

---

## 📎 相关资源

| 资源 | 说明 |
|------|------|
| [🧰 AI 工具箱 · 资源汇总](https://ai-toolkit-collection.pages.dev/) | Claude Code Skills、MCP 服务、开源模型与实用工具 |
| [📅 7天 AI 工具链全栈进化路线](https://7day-roadmap.pages.dev/) | 从 Obsidian 数据库到自媒体 IP 打造的完整学习路线 |
