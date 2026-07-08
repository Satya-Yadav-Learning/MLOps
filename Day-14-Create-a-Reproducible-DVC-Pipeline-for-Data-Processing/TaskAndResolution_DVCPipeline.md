# TaskAndResolution_DVCPipeline.md

# Create a Reproducible DVC Pipeline for Data Processing

---

# Project Overview

Modern Machine Learning projects consist of multiple dependent steps such as:

- Data Collection
- Data Cleaning
- Feature Engineering
- Dataset Splitting
- Model Training
- Model Evaluation
- Model Deployment

Running these scripts manually every time is inefficient and error-prone.

**DVC Pipelines** solve this problem by defining dependencies between stages so the entire workflow can be reproduced using a single command.

In this task, a two-stage DVC pipeline was created for a fraud detection project. The pipeline processes raw transaction data and then splits it into training and testing datasets.

---

# Business Requirement

The ML Engineering team wants the complete data preparation workflow to be reproducible.

Requirements:

- Create two DVC stages.
- Stage 1 should process raw data.
- Stage 2 should split processed data.
- Define dependencies correctly.
- Execute the pipeline using `dvc repro`.
- Generate `dvc.lock`.
- Ensure `dvc status` reports no stale stages.

---

# Learning Objectives

After completing this task you will understand:

- DVC Pipeline
- DVC Stage
- dvc.yaml
- dvc.lock
- Dependencies
- Outputs
- Pipeline Reproducibility
- Data Lineage

---

# Production Architecture

```text
                  Raw Dataset
                       │
                       ▼
        +-----------------------------+
        |      process_data Stage      |
        +-----------------------------+
                       │
                       ▼
          clean_transactions.csv
                       │
                       ▼
        +-----------------------------+
        |       split_data Stage       |
        +-----------------------------+
                │               │
                ▼               ▼
          train.csv        test.csv
```

---

# Why Companies Use DVC Pipelines

Without DVC:

```text
Engineer

↓

Run Script 1

↓

Run Script 2

↓

Run Script 3
```

Problems:

- Manual execution
- Wrong execution order
- Difficult troubleshooting
- Poor reproducibility

With DVC:

```bash
dvc repro
```

DVC automatically:

- Determines execution order.
- Runs only necessary stages.
- Tracks dependencies.
- Produces identical outputs every time.

---

# Initial Repository Structure

Navigate to the project.

```bash
cd /root/code/fraud-detection
```

Verify project structure.

```bash
ls -R
```

Output

```text
.
├── data
│   └── raw
│       └── transactions.csv
└── src
    └── data
        ├── process_data.py
        └── split_data.py
```

Explanation

The repository contains:

- Raw dataset
- Processing script
- Splitting script

No DVC pipeline has been defined yet.

---

# Step 1 – Create First Pipeline Stage

Command

```bash
dvc stage add \
-n process_data \
-d data/raw/transactions.csv \
-d src/data/process_data.py \
-o data/processed/clean_transactions.csv \
python3 src/data/process_data.py
```

Output

```text
Could not create .gitignore entry...
Added stage 'process_data' in 'dvc.yaml'

To track the changes with git, run:

git add dvc.yaml
```

Explanation

The first stage was added.

This stage defines:

Command

```text
python3 src/data/process_data.py
```

Dependencies

- Raw dataset
- Python script

Output

```text
clean_transactions.csv
```

The warning about `.gitignore` occurred because the `data/processed/` directory did not yet exist. This is expected and resolved automatically when the stage executes.

---

# Step 2 – Create Second Pipeline Stage

Command

```bash
dvc stage add \
-n split_data \
-d data/processed/clean_transactions.csv \
-d src/data/split_data.py \
-o data/processed/train.csv \
-o data/processed/test.csv \
python3 src/data/split_data.py
```

Output

```text
Added stage 'split_data' in 'dvc.yaml'
```

Explanation

The second stage depends on the output of the first stage.

This creates the dependency chain:

```text
transactions.csv

↓

clean_transactions.csv

↓

train.csv
test.csv
```

---

# Step 3 – Execute Pipeline

Command

```bash
dvc repro
```

Output

