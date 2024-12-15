---
layout: post
title: Loading and Saving Data in R
gh-repo: David-Manning/David-Manning.github.io
gh-badge: [star, fork, follow]
tags: [R, data-wrangling, data-loading, data.table, tidyverse, data-import]
comments: true
mathjax: true
author: David Manning
---

Only the basic functionality is given - use `?function_name` for details on loading specific parts.

### CSV
I usually use `data.table` because it can run on multiple cores which can make a big difference when loading large files on modern computers.

```r
# Base R
df <- read.csv(file = "/home/david/Documents/Temp/test.csv")
write.csv(x = df, file = "/home/david/Documents/Temp/test.csv", row.names = FALSE)

# Tidyverse
library(readr) # or library(tidyverse)
df <- readr::read_csv(file = "/home/david/Documents/Temp/test.csv")
readr::write_csv(x = df, file = "/home/david/Documents/Temp/test.csv")

# data.table
library(data.table)
df <- data.table:: fread(file = "/home/david/Documents/Temp/test.csv")
data.table::fwrite(x = df, file = "/home/david/Documents/Temp/test.csv")
```

### JSON
```r
library(jsonlite)
df <- jsonlite::fromJSON(txt = "/home/david/Documents/Temp/test.json")
jsonlite::write_json(x = df, json = "/home/david/Documents/Temp/test.json")
```

### Parquet
Parquet files are very heavily compressed so have a relatively small file size, but are computationally expensive to open and save. They are great for storing large datasets.
```r
library(arrow)
df <- arrow::read_parquet(file = "/home/david/Documents/Temp/test.parquet")
arrow::write_parquet(x = df, file = "/home/david/Documents/Temp/test.parquet")
```

### Excel (xlsx)
```r
library(openxlsx)
openxlsx::read.xlsx(xlsxFile = "/home/david/Documents/Temp/test.xlsx")
openxlsx::write.xlsx(x = df, file = "/home/david/Documents/Temp/test.xlsx")
```

### Bulk Read
This loads multiple files in one directory (can include subdirectories). Any function or file type can be used as long as the first input is the file name and the function is specified.

```r
library(readbulk)
readbulk::read_bulk(
	directory = "/home/david/Documents/Test/",
	extension = ".csv",
	verbose = FALSE,
	fun = data.table::fread,
	data = df, # Add to an existing dataframe
	subdirectories = TRUE # FALSE to only include top level folder
	)

```

### S3
R does not have basic S3 functionality but this function will write and load data.

```r
library(aws.s3)
aws.s3::s3read_using(FUN = fread, bucket = "", object = "")
aws.s3::s3write_using(x = df, FUN = fwrite, bucket = "", object = "")
```

