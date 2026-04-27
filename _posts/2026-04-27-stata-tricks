---
title: 'Stata patterns'
date: 2026-04-07 14:24:00
excerpt: Stata tricks and patterns I keep forgetting
---

There are some Stata patterns I keep having to search on Google over and over. I'll try to put them here to save myself time.

## List all files in a folder with a certain name pattern
local filelist : dir "folder-path" files "name-pattern-*.dta", respectcase // Without the option, file name will be displayed wrong
foreach file in `filelist' {
  Here it goes ...
}
