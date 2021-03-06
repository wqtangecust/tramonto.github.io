---
title: Release Notes
folder: codes
permalink: release_notes.html
---

## v5.0 Notes

***

Tramonto-5.0 is a major release delivered in October 2013\. It is recommended that all users upgrade to Tramonto 5.0\. 
New features and capabilities in Tramonto 5.0 include:

### Software Features

*   GUI (beta) - Tramonto 5.0 delivers a beta version of a GUI for Tramonto. The GUI is designed based on [Teuchos](https://trilinos.github.io/teuchos.html) and [Optika](https://trilinos.github.io/optika.html) classes and also requires the software [QT](http://qt-project.org/) to run. 
The version of the GUI included with Tramonto 5.0 is still being developed and refined, and we are seeking input from users who elect to try this beta software.
*   ctest - new, comprehensive testing capabilities are available to users via ctest. All the example problems in the Tramonto repository are included in one or more of five testing categories. 
Those categories are based on runtimes as benchmarked on an 8-processor Mac OS X desktop machine with 2.66 GHz Quad-Core Intel Xenon processors and 16 GB of RAM. The specific ctest categories, the maximum individual runtimes, and the numbers of test in those categories are:
    *   Sanity (30 sec :: 57 cases )
    *   Checkin (100 sec :: 89 cases)
    *   Continuous (600 sec :: 122 cases)
    *   Nightly (2000 sec :: 133 cases)
    *   Occasional (7200 sec :: 11 cases)

### New Physics Features

*   Fickian Diffusion code extended to chain molecules. (see Tramonto/Examples/DIFFUSION2_FMT1_WCA_WJDC_1D)
*   Tethered chain capability rewritten and generalized to be able to treat:
    *   arbitrary surface geometry
    *   2D and 3D systems
    *   mixtures of tethered chains
    *   tethered branched chains
    *   constant surface density or number of tethered chainsSee the Tramonto/Examples/POLYGRAFT cases.

### New Solver Functionality

*   Tpetra based solvers delivered for Tramonto, enabling extended-precision computation.
*   Picard solver extended to electrostatic systems. (see Tramonto/Examples/MIX13_ELEC_PICARD_1D)
*   Picard solver extended to diffusion systems.
*   Newton solver extended to tehtered chain systems.
*   Parallel computing capability implemented for tethered chain systems.

### Refactored Code and Bug fixes

*   Addition of more than 14 new example problems to the release with an emphasis on diffusion and tethered polymer cases.*   Refactored of file I/O to allow for user definition of input and output paths.*   Refactored of input code to allow for GUI based input streams. The code allows generation of a GUI state using static input parameters.

## v4.0.1 Notes

Tramonto-4.0.1 is a release of bug fixes to Tramonto 4.0\. The following issues are addressed:

*   Bug fix for electrostatic free energy. Uninitialized variable causing failure on some platforms.
*   Bug fix for WJDC polymer recursion equation. Bug in residual and Jacobian affecting some polymer mixtures with varying bead sizes.
*   Bug fixes for atomistic diffusion cases. Atomistic diffusion working with standard AztecOO solvers (not Picard solvers or Schur solvers yet). One new test case is included in this release to demonstrate color diffusion through a permeable membrane.
*   Bug fix to include electrostatic term in bulk pressure calculation. Note that it is only nonzero if the DELTAC_GENERAL term is turned on and if the ion sizes differ.

## v4.0 Notes

Tramonto-4.0 is a major release to be delivered in July of 2011\. It is recommended that all users upgrade to Tramonto4.0\. New features and capabilities in Tramonto-4.0 include:

### Build System

*   A cmake based build system that is consistent with trilinos10.x.
*   Tramonto now can be built on Windows through Visual Studio. The Windows build is not fully supported for this release. We are planning to add a Windows installer for future releases.

### New Physics

*   Implementaion of a fourth hard-sphere FMT method.
*   Generalized electrostatics methods for electrolytes with species of unlike size.
*   Generalized methods for defining attractive functionals.
*   New pair potential interactions for summed Yukawa with repulsions to allow for studies of coarse-grained electrostatics systems.
*   Option for manual input of hard sphere diameters to allow for studies of nonadditive potentials.
*   Implementation of automated output for sum rule studies.
*   Generalization of surface modifications (roughess, periodic, and linear functions) to simple surfaces.
*   Generalized external field code to allow for different types of surfaces (e.g. Lennard-Jones and Yukawa) in one calculation.

### New Solver Functionality

*   Implementation of Schur solvers for analytic Jacobians for WJDC3 polymer methods.
*   Implementation of Schur solvers for charged systems.
*   Manual input option for physics based scaling of WJDC polymer systems.
*   Picard and Newton iterations and tolerances changed to be set independently.

### Refactored Code and Bug Fixes

*   Surface geometry code refactored to allow for easier code extensions and modifications.
*   External field code refactored to simplify treatment of any case where the external field is based on distance to surface or distance to center of wall or atom.
*   A new numerical scheme for computing derivatives of external field for every case except integrated surfaces.
*   Addition of more than 40 new example problems to the release with a special emphasis on 2D and 3D problems of various geometries.
*   Bug fixes for free energy calculations in charged systems.
*   Bug fixes for LAST_NODE boundary conditions.

## v3.1 Notes

Tramonto-3.1 is a minor release completed in August, 2009 that includes with some bug fixes and some new functionality as itemized here. It is recommended that all existing users download Tramonto-3.1\. See v3.0 release notes below for more details on recent and developing features in the Tramonto code.

*   New type of boundary condition (LAST_NODE_RESTART) is added to facilitate maintaining a solved boundary condition for a complex interface when surfaces are added to the problem.
*   Two new interaction potentials (square well, PAIR_SW, and cut and shifted exponential, PAIR_EXP_CS) are added to the suite of interaction potential options.
*   New Restart type (RESTART_FEWERCOMP) implemented to allow Restart of a multicomponent system from a file that contains a solved profile for fewer components. This is particularly helpful when studying contaminants at two phase interfaces.
*   A bug fix has been implemented for the printing of WJDC field parameters. This bug fix properly removes the Physiscs scaling parameters prior to printing. This field is only printed when Iwrite=VERBOSE, however the bug was causing problems with retsarts in various problems.
*   Several optimizations have been implemented for large scale parallel computing using Tramonto.
*   Bug fix for Type_pairPot=4 case. Previous release had a small positive contribution to the attractive term for 0<r<sigma. current="" version="" fixes="" this="" error.="" <="" li=""></r<sigma.>

## v3.0 Notes

Tramonto-3.0 is the second open source release of the Tramonto software completed in March 2009\.

**New features of the 3.0 release are as follows:**

*   Linear bonded systems have been implemented using the functionals of Jain, Dominik, and Chapman, J. Chem. Phys., 2007\.
*   Branched bonded systems have been implemented using the functionals of Jain and Chapman, manuscript in preparation for J. Chem. Phys., 2009\.
*   Generalized continuation methods have been implemented for multicomponent systems.
*   Segregation of supported general continuation methods, archived specialized contuation methods, and user plugin continuation methods. These developments allow users to implement new and specialized continuation methods with no significant impact on core Tramonto source code.
*   New continuation capability for performing binodal (phase transition tracking) calculations with mesh size as one continuation parameter. Makes studies of confined phase transitions more automated.
*   New continuation capability for using chemical potential as a continuation variable - makes bulk phase transition studies possible.
*   An improved interface for setting up two phase interface or diffusion calculations has been developed.
*   Implementations include improved flexibility for performing bulk calculations including generating Pxy diagrams in mixtures and performing partitioning studies.
*   Improved methods for generating initial guesses. It is now possible for Tramonto to automatically generate initial guesses for all field variables that are fully consistent with the initial guess for the density. It is still possible to generate guesses where all variables are based on bulk parameters as well.
*   Restart files only require density inputs at this time. All other fields are generated automatically. For polymer systems any calculation can be started by providing either segment density profiles or component density profiles.
*   New Picard and mixed Picard/Newton solver methods are now available with both built-in and NOX libraries.
*   Schur solver capabilities have been expanded to treat branched polymer systems, and new Jain-Dominik-Chapman polymer functionals.
*   Significantly expanded test harness (80 Example problems provided for testing and reference) including a variety of physical problems from bulk Pxy diagrams to self-assembled fluids.

**Developing (not fully implemented or not extensively tested) features in Tramonto as of the 3.0 release are as follows:**

*   Polarizeable fluids are limited to 1-dimensional computational systems.
*   Tethered polymer chains are only available to 1-dimensional computational systems.
*   Electrostatics methods are available, but not mature, and force sum rules are only correct for Poisson-Boltzmann electrolyte models.
*   Steady-State Diffusion methods are availbale, but have not be heavily used and tested.
*   Rough surfaces have been implemented for some surface types, but have not been extensively tested.

## v2.1 Notes

*   Tramonto-2.1 is the first general release of the open source software Tramonto (released in March 2007).
*   Tramonto-2.1 has been significantly refactored from the v2.0 limited release to improve the ease with which new physics functionality can be implemented.
*   Tramonto-2.1 has a new implementation of Yukawa fluids for modeling colloidal systems.
*   An interface to NOX solvers exists in the Tramonto-v2.1 release, but NOX is not the default nonlinear solver at the time of the v2.1 release.
*   Some known shortcomings of the software are listed in the v2.0 release notes below.

## v2.0 Notes

*   This version is a limited release version of Tramonto being implemented in preparation for the v2.1 public release in early 2007.
*   Computation of Electrostatic Free Energy is implemented only approximately except for the special case of the Poissson-Boltzmann electrolyte where the implementation is exact.
*   The pressure for WTC functionals (Tripathi-Chapman functionals using Werthiem's theory) has not been implemented.
*   Computation of bulk two phase coexistence is currently disabled and has not been implemented generally.
*   Stoichiometry errors (usually small) can be observed in CMS (Chandler-McCoy-Singer) functionals when hard surfaces are used.
*   Stoichiometry errors (of varying severity) are often observed with WTC functionals.

***

[Privacy and Security](http://www.sandia.gov/general/privacy-security/index.html)  