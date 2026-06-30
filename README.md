## Problem statement

A cart of mass (M) moves horizontally along a frictionless rail. A rigid pendulum of mass (m) and length (l) is attached to the cart through a frictionless pivot.

The pendulum is initially close to its upright position. A horizontal force (u) is applied to the cart.

The goal is to design a **Linear Quadratic Regulator, LQR**, that:

* keeps the pendulum upright,
* returns the cart to the origin,
* minimizes excessive control force,
* and stabilizes the system after a small disturbance.

The system is illustrated conceptually as:

```text
                 θ
                 |
                 |
                 ●  pendulum mass m
                /
               /
        ┌────────────┐
        │    cart    │  → x
        └────────────┘
              → u
```

The upright equilibrium is:

$$
x=0,\qquad \dot{x}=0,\qquad \theta=0,\qquad \dot{\theta}=0
$$

where $\theta=0$ represents the pendulum pointing upward.

---

# 1. Given system parameters

Use the following values:

$$
M=1.0\text{ kg}
$$

$$
m=0.2\text{ kg}
$$

$$
l=0.5\text{ m}
$$

$$
g=9.81\text{ m/s}^2
$$

$$
b=0.1\text{ N s/m}
$$

where:

* (M): cart mass
* (m): pendulum mass
* (l): distance from pivot to pendulum center of mass
* (g): gravitational acceleration
* (b): cart friction coefficient
* (u): horizontal control force

---

# 2. State variables

Define the state vector as:

$$
\mathbf{x}
=
\begin{bmatrix}
x\\
\dot{x}\\
    \theta\\
\dot{\theta}
\end{bmatrix}
$$

where:

* ($x$): cart position
* ($\dot{x}$): cart velocity
* ($\theta$): pendulum angle from upright
* ($\dot{\theta}$): angular velocity

The control input is:

$$
u=F
$$

where $F$ is the horizontal force applied to the cart.

---

# 3. Nonlinear equations of motion

The nonlinear dynamics are:

$$
(M+m)\ddot{x} + b\dot{x} + ml\ddot{\theta}\cos\theta - ml\dot{\theta}^2\sin\theta = u
$$

and

$$
ml^2\ddot{\theta} + ml\ddot{x}\cos\theta - mgl\sin\theta = 0
$$

These equations are nonlinear because they contain terms such as:

$$
\sin\theta,\qquad \cos\theta,\qquad \dot{\theta}^2\sin\theta
$$

LQR is designed for linear systems, so we will linearize these equations around the upright equilibrium.

---

# 4. Linearization assumption

Near the upright position:

$$
    \theta \approx 0
$$

Therefore:

$$
\sin\theta \approx \theta
$$

$$
\cos\theta \approx 1
$$

$$
\dot{\theta}^2\sin\theta \approx 0
$$

Using these approximations, the linearized equations become:

$$
(M+m)\ddot{x} + b\dot{x} + ml\ddot{\theta} = u
$$

$$
ml^2\ddot{\theta} + ml\ddot{x} - mgl\theta = 0
$$

---

# 5. Continuous-time state-space model

## Solve for $\ddot{x}$

Divide Equation (2) by $ml$:

$$
l\ddot{\theta}+\ddot{x}-g\theta=0
$$

Therefore:

$$
l\ddot{\theta}=g\theta-\ddot{x}
$$

and:

$$
\boxed{\ddot{\theta}=\frac{g\theta-\ddot{x}}{l}}
    ag{3}
$$

Now substitute this into Equation (1):

$$
(M+m)\ddot{x}+b\dot{x}+ml\left(\frac{g\theta-\ddot{x}}{l}\right)=u
$$

Cancel $l$:

$$
(M+m)\ddot{x}+b\dot{x}+m(g\theta-\ddot{x})=u
$$

Expand:

$$
(M+m)\ddot{x}+b\dot{x}+mg\theta-m\ddot{x}=u
$$

Combine the acceleration terms:

$$
M\ddot{x}+b\dot{x}+mg\theta=u
$$

Therefore:

$$
\boxed{\ddot{x}=-\frac{b}{M}\dot{x}-\frac{mg}{M}\theta+\frac{1}{M}u}

$$

This equation tells us that cart acceleration is affected by:

* Cart velocity through friction
* Pendulum angle through gravity and coupling
* Applied force $(u)$

