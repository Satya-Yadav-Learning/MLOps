# TaskAndResolution_CodeQualityTools.md

# Set Up Code Quality Tools for an ML Project

---

# Project Overview

Maintaining consistent code quality is essential in Machine Learning projects where multiple developers contribute to the same codebase. Modern Python projects use automated tools to enforce coding standards, detect errors early, and ensure consistent formatting before code is merged into the main branch.

This task focuses on configuring **Ruff** (linter) and **Black** (formatter) to maintain code quality.

---

# Business Requirement

A Python ML project fails the code quality checks because:

* Ruff configuration is outdated.
* Black uses a different line length.
* Source code contains linting issues.
* Formatting validation must pass.

The objective is to configure the project so that both Ruff and Black execute successfully.

---

# Learning Objectives

After completing this task, you should understand:

* Python code linting
* Code formatting
* Ruff configuration
* Black configuration
* pyproject.toml
* Automated code quality
* Static code analysis

---

# Architecture

```text
                 Developer
                      │
                 Write Python Code
                      │
                      ▼
              pyproject.toml
                      │
        ┌─────────────┴─────────────┐
        │                           │
        ▼                           ▼
     Ruff Check               Black Check
(Code Quality Analysis)   (Code Formatting)
        │                           │
        └─────────────┬─────────────┘
                      ▼
                 CI/CD Pipeline
                      │
                      ▼
               Production Merge
```

---

# Why Did the Company Ask for This?

Without automated code quality tools:

* Different coding styles
* Unused imports
* Incorrect import ordering
* Inconsistent formatting
* Difficult code reviews

Using Ruff and Black ensures every developer follows the same coding standards.

---

# Understanding the Tools

## What is Ruff?

Ruff is a **Python Linter**.

It checks:

* Unused imports
* Import order
* Syntax issues
* Style violations
* Potential bugs

Think of Ruff as a **Code Inspector**.

---

## What is Black?

Black is a **Python Code Formatter**.

It automatically:

* Formats indentation
* Fixes spacing
* Wraps long lines
* Produces consistent code style

Think of Black as an automatic **Code Beautifier**.

---

# Step-by-Step Solution

## Step 1 – Navigate to Project

```bash
cd /root/code/fraud-detection
```

Verify:

```bash
pwd
```

Expected Output

```text
/root/code/fraud-detection
```

---

## Step 2 – Review Project Configuration

```bash
cat pyproject.toml
```

Original Configuration

```toml
[tool.ruff]
line-length = 88
select = ["E", "F"]

[tool.black]
line-length = 100
```

Problems:

* Different line lengths
* Deprecated Ruff configuration
* Missing lint section
* Missing W and I rules

---

## Step 3 – Update Configuration

Correct Configuration

```toml
[tool.ruff]
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "W", "I"]

[tool.black]
line-length = 120
```

Explanation

### Ruff

```toml
line-length = 120
```

Allows a maximum line length of 120 characters.

---

### Ruff Lint Rules

```toml
select = ["E","F","W","I"]
```

| Rule | Purpose        |
| ---- | -------------- |
| E    | Style Errors   |
| F    | Logical Errors |
| W    | Warnings       |
| I    | Import Sorting |

---

### Black

```toml
line-length = 120
```

Matches Ruff to maintain consistency.

---

## Step 4 – Run Ruff

```bash
ruff check src/
```

Representative Output

```text
I001 Import block is unformatted

F401 os imported but unused
```

Explanation

* Imports not sorted
* Unused import detected

---

## Step 5 – Automatically Fix Ruff Issues

```bash
ruff check src/ --fix
```

Representative Output

```text
Found 1 error (1 fixed, 0 remaining)
```

Ruff automatically:

* Removed unused imports
* Organized imports

---

## Step 6 – Verify Ruff

```bash
ruff check src/
```

Expected Output

```text
All checks passed!
```

---

## Step 7 – Verify Black

```bash
black --check src/
```

Expected Output

```text
All done!

5 files would be left unchanged.
```

Meaning:

All Python files already follow Black's formatting rules.

---

# Important Commands

```bash
cd /root/code/fraud-detection

cat pyproject.toml

vi pyproject.toml

ruff check src/

ruff check src/ --fix

black --check src/

black src/
```

---

# Linux Concepts

## cat

Displays file contents.

---

## vi

Edits configuration files.

---

## pwd

Shows the current working directory.

---

# Python Concepts

## Linting

