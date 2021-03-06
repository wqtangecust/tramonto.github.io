---
title: Physical Edits/Tips
folder: developer_pages
permalink: tips_for_physical_edits.html
---

## Tramonto 4.x Tips for Making Physics Modifications in Tramonto

This page contains information specific to Tramonto 4.0. Click the following links for information on previous releases: [: Tramonto v3.0/3.1](developer_physics.html)

## Modifying Tramonto - v4.0

There are several ways in which a user might want to modify the capabilities in Tramonto in order to study a particular problem of interest. This page is meant to provide basic instructions on what is involved for some common manipulations. For modifications not described here send e-mail to tramonto-help@software.sandia.gov for additional guidance. Modifications discussed here include:

*   [New Arc-Length Continuation Parameters](developer_physics_4.html#CONT)
*   [New External Field Potential (1D)](developer_physics_4.html#VEXT)
*   [New Fluid Interaction Potential](developer_physics_4.html#ATT)
*   [New FMT Hard-Sphere Functional](developer_physics_4.html#FMT)
*   [New DFT with new Fields](developer_physics_4.html#NEWDFT)
*   [New Integration Stencil](developer_physics_4.html#NEWSTENCIL)

Note that any significant modification or addition of source code (*.c files) may require modification of header files (*.h files) and a new configure, in addition to a new compile. In these cases once your modifications are complete be sure to:

1.  Either manually edit header files or run the [makeheaders](../makeheaders.html) utility (source code is included in Tramonto/Utilities) to ensure header files are current.
2.  Add new files (new.c and/or new.h) to the lists in CMakeLists.txt
3.  run cmake in the build directory.

<a name="CONT"></a>

### Implement a New Arc-Length Continuation Strategy

All of the build-in continuation options distributed with Tramonto-4.x can be found in dft_switch_continuation.c. The numerical IDs associated with different continuation types are enumerated in dft_globals_const.h. The continuation IDs 0-10 are currently in use, but all of the continuation IDs 0-99 are reserved for the development of general methods. However, continuation may be performed in any combination of continous variables (for example varying densities of two components in a multicomponent system simultaneously). It is impossible to encode all of the possibilities apriori. Therefore a user plug-in space has been created for specialized implementations.

Some specialized implementations are provided in the file dft_plugin_archived_continue.c (continuation IDs 100-199). These options have been used by the Sandia Tramonto team for specialized investigations, but are not general in nature. This file has the same structure as dft_switch_continue.c. For a user who would like to implement their own methods, an additional file (dft_plugin_user_continue.c) is provided. The continuation IDs for locally defined methods should be >199\. The routines that must be populated in order to perform continuation calculations are:

*   **double get_init_param_archived_plugin(int cont_type,int Loca_contID)**: returns the inital value of the parameter that will be varied.
*   **void assign_param_user_plugin(int cont_type, int Loca_contID, double param)**: assigns the parameter during the course of a continuation run.
*   **void print_cont_type_user_plugin(int cont_type, FILE *fp,int Loca_contID)**: prints a character string identifier for the continuation variable(s) to dft_output.dat.
*   **void print_cont_variable_user_plugin(int cont_type,FILE *fp,int Loca_contID)**: prints continuation variable(s) to dft_output.dat. <.li>

To implement a new continuation method, the following steps are required.

1.  Choose an identifier for the new continuation method. Set it in dft_globals_const.h. Set up switches to reference the new identifier in the four routines in dft_plugin_user_continue.c.
2.  Implement code to perform the four functions in dft_plugin_user_continue.c.
3.  call functions to recalculate thermodynamics, stencils, or external fields if necessary. Note these functions are most likely already available so refer to the routine adjust_dep_params() found in dft_switch_continuation.c for reference.
4.  adjust header files manually or run the makeheaders utility prior to rebuilding code.

Additional guidance on parameters can be found at the top of dft_plugin_user_continue.c.

<a name="VEXT"></a>

### Add a New External Field Potential (1D)

Changing the external field potential requires only small changes to the Tramonto source code.

1.  Add a new 1D external field either in dft_vext_1D.c as a new function or in a new user generated file.
2.  Assign a key word name and ID number to the new potential type in dft_globals_const.h or in a new user generated header file user.h. (as in #define NEW_VEXT_1D 8). When choosing the ID number respect existing external field types in dft_globals_const.h.
3.  Add the new key word option to the switch statements in dft_switch_vext1D.c

_Note: as of Tramonto 4.0, the external field derivatives are computed numerically rather than analytically to allow a more flexible application to complex compound surface geometries. Therefore it is not necessary to add code associated with external field derivatives._

<a name="ATT"></a>

### Implement a New Fluid Interaction Potential

Follow these instructions to implement a new interaction potential for pairwise fluid interactions or for defining an external field based on pairwise potentials.

1.  Create a new file to contain several functions associated with the new mean field pair potential. Any of the existing pair potential files (dft_pairPot__func_.c) can be used as as a template and for reference.
2.  Identify the input parameters that should be used to compute this potential in case it will be used for fluid-fluid, fluid-wall, or wall-wall interactions.
3.  Write functions to compute:

    *   The value of pair potential.
    *   The derivative of the pair potential.
    *   The properties of the inner core for various treatments (e.g. weeks-chandler-anderson vs zero core potential).
    *   The attractive part of the pair potential to be used in the DFT perturbation calculation.
    *   The pair potential without any cut and shift applied.
    *   The integral of the potential.
4.  Assign a key word name and ID number to the new potential type in dft_globals_const.h or in a new user generated header file user.h. (as in #define NEW_PAIR_POT 10). When choosing the ID number respect existing pair potential types in dft_globals_const.h.
5.  Add the new key word option to the switch statements in dft_switch_pairPot.c

_Implementing all of the routines defined here automatically adds the new potential as an option for calculation of external fields based on the new pair potential._

<a name="FMT"></a>

### Implement a New FMT Hard-Sphere Functional

The implementation described here assumes that the new functional does not require new stencil functions or variables. It is based only on scalar and vector nonlocal densities already defined in the Tramonto code.

1.  Register the new type of FMT functional in the file dft_globals_const.h (or in a user supplied header file) with a new #define FMT_NEW statement. Be sure to respect existing defined options for the Type_func parameter.
2.  Create a new file to contain essential physics for the new FMT functional (e.g. dft_physics_FMTnew.c).
3.  Create functions in dft_physics_FMTnew.c to compute:

    *   the free energy density.
    *   the first derivative of the free energy denstiy with respect to nonlocal densities.
    *   the second derivatives of the free energy with respect to nonlocal densities. Note that the matrix fill logic implementation requires segregation of the second derivative calculations into two routines. The first should collect all terms that will be integrated over with a delta function weighting. The second should collect all terms that at will be integrated over with a step funtion (theta function) weighting.It may be helpful to study existing code in other dft_physics_FMT#.c files when doing the implementation.
4.  Modify all the switches in dft_switch_FMT.c to include the new functional type (FMT_NEW).

<a name="NEWDFT"></a>

### Implement a New DFT Method that Introduces New Fields

1.  Determine if new theory will require new integration stencil(s). If so see directions below for implementation of a new integration stencil.
2.  Define a key word and ID number for the new variable (#define NEW_VARIABLE 8) in dft_globals_const.h or in a user supplied function.
3.  Increase the parameter NEQ_TYPE in dft_globals_const.h.
4.  Edit setup_nunk_per_node() in dft_defs_unknowns.c to define the number of unknowns associated with this variable Phys2Nunk[NEW_VARIABLE], and set logicals Lseg_densities and L_HSperturbation.
5.  Edit the switch in load_standard_node() in dft_fill_main.c to include the new variables.
6.  Create new dft_fill_physics.c file to contain the logic associated with calculation of the residual equations (and Jacobian entries) associated with the new variable or equation.
7.  If performing an extension to existing functionals modify existing fill routines. For example if you are adding a new term to a FMT based perturbation DFT calculation, modify the logic in load_euler_lagrange() which can be found in dft_fill_EL.c. New routines that define the contributions of the new physics to the Euler-Lagrange equation should be located in the new dft_fill_physics.c routine.
8.  Modify switch in constant_boundary() found in dft_utils.c to define boundary conditions for new variable.
9.  Modify logic in dft_thermo.c to include new terms in computation of bulk thermodynamics.
10.  Create new file dft_thermo_physics.c to contain new physics specific bulk thermodynamic calculations for the new theory.
11.  Modify logic in set_initial_guess() found in dft_guess.c to include the new variable in the switch that controls computation of an initial guess.
12.  Add a new file dft_guess_physics.c that includes initial guess routines for the variables associated with the new theory.
13.  Modify logic in print_profile() in dft_out_profiles.c so that the key word and results for this variable are printed in the dft_dens.dat file.
14.  Modify logic in dft_energy.c to compute (postprocess) free energy for new theory.
15.  Add a new file dft_energy_physics.c that includes contributions to the free energy from the new theory.

<a name="NEWSTENCIL"></a>

### Implement a New Type of Integration Stencil

1.  Register an identfication tag for the new integration stencil (using a #define STENCIL_NEW statement) in either dft_globals_const.h or in a user supplied header file. Be sure to respect the existing defined options for the stencil types.
2.  Increase the value of the parameter NSTEN in dft_globals_const.h. This parameter is a count of the integration stencil types Tramonto has currently implemented.
3.  Edit setup_stencil_logical() found in dft_defs_stencil.c to turn on the new integration stencil (i.e. set Sten_Type[STENCIL_NEW]=TRUE) based on input file settings for the functional type parameters (Type_func, Type_attr, etc)
4.  Edit stencil_deltaLogical() found in dft_defs_stencil.c to include STENCIL_NEW as a case in the switch. The logical returned here simply indicates if the integration stencil is based on a delta function.
5.  Add the new STENCIL_NEW case to all the switch routines found in dft_switch_stencil.c, and reference functions associated with the new integration stencil.
6.  Create a new file (i.e. dft_stencil_type_range.c) to contain stencil specific routines.
7.  Write new functions that define the new integration stencil. These functions set:

    *   the stencil range (radius).
    *   the stencil volume (which may or may not be known analytically).
    *   the order of the stencil (first order depends only on one component, 2nd order stencils depend on two components).
    *   the quadrature parameters used to compute the integration stencils.
    *   a local stencil weight given the square of the distance from the stencil origin.Note that it may be helpful to study source code in one or more of the routines dft_stencil_theta*.c or dft_stencil_delta*.c before writing the new functions associated with STENCIL_NEW.

***

[Privacy and Security](http://www.sandia.gov/general/privacy-security/index.html)  


