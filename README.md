# Agent Engineering Skills

一组面向 Codex 的可复用工程协作 Skills，用于代码路径调查、评审意见处理、前端技术规格编写和技术文章创作。

## Skills

| Skill | 用途 |
| --- | --- |
| [`feature-architecture-recon`](./feature-architecture-recon/) | 在设计、评审或实现前，基于代码证据梳理现有功能的入口、数据流、事实源、边界和风险。 |
| [`review-comment-handler`](./review-comment-handler/) | 可追踪地处理批量评审意见，先分类和讨论，再统一修改并输出最终处理清单。 |
| [`write-frontend-spec`](./write-frontend-spec/) | 将业务需求、当前代码和接口契约整理为可实现、可验收的前端功能技术 Spec。 |
| [`write-story-driven-tech-articles`](./write-story-driven-tech-articles/) | 使用问题驱动、渐进揭示和完整收束的方式编写或改写技术文章。 |

## 目录约定

每个 Skill 至少包含：

```text
<skill-name>/
├── SKILL.md
└── agents/
    └── openai.yaml
```

按需补充：

- `DESIGN.zh-CN.md`：面向使用者的中文设计说明。
- `references/`：执行 Skill 时按需读取的参考资料。
- `scripts/`：需要确定性执行的辅助脚本。
- `assets/`：产出时复用的模板或资源。

## 使用方式

克隆仓库：

```powershell
git clone ssh://git@ssh.github.com:443/Chralay/agent-engineering-skills.git
```

将需要的 Skill 复制或链接到 Codex 的个人 Skills 目录，或者目标项目的 `.codex/skills/` 目录。Windows 下可以使用目录联接共享同一份 Skill：

```powershell
New-Item -ItemType Junction `
  -Path '<project>\.codex\skills\<skill-name>' `
  -Target '<clone-path>\<skill-name>'
```

安装完成后，可在提示词中显式调用，例如：

```text
使用 $feature-architecture-recon 调查现有功能的数据流和事实源。
```

## 维护约定

- `SKILL.md` 的 YAML frontmatter 只保留 `name` 和 `description`。
- Skill 名称和目录名使用小写字母、数字及连字符。
- 中文文件使用 UTF-8 无 BOM。
- 文本文件统一使用 LF。
- 修改后使用 Codex `skill-creator` 提供的 `quick_validate.py` 校验对应 Skill。
- 不提交私钥、Token、会话记录、项目机密或仅适用于单台机器的绝对路径。

## License

[MIT](./LICENSE)
