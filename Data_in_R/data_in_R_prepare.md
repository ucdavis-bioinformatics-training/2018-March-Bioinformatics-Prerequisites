---
title: "Data_in_R"
author: "Bioinformatics Core"
date: "2018-03-27"
output:
    html_document:
      keep_md: TRUE
---

## Create a new RStudio project

Open RStudio and create a new project

* File > New Project > New Directory > New Project (name the new directory, Ex. Data_in_R) and check "use packrat with this project".

Set some options and make sure the 'tidyverse' is installed (if not install it), and then load

In the R console run the following commands

```r
knitr::opts_chunk$set(echo = TRUE) # make sure to include the R chunks in the output

if (!any(rownames(installed.packages()) == "tidyverse")){
  install.packages("tidyverse")
}
library(tidyverse)
```

```
## ── Attaching packages ──────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
## ✔ tidyr   0.8.0     ✔ stringr 1.3.0
## ✔ readr   1.1.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ─────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

Learn more about the tidyverse see <https://www.tidyverse.org>.

## Open a new R Notebook

An R notebook is an R Markdown document with chunks that can be executed independently and interactively, with output visible immediately beneath the input. More info see <https://rmarkdown.rstudio.com/r_notebooks.html>

* File -> New File -> R Notebook
* Save the Notebook (Ex. data_in_R)

## Edit the file YAML portion
The top YAML (YAML ain't markup language) portion of the doc tells RStudio how to parse the document.
<pre><code>---
title: "Data_in_R"
author: your_name
date: "2018-03-27"
output:
    html_notebook: default
    html_document: default
---</code></pre>

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **preview** or **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed R code and plots in chunks like this:

<pre><code>```{r chunk_name}
print('hello world!')
```</code></pre>

Try 'knitting' to html, pdf, and doc as well as previewing the notebook. Open the resulting documents.

## Download 
