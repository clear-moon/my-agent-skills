# npx skills 命令参考

## 核心命令

| 命令 | 别名 | 用途 |
| --- | --- | --- |
| `npx skills add <package>` | `a`, `i`, `install` | 安装 skill 来源 |
| `npx skills remove [skills...]` | `rm`, `r` | 卸载已安装的 skills |
| `npx skills list` | `ls` | 列出已安装的 skills |
| `npx skills find [query]` | `search`, `f`, `s` | 搜索 skills |
| `npx skills check` | 无 | 检查是否有更新 |
| `npx skills update` | `upgrade` | 更新已安装的 skills |
| `npx skills init [name]` | 无 | 新建 `SKILL.md` 模板 |
| `npx skills experimental_install` | 无 | 从 skill lock 状态恢复 |
| `npx skills experimental_sync` | 无 | 从 `node_modules` 同步 |
| `npx skills --help` | `-h` | 显示帮助 |
| `npx skills --version` | `-v` | 显示版本 |

## `add`

用于从 GitHub 简写、仓库 URL、git URL、直接 skill 路径或本地路径安装。

常用参数：

- `-g, --global`：全局安装
- `-a, --agent <agents...>`：指定目标 agent
- `-s, --skill <skills...>`：只安装指定 skills
- `-l, --list`：列出可用 skills，不安装
- `--copy`：复制而非 symlink
- `-y, --yes`：跳过交互确认
- `--all`：为所有 agent 安装全部 skills
- `--full-depth`：即使根目录已有 `SKILL.md`，仍搜索所有子目录

示例：

```bash
npx skills add vercel-labs/agent-skills
npx skills add vercel-labs/agent-skills --list
npx skills add vercel-labs/agent-skills -a codex
npx skills add vercel-labs/agent-skills --skill frontend-design
npx skills add vercel-labs/agent-skills --all
```

## `remove`

用于卸载一个、多个或全部已安装的 skills。

常用参数：

- `-g, --global`：从 global scope 移除
- `-a, --agent <agents>`：仅从指定 agent 移除
- `-s, --skill <skills>`：显式指定 skill 名
- `-y, --yes`：跳过交互确认
- `--all`：从所有 agent 移除全部 skills

示例：

```bash
npx skills remove
npx skills remove my-skill
npx skills rm my-skill
npx skills remove --global my-skill
npx skills remove --skill '*' -a cursor
```

## `list`

用于展示已安装的 skills。

常用参数：

- `-g, --global`：显示全局 skills
- `-a, --agent <agents>`：按 agent 过滤
- `--json`：机器可读输出

示例：

```bash
npx skills list
npx skills ls -g
npx skills ls -a codex
npx skills ls --json
```

## `find`

用于发现 skills 或关键词搜索。

示例：

```bash
npx skills find
npx skills find typescript
npx skills search typescript
```

## `check` 与 `update`

- 用 `check` 查看是否有可用更新。
- 用 `update` 应用所有可用更新。

示例：

```bash
npx skills check
npx skills update
npx skills upgrade
```

## `init`

用于在当前目录或指定子目录中创建新的 skill 模板。

示例：

```bash
npx skills init
npx skills init my-skill
```

## Experimental 命令

仅在用户明确需要这些工作流时使用。

### `experimental_install`

从 lock file（`skills-lock.json`）恢复 skills。适用于迁移到新机器或恢复先前状态。

**Lock file 位置：**

- Global：`~/.agents/.skill-lock.json`
- Project：`<project>/.agents/skills-lock.json`

**常用参数：**

- `-g, --global`：恢复全局 skills
- `-y, --yes`：跳过确认提示

**示例：**

```bash
# 恢复项目级 skills（读取当前目录下的 lock file）
npx skills experimental_install

# 恢复全局 skills
npx skills experimental_install -g

# 无提示恢复
npx skills experimental_install -g -y
```

**迁移流程示例：**

```bash
# 旧机器：备份 lock file
cp ~/.agents/.skill-lock.json ~/backup/

# 新机器：从 lock file 恢复
cp ~/backup/.skill-lock.json ~/.agents/
npx skills experimental_install -g -y
```

### `experimental_sync`

从 `node_modules` 同步 skills 并创建 symlink。适用于 skills 已在 node_modules 中但 symlink 缺失的情况。

**示例：**

```bash
npx skills experimental_sync
npx skills experimental_sync -a codex -y
```

## Scope 说明

- 默认是 project scope。
- global scope 通常需要加 `-g`。
- 针对特定 agent 的操作通常加 `-a <agent>`。
- 针对特定 skill 的操作通常加 `-s <skill>`。
- 整仓安装时可省略 `-s`。