```text
Running stage 'process_data'

Processed 15 rows

Generating lock file 'dvc.lock'

Updating lock file 'dvc.lock'

Running stage 'split_data'

Train: 12 rows
Test: 3 rows

Updating lock file 'dvc.lock'
```

Explanation

DVC automatically executed the stages in dependency order.

Stage 1

Generated

```text
clean_transactions.csv
```

Stage 2

Generated

```text
train.csv

test.csv
```

The entire workflow completed successfully.

---

# Step 4 – Verify Generated Files

Command

```bash
ls -R data
```

Output

```text
data
├── processed
│   ├── clean_transactions.csv
│   ├── train.csv
│   └── test.csv
└── raw
    └── transactions.csv
```

Explanation

The pipeline successfully generated all required output datasets.

---

# Step 5 – Review dvc.yaml

Command

```bash
cat dvc.yaml
```

Output

```yaml
stages:
  process_data:
    cmd: python3 src/data/process_data.py
    deps:
      - data/raw/transactions.csv
      - src/data/process_data.py
    outs:
      - data/processed/clean_transactions.csv

  split_data:
    cmd: python3 src/data/split_data.py
    deps:
      - data/processed/clean_transactions.csv
      - src/data/process_data.py
    outs:
      - data/processed/train.csv
      - data/processed/test.csv
```

Explanation

`dvc.yaml` defines the pipeline structure.

It tells DVC:

- What to execute.
- What inputs are required.
- What outputs are generated.
- Which stages depend on previous stages.

---

# Step 6 – Review dvc.lock

Command

```bash
cat dvc.lock
```

Output (shortened)

```yaml
schema: '2.0'

stages:

process_data

deps:

transactions.csv

process_data.py

outs:

clean_transactions.csv

split_data

deps:

clean_transactions.csv

split_data.py

outs:

train.csv

test.csv
```

Explanation

`dvc.lock` stores:

- MD5 hashes
- File sizes
- Dependency checksums
- Output checksums

This guarantees reproducibility.

---

# How DVC Decides Whether to Run a Stage

Example

Current hash

```text
transactions.csv

↓

MD5 = abc123
```

Later

```text
transactions.csv

↓

MD5 = xyz999
```

Hashes differ.

DVC automatically reruns:

```text
process_data

↓

split_data
```

because the downstream stage depends on the upstream output.

---

# Dependency Graph

```text
transactions.csv
        │
        ▼
process_data.py
        │
        ▼
clean_transactions.csv
        │
        ▼
split_data.py
        │
   ┌────┴────┐
   ▼         ▼
train.csv  test.csv
```

---

# Files Generated

| File | Purpose |
|-------|----------|
| dvc.yaml | Pipeline definition |
| dvc.lock | Pipeline execution metadata |
| clean_transactions.csv | Processed dataset |
| train.csv | Training dataset |
| test.csv | Testing dataset |

---

# Linux Commands Used

```bash
cd
ls
cat
pwd
find
```

---

# DVC Commands Used

```bash
dvc stage add
dvc repro
```

---

# Important DVC Files

## dvc.yaml

Defines:

- Stages
- Dependencies
- Outputs
- Commands

---

## dvc.lock

Stores:

- MD5 hashes
- File sizes
- Dependency information
- Output information

Generated automatically after running:

```bash
dvc repro
```

---

# Difference Between dvc.yaml and dvc.lock

| dvc.yaml | dvc.lock |
|------------|------------|
| Written by user | Generated automatically |
| Defines workflow | Records execution |
| Editable | Normally not edited |
| Human readable | Metadata for reproducibility |

---

# AWS DevOps Comparison

| AWS DevOps | MLOps |
|------------|--------|
| Jenkins Pipeline | DVC Pipeline |
| Jenkinsfile | dvc.yaml |
| Build Metadata | dvc.lock |
| Build Stage | DVC Stage |
| make all | dvc repro |

---

# Real Production Workflow

```text
Raw Data

↓

Cleaning

↓

Feature Engineering

↓

Train/Test Split

↓

Model Training

↓

Model Evaluation

↓

Model Registry

↓

Deployment

↓

Monitoring
```

Most enterprise ML pipelines contain many more stages than this lab.

---

# Common Errors

