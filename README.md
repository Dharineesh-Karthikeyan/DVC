# DVC (Data Version Control)

### What is Data Versioning
- Versioning provides history record of changes to data.
- It is crucial for :
    - Reproducibility
    - Experimentation and Iteration
    - Collaboration
    - Bug Tracking and Debugging
    - Model Monitoring and Maintenance
    - Auditing and Validation

___

### DVC (Data Version Control)
- It is an open source tool to manage data (Similar to git).
- The strenght of DVC is its integration with Git.
- Git can manage the code versioning, while DVC manages the data versioning. Hence, providing us the means to manage both in a unified manner.

  
  <img width="400" alt="image" src="https://github.com/Dharineesh-Karthikeyan/DVC/assets/12586329/21def4cf-9e99-4b5e-a64a-c2d261a1520f">

___
### DVC Storage
- DVC enables us to maintain records of various data versions using Git, while the actual storage is stored seperately.
- Hence, the storage can be configured by variety of back-end storages such as:
      - SSH, Web APIs, Local file system
      - AWS, GCP, Azure storages

___
### DVC Initialization
If we need to install DVC, follow
```python
pip install dvc
```
<br></br>
**Step 1** : DVC works in conjunction with Git, hence we need to initialize git first.

`git status`

**Step 2** : Initialize DVC

`dvc init`

___
### Adding Files to DVC
- Add files using `dvc add <file> ` command.

- This generates a .dvc file. This contains the file metadata.
- We version these .dvc files with Git,we keep track of data changes and manage to keep it lightweight.
- DVC cache is populated in `.dvc/cache`.

___
### DVC Data Files
- The DVC files contain the "outs" which contain the following data:
    - md5 : The MD5 checksum of the tracked data file. The MD5 is a cryptographic hash function that is a unique value generated based on the content of the file.
    - size : The size of the tracked data file in bytes.
    - hash : Specifies the type of hash function used to calculate checksum.
    - path : File path of the tracked data file.
 
___
### DVC Remotes
- DVC uses external storage locations called DVC Remotes to track and share data and ML Models.
- Similar to Git Remote, but for cached data.
- Benefits:
    - Sync with large files and directories
    - Centralize and distribute data storage
    - Save Local Space
- Supported Storage Types
    - Amazon S3, GCS, Google Drive
    - SSH, HTTP/HTTPS, Local file system

____
### Setting Up DVC Remotes
- Set up using `dvc remote add` command.
- For example, we can set an AWS S3 bucket named "mybucket" as a remote DVC and name it "AWSremote"

    `dvc remote add AWSremote s3://mybucket`

- These add the remote and settings into the project's `.dvc/config`.
- If we want to add customized settings to the DVC remote, we do it as such

    `dvc remote modify AWSremote connect_timeout 300`

- This change will be reflected in the config file at `.dvc/config`
<br></br>

### Local and Default Remotes
- Use system directories, file systems, mounted drives or Network Attached Storage

    `dvc remote add localremote /tmp/dvc`

- In order to set this remote as the **default** remote,

    `dvc remote add -d localremote /tmp/dvc`

- Hence, all commands that require a remote such as `dvc push, dvc pull, dvc fetch` will be using this remote by default to upload or download data.

___
### Uploading and Downloading Data
The commands to transfer data are:
- Push to Remote : `dvc push <target>`
- Pull from Remote : `dvc pull <target>`

If we don't mention the target, all the data will be pushed or pulled.
To override the default remote,

    `dvc push -r AWSremote <target>`

> **NOTE : "target" here is the datafile and not metadata file**

___
### Tracking Data Changes
If the contents of the data file changes, the following steps are needed to keep track of the changes with DVC.

**Step 1** : Stage changes to the DVC cache (This also changes the .dvc file)

```
dvc add /path/to/datafile
```

**Step 2** : Stage and commit the new .dvc file to Git

```
git add /path/to/datafile.dvc
git commit /path/to/datafile.dvc -m "Dataset Updates"
```

**Step 3** : Push metadata to Git

```
git push origin main
```

**Step 4** : Push Changed Data File

```
dvc push 
```

___
### DVC Pipelines
- In actual ML use cases, data needs to be filtered, cleaned and transformed before training ML models.
- Additionally, there is no need to repeat steps and only run what we need.

- A DVC Pipeline is a structured sequence of stages that defines the workflow and dependencies for a ML or data processing task.
- These steps are defined in a `dvc.yaml` file. This configures the different stages of the pipeline.
    - `deps` : Input data and scripts (e.g preprocessing or training code files)
    - `cmd` : Stage Execution commands (e.g running Python scripts)
    - `outs` : Output artifacts (e.g processed dataset, metrics, plots)
- A little similar to the Github Actions workflow but more focused towards ML tasks.

### Defining Pipeline Stages
- Create stages using `dvc stage add`
  
```
dvc stage add \
-n preprocess \
-d data.csv -d preprocess.py \
-o processed_data.csv \
python preprocess.py
```

- 'n' for name, 'd' for dependencies and 'o' for output and the command at the end.
- This creates the corresponding `dvc.yaml` file filled with the `cmd, deps and outs` as mentioned in the command.
- We can add another stage that goes after the first 'preprocess stage' by adding another command as such

```
dvc stage add \
-n train \
-d processed_data.csv -d train.py \
-o plots.png -o metrics.json \
python train.py
```

- The corresponding `dvc.yaml` file must look like 
<img width="200" alt="image" src="https://github.com/Dharineesh-Karthikeyan/DVC/assets/12586329/e31bba0e-34ee-4fd9-9707-1c3255f26940">


### Reproducing a DVC Pipeline
- Reproduce the pipeline using `dvc repro`
- We can also reproduce an indivijual stage using `dvc repro <stage_name>`. Any dependency stages will be automatically runned.
- As for our example, this commands runs the preprocess and train step one after another.
- A state file `dvc.lock` is created. This file is very similar to a `.dvc` file. It stores the metainformation of the pipeline.
- It is good practice to commit to Git immediately after creation or modification to record the current state of the pipeline.
  
```
git add dvc.lock
git commit -m "Pipeline Repro"
```

> **NOTE : Using **Cached Result** when we try to rerun the pipeline, if there is no changes in the dependencies, it will skip its execution.**


### Visualize the dependencies
If we want to visualize the dependencies between the stages with the help of a DAG (Directed Acyclic Graph), we can run the command `dvc dag`

___

### USE CASES: (With an example)
1. The data is originally present in local.
2. We initialize git and dvc.
3. DVC add to create the .dvc file containing the metadata.
4. Git add and commit the .dvc file.
5. Set up a cloud storage as our DVC remote.
6. Git commit .dvc/config so that we record the metadata of the remote server we created.
7. DCV push the data to the cloud storage (DVC remote).

> The .dcv file is backed up in the Git and the data is backed up in the cloud. The git uses the .dcv file to access the data file present in the cloud (which is the dvc remote).
<br></br>
> The .gitignore file that is created during dcv init, is the reason the local data does not get commited in Git.


### Backtracking 
1. We can look at the git logs to see all previous commits and identify which version we are trying to backtrack to.
2. Git checkout <version> <file_name> (In our case the .dcv file)
3. dvc checkout - updates the data to the same version as well (No parameters required)
4. Git commit the .dcv file just to make sure the update is committed.

> Note: We do not need to perform a dvc add as it is already backtracked.

https://www.youtube.com/watch?v=kLKBcPonMYw
