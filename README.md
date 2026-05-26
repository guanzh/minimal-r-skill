# R Suite

`r-suite` is the recommended single entry point for R work. It merges the previous minimal R style, simple statistical scripting, review, refactoring, modeling, plotting, and light performance guidance into one skill so you do not need to choose among many R skills.

It prioritizes:

- Base R first
- Flat top-to-bottom scripts
- Sparse comments
- Explicit missing-value handling where it matters
- Direct printed, plotted, or saved output
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

## Repository Layout

```text
minimal-r-skill/
  r-suite/
    SKILL.md
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

It selectively borrows readable style practices from the tidyverse style guide, such as spacing, clear names, native pipes, sparse comments, and readable ggplot layout, while rejecting project-heavy defaults that would make small analysis scripts larger than needed.
