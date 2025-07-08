---
title: "Markdown Guide"
date: 2025-07-08
tags: ["markdown", "guide", "writing"]
---

# Markdown Guide

Markdown is a lightweight markup language with plain-text formatting syntax. Its design allows it to be converted to many output formats, but the original tool by the same name only supports HTML.

## Basic Syntax

### Headings

```
# H1
## H2
### H3
#### H4
##### H5
###### H6
```

### Emphasis

```
*Italic text*
_Italic text_

**Bold text**
__Bold text__

~~Strikethrough~~
```

### Lists

```
1. Ordered list item 1
2. Ordered list item 2
3. Ordered list item 3

- Unordered list item
- Another item
  - Nested item
    - Deeper nested item
```

### Links and Images

```
[Link text](https://example.com)

![Alt text](image.png)

![Alt text](attachments/image.png)
```

### Code

```
`Inline code` with backticks

```python
# Code block with syntax highlighting
def hello_world():
    print("Hello, World!")
```
```

## Obsidian-Specific Syntax

KidiVerse also supports some Obsidian-specific markdown syntax:

```
![[image.png]] - Image transclusion
[[Note Title]] - Note link
```
