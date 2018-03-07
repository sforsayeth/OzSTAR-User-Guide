.. highlight:: rst

Torque-Moab vs Slurm
==================================

If you have been a user of the Green II supercomputer, you may be aware that submitting jobs to the queue is done via the
Terascale Open-source Resource and QUEue Manager (Torque) and the Moab Cluster Suite (e.g. PBS scripts; ``qsub``).
If so, please note that OzSTAR makes use of the Simple Linux Utility For Resource Management (Slurm).

To ease your transition from one model to the other, you can find a list of equivalencies (where applicable) below.
Also, the Slurm developers maintain a ‘Rosetta stone of Workload manager‘ which gives the correspondences between the options of several job schedulers. You can access the `PDF here <https://slurm.schedmd.com/rosetta.pdf>`__.

Torque/Moab vs Slurm Environment Variables
--------------------------------------------------------------------------------

(Mostly swiped from `NERSC <http://www.nersc.gov/>`__)

+--------------------------+-----------------+----------------------+
| **Description**          | **Moab/Torque** | **Slurm**            |
+==========================+=================+======================+
| Job Id                   | $PBS_JOBID      | $SLURM_JOB_ID        |
+--------------------------+-----------------+----------------------+
| Job Name                 | $PBS_JOBNAME    | $SLURM_JOB_NAME      |
+--------------------------+-----------------+----------------------+
| Submit Directory         | $PBS_O_WORKDIR  | $SLURM_SUBMIT_DIR    |
+--------------------------+-----------------+----------------------+
| Node List                | $PBS_NODEFILE   | $SLURM_NODELIST      |
+--------------------------+-----------------+----------------------+
| GPU List                 | $PBS_GPUFILE    | n/a                  |
+--------------------------+-----------------+----------------------+
| Host submitted from      | $PBS_O_HOST     | $SLURM_SUBMIT_HOST   |
+--------------------------+-----------------+----------------------+
| Nodes allocated          | $PBS_NUM_NODES  | $SLURM_JOB_NUM_NODES |
+--------------------------+-----------------+----------------------+
| Number cores/nodes       | $PBS_NUM_PPN    | $SLURM_CPUS_ON_NODE  |
+--------------------------+-----------------+----------------------+
| Node local scratch space | $PBS_JOBFS      | $JOBFS               |
+--------------------------+-----------------+----------------------+


Torque vs Slurm Commmands
----------------------------------------
+-----------------+-------------+---------------------------------+---------------------+
| **Description** | **PBS**     | **Slurm**                       | **Notes/Examples**  |
+=================+=============+=================================+=====================+
| Cancel a job    | qdel        | scancel                         | scancel -i 23447    |
+-----------------+-------------+---------------------------------+---------------------+
| Job submission  | qsub [file] | sbatch [file]                   | sbatch ./a.out      |
+-----------------+-------------+---------------------------------+---------------------+
| Job hold        | qhold jobid | scontrol hold jobid             | scontrol hold 23447 |
+-----------------+-------------+---------------------------------+---------------------+
| Job release     | qrls jobid  | scontrol release                |                     |
+-----------------+-------------+---------------------------------+---------------------+
| Nodes list      | pbsnodes -l | sinfo -N or scontrol show nodes |                     |
+-----------------+-------------+---------------------------------+---------------------+


#PBS vs #SBATCH SCRIPT DIRECTIVES
----------------------------------------

+----------------------+--------------------------------------+----------------------------------------------------------------+
| **Script directive** | **#PBS**                             | **#SBATCH**                                                    |
+======================+======================================+================================================================+
| Queue                | -q [queue]                           | --partition=[queue] or -p [queue]                              |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Number of tasks      |                                      | --ntasks=[count] or -n [count]                                 |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Node Count           | -l nodes=[count]                     | --nodes=[count]  or -N [count]                                 |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Tasks per node       | -l ppn=[count]                       | --ntasks-per-node=[count]                                      |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Wall Clock Limit     | -l walltime=[hh:mm:ss]               | --time=[min] or --time=[days-hh:mm:ss] or -t [nn:nn:nn]        |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Standard output file | -o [file]                            | --output=[file] or -o [file]                                   |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Standard error file  | -e [file]                            | --error=[file] or -e [file]                                    |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Combine stdout/err   | -j oe (both to stdout)               | default if no --error (-e)                                     |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Event notification   | -m abe                               | --mail-type=[events]                                           |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Email address        | -M [address]                         | --mail-user=[address]                                          |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Job Name             | -N [name]                            | --job-name=[name] or -J [name]                                 |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Job restart          | -r [y\n] (default n)                 | --requeue OR -no-requeue (default set to -no-requeue)          |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Working directory    | N/A                                  | --workdir=[dir_name] or -D [directory]                         |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Account to charge    | -W group_list=[account]              | --account=[account] or -A [account]                            |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| CPUs per task        |                                      | --cpus-per-task=[count] or -c [count]                          |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Job dependency       | -d [jobid]                           | --dependency=[state:jobid] or -d [dependency list]             |
+----------------------+--------------------------------------+----------------------------------------------------------------+
| Job arrays           | -t [array_spec]                      | --array=array_spec or -a [indexes]                             |
+----------------------+--------------------------------------+----------------------------------------------------------------+
