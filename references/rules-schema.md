# .tidy-rules.yaml 规则格式参考

## 完整结构

```yaml
targets:        # 规则列表，按 priority 降序匹配
  - pattern: ""   # 必填，文件名匹配模式
    target: ""    # move 时必填，目标子目录
    priority: 0   # 可选，默认 0，越大越优先
    action: ""    # 可选，move(默认) / delete / ignore
    reason: ""    # delete 时建议填写，说明原因
options:        # 可选，扫描配置
  max_depth: 3  # 最大扫描深度，默认 3
  exclude: []   # 跳过目录列表
```

## pattern 语法

| 写法 | 含义 |
|------|------|
| `*.py` | 所有 .py 文件 |
| `movie_*` | movie_ 开头的文件 |
| `*.tmp\|*.bak` | .tmp 或 .bak 文件 |
| `.*` | 所有隐藏文件 |
| `report-202[0-9].xlsx` | 不推荐，通配符不是正则 |

pattern 使用 PowerShell 通配符（`*` `?`），管道符 `|` 分隔多个模式。

## action 说明

| action | 行为 |
|--------|------|
| `move` | 移动到 target 目录（默认） |
| `delete` | 标记建议删除，需用户二次确认 |
| `ignore` | 跳过不处理 |

## 示例

### Python 项目

```yaml
targets:
  - pattern: "*.py"
    target: "src/"
    priority: 10
  - pattern: "*.md"
    target: "docs/"
  - pattern: "*.pyc|__pycache__|*.egg-info"
    action: "delete"
    reason: "编译缓存"
  - pattern: ".*"
    action: "ignore"
options:
  exclude:
    - ".git"
    - ".venv"
    - "node_modules"
```

### Java 项目

```yaml
targets:
  - pattern: "*.java"
    target: "src/main/java/"
    priority: 10
  - pattern: "*.xml"
    target: "src/main/resources/"
  - pattern: "*.class|target/"
    action: "delete"
    reason: "编译产物"
```
