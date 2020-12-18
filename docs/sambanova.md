# Getting Started with SambaNova
### Accounts:

#### 1.	Get an account on the SambaNova (SN) system. Contact Venkat Vishwanath (<venkat@anl.gov>) for access.

#### 2.	You will first need to login to one of the MCS or CELS machines first and then ssh to the SambaNova system that has 2 nodes `sm-01` and `sm-02`. Hostname for SambaNova is `sm-01.cels.anl.gov` and `sm-02.cels.anl.gov`

### Documents:
SambaNova technical documents to read through. Part 1 is H/W and Part 2 is S/W

<!--<https://anl.box.com/s/bwy9ve72306l1q01qw3g4vwk4a93k06z>{:target="_blank"}-->

These are under NDA. Please don't share. If you are unable to access these documents, please reach out to Venkat/Murali for access.

There is a dedicated documentation portal, please reach out to Murali Emani (<memani@anl.gov>) for access.

### First Steps:
After logging in, you need to activate SN virtual environment (venv) before running models.

- Activating venv environment: `source /opt/sambaflow/venv/bin/activate`

- Deactivating venv environment: `deactivate`

### How to Run
To run an application on the SambaNova nodes, it has to be written in 'SambaFlow' which is similar to PyTorch. This software stack includes the compilers, runtime and SambaFlow Python SDK.  It is to be noted not all operators are supported yet, they will be released in monthly releases. Support for Tensorflow is work in progress.

The workflow includes the following four steps to run a model. A high-level overview is mentioned here. Detailed information can be obtained from the official documentation.

#### 1. Compile: Compiles the model and generates a .pef file. This file contains information on how to reconfigure the hardware like many compute and memory resources are required, and will be used in all subsequent steps. The pef files are usually saved in 'out' directory, it is advised to save it in a separate dir with '--output-folder' option
```
python myapp.py compile --pef-name="myapp.pef"
```

#### 2.	Test (optional): Runs a test both on the host CPU and SN node and will raise errors if any discrepancies are found. Pass the pef file generated above as the input.
```
python myapp.py test --pef="out/myapp/myapp.pef"
```

#### 3.	Run: This will run the application on SN nodes.
```
python myapp.py run --pef="out/myapp/myapp.pef"
```

#### 4.	Measure Performance: This step will report the measured performance. The parameters depend on the model and can include latency, samples/sec.
```
python myapp.py measure-performance --pef="out/myapp/myapp.pef
```

Other parameters can be found with `--help` command in any step, (ex. `python myapp,py compile --help`)

There are some sample programs at `/opt/sambaflow/apps/` to try out. 

These steps can be submitted in a single script via Slurm using sbatch command.
```
sbatch --output=<path>/output.log  submit-job.sh
```

The node and number of resources required can be passed as arguments to sbatch
```
sbatch --output=<path>/output.log -w <node: sm-01/sm-2> --cpus-per-task=4 --gres=rdu:1 submit-job.sh
```

For any other questions, please reach out to Venkat (<venkat@anl.gov>), Murali (<memani@anl.gov>).
