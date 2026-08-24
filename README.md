# AdvDiff

A C++ library of finite-difference solvers for **time-evolving PDEs**: 1D advection (hyperbolic), Forward-Time Centered-Space diffusion (parabolic), and Black-Scholes, which reduces to the same diffusion machinery under a change of variables.

This repo split off from [numkit](https://github.com/jyahdi-byte/numkit), which keeps the elliptic (steady-state) solvers — Jacobi, Gauss-Seidel, SOR, Conjugate Gradient, and their threaded/CUDA variants. Time-marching problems raise a different set of concerns than steady-state iteration: stability limits on the time step (CFL, r ≤ 1/2), and exact traveling or diffusing solutions to validate against, rather than convergence to a fixed point.

---

## Highlights

- Implemented an upwind finite-difference solver for the 1D advection equation (hyperbolic)
- Implemented Forward-Time Centered-Space (FTCS) for the 1D diffusion equation (parabolic)
- Investigated CFL and diffusion stability limits, including deliberate stability-violation experiments
- Validated against exact analytical solutions: a translated initial condition for advection, a Gaussian solution for diffusion, and the closed-form Black-Scholes formula
- Implemented Black-Scholes via a change of variables into the existing diffusion (FTCS) machinery

---

# Hyperbolic PDEs

AdvDiff implements an upwind finite-difference solver for the 1D advection equation:

u_t + c u_x = 0

Features include:

- Periodic boundary conditions
- Courant-number calculation
- CFL stability analysis
- Exact-solution comparison
- Numerical-diffusion experiments

For the exact solution,

u(x, t) = u₀(x − ct)

the numerical solution can be compared directly against the translated initial condition.

The repository also includes deliberate CFL-violation experiments to demonstrate instability when the stability condition is exceeded.

---

# Parabolic PDEs

AdvDiff implements Forward-Time Centered-Space (FTCS) for the 1D diffusion equation:

u_t = α u_xx

with stability parameter:

r = α Δt / Δx²

and explicit stability condition:

r ≤ 1/2

Features include:

- FTCS diffusion solver
- Gaussian analytical solution
- Conservation testing
- Stability-violation experiments
- Grid-refinement studies
- Error analysis

## Diffusion Convergence Study

The convergence experiments hold

r = α Δt / Δx²

constant while refining the spatial grid.

Therefore,

Δt ∝ Δx²

Measured refinement ratios include:

```text
4.01
4.00
4.00
```

matching the expected second-order behavior.

At the finest resolution, the measured ratio begins to deviate as the number of time steps becomes very large, providing an example of accumulated floating-point error becoming more significant relative to discretization error.

## Black-Scholes

A change of variables reduces the Black-Scholes PDE to the same 1D heat equation the diffusion solver already handles, so `black_scholes.hpp` reuses that FTCS machinery directly instead of implementing a separate solver.

The result is validated against the closed-form Black-Scholes formula for a European call option:

| Measured price | Closed-form price | Relative error |
|---:|---:|---:|
| 10.4206 | 10.4506 | 0.29% |

---

# Repository Structure

```text
advdiff/
├── include/
│   ├── advection.hpp
│   ├── diffusion.hpp
│   ├── diffusion_exact.hpp
│   ├── black_scholes.hpp
│   ├── black_scholes_exact.hpp
│   ├── grid1d.hpp
│   ├── space_time_log.hpp
│   ├── grid.hpp
│   ├── ppm.hpp
│   └── stats.hpp
│
├── tests/
│   ├── test_advection.cpp
│   ├── test_advection_exact.cpp
│   ├── test_diffusion.cpp
│   ├── test_diffusion_exact.cpp
│   ├── test_black_scholes_exact.cpp
│   ├── diffusion_study.cpp
│   ├── diffusion_convergence.cpp
│   ├── demo_cfl_violation.cpp
│   └── demo_r_violation.cpp
│
└── docs/
    ├── diffusion_study.md
    ├── diffusion_convergence.md
    ├── diffusion_cn_0.1.png
    └── diffusion_cn_0.9.png
```

`grid.hpp`, `ppm.hpp`, and `stats.hpp` are duplicated from numkit rather than shared across a cross-repo dependency, since they're small and stable.

---

# Building

There's no Makefile here yet — build each target directly:

```bash
g++ -std=c++20 -Wall -I include tests/test_advection.cpp -o test_advection.exe
g++ -std=c++20 -Wall -I include tests/test_advection_exact.cpp -o test_advection_exact.exe
g++ -std=c++20 -Wall -I include tests/test_diffusion.cpp -o test_diffusion.exe
g++ -std=c++20 -Wall -I include tests/test_diffusion_exact.cpp -o test_diffusion_exact.exe
g++ -std=c++20 -Wall -I include tests/test_black_scholes_exact.cpp -o test_black_scholes_exact.exe
g++ -std=c++20 -Wall -I include tests/diffusion_study.cpp -o diffusion_study.exe
g++ -std=c++20 -Wall -I include tests/diffusion_convergence.cpp -o diffusion_convergence.exe
g++ -std=c++20 -Wall -I include tests/demo_cfl_violation.cpp -o demo_cfl_violation.exe
g++ -std=c++20 -Wall -I include tests/demo_r_violation.cpp -o demo_r_violation.exe
```

---

# Author

**Jonas Yahdi**

Applied Mathematics / Computational Mathematics & Computer Science
Santa Clara University

GitHub: [jyahdi-byte](https://github.com/jyahdi-byte)
LinkedIn: [Jonas Yahdi](https://www.linkedin.com/in/jonas-yahdi-b00a1a226/)
Email: jyahdi@scu.edu (school) · jyahdi@gmail.com (personal)

## License

MIT
