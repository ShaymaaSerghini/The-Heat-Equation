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

<img width="864" height="468" alt="10115d2e-38c3-4e9b-a48e-4f30d31d9661" src="https://github.com/user-attachments/assets/81414e07-682e-40e8-9b05-cc3aa978b355" />


<img width="864" height="468" alt="c4a2c125-259a-4f31-b003-7b2c45094c52" src="https://github.com/user-attachments/assets/72f4f579-817e-41ef-89ff-7385b4e4bb61" />


<img width="855" height="545" alt="b9a1ca1c-fe5e-4372-b693-0c4957842f7c" src="https://github.com/user-attachments/assets/952bc11a-1d93-47e8-b24e-ed0d082edb1f" />

The implementation reproduced all four supplied test values exactly, validating the model before using it in later calculations.

<img width="1092" height="453" alt="c067467c-68db-4f42-b183-baafb889185b" src="https://github.com/user-attachments/assets/c29c4d64-d57d-4725-b52c-8873da63b4b2" />

### 2. Interpolation

I reconstructed the temperature profile using **Lagrange interpolation**, first deriving the polynomial with SymPy and then verifying it against SciPy.

With equally spaced points, higher-order interpolation developed oscillations near the boundaries.


<img width="859" height="545" alt="e40aa341-bb25-49aa-80a0-6434a3f73698" src="https://github.com/user-attachments/assets/0d941445-8d26-4fc1-abe4-4956845a9aca" />


<img width="576" height="453" alt="9927ab9a-0e60-41f2-b241-b1f4ba9a18a8" src="https://github.com/user-attachments/assets/a841f3c9-cebb-4e82-982f-ee5eb4933138" />

![Uploading e40aa341-bb25-49aa-80a0-6434a3f73698.png…]()
<img width="576" height="453" alt="9be70907-24eb-40fe-b16a-321a646dedd8" src="https://github.com/user-attachments/assets/a6a64505-aef2-421e-83d7-69df525843ac" />

I therefore switched to **Gauss-Lobatto nodes**.

#### Result

<img width="602" height="247" alt="image" src="https://github.com/user-attachments/assets/2cb55e0b-380d-43fd-acc1-b8b4da712ba6" />

```text
6 nodes  → max error = 0.29829
9 nodes  → max error = 0.04198
13 nodes → max error = 0.00574
```

<img width="855" height="545" alt="3ecb2836-4c33-46fc-adb4-165b2030afc9" src="https://github.com/user-attachments/assets/9d95548b-bb5b-48b2-aa28-54ff909fd042" />

<img width="855" height="545" alt="9c80b87c-a5f4-4b24-ba56-f921832bc90b" src="https://github.com/user-attachments/assets/d8726896-d3a3-4599-8413-953eb8cc2d4a" />

<img width="855" height="545" alt="23d0a58c-7687-4746-a696-cd13c8c755cf" src="https://github.com/user-attachments/assets/64d2b2a8-aeed-472f-8374-62dcfeabf43f" />

<img width="855" height="545" alt="96402c1e-12f5-4241-acbb-4242d2e07f55" src="https://github.com/user-attachments/assets/0b43f3d4-15b5-40f3-ae2d-64bcceb011d9" />

**13 Gauss-Lobatto points were sufficient to reduce the maximum error below 0.01.**

<img width="855" height="545" alt="054bb124-5367-4c59-a39b-e517f316b3f6" src="https://github.com/user-attachments/assets/a444be2e-64de-4689-97b8-4c14c1b21534" />



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

Through this coursework, I learned how to translate a nonlinear physical problem into a sequence of numerical methods that could be implemented, tested and compared in Python. Rather than using numerical libraries as black boxes, I implemented several methods myself, including Lagrange interpolation, the Trapezium Rule, finite-difference derivatives and Runge–Kutta methods, before comparing my results with established SciPy implementations.

I also learned the importance of **validating numerical methods rather than relying only on the appearance of the final result**. Throughout the project, I used convergence studies, error measurements and comparisons with independent implementations to check whether my code was behaving as expected. This showed me that obtaining a plausible numerical answer is only part of the problem; I also need evidence that the answer is accurate and that the method converges correctly.

The interpolation work showed me that **how a numerical problem is represented can be as important as the order of the method itself**. Increasing the number of equally spaced interpolation points did not automatically improve the solution because of oscillations near the boundaries. By switching to Gauss-Lobatto nodes, I was able to substantially reduce the interpolation error. This helped me understand why careful grid and node selection matters in numerical modelling.

I also developed a better understanding of the trade-off between **accuracy, stability and computational cost**. Comparing finite-difference and pseudospectral differentiation showed me that higher-accuracy methods can sometimes achieve better results with considerably fewer spatial points. At the same time, solving the heat equation demonstrated that numerical accuracy alone is not sufficient: timestep restrictions such as the CFL condition must also be respected for the simulation to remain stable.

The relaxation calculation gave me practical experience with **numerical convergence and parameter selection**. Increasing the relaxation parameter reduced the number of iterations required to reach the steady-state solution, but increasing it too far caused the method to become unstable. This showed me that numerical parameters often involve a balance between computational speed and robustness rather than simply choosing the largest or smallest possible value.

I also learned how different numerical techniques can be combined to solve a larger problem. For example, I converted the spatial part of the heat equation into a system of ordinary differential equations using finite differences or pseudospectral differentiation and then solved the resulting time evolution using RK2 or adaptive RK45. This helped me understand how interpolation, differentiation, integration and ODE solvers fit together within a complete computational model.

Throughout the coursework, I used **Git and GitHub** to manage the development of the project, keep track of changes to my implementations and organise the numerical experiments and results. This gave me further experience maintaining a structured scientific coding project rather than treating each calculation as an isolated script.

Overall, this project showed me how mathematical theory, numerical analysis and scientific programming work together. I learned not only how to implement numerical methods, but also how to test their limitations, compare alternative approaches and decide whether a computational result can be trusted.

## Applying What I Learned

The techniques I developed in this project are transferable to a wide range of computational problems because many physical and quantitative models can ultimately be expressed as differential equations that must be solved numerically.

In future projects, I would reuse the same workflow of **formulating the mathematical problem, selecting an appropriate numerical representation, implementing the method, testing convergence and stability, comparing alternative approaches and validating the final result**.

The PDE-solving techniques could be extended to problems involving:

* heat and diffusion processes;
* fluid and wave simulations;
* boundary-value problems;
* reaction-diffusion systems;
* Black-Scholes-type financial PDEs;
* other quantitative models governed by differential equations.

I would particularly reuse the convergence and error-analysis techniques from this project. Testing how numerical error changes as the spatial or temporal resolution is increased provides a systematic way of checking whether an implementation behaves according to the underlying numerical theory.

I would also reuse the comparison between **custom numerical implementations and established scientific libraries**. Implementing a method myself gives me a deeper understanding of the algorithm, while comparing it with NumPy or SciPy provides an independent check that the implementation is correct.

More generally, this coursework gave me a framework that I can apply to future computational physics and quantitative modelling projects: **understand the mathematics, choose the numerical method carefully, implement it transparently, test its convergence and stability, and only then interpret the numerical output.**

