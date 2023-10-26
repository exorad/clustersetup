# clustersetup

Summary and useful files for the setup of `expeRT/MITgcm` on a slurm cluster. All suggestions are subject to experiences and use of the author (Aaron Schneider). Better options might be available.

## bashrc:
```
export MITGCM_ROOTDIR="/lustre/astro/aaron/codes/MITgcm"
alias get_iternum='cat STDOUT.0000 | grep " cg2d:" | wc -l'
export pRT_input_data_path="/lustre/astro/aaron/prt_input_data"
alias squeue_u="squeue -u $USER --format '%.18i %.9P %.8j %.2t %.10M %.6D %R %E'"
```

- `MITGCM_ROOTDIR`: useful to set and used by the `install_exorad.sh` shell script and the `genmake2` script
- `pRT_input_data_path`: Useful environment variable to set for `petitRADTRANS`
- `squeue_u`: display the slurm jobs that run under your user name
- `get_iternum`: Show the iteration number at which MITgcm currently is by counting rows in the `STDOUT.0000` file
 
## bin directory:
It is useful to setup a directory in which you put bash scripts that will be known to your shell. Such a folder could be called `bin` and located in `$HOME`. Add this folder to the `PATH` variable in the `.bashrc` file:
```
PATH=$PATH:~/bin
```

### scripts to link in your `bin` folder
Link some scripts to your `bin` folder by

```ln -s <script_name> ~/bin```

- from exorad: `install_exorad.sh`
- from MITgcm: `$MITGCM_ROOTDIR/tools/genmake2`

### other scripts from this repo

- `exorad_convert`: A script that can be used to submit a slurm job that utlizies [gcm_toolkit](https://gcm-toolkit.readthedocs.io/en/latest/) to convert raw `MITgcm` output to regridded `netcdf` files. See [here](https://gcm-toolkit.readthedocs.io/en/latest/cli.html).
- `exorad_compile<i>`: slurm script that compiles `expeRT/MITgcm` on the targeted hardware `<i>`
- `exorad_submit_<queue><i>` slurm script that runs `expeRT/MITgcm` on the targeted hardware `<i>` in queue `<queue>`
- `genmake2_iimpi<i>`: `genmake2` wrapper that uses `mpi` and specified build options for hardware `<i>`

## Tools to install:
- [findent](https://sourceforge.net/projects/findent/), used by `install_exorad.sh`
- [loop_submit](https://github.com/exorad/loop_submit), used in slurm scripts to update iteration number in `data` file
- [telegram-simulation-bot](https://github.com/AaronDavidSchneider/telegram_simulation_bot), for chatting with the cluster about the current status of the simulation
- [telegram-send](https://github.com/rahiel/telegram-send) for sending telegram messages to inform about starting and ending jobs

## `build_options`
The `build_options` files in this repository are adapted from the `$MITGCM_ROOTDIR/tools/build_options/linux_amd64_ifort+impi` file to work for the `astro3` and `astro2` nodes on the copenhagen cluster. 

Compiler: `ifort` and `intelmpi`

Hardware configuration of Copenhagen cluster:
- `astro3`: AMD EPYC 9554 64-Core Processor, 256 cores, 2 threads per core
- `astro2`: Intel(R) Xeon(R) Gold 6248R CPU @ 3.00GHz, 96 cores, 2 threads per core

`MITgcm` in cubesphere can only run with $6*2^n$ cores with $n\in\mathbb{N}^+$. In the `astro2` nodes, we therefore use 48 cores, and on the `astro3` nodes, we use 96 cores. You can find examples for the corresponding `SIZE.h` files in the folder `size_files`.

**Important**: Make sure to use the CPU instruction sets! If you fail to utilize the correct instruction set for your targeted hardware, you will be penalized with slow performance. This is especially nescessary for AMD hardware! For the `astro3` hardware (4th gen AMD EPYC), you may use: `PROCF=-march=core-avx2`. For intel hardware `PROCF=-xHost` should be enough.

## Python:
Install your own python! Python packaging is a nightmare. Make it easier by downloading anaconda3 or miniconda to your cluster. Host your own installation somewhere in a home directory. Do not use the anaconda supplied from a global module! You won't be flexible enough to install packages and manage environments.
