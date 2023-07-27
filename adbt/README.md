# 📄 ADBT 🪅

🐲 This file contains the specification and documentation for the `ADBT` - Adblock template files. ⚡

<br>

`ADBT` files are template files that provide a way of writing reusable, component-like based Adblock filter files.  
`ADBT` use plain text, are UTF-8 encoded and use LFs for line-breaks. Even if you add CRLFs, the compiler will auto-convert them to LFs and the output file will contain only LFs.  
`ADBT` templates contain header and filter rule files that will get compiled to a single filter list file.

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
- [Meta files](#-meta-files)
  - [Variables](#-variables)
- [Examples](#💡-examples)
- [Development](#-development)
- [Related](#-related)

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

<br>

❗Each `ADBT` template can be compiled to only 1 filter list file.

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

This is where _meta files_ come into play. Meta files are complementary files with an extension of `*.adbt.meta` that provide a way to have a dynamic header file that can be reused in all `ADBT` template files.

<br>

> ℹ️ Meta files are completely optional, use them only when needed.

<br>

Meta files have a special naming convention, if you fail to name them properly they won't be recognized by the compiler.
Meta files should be named after the `ADBT` template that you want to compile, see an example below.

<br>

`ADBT` template name: `my-filter.adbt`  
`ADBT` meta file name: `my-filter.adbt.meta`

<br>

In a nutshell, meta files' name should be:

> \<templateName\> + ".meta"

Meta files have a syntax of their own, even though you are already familiar with its base, JSON (if not, you can learn more [here](https://www.json.org/json-en.html)).

In its earliest stage, the current properties are supported:

- `title`,
- `description`,
- `versioning`

<br>

Each property in the `*.adbt.meta` file has a corresponding placeholder variable that you can use in your header files. Placeholders can have aliases.

#### `title`

The title of the filter list.

Placeholders: `$(title)`, `$(file)`

<br>

#### `description`

The description of the filter list.

Placeholders: `$(description)`, `$(about)`

<br>

Here's an example of how to transform a common header file to be a reusable, dynamic one:

<br>

`my-header.txt` (not reusable)

```adblock
[Adblock Plus 2.0]
! Title: AdVoid.Core
! Description: Will blocks trackers.
! Version: 1.0.5
```

If we were to include this header file via the [`header`](#header) statement in multiple `ADBT` templates all of the resulting files would be named `AdVoid.Core` which is wrong because each filter list should have a unique name and (optionally) description and version.

Now, let's use meta files to modify our header file:

`my-header.txt` (reusable!)

```adblock
[Adblock Plus 2.0]
! Title: $(title)
! Description: $(about)
```

Now, the header file has 2 dynamic properties, `title`, `description`. They pulled from your meta file when the template is compiled, thus, allowing you to provide custom values in the meta file for each template.

<br>

<br>

### 🔮 Variables

### 💡 Examples

#### Code in Actions

### 💻 Development

### 🧬 Related
