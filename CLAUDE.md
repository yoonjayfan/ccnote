# CCNOTE 项目指令

## 项目结构

```
ccnote/
├── .claude/commands/cn.md   # /cn 斜杠命令模板
├── notes/                   # 笔记存放目录
├── index.md                 # 笔记目录索引
├── home.html                # 浏览器笔记查看器
├── lib/marked.min.js        # marked.js 本地副本
└── CLAUDE.md                # 本文件
```

## 笔记格式规则

- 融合标注格式：`[YYYY-MM-DD]`，插入到语义合适位置
- 追加格式：`---` 分隔线 + `> YYYY-MM-DD` 日期行 + 内容
- 新建格式：`# 标题` + `> YYYY-MM-DD` 日期行 + 内容
- 永远不覆盖已有内容，只融合/追加/新建
- 文件名倾向中文（如 `Python学习.md`）

## index.md 格式

```markdown
## 大类名称

- [文件名](notes/文件名.md "简短说明")
```

## 记忆文件

路径：`~/.claude/projects/{project}/memory/last_note_document.md`
内容：上次写入的文档路径，用于下次保存时提示
