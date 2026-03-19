---
name: stata-regression
description: Write reproducible Stata regression code with publication-ready output tables using esttab or outreg2. Use when the user asks to run regressions, estimate models, produce results tables, or run robustness checks in Stata.
argument-hint: "[model description or do-file path]"
allowed-tools: ["Read", "Write", "Glob", "Bash"]
---

# Stata Regression

## Steps

1. **Clarify the specification** — before writing any code, ask:
   - What is the dependent variable and key regressors?
   - What controls and fixed effects are required?
   - At what level should standard errors be clustered?
   - What output format is needed (LaTeX, Word, CSV)?

2. **Choose the right estimator:**
   - `regress` — basic OLS
   - `reghdfe` — high-dimensional fixed effects (preferred for multiple FEs)
   - `xtreg, fe` — panel fixed effects (single FE)
   - `logit` / `probit` / `ivregress` — as appropriate

3. **Write the do-file** using this structure:
   ```stata
   * ============================================
   * [Project] — Regression Analysis
   * Author: [Name] | Date: [Date]
   * ============================================

   * Load and inspect
   use "data/analysis_data.dta", clear
   summarize $depvar $regressors

   * Main specification
   reghdfe $depvar $regressors $controls, ///
       absorb($fe) vce(cluster $cluster_var)
   eststo m1

   * Alternative specification
   reghdfe $depvar $regressors $controls $extra_controls, ///
       absorb($fe) vce(cluster $cluster_var)
   eststo m2

   * Export table
   esttab m1 m2 using "output/table1.tex", replace ///
       se label booktabs ///
       title("Main Results") ///
       mtitles("Baseline" "Extended") ///
       addnotes("Clustered SEs in parentheses.")
   ```

4. **After generating output:**
   - Explain what each specification estimates
   - Flag any assumptions the user should verify
   - Suggest robustness checks (alternative clustering, subsample, placebo)

## Key Rules

- Always cluster SEs at the level where treatment varies
- Prefer `reghdfe` over `xtreg, fe` when using multiple fixed effects
- Required packages: `ssc install estout` | `ssc install reghdfe`
- Never run regressions without first checking for missing values and outliers
