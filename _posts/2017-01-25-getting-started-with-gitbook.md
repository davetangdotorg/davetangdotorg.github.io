---
layout: post
title: "Getting started with GitBook"
description: ""
category: Documentation
tags: [gitbook]
---
{% include JB/setup %}

## Installation

Official guide for [setting up and installing GitBook](https://toolchain.gitbook.com/setup.html). The best way to install GitBook is via [npm](https://docs.npmjs.com/getting-started/what-is-npm):

~~~~{.bash}
which npm
/usr/local/bin/npm

sudo npm install gitbook-cli -g
Password:
/usr/local/bin/gitbook -> /usr/local/lib/node_modules/gitbook-cli/bin/gitbook.js
gitbook-cli@2.3.0 /usr/local/lib/node_modules/gitbook-cli
├── bash-color@0.0.4
├── q@1.4.1
├── semver@5.1.0
├── user-home@2.0.0 (os-homedir@1.0.2)
├── commander@2.9.0 (graceful-readlink@1.0.1)
├── tmp@0.0.28 (os-tmpdir@1.0.2)
├── optimist@0.6.1 (wordwrap@0.0.3, minimist@0.0.10)
├── lodash@4.5.1
├── fs-extra@0.26.5 (path-is-absolute@1.0.1, klaw@1.3.1, jsonfile@2.4.0, graceful-fs@4.1.11, rimraf@2.5.4)
├── npm@3.7.5
└── npmi@1.0.1 (semver@4.3.6, npm@2.15.11)
~~~~

## Getting started

GitBook can setup a boilerplate book.

~~~~{.bash}
mkdir book_test
gitbook init book_test/

ls -1
README.md
SUMMARY.md
~~~~

## Previewing

Preview and serve your book using:

~~~~{.bash}
gitbook serve
~~~~

View site at `http://localhost:4000`. If you make changes while serving the page, they will automatically incorporated.

## Directory Structure

A basic GitBook usually looks something like this:

```
.
├── book.json
├── README.md
├── SUMMARY.md
├── chapter-1/
|   ├── README.md
|   └── something.md
└── chapter-2/
    ├── README.md
    └── something.md
```

GitBook uses a `SUMMARY.md` file to define the structure of chapters and sub-chapters of the book. The `SUMMARY.md` file is used to generate the book's table of contents. The format of `SUMMARY.md` is just a list of links. The link's title is used as the chapter's title, and the link's target is a path to that chapter's file.

## Generate a PDF

`ebook-convert` is required to generate ebooks (epub, mobi, pdf). Install the Calibre application first.

~~~~{.bash}
gitbook pdf . blah.pdf
~~~~

## GitBook with R Markdown

[Check out](http://jason.bryer.org/Rgitbook/installation/installation.html)

~~~~{.r}
library(devtools)
install_github("jbryer/Rgitbook")
library(Rgitbook)
~~~~

Download and install [Node.js](https://nodejs.org/en/). Despite installing Node.js, I'm still getting the error:

~~~~{.r}
newGitbook("testbook")
Error in checkForGitbook(quiet = TRUE) : 
  Cannot find node.js. You can install it from http://nodejs.org/download/
~~~~

## Example

[Introduction to R course](https://github.com/idocs/test1)

