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

<br>
<br>

### 📰 Parsing

Parsing and tokenization occur simultaneously in 1 step, line-by-line.
Parser will log its steps and progress (needs to enabled in the `Aria` compiler via a flag).

<br>

### 🧪 Syntax

<br>

#### ⚡ Statements

`header <path>`

> Imports a header file.

<br>

Adblock header files should contain the metadata that will be used for the resulting Adblock filter file.

> 💡 Metadata is composed of special comments that describe your filter list closely, like the filter list title, number of entries, version, modified data, author, etc.

<br>

Here's an example:

```adblock
[Adblock Plus 2.0]
! Title: AdVoid.Core
! Description: ✈ AdVoid is an efficient AdBlock filter that blocks ads, trackers, malwares and a lot more if you want it to! 👾
! Version: 1.8.1082
! Last modified: 2023-07-23T19:53:00+0200
! Expires: 6 hours (update frequency)
! Homepage: https://github.com/igorskyflyer/ad-void
! Entries: 2533
! Author: Igor Dimitrijević (@igorskyflyer)
```
