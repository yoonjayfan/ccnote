# CCNOTE 插件精简总结

> 2026-05-19

目的：在其他地方用 Claude Code 提示词编程重写本插件，保持功能相同。
```
本Prompt是第一版本,用于启动本项目开发,随着迭代进行,工程代码本身会比提示词更先进,想要更新Prompt,请自己让 Claude Code 替你总结
```

## 一句话概括

Claude Code 斜杠命令插件，输入 `/cn <内容>` 保存或查找 Markdown 笔记，核心能力是"智能融合"——理解文档结构后把内容插入语义最合适的位置。

## 需创建的文件

| 文件 | 作用 |
|------|------|
| `.claude/commands/cn.md` | 斜杠命令 prompt 模板（核心） |
| `CLAUDE.md` | 项目级指令，定义目录结构和格式规则 |
| `index.md` | 笔记目录，`## 大类` + `- [文件名](路径 "说明")` 格式，保存后自动更新 |
| `home.html` | 浏览器笔记查看器：左侧目录（渲染 index.md）+ 右侧内容，用 marked.js 渲染，侧边栏可拖拽调宽 |
| `lib/marked.min.js` | marked.js 本地副本（浏览器渲染用） |
| `.vscode/settings.json` | `markdown.preview.openMarkdownLinks: openFile` + `files.autoSave: afterDelay` |
| `~/.claude/projects/{project}/memory/last_note_document.md` | 记忆文件，追踪上次写入的文档路径 |

## 命令工作流（cn.md 核心逻辑）

1. **意图识别**：疑问词/查询动词/文件名暗示 → 查询意图，否则 → 保存意图，不明则询问
2. **查询意图**：Glob 搜 `notes/*.md` + 根目录 `*.md`，Grep 按关键词匹配，展示结果，只读
3. **保存意图**：
   - 读记忆文件获取 `LAST_DOC_PATH`
   - 询问保存位置：写入上次文档 / 创建新笔记 / 查找相关笔记
   - 若写入已有文档，询问：融合 / 追加
   - **融合**：读全文 → 理解结构 → 插入语义最合适位置 → 标注 `[YYYY-MM-DD]`
   - **追加**：整理格式 → `---` + 日期 + 内容追加到末尾
   - **新建**：询问标题 → 创建 `notes/{标题}.md`，首行 `# 标题` + 日期行 + 内容
4. **每次保存后**：更新记忆文件 + 更新 index.md 目录

## 格式规则

- 融合标注 `[YYYY-MM-DD]`，追加用 `---` + 日期行，新建用标题 + 日期行
- 融合时保持原文档风格和层级，追加前整理内容格式
- 永远不覆盖已有内容，只融合/追加/新建
- 文件名倾向中文（如 `Python学习.md`）
- index.md 按 `## 大类` 分组，链接用 title 属性放说明

## home.html 核心机制

- fetch `index.md` → marked.parse() 渲染到侧边栏
- 事件委托拦截侧边栏 `<a>` 点击 → fetch 笔记文件 → marked.parse() 渲染到右侧内容区
- 侧边栏拖拽调宽（mousedown/move/up，范围 180-500px）
- marked.js 引用本地 `lib/marked.min.js`
- 需 Live Server 扩展启动（不能直接双击打开）

## 前置依赖

Claude Code + Git 仓库 + VSCode Live Server 扩展
