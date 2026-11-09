# 連接 GitHub and Obsidian

這份懶人包帶你在 macOS 的 Codex Desktop 中完成兩件事：

1. 讓 Codex 能透過 Git 與 GitHub CLI 進行 commit 與 push。
2. 讓 Codex 透過 MCPVault 讀寫你的 Obsidian vault。

## 完成後你可以做什麼？

- 請 Codex 將專案 commit 並 push 到 GitHub。
- 請 Codex 搜尋、讀取與建立 Obsidian 筆記。
- 在工作結束時，把進度、決策與待辦寫回第二大腦。

## 先備條件

- 已安裝 Codex Desktop。
- 已有 GitHub 帳號。
- 已建立 Obsidian vault。
- 可使用終端機。

> 不要將 GitHub token、密碼或 `.codex/config.toml` 的完整內容放進 repo。

## 第一部分：GitHub CLI

### 1. 檢查工具

在 Terminal 執行：

```bash
git --version
gh --version
node --version
npm --version
```

如果缺少 GitHub CLI，可用 Homebrew 安裝：

```bash
brew install gh
```

### 2. 登入 GitHub

```bash
gh auth login --web --git-protocol https
gh auth status
```

依照瀏覽器畫面完成授權。成功後，`gh auth status` 應顯示已登入帳號與 `https` Git protocol。

### 3. 設定提交者資訊

```bash
git config --global user.name
git config --global user.email
```

若其中一項是空的，設定它們：

```bash
git config --global user.name "你的姓名"
git config --global user.email "你的 GitHub no-reply email"
```

建議使用 GitHub 提供的 no-reply email，避免公開私人 email。

### 4. 建立並推送測試 repo

```bash
mkdir -p ~/Documents/codex-github-test
cd ~/Documents/codex-github-test
printf '# Codex GitHub 測試\n' > README.md
git init
git add README.md
git commit -m "建立 Codex GitHub 測試"
gh repo create codex-github-test --private --source=. --push
```

驗證：

```bash
gh repo view codex-github-test --web
```

若測試 repo 不再需要，刪除前請確認名稱無誤：

```bash
gh repo delete codex-github-test --yes
rm -rf ~/Documents/codex-github-test
```

## 第二部分：Obsidian MCPVault

### 1. 找到 vault

你的 vault 是含有 `.obsidian` 資料夾的目錄。Google Drive for desktop 在 macOS 常見路徑如下：

```text
~/Library/CloudStorage/GoogleDrive-你的帳號/My Drive/Obsidian/你的Vault名稱
```

先確認 `.obsidian` 存在：

```bash
ls -la "/你的/vault/路徑/.obsidian"
```

### 2. 安裝 MCPVault

若全域 npm 目錄有寫入權限：

```bash
npm install -g @bitbonsai/mcpvault
```

若遇到 `EACCES`，改安裝到使用者私有目錄：

```bash
npm install --prefix ~/.local -g @bitbonsai/mcpvault
```

後者的執行檔通常是：

```text
~/.local/bin/mcpvault
```

### 3. 先測試 MCPVault

將以下的 vault 路徑換成自己的：

```bash
printf '%s\n' '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' \
  | ~/.local/bin/mcpvault "/你的/vault/路徑"
```

成功時會列出 `read_note`、`write_note`、`search_notes` 等工具。

### 4. 設定 Codex

先備份設定檔：

```bash
cp ~/.codex/config.toml ~/.codex/config.toml.bak-$(date +%Y%m%d-%H%M%S)
```

在 `~/.codex/config.toml` 加入以下內容；已有同名區塊時請更新它，而不是重複新增：

```toml
[mcp_servers.obsidian]
command = "/Users/你的帳號/.local/bin/mcpvault"
args = ["/你的/vault/路徑"]
startup_timeout_sec = 20
tool_timeout_sec = 60
```

完全結束並重新開啟 Codex Desktop。

### 5. 驗證讀寫

重新開啟後，向 Codex 請求：

```text
請列出我的 Obsidian vault 根目錄資料夾。
```

接著建立一則測試筆記並讀回：

```text
請在我的 Obsidian 筆記本建立「Codex 測試/連線測試.md」，內容寫入今天日期與「Codex 已成功連接 Obsidian MCP」。然後讀回這則筆記。
```

## 常見問題

| 現象 | 處理方式 |
| --- | --- |
| `gh auth status` 顯示未登入 | 執行 `gh auth login --web --git-protocol https`，並在瀏覽器完成授權。 |
| commit 失敗且要求作者資訊 | 設定 `git config --global user.name` 與 `user.email`。 |
| `npm install -g` 出現 `EACCES` | 使用 `npm install --prefix ~/.local -g @bitbonsai/mcpvault`。 |
| 重啟後沒有 Obsidian 工具 | 檢查 `config.toml` 的 command、vault 路徑與 TOML 語法，再完全重啟 Codex。 |
| 找不到 vault | 尋找含有 `.obsidian` 的資料夾，並確認雲端檔案已下載到本機。 |

## 安全提醒

- 只把實際要公開的內容推送到公開 repo。
- 推送前先看 `git status` 與 `git diff --staged`。
- 刪除 GitHub repo 或筆記前，先確認目標與備份。
