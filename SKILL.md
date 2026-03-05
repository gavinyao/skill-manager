# Skill Manager

管理和安装 Claude Code skills 的工具。维护一份常用 skills 注册表，支持在新机器上交互式选择并安装所需 skills。

## 触发条件

当用户提到以下意图时激活：
- "列出可用 skills"、"有哪些 skills"
- "安装 skill"、"装一下 xxx skill"
- "管理 skills"

## 工作流程

### 1. 读取注册表

读取本技能目录下的 `registry.yaml`，解析所有已注册的 skills。

### 2. 展示可用列表

以带编号的表格形式展示所有可用 skills，方便用户通过编号快速选择：

```
| # | 名称 | 描述 | 来源 | 状态 |
|---|------|------|------|------|
| 1 | ...  | ...  | ...  | ...  |
| 2 | ...  | ...  | ...  | ...  |
```

编号从 1 开始，按 registry.yaml 中的顺序递增。同时检查 `~/.claude/skills/` 和当前项目 `.claude/skills/` 下是否已存在同名目录，标注已安装状态。

### 3. 用户选择

使用 AskUserQuestion 工具让用户选择要安装的 skill。用户可以通过编号（如 "1"）或名称指定。如果用户在触发时已经指定了名称或编号，跳过此步。

### 4. 选择安装位置

使用 AskUserQuestion 询问安装位置：
- **全局安装** (`~/.claude/skills/`) — 所有项目可用
- **项目安装** (`.claude/skills/`) — 仅当前项目可用

### 5. 执行安装

安装前检查该 skill 的 `depends` 字段。如果存在依赖且依赖的 skill 尚未安装，先按相同流程安装依赖的 skill（递归处理），全部依赖就绪后再安装目标 skill。

根据 registry.yaml 中的 `source` 类型执行安装：

- **npx**: 在目标 skills 目录下运行 install 命令
  ```bash
  cd <target_skills_dir> && <install_command>
  ```
- **git**: 克隆仓库到目标 skills 目录
  ```bash
  git clone <install_url> <target_skills_dir>/<skill_name>
  ```
- **local**: 复制本地目录到目标位置
  ```bash
  cp -r <source_path> <target_skills_dir>/<skill_name>
  ```

### 6. 确认结果

检查目标目录下是否存在安装后的 skill 目录，报告安装结果。

## 安装

将本目录复制或链接到 Claude Code skills 目录：

```bash
# 全局安装
cp -r skill-manager ~/.claude/skills/

# 或使用符号链接
ln -s $(pwd)/skill-manager ~/.claude/skills/skill-manager
```

## 自定义注册表

编辑 `registry.yaml` 添加你自己的 skills：

```yaml
skills:
  - name: my-skill
    description: 我的自定义 skill
    source: npx          # npx | git | local
    install: "npx skills add owner/repo@skill-name -y -g"
    depends: []           # 可选，依赖的其他 skill 名称
```

## License

MIT
