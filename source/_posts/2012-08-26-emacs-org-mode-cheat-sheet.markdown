---
layout: post
title: "Emacs Org-mode Cheat Sheet"
date: 2012-08-26
comments: true
categories: [tips, emacs]
---

<!--more-->


## Keybindings

### General

    | Visibility cycling    |                                                 |
    | <TAB>                 | Rotate current subtree                          |
    | S-<TAB> and C-u <TAB> | Rotate entire buffer                            |
    | C-u C-u C-u <TAB>     | Show all                                        |
    |-----------------------+-------------------------------------------------|
    | Move between heading  |                                                 |
    | C-c C-n               | Next heading                                    |
    | C-c C-p               | Previous Heading                                |
    | C-c C-f               | Next heading same level                         |
    | C-c C-b               | Previous Heading same level                     |
    | C-c C-u               | Backward to higher level heading                |
    |-----------------------+-------------------------------------------------|
    | Structure editing     |                                                 |
    | M-<RET>               | Insert new heading with same level as current   |
    | M-S-<RET>             | Insert new TODO entry                           |
    | <TAB>                 | Cycle through levels in empty entry             |
    | M-<left>/<right>      | Promote/demote current heading by one level     |
    | M-S-<left>/<right>    | Promote/demote current subtree by one level     |
    | M-S-<up>/<down>       | Move subtree up/down                            |
    | C-c C-w               | Refile entry or region to different location    |
    | C-x n s/w             | Narrow buffer to current subtree/widen it again |
    |-----------------------+-------------------------------------------------|
    | Sparse trees          |                                                 |
    | C-c /                 |                                                 |
    | C-c / r               |                                                 |
    |-----------------------+-------------------------------------------------|
    | Plain lists           |                                                 |
    | '-' '+' '*'           | Unordered list                                  |
    | '1.' '1)'             | Ordered list                                    |
    | '::'                  | Description list                                |
    | <TAB>                 | Fold items                                      |
    | M-<RET>               | Insert new items                                |
    | M-S-<RET>             | Insert new items with a checkbox                |
    | M-S-<up>/<down>       | Move item up/down                               |
    | M-<left>/<right>      | Decrease/increase indentation of item           |
    | M-S-<left>/<right>    | Decrease/increase including subitems            |
    | C-c C-c               | Toggle checkbox                                 |
    | C-c -                 | Cycle item bullets('-','+','*','1.','1)')       |
    |-----------------------+-------------------------------------------------|
    | Footnotes             |                                                 |
    | C-c C-x f             | Footnote action command                         |
    | C-c C-c               | Jump between definition and reference           |
    |-----------------------+-------------------------------------------------|


### Tables

    | C-c |                        | convert and active region to table, create new table if no active region |
    | C-c C-c                      | Re-align table without moveing the cursor                                |
    | <TAB>                        | Re-align and move to next field                                          |
    | S-<TAB>                      | Re-align and move to previous field                                      |
    | <RET>                        | Re-align and move down to next row                                       |
    |------------------------------+--------------------------------------------------------------------------|
    | Column and row editing       |                                                                          |
    | M-<left>/<right>             | Move current column left/right                                           |
    | M-S-<left>                   | Kill current column                                                      |
    | M-S-<right>                  | Insert new column to the left of cursor                                  |
    | M-<up>/<down>                | Move current row up/down                                                 |
    | M-S-<up>                     | Kill current row                                                         |
    | M-S-<down>                   | Insert new row above current                                             |
    | C-c -                        | Insert horizontal line below current                                     |
    | C-c <RET>                    | Insert horizontal line below and move to it                              |
    | C-c ^                        | Sort table lines                                                         |

### Hyperlinks

    | C-c l   | Store a link                                |
    | C-c C-l | Insert a link                               |
    | C-c C-l | Edit links when cursor on the link          |
    | C-c C-o | Open link                                   |
    | C-c &   | Jump back to recorded position(Using C-c %) |

### TODO Items

    | C-c C-t          | Rotate TODO states               |
    | S-<right>/<left> | Select following/preceding state |
    | C-c / t          | View TODO items in sparse tree   |
    | C-c a t          | Show global TODO list            |
    | S-M-<RET>        | Insert new TODO entry            |
    | C-c ,            | Set priority                     |
    | S-<up>/<down>    | Increase/decrease priority       |
