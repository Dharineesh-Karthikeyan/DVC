# DVC
DVC (Data Version Control) - CI/CD For ML

### What is Data Versioning
- Versioning provides history record of changes to data.
- It is crucial for :
    - Reproducibility
    - Experimentation and Iteration
    - Collaboration
    - Bug Tracking and Debugging
    - Model Monitoring and Maintenance
    - Auditing and Validation

### DVC (Data Version Control)
- It is an open source tool to manage data (Similar to git).
- The strenght of DVC is its integration with Git.
- Git can manage the code versioning, while DVC manages the data versioning. Hence, providing us the means to manage both in a unified manner.
  <img width="519" alt="image" src="https://github.com/Dharineesh-Karthikeyan/DVC/assets/12586329/21def4cf-9e99-4b5e-a64a-c2d261a1520f">

### DVC Storage
- DVC enables us to maintain records of various data versions using Git, while the actual storage is stored seperately.
- Hence, the storage can be configured by variety of back-end storages such as:
      - SSH, Web APIs, Local file system
      - AWS, GCP, Azure storages

### DVC Initialization
- If we need to install DVC, follow
```python
pip install dvc
```

**Step 1** : DVC works in conjunction with Git, hence we need to initialize git first.
  `git status`
** Step 2** : Initialize DVC
  `dvc init`

### Adding Files to DVC
- Add files using `dvc add <file> ` command.

- This generates a .dvc file. This contains the file metadata.
- We version these .dvc files with Git,we keep track of data changes and manage to keep it lightweight.
- DVC cache is populated in `.dvc/cache`.

### DVC Data Files
- The DVC files contain the "outs" which contain the following data:
    - md5 : The MD5 checksum of the tracked data file. The MD5 is a cryptographic hash function that is a unique value generated based on the content of the file.
    - size : The size of the tracked data file in bytes.
    - hash : Specifies the type of hash function used to calculate checksum.
    - path : File path of the tracked data file.
