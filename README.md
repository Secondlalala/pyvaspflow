# Defect-Formation-Calculation

This package is a integrated Defect Formation energy package, which contains generating tetrahedral interstitial sites and  octahedral interstitial sites, submitting `VASP` calculation job and withdraw necessary data to calculate defect formation energy.

## 0. Installment
Firstly, you should make a directory for those scripts and add its path to your `.bashrc`. For example, I make the scripts directory under my $HOME directory, so I can use the below command to add this directory to $PATH, and `source` your `.bashrc` to make it work.
```shell
echo export PATH="$PATH:/home/hecc/scripts" >> ~/.bashrc
source ~/.bashrc
```

## 1. Generate defect structures

There are three kinds of defect system you can generate, vacancy defect, purity defect and interstitial defect. All these can be generated by [defect_maker](./defect_maker.py), and the generated structures in VASP format will be stored in a directory named by its attributes.



## 2. submit your common calculatiojobn jobs
Here, we supply some integrated shell scripts to calculate the jobs you need.<br />
- [x] [structure_relax](./common_calculation_shell/stru_relax.sh)<br />
- [x] [structures_optimization](./common_calculation_shell/stru_optimization.sh)<br />
- [x] [structures_self-consistent-field](./common_calculation_shell/stru_scf.sh)<br />
- [x] [structures_band-calculation](./common_calculation_shell/stru_band.sh)<br />
- [x] [structures_dos-calculation](./common_calculation_shell/stru_dos.sh)<br />
and a main [job scripts](./common_calculation_shell/job.sh) to submit your job.

We also supply some scripts to generate the input files needed in `VASP` Calculation, such as `INCAR`, `KPOINTS`, `POTCAR`, you can also use these  scripts to generate them.

So in general, you can just begin your job from a `POSCAR` and a `job.sh`.

## 3. Get some calculation value in your vaspout files

Here we supply a command interface to get the value you want.

```shell
module load sagar #load the necesary package
vaspout.py --help # you can get some short help from this command
vaspout.py main --help # get the help of a specific command  
```

`main`  <br >
This command is used to get some common value of your calculation system. For instance, gap, fermi energy, electrons number and so on.
The last parameter is the directory path of your calculation system, make sure it is right or you will get wrong answer.
```shell
vaspout.py main -a gap . # this can read the gap and vbm, cbm
vaspout.py main -a fermi . # this can read the fermi energy
vaspout.py main -a energy . # this can read the total energy
vaspout.py main -a ele . # this can read the electrons in your OUTCAR
vaspout.py main -a ele-free . # this can get electrons number of  the defect-free system
vaspout.py main -a image image_corr/ # this can get Ewald energy of your system
```

`get_delete_atom_num` <br >
This command is used to get which atom has been removed from the defect free system. The usage is very simple, the first parameter is the path of no defect POSCAR, the second parameter is the path of one defect POSCAR.
```shell
vaspout.py get_delete_atom_num no_defect_poscar one_defect_poscar
```

`get_farther_atom_num` <br >
Because you will calculate the potential alignment in the process of defect calculation, so you should know where is the farther atom from the defect position. This command can help you to get the atom number.
The two parameters is exactly the same as the `get_delete_atom_num`.
```shell
vaspout.py get_farther_atom_num no_defect_poscar one_defect_poscar
```

`get_potential_align` <br >
This command can get the electrostatic of the specific atom number, which can be used based on the above command `get_farther_atom_num` to get the potential alignment.
```shell
num=`vaspout.py get_farther_atom_num Si-POSCAR charge_state_-1/POSCAR |awk 'NR == 1{print $1}'`
vaspout.py get_potential_align OUTCAR $num
```
