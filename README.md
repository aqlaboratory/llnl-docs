# llnl-docs (GLM-geared)

## 0. Ask Mohammed to be onboarded to LLNL. 

You will receive an email from Barry Chen (most likely) and will need to provide your address for them to ship you an RSA token. 

## 0.5. Check your mail for a physical RSA token. 

Meet with Barry following mail receipt of the RSA token. 

## 1. ssh onto an LLNL compute cluster, accessing a compute node like [Dane](https://hpc.llnl.gov/hardware/compute-platforms/dane):
```
dhcp-10-118-155-112:~ mayavenkatraman$ ssh venkatraman2@dane.llnl.gov
```
You will be prompted for your 8-digit pin appended to the 6-digit number on the RSA token you received in the mail.  

## 2. ssh onto Tuolomne, which has the same architecture as exascale supercomputer El Capitán.
```
[venkatraman2@dane6:~]$ ssh tuolumne.llnl.gov
```

## 2.5 Set up your environment using mamba.

### Install mamba using miniforge.

Miniforge is an edition of Conda that only uses free and openly-licensed packages from the conda-forge project. It is already available on LLNL. Conda is an open-source package and environment manager that helps users install, update, and remove software. It allows you to isntall software WITHOUT needing root access (`sudo`), which is critical on systems like LLNL. 

Mamba is like Conda, but more efficient. 
```conda install -n base -c conda-forge mamba```

Check that mamba is installed correctly by running
```mamba --version```

### Optional: Improve your shell

Run `mamba install oh-my-bash` or some variant. 

### Install tmux

Run `mamba install tmux` or some variant.

### Optimize vim 

#### Vim deletion issue on Mac

If you are on Mac, you might observe that whenever you try to delete characters in vim, the terminal adds `^?`. By default, the macOS Terminal and some remote SSH sessions send ^? for Delete, which Vim does not interpret as a delete command. Run `stty erase ^?` to circumvent this. 

To make this change permanent, run `echo 'stty erase ^?' >> ~/.bashrc`.

#### Enable scrolling with mouse

Run `echo "set mouse=a" >> ~/.vimrc`.

### Cache your git credentials

Add your git userame and personal access token to `~/.git-credentials` like so:
`echo "https://your-username:your-personal-access-token@gitlab.com" >> ~/.git-credentials`

This way, you won't have to log in every time you pull or push. 

## 3. Find GLM model of interest

To find a model, check out `/p/vast1/OpenFoldCollab/genome_lm/training_output/`. It is probably there. 

## 4. Use sbatch to run a script.

Like on Manitou, you can use sbatch to run a script.

An example training script `train_model.sh` can be found in `/p/vast1/OpenFoldCollab/genome_lm/experiments/emb_exp/test_fsdp/train_model.sh`.

Run it like so:
```
[venkatraman2@tuolumne1003:scripts]$ sbatch train_model.sh 
faKoR2uSfgB
```
## 5. Check on the success of your job using your username
`[venkatraman2@tuolumne1004:scripts]$ squeue -u {username}`

NOTE: If your job has an "S" under "ST" (Status), this means "Suspended." It is possible that all jobs on Tuolomne are being suspended, due to maintenance. 

### Check whether all jobs are suspended

By running squeue without specifying a username:
`[venkatraman2@tuolumne1004:scripts]$ squeue`

### To search for your job by its id
`flux jobs | grep {job_id}`

## 6. Kill jobs you don't want

Run `scancel {job_id}`.

# Debugging

If your job fails silently (many of mine did at first), try the following.

### 1. Add echo statements to your original script. 

### 2. Try running in interactive mode using `srun` rather than `sbatch`. 

#### Example: training glm using srun
```
srun python /p/vast1/OpenFoldCollab/genome_lm/glm/glm/train/training.py \
  --config-yaml="/g/g14/venkatraman2/scripts/mvenkat_glm_12l_20k.yaml" \
  --limit-val-batches 50     --inference-mode-off     --skip-last-val \
  --compile-off
```

## Selective Learning

Directory: `/p/vast1/OpenFoldCollab/genome_lm/experiments/SL-GLM_exp`.

Experiment 1: `/SL-GLM_exp/02.10.2025_experiment_1`.

Submission/training scripts: `/02.10.2025_experiment_1/submit_SL/`.

Check `/02.10.2025_experiment_1/configs_SL/esm3s_12l_varlen20k_spanmask01_student_teacher_token.yaml` for the token selection config.
* Note the `student_teacher:` key

Currently SL can only handle standard-rope so set data params like `return_contig_indices: false`  correctly in both student and teacher configs. The key in ['student_teacher']['selection_scheme']  can only have two values — token or batch . You can see the batch selection config in `configs_SL/esm3s_12l_varlen20k_spanmask01_student_teacher_batch.yaml`.