---

## Solve for $\ddot{\theta}$

Return to:

$$
\ddot{\theta}=\frac{g\theta-\ddot{x}}{l}
$$

Substitute Equation (4):

$$
\ddot{\theta}=\frac{1}{l}\left[g\theta-\left(-\frac{b}{M}\dot{x}-\frac{mg}{M}\theta+\frac{1}{M}u\right)\right]
$$

Distribute the negative sign:

$$
\ddot{\theta}=\frac{1}{l}\left[g\theta+\frac{b}{M}\dot{x}+\frac{mg}{M}\theta-\frac{1}{M}u\right]
$$

Group the $\theta$ terms:

$$
g\theta+\frac{mg}{M}\theta=\frac{Mg+mg}{M}\theta=\frac{(M+m)g}{M}\theta
$$

Therefore:

$$
\boxed{\ddot{\theta}=\frac{b}{Ml}\dot{x}+\frac{(M+m)g}{Ml}\theta-\frac{1}{Ml}u}
$$

The positive coefficient on $\theta$ is important:

$$
\frac{(M+m)g}{Ml}>0
$$

This means that when the pendulum is slightly tilted away from upright, gravity accelerates it farther away. That is the mathematical indication that the upright equilibrium is unstable.


The system will be represented as:

$$
\dot{\mathbf{x}}=A\mathbf{x}+Bu
$$

$$
\mathbf{y}=C\mathbf{x}+Du
$$

Using the parameter values above, the linearized matrices are:

Now, $\dot{x_1} = x_2$, $\ddot{x_1} = \dot{x_2}$

So, $ \dot{x_2} =-\frac{b}{M} x_2-\frac{mg}{M}x_3+\frac{1}{M}u $



$$
A=
\begin{bmatrix}
0 & 1 & 0 & 0\\
0 & -b/M & -(m * g) / M & 0\\
0 & 0 & 0 & 1\\
0 & b / (M * l) & ((M + m) * g) / (M * l) & 0
\end{bmatrix}=
\begin{bmatrix}
0 & 1 & 0 & 0\\
0 & -0.1 & -1.962 & 0\\
0 & 0 & 0 & 1\\
0 & 0.2 & 23.544 & 0
\end{bmatrix}
$$

and

$$
B=
\begin{bmatrix}
0\\
1\\
0\\
-2
\end{bmatrix}
$$

For full-state observation:

$$
C=
\begin{bmatrix}
1&0&0&0\\
0&1&0&0\\
0&0&1&0\\
0&0&0&1
\end{bmatrix}
$$

and

$$
D=
\begin{bmatrix}
0\\
0\\
0\\
0
\end{bmatrix}
$$

The exact numerical values will be derived rather than accepted blindly.

---

# 6. Control objective

The controller will use state feedback:

$$
u=-K\mathbf{x}
$$

where:

$$
K=
\begin{bmatrix}
k_1 & k_2 & k_3 & k_4
\end{bmatrix}
$$

Therefore:

$$
u=-k_1x-k_2\dot{x}-k_3\theta-k_4\dot{\theta}
$$

Each gain acts on a different physical quantity:

* (k_1): cart-position correction
* (k_2): cart-velocity damping
* (k_3): pendulum-angle correction
* (k_4): angular-velocity damping

The closed-loop system becomes:

$$
\dot{\mathbf{x}}=(A-BK)\mathbf{x}
$$

For stability, the eigenvalues of:

$$
A-BK
$$

must have negative real parts.

---

# 7. LQR cost function

The LQR controller minimizes:

$$
J=\int_0^\infty \left(\mathbf{x}^TQ\mathbf{x}+u^TRu\right)dt
$$

where:

* (Q): penalizes state errors
* (R): penalizes control effort

We will begin with:

$$
Q=
\begin{bmatrix}
10 & 0 & 0 & 0\\
0 & 1 & 0 & 0\\
0 & 0 & 100 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}
$$

and

$$
R=
\begin{bmatrix}
0.1
\end{bmatrix}
$$

This means:

* cart-position error has weight (10),
* cart velocity has weight (1),
* pendulum-angle error has weight (100),
* angular velocity has weight (1),
* control force has weight (0.1).

The pendulum angle is penalized strongly because balance is the most important objective.

---

# 8. Algebraic Riccati equation

