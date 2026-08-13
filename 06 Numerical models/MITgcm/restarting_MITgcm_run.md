# Restarting a run in MITgcm

**Contributor:** Nelson Poumaëre

Due to wall times on computing clusters, one must often conduct a numerical experiment in several consecutive runs.
For example, on Archer2, the wall time is 24 hours, so if a specific experiment takes e.g. 4 days of real time computation, one will have to restart the model 3 times.
Having to do that manually by copying config and restart files from one run to the next can become real tedious real fast (longer experiments, different parameters, etc).
The following shows how to automate that for MITgcm thanks to a bash script.

**Disclaimer: This is only one way of doing this, and there surely is a more clever one out there! Also, it is rather specific to the contributor's configuration: adapt it to your needs!** 

## The structure of an experiment in MITgcm

Let's look at an example structure for a MITgcm experiment:
> ```
> |-- code/
> |-- build/
> |-- input/
> |-- experiments/
> |   |-- PARAM_VALUE_1/
> |   |-- PARAM_VALUE_2/
> |   |-- PARAM_VALUE_3/
> |   |   |-- restart_run.sh
> |   |   |-- run0/
> |   |   |-- run1/
> |   |   |-- run2/
> |   |   |   |-- run_MITgcm.slurm
> |   |   |   |-- organize
> |   |   |   |-- data
> |   |   |   |-- data.*
> |   |   |   |-- *.bin -> /.../input/*bin
> |   |   |   |-- OUTPUT_2/
> |   |   |   |-- PICKUP/
> |   |   |   |-- [...]
> ```

The `build/` directory contains the executable `mitgcmuv` built from the files contained in the `code/` directory. 
The `input\` directory contains all the input files required for any experiment; we'll use symbolic links in each run directory to avoid unnecessary copying.
In `experiments/`, there are several directories each corresponding to one specific experiment (e.g. a parameter value).
In each of these specific experiment directories, there's a succession of directories: `run0`, `run1/`, `run2/`, etc, each containing one part of the full experiment, and each being restarted from the previous one.
Thus, `run0/` contains the initial run. Each of these run directories contains runtime parameters files `data*`, 
a slurm submitting file `run_MITgcm.slurm`, a file `organize` containing "cleaning commands" for when a run is finished, symbolic links to the input files `*bins`, and directories `OUTPUT_i/` and `PICKUP/`
containing, you guessed it, output and pickup files respectively.

## Restarting a run

Let's say a run is just finished, e.g. `run2/`. One then runs `source organize` in the `run2/` directory, which simply serves to have a clean-ish and consistent run directory structure:
```bash
> cat organize
mkdir PICKUP
mv pickup* PICKUP/
rm STDERR.*
mv STDOUT.0000 OUTPUT_*/
rm STDOUT.*
mv *.data *.meta available_diagnostics.log ocean_stats.*.txt OUTPUT_*/
```
After that, go in the parent directory, type the command `./restart_run.sh run2` (without the `/` in `run2`), and hit enter.
This will create a directory `run3/`, copy config and pickup files and modify the config files accordingly.
For example, one needs to change the `nIter0` parameter in `data` so that the model knows what pickup file to restart and when it is in the global calculation.

Following below is the `restart_run.sh` bash script, which hopefully should be self-explanatory enough.
Among other things, it works out the name and time step number of the most recent pickup file.
Based on the model time step size `dt`, it lets you know the current year of the computation (based on a 360 days calendar).
It lets you decide whether you want to submit the new job immediately or not, in case you might want to e.g. add a diagnostic in `data.diagnostics`.
```bash
#!/bin/bash

PREVIOUS_DIR=$1

