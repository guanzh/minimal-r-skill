---
name: r-suite
description: Unified R coding suite for writing, refactoring, reviewing, explaining, modeling, plotting, and lightly optimizing R, R Markdown, and Quarto code. Use whenever the user asks for R code, minimal R, Base R first analysis, R script cleanup, R code review, statistical modeling, report figures, Chinese ecological/statistical analysis scripts, or R workflow help. Internally route to the simplest mode without asking the user to choose. Prioritize direct Base R, flat top-to-bottom scripts, sparse comments, explicit missing-value handling where it matters, preserved outputs during refactors, and no unnecessary functions, packages, setup boilerplate, project scaffolds, tests, Shiny, renv, CI, Docker, or software-engineering abstractions unless explicitly requested.
---

# R Suite

## Goal

Use one entry point for ordinary R work. Infer the needed mode from the user's request and produce the simplest correct R code or review.

Do not present a menu of modes. Ask at most one clarification only when the missing detail changes the analysis result, such as an unknown outcome variable, grouping variable, input file, or model purpose.

## Routing

Choose the first matching mode silently:

1. **Tiny Task**: answer with the shortest direct R code.
2. **Script**: write a flat top-to-bottom script.
3. **Refactor**: simplify existing code while preserving inputs, outputs, object names, and results where possible.
4. **Review**: list only actionable issues; do not rewrite unless asked.
5. **Model**: if outcome, predictors, grouping, or model purpose are unclear, ask one question; otherwise fit directly.
6. **Plot**: use base plotting for quick checks, direct `ggplot2` for report figures.
7. **Performance**: only when the user mentions speed, memory, large data, or optimization; measure or identify the bottleneck before changing style.
8. **Project/Package/Shiny**: only when explicitly requested or already present in the repo; keep the structure minimal.

## Priority

When rules conflict, use this order:

```text
correct result > simplest readable code > Base R > named intermediates > native pipe > small package use > helper function > project structure
```

## Default Output

When writing R code:

1. Show the code block immediately.
2. Keep explanation outside code to at most two sentences unless the user asks for teaching, review, or interpretation.
3. Write a script that runs from top to bottom.
4. Print, plot, or save the result directly.
5. Use comments only for analysis intent, assumptions, or manual judgment.

For a small task, return a small answer. A two-line script is better than a full workflow when two lines solve the problem.

## Hard Rules

Do not include by default:

1. `install.packages()` or dependency-management setup.
2. `renv`, Docker, CI, Git workflow, `targets`, `testthat`, or project scaffolding.
3. Custom `function()` wrappers for simple one-off tasks.
4. `tryCatch()`, custom error classes, broad type checking, or elaborate file validations.
5. Shiny modules, R6/S3/S4/S7 classes, package structure, roxygen2, or `devtools`.
6. Many helper functions used only for visual structure.
7. `purrr`, closures, higher-order functions, or anonymous functions when a simple loop is clearer.
8. Forced `pkg::fun()` syntax when a short `library(pkg)` plus direct calls is clearer.
9. Forced `glue::glue()` when `paste()` or `paste0()` is simpler.
10. Long comments that explain obvious syntax.
11. Long preambles before code or long summaries after code.

## Base R First

Prefer Base R when it solves the task clearly:

```r
d <- read.csv("data.csv")
mean(d$score, na.rm = TRUE)
```

Useful default tools include `read.csv()`, `subset()`, `transform()`, `aggregate()`, `table()`, `tapply()`, `ave()`, `merge()`, `order()`, `lm()`, `glm()`, `t.test()`, `wilcox.test()`, `aov()`, and base plotting functions.

Use packages only when they clearly improve the task:

1. Use `readxl` for Excel files.
2. Use `ggplot2` when a polished figure matters.
3. Use `dplyr` or `tidyr` when grouped summaries, joins, reshaping, or transformations are clearer than Base R.
4. Use `data.table`, `arrow`, `duckdb`, `sf`, `vegan`, `lme4`, `brms`, or similar packages only when the data size, file type, model, or spatial/statistical task genuinely needs them.

Do not load `tidyverse` for a task Base R solves cleanly in a few lines.

## Script Shape

For ordinary analysis, use this order and skip irrelevant steps:

1. Load necessary packages at the top.
2. Define a few simple paths or constants.
3. Read data.
4. Clean or transform data.
5. Summarize data.
6. Fit models or run tests.
7. Plot or save outputs.

Use one script by default. Create folders such as `R/`, `data/`, `output/`, or `docs/` only when the user asks for a project layout or the existing project already uses one.

Keep named intermediate objects only when they make the analysis easier to audit, such as `d`, `d_clean`, `summary_tab`, `fit`, and `p`.

## Style

Use a tidy, readable R style without importing tidyverse habits wholesale:

1. Use `<-` for assignment.
2. Use spaces around operators: `a + b`, not `a+b`.
3. Prefer double quotes for strings.
4. Use `TRUE` and `FALSE`, not `T` and `F`.
5. Use full argument names for non-obvious arguments: `na.rm = TRUE`, not partial matches.
6. Avoid assignment inside function calls.
7. Keep vertical whitespace sparse; use blank lines like paragraph breaks.
8. Keep lines reasonably short, but do not contort simple code only to hit a fixed line limit.
9. If a function call becomes hard to scan, put each argument on its own line.
10. Use concise, readable names; for English names, prefer `snake_case`.

