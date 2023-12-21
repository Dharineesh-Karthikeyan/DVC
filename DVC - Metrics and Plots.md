# DVC - Metrics and Plots

DVC can be used to track and compare the performance metrics and visualize plots between various versions of the ML task. 

### Metrics
To track the metrics, we need to configure the `dvc.yaml` file,

Before: (DVC Pipeline)
```yaml
stages:
  preprocess:
  ...

  train:
  ...
  outs:
    - metrics.json
    - confusion_matrix.png
```

After:
```yaml
stages:
  preprocess:
  ...

  train:
  ...
  outs:
    - confusion_matrix.png
  metrics:
    - metrics.json:
        cache:false
```
> The parameter cache: false, is to indicate that the metrics file must be tracked in Git and not DVC.

### Show Current Metrics
If we want to see the current metrics on the terminal (metrics.json will be displayed here)
```yaml
dvc metrics show
```
<img width="400" alt="image" src="https://github.com/Dharineesh-Karthikeyan/DVC/assets/12586329/779ab969-3ed6-435d-9e13-9d129cd48512">

### Comparing Metrics
If we make a change in the training and push the new version using `dvc repro`.
Next, we can compare metrics with the previous versions, by running
```yaml
dvc metrics diff
```

If we want to compare with a specific branch,
```yaml
dvc metrics diff main --md
```
> --md returns the result in a table format
<img width="400" alt="image" src="https://github.com/Dharineesh-Karthikeyan/DVC/assets/12586329/86647a3c-d86f-4563-9520-1bb6175b5c91">



___
### Plots
We can render plots in DVC using their templates such as `scatter, linear, simple, smooth, confusion, confusion_normalized, bar_horizontal, bar_horizontal_sorted`.
DVC does not track the image plots, but rather plots the data files.
The data file name serves as the target name which needs to be referenced under the `plot` keys.
```yaml
stages:
  train:
  ...
  plots:
    - predictions.csv: # Target data file
      template: confusion # Style of Plot
      x: y_pred
      y: y_true
      x_label: 'Prediction Values'
      y_label: 'True Values'
      title: Confusion Matrix
      cache: false
```
> The parameter cache: false, is to indicate that the metrics file must be tracked in Git and not DVC.

### Plotting the Plots
We can view the plots by referencing the target data file and,
```yaml
dvc plots show predictions.csv
```
By default, it returns the path to an HTML file, which is embedded with the interactive plots.

### Comparing Plots
In order to compare the plots, we can compare them between branches. Hence, we need to pass the target data file and the branch to compare it with,
```yaml
dvc plots diff --target predictions.csv main
```
This plots the confusion matrixes side-by-side and make it easier to compare them.

### Comparing Different Type of Plots:
Let's say we want to plot the ROC curve, we can do so with the help of a Python script.
```python
# Snippet of the python code
y_proba = model.predict_proba(x_test)
fpr,tpr,_ = roc_curve(y_test,y_proba)
```

The `dvc.yaml` file needs to be updated as well
```yaml
# Snippet of changes to yaml file
train:
...
  plots:
    - roc_curve.csv:
        template: linear # Linear type of graph
        x: fpr
        y: tpr
        x_label: 'False Positive Rate'
        y_label: 'True Positive Rate'
        title: 'ROC Curve'
        cache: false
```

