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
`[venkatraman2@tuolumne1004:scripts]$ squeue -u venkatraman2`

NOTE: If your job has an "S" under "ST" (Status), this means "Suspended." It is possible that all jobs on Tuolomne are being suspended, due to maintenance. 

### Check whether all jobs are suspended

By running squeue without specifying a username:
`[venkatraman2@tuolumne1004:scripts]$ squeue`

## Selective Learning

Directory: `/p/vast1/OpenFoldCollab/genome_lm/experiments/SL-GLM_exp`.

Experiment 1: `/SL-GLM_exp/02.10.2025_experiment_1`.

Submission/training scripts: `/02.10.2025_experiment_1/submit_SL/`.

Check `/02.10.2025_experiment_1/configs_SL/esm3s_12l_varlen20k_spanmask01_student_teacher_token.yaml` for the token selection config.
* Note the `student_teacher:` key

Currently SL can only handle standard-rope so set data params like `return_contig_indices: false`  correctly in both student and teacher configs. The key in ['student_teacher']['selection_scheme']  can only have two values — token or batch . You can see the batch selection config in `configs_SL/esm3s_12l_varlen20k_spanmask01_student_teacher_batch.yaml`.
