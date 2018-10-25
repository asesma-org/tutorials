# INSTRUCTIONS FOR EXERCISE 2

Note that the following color code has been used in this instruction sheet:

- File names are in magenta,
- Phrases to be typed into the command line are in blue.
- Input parameters are in dark green.

In this exercise, we will examine some of the 'post-processing' that you can do once you have run your scf calculations.

In the previous exercise, there was only one output quantity that we cared about: the total energy.

Here we will see some other things that we can extract from the files produced when one does a PW calculation, e.g., charge densities and eigenvalues (which we will use to get band structure plots and densities of states). We will continue to work with Si, which was the subject of yesterday's exercise. After that you will go on to calculating the electronic structure of GaAs and Al.

## What you will learn today

- How to calculate the band structure of Silicon along the main high-symmetry directions

- How to calculate the density of states of Silicon

- How to plot the charge density of Silicon for a given plane

- How to calculate metals

- How to find all of the information you need to perform a band structure (building up the crystal structure, choosing pseudopotentials and band structure kpoint paths)

### Getting the files for the tutorial

When you are reading this, you already have all the necessary files.

### In a terminal, go to the directory exercise2/Si/ (if you are not already there!)
You will see the following files:

- exercise2_instructions2018.html: This file containing the instructions
- Si.scf.in: This is an input file for scf calculations.
- bands.in: This is an input file for collecting bands.
- k-point-path: This is a file containing list of k-points along symmetry directions in the Brillouin zone.
- Si.plotband.in: This is an input file for putting band structure data into a plottable format.
- Si.pp_rho.in: This is an input file to extract charge density using post-processing.
- Si.plotrho.in: This is an input file to plot the charge density.
- dos.in: This is an input file to calculate the density of states.

## Step 0: Self-consistent (scf) calculations for Si
Open and read the sample file `Si.scf.in`. We chose values of `celldm(1)` , `ecutwfc`, `nk1`, `nk2` and `nk3` based upon our results for the first exercise.

You may choose the same values as these, or substitute the values that you obtained yesterday.

Now run the scf calculation:

```$ pw.x < Si.scf.in > Si.scf.out```


## Step 1: Band structure of Si
### Calculations for bands
- Copy `Si.scf.in` to `Si.band.in`
- Edit `Si.band.in` to perform a non self-consistent calculation that will be used to obtain the band structure, by setting calculation='bands'.
- Add to the &SYSTEM namelist: nbnd = 8 to specify the number of bands computed. Note that for a 2-atom Si cell, we have 8 electrons and therefore only 4 occupied bands, but we are going to compute some extra (empty) bands.
- Define the K_POINTS card to specify the path along symmetry directions in Brillouin zone. In order to this, you can use the information supplied in the file called k-point-path, which contains a list of k-points along high symmetry directions in the BZ, i.e., L (½, ½, ½) to Gamma (0, 0, 0) to X (0, 0, 1) to W (0, ½, 1) to X (0, 1, 1) to Gamma (0, 0, 0). You can paste that file to `Si.band.in` after the K_POINTS card.
- Now run the 'bands' calculation:

  ```$ pw.x < Si.band.in > Si.band.out```

What differences do you see between the output file you obtain here, and the output file that you obtained from your scf calculation?

### Collect band results for plotting
Now open the file `bands.in`. Note that you have to use the same prefix in this calculation as was used in scf AND bands calculations.

The flag `filband` defines the name of the file in which bands data is stored.

Run:

```$ bands.x < bands.in > bands.out```

Have a look at bands.out and bands.dat and note the minimum and maximum energy eigenvalues at different k-points.

### Get the data in a format to plot
Now open the file `Si.plotband.in`.

- The first line contains (a) input file (= bands.dat obtained in the earlier step).
- Then (b) Emin and Emax (= -6.00 and 10.00) -> the minimum and maximum energy used for the plot.
  - output file in xmgrace format (bands.xmgr)
  - output file in ps format (bands.ps)
  - Fermi energy (= 6.337 eV)
  - deltaE and reference energy (= 1.00 6.337)

