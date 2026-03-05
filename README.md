# Skill Manager

一个 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill，用于管理和安装其他 Claude Code skills。

维护一份常用 skills 注册表（`registry.yaml`），支持交互式选择、依赖解析和一键安装。

## 功能

- 列出所有已注册的 skills 及其安装状态
- 支持通过编号或名称选择安装
- 支持全局安装和项目级安装
- 自动解析和安装依赖的 skills
- 支持三种安装来源：`npx`、`git`、`local`

## 安装

```bash
# 全局安装（推荐）
cp -r . ~/.claude/skills/skill-manager

# 或使用符号链接
ln -s $(pwd) ~/.claude/skills/skill-manager
```

## 使用

在 Claude Code 中直接说：

- "列出可用 skills"
- "安装 skill-creator"
- "管理 skills"

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

### 来源类型

| 类型 | 说明 | install 字段 |
|------|------|-------------|
| `npx` | 通过 npx 运行安装命令 | npx 命令 |
| `git` | 克隆 git 仓库 | 仓库 URL |
| `local` | 复制本地目录 | 本地路径 |

## License

[MIT](LICENSE)
