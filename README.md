# CLASS Modified for Cobaya Integration (Dark Energy Fluid)

This repository contains a specialized, modified version of [CLASS](https://github.com/lesgourg/class_public) (Cosmological Linear Anisotropy Solving System), explicitly tailored to introduce new Dark Energy Fluid parametrizations (`SQRT` and `TANH`) for seamless integration within a [Cobaya](https://cobaya.readthedocs.io/) environment. Modified by Artola M. (2026).

## 📁 Repository Structure & Modified Files

This repository hosts **only** the specific source code files that have been modified from the base CLASS distribution. These files must retain their exact paths relative to the `classy` root directory:

- **`include/background.h`**
  - **Modifications:**
    - Updates the `equation_of_state` enumeration to include the new `SQRT` and `TANH` parametrizations.
    - Declares the new `double n_sigmoid` variable within the `background` structure to control the steepness of the fluid transitions.
- **`source/input.c`**
  - **Modifications:**
    - Extends the input parsing mechanism to read the `n_sigmoid` parameter directly from `.ini` runs or Cobaya dictionaries.
    - Initializes `n_sigmoid` with a default value of `1.0`.
- **`source/background.c`**
  - **Modifications:**
    - Implements the actual mathematical formulations for the new Equation of State (EoS) functions (`SQRT` and `TANH`) inside the `background_w_fld` routine.
    - Implements the corresponding analytical derivatives (`dw_over_da_fld`) necessary for consistent background and perturbation calculations.

## 🧮 New Variables & EoS Models

### 1. `n_sigmoid` Variable

A new parameter, `n_sigmoid`, has been introduced. It governs the transition steepness or the width of the functional step in the dark energy equation of state evolutions.

### 2. Equation of State (EoS) Models

The `fluid_equation_of_state` can now accept these two new models (in addition to standard types like `CLP` and `EDE`):

- **`SQRT`**: Defines the equation of state using a square-root/power-law smoothed transition model. It uses `w0_fld`, `wa_fld` and `n_sigmoid`.
- **`TANH`**: Defines the equation of state using a hyperbolic tangent `tanh` setup, providing a smooth, symmetric switch governed by `n_sigmoid`.

## ⚙️ How to Update and Recompile within Cobaya

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

## 🐍 Cobaya Integration Snippet

Once recompiled, you can immediately utilize the new EoS models inside a Cobaya configuration (`.yaml`) file. Here is an example of how to pass the parameters to the `classy` theory code block:

```yaml
theory:
  classy:
    extra_args:
      # Switch to one of the new Dark Energy implementations
      fluid_equation_of_state: "TANH" # or 'SQRT'

      # Standard Fluid EoS parameters
      w0_fld: -1.0
      wa_fld: 0.1

      # New parameter controlling transition steepness
      n_sigmoid: 1.5

      # Ensure you enable the fluid and disable lambda
      Omega_Lambda: 0.0
      Omega_fld: 0.69
```
