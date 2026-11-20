# Luftbetankung — Aerial Refueling Simulation

MATLAB/Simulink project for the simulation and controller design of an aerial
refueling maneuver between two **Boeing 757-200** aircraft, using a nonlinear
6-degree-of-freedom (6-DOF) model and the
[gammasyn](https://github.com/pvogt09/gammasyn) robust pole-region assignment /
coupling-control toolbox.

> **Note:** `gammasyn` is a third-party toolbox and is **not** included in this
> repository. It must be available on the MATLAB path to run the controller design.

## Overview

- **Nonlinear 6-DOF aircraft model** (`nonlinear_6DOF.m`) with Boeing 757-200
  inertia and aerodynamic parameters (`initializeParameters.m`).
- **Two-aircraft formation**: a leader and a follower (offset by `deltah_offset = 10 m`),
  trimmed for straight and level flight (`trimValues.m`, `cost_straight_flight.m`).
- **Linearization** around the trim points (`implicit_linmod.m`, `model_implicit.m`)
  and assembly of the coupled system (`defineABC.m`).
- **Controller design** via `gammasyn` coupling control
  (`coupling_controller.m`), with an LQR initial guess and a pole region defined
  as the intersection of a circle and a hyperbola.
- **Refueling model**: fuel mass transfer `m_fuel = 28000 kg` over `20 s`,
  starting at `10 s` (`initializeParameters.m`).
- **Visualization**: MATLAB 3-D animations (`visual.m`, `OULA.m`) and an optional
  FlightGear interface (`runfg.bat`).

## Repository contents

| File | Description |
|------|-------------|
| `SixDOFSim.slx` | Main Simulink model |
| `SixDOFSim.slx.r2020b` | Backup of the model in R2020b format |
| `SixDOFModel.m` | Simulation setup: init, trim, linearize, LQR baseline |
| `nonlinear_6DOF.m` | Nonlinear 6-DOF equations of motion |
| `model_implicit.m` | Implicit-form wrapper for the nonlinear model |
| `implicit_linmod.m` | Numerical (implicit) linearization |
| `trimValues.m` | Trim-point search via `fminsearch` |
| `cost_straight_flight.m` | Trim cost function |
| `coupling_controller.m` | gammasyn coupling-controller design |
| `initializeParameters.m` | Aircraft constants (Boeing 757-200) |
| `defineABC.m` | Assemble the coupled state-space matrices |
| `normieren.m` | Normalization of the system |
| `coordTransfMatrix.m` | Coordinate transformation matrix |
| `vecToMat.m` | Skew-symmetric (cross-product) matrix |
| `visual.m`, `OULA.m` | 3-D visualization → AVI videos |
| `plane.avi`, `planefar.avi`, `trackOFplane.avi` | Example simulation videos |
| `runfg.bat` | FlightGear launch script |
| `TODO.TXT` | Open points (in German) |

## Requirements

- MATLAB (model saved for **R2020b**) with Simulink
- Control System Toolbox
- Optimization Toolbox (`fminsearch`, `fmincon`)
- [gammasyn](https://github.com/pvogt09/gammasyn) toolbox (external)
- [FlightGear](https://www.flightgear.org/) 2018.1.1 (optional, for `runfg.bat`)

## Getting started

1. Install the gammasyn toolbox and make it available on the MATLAB path
   (see `coupling_controller.m`, which calls `addpath('gammasyn')` and `startup`).
2. Run `SixDOFModel.m` to set up the model, trim the two aircraft, linearize the
   system, and compute the LQR baseline controller.
3. Run `coupling_controller.m` to design the gammasyn coupling controller.
4. Simulate in `SixDOFSim.slx`, then run `visual.m` or `OULA.m` to render the
   trajectory animation.
