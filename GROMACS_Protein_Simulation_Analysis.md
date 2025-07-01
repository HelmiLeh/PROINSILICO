# GROMACS Protein Simulation Analysis

## Tutorial to Analyse the MD Trajectory

### Check the Copy of Trajectory
```bash
gmx check -f md_20.xtc
```

### Center the Protein
```bash
gmx trjconv -f md_20.xtc -s md_20.tpr -o md_center.xtc -pbc nojump -center -ur compact
# Select:
# 1 - Protein
# 0 - System
```

### Fit the Protein to Starting Structure
```bash
gmx trjconv -f md_center.xtc -s md_20.tpr -o md_fit.xtc -center -fit rot+trans
# Select:
# 4 - Backbone
# 1 - Protein
# 1 - Protein
```

### Extract the Starting Structure
```bash
gmx trjconv -f md_20.xtc -s md_20.tpr -o md_nowater.pdb -center -dump 0
# Select:
# 1 - Protein
# 1 - Protein
```

### View the Trajectory
```bash
vmd md_nowater.pdb md_fit.xtc
```

---

## Structural Analysis

### Calculate RMSD
```bash
gmx rms -f md_center.xtc -s md_20.tpr -o rmsd_backbone.xvg -tu ns
# Select:
# 4 - Backbone
# 4 - Backbone

xmgrace rmsd_backbone.xvg
```

### Calculate Radius of Gyration
```bash
gmx gyrate -f md_center.xtc -s md_20.tpr -o gyrate_backbone.xvg
# Select:
# 4 - Backbone

xmgrace gyrate_backbone.xvg
```

### Calculate RMSF
```bash
gmx rmsf -f md_center.xtc -s md_20.tpr -o rmsf_backbone.xvg -oq bfactor_backbone.pdb -res
# Select:
# 4

xmgrace rmsf_backbone.xvg
```

> For B-factor putty: open `bfactor_backbone.pdb` then go to `A > Preser > Bfactor Putty`

---

## Pocket Analysis

### Create Index for Protein Pocket
```bash
gmx make_ndx -f md_20.tpr -o index_pocket.ndx
# Input:
r694|r695|r697|r699|r702|r719|r721|r738|r742|r751|r764|r766|r767|r768|r769|r770|r776
# Press `q` to quit
```

### Calculate Pocket SASA and Volume
```bash
gmx sasa -f md_center.xtc -s md_20.tpr -n index_pocket.ndx -o area_pocket.xvg -tv volume_pocket.xvg -tu ns
# Select:
# 17 - Pocket residues

xmgrace area_pocket.xvg 
xmgrace volume_pocket.xvg
```

---

## Clustering and Representative Structure

### Cluster Protein Structure
```bash
gmx cluster -f md_center.xtc -s md_20.tpr -g cluster_0.2.log -b 10 -tu ns -method gromos -cutoff 0.2 -skip 10
# Select:
# 4 - Backbone
```

### Extract Representative Structure
```bash
gmx trjconv -f md_20.xtc -s md_20.tpr -o rep[time].pdb -center -dump [time]
```

---

## Statistical Analysis

### Calculate Average and SD
```bash
gmx analyze -f rmsd_backbone.xvg
gmx analyze -f gyration_backbone.xvg
```

---

## Summary

| Parameter        | Tool/Command                         |
|------------------|--------------------------------------|
| RMSD             | `gmx rms`                            |
| Gyration         | `gmx gyrate`                         |
| RMSF             | `gmx rmsf`                           |
| Pocket Area      | `gmx sasa`                           |
| Pocket Volume    | `gmx sasa -tv`                       |
| Clustering       | `gmx cluster`                        |
| Representative   | `gmx trjconv -dump [time]`           |
| Averages & SD    | `gmx analyze`                        |
