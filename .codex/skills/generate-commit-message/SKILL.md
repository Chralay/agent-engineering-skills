---
name: generate-commit-message
description: Read git staged changes and generate Chinese commit messages. Use when Codex is asked to draft, review, or improve a git commit message from the staged index/diff, especially when the message must be in Chinese, include header and body, use scope for larger changes, preserve issue ids such as #713508014, or explain background for substantial changes.
---

# Generate Commit Message

## Overview

Generate a commit message from the git staging area only. Produce messages that follow Conventional Commits by default, use repo/team issue-id header conventions when requested or clearly established, and include Chinese content with at least a header and body.

## Workflow

1. Inspect staged changes with `git diff --cached --stat` and `git diff --cached`.
2. If no staged changes exist, state that the staging area is empty and stop. Do not generate a message from unstaged files unless the user explicitly asks.
3. Identify the commit type from intent:
   - `feat`: user-visible feature or new capability
   - `fix`: bug fix or incorrect behavior
   - `refactor`: internal restructure without behavior change
   - `perf`: performance improvement
   - `test`: tests only or test infrastructure
   - `docs`: documentation only
   - `style`: formatting, lint, naming, or non-behavioral style
   - `chore`: build, tooling, dependency, config, or maintenance
   - `ci`: CI/CD workflow changes
   - `revert`: revert a previous change
4. Detect issue ids provided by the user or clearly present in the staged change, such as `#713508014`. Preserve provided issue ids exactly. Do not invent an issue id.
5. Add a scope when the staged diff has a clear bounded area, especially for multi-file or larger changes. Prefer package, layer, component, feature, or subsystem names, for example `api`, `auth`, `router`, `service`, `db`, `docs`, `ci`.
6. Decide whether background is needed. Include a `Background:` section when the change is large, spans multiple areas, changes behavior or contracts, fixes a non-obvious issue, or depends on historical context. Omit background for small obvious edits.
7. Draft the final answer as a ready-to-use commit message. If uncertainty matters, provide a brief note after the message instead of weakening the message itself.

## Output Format

Always output a fenced `text` block containing the commit message.

Use this default structure:

```text
type(scope): 中文摘要

- 中文正文第一行，说明做了什么。
- 中文正文第二行，说明为什么这么做或影响是什么。

Background:
- 中文背景说明，仅在较大或上下文复杂的修改中包含。
```

When the user gives an issue id and the repo/team convention is `type#<issue-id>: <summary>`, prefer this header structure instead of adding a Conventional Commit scope:

```text
fix#713508014: 中文摘要

- 中文正文第一行，说明修复了什么。
- 中文正文第二行，说明为什么这么做或影响是什么。
```

Rules:

- Use English for commit type and structural labels, including `feat`, `fix`, optional `scope`, issue ids such as `#713508014`, and `Background:`.
- For bug fixes with a provided issue id, prefer the repo/team header convention `fix#<issue-id>: <summary>` when the user says that is their habit or asks to reflect the issue id in `fix`.
- Write the summary, body bullet text, and background bullet text in Chinese unless the codebase terminology is normally English.
- Keep the header concise, normally no more than 72 characters.
- Use imperative or result-oriented Chinese in the header, for example `fix(api): 修复文件上传校验失败` or `fix#713508014: 修复在线项目筛选参数缺失`.
- The body must be present and explain the meaningful behavior, implementation, or review impact.
- Do not add a `Body:` label by default; the unlabeled bullet section after the header is the commit body.
- Format every body line as a bullet starting with `- `. If the body has multiple points, use one bullet per line.
- When `Background:` is included, format each background line as a bullet starting with `- `.
- Do not mention files mechanically unless the file names are important to understand the change.
- Do not include unstaged changes, untracked files, or speculative future work.
- If the user asks for multiple options, provide 2-3 alternatives and mark the recommended one.

## Quality Checks

Before finalizing:

- Verify the header type matches the actual staged intent.
- Verify any user-provided issue id is preserved exactly in the header when the repo/team format calls for it, and verify no issue id is invented.
- Verify scope is present for larger or clearly localized changes.
- Verify the body adds information beyond repeating the header.
- Verify `Background:` appears when the staged change is broad, risky, or non-obvious.
- Verify no private secrets or sensitive values from the diff are copied into the message.
