# Data Version Control (DVC) with Git - Versioning Dataset using Local Remote

This project demonstrates how to use **Git** and **DVC (Data Version Control)** together for versioning datasets. Git tracks the source code while DVC tracks the data files.

---

# Prerequisites

- Git installed
- Python 3.x
- pip
- DVC

Install DVC:

```bash
pip install dvc
```

---

# Project Workflow

## Step 1: Create Git Repository

Create a new repository and clone it locally.

```bash
git init
# OR create on GitHub and clone

git clone <repository-url>

cd <repository-name>
```

---

## Step 2: Create Python Script

Create a file named

```
mycode.py
```

This script should generate a CSV file inside a new folder named

```
data/
```

Example project structure:

```
project/
│
├── mycode.py
├── data/
│   └── sample.csv
```

Run the script.

```bash
python mycode.py
```

---

## Step 3: Save Initial Project to Git

Before initializing DVC, commit everything.

```bash
git add .
git commit -m "Initial project with generated data"
git push
```

---

## Step 4: Initialize DVC

```bash
dvc init
```

This creates

```
.dvc/
.dvcignore
```

Commit these changes.

```bash
git add .
git commit -m "Initialize DVC"
```

---

## Step 5: Create Local DVC Remote

Create a directory that will act as the storage backend.

```bash
mkdir S3
```

---

## Step 6: Add DVC Remote

```bash
dvc remote add -d myremote S3
```

Commit the configuration.

```bash
git add .dvc/config
git commit -m "Configure DVC remote"
```

---

## Step 7: Add Dataset to DVC

Run

```bash
dvc add data/
```

### Expected Error

Since Git is already tracking the `data/` folder, DVC will display an error similar to:

```text
ERROR: output 'data' is already tracked by SCM.
```

Follow the suggested commands:

```bash
git rm -r --cached data
git commit -m "Stop tracking data with Git"
```

Now Git no longer tracks the dataset directly.

---

## Step 8: Add Dataset Again

```bash
dvc add data/
```

This generates

```
data.dvc
```

Git should now track only:

- `data.dvc`
- `.gitignore`

Commit them.

```bash
git add .gitignore data.dvc
git commit -m "Track dataset using DVC"
```

---

## Step 9: Push Data to DVC Remote

Commit the current DVC state.

```bash
dvc commit
```

Upload data to the remote.

```bash
dvc push
```

---

## Step 10: Save First Version

Commit the updated DVC metadata.

```bash
git add .
git commit -m "Dataset Version 1"
git push
```

At this stage:

- Git stores metadata.
- DVC stores actual dataset files.

---

# Creating Dataset Version 2

## Step 11: Modify Dataset

Update `mycode.py` so it appends a new row to the CSV.

Run the script again.

```bash
python mycode.py
```

Check DVC status.

```bash
dvc status
```

---

## Step 12: Commit Dataset Changes

```bash
dvc commit
dvc push
```

---

## Step 13: Save Version 2

```bash
git add .
git commit -m "Dataset Version 2"
git push
```

---

## Step 14: Verify Repository Status

```bash
git status
```

```bash
dvc status
```

Everything should be up to date.

---

# Creating Dataset Version 3

Repeat the previous workflow.

## Modify dataset

```bash
python mycode.py
```

Check status

```bash
dvc status
```

Commit changes

```bash
dvc commit
dvc push
```

Save Version 3

```bash
git add .
git commit -m "Dataset Version 3"
git push
```

---

# View Git History

```bash
git log --oneline
```

Example

```
91c0abc Dataset Version 3
63a8d21 Dataset Version 2
27efad0 Dataset Version 1
b61d3fa Track dataset using DVC
...
```

---

# Restore an Older Dataset Version

Checkout the desired Git commit.

```bash
git checkout <commit-sha>
```

Or switch back to the latest version.

```bash
git checkout main
```

---

## Download the Corresponding Dataset

After checking out the desired Git commit, restore the matching dataset.

```bash
dvc pull
```

DVC downloads the correct version of the data from the configured remote.

---

# Final Project Structure

```
project/
│
├── .dvc/
├── .dvcignore
├── .gitignore
├── data/
├── data.dvc
├── mycode.py
├── S3/
└── README.md
```

---

# Useful Commands

| Task | Command |
|------|----------|
| Initialize DVC | `dvc init` |
| Add data | `dvc add data/` |
| Check status | `dvc status` |
| Commit data changes | `dvc commit` |
| Push data | `dvc push` |
| Pull data | `dvc pull` |
| List Git commits | `git log --oneline` |
| Switch versions | `git checkout <commit>` |

---

# Workflow Summary

```text
Create Git Repo
        │
        ▼
Generate Data
        │
        ▼
Git Commit
        │
        ▼
Initialize DVC
        │
        ▼
Configure Remote
        │
        ▼
Track Data with DVC
        │
        ▼
DVC Push
        │
        ▼
Git Commit (Version 1)
        │
        ▼
Modify Data
        │
        ▼
DVC Commit + Push
        │
        ▼
Git Commit (Version 2)
        │
        ▼
Modify Data Again
        │
        ▼
DVC Commit + Push
        │
        ▼
Git Commit (Version 3)
        │
        ▼
Checkout Any Git Commit
        │
        ▼
dvc pull
        │
        ▼
Dataset Restored
```

---

## Key Takeaways

- **Git** tracks source code and DVC metadata (`*.dvc`, configuration files).
- **DVC** tracks large datasets efficiently.
- The `S3/` directory acts as the DVC remote storage in this example.
- Each Git commit corresponds to a specific version of the dataset, allowing you to restore any version using `git checkout` followed by `dvc pull`.
