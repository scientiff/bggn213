Structure based drug design
================

## Download and process starting structure.

Here, we download and clean up the HIV-Pr sturcture. PDB Code: 1HSG from
the main PDB database. We will make a show “protein only” and “ligand
only” PDB files.

``` r
library(bio3d)

file.name <- get.pdb("1HSG")
```

    ## Warning in get.pdb("1HSG"): ./1HSG.pdb exists. Skipping download

We will use `read.pdb()`, `atom.select()`, and `write.pdb()` functions
to make our separate “protein-only” and “ligand-only” PDB files.

You “got” the PDB file, now you have to read it to see its contents.

``` r
hiv <- read.pdb(file.name)

hiv
```

    ## 
    ##  Call:  read.pdb(file = file.name)
    ## 
    ##    Total Models#: 1
    ##      Total Atoms#: 1686,  XYZs#: 5058  Chains#: 2  (values: A B)
    ## 
    ##      Protein Atoms#: 1514  (residues/Calpha atoms#: 198)
    ##      Nucleic acid Atoms#: 0  (residues/phosphate atoms#: 0)
    ## 
    ##      Non-protein/nucleic Atoms#: 172  (residues: 128)
    ##      Non-protein/nucleic resid values: [ HOH (127), MK1 (1) ]
    ## 
    ##    Protein sequence:
    ##       PQITLWQRPLVTIKIGGQLKEALLDTGADDTVLEEMSLPGRWKPKMIGGIGGFIKVRQYD
    ##       QILIEICGHKAIGTVLVGPTPVNIIGRNLLTQIGCTLNFPQITLWQRPLVTIKIGGQLKE
    ##       ALLDTGADDTVLEEMSLPGRWKPKMIGGIGGFIKVRQYDQILIEICGHKAIGTVLVGPTP
    ##       VNIIGRNLLTQIGCTLNF
    ## 
    ## + attr: atom, xyz, seqres, helix, sheet,
    ##         calpha, remark, call

# Prepare the initial protein and ligand input files for AutoDock.

Next, to select the atom look up `?atom.select` then use the correct
inputs.

``` r
prot <- atom.select(hiv, "protein", value = TRUE)
lig <- atom.select(hiv, "ligand", value = TRUE)

prot
```

    ## 
    ##  Call:  trim.pdb(pdb = pdb, sele)
    ## 
    ##    Total Models#: 1
    ##      Total Atoms#: 1514,  XYZs#: 4542  Chains#: 2  (values: A B)
    ## 
    ##      Protein Atoms#: 1514  (residues/Calpha atoms#: 198)
    ##      Nucleic acid Atoms#: 0  (residues/phosphate atoms#: 0)
    ## 
    ##      Non-protein/nucleic Atoms#: 0  (residues: 0)
    ##      Non-protein/nucleic resid values: [ none ]
    ## 
    ##    Protein sequence:
    ##       PQITLWQRPLVTIKIGGQLKEALLDTGADDTVLEEMSLPGRWKPKMIGGIGGFIKVRQYD
    ##       QILIEICGHKAIGTVLVGPTPVNIIGRNLLTQIGCTLNFPQITLWQRPLVTIKIGGQLKE
    ##       ALLDTGADDTVLEEMSLPGRWKPKMIGGIGGFIKVRQYDQILIEICGHKAIGTVLVGPTP
    ##       VNIIGRNLLTQIGCTLNF
    ## 
    ## + attr: atom, helix, sheet, seqres, xyz,
    ##         calpha, call

``` r
lig
```

    ## 
    ##  Call:  trim.pdb(pdb = pdb, sele)
    ## 
    ##    Total Models#: 1
    ##      Total Atoms#: 45,  XYZs#: 135  Chains#: 1  (values: B)
    ## 
    ##      Protein Atoms#: 0  (residues/Calpha atoms#: 0)
    ##      Nucleic acid Atoms#: 0  (residues/phosphate atoms#: 0)
    ## 
    ##      Non-protein/nucleic Atoms#: 45  (residues: 1)
    ##      Non-protein/nucleic resid values: [ MK1 (1) ]
    ## 
    ## + attr: atom, helix, sheet, seqres, xyz,
    ##         calpha, call

Now that you’ve put together the file, write it so you can save
“1HSG\_prot” and “1HSG\_lig” as PDB files.

``` r
write.pdb(prot, file="1hsg_protein.pdb")
write.pdb(lig, file="1hsg_ligand.pdb")
```

Now, switch to the AutoDock program to open those PDB files. Change the
formats to PDBQT files. The PDBQT file gives you the atom charge + atom
type. Now, make a config.txt file with the relevant arguments to specify
your files and inputs for the program.

> > Note that we skipped the “prepare the ligand” step during class due
> > to time constraints.

# Prepare a docking configuation file.

Install AutoDock Tools Vina. Run Vina using the Terminal. You have to
identify WHERE your program is and argue with it. (That’s why we made a
config. .txt file.)

# Dock the ligand onto the HIV-1 protease.

INPUT \> “Files (x86)Scripps Research Institute.exe” –config config.txt
–log log.txt

Once the run is complete, you should have two new files all.pdbqt, which
contains all the docked modes, and log.txt, which contains a table of
calculated affinities based on AutoDock Vina’s scoring function. The
best docked mode, according to AutoDock Vina, is the first entry in
all.pdbqt.

# Read docking results.

Now let’s inspect what we just made,

``` r
result <- read.pdb("all.pdbqt", multi=TRUE)

write.pdb(result, "results.pdb")
```

We can now view the docking as a movie on PyMOL. You can overlay your
results with the original to see what optimal docking (\#1) looks like.
Turn on the “surface” option of the 1HSG molecule in order to see where
interactions occur. (ex: In this model, the result is able to nest
within the water molecules.)

# Quantify the RMSD value of the results.

We can use R to quantify the results by calculating RMSD (root mean
square distance) between each docking result and comparing it to bio3d
crystal structures.

``` r
num_res <- read.pdb("all.pdbqt", multi = TRUE)
num_res
```

    ## 
    ##  Call:  read.pdb(file = "all.pdbqt", multi = TRUE)
    ## 
    ##    Total Models#: 18
    ##      Total Atoms#: 50,  XYZs#: 2700  Chains#: 1  (values: B)
    ## 
    ##      Protein Atoms#: 0  (residues/Calpha atoms#: 0)
    ##      Nucleic acid Atoms#: 0  (residues/phosphate atoms#: 0)
    ## 
    ##      Non-protein/nucleic Atoms#: 50  (residues: 1)
    ##      Non-protein/nucleic resid values: [ MK1 (1) ]
    ## 
    ## + attr: atom, xyz, calpha, call

``` r
ori <- read.pdb("ligand.pdbqt")
ori
```

    ## 
    ##  Call:  read.pdb(file = "ligand.pdbqt")
    ## 
    ##    Total Models#: 1
    ##      Total Atoms#: 50,  XYZs#: 150  Chains#: 1  (values: B)
    ## 
    ##      Protein Atoms#: 0  (residues/Calpha atoms#: 0)
    ##      Nucleic acid Atoms#: 0  (residues/phosphate atoms#: 0)
    ## 
    ##      Non-protein/nucleic Atoms#: 50  (residues: 1)
    ##      Non-protein/nucleic resid values: [ MK1 (1) ]
    ## 
    ## + attr: atom, xyz, calpha, call

``` r
rmsd(ori, num_res)
```

    ##  [1]  0.649  4.206 11.110 10.529  4.840 10.932 10.993  3.655 10.996 11.222
    ## [11] 10.567 10.372 11.019 11.338  8.390  9.063  8.254  8.978
