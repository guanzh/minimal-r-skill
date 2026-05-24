---
name: minimal-r
description: Write, edit, review, or refactor R code in an extremely minimalist statistical scripting style. Use whenever the user asks for minimal-r, minimalist R, simple R, Base R first code, linear R scripts, R code generation, R refactoring, R Markdown, Quarto, or direct data analysis code with no over-engineering. Prioritize Base R, flat top-to-bottom scripts, sparse comments, direct outputs, readable statistical workflows, and no unnecessary functions, tryCatch, install.packages, setup boilerplate, package scaffolds, or software-engineering abstractions.
---

# Minimal R

## Goal

Write R code as a clean analyst script: short, direct, readable from top to bottom, and focused on the user's data question.

Prefer the simplest correct code. Do not turn routine statistical analysis into a software engineering project unless the user explicitly asks for reusable infrastructure.

## Default Response Shape

When generating R code:

1. Show the code block immediately.
2. Keep explanation outside code to at most two sentences unless the user asks for teaching, review, or interpretation.
3. Write a flat script that can run from top to bottom.
4. Print, plot, or save the result directly.
5. Use comments only where they clarify the analysis step or an important judgment.

For a small task, return a small answer. A two-line script is better than a full workflow when two lines solve the problem.

## Hard Rules

Do not include by default:

1. `install.packages()` or package-management setup.
2. Custom `function()` wrappers for simple one-off tasks.
3. `tryCatch()`, custom error classes, broad type checking, or elaborate file validations.
4. Logging systems, config systems, command-line interfaces, package scaffolds, or test frameworks.
5. Many helper functions used only for visual structure.
6. `purrr`, closures, higher-order functions, or anonymous functions when a simple loop is clearer.
7. Long comments that explain obvious syntax.
8. Long preambles before code or long summaries after code.

## Base R First

Prefer Base R when it solves the task clearly:

```r
d <- read.csv("data.csv")
mean(d$score, na.rm = TRUE)
```

Useful default tools include `read.csv()`, `subset()`, `transform()`, `aggregate()`, `table()`, `tapply()`, `ave()`, `merge()`, `order()`, `lm()`, `glm()`, `t.test()`, `wilcox.test()`, `aov()`, and base plotting functions.

Use the native pipe `|>` only when it makes a sequence easier to read. Do not use `%>%` unless the surrounding file already uses it consistently.

Use packages only when they clearly improve the analysis:

1. Use `readxl` for Excel files.
2. Use `ggplot2` when a polished figure matters.
3. Use `dplyr` or `tidyr` when grouped summaries, joins, reshaping, or readable transformations become clearer than Base R.
4. Use modeling packages such as `lme4`, `brms`, `vegan`, or `sf` only when the statistical or spatial task genuinely needs them.

Do not load `tidyverse` for a task Base R solves cleanly in a few lines.

## Script Order

For ordinary analysis, use this order and skip irrelevant steps:

1. Load necessary packages.
2. Define a few simple paths or constants.
3. Read data.
4. Clean or transform data.
5. Summarize data.
6. Fit models or run tests.
7. Plot or save outputs.

Keep named intermediate objects only when they make the analysis easier to audit, such as `d`, `d_clean`, `summary_tab`, `fit`, and `p`.

## Comments

Use at most one concise comment per major step.

Good:

```r
# Keep complete cases for the main outcome.
d2 <- subset(d, !is.na(score) & !is.na(group))
```

Avoid:

```r
# This line filters the data frame and removes rows where the score column
# or the group column has missing values before running the next analysis.
d2 <- subset(d, !is.na(score) & !is.na(group))
```

## Helper Functions

Write a helper function only when one of these is true:

1. The user asks for reusable code.
2. The same logic appears three or more times.
3. The function name makes the statistical intent much clearer.
4. The file is already part of a package, workflow, or production pipeline.

Keep helper functions short and specific. Do not create helper libraries unless requested.

## Loops

Use a simple `for` loop when repeating similar work:

```r
outcomes <- c("score", "height", "weight")
models <- list()

for (x in outcomes) {
  models[[x]] <- lm(as.formula(paste(x, "~ age + group")), data = d)
}
```

Do not replace clear loops with functional programming for style.

## Modeling

Fit models directly and print the result:

```r
fit <- lm(score ~ age + group, data = d)
summary(fit)
```

For `glm`, mixed models, Bayesian models, ecological community analysis, or spatial models, still keep the script linear. Use the package only for the necessary model call and the direct summary, prediction, or plot the user needs.

## Plotting

Use base plotting for quick exploration:

```r
boxplot(score ~ group, data = d)
```

Use direct `ggplot2` code for report figures:

```r
library(ggplot2)

p <- ggplot(d, aes(group, score)) +
  geom_boxplot() +
  labs(x = "Group", y = "Score")

p
```

Do not create plotting frameworks for one or two plots.

## Chinese Data And Reports

For Chinese field monitoring, ecological survey, project reporting, teaching, or applied research:

1. Preserve meaningful Chinese column names.
2. Use concise Chinese comments and plot labels when the report is Chinese.
3. Keep names close to the report terminology, such as `物种数`, `相机日`, `独立事件数`, and `保护区内外`.
4. Put manual corrections near the affected code with one short reason.
5. Avoid translating Chinese columns into English only for style consistency.

Example:

```r
# 读取数据
d <- read.csv("camera_status.csv")

# 汇总相机状态
状态汇总 <- sort(table(d$当前状态), decreasing = TRUE)
状态汇总
```

## Refactoring Existing R Code

When refactoring, simplify first:

1. Remove unnecessary abstractions.
2. Replace package-heavy code with Base R where readability improves.
3. Keep the statistical workflow visible.
4. Keep only useful helper functions.
5. Preserve results and output names when possible.
6. Remove package installation and setup boilerplate unless requested.
7. Shorten comments to analysis intent.

Do not rewrite a working analysis into a new framework just to make it look organized.

## Reviews And Explanations

When reviewing R code, focus on:

1. Unnecessary functions or packages.
2. Hidden statistical operations.
3. Code that is harder to audit than needed.
4. Output that is not printed, plotted, or saved.
5. Comments that obscure the analysis.

When explaining R code, explain the analysis flow and statistical meaning rather than every syntax detail.

## Final Check

Before finalizing, verify:

1. Is this as short as the task allows?
2. Can it run from top to bottom?
3. Did Base R solve it clearly?
4. Are packages loaded only when useful?
5. Are comments sparse and meaningful?
6. Are functions, checks, and frameworks minimal?
7. Are results shown or saved directly?
8. Does the language match the user's data and report context?

When in doubt, write simple R code that looks like an analyst wrote it in a scratchpad for a real statistical task.
