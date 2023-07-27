# 📄 ADBT 🪅

🐲 This file contains the specification and documentation for the `ADBT` - Adblock template files. ⚡

<br>

`ADBT` files are template files that provide a way of writing reusable, component-like based Adblock filter files.  
`ADBT` use plain text, are UTF-8 encoded and use LFs for line-breaks. Even if you add CRLFs, the compiler will auto-convert them to LFs and the output file will contain only LFs.

---

## 📃 Table of Contents

- [File structure](#%EF%B8%8F-file-structure)
- [Parsing](#-parsing)
- [Syntax](#-syntax)
  - [Statements](#-statements)
    - [header](#header)
    - [include](#include)
    - [nl](#nl)
  - [Comments](#-comments)
    - [Internal](#internal)
    - [Exported](#exported)

<br>

---

## ⚙️ File structure

`ADBT` files follow an exact order and rules of their source code, described below:

- any line can be blank and all whitespace will be ignored when compiling
  - an explicit blank line can be used as well,
- any blank line can contain a comment (either internal or exported)
  - comments are not allowed to exist on the same line with statements,
- header files can be included multiple times
  - preferably, you should only have 1 header file and include it,
- filter files can be included multiple times,
- export statement **must** be the last statement of the file
  - only 1 export statement is allowed per template file

---

## 📰 Parsing

Parsing and tokenization occur simultaneously in 1 step, line-by-line.  
Parser will log its steps and progress (needs to enabled in the [`Aria` compiler](https://github.com/igorskyflyer/npm-adblock-aria-compiler/blob/main/README.md) via a flag).

---

## 🧪 Syntax

### ⚡ Statements

##### `header`

> Imports a header file.

<br>

Accepts: `path: string`

<br>

Example:

```shell
header './headers/my-header.txt'
```

<br>

Header files are plain text files and although not mandatory, they usually end with a `.txt` extension.

<br>

Adblock header files should contain the metadata that will be used for the resulting Adblock filter file.

> 💡 Metadata is composed of special comments that describe your filter list closely, like the filter list title, number of entries, version, modified data, author, etc.

<br>

Here's an example:

```adblock
[Adblock Plus 2.0]
! Title: AdVoid.Core
! Description: ✈ AdVoid is an efficient AdBlock filter that blocks ads, trackers, malware and a lot more if you want it to! 👾
! Version: 1.8.1082
! Last modified: 2023-07-23T19:53:00+0200
! Expires: 6 hours (update frequency)
! Homepage: https://github.com/igorskyflyer/ad-void
! Entries: 2533
! Author: Igor Dimitrijević (@igorskyflyer)
```

The `header` should be at the top of the `ADBT` template file; comments are allowed before it.

❗Path to the header file to include can be either relative or absolute but must be wrapped by single quotes. Failing to do so, will produce a fatal error.

<br>

#### `include`

> Imports an Adblock filter list file.

<br>

Accepts: `path: string`

<br>

Example:

```shell
include './rules/domains.txt'
include './rules/cosmetic.txt'
include './rules/query.txt'
```

<br>

Filter list files are plain text files and although not mandatory, they usually end with a `.txt` extension.

The filter list file should contain **only** filter rules, i.e.

```adblock
||somesite.abc^
||somesite.abc^
||somesite.abc^
||somesite.abc^
||somesite.abc^
##.someclass
##.someclass
! comment
##.someclass
##.someclass
##.someclass
```

It should **not** include any metadata - that should be included via the [`header`](#header) statement. Doing otherwise will result in metadata conflicts.
It can contain any valid filter rules and comments.

❗Path to the filter list file to include can be either relative or absolute but must be wrapped by single quotes. Failing to do so, will produce a fatal error.

<br>

#### `nl`

> Generates an explicit blank newline.

<br>

Accepts: N/A

<br>

Example:

```shell
nl
```

<br>

The newline will be present in the output filter file. Used to improve readability and/or organize your rules, see [examples](#-examples) below.

<br>

### 📢 Comments

`ADBT` files support two types of comments:

`Internal` &ndash; comments that are only visible in the template file but **are not** exported to the compiled file,  
`Exported` &ndash; comments that are visible in the template and **are** exported to the compiled file.

<br>

#### Internal

> Internal comments are prefixed by an `@` (at sign, asperand) and are line-based.

<br>

Example:

```shell
include './rules/domains.txt'
@ This is my internal comment
include './rules/cosmetic.txt'
@ This is my another internal comment
include './rules/query.txt'
```

<br>

#### Exported

> Exported comments are prefixed by an `#` (hash, pound, number sign) and are line-based.

<br>

Example:

```shell
include './rules/domains.txt'
# This will be present in the compiled file
include './rules/cosmetic.txt'
# This too!
include './rules/query.txt'
```

<br>

### 🔋 Meta files

`ADBT` template files can contain a header file - as mentioned [here](#header) but in real-life use case we usually want to have a single, reusable header file with its metadata.

Unfortunately, a common header file most certainly has a property that should be different for every filter list file we compile, in most cases, at least the `! Title: Filter name` should be different for every filter list file.