Static analysis that identifies coding issues without executing the program.

---

## Formatting

Automatically rewrites code into a consistent style.

---

## pyproject.toml

Central configuration file for Python development tools.

---

# Ruff vs Black

| Ruff                     | Black               |
| ------------------------ | ------------------- |
| Linter                   | Formatter           |
| Detects issues           | Formats code        |
| Finds unused imports     | Fixes spacing       |
| Checks import order      | Formats indentation |
| Detects style violations | Wraps long lines    |

---

# DevOps vs MLOps Comparison

| DevOps Tool              | Python Equivalent |
| ------------------------ | ----------------- |
| terraform fmt            | Black             |
| tflint                   | Ruff              |
| Checkov                  | Ruff              |
| SonarQube                | Ruff              |
| Jenkins Build Validation | Ruff + Black      |

---

# Enterprise Use Case

```text
Developer
     │
Git Commit
     │
Pre-Commit Hook
     │
 ┌───┴───────────┐
 │               │
 ▼               ▼
Ruff          Black
 │               │
 └──────┬────────┘
        ▼
GitHub
        │
CI/CD Pipeline
        │
Production
```

If either tool fails, the pull request is rejected until the issues are fixed.

---

# Common Errors

| Error                  | Reason                | Solution                                 |
| ---------------------- | --------------------- | ---------------------------------------- |
| I001                   | Imports not sorted    | Run `ruff --fix`                         |
| F401                   | Unused import         | Remove unused import or run `ruff --fix` |
| Different line lengths | Ruff & Black mismatch | Configure both to 120                    |
| Deprecated Ruff config | Wrong section         | Use `[tool.ruff.lint]`                   |

---

# Best Practices

* Keep Ruff and Black line lengths identical.
* Run Ruff before Black.
* Automate checks using pre-commit hooks.
* Include Ruff and Black in CI/CD.
* Keep `pyproject.toml` under version control.

---

# Scenario-Based Interview Questions & Answers

## L1 – Junior Engineer

### Q1. What is Ruff?

**Answer:**
Ruff is a Python linter that analyzes source code for style violations, unused imports, syntax issues, and potential programming errors.

---

### Q2. What is Black?

**Answer:**
Black is a Python formatter that automatically formats code into a consistent style.

---

## L2 – Mid-Level Engineer

### Q1. Why use Ruff and Black together?

**Answer:**
Ruff focuses on code quality, while Black focuses on formatting. Together they produce clean, consistent, and maintainable code.

---

### Q2. What is `ruff --fix`?

**Answer:**
It automatically fixes many linting issues such as unused imports and incorrect import ordering.

---

## L3 – Senior Engineer

### Q1. How would you enforce code quality across multiple developers?

**Answer:**
Use Ruff and Black with pre-commit hooks, integrate them into CI/CD pipelines, and reject pull requests that fail validation.

---

### Q2. Why configure Ruff using `[tool.ruff.lint]`?

**Answer:**
Ruff version 0.1 and later requires lint rule selection under `[tool.ruff.lint]`, ensuring compatibility with the latest configuration schema.

---

## L4 – Architect

### Q1. How would you standardize Python development across an enterprise?

**Answer:**
Create organization-wide coding standards, provide shared Ruff and Black configurations, enforce them through Git hooks and CI/CD pipelines, and include automated quality gates before deployment.

---

### Q2. Why is automated code quality important in MLOps?

**Answer:**
Machine Learning systems often involve collaborative development. Automated code quality improves maintainability, reduces technical debt, prevents common coding mistakes, and ensures consistent deployments.

---

# Cheat Sheet

```bash
cd /root/code/fraud-detection

cat pyproject.toml

ruff check src/

ruff check src/ --fix

black src/

black --check src/
```

---

# Key Takeaways

* Ruff performs static code analysis.
* Black formats Python code automatically.
* Configure both tools with the same line length.
* Use `[tool.ruff.lint]` for Ruff rule selection.
* Run `ruff --fix` to resolve common lint issues automatically.
* Integrate Ruff and Black into CI/CD pipelines for consistent code quality.

---

# Conclusion

This task introduced two essential tools used in modern Python and MLOps projects: **Ruff** for code quality analysis and **Black** for automatic formatting. Together, they help teams maintain clean, consistent, and maintainable code. In enterprise environments, these tools are commonly integrated with Git hooks and CI/CD pipelines to ensure that only high-quality code is merged and deployed.

