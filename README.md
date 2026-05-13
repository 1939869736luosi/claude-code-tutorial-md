# Claude Code 安装与第三方 API 网关配置

系统要求：macOS 13+ 或 Windows 10 1809+，4GB 以上内存，能正常联网。

## 1. 安装 Claude Code

### macOS

先检查 Git：

```bash
git --version
```

如果没有显示版本号，先安装 Command Line Tools：

```bash
xcode-select --install
```

打开终端，运行官方 Native Install：

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

安装完成后，重新打开终端，检查版本：

```bash
claude --version
claude doctor
```

`claude --version` 显示版本号，`claude doctor` 没有关键报错，就说明安装成功。

---

### Windows

Windows 只推荐这一套：Git for Windows + PowerShell Native Install。

先安装 Git for Windows：

<https://git-scm.com/downloads/win>

![Git for Windows 下载页](images/image_3.png)

安装时一路保持默认选项即可。Git for Windows 会带上 Git Bash，Claude Code 在原生 Windows 上推荐使用它来执行命令。

然后打开 PowerShell，运行官方 Native Install：

```powershell
irm https://claude.ai/install.ps1 | iex
```

安装完成后，重新打开 PowerShell，检查版本：

```powershell
claude --version
claude doctor
```

`claude --version` 显示版本号，`claude doctor` 没有关键报错，就说明安装成功。

---

## 2. 配置第三方 API 网关

Claude Code 的第三方 API 网关配置只写到这一个文件：

| 系统 | 文件位置 |
|---|---|
| macOS | `~/.claude/settings.json` |
| Windows | `C:\Users\<你的用户名>\.claude\settings.json` |

如果 `.claude` 文件夹或 `settings.json` 不存在，就手动新建。

把下面内容写进去：

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "替换成你的 API Key",
    "ANTHROPIC_BASE_URL": "替换成你的第三方 API 网关地址"
  }
}
```

常见示例：

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxxxxxxxxxxxxxxxxxxxxxxx",
    "ANTHROPIC_BASE_URL": "https://api.kimi.com/coding/"
  }
}
```

保存后重新启动 Claude Code：

```bash
claude
```

进入 Claude Code 后输入：

```text
/status
```

确认当前连接已经走第三方 API 网关。

---

## 3. VS Code 系 IDE 使用第三方 API

VS Code 系 IDE 都优先使用上面的 `~/.claude/settings.json`。

不要在 IDE 的 `claudeCode.environmentVariables` 里配置 API。API 配置只放在 `~/.claude/settings.json`。

如果 IDE 用户设置里已经有 `claudeCode.environmentVariables`，直接删掉这一项。

如果 Antigravity 等 VS Code fork 报 `v is not iterable`、`TypeError`，或者 Claude Code 扩展命令没有注册，优先检查并删除 IDE 用户设置里的 `claudeCode.environmentVariables`。

### 安装 Claude Code 扩展

在 Cursor、VS Code、Windsurf 这类 VS Code 系 IDE 里，打开 Extensions，搜索：

```text
Claude Code
```

安装 Anthropic 发布的 Claude Code 扩展。

如果扩展装不上，就打开 IDE 内置终端，直接运行：

```bash
claude
```

### 配置 IDE 用户设置

插件端使用第三方 API 网关时，固定做三件事：

| 步骤 | 配置 | 作用 |
|---|---|---|
| 1 | `~/.claude/settings.json` | 配第三方 API Key 和 Base URL |
| 2 | `Claude Code: Disable Login Prompt` | 关闭插件里的 Anthropic 官方登录提示 |
| 3 | `~/.claude.json` 里的 `hasCompletedOnboarding` | 跳过首次引导状态，避免 VS Code fork 卡在欢迎页或登录页 |

### 打开 Disable Login Prompt

打开当前 IDE 的用户设置文件：

| IDE | macOS | Windows |
|---|---|---|
| VS Code | `~/Library/Application Support/Code/User/settings.json` | `%APPDATA%\Code\User\settings.json` |
| Cursor | `~/Library/Application Support/Cursor/User/settings.json` | `%APPDATA%\Cursor\User\settings.json` |
| Windsurf | `~/Library/Application Support/Windsurf/User/settings.json` | `%APPDATA%\Windsurf\User\settings.json` |
| 其它 VS Code fork | `~/Library/Application Support/<IDE 名>/User/settings.json` | `%APPDATA%\<IDE 名>\User\settings.json` |

如果用 Cursor 里的 agent 帮你安装，就让它直接编辑这个文件，不要让它去设置界面里搜索开关。

