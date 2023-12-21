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
