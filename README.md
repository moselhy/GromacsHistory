Full list of commands used:

pwd: BashHome/md
gmx pdb2gmx -f etge.pdb -o etge_processed.gro -ff amber99sb-star-ildnp
-> Chose water model 1 (TIP3P)
gmx editconf -f etge_processed.gro -o etge_newbox.gro -c -d 1 -bt dodecahedron
gmx solvate -cp etge_newbox.gro -cs spc216.gro -o etge_solv.gro -p topol.top
gmx grompp -f ions.mdp -c etge_solv.gro -p topol.top -o ions.tpr
gmx genion -s ions.tpr -o etge_solv_ions.gro -p topol.top -pname NA -np 4
-> Chose group 13 (SOL)
gmx grompp -f ions.mdp -c etge_solv_ions.gro -p topol.top -o ions_afterneutr.tpr
gmx genion -s ions_afterneutr.tpr -o etge_solv_ions_afterneutr.gro -p topol.top -conc 0.15 -pname NA -nname CL
-> Chose group 13 (SOL)

gmx grompp -f minim.mdp -c etge_solv_ions_afterneutr.gro -p topol.top -o em.tpr
gmx mdrun -v -deffnm em
gmx energy -f em.edr -o potential.xvg
gmx grompp -f nvt.mdp -c em.gro -p topol.top -o nvt.tpr
gmx mdrun -deffnm nvt
gmx energy -f nvt.edr -o temperature.xvg
gmx grompp -f npt.mdp -c nvt.gro -t nvt.cpt -p topol.top -o npt.tpr
gmx mdrun -deffnm npt
gmx energy -f npt.edr -o pressure.xvg
gmx energy -f npt.edr -o density.xvg
gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr
gmx mdrun -deffnm md_0_1
gmx trjconv -s md_0_1.tpr -f md_0_1.xtc -o md_0_1_noPBC.xtc -pbc mol -ur compact
-> Chose 0 for System
gmx rms -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsd.xvg -tu ns
-> Chose 4 and 4 for backbones
gmx rms -s em.tpr -f md_0_1_noPBC.xtc -o rmsd_xtal.xvg -tu ns
-> Chose 4 and 4 for backbones
