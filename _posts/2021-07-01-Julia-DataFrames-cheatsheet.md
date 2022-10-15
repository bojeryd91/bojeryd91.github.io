---
title: 'Julia DataFrames cheatsheet'
date: 2021-07-01 11:48:52
excerpt: My cheatsheet to go between data manipulation in Stata and Julia/DataFrames
---

## Creating and calculating within groups</h4>

| In Stata:      | In Julia: |
| :------------- | :-------- |
|bysort group1 group2 (t): egen sum = sum(freq) <br> generate prob = freq/sum <br> bysort group1 group2: gen cumul = sum(prob) | sort!(df, [:group1, :group2, :t]) <br> df = transform(groupby(df, [:group1, :group2]), :freq =&gt; sum =&gt; :sum) <br> df[:, :prob] = df[:, :freq]./df[:, :sum] <br> df = transform(groupby(df, [:group1, :group2]), :prob => cumsum => :cumul) |

## Collapsing data

| In Stata: | In Julia: |
| :-------- | :-------- |
| collapse (sum) freq, by(group) | combine(groupby(df, group), :freq => sum => :freq) |
