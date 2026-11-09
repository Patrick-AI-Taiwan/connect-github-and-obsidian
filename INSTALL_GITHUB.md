# GitHub Setup Quick Guide

This guide sets up Git and GitHub CLI on macOS or Windows so you can create commits and push projects to GitHub. It is written for Codex Desktop users, but the commands work in any standard terminal.

## Before You Start

- A GitHub account
- An internet connection
- Terminal on macOS or PowerShell on Windows

> GitHub is the hosted service. Git is the version-control tool, and GitHub CLI (`gh`) connects your terminal to your GitHub account. You do not need a GitHub Copilot subscription for this guide.

## 1. Install Git and GitHub CLI

### macOS

Check whether the tools are already installed:

```bash
git --version
gh --version
```

If Git is missing, install Apple's Command Line Tools:

```bash
xcode-select --install
```

If you use Homebrew, install GitHub CLI with:

```bash
brew install gh
```

### Windows PowerShell

Check whether the tools are already installed:

```powershell
git --version
gh --version
```

Install anything that is missing:

```powershell
winget install --id Git.Git --accept-source-agreements --accept-package-agreements
winget install --id GitHub.cli --accept-source-agreements --accept-package-agreements
```

Close and reopen Codex Desktop or PowerShell after installation, then run the checks again.

## 2. Sign In to GitHub CLI

Run this on either platform:

```bash
gh auth login --web --git-protocol https
```

GitHub CLI displays a one-time code and opens a browser. Sign in to GitHub, enter the code if prompted, and approve the request. Verify the result:

```bash
gh auth status
```

The output should show your GitHub account and `Git operations protocol: https`.

## 3. Configure Your Commit Identity

Check your current identity:

```bash
git config --global user.name
git config --global user.email
```

Set either value if it is empty:

```bash
git config --global user.name "Your Name"
git config --global user.email "YOUR_GITHUB_NOREPLY_EMAIL"
```

Use the no-reply email address shown in your GitHub email settings if you do not want your personal email address included in public commits.

## 4. Verify Commit and Push Access

Create a small private test repository.

### macOS

```bash
mkdir -p ~/Documents/codex-github-test
cd ~/Documents/codex-github-test
printf '# Codex GitHub Test\n' > README.md
git init
git add README.md
git commit -m "Create Codex GitHub test"
gh repo create codex-github-test --private --source=. --push
```

### Windows PowerShell

```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\Documents\codex-github-test" -Force
Set-Location "$env:USERPROFILE\Documents\codex-github-test"
"# Codex GitHub Test" | Set-Content -Encoding UTF8 README.md
git init
git add README.md
git commit -m "Create Codex GitHub test"
gh repo create codex-github-test --private --source=. --push
```

Open the repository in your browser:

```bash
gh repo view codex-github-test --web
```

If the README is visible, GitHub authentication, commits, and pushes are working.

## 5. Optional Cleanup

Only run these commands after confirming that `codex-github-test` is the disposable test repository.

```bash
gh repo delete codex-github-test --yes
```

Remove the local folder.

**macOS**

```bash
rm -rf ~/Documents/codex-github-test
```

**Windows PowerShell**

```powershell
Remove-Item -LiteralPath "$env:USERPROFILE\Documents\codex-github-test" -Recurse -Force
```

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `gh` is not found | Install GitHub CLI, then reopen the terminal or Codex Desktop. |
| `gh auth status` says you are not logged in | Run `gh auth login --web --git-protocol https` again and complete browser authorization. |
| Git refuses to commit without an identity | Set `user.name` and `user.email` in step 3. |
| GitHub CLI login waits too long in Codex | Run the login command in an interactive Terminal or PowerShell window, finish browser authorization, then return to Codex. |
| Push is rejected | Run `gh auth status`, then check that the remote repository name and account are correct. |

## Security Notes

- Never paste access tokens, passwords, or SSH private keys into a repository or a chat.
- Use a private test repository unless you intend the content to be public.
- Before pushing real work, review `git status` and `git diff --staged`.
