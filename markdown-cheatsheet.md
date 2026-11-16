# Markdown Cheatsheet (Markdown 速查表)

> A quick reference guide for writing clean technical documentation in Markdown.
> (写技术文档时随手查阅的 Markdown 速查表)

---

## 1. Headings (标题)

Use `#` for titles. More `#` symbols mean smaller heading levels.
(用 `#` 表示标题级别，`#` 越多级别越低；`#` 后必须有一个空格)

```markdown
# H1 - Document Main Title   (文档主标题)
## H2 - Chapter              (章节)
### H3 - Sub-section         (小节)
#### H4 - Sub-sub-section    (子小节)
```

---

## 2. Emphasis (强调)

| Style (样式) | Syntax (写法) | Result (效果) |
|---|---|---|
| Italic (斜体) | `*text*` | *text* |
| Bold (粗体) | `**text**` | **text** |
| Bold + Italic (粗斜体) | `***text***` | ***text*** |
| Strikethrough (删除线) | `~~text~~` | ~~text~~ |

---

## 3. Lists (列表)

### Unordered Lists (无序列表)

Use `-`, `*` or `+`. Add a space after the symbol. Indent 2 spaces to nest.
(用 `-` / `*` / `+`，符号后要有空格；缩进 2 个空格实现嵌套)

```markdown
- Item 1
- Item 2
  - Nested Item 2.1
  - Nested Item 2.2
```

### Ordered Lists (有序列表)

Use a number + period. Markdown renumbers automatically.
(数字 + 点；渲染时会自动重排，顺序写错也能修正)

```markdown
1. First step
2. Second step
3. Third step
```

---

## 4. Code (代码) — the most important part (最重要)

### Inline Code (行内代码)

Use single backticks inside a sentence. (句中提命令用单个反引号)

````markdown
Run `git add .` to stage files. Use `netstat -ano` to check ports.
````

### Code Blocks (代码块) — must be closed! (必须闭合)

Three backticks open a block, three backticks close it. Add the language name for highlighting.
(三个反引号成对出现：开头写语言名高亮，结尾必须再写三个反引号。漏写结尾 = 后面所有内容被吞)

````markdown
```python
print("Hello, World!")
```

```powershell
ls
```
````

> **Tip (提示):** If you need to *show* a code block inside your own code block (like this cheatsheet does), wrap the outer block with **four** backticks.
> (要展示"代码块怎么写"时，外层用四个反引号包裹，内层用三个，就不会互相打架。)

---

## 5. Links (链接)

Format: `[Display Text](URL)` (格式: [显示文本](链接地址))

```markdown
[My GitHub Repository](https://github.com/username/repo)
```

---

## 6. Images (图片)

Add a `!` before a link. Alt Text is the image description.
(链接前加感叹号，方括号内是图片描述文字)

```markdown
![Ollama Logo](https://example.com/logo.png)
```

---

## 7. Tables (表格)

Use `|` to separate columns and `-` for the header row.
(用 `|` 分隔列，第二行用 `-` 标注表头)

```markdown
| Command | Description |
|---------|-------------|
| `git add .` | Stage all changes (暂存所有文件) |
| `git commit -m "msg"` | Commit changes (提交更改) |
| `git push -u origin master` | Push to remote and set upstream (推送并建立追踪) |
```

---

## 8. Blockquotes (引用)

Use `>` to highlight notes or important information. (用 `>` 突出注意事项)

```markdown
> **Note:** Always double-check your remote repository settings before pushing.
> (推送前请再次检查远程仓库配置)
```

---

## 9. Common Pitfalls (常见坑)

- A code block opened with three backticks must be closed; an unclosed block swallows all following text. (代码块必须闭合，否则后续内容被吞)
- Always put a space after `#`, `-`, `>`, and `1.` etc. (这些符号后面要加空格)
- Leave a blank line between paragraphs and around headings/lists. (段落之间、标题和列表前后各留一个空行)
- To *display* code fences inside a document, wrap the outer fence with four backticks. (文档内演示围栏时，外层用四反引号)
