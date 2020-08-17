# Osprey basis set tools

Being a linear-combination modelling software, **Osprey** requires prior spectral knowledge in the form of so-called *basis sets*. These are collections of model spectra (*basis functions*) for the various metabolites you wish to quantify.

Historically, basis functions were acquired experimentally from aqueous solutions, which was a laborious and often incredibly frustrating task. Fortunately, many free software solutions open up a world of simulating any NMR experiment, and can generate noise-free model spectra of most of the spin systems we could be interested in.

In this chapter, you will learn how to generate an **Osprey** basis set from a set of synthetic spectra that have been simulated using the [FID-A software](https://github.com/CIC-methods/FID-A). Please refer to the example scripts in the repository for directions and how to do this. You can also ask for advice in the [Spectral Simulation](https://forum.mrshub.org/c/analysis-modeling/spectral-simulation/22) and [Basis Sets](https://forum.mrshub.org/c/analysis-modeling/basis-sets/23) categories of the [MRSHub forum](https://forum.mrshub.org).

## fit_makeBasis

For the purpose of this tutorial, we will assume that you have a set of FID-A simulations saved in a folder on your hard drive. Your folder may look like this:

<img src="../../img/05-fid-a-folder.png" alt="A folder full of FID-A simulations" width="200"/>

In this case, we have simulated a bunch of metabolites for the GE PRESS sequence at an echo time of 144 ms.

If this folder is located at, say, `/Users/Georg/MRS_Simulations/GE/press144`, you can create a variable called `folder` at the MATLAB prompt:

```matlab
>> folder = '/Users/Georg/MRS_Simulations/GE/press144';
```

You can then call **Osprey**'s `fit_makeBasis` function to create a basis set:

```matlab
>> [BASIS] = fit_makeBasis(folder, 1, 'unedited');
```

This will generate a struct array called `BASIS` in the MATLAB workspace, and also save it to the current MATLAB working directory. This file is your new **Osprey** basis set. If the `addMMFlag` argument (see below) is set to `1`, the filename of the basis set is going to be `BASIS_MM.mat`; if it is set to `0`, the filename will be `BASIS_noMM.mat`.

### Complete syntax

```matlab
>> [BASIS] = fit_makeBasis(folder, addMMFlag, sequence, editTarget);
```

#### Inputs

| Input | Type | Description | Mandatory? | Options |
| ----  | ---- | ----------- | --------- | ------- |
| folder | String | Complete path to the folder containing `.MAT` files with FID-A-simulated metabolite basis functions| Yes | --- |
| addMMFlag | Boolean | If set to `1` (default), Osprey will automatically add macromolecule and lipid basis functions to the basis set | No | `1` (default), `0` |
| sequence | String | Determines the type of sequence that you want to create a basis set for. Will try to automatically determine the order of editing steps from saturated NAA and water signals. | No | `'unedited'` (default), `'MEGA'`, `'HERMES'`, `'HERCULES'` |
| editTarget | String | Determines the target molecule(s) of the editing experiment. Only needs to be set if `sequence` is set to something other than `'unedited'`. | No | `'GABA'` (default), `'GSH'` |

#### Outputs

| Output | Type | Description | Mandatory? | Options |
| ----  | ---- | ----------- | --------- | ------- |
| BASIS | Struct | Structure containing the **Osprey** basis set. | Yes | --- |

## fit_plotBasis

Once you have generated the basis set, you may want to plot the basis functions and inspect that they look like you expect them to. For this purpose, **Osprey** includes a function called `fit_plotBasis`.

`fit_plotBasis` acts on the contents of an **Osprey** `.MAT` basis set file. If you load any basis set into MATLAB by double-clicking on it, you will see the struct `BASIS` in the MATLAB workspace:

<img src="../../img/05-basis-in-workspace.png" alt="An Osprey basis set after loading into MATLAB"/>

You can then call `fit_plotBasis` to create a plot overview of the basis set:

```matlab
>> fit_plotBasis(BASIS, 1, 1);
```

This command will generate a stack plot of all basis functions (including macromolecules and lipids, if you have chosen to include them in the basis set) along with a ppm axis:

<img src="../../img/05-plot-basis.png" alt="Output from fit_plotBasis"/>

### Complete syntax

```matlab
>> out = fit_plotBasis(basisSet, dim, stagFlag, ppmmin, ppmmax, xlab, ylab, figTitle);
```

#### Inputs

| Input | Type | Description | Mandatory? | Options |
| ----  | ---- | ----------- | --------- | ------- |
| basisSet | String | Name of the struct in the MATLAB workspace containing the Osprey basis set (usually `BASIS`) | Yes | --- |
| dim | Integer | Dimension of the basis function that you want to plot. For unedited data, this will be `1`. MEGA-edited data will have the `off` spectrum in dimension 1, the `on` spectrum in dimension 2, the difference spectrum in dimension 3, and the sum spectrum in dimension 4. | No | Default: `1` |
| stagFlag | Boolean | Determines whether basis functions are plotted vertically staggered or simply on top of one another. | No | `1` = staggered (default), `0` (not staggered) |
| ppmmin | Float | Lower limit of ppm axis to plot. | No | Default: `0.2` |
| ppmmax | Float | Upper limit of ppm axis to plot. | No | Default: `5.2` |
| xlab | String | X-axis label | No | Default: `'Frequency (ppm)'` |
| ylab | String | Y-axis label | No | Default: `''` |
| figTitle | String | Figure title | No | Default: `''` |

#### Outputs

| Output | Type | Description | Mandatory? | Options |
| ----  | ---- | ----------- | --------- | ------- |
| out | Figure handle | Figure handle for the output figure | Yes | --- |