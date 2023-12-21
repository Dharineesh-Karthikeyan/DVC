# Advanced DVC

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
