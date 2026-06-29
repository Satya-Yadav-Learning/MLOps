# TaskAndResolution_PackageMLProject.md

# Package an ML Project as an Installable Python Distribution

---

# Project Overview

In enterprise Machine Learning environments, source code is not shared by copying project folders. Instead, it is packaged into an installable Python distribution (Wheel) so that Data Scientists, ML Engineers, QA teams, and Deployment teams can install and use the same version of the project consistently.

This task focuses on configuring a Python project using **pyproject.toml** and building an installable package.

---

# Business Requirement

A Machine Learning project already exists under the `src/` directory, but it cannot be packaged according to the organization's standards.

Your objectives were:

* Configure the Python build system.
* Correct project metadata.
* Define supported Python version.
* Add project dependencies.
* Build an installable Python package (.whl).

---

# Learning Objectives

After completing this task, you should understand:

* Python packaging
* `pyproject.toml`
* Build systems
* Wheel (`.whl`) packages
* Source Distribution (`.tar.gz`)
* Package metadata
* Python dependencies

---

# Architecture

```text
                Developer
                     │
                     ▼
              Python Source Code
                     │
                     ▼
             pyproject.toml
                     │
                     ▼
            python3 -m build
                     │
        ┌────────────┴─────────────┐
        │                          │
        ▼                          ▼
Source Distribution          Wheel Package
   (.tar.gz)                   (.whl)
        │                          │
        └────────────┬─────────────┘
                     ▼
            Package Repository
                     │
                     ▼
          Production Deployment
```

---

# Why Did the Company Ask for This?

Without packaging:

* Developers copy project folders manually.
* Different environments may use different code versions.
* Dependency management becomes difficult.
* CI/CD pipelines cannot easily distribute the application.

Packaging provides:

* Version control
* Easy installation
* Reproducible deployments
* Standard distribution format

---

# Step-by-Step Solution

## Step 1 – Navigate to Project Directory

```bash
cd /root/code/fraud-detection
```

Verify the location:

```bash
pwd
```

Expected Output

```text
/root/code/fraud-detection
```

---

## Step 2 – Verify Project Structure

```bash
ls -R
```

Representative Output

```text
.
├── pyproject.toml
└── src
    └── fraud_detection
        ├── __init__.py
        └── predict.py
```

Purpose:

* Verify package source exists.
* Ensure project follows the expected layout.

---

## Step 3 – Review Existing Configuration

```bash
cat pyproject.toml
```

Problems identified:

* Missing `[build-system]`
* Incorrect package name
* Incorrect version
* Wrong Python version
* Missing dependencies

---

## Step 4 – Update pyproject.toml

```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "fraud_detection"
version = "0.1.0"
description = "Fraud detection model"
requires-python = ">=3.10"
dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

[tool.setuptools.packages.find]
where = ["src"]
```

Explanation:

### Build System

Specifies the tools used to build the package.

### Project Name

Must match the package directory:

```text
src/fraud_detection
```

### Version

Defines the release version.

### Python Version

Restricts installation to compatible Python versions.

### Dependencies

Automatically installed when users install the package.

---

## Step 5 – Build the Package

```bash
python3 -m build
```

Representative Output

```text
Creating isolated environment...
Building sdist...
Building wheel...
Successfully built
```

Purpose:

Creates:

* Source Distribution (`.tar.gz`)
* Wheel Package (`.whl`)

---

## Step 6 – Verify Build Artifacts

```bash
ls -ltr dist
```

Representative Output

```text
fraud_detection-0.1.0.tar.gz
fraud_detection-0.1.0-py3-none-any.whl
```

Task completed successfully.

---

# Important Commands

```bash
cd /root/code/fraud-detection

ls -R

cat pyproject.toml

vi pyproject.toml

python3 -m build

ls -ltr dist
```

---

# Linux Concepts

## ls -R

Displays the complete directory hierarchy.

---

## cat

Displays file contents.

---

## vi

Edits configuration files.

---

# Python Packaging Concepts

## What is pyproject.toml?

It is the standard configuration file for modern Python projects.

It defines:

* Build system
* Metadata
* Dependencies
* Packaging configuration

