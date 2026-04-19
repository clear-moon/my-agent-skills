---
name: github-local-upload
description: >-
  将本地项目或当前改动发布到 GitHub 的 main 分支：远程仓库已存在则直接关联并推送，不存在则使用 GitHub CLI（gh）创建仓库后再推送。适用于首次发布、继续上传当前项目、补传当前修改，或用户明确提到 gh repo create / push main / 上传到 GitHub main。
---

# GitHub 本地项目上传统一流程（gh）

## 技能目标

在用户需要把**当前项目**或**当前项目的改动**发布到 **GitHub 仓库的 `main` 分支**时，按本流程执行：

1. 确认本机已安装并登录 **GitHub CLI（`gh`）**；否则**停止并向用户说明**，不假装已推送成功。
2. 本地 Git 仓库以 **`main`** 作为默认主分支（必要时重命名或新建）。
3. 若远程**已存在**目标仓库：配置 `origin` 并推送。
4. 若远程**不存在**：使用 **`gh repo create`** 创建仓库，再关联远程并推送。
5. 默认以**直接发布到 `main`** 为目标；若用户明确要求更保守的流程，可在本技能内改走“临时分支 -> 合并 `main`”模式，但这不是默认路径。

## 何时使用

- 用户明确要「把当前项目上传到 GitHub」「发布到 GitHub」「推到 `main`」「新建仓库并 push」等。
- 用户要把**当前项目目录**、**当前项目修改**或**本地已有 Git 仓库**发布到 GitHub。
- 用户给出本地路径，且目标为 GitHub（可附带 `owner/repo` 或期望的仓库名）。

## 何时不使用

- 用户只要文档或命令草稿、不要实际执行。
- 目标不是 GitHub（例如 GitLab、仅本地 Git 操作）。
- 用户只想把某个目录同步进**另一个仓库的子目录**，而不是发布当前项目本身。

## 前置条件（必须先做）

### 1. 检测 `gh` 是否可用

在 **Windows** 上优先用 PowerShell，例如：

```powershell
Get-Command gh -ErrorAction SilentlyContinue
gh --version
```

若命令不存在或报错：**不要继续推送**。向用户说明需安装 GitHub CLI，并给出官方入口：<https://cli.github.com/>  
常见安装方式（择一说明即可）：`winget install GitHub.cli`、官网安装包、包管理器文档中的方式。

### 2. 检测是否已登录 GitHub

```powershell
gh auth status
```

若未登录或 token 无效：**不要继续创建仓库或推送**。提示用户在本机执行：

```powershell
gh auth login
```

并按提示完成浏览器或 token 登录；必要时说明需 `repo` 等权限以创建仓库与推送。

## 输入要求

向用户确认或推断：

- **本地项目路径** `local_path`（必选）。
- **目标仓库**：`owner/repo` 或完整 `https://github.com/owner/repo.git`；若未给出，应用 `gh api user --jq .login` 等方式取当前用户后询问默认命名，或让用户指定仓库名。
- **可见性**：`public` / `private`（创建新仓库时必须明确或采用用户指定默认值）。
- **上传范围**：是发布整个当前项目，还是仅提交当前已修改内容；若用户未特别说明，按当前工作区实际改动和 Git 状态执行。

## 标准执行步骤

以下在 `local_path` 下执行（先 `cd` 到该目录）。**推送前**若存在未提交改动，按用户意图 `add`/`commit`；若工作区干净且已有提交则直接推送。

### 1. 初始化 Git（若尚无仓库）

若不存在 `.git`：

```powershell
git init
```

### 2. 统一主分支为 `main`

```powershell
git branch -M main
```

若当前无提交，该命令在部分 Git 版本下仍可在首次提交后确保默认分支名为 `main`；若已有其他默认分支名，以 `main` 为准重命名。

### 3. 检查当前项目状态

至少检查：

```powershell
git status --short --branch
git remote -v
```

需要根据结果判断：

- 当前是否已经是 Git 仓库。
- 当前分支是否为 `main`。
- 是否已有未提交改动。
- 是否已经配置 `origin`，以及 URL 是否与目标仓库一致。

若存在与你本次要上传内容无关的改动，不要擅自提交、覆盖或回退；应暂停并先询问用户。

