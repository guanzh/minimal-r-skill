# R Suite

`r-suite` is the recommended single entry point for R work. It combines minimal R scripting, review, refactoring, modeling, plotting, data-specific analysis recommendations, and light performance guidance so you do not need to choose among many R skills.

It prioritizes:

- Base R first
- Flat top-to-bottom scripts
- Sparse comments
- Explicit missing-value handling where it matters
- Direct printed, plotted, or saved output
- Data-specific basic, intermediate, and advanced analysis suggestions
- Minimal helper functions
- No `install.packages()` boilerplate
- No unnecessary defensive programming or software-engineering structure

## Install

Copy the `r-suite` folder into your Codex skills directory:

```powershell
Copy-Item -Recurse -Force .\r-suite "$env:USERPROFILE\.codex\skills\r-suite"
```

Restart Codex after installing or updating the skill so the skill index refreshes.

## Use

Examples:

```text
Use r-suite to write an R script that reads data.csv and summarizes score by group.
```

```text
Use r-suite to simplify this R script. Keep the output the same.
```

```text
用 r-suite 写一个 R 脚本，读取 Excel 表格并统计各物种的独立事件数。
```

```text
用 r-suite 根据我的数据结构，建议初级、中级和高级分析方法。
```

## What It Changes

`r-suite` pushes the agent toward small analyst scripts instead of engineered R projects.

| Task | Common overbuilt output | `r-suite` output |
|---|---|---|
| Read a CSV and calculate a mean | Loads `dplyr`, checks whether the file exists, writes a custom function, prints a sentence | Reads the file and calls `mean()` |
| Summarize by group | Loads the full `tidyverse`, builds a reusable pipeline, adds extra checks | Uses Base R `aggregate()` or a short direct `dplyr` summary only when clearer |
| Refactor a script | Splits one analysis into functions, config blocks, and helper files | Keeps one readable top-to-bottom script and removes unnecessary abstraction |
| Review R code | Rewrites the whole file immediately | Lists only actionable issues unless you ask for a rewrite |
| Recommend analyses | Dumps a generic list of statistical methods | Connects a few basic, intermediate, and advanced methods to the actual variables and study design |

### Example 1: Mean Value

Overbuilt:

```r
library(dplyr)

calculate_mean_safe <- function(file_path, column_name) {
  if (!file.exists(file_path)) {
    stop("File not found")
  }

  data <- read.csv(file_path)
  data |>
    summarise(mean_value = mean(.data[[column_name]], na.rm = TRUE)) |>
    pull(mean_value)
}

calculate_mean_safe("data.csv", "score")
```

`r-suite`:

```r
d <- read.csv("data.csv")
mean(d$score, na.rm = TRUE)
```

### Example 2: Group Summary

Overbuilt:

```r
library(tidyverse)

summarize_scores <- function(path) {
  read_csv(path, show_col_types = FALSE) |>
    filter(!is.na(score), !is.na(group)) |>
    group_by(group) |>
    summarise(
      n = n(),
      mean_score = mean(score, na.rm = TRUE),
      sd_score = sd(score, na.rm = TRUE),
      .groups = "drop"
    )
}

score_summary <- summarize_scores("data.csv")
print(score_summary)
```

`r-suite`:

```r
d <- read.csv("data.csv")
d <- subset(d, !is.na(score) & !is.na(group))

aggregate(
  score ~ group,
  data = d,
  FUN = function(x) c(n = length(x), mean = mean(x), sd = sd(x))
)
```

### Example 3: Report Plot

Overbuilt:

```r
library(tidyverse)

make_score_plot <- function(data, output_path) {
  p <- data |>
    filter(!is.na(score), !is.na(group)) |>
    ggplot(aes(x = group, y = score, fill = group)) +
    geom_boxplot() +
    theme_minimal() +
    labs(x = "Group", y = "Score")

  ggsave(output_path, p, width = 7, height = 5)
  p
}

make_score_plot(read_csv("data.csv"), "score_by_group.png")
```

`r-suite`:

```r
library(ggplot2)

d <- read.csv("data.csv")
d <- subset(d, !is.na(score) & !is.na(group))

p <- ggplot(d, aes(group, score)) +
  geom_boxplot() +
  labs(x = "Group", y = "Score")

p
ggsave("score_by_group.png", p, width = 7, height = 5)
```

### Example 4: Analysis Advice

Prompt:

```text
我的相机监测数据包含物种、保护区内外、样点、日期、相机工作日和独立事件数。还能做哪些分析？
```

`r-suite` gives a short, data-specific roadmap:

- **Basic**: check effort and missingness; summarize effort-standardized event rates; plot species and temporal patterns.
- **Intermediate**: compare protected versus unprotected sites with effect sizes; model event rates while controlling for effort and site.
- **Advanced**: consider mixed models for repeated sites or occupancy models only when repeat-survey and detection data support them.

Each recommendation states the question, method, why it fits, and what additional information it needs. It does not force an advanced method when the design cannot support one.

## Repository Layout

```text
minimal-r-skill/
  r-suite/
    SKILL.md
    test-prompts.json
    agents/
      openai.yaml
  minimal-r/
    SKILL.md
    agents/
      openai.yaml
  README.md
  LICENSE
  .gitignore
```

Use `r-suite/` as the primary Codex skill. `minimal-r/` is retained as a legacy minimal entry for compatibility.

## Notes

This suite intentionally does not include scripts, templates, or large references. Its job is style control: keep R code direct, readable, and statistically auditable.

`r-suite/test-prompts.json` is a small regression set for future optimization. Use it to check that updates still produce tiny Base R answers, direct refactors, one-question clarification when details are missing, and data-specific advice instead of generic method catalogs.

It selectively borrows readable style practices from the tidyverse style guide, such as spacing, clear names, native pipes, sparse comments, and readable ggplot layout, while rejecting project-heavy defaults that would make small analysis scripts larger than needed.
