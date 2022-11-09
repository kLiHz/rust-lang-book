# 格式指南

## 正常行文

* 对于章 / 节标题中的英文, 倾向于使用标题大小写形式, 例如: `## Generating a Secret
  Number`, 而不是 `## Generating a secret number`.
* 在提出术语时, 倾向于使用手写体印刷字形, 而不是用单引号括起来, 例如: `是一个 ... 的
  *关联函数*`, 而不是 `是一个 ... 的 '关联函数'`.
* 在中文中提及函数时, **不要**把括号带上, 例如: `read_line` 而不是 `read_line()`.
* 每行超过 80 个半角字符宽度时手动换行.
* 最好不要在一个词中混合代码和非代码, 比如: ``还记得我们写 `use std::io`
  的情景吗?`` 而不是 ``还记得我们 `use`过 `std::io` 吗?``

## 代码部分

* 每当可行时, 在 Markdown 代码块之前加注文件名, 以明确所讨论的是哪个文件.
* When making changes to code, make it clear which parts of the code changed
  and which stayed the same... not sure how to do this yet
* 尽可能将过长的代码行拆成多行, 以使每行代码都在 80 字符之内.
* 对于命令行界面的输出的代码块, 使用 `bash` 语法高亮.

## Links

Once all the scripts are done:

* If a link shouldn't be printed, mark it to be ignored
  * This includes all "Chapter XX" intra-book links, which *should* be links
    for the HTML version
* Make intra-book links and stdlib API doc links relative so they work whether
  the book is read offline or on docs.rust-lang.org
* Use markdown links and keep in mind that they will be changed into `text at
  *url*` in print, so word them in a way that it reads well in that format
