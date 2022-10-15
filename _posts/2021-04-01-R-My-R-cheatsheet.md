---
title: 'R cheatsheet'
date: 2021-04-01 15:13:55
excerpt: A list of R commands I often look up (and use frequently), and how they map to corresponding Stata commands
---

#  data.table
## Produce frequency table by group

| In R:      | In Stata: |
| :------------- | :-------- |
| dt\[, .\(rowcount = .N\), by=group\] | tab group |

## Compute max of var per group

| In R: | In Stata: |
| :-------- | :-------- |
| dt\[, group_max := max(var), by=group\] | by group: egen group_max = max(var) |

## Create row id within group

| In R: | In Stata: |
| :-------- | :-------- |
| dt\[, r_id:=seq_len(.N), by=group\] | by group: g r_id = \_n |

#  lubridate (w/ data.table)
## Create Date from string

dt\[, date_var := as.Date(as.character(num_date), format="%Y%m%d")]

## Convert Date to year-quarter
dt\[, yq := quarter(date_var, with_year=TRUE)

## Create Date from components (e.g., year-quarter from year and quarter)
yq = quarter(parse_date_time(paste0(year, ".", quarter), orders="Yq")