LQR computes the gain $K$ by solving the continuous-time algebraic Riccati equation:

$$
A^TP+PA-PBR^{-1}B^TP+Q=0
$$

where $P$ is a positive-semidefinite matrix.

Once $P$ is found:

$$
K=R^{-1}B^TP
$$

The control law becomes:

$$
u=-R^{-1}B^TP\mathbf{x}
$$

We will compute this both using a library and inspect the result mathematically.

---

# 9. Initial condition

Assume the cart starts at the origin but the pendulum is tilted by $10^\circ$:

$$
x(0)=0
$$

$$
\dot{x}(0)=0
$$

$$
    heta(0)=10^\circ
$$

$$
\dot{\theta}(0)=0
$$

Convert the angle to radians:

$$
10^\circ = 10\frac{\pi}{180} \approx 0.1745\text{ rad}
$$

Therefore:

$$
\mathbf{x}(0)
=
\begin{bmatrix}
0\\
0\\
0.1745\\
0
\end{bmatrix}
$$

---

# 10. Performance requirements

The designed controller should satisfy approximately:

$$
|\theta(t)|<0.02\text{ rad}
$$

after about (2) seconds,

$$
|x(t)|<0.05\text{ m}
$$

after about (5) seconds, and

$$
|u(t)|\leq 20\text{ N}
$$

during normal operation.

We will examine whether the chosen (Q) and (R) satisfy these goals.

---

# 11. Coding objectives

We will implement the problem in Python using:

```python
numpy
scipy
matplotlib
```

Optionally:

```python
python-control
```

The final code will:

1. Define the physical parameters.
2. Derive the (A) and (B) matrices.
3. Check the open-loop eigenvalues.
4. Check controllability.
5. Define (Q) and (R).
6. Solve the Riccati equation.
7. Compute (K).
8. Compute closed-loop eigenvalues.
9. Simulate the linear closed-loop system.
10. Plot all states.
11. Plot the control force.
12. Compare different (Q) and (R) values.
13. Add actuator saturation.
14. Simulate the nonlinear cart-pole equations.
15. Compare linear and nonlinear performance.

---

# 12. Base Python structure

This is the overall structure we will eventually complete:

```python
import numpy as np
import matplotlib.pyplot as plt

from scipy.integrate import solve_ivp
from scipy.linalg import solve_continuous_are


# Physical parameters
M = 1.0
m = 0.2
l = 0.5
g = 9.81
b = 0.1


# State-space matrices
A = np.array([
    [0.0, 1.0, 0.0, 0.0],
    [0.0, -0.1, -1.962, 0.0],
    [0.0, 0.0, 0.0, 1.0],
    [0.0, 0.2, 23.544, 0.0]
])

B = np.array([
    [0.0],
    [1.0],
    [0.0],
    [-2.0]
])


# LQR weights
Q = np.diag([10.0, 1.0, 100.0, 1.0])
R = np.array([[0.1]])


# Solve Riccati equation
P = solve_continuous_are(A, B, Q, R)


# Compute feedback gain
K = np.linalg.inv(R) @ B.T @ P


# Closed-loop system
A_cl = A - B @ K


# Initial condition
x0 = np.array([
    0.0,
    0.0,
    np.deg2rad(10.0),
    0.0
])


def closed_loop_dynamics(t, state):
    control = -K @ state
    state_dot = A @ state + B.flatten() * control.item()
    return state_dot


time_span = (0.0, 10.0)
time_points = np.linspace(0.0, 10.0, 1000)

solution = solve_ivp(
    closed_loop_dynamics,
    time_span,
    x0,
    t_eval=time_points
)
```

---

# Step-by-step solution sequence

We will solve the problem in this order:

1. Understand the physical system and sign conventions.
2. Derive the nonlinear equations.
3. Find the equilibrium point.
4. Linearize the nonlinear dynamics.
5. Construct the state-space matrices.
6. Analyze open-loop stability.
7. Build the controllability matrix.
8. Select (Q) and (R).
9. Solve the Riccati equation.
10. Compute the LQR gain (K).
11. Analyze closed-loop eigenvalues.
12. Simulate the linear system.
13. Interpret every plot.
14. Tune (Q) and (R).
15. Add actuator saturation.
16. Simulate the full nonlinear system.
17. Discuss limitations and humanoid applications.
