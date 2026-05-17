---
name: file-tidy
description: 文件系统整理师——按规则归类散乱文件、识别可删除的垃圾文件，支持干跑模式和自定义 YAML 规则。独立使用或被 weaver-自我迭代 调用。
---

# 文件系统整理师 — File Tidy

智能整理杂乱目录。按规则归类文件，标记可删除的临时/垃圾文件，输出直观的 before/after 树。

## 触发词

`整理文件` / `整理目录` / `文件归类` / `tidy` / `cleanup`

## 何时使用

- 项目根目录散落各种 .py .md .json 文件
- 想把临时文件、备份文件找出来
- weaver 全局整理时自动调用

## 工作流程

### 第一步：确定目标目录和模式

1. 如果用户指定了目录，使用该目录；否则使用当前工作目录
2. 默认干跑模式（`--dry-run` 或用户说"先看看"/"预览"）。用户说"执行"/"确认"/"动手"才进入执行模式
3. 检查目标目录下是否存在 `.tidy-rules.yaml`。存在则加载，不存在则使用内置默认规则

### 第二步：扫描目录

使用 PowerShell 命令扫描：

```powershell
# 列出目标目录下所有文件（含子目录，受 max_depth 限制）
Get-ChildItem -Path <dir> -Recurse -Depth <max_depth> -File |
    Where-Object { $_.DirectoryName -notmatch '<exclude patterns>' } |
    Select-Object FullName, Name, Extension, Length, LastWriteTime
```

1. 跳过 `options.exclude` 中列出的目录
2. 跳过 `action: ignore` 匹配的文件（如 `.*` 隐藏文件）

### 第三步：匹配规则

对每个文件，按 `priority` 降序匹配 `targets`：

1. `pattern` 支持通配符（`*` `?`）和管道分隔（`|`），匹配文件名
2. 第一个匹配到的规则生效（高 priority 优先）
3. action 为 `move`：文件将被移动到 `target` 目录
4. action 为 `delete`：文件被标记建议删除
5. action 为 `ignore`：文件被跳过
6. 未匹配任何规则的文件列入"跳过"清单

### 第四步：展示 before/after 树

按以下格式输出。左边原路径（相对于目标目录），右边新路径。跳过项和错误项单独列出。

```
整理计划 — <目标目录绝对路径>

  整理前                    整理后

  <file1>                  <target_dir>/
  <file2>                    <file1>
  <subdir>/                  <file2>
    <file3>                  <subdir>/
                             <file3> [建议删除: <reason>]

已处理 X 项，建议删除 Y 项，跳过 Z 项。
执行：整理文件 --confirm
```

**格式规则：**
- 左边按文件路径缩进，右边按归类后的目标目录缩进
- 被标记建议删除的文件在右边用 `[建议删除: reason]` 标注
- 未匹配规则的文件不出现（列在跳过清单中）
- 如果两边都无文件，输出"目录已整洁，无需整理"

### 第五步：用户确认（独立使用时）

展示计划后询问：**"执行整理 / 只看归类 / 跳过删除 / 取消？"**

- 执行整理：执行所有 move 操作，跳过 delete（delete 需二次确认）
- 只看归类：只执行 move，不碰 delete 标记项
- 跳过删除：只执行 move
- 取消：不执行任何操作

### 第六步：执行

```powershell
# 创建目标目录
New-Item -ItemType Directory -Force -Path "<target>"

# 移动文件（不覆盖已存在的同名文件）
if (-not (Test-Path "<target>/<filename>")) {
    Move-Item -Path "<source>" -Destination "<target>/<filename>"
    Write-Output "OK  <source>  →  <target>/<filename>"
} else {
    Write-Output "SKIP  <source>  →  <target>/<filename>  已存在"
}
```

执行完成后输出摘要：

```
整理完成 — 移动 X 项，跳过 Y 项（冲突），建议删除 Z 项（未执行）
```

### 被 weaver 调用时

weaver 会在第三步"周边整理"后调用 file-tidy：
1. weaver 以干跑模式启动 file-tidy（扫描目录，展示 before/after 树）
2. 结果嵌入 weaver 第五步变更摘要的"文件系统整理"段落
3. 用户在 weaver 摘要中确认后，weaver 再次调用 file-tidy 执行

## 内置默认规则

当项目根目录没有 `.tidy-rules.yaml` 时使用：

```yaml
targets:
  - pattern: "*.py"
    target: "scripts/"
    priority: 10
  - pattern: "*.md"
    target: "docs/"
    priority: 10
  - pattern: "*.json|*.yaml|*.yml|*.toml"
    target: "config/"
    priority: 10
  - pattern: "*.bat|*.ps1|*.sh"
    target: "scripts/"
    priority: 10
  - pattern: "*.tmp|*.bak|*~|*.swp"
    action: "delete"
    reason: "编辑器备份或临时文件"
  - pattern: "*.pyc|__pycache__|*.class"
    action: "delete"
    reason: "编译产物，可从源码重新生成"
  - pattern: ".*"
    action: "ignore"

options:
  max_depth: 3
  exclude:
    - ".git"
    - "node_modules"
    - ".claude"
    - ".obsidian"
```

## 安全规则

- 绝不删除文件，只标记建议删除（需用户二次确认）
- 目标已有同名文件时跳过，报告冲突，不覆盖
- 默认忽略隐藏文件（`.*`）
- 不碰排除目录（`.git` `.claude` `node_modules`）
- 如果目标目录不存在则自动创建

## 规则文件格式

详见 `references/rules-schema.md`。
