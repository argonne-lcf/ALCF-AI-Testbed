# CS-1 Quickstart Guide for ANL
*November 2020*

**Note: For support or questions, please contact <support@cerebras.net>.**


This guide provides a quickstart guide to running a training job on the ANL CS-1 cluster with SLURM, using a simple FC model as a demo model.

For more details on how to prepare a model for CS-1 and on the execution model, please see the full CS-1 ML User Guide.

#### 1. Log in to the CS-1 `medulla` cluster at ANL

In order to login, you need a CELS ldap account. Go to <https://accounts.cels.anl.gov/> and create an account. After that, request to join a project named "Cerebras".  

The CS-1 support cluster accepts connections from CELS homes nodes (homes.cels.anl.gov). You can connect to CELS homes nodes first and ssh to cs1. See <https://virtualhelpdesk.cels.anl.gov/docs/linux/login-compute-and-home-nodes/> for connecting homes nodes.

For your convenience, you can set an ssh proxy as below,
```
# ssh proxy setting ~/.ssh/config
ControlPath ~/.ssh/.control_channels/%h:%p:%r
Host login-gce
User [username]
Hostname logins.cels.anl.gov
ControlMaster auto
ControlPersist yes
LogLevel FATAL

Host homes-gce
User [username]
Hostname homes.cels.anl.gov
ProxyCommand ssh login-gce -q -W %h:%p
ForwardX11 yes

Host cs1-gce
HostName cs1.cels.anl.gov
ProxyCommand ssh login-gce -q -W %h:%p
```

and

```
ssh cs1-gce
```

#### 2. Clone model zoo repository to home directory
Currently, the Cerebras **[ModelZoo](https://github.com/Cerebras/modelzoo)** repo is private. Please contact <support@cerebras.net>, cc: <jessica@cerebras.net> with your Github username and "UChicago ModelZoo Access" in the subject line.
```
git clone git@github.com:Cerebras/modelzoo.git
```

In the modelzoo directory, you should see several examples. To name a few:

1.	`fc_mnist`, A simple multi-layer perceptron model composed of fully-connected layers for performing handwriting recognition on the MNIST dataset. Check README.md for more detail.

2.	`bert`, BERT model implementation. Check README.md for more detail.

3.	`rnn_sentiment`, LSTM-based sentiment analysis model that predicts sentiment type on a passage

**[Model zoo walkthrough video is available here.](https://bluejeans.com/playback/s/mZJ5DqnjvP2nsLPCwegfMqjRkz6gzaaqG80NmjmpU4bpx6teHotpximQ3PsMos4f)**

For the purposes of this quickstart, we will use the `fc_mnist` model.
```
cd modelzoo/fc_mnist/tf/
```

#### 3. Running a model with Slurm on CS-1
For more details on Slurm options please see the full **[CS-1 User Guide](https://docs.google.com/document/d/12u7eooakOlv16nF4-4Cw7njArE_Vk5LMxKwo-PKcozI/edit#heading=h.xg9jdr68szft)** section *Executing a training job on CS-1 with Slurm*.

The common below trains the simple FC model for 100,000 steps, takes a checkpoint every 10,000 steps, and tells Slurm to launch 1 input worker node to feed the training job. The `num_steps` and `save_checkpoints_steps` parameters can also be set in the `params.yml` file.
```
NUM_WORKER_NODES=1 srun_train python run.py --mode train --cs_ip 10.80.0.100 --max_steps 100000
```

After running this command, you should see similar output to the below: 

```
srun: job 5834 queued and waiting for resources
srun: job 5834 has been allocated resources
...
INFO:tensorflow:Graph was finalized.
INFO:tensorflow:Running local_init_op.
INFO:tensorflow:Done running local_init_op.
INFO:tensorflow:Saving checkpoints for 0 into model_dir/model.ckpt.
INFO:tensorflow:Programming CS-1 fabric. This may take a couple of minutes - please do not interrupt.
INFO:tensorflow:Fabric programmed
INFO:tensorflow:Coordinator fully up. Waiting for Streaming (using 0.97% out of 301600 cores on the fabric)
INFO:tensorflow:Graph was finalized.
INFO:tensorflow:Running local_init_op.
INFO:tensorflow:Done running local_init_op.
...
INFO:tensorflow:Training finished with 25600000 samples in 187.465 seconds, 136558.69 samples / second
INFO:tensorflow:Saving checkpoints for 100000 into model_dir/model.ckpt.
INFO:tensorflow:global step 100000: loss = 1.901388168334961e-05 (532.0 steps/sec)
INFO:tensorflow:global step 100000: loss = 1.901388168334961e-05 (532.0 steps/sec)
INFO:tensorflow:Loss for final step: 1.9e-05.
```


### Training and Evaluating on CPU
To train and eval on CPU, run the Cerebras Singularity container in interactive mode, then run the `run.py` script, specifying ‘train’ or ‘eval’ for the ‘--mode’ script argument. In these scripts, if the `cs_ip` address is not specified, it will automatically run on CPU. 

#### 1. Navigate to model directory
```
cd modelzoo/fc_mnist/tf/
```

#### 2. Run salloc_node, which will both start the Cerebras Singularity container in interactive shell mode and reserve a worker CPU node in the CS-1 Slurm cluster
```
salloc_node
```

#### 3. Train and evaluate the model on CPU
```
# train the simple model on CPU
Singularity> python run.py --mode train

# run eval on the simple model on CPU
Singularity> python run.py --mode eval
```

### Fast iteration and precompilation on CPU
CerebrasEstimator provides a lightweight model "verification" mode through the compile() function. In this mode, compile() will only run through the first few stages of the Cerebras Graph Compiler (CGC), up until kernel library matching. This step is very fast and allows you to quickly iterate on your model code and determine if you are using any TensorFlow layers or functionality that is unsupported by either XLA or CGC.

#### 1. Navigate to model directory
```
cd modelzoo/fc_mnist/tf/
```

#### 2. Run the Cerebras Singularity container in interactive shell mode and reserve a worker CPU node in the Cerebras cluster
```
# do not use salloc_node
singularity shell -B /data /data/shared/software/singularity/cbcore_latest.sif
```

#### 3. Run the compilation process in `validate_only` mode
```
Singularity> python run.py --mode validate_only 
...
XLA Extraction Complete
=============== Starting Cerebras Compilation ===============
Cerebras compilation completed: 100%|██████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:02s,  1.23s/stages]
=============== Cerebras Compilation Completed ===============
```

`validate_only` mode checks kernel compatibility of your model.
Once your model passes, run the full compilation to generate the CS-1 executable.

#### 4. Run the full compilation process in `compile_only` mode
This runs the full compilation through all stages of the Cerebras software stack to generate a CS-1 executable. 
```
Singularity> python run.py --mode compile_only 
...
XLA Extraction Complete
=============== Starting Cerebras Compilation ===============
Cerebras compilation completed: |                    | 17/? [00:18s,  1.09s/stages]
=============== Cerebras Compilation Completed ===============
```

If this is successful, your model is guaranteed to run on CS-1. You can also use this mode to run pre-compilations of many different model configurations offline, so you can more fully utilize allotted CS-1 cluster time. CGC will detect if a binary already exists for a particular model config and will skip compiling on-the-fly during training if it detects one.
