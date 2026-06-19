# How to Use Denote


# How to Use Denote

## About

These are casual notes on how to use Denote. Most of the content is translated or adapted directly from the official documentation.

## References

[Denote documentation](https://protesilaos.com/emacs/denote)

## Creating Standard Notes

Commands that prompt the user for input when creating notes include, but are not limited to:

-   `denote`
-   `denote-signature`
-   `denote-type`
-   `denote-date`
-   `denote-subdirectory`
-   `denote-rename-file`
-   `denote-dired-rename-files`

### The `denote-templates` option

Users can start a new note from a template.

A template can be configured like this:

elisp

```swift
(setq denote-templates
      '((report . "* Some heading

* Another heading")
        (memo . "* Some heading

* Another heading)))
```

A better approach is to use `concat` to join strings together:

emacs-lisp

```swift
(setq denote-templates
      `((report . "* Some heading

* Another heading")
        (memo . ,(concat "* Some heading"
                         "

"
                         "* Another heading"
                         "

"))))
```

### Maintaining separate note-directory silos

The idea of `silos` means that notes are stored in different places, and those directories are separated from one another. We call these directories `silos`.

To create a `silo`, first create a `.dir-locals.el` file inside the note directory. When creating it, Emacs will ask whether to store this path variable; choose yes.

Write the following into `.dir-locals.el`:

emacs-lisp

```wasm
;;; Directory Local Variables.  For more information evaluate:
;;;
;;;     (info "(emacs) Directory Variables")

((nil . ((denote-directory . "/path/to/silo/")
         (denote-infer-keywords . nil))))
```

Here is my notes directory (`01Index` is my `denote-directory`):

bash

```lua
./01Index/
├── .DS_Store
├── 20240228T160157--mastering-eshell__emacs_linux_shell.assets
├── 20240228T160157--mastering-eshell__emacs_linux_shell.org
├── 20240315T225352--c语言程序设计现代方法__c.org
├── 20240317T201104--cpp-primer__cpp.org
├── 20240407T230222--西方哲学史__reading.org
└── ltximg
./02Area/
├── .dir-locals.el
├── 20240201T021341--emacs-manual-note__emacs.assets
├── 20240201T021341--emacs-manual-note__emacs.org
├── 20240302T153745--计算机网络原理__network.assets
├── 20240302T153745--计算机网络原理__network.org
├── 20240320T175109--操作系统__operatingsystem.assets
├── 20240320T175109--操作系统__operatingsystem.org
├── 20240328T141613--计算机系统结构__computerarchitecture.assets
└── 20240328T141613--计算机系统结构__computerarchitecture.org
./03Resource/
├── .dir-locals.el
├── 20240221T162109--kde-plasma-桌面-korganizer-等软件关闭后仍然占用约-800-m-内存__bug_kde.assets
├── 20240221T162109--kde-plasma-桌面-korganizer-等软件关闭后仍然占用约-800-m-内存__bug_kde.org
└── 20240222T000349--opensuse-tumbleweed-和-kde-的个人安装过程__kde_linux_opensuse.org
./04Project/
├── .dir-locals.el
└── 20240417T220159--denote-使用方法__emacs.org
```

After that, we still need a custom user command that selects the `silo` automatically. In normal use, `denote` will put notes into `01Index` by default. When we switch to `02Area`, Denote-related commands will automatically store notes in the current `silo`.

My user configuration is as follows:

emacs-lisp

```perl
(defvar my-denote-silo-directories
    `("/home/MEGA/02Area"
      "/home/MEGA/03Resource"
      "/home/MEGA/04Project"
      ;; You don't actually need to include the `denote-directory' here
      ;; if you use the regular commands in their global context.  I am
      ;; including it for completeness.
      ,denote-directory)
    "List of file paths pointing to my Denote silos.
  This is a list of strings.")

  (defvar my-denote-commands-for-silos
    '(denote
      denote-date
      denote-subdirectory
      denote-template
      denote-type)
    "List of Denote commands to call after selecting a silo.
  This is a list of symbols that specify the note-creating
  interactive functions that Denote provides.")

  (defun my-denote-pick-silo-then-command (silo command)
    "Select SILO and run Denote COMMAND in it.
  SILO is a file path from `my-denote-silo-directories', while
  COMMAND is one among `my-denote-commands-for-silos'."
    (interactive
     (list (completing-read "Select a silo: " my-denote-silo-directories nil t)
           (intern (completing-read
                    "Run command in silo: "
                    my-denote-commands-for-silos nil t))))
    (let ((denote-directory silo))
      (call-interactively command)))
```

The implementation of silos is not especially elegant. It is a relatively new feature, so for now we can only make do with it. I think the author should probably integrate this distributed-repository concept into the configuration around `denote-directory`.

After all, I cannot always put all notes in one folder, and always using `denote-subdirectory` is not a great solution either.

## Note File Naming Convention

Notes are stored in `denote-directory`, which defaults to `~/Documents/notes`.

By default, Denote follows this naming convention:

text

```ini
DATE==SIGNATURE--TITLE__KEYWORDS.EXTENSION
```

The `DATE` field represents the date in year-month-day format, followed by an uppercase T for the current time. `DATE` also serves as the unique identifier of each note file.

The `SIGNATURE` field contains an alphanumeric string. A signature has no predefined purpose; it is defined by the user. One possible use is to establish ordering relationships between files, such as 1, 1a, 1b, 1b1, 1b2, and so on.

The `TITLE` field is lowercase by default and uses hyphens.

The `KEYWORDS` field consists of one or more entries separated by underscores; when entering `KEYWORDS`, use commas as separators.

Examples:

text

```css
20220610T043241--initial-thoughts-on-the-zettelkasten-method__notetaking.org
20220610T062201--define-custom-org-hyperlink-type__denote_emacs_package.md
20220610T162327--on-hierarchy-and-taxis__notetaking_philosophy.txt
```

`denote-prompts` can be configured in the following forms:

text

```sql
DATE.EXT
DATE--TITLE.EXT
DATE__KEYWORDS.EXT
DATE==SIGNATURE.EXT
DATE==SIGNATURE--TITLE.EXT
DATE==SIGNATURE--TITLE__KEYWORDS.EXT
DATE==SIGNATURE__KEYWORDS.EXT
```

## Linking Notes

`denote` provides several commands for creating links between notes.

All linked files must be Denote files; other files cannot be recognized.

### Adding a single link

The `denote-link` command selects a file in the minibuffer and inserts it into the current file as a link. The link format depends on whether the current file is Org or Markdown.

In Org files, links to headings are also supported through the `denote-org-store-link-to-heading` command. Other file formats are not supported.

When `denote-link` is called with a prefix argument (`C-u` by default), it sets the link to the `[[denote:IDENTIFIER]]` format regardless of file type.

By default, the link description is determined as follows:

### The `denote-org-store-link-to-heading` user option

The option `denote-org-store-link-to-heading` determines whether `org-store-link` links to the current heading in an Org file.

`org-store` stores the current link so it can later be inserted with `org-insert-link`, and the `denote-org-store-link-to-heading` option affects this command.

When this option is non-`nil`, `org-store-link` stores the link to the current heading instead of the Denote file itself.

If the heading does not have a `CUSTOM_ID`, Denote creates a `PROPERTIES` drawer and includes it in the heading. If a `CUSTOM_ID` already exists, `org-store-link` uses it as is.

### Inserting a link to a heading inside an Org file

As part of the `denote-org-extras.el` extension, `denote-org-extras-link-to-heading` provides links to headings inside Org files.

The link takes this form:

text

```lua
[[denote:IDENTIFIER::#ORG-HEADING-CUSTOM-ID]][Description::Heading text]]
```

### Inserting links that match a regular expression

The command `denote-add-links` adds multiple links that match a regular expression. These links are inserted as a list, for example:

text

```diff
- link1
- link2
- link3
```

Examples of usable regular expressions:

-   `journal` matches all notes whose title contains `journal`.
-   `_journal` matches all notes whose keyword is `journal`.
-   `^2022.*_journal` matches all notes that start with `2022` and contain the keyword `journal`.
-   `\.txt` matches files with the `txt` extension.

### Linking to an existing note or creating a new one

In a note-taking workflow, you may be developing one topic and want to connect it to another. Denote provides several convenient commands:

-   `denote-link-after-creating`
    
    Create a new note in the background and link to it directly.
    
-   `denote-link-after-creating-with-command`
    
    This command is similar to the previous one, but it prompts for the command used to create the note.
    
-   `denote-link-or-create`
    
    Use `denote-link` on the target file, creating it if necessary.
    

### The backlinks buffer

`denote-backlinks` generates a custom buffer that shows backlinks to the current note. A backlink is a link that points back to the current entry.

By default, the backlinks buffer is designed to show the names of files that contain annotations linked to the current entry. Each file name is displayed on its own line.

text

```lua
Backlinks to "On being honest" (20220614T130812)
------------------------------------------------

20220614T145606--let-this-glance-become-a-stare__journal.txt
20220616T182958--feeling-butterflies-in-your-stomach__journal.txtx
```

The backlinks buffer uses Emacs' built-in Xref infrastructure. On some operating systems, users may need to add certain executables to the relevant environment variables.

