# Minimal R Skill

`minimal-r` is a Codex skill for writing and refactoring R code in an extremely simple analyst-script style.

It prioritizes:

- Base R first
- Flat top-to-bottom scripts
- Sparse comments
- Direct printed, plotted, or saved output
- Minimal helper functions
- No `install.packages()` boilerplate
- No unnecessary defensive programming or software-engineering structure

## Install

Copy the `minimal-r` folder into your Codex skills directory:

```powershell
Copy-Item -Recurse -Force .\minimal-r "$env:USERPROFILE\.codex\skills\minimal-r"
```

Restart Codex after installing or updating the skill so the skill index refreshes.

## Use

Examples:

```text
Use minimal-r to write an R script that reads data.csv and summarizes score by group.
```

```text
Use minimal-r to simplify this R script. Keep the output the same.
```

```text
用 minimal-r 风格写一个 R 脚本，读取 Excel 表格并统计各物种的独立事件数。
```

## Repository Layout

```text
minimal-r-skill/
  minimal-r/
    SKILL.md
    agents/
      openai.yaml
  README.md
  LICENSE
  .gitignore
```

Only the `minimal-r/` folder is the Codex skill. The root files are for GitHub packaging and human installation.

## Notes

This skill intentionally does not include scripts, templates, or large references. Its job is style control: keep R code direct, readable, and statistically auditable.
