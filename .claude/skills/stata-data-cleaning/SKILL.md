---
name: stata-data-cleaning
description: Write reproducible Stata data cleaning and preparation code. Use when the user needs to clean raw data, merge datasets, handle missing values, create derived variables, or build an analysis-ready dataset in Stata.
argument-hint: "[dataset path or cleaning task description]"
allowed-tools: ["Read", "Write", "Glob", "Bash"]
---

# Stata Data Cleaning

## Steps

1. **Understand the data** — before writing any code, ask:
   - What is the data source? (survey, administrative registers, API)
   - What is the unit of observation?
   - What are the key variables needed for analysis?
   - Are there known data quality issues? (missing codes, duplicates, etc.)

2. **Write the do-file** using this standard structure:
   ```stata
   /*============================================================
       Project:  [Project Name]
       Author:   [Name] | Date: [Date]
       Input:    $raw/raw_data.dta
       Output:   $clean/analysis_data.dta
   ============================================================*/

   clear all
   set more off
   cap log close
   log using "logs/clean_`c(current_date)'.log", replace

   * --- Globals ---
   global raw   "data/raw"
   global clean "data/clean"

   * --- Load and inspect ---
   use "${raw}/raw_data.dta", clear
   describe
   summarize
   duplicates report id_var

   * --- Clean variables ---
   * [transformations here — comment every step]

   * --- Validate ---
   assert !mi(id_var)
   isid id_var     // Confirm unique identifier

   * --- Save ---
   compress
   save "${clean}/analysis_data.dta", replace
   log close
   ```

3. **Apply standard cleaning steps as needed:**
   - Replace missing-value codes: `mvdecode var, mv(-99 -88 -77)`
   - Drop duplicates: `duplicates drop id_var, force` (document why)
   - Rename variables: `rename q1 age`
   - Label variables: `label variable age "Age in years"`
   - Create derived variables with inline comments explaining the logic
   - Validate ranges with `assert`: `assert age >= 0 & age <= 120 if !mi(age)`

4. **Deliver the do-file** and summarize:
   - What transformations were made and why
   - Any assumptions embedded in the cleaning choices
   - Variables the user should verify manually before analysis

## Key Rules

- Never overwrite raw data files — always save to a separate clean directory
- Every transformation needs a comment explaining WHY, not just what
- Use `assert` statements generously — silent data errors are the costliest
- Useful packages: `ssc install unique` | `ssc install mdesc`
