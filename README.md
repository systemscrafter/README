# HMBD HPC - Miniconda3 guide 
February 14, 2024​

## Miniconda3 setup​  
Latest Miniconda installer links, visit https://docs.anaconda.com/free/miniconda/ 

### 1. To setup Miniconda system-wide as a module
````
$ sudo bash DOWNLOAD_PATH/Miniconda3-latest-Linux-x86_64.sh
````
1. Accept End User License Agreement
2. Change installation location to: **/home/apps/miniconda3/<version>** (e.g. ***/home/apps/miniconda3/23.10.0***)

### 2. Create a module file in the following location: /cm/shared/modulesfiles/miniconda/23.10.0 **Update the version number accordingly
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
module load miniconda/23.10.0
````

If loaded successfully, a prefix **(base)** would be inserted to PS1 prompt

New conda environment will be created and store in user home directory **$HOME/.conda location**

Installed packages will be setup in **$HOME/.conda/envs/<environment_name>/lib/python<version>/site-packages/**