---

## What is setuptools?

A Python packaging library that builds distributable packages.

---

## What is wheel?

A binary distribution format (`.whl`) that allows fast installation without rebuilding source code.

---

## What is an sdist?

A Source Distribution (`.tar.gz`) containing the project source code.

---

# DevOps vs MLOps Comparison

| Traditional DevOps | Python / MLOps                  |
| ------------------ | ------------------------------- |
| pom.xml            | pyproject.toml                  |
| Maven              | setuptools                      |
| mvn package        | python3 -m build                |
| JAR                | Wheel (.whl)                    |
| Nexus              | PyPI / Private Package Registry |

---

# Enterprise Use Case

```text
Developer
      │
Git Repository
      │
CI/CD Pipeline
      │
python3 -m build
      │
Wheel Package
      │
Artifact Repository
      │
Deployment
```

The deployment pipeline installs the generated wheel instead of copying source code manually.

---

# Common Errors

| Error                | Reason                       | Solution                     |
| -------------------- | ---------------------------- | ---------------------------- |
| Missing build-system | Build backend undefined      | Add `[build-system]` section |
| Wrong package name   | Does not match module path   | Use `fraud_detection`        |
| Incorrect version    | Metadata mismatch            | Set `0.1.0`                  |
| Wrong Python version | Does not satisfy requirement | Use `>=3.10`                 |
| Missing dependencies | Empty dependency list        | Add required packages        |

---

# Best Practices

* Use `pyproject.toml` for all modern Python projects.
* Match the distribution name with the package directory when required.
* Use semantic versioning.
* Keep dependencies minimal.
* Build packages using `python3 -m build`.
* Store packages in an artifact repository.

---

# Scenario-Based Interview Questions & Answers

## L1 – Junior Engineer

### Q1. What is a Wheel?

**Answer:**
A Wheel (`.whl`) is an installable Python package format that allows users to install applications or libraries quickly without rebuilding source code.

---

### Q2. What is `pyproject.toml`?

**Answer:**
It is the standard configuration file used to define project metadata, dependencies, and build settings for Python packages.

---

## L2 – Mid-Level Engineer

### Q1. Why use `python3 -m build`?

**Answer:**
It creates both a source distribution (`.tar.gz`) and a wheel (`.whl`) using the configured build backend, ensuring standardized package creation.

---

### Q2. Why specify `requires-python`?

**Answer:**
It prevents installation on unsupported Python versions and ensures compatibility.

---

## L3 – Senior Engineer

### Q1. Why distribute Wheels instead of source code?

**Answer:**
Wheel packages provide faster installation, consistent deployments, dependency management, and easier integration with CI/CD pipelines.

---

### Q2. How would you automate package creation?

**Answer:**
Integrate `python3 -m build` into a CI/CD pipeline, publish artifacts to a private package repository, and trigger deployments from versioned releases.

---

## L4 – Architect

### Q1. How would you manage internal Python packages across multiple teams?

**Answer:**
Use a centralized artifact repository (such as an internal PyPI server or Artifactory), semantic versioning, automated CI/CD builds, and governance policies to ensure secure and reproducible package distribution.

---

### Q2. Why is packaging important in enterprise MLOps?

**Answer:**
Packaging ensures reproducibility, standardized deployments, dependency consistency, version control, and seamless integration into automated ML pipelines.

---

# Cheat Sheet

```bash
cd /root/code/fraud-detection

cat pyproject.toml

python3 -m build

ls -ltr dist
```

---

# Key Takeaways

* `pyproject.toml` is the standard configuration file for Python packaging.
* `setuptools` builds Python packages.
* `wheel` creates installable `.whl` files.
* `python3 -m build` generates both source and wheel distributions.
* Wheel packages simplify installation and deployment.
* Packaging is a core practice in production MLOps environments.

---

# Conclusion

This task introduced Python packaging, a fundamental skill for MLOps engineers. By configuring the build system, defining project metadata, managing dependencies, and generating a wheel package, the ML project became portable, versioned, and ready for deployment. Packaging enables reproducible installations, integrates naturally with CI/CD pipelines, and supports enterprise-scale software distribution.

