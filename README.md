# CLASS Modified for Cobaya Integration (Dark Energy Fluid)

This repository contains a modified version of [CLASS](https://github.com/lesgourg/class_public), introducing two new Dark Energy equation of state parametrizations (`SQRT` and `TANH`) based on [Artola et. al. 2025](https://arxiv.org/abs/2510.04191).

## Repository Structure & Modified Files

This repository hosts **only** the specific source code files that have been modified from the base CLASS distribution. These files must retain their exact paths relative to the `classy` root directory:

- **`include/background.h`**
    - Updates the `fluid_equation_of_state` enumeration to include the `SQRT` and `TANH` parametrizations.
    - Declares the new `n_sigmoid` variable within the `background` structure to control the steepness of the fluid transitions.
- **`source/input.c`**
    - Extends the input parsing mechanism to read the `n_sigmoid` parameter directly from `.ini` runs or Cobaya dictionaries.
    - Initializes `n_sigmoid` with a default value of `1.0`.
- **`source/background.c`**
    - Implements the actual mathematical formulations for the equations of state (`SQRT` and `TANH`) inside the `background_w_fld` routine.
    - Implements the corresponding analytical derivatives (`dw_over_da_fld`) necessary for consistent background and perturbation calculations.
 
The specific modifications can be identified by searching `Modified by ...`.

## Update and recompile Cobaya

To use these modifications within your Cobaya workflows, you must swap these modified files into your local CLASS installation and recompile the `classy` Python wrapper.

**Step-by-step installation guide:**

1.  **Locate your local CLASS installation**: Navigate to the directory where your CLASS source code used by Cobaya is located (typically somewhere like `cobaya_packages/code/classy/`).

    ```bash
    cd /path/to/your/cobaya_packages/code/classy
    ```

2.  **Back up existing files (Optional but Recommended)**:

    ```bash
    cp include/background.h include/background.h.backup
    cp source/input.c source/input.c.backup
    cp source/background.c source/background.c.backup
    ```

3.  **Copy the modified files**: Drop the modified files from this repository directly into your local CLASS structure, replacing the old ones.

4.  **Clean the previous build**: Clear out previous build artifacts to ensure a fresh compilation.

    ```bash
    make clean
    ```

5.  **Recompile CLASS and the `classy` wrapper**:
    ```bash
    # You can alternatively use `pip install -e .` if required by your setup
    make
    ```
    _(Note: If you are using a conda/virtual environment for Cobaya, make sure it is activated before running `make`, since CLASS automatically builds the Python `classy` extension using your active Python binary)._

## Cobaya Integration Snippet

Once recompiled, you can utilize the new models inside a Cobaya configuration (`.yaml`) file. Here is an example of how to pass the parameters to the `classy` theory code block:

```yaml
theory:
  classy:
    extra_args:
      # Switch to one of the new Dark Energy implementations
      fluid_equation_of_state: "TANH" # or 'SQRT'

# Standard Fluid EoS parameters
w0_fld: ...
wa_fld: ...

# New parameter controlling transition steepness
n_sigmoid: ...

# Ensure you enable the Omega_fld parameter and disable Omega_Lambda
```
