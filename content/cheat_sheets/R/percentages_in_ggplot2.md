+++
title = 'Percentages in ggplot2'
draft = false
toc = true
type = 'docs'
+++

Use this syntax to format as percentages:

`scale_x_continuous(labels = scales::percent_format())`

`scale_y_continuous(labels = scales::percent_format())`

To go to 1 decimal place, use:

`scale_x_continuous(labels = scales::percent_format(accuracy = 0.1))`

`scale_y_continuous(labels = scales::percent_format(accuracy = 0.1))`
