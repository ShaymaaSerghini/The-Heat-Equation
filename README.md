## Nonlinear Heat Equation — Numerical Methods in Python

### Overview

This project investigates a **1D nonlinear heat equation** using several numerical techniques for interpolation, integration, differentiation and PDE solving.

I built Python implementations of the main numerical methods, compared them with SciPy where possible, tested their convergence and accuracy, and used them to model the evolution and steady-state temperature of a heated bar.


### Skills & Technologies

**Python · NumPy · SciPy · SymPy · Matplotlib · Jupyter**

- Numerical ODE/PDE solving
- Lagrange interpolation
- Gauss-Lobatto / Chebyshev nodes
- Trapezium integration
- Finite differences
- Pseudospectral differentiation
- RK2, RK4 and RK45
- Convergence & error analysis
- Object-oriented programming
- Scientific visualisation



## Results

### 1. Modelling the Surroundings

I created a `Surroundings` class to model the spatial and time-dependent external temperature acting on the bar.

The implementation reproduced all four supplied test values exactly, validating the model before using it in later calculations.


### 2. Interpolation

I reconstructed the temperature profile using **Lagrange interpolation**, first deriving the polynomial with SymPy and then verifying it against SciPy.

With equally spaced points, higher-order interpolation developed oscillations near the boundaries.

I therefore switched to **Gauss-Lobatto nodes**.

#### Result

```text
6 nodes  → max error = 0.29829
9 nodes  → max error = 0.04198
13 nodes → max error = 0.00574
```

**13 Gauss-Lobatto points were sufficient to reduce the maximum error below 0.01.**


### 3. Numerical Integration

I implemented the **Trapezium Rule from scratch** and compared it with `scipy.integrate.trapezoid`.

For 11 points:

```text
Custom method: 2932.3331985741256
SciPy:          2932.333198574126
```

The two results matched to numerical precision.

A resolution study also showed the expected second-order convergence, with the test error falling from:

```text
1.77 → 4.64 × 10⁻¹²
```

as the grid was refined.

---

### 4. Numerical Differentiation

I compared two approaches for calculating temperature derivatives:

- **Finite differences**
- **Gauss-Lobatto pseudospectral differentiation**

For the first derivative:

```text
Finite Difference error = 14.845
Pseudospectral error    = 1.170
```

Increasing the pseudospectral resolution reduced the error below `0.1` at approximately **N = 26**.

#### Result

For this smooth temperature profile, the **pseudospectral method achieved substantially higher accuracy with fewer grid points**.



### 5. Solving the Heat Equation

I solved the time-dependent nonlinear heat equation using two independent approaches:

#### Finite Difference + RK2

The spatial derivatives were discretised using finite differences and the resulting system evolved using explicit RK2.

The simulation showed the initial central temperature peak gradually smoothing as heat diffused through the bar.

The timestep was constrained by the **CFL stability condition**.

#### Pseudospectral + RK45

I then replaced finite differences with pseudospectral derivative matrices and used SciPy's adaptive `solve_ivp` RK45 solver.

This provided higher spatial accuracy for smooth solutions while adaptive time stepping removed the need to manually select every timestep.


### 6. Steady-State Nonlinear Solution

Finally, I solved the nonlinear steady-state equation using a **relaxation method**.

The system was evolved artificially until the temperature stopped changing while enforcing:

```text
T(-L/2) = T(L/2) = 20
```

#### Result

```text
τ = 1 → 3708 iterations
τ = 2 → 1938 iterations
τ = 2.5 → failed to converge
```

Increasing the relaxation parameter therefore almost halved the iterations, but making it too large caused instability.


## What I Learned

- Numerical results must be **validated through convergence and error testing**, not just visually inspected.
- Node placement can matter as much as polynomial order in interpolation.
- Higher-order and pseudospectral methods can achieve much greater accuracy with fewer points.
- Numerical accuracy, stability and computational cost must be considered together.
- Parameters such as timestep and relaxation rate can determine whether a numerical method converges or fails.
- Comparing custom implementations with established scientific libraries is an effective way to validate numerical code.


## Future Applications

The techniques developed here can be applied to:

- Heat and diffusion modelling
- Black-Scholes-type PDEs
- Fluid and wave simulations
- Quantitative modelling
- Boundary-value problems