把下面配置写进去：

```json
{
  "claudeCode.disableLoginPrompt": true,
  "claudeCode.autosave": true,
  "claudeCode.enableNewConversationShortcut": true
}
```

如果文件里已经有其它配置，只把这几项合并进去，不要删原来的内容。例如：

```json
{
  "editor.fontSize": 14,
  "claudeCode.disableLoginPrompt": true,
  "claudeCode.autosave": true,
  "claudeCode.enableNewConversationShortcut": true
}
```

手动操作时，如果设置界面能搜到插件设置，也可以搜索：

```text
Claude Code login
```

或：

```text
disableLoginPrompt
```

勾选 `Claude Code: Disable Login Prompt`。如果搜不到，就直接改上面的 `settings.json`。

然后执行：

```text
Developer: Reload Window
```

重新加载窗口。

### 配置 hasCompletedOnboarding

`hasCompletedOnboarding` 要保留，但不要把它理解成 API 认证。

它的作用是让 Claude Code 认为首次引导已经完成。真正的第三方 API 认证来自：

- `~/.claude/settings.json` 里的 `ANTHROPIC_AUTH_TOKEN`
- `~/.claude/settings.json` 里的 `ANTHROPIC_BASE_URL`
- IDE 里的 `Claude Code: Disable Login Prompt`

在 VS Code fork 里使用第三方 API 时，直接把这个配置也加上：

| 系统 | 文件位置 |
|---|---|
| macOS | `~/.claude.json` |
| Windows | `C:\Users\<你的用户名>\.claude.json` |

文件不存在就新建，写入：

```json
{
  "hasCompletedOnboarding": true
}
```

如果文件已经存在，只加这一行，不要删掉原来的其它字段：

```json
"hasCompletedOnboarding": true
```

---

## 4. Windows 找不到 Git Bash

如果 Windows 已安装 Git for Windows，但 Claude Code 仍提示找不到 Git Bash，把 Git Bash 路径也写进同一个 `settings.json`：

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "替换成你的 API Key",
    "ANTHROPIC_BASE_URL": "替换成你的第三方 API 网关地址",
    "CLAUDE_CODE_GIT_BASH_PATH": "C:\\Program Files\\Git\\bin\\bash.exe"
  }
}
```

如果你的 Git 安装在别的位置，就把路径改成自己的 `bash.exe` 路径。

如果仍然报错，再用备用修复。

在 PowerShell 中运行：

```powershell
$files = @()
$files += Get-ChildItem "$env:USERPROFILE\.vscode\extensions\anthropic.claude-code-*\extension.js" -ErrorAction SilentlyContinue
$files += Get-ChildItem "$env:USERPROFILE\.cursor\extensions\anthropic.claude-code-*\extension.js" -ErrorAction SilentlyContinue
$files += Get-ChildItem "$env:USERPROFILE\.windsurf\extensions\anthropic.claude-code-*\extension.js" -ErrorAction SilentlyContinue
$files += Get-ChildItem "$env:USERPROFILE\.*\extensions\anthropic.claude-code-*\extension.js" -ErrorAction SilentlyContinue
$file = $files | Select-Object -First 1

if (-not $file) { Write-Host "ERROR: Claude Code extension not found"; return }

$content = [System.IO.File]::ReadAllText($file.FullName)
$pattern = 'catch{throw Error("Claude Code on Windows requires git-bash'
$idx = $content.IndexOf($pattern)

if ($idx -ge 0) {
    $catchEnd = $content.IndexOf(')")', $idx) + 3
    $oldBlock = $content.Substring($idx, $catchEnd - $idx)
    $content = $content.Replace($oldBlock, 'catch{return}')
    [System.IO.File]::WriteAllText($file.FullName, $content)
    Write-Host "SUCCESS: Git Bash check bypassed in $($file.FullName)"
} else {
    Write-Host "Already patched or pattern changed in this version"
}
```

扩展更新后，如果同样的报错又出现，再运行一次上面的命令。

---

## 5. 官方参考

- Claude Code 官方概览：<https://code.claude.com/docs/en>
- 官方安装说明：<https://code.claude.com/docs/zh-CN/setup>
- Settings 配置说明：<https://code.claude.com/docs/en/settings>
- Environment variables：<https://code.claude.com/docs/en/env-vars>
- LLM gateway 配置：<https://code.claude.com/docs/zh-CN/llm-gateway>
- VS Code 扩展说明：<https://code.claude.com/docs/en/vs-code>