| Error | Cause | Solution |
|--------|--------|-----------|
| Stage not found | Wrong stage name | Verify stage name |
| Missing dependency | Incorrect dependency path | Update dependency |
| Missing output | Script failed | Debug Python script |
| dvc repro reruns everything | Input file changed | Expected behaviour |
| Pipeline not updating | Forgot dvc repro | Execute pipeline |

---

# Best Practices

- Keep one responsibility per stage.
- Use meaningful stage names.
- Track all dependencies.
- Track every generated artifact.
- Commit `dvc.yaml` and `dvc.lock`.
- Avoid manually modifying output files.

---

# Scenario-Based Interview Questions & Answers

# L1 – Junior Engineer

## Q1. What is a DVC Pipeline?

**Answer**

A DVC Pipeline is a workflow that defines multiple dependent stages of an ML project. Each stage specifies the command to run, its inputs, and its outputs, enabling reproducible execution.

---

## Q2. What does `dvc repro` do?

**Answer**

`dvc repro` executes the pipeline by running only the stages whose dependencies have changed, ensuring efficient and reproducible processing.

---

# L2 – Mid-Level Engineer

## Q1. Why do we define dependencies in DVC?

**Answer**

Dependencies allow DVC to detect changes in input files or scripts. If a dependency changes, DVC automatically reruns the affected stage and any downstream stages.

---

## Q2. Why does DVC create `dvc.lock`?

**Answer**

`dvc.lock` records hashes, file sizes, and execution metadata so that future executions can determine whether stages are already up to date.

---

# L3 – Senior Engineer

## Q1. How does DVC decide which stages to execute?

**Answer**

DVC compares the current hashes of dependencies with the hashes stored in `dvc.lock`. If a dependency has changed, DVC reruns that stage and all stages that depend on its outputs.

---

## Q2. Why separate `dvc.yaml` and `dvc.lock`?

**Answer**

`dvc.yaml` describes the intended workflow, while `dvc.lock` records the exact state of a successful execution. This separation improves reproducibility and collaboration.

---

# L4 – Architect

## Q1. Design a production DVC pipeline for an ML platform.

**Answer**

A production pipeline typically consists of data ingestion, validation, preprocessing, feature engineering, dataset splitting, model training, evaluation, model registration, deployment, and monitoring. Each stage is tracked in `dvc.yaml`, while datasets and models are versioned using DVC remotes such as Amazon S3, Azure Blob Storage, or SeaweedFS. CI/CD systems trigger `dvc repro` whenever relevant data or code changes.

---

## Q2. How does DVC improve reproducibility compared to manually running Python scripts?

**Answer**

DVC captures dependencies, outputs, commands, and execution metadata. It reruns only the necessary stages based on dependency changes and ensures every engineer can reproduce identical results using the same pipeline definition and tracked data versions.

---

# Cheat Sheet

```bash
cd /root/code/fraud-detection

dvc stage add -n process_data \
-d data/raw/transactions.csv \
-d src/data/process_data.py \
-o data/processed/clean_transactions.csv \
python3 src/data/process_data.py

dvc stage add -n split_data \
-d data/processed/clean_transactions.csv \
-d src/data/split_data.py \
-o data/processed/train.csv \
-o data/processed/test.csv \
python3 src/data/split_data.py

dvc repro

ls -R data

cat dvc.yaml

cat dvc.lock

dvc status
```

---

# Key Takeaways

- DVC Pipelines automate Machine Learning workflows.
- `dvc.yaml` defines the pipeline stages.
- `dvc.lock` stores execution metadata for reproducibility.
- `dvc repro` executes only the required stages based on dependency changes.
- Dependencies create automatic execution order.
- Pipelines eliminate manual execution errors and improve collaboration.
- DVC Pipelines are conceptually similar to Jenkins pipelines or Makefiles but are designed specifically for Machine Learning workflows.

---

# Conclusion

In this task, a reproducible two-stage DVC pipeline was created for data processing and dataset splitting. The pipeline definition was stored in `dvc.yaml`, execution metadata was recorded in `dvc.lock`, and the workflow was executed successfully using `dvc repro`. By defining dependencies and outputs explicitly, DVC can automatically determine execution order, rerun only the necessary stages when inputs change, and guarantee reproducible ML data preparation across development, testing, and production environments.
