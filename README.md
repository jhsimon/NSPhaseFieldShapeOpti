# NSPhaseFieldShapeOpti
Phase-field based shape optimization for Navier--Stokes flow problems using FreeFEM

The project supports stationary and time-dependent optimization using Newton solver for the stationary Navier--Stokes equation and implicit/explicit scheme for the time-dependent version, adjoint equations, PDAS phase-field updates, Brinkman penalization, and Armijo backtracking.

Related work
------------

This code is related to the work "Long-time behavior of solutions to a fluid dynamic shape optimization problem via phase-field method" by Michael Hinze, Christian Kahle, and John Sebastian H. Simon. The paper studies time-dependent Navier--Stokes shape and topology optimization using a stationary phase-field and porous-media approximation, and proves convergence of time-dependent minimizers to stationary minimizers as the time horizon tends to infinity.

Preprint: https://arxiv.org/abs/2601.13293

Citation
--------

If this code helps your research, you can cite our paper:

Michael Hinze, Christian Kahle, and John Sebastian H. Simon, "Long-time behavior of solutions to fluid dynamic shape optimization problems via phase-field method", to appear in Interface and Free Boundaries, arXiv:2601.13293, 2026.

Files
-----

- init_script.edp: problem parameters, mesh, finite element spaces, variational forms, and objective functionals.
- state_sol.edp: state solver, adjoint solver, time-dependent solvers, and PDAS solver.
- target_sol.edp: generates the target velocity field.
- optim_stat.edp: stationary optimization routine.
- optim_transient.edp: time-dependent optimization routine.

Usage
-----

1. Generate the target velocity:

```bash
FreeFem++ target_sol.edp
```

This creates the target velocity data:

```bash
target/ux.txt
target/uy.txt
```

2. Run the stationary optimization:

```bash
FreeFem++ optim_stat.edp
```

This performs the stationary optimization loop:

- state solve,
- adjoint solve,
- PDAS phase-field update,
- Armijo line search,
- objective evaluation.

Stationary output is saved in:

```bash
stat_dat_folder/
stat_dat_folder/state/
stat_dat_folder/phase/
stat_dat_folder/objectivevalue.txt
```

3. Run the time-dependent optimization:

```bash
FreeFem++ optim_transient.edp --time 0.5
```

The option `--time` sets the final time horizon. For example:

```bash
FreeFem++ optim_transient.edp --time 1.0
```

The transient routine performs:

- time-dependent state solve,
- backward-in-time adjoint solve,
- PDAS phase-field update,
- Armijo line search,
- time-averaged objective evaluation.

Transient output is saved in a folder whose name depends on the chosen final time:

```bash
transient_T-0.5_dat_folder/
transient_T-0.5_dat_folder/state/
transient_T-0.5_dat_folder/phase/
transient_T-0.5_dat_folder/objectivevalue.txt
```

For example, using `--time 1.0` saves output in:

```bash
transient_T-1_dat_folder/
```

For `T = 0.5`, the transient routine initializes `phi = 1`. For larger time horizons, it attempts to initialize from a previous solution at `T/2`.

Recommended workflow
--------------------

```bash
FreeFem++ target_sol.edp
FreeFem++ optim_stat.edp
FreeFem++ optim_transient.edp --time 0.5
```

Output files
------------

Visualization files are saved in VTK/VTU format and can be opened in ParaView.

The objective history is stored in `objectivevalue.txt` inside the corresponding output folder.

Requirements
------------

- FreeFEM++
- iovtk
- UMFPACK
