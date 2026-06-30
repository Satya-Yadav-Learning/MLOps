# TaskAndResolution_PreCommitHooks.md

# Configure Pre-Commit Hooks for an ML Repository

---

# Project Overview

As Machine Learning projects grow, multiple developers contribute code simultaneously. Maintaining consistent code quality becomes difficult if every developer follows different coding styles.

To solve this problem, organizations use **pre-commit**, which automatically validates code before every Git commit.

This task focused on configuring **pre-commit** to run multiple quality checks automatically.

---

# Business Requirement

A Git repository already contained a `.pre-commit-config.yaml` file, but it was incorrectly configured.

The objectives were:

* Configure five required hooks.
* Use current hook repositories.
* Pin every repository to a release using `rev`.
* Install Git hooks.
* Execute all hooks successfully.

---

# Learning Objectives

After completing this task, you should understand:

* Git Hooks
* Pre-Commit Framework
* YAML configuration
* Ruff integration
* Black integration
* Automatic code quality
* Git workflow automation

---

# Architecture

```text
                Developer
                     │
               git commit
                     │
                     ▼
            Pre-Commit Framework
                     │
   ┌─────────┬────────┬────────┬────────┬────────┐
   ▼         ▼        ▼        ▼        ▼
Trailing  EOF Fix  YAML     Ruff    Black
Spaces              Check
   │         │        │        │        │
   └─────────┴────────┴────────┴────────┘
                     │
                     ▼
               Commit Accepted
```

---

# Why Did the Company Ask for This?

Without pre-commit:

* Developers commit badly formatted code.
* Unused imports reach production.
* YAML syntax errors break CI/CD.
* Code reviews become time-consuming.

With pre-commit:

* Problems are detected before the commit.
* Developers fix issues immediately.
* Code quality remains consistent.

---

# Understanding Pre-Commit

## What is Pre-Commit?

Pre-Commit is a framework that automatically runs validation scripts before Git creates a commit.

Instead of relying on developers to remember every check manually, Git executes them automatically.

---

# Hooks Used

| Hook                | Purpose                          |
| ------------------- | -------------------------------- |
| trailing-whitespace | Removes trailing spaces          |
| end-of-file-fixer   | Ensures files end with a newline |
| check-yaml          | Validates YAML syntax            |
| ruff                | Performs Python linting          |
| black               | Formats Python code              |

---

# Step-by-Step Solution

## Step 1 – Navigate to Repository

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

## Step 2 – Review Existing Configuration

```bash
cat .pre-commit-config.yaml
```

Problems identified:

* Old hook versions
* Incorrect hook IDs
* Wrong Ruff repository
* Missing `rev` for Black
* Outdated YAML hook name

---

## Step 3 – Update Configuration

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.15.20
    hooks:
      - id: ruff

  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: 26.5.1
    hooks:
      - id: black
```

Explanation:

### pre-commit-hooks

Provides general-purpose Git hooks.

### Ruff

Checks Python code quality.

### Black

Automatically formats Python code.

---

## Step 4 – Update Hook Versions

```bash
pre-commit autoupdate
```

Representative Output

```text
Updating hook versions...
```

Purpose:

Updates every `rev:` field to the latest released version.

---

## Step 5 – Install Hooks

```bash
pre-commit install
```

Expected Output

```text
pre-commit installed at .git/hooks/pre-commit
```

Purpose:

Registers Git hooks inside the local repository.

---

## Step 6 – Execute Hooks

```bash
pre-commit run --all-files
```

First Run

```text
trim trailing whitespace........Failed

Fixing process.py
```

Explanation:

The hook detected trailing spaces and automatically corrected them.

---

## Step 7 – Execute Again

```bash
pre-commit run --all-files
```

Final Output

```text
trim trailing whitespace........Passed
fix end of files................Passed
check yaml......................Passed
ruff............................Passed
black...........................Passed
```

Task completed successfully.

---

# Important Commands

```bash
cd /root/code/fraud-detection

cat .pre-commit-config.yaml

pre-commit autoupdate

pre-commit install

pre-commit run --all-files