### 4. 判断远程仓库是否已存在

对目标 `owner/repo` 执行：

```powershell
gh repo view owner/repo
```

- **退出码为 0**：仓库已存在 → 进入「关联远程并推送」。
- **报错 / 404**：仓库不存在 → 进入「创建仓库」。

（也可用 `gh api repos/owner/repo` 判断，以 `gh` 官方推荐方式为准。）

### 5. 处理待上传改动

若工作区存在未提交改动，先按用户意图处理：

- 若用户要上传**当前项目全部最新状态**：可暂存并提交相关改动后推送。
- 若用户只要上传**当前已经修改的部分**：只 `git add` 相关文件，不要把不相关改动一起提交。
- 若工作区干净且已有目标提交：可直接推送，不必强行新建提交。

提交前建议至少查看：

```powershell
git diff
git diff --cached
```

提交信息应说明**为什么要上传这批修改**，避免只写机械的 `update files`。

### 6. 仓库不存在：用 `gh` 创建

在**项目根目录**（已 `git init` 且当前分支为 `main`）典型做法：

```powershell
# 示例：在当前目录创建同名远程仓库并推送（按用户选的 public/private 加开关）
gh repo create owner/repo --public --source=. --remote=origin --push
```

或分步：先 `gh repo create ... --source=. --remote=origin`（不 push），再 `git push -u origin main`。  
具体子命令以用户选择的可见性与是否组织仓库为准（`--public` / `--private`，组织下可能需 `--confirm` 等）。

**禁止**默认使用 `--force` 或破坏性覆盖；若推送被拒绝，说明原因（分支保护、非空远程历史冲突等）并暂停。

### 7. 仓库已存在：关联 `origin` 并推送

若尚无 `origin`：

```powershell
git remote add origin https://github.com/owner/repo.git
```

若已有 `origin` 但 URL 错误：

```powershell
git remote set-url origin https://github.com/owner/repo.git
```

然后：

```powershell
git push -u origin main
```

若远程已有初始提交（如 README）导致非快进，**不要擅自强推**；向用户说明可选方案（pull 合并、`--allow-unrelated-histories` 等）由用户决定。

### 8. 可选保守模式：临时分支后再合并 `main`

仅当用户明确要求“不要直接推 `main`”或希望走更保守流程时，才在本技能内启用该模式：

1. 新建临时分支。
2. 在临时分支提交并推送。
3. 切回 `main`，拉取远程最新 `main`。
4. 合并临时分支后再推送 `main`。
5. 只有在确认远程 `main` 已包含改动后，才删除临时分支。

若合并冲突或远程分支保护阻止直接推送 `main`，应停止自动流程并询问用户。

## 输出与验证

完成后向用户汇报：

- 本地路径、远程仓库 URL、`main` 是否已推送成功。
- 若新建仓库：可见性与仓库名。
- 若走了保守模式：说明临时分支名、合并状态、分支清理状态。
- 建议用户在浏览器打开 GitHub 仓库页确认文件与默认分支为 `main`。

## 风险与暂停条件

- `gh` 未安装或未登录：**仅提示，不继续**。
- 工作区含疑似密钥、`.env`、凭证：提醒并勿提交；必要时帮用户检查 `.gitignore`。
- 权限不足、2FA、SSO、组织策略导致 `gh repo create` 或 `push` 失败：如实转述错误信息并给出下一步（权限申请、`gh auth refresh` 等）。
- 远程非空历史、默认分支不是 `main`、或受保护策略阻止写入：如实说明现状，不要盲推或强推。

## 示例提示词

**示例 1**

请使用 `github-local-upload` 技能，把当前项目上传到 GitHub 的 `main`。如果仓库不存在就先创建，再上传。

**示例 2**

请使用 `github-local-upload` 技能，把 `C:\Users\me\Desktop\demo-skill` 发布到 `me/demo-skill` 的 `main`，已有仓库就直接 push，没有就用 `gh repo create` 建好再 push。

**示例 3**

请使用 `github-local-upload` 技能，把我当前项目的修改上传到 GitHub；默认直接进 `main`，但如果你判断直接推送风险较高，可以先停下来问我。