Now run the plotting program:

```$ plotband.x < Si.plotband.in > Si.plotband.out```

You can view the plotted band structure written in ps format (bands.ps) using a postscript viewer (e.g., gv); or by opening the file bands.xmgr in xmgrace. Note that you can use the data from Si.plotband.out to customize the plot (e.g., adjust x-range, add labels, ...).

## Step 2: Charge density for Si
### Post-processing to extract the charge density
Open the file `Si.pp_rho.in`. Make sure that the value of prefix is the same as the values as were used in scf calculations.

- filplot = 'Si.charge' in the &inputpp namelist saves the extracted charge density.
- This file is then used for plotting by defining filepp(1) = 'Si.charge' in the &plot namelist and the data to be plotted is written into fileout='Si.rho.dat'.
- plotnum = 0 in the &inputpp namelist indicates that the quantity to be extracted is the charge density (for other possible options, see INPUT_PP.* in the directory Doc in the espresso package).
- iflag and output_format specify the dimensionality for the plot.
- e1(i) and e2(i) (i = x, y and z) are 3D vectors that determine the plotting plane and must be orthogonal to eact other.
- nx and ny are the number of points in the plane.

Now run the post-processing code:

```$ pp.x < Si.pp_rho.in > Si.pp_rho.out```

### Plot the extracted charge density
Open the file Si.plotrho.in.

- The first line contains the name of the output file from the earlier step (Si.rho.dat)
- The second line contains the name of the file to which output is to be written, that is Si.rho.ps (in ps format).

Now run the plotting code:

```$ plotrho.x < Si.plotrho.in > Si.plotrho.out```

Use gv to view the charge density of Si.

### Optional: Isosurface plots of the charge density
Use the documentation for pp.x to find out how you can produce a file that XCrysDen can read. You will have to change the output_format variable and adjust the filenames. Then run pp.x again.



## Step 3: Density of states (DOS) plot for Si
### nscf calculations for DOS
- Copy Si.scf.in to Si.nscf.in
- Change the flag calculation='nscf'
- Change the number of bands to add a few empty states in the namelist &SYSTEM (nbnd = 8).
- Define occupations='tetrahedra' in the namelist &SYSTEM.
- Modify the K_POINTS card from “6 6 6 1 1 1” to “12 12 12 1 1 1”, this is because we now want to compute eigenvalues on a finer mesh in k-space.
- Remember that the same wavefunctions (as obtained from scf calculations) have to be used, so do NOT change prefix.

Now run the nscf calculation:

```$ pw.x < Si.nscf.in > Si.nscf.out```

### Calculate and plot DOS
- Open dos.in.
- Make sure that it has the same prefix as used in the scf calculations.
- The DOS results are written in fildos (= dos.dat)

Now run the density of states calculation:

```$ dos.x < dos.in > dos.out```

Have a look at dos.out and the data file dos.dat. Plot dos.dat to see the DOS plot (you can use gnuplot or xmgrace programs).

### Optional:
Try running the nscf calculation with different k-point grids and see how the shape of the DOS changes.


## Challenge task: GaAs
Calculate the band structure and density of states for GaAs using the files provided in the directory GaAs/. Compare the band structures of Si and GaAs.


## Step 4: Calculations of Metals (Al)

### In a terminal, go to the directory exercise2/Al/ (if you are not already there!)

You will see the following file: `Al.scf.in`

How is this file different to the input file that we used for Si earlier? Perform each of the calculations below for Al:

### Calculate and plot the density of states (DOS)
Perform a nscf calculation for the density of states. Plot your results.

### Optional: Calculate and plot the band structure.
Perform a nscf calculation for the band structure. Plot your results.

You might find the PDF in the folder useful for choosing high-symmetry Brillouin zone points!