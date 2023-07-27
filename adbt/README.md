## 📄 ADBT 🪅

🐲 This file contains the specification and documentation for the `ADBT` - Adblock template files. ⚡

<br>

`ADBT` files are template files that provide a way of writing reusable, component-like based Adblock filter files.
`ADBT` use plain text, are UTF-8 encoded and use LF for line-breaks.

<br>

### 📃 Table of Contents

- [File structure](#%EF%B8%8F-file-structure)

<br>

### ⚙️ File structure

`ADBT` files follow an exact order and rules of their source code, described below:

- any line can be blank and all whitespace will be ignored when compiling
  - an explicit blank line can be used as well,
- any blank line can contain a comment (either internal or exported)
  - comments are not allowed to exist on the same line with statements,
- header files can be included multiple times
  - preferably, you should only have 1 header file and include it,
- filter files can be included multiple times,
- export statement **must** be the last statement of the file
  - only 1 export statement is allowed per template file.
