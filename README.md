# HPC - Miniconda3 guide 
February 14, 2024​


## Miniconda3 setup​  
Latest Miniconda installer links, visit https://docs.anaconda.com/free/miniconda/ 

### 1. To setup Miniconda system-wide as a module
````
$ sudo bash DOWNLOAD_PATH/Miniconda3-latest-Linux-x86_64.sh
````
1. Accept End User License Agreement
2. Change installation location to: **/home/apps/miniconda3/<version>** (e.g. ***/home/apps/miniconda3/23.11.0***)

### 2. Create a module file
File location: **/cm/shared/modulesfiles/miniconda/23.11.0** << *Update the version number accordingly*

````
#%Module -*- tcl -*-
##
## modulefile
##
proc ModulesHelp { } {
  puts stderr "\tAdds Anaconda application to your environment variables,"
}

module-whatis "adds Anaconda application to your environment variables"

set             root              /home/apps/miniconda3/23.11.0
prepend-path    PATH              $root/bin

# Initialize Conda
if { [module-info mode load] } {
    puts stderr "Configuring Conda for Bash shell..."
    exec "$root/bin/conda" "shell.bash" "hook" >> /tmp/conda_init_$env(USER).sh
    puts "source /tmp/conda_init_$env(USER).sh"
}
````


## Initializing the conda environment as module
User can load the module using the following:
````
module load miniconda/23.11.0
````

If loaded successfully, a prefix **(base)** would be inserted to PS1 prompt

New conda environment will be created and store in user home directory **$HOME/.conda location**

Installed packages will be setup in **$HOME/.conda/envs/<environment_name>/lib/python<version>/site-packages/**


## SLURM example
### 1. Load module
````
module load miniconda/23.11.0
````

### 2. Create conda env
````
conda env create --name testenv python=3.11
````

### 3. Activate testenv and setup numpy
````
conda activate testenv
pip install numpy
````

### 4. Sample script for numpy
Create a new file name: **sample-numpy.py**
````
import numpy as np

a = np.arange(15).reshape(3, 5)
print("Array a:")
print(a)

print("\nTranspose of a:")
print(a.T)
````

### 5. Sample SBATCH job definition
Create a new file name: **sample-conda.sh**
````
#!/bin/bash
#SBATCH --job-name=staging
#SBATCH --partition=workq
#SBATCH --ntasks-per-node=1
#SBATCH --nodes=2
#SBATCH --ntasks=2
#SBATCH --mem=1G
#SBATCH --time 00:01:00
#SBATCH --mail-user a.wong@hummingbirdbio.com
#SBATCH --mail-type BEGIN
#SBATCH --mail-type END
#SBATCH --mail-type FAIL

OEXEDIR=/home/awong/out

module load miniconda/23.11.0
conda activate testenv

date
echo 'Your job is running on node(s):' $SLURM_JOB_NODELIST
echo 'Cores per node:' $SLURM_TASKS_PER_NODE
module list
python sample-numpy.py
python sample-numpy.py
python sample-numpy.py
sleep 60

exit 0
````

### 6. Submit job
````
sbatch sample-conda.sh
````

### 7. Check submitted job ID
````
squeue -l
````
example output:
> *JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)*
> 
> *113615     workq  staging    awong  RUNNING       0:04      1:00      2 apollocn[01-02]*

*showing job running on 2 nodes
