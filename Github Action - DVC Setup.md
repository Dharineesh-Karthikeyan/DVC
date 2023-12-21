# Setup CML Github Action for DVC Pipeline

### DVC Pipeline
We shall set up a DVC pipeline based on the Metrics and Plots example. The Pipeline for the ML task is as such,

```yaml
stages:
  preprocess:
    cmd: python3 preprocess_dataset.py
    deps:
    - preprocess_dataset.py
    - data/raw_data.csv
    outs:
    - data/processed_data.csv

  train:
    cmd: python3 train.py
    deps:
    - metrics_and_plots.py
    - data/processed_data.csv
    - train.py

    metrics:
      - metrics.json:
          cache: false

    plots:
    - predictions.csv:
        template: confusion
        x: y_pred
        y: y_true
        x_label: 'Predicted label'
        y_label: 'True label'
        title: Confusion matrix
        cache: false
    - roc_curve.csv:
        template: simple
        x: fpr
        y: tpr
        x_label: 'False Positive Rate'
        y_label: 'True Positive Rate'
        title: ROC curve
        cache: false
```

### CML Github Action

The main steps to setup DVC and execute the pipeline are :
1. Setup DVC GitHub Action `iterative/setup-dvc@v1.`
2. Run DVC pipeline in Run DVC pipeline step using `dvc repro` command

The CML Github Action yaml file looks like,
```yaml
name: dvc-pipeline

on:
  pull_request:
    branches: main

permissions: write-all

jobs:
  train_and_report_eval_performance:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 
        uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: 3.9

      - name: Setup CML
        uses: iterative/setup-cml@v1
          
      # Setup DVC GitHub Action
      - name: Setup DVC
        uses: iterative/setup-dvc@v1
          
      # Run DVC pipeline
      - name: Run DVC pipeline
        run: dvc repro

      - name: Write CML report
        env:
          REPO_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          # Compare metrics with main branch and write in the markdown report
          git fetch --prune
          dvc metrics diff --md main >> metrics_compare.md
          
          # Create comment from markdown report
          cml comment create metrics_compare.md
```