cat process.py
```

---

# Linux Concepts

## cat

Displays file contents.

---

## Git Hooks

Scripts executed automatically during Git operations.

---

# Python Concepts

## Ruff

Python linter.

Checks:

* unused imports
* style issues
* syntax problems
* import ordering

---

## Black

Python formatter.

Automatically formats:

* spacing
* indentation
* line wrapping

---

## YAML

Human-readable configuration language used by pre-commit.

---

# DevOps vs MLOps Comparison

| DevOps                | Python / MLOps          |
| --------------------- | ----------------------- |
| terraform fmt         | Black                   |
| tflint                | Ruff                    |
| checkov               | Ruff + Additional Hooks |
| Jenkins Validation    | Pre-Commit              |
| GitHub Actions Checks | Pre-Commit Hooks        |

---

# Enterprise Workflow

```text
Developer
      │
git commit
      │
      ▼
Trailing Whitespace
      │
      ▼
EOF Fixer
      │
      ▼
YAML Validation
      │
      ▼
Ruff
      │
      ▼
Black
      │
      ▼
Commit Accepted
```

Only code that passes every hook can proceed to the next stage.

---

# Common Errors

| Error                 | Reason                | Solution                        |
| --------------------- | --------------------- | ------------------------------- |
| Missing `rev`         | Repository not pinned | Add `rev` field                 |
| Wrong Ruff repository | Old repository used   | Use `astral-sh/ruff-pre-commit` |
| `check_yaml`          | Incorrect hook ID     | Use `check-yaml`                |
| Hook modifies file    | Auto-fix applied      | Run hooks again                 |
| Hook failure          | Code quality issue    | Fix reported problems           |

---

# Best Practices

* Pin hook versions using `rev`.
* Run `pre-commit autoupdate` periodically.
* Execute hooks locally before pushing code.
* Keep `.pre-commit-config.yaml` under version control.
* Integrate pre-commit into CI/CD pipelines.

---

# Scenario-Based Interview Questions & Answers

## L1 – Junior Engineer

### Q1. What is pre-commit?

**Answer:**

Pre-commit is a framework that automatically runs validation scripts before Git creates a commit.

---

### Q2. Why use Git Hooks?

**Answer:**

Git Hooks automate quality checks, preventing incorrect or poorly formatted code from entering the repository.

---

## L2 – Mid-Level Engineer

### Q1. Why did the first `pre-commit run` fail?

**Answer:**

The `trailing-whitespace` hook detected extra spaces at the end of a line, fixed the file automatically, and exited so the developer could review the changes. Running the hooks again confirmed that all issues were resolved.

---

### Q2. Why use `pre-commit autoupdate`?

**Answer:**

It updates every hook to the latest released version by rewriting the `rev` values in the configuration file.

---

## L3 – Senior Engineer

### Q1. How would you enforce code quality across multiple teams?

**Answer:**

Use shared pre-commit configurations, enforce them with Git hooks, integrate them into CI/CD pipelines, and reject pull requests that fail validation.

---

### Q2. Why combine Ruff and Black in pre-commit?

**Answer:**

Ruff detects code-quality issues, while Black enforces consistent formatting. Running both before every commit ensures high-quality and maintainable code.

---

## L4 – Architect

### Q1. How would you standardize development across hundreds of repositories?

**Answer:**

Maintain a centralized pre-commit configuration template, enforce version pinning, integrate automated updates, and validate all repositories through CI/CD quality gates.

---

### Q2. Why is pre-commit important in enterprise MLOps?

**Answer:**

Machine Learning systems rely on collaborative development. Automated pre-commit checks improve consistency, reduce technical debt, catch errors early, and prevent poor-quality code from entering production pipelines.

---

# Cheat Sheet

```bash
cd /root/code/fraud-detection

cat .pre-commit-config.yaml

pre-commit autoupdate

pre-commit install

pre-commit run --all-files

cat process.py
```

---

# Key Takeaways

* Pre-commit automates code validation before Git commits.
* Git Hooks improve code quality and consistency.
* `pre-commit autoupdate` keeps hook versions current.
* Ruff performs linting.
* Black formats Python code.
* Some hooks automatically modify files, requiring a second run.
* Pre-commit is widely used in enterprise Python, DevOps, and MLOps projects.

---

# Conclusion

This task introduced **Git Hook automation** using the Pre-Commit framework. By configuring and validating hooks for whitespace cleanup, file formatting, YAML validation, linting, and code formatting, the project now enforces consistent quality before every commit. This practice reduces manual reviews, prevents common mistakes, and integrates naturally into modern CI/CD and MLOps workflows.

