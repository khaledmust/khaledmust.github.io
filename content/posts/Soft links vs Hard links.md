+++
title = "Soft links vs Hard links"
author = ["Khaled Mustafa"]
description = "I wanted to upload my dot files to a GitHub repository, but they were spread across multiple directories. The solution was to create a link to these files in the Git repository, ensuring that any edits to the main configuration file would be reflected in the linked file."
lastmod = 2023-10-24T13:01:49+03:00
tags = ["articles"]
draft = false
+++

## Introduction {#introduction}

I wanted to upload my dot files to a GitHub repository, but they were spread across multiple directories. The solution was to create a link to these files in the Git repository, ensuring that any edits to the main configuration file would be reflected in the linked file.


## There are two distinct types of links: {#there-are-two-distinct-types-of-links}

1.  Symlink (Symbolic Links), also known as Soft Links:

These links point to the file itself and do not reference memory addresses. When you examine a symlink file, you'll only find the path to the linked file. This means that if you were to copy it to a different machine, it would become non-functional.

The contents of a symlink is as follows:

```text
/home/khaled/.config/doom/config.el
```

As you can see it's just a path to the configuration file, and nothing more.

1.  Hard Links:

A hard link is a file that points to the same memory address as the original file. You could think of it as an exact copy, and any changes made in either file will be mirrored in the other.

{{< figure src="/ox-hugo/2023-10-24_12-59-15_symlink vs hardlink.png" >}}

Thus, for our use case, Hard Links is to win.


## How to create a Hard Link? {#how-to-create-a-hard-link}

This is simply done using the `ln` command without specifying any options to it.
For example,

```bash
ln ./Original_File.txt ./HardLink_File.txt
```