PREVIOUS_RUN=$(echo $PREVIOUS_DIR | cut -d'_' -f1)
SUFFIX=$(echo ${PREVIOUS_DIR#${PREVIOUS_RUN}})

# Work out if previous run was a new run or restart (and number if so)
if [[ $PREVIOUS_RUN == "run0" ]]; then
        # It was a new run (zeroth run), and this is gonna be the first restart
        NEXT_RUN="run1"
        OUTPUT_DIR="OUTPUT_1"
        is_1st_restart=true
else
        # This was a restarted run
        # Now we read the restart number
        restart_number=1
        while [[ $PREVIOUS_RUN != "run${restart_number}" ]]; do
                ((restart_number++))
        done
        ((restart_number++))
        # Now we create folder names with (restart_number + 1)
        NEXT_RUN="run${restart_number}"
        OUTPUT_DIR="OUTPUT_${restart_number}"
fi

NEXT_DIR="${NEXT_RUN}${SUFFIX}"

echo  # New line in output

# Ask to confirm restart process
read -p "Restart computation from $PREVIOUS_DIR to $NEXT_DIR ? [y/n] " -r
echo
if [[ ! $REPLY =~ ^[y]$ ]]
then
	echo "Aborting..."
	exit 1
fi
echo "Preparing files for restart..."
echo


# Create new run folder and copy/create relevant folders from previous run.
# Also, creates symbolic links to the input files.
mkdir -p $NEXT_DIR
ln -s /work/e786/e786/npoumaere/MITgcm/verification/southern_ocean_channel/input/*.bin ./$NEXT_DIR
cp "$PREVIOUS_DIR"/{data*,eedata*,run_MITgcm.slurm,organize}  $NEXT_DIR

cd $PREVIOUS_DIR/PICKUP/

restart_file=$(ls -1rt pickup.*.meta | tail -1)
middle=${restart_file#pickup.}
middle=${middle%.meta}
echo "Middle string is $middle"
timestep=$(cat $restart_file | grep timeStepNumber | cut -f2 -d'[' | cut -f1 -d']' | xargs)

dt=600

nb_years=$(bc <<< "${timestep}*${dt}/(360*86400)" )

echo "Current time step is ${timestep} which corresponds to ${nb_years} years"

str_timestep=$(printf "%010d\n" "$timestep")
echo "Correct string for file is str_timestep is $str_timestep"

if [[ "$middle" != "$str_timestep" ]]; then
    echo "pickup.${middle}.data will be renamed to pickup.${str_timestep}.data"
    echo "pickup.${middle}.meta will be renamed to pickup.${str_timestep}.meta"
    echo "pickup_somT.${middle}.data will be renamed to pickup_somT.${str_timestep}.data"
    echo "pickup_somT.${middle}.meta will be renamed to pickup_somT.${str_timestep}.meta"
    read -p "Continue? [y/n] " -r
    echo
    if [[ ! $REPLY =~ ^[y]$ ]]
    then
            echo "Aborting..."
            exit 1
    fi
    mv "pickup.${middle}.data" "pickup.${str_timestep}.data"
    mv "pickup.${middle}.meta" "pickup.${str_timestep}.meta"
    mv "pickup_somT.${middle}.data" "pickup_somT.${str_timestep}.data"
    mv "pickup_somT.${middle}.meta" "pickup_somT.${str_timestep}.meta"
    echo
fi

echo "Copying pickup*.${str_timestep}.* files to ${NEXT_DIR}..."
echo

cp pickup*.${str_timestep}.*  ../../$NEXT_DIR/

cd ../../$NEXT_DIR

echo "Setting nIter0=${timestep} in data..."
echo
sed -i "s/^ nIter0=.*/ nIter0=${timestep},/" data 

echo "Setting diagMdsDir = '${OUTPUT_DIR}' in data.diagnostics..."
echo
sed -i "s/^ diagMdsDir =.*/ diagMdsDir = '${OUTPUT_DIR}'/" data.diagnostics 

# Ask to submit job to SLURM or not
read -p "If no further change, run 'sbatch ./run_MITgcm.slurm' in ${NEXT_DIR}? [y/n] " -r
echo
if [[ ! $REPLY =~ ^[y]$ ]]
then
	echo "Doing that later."
	exit 1
fi

sbatch ./run_MITgcm.slurm
```

And here is the SLURM submission file `run_MITgcm.slurm` (specific to the Archer2 computing cluster), for completeness:
```bash
#!/bin/bash

# Slurm job options (job-name, compute nodes, job time)
#SBATCH --job-name=MITgcm
#SBATCH --time=24:00:00
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=100
#SBATCH --cpus-per-task=1

# Replace [budget code] below with your project code (e.g. t01)
#SBATCH --account=e786
#SBATCH --partition=standard
#SBATCH --qos=standard

# Set the number of threads to 1
#   This prevents any threaded system libraries from automatically
#   using threading.
export OMP_NUM_THREADS=1

# Ensure the cpus-per-task option is propagated to srun commands
export SRUN_CPUS_PER_TASK=$SLURM_CPUS_PER_TASK

# Launch the parallel job
#   Using 200 MPI processes and 100 MPI processes per node
#   srun picks up the distribution from the sbatch options
srun --distribution=block:block --hint=nomultithread /work/e786/e786/npoumaere/MITgcm/verification/southern_ocean_channel/build/mitgcmuv
```

