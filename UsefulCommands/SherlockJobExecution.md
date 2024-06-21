# **Useful Commands for Sherlock Job Execution:**

### Storage
Here is where we recommend storing different types of files and data on Sherlock:
personal scripts, configuration files and software installations → $HOME
group-shared scripts, software installations and medium-sized datasets → $GROUP_HOME
temporary output of jobs, large checkpoint files → $SCRATCH
curated output of job campaigns, large group-shared datasets, archives → $OAK

To check your quota usage on the different filesystems you have access to, you can use the sh_quota command:
```
sh_quota
```

### Partitioning
1. to examine the status of CPUs in our partition: sinfo --format=%C -p krasnow
 - the output will be listed as "CPUS(A/I/O/T)" which stands for:
      - A (Allocated): The number of CPUs currently allocated or in use by running jobs.
      - I (Idle): The number of CPUs that are idle and available for new jobs.
      - O (Other): The number of CPUs in other states (e.g., drained, down, or reserved).
      - T (Total): The total number of CPUs available on the system.
        - Here's an example: "CPUS(A/I/O/T), 38/90/0/128"

### Submitting a Job
To submit a job to the Sherlock computing nodes, you'll need to create a job script, typically a shell script, that contains the commands you want to run. Here's an example of how you can create a job script to run the cellranger count command:

1. Create a new file, for example, run_cellranger.sh, and open it in a text editor:

```
nano run_cellranger.sh
```

  - This will open a file in your terminal where you should add your code. Note:
    - #SBATCH lines are Slurm directives that specify job parameters, such as the job name, output and error files, partition, number of nodes, tasks per node, memory, and time limit.
    - module load cellranger loads the Cell Ranger module so that the cellranger command is available.
    - cellranger count is the command you want to run, with its arguments.
    - The TIME column shows the cumulative CPU time used by the job in the format D-HH:MM:SS. 
      - For CellRanger, here's what I've used:

```
#!/bin/bash
#SBATCH --job-name=CIN5Cellranger
#SBATCH --output=CIN5Cellranger_output.txt
#SBATCH --error=CIN5Cellranger_error.txt
#SBATCH --partition=krasnow
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=10
#SBATCH --mem=64G
#SBATCH --time=48:00:00
#SBATCH --mail-user=annie999@stanford.edu
#SBATCH --mail-type=BEGIN,END,FAIL

# Load any necessary modules (if required)
module spider cellranger

# Change directory to where the fastq files are located
cd /oak/stanford/groups/krasnow/ATN/snRNAseq_CIN1sample

# Run CellRanger count command
/oak/stanford/groups/krasnow/ATN/CIN1_CellRangerFiles/cellranger-8.0.0/cellranger count \
 --id=ATNAlignedReadsCIN5 \
 --transcriptome=/oak/stanford/groups/krasnow/ATN/CIN1_CellRangerFiles/refdata-gex-GRCm39-2024-A \
 --fastqs=/oak/stanford/groups/krasnow/ATN/snRNAseq_CIN1sample \
 --sample=CIN_1 \
 --create-bam=true \
 --localcores=10 \
 --localmem=64
 ```

2. Save the file and exit the text editor (in nano, you can do this by pressing Ctrl + X, then Y to confirm, and then Enter).
3. Once you have the script saved, you can submit it to the Slurm scheduler using the sbatch command:

```
sbatch run_cellranger.sh
```
This will submit the job to the Slurm scheduler, and it will be executed on one of the computing nodes according to the specified parameters.

### Check on Job Status
To check your place in the queue and get an estimate of if/when your job will be accepted and completed, you can use various Slurm commands on Sherlock:

- Use the squeue command to view the status of your job and its position in the queue. This will often give you an incredibly long output of everyone's submitted Sherlock jobs. However, I like to use this in combination with command f for "krasnow" or "annie999" to examine our lab's CPUs or my specific job.
  ```
  squeue 
  ```
  
- You can also filter the output to only show your jobs by specifying your username with the -u option:
  ```
  squeue -u your_username
  ```
  I particularly appreciate this command, because it provides a very clean readout compared to the scontrol show job command. You will also likely need to retrieve the listed job ID if you're looking to use the scontrol show job command.
  ```
              JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          45477143   krasnow CIN5Cell annie999  R      14:30      1 sh03-16n23
  ```
    - The ST column represents the state of the job. The state can be one of several values, including R, CG, and PD, among others. Here's what these abbreviations typically mean:
        - R (Running): The job is currently running on a compute node. This means that the resources requested by the job have been allocated, and the job is actively using those resources.
        - CG (Completing): The job has finished its execution and is in the process of completing. This typically involves tasks such as finalizing output files, cleaning up resources, and performing any necessary post-processing steps before the job is considered finished.
        - PD (Pending): The job is waiting in the queue to be scheduled and executed. This means that the resources requested by the job are not currently available, and the job is waiting for those resources to become available.

This code will list all jobs in a specific partition. Here we're using "krasnow"
```
squeue -p krasnow
```

- If you want more detailed information about a specific job, you can use the scontrol show job command followed by the job ID:
  ```
  scontrol show job job_id
  ```
  
After submitting any job, I like to run through these commands to ensure it's in queue properly. Typically, notification of failed jobs will occur quickly and if you stick around to check these things, you'll be better prepared to troubleshoot and resubmit ASAP. Including the email notification lines in your code, however, can provide flexibility if you need to step away.