Do not rename meaningful Chinese columns or variables into English only for style consistency.

## Missing Values

Handle missing values explicitly when they affect the result:

```r
mean(d$score, na.rm = TRUE)
d2 <- subset(d, !is.na(score) & !is.na(group))
```

Do not add defensive missing-value machinery when the user's task only needs a direct calculation.

## Pipes

Use the native pipe `|>` only when it makes a sequence easier to read.

Prefer named intermediate objects over long, clever pipelines. Avoid `%>%` in new code unless the surrounding file already consistently uses it. Avoid right-hand assignment with `->`.

```r
d_clean <- d |>
  subset(!is.na(score)) |>
  transform(passed = score >= 60)
```

## Comments

Use at most one concise comment per major step. Comments should explain why a step matters, not what obvious syntax does.

If the code needs many comments to be understandable, simplify the code first.

## Base R Pitfalls

Use these small safeguards when relevant:

1. Prefer `seq_along(x)` over `1:length(x)`.
2. Preallocate objects filled inside loops.
3. Use `drop = FALSE` when subsetting one-column data frames that must remain data frames.
4. Use `write.csv(x, "file.csv", row.names = FALSE)` unless row names are meaningful output.
5. Avoid naming objects `data`, `df`, `T`, `F`, `c`, `mean`, or `sd` when clearer names are available.

## Loops

Prefer vectorized Base R when it is natural. Use a simple `for` loop when it is clearer than `apply()` or `purrr`.

```r
outcomes <- c("score", "height", "weight")
models <- vector("list", length(outcomes))
names(models) <- outcomes

for (x in outcomes) {
  models[[x]] <- lm(as.formula(paste(x, "~ age + group")), data = d)
}
```

Do not replace clear loops with functional programming for style.

## Helper Functions

Write a helper function only when one of these is true:

1. The user asks for reusable code.
2. The same logic appears three or more times.
3. The function name makes the statistical intent much clearer.
4. The file is already part of a package, workflow, or production pipeline.

Keep helper functions short and specific. In ordinary analysis scripts, do not move small one-off steps into separate files.

If writing a small helper, avoid unnecessary `return()` except for early exits.

## Modeling

Fit models directly and print the result:

```r
fit <- lm(score ~ age + group, data = d)
summary(fit)
```

If the user has not specified the outcome, predictors, grouping, or model purpose, ask one concise clarification before writing a model.

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

When using `ggplot2`, put `+` at the end of a line and start each layer on a new line. Prepare filtered or summarized data before `ggplot()` instead of hiding data cleaning inside the plot call.

Do not create plotting frameworks for one or two plots.

## Review Mode

When the user asks for review, check, audit, or diagnose:

1. Lead with the most important issues.
2. Give exact lines or snippets when available.
3. Focus on wrong results, unnecessary complexity, hidden assumptions, missing outputs, and missing-value problems.
4. Do not rewrite the whole script unless the user asks.
5. End with the smallest concrete fix path.

## Refactor Mode

When refactoring existing R code:

1. Preserve inputs, outputs, file paths, object names, and results when possible.
2. Remove unnecessary abstractions.
3. Replace package-heavy code with Base R where readability improves.
4. Keep the statistical workflow visible.
5. Keep only useful helper functions.
6. Remove package installation and setup boilerplate unless requested.
7. Shorten comments to analysis intent.
8. Prefer named intermediate objects over long, clever pipelines.

Do not rewrite a working analysis into a new framework just to make it look organized.

## Chinese Data And Reports

For Chinese field monitoring, ecological survey, project reporting, teaching, or applied research:

1. Preserve meaningful Chinese column names.
2. Use concise Chinese comments and plot labels when the report is Chinese.
3. Keep names close to the report terminology, such as `物种数`, `相机日`, `独立事件数`, and `保护区内外`.
4. Put manual corrections near the affected code with one short reason.
5. Avoid translating Chinese columns into English only for style consistency.

```r
# 读取数据
d <- read.csv("camera_status.csv")

# 汇总相机状态
状态汇总 <- sort(table(d$当前状态), decreasing = TRUE)
状态汇总
```

## Verification

When editing or creating local files:

1. Run the smallest available check, such as `Rscript file.R`, when R is available and inputs exist.
2. If R is unavailable or data is missing, do a static check for syntax, obvious missing objects, and package assumptions.
3. Tell the user exactly what was and was not verified.

## When Project Rules Apply

Only apply heavier project rules when the user explicitly asks for a package, Shiny app, reproducible pipeline, course project, or multi-file repository.

Then, and only then, it is acceptable to use project folders, README files, tests, `renv`, roxygen2, package metadata, Shiny modules, or CI. Keep even those choices minimal and aligned with the existing project.

## Final Check

Before finalizing, verify:

1. Is this as short as the task allows?
2. Can it run from top to bottom?
3. Did Base R solve it clearly?
4. Are packages loaded only when useful?
5. Are missing values handled where they matter?
6. Are comments sparse and meaningful?
7. Are functions, checks, and frameworks minimal?
8. Are results shown or saved directly?
9. Does the language match the user's data and report context?

When in doubt, write simple R code that looks like an analyst wrote it in a scratchpad for a real statistical task.
