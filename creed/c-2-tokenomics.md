# 📄 C-2 Tokenomics

|                   |                                                                   |
| ----------------- | ----------------------------------------------------------------- |
| **Authors**       | Xavier Lau ([@AurevoirXavier](https://github.com/AurevoirXavier)) |
| **Created**       | 2024-05-04                                                        |
| **Discussion**    | None                                                              |
| **Status**        | Draft / Proposed / Accepted ✓ / Implemented                       |
| **Requires**      | None                                                              |
| **Replaces**      | None                                                              |
| **Superseded By** | None                                                              |

## Abstract

This proposal outlines a dynamic inflation model for two native tokens in the t0y ecosystem: _YOKE_ and _ZEST_. _YOKE_ serves as the primary token for transaction fees and regular operations, while _ZEST_ is utilized for governance and value storage. The initial supply is set at **1 billion** for _YOKE_ and **1 million** for _ZEST_, with both tokens starting with an inflation rate of `10%`. The inflation rates of both tokens are adjusted dynamically based on the destruction rate of _YOKE_, promoting a balanced and sustainable economic environment.

## Rationale

The dual-token system with dynamic inflation is designed to incentivize user engagement and reward contributions to the ecosystem. By adjusting the issuance rates of _YOKE_ based on its destruction rate and linking the minting of _ZEST_ to _YOKE_'s destruction metrics, we aim to encourage optimal usage and governance participation. This approach ensures that tokens are distributed to users who actively contribute to the growth and stability of the platform.

## Specification

### **Token Dynamics**

* _YOKE_ is the primary currency for transaction fees and standard operations within the t0y ecosystem. The initial supply is **1 billion** tokens.
* _ZEST_ is used primarily for governance and acts as a store of value. The initial supply is **1 million** tokens.

### **Inflation and Minting Mechanisms**

* _YOKE_ inflation rate is dynamically adjusted based on its destruction rate (`destroyed / staked` detailed in C-3):
  * When the destruction rate decreases from `X%` to `0%`, the inflation rate gradually increases to `20%` to encourage more issuance and stimulate destruction.
  * Between `X%` and `Y%`, the inflation rate remains stable at `10%`.
  * When the destruction rate increases from `Y%` to `100%`, the inflation rate gradually decreases to `2.5%` to preserve value and prevent dilution.
  * The adjustment curve for _YOKE_ inflation is a piecewise function using the Hyperbolic Tangent function.
  * Currently, `X` is set at `30%` and `Y` at `70%`.
* _ZEST_ inflation rate is tied to the destruction rate of _YOKE_:
  * At a `50%` destruction rate, _ZEST_ has an inflation rate of `10%`.
  * As _YOKE_'s destruction rate increases from `50%` to `100%`, _ZEST_'s inflation rate increases from `10%` to `20%`.
  * As _YOKE_'s destruction rate decreases from `50%` to `0%`, _ZEST_'s inflation rate decreases from `10%` to `0%`.
  * The relationship between _YOKE_'s destruction rate and _ZEST_'s inflation rate is modeled using a Sigmoid function.

### **Token Usage Adjustments**

* At any time, if the consumption rate of _YOKE_ falls below `50%`, _ZEST_ can be used to burn _YOKE_. Conversely, if the rate is above `50%`, _ZEST_ can be used to issue additional _YOKE_.

## **Additional**

### **Mathematical Derivations and Function Graphs**

The approximations involved in the following functions will retain two decimal places, and ultimately, percentages will be directly rounded to the nearest whole number in the engineering application.

#### _**YOKE**_** Inflation Adjustment Function**

The mathematical function used to adjust the inflation rate of _YOKE_ based on its destruction rate is detailed here. This includes the use of the Hyperbolic Tangent function to model the inflation curve dynamically.

*   Problem Statements

    Find parameters $$A_1$$, $$B_1$$, and $$C_1$$ such that the function satisfies $$I(0) = 0.2$$ and $$I(0.1) = 0.3$$, while maintaining a smoothing factor of $$s = 0.1$$.\
    Find parameters $$A_2$$, $$B_2$$, and $$C_2$$ such that the function satisfies $$I(0.1) = 0.7$$ and $$I(1) = 0.025$$, while maintaining a smoothing factor of $$s = 0.1$$.
*   Function Form

    $$
    I(x) = A + B \tanh\left(\frac{x - C}{0.1}\right)
    $$
*   Conditions

    $$
    \begin{cases} I(0.3) = 0.1\\ I(0) = 0.2 \end{cases}\\
    \text{and}\\
    \begin{cases} I(0.7) = 0.1\\ I(1) = 0.025 \end{cases}
    $$
*   Solving the Equations&#x20;

    Assuming $$C_1 = 0.15$$, $$C_2 = 0.85$$ as midpoints, we calculate:

    $$
    \begin{cases} \tanh\left(\frac{-0.15}{0.1}\right) = \tanh(-1.5) \approx -0.905\\ \tanh\left(\frac{0.15}{0.1}\right) = \tanh(1.5) \approx 0.905 \end{cases}
    $$

    Substituting these into the equations:

    $$
    \begin{cases} A - 0.905B = 0.2\\ A + 0.905B = 0.1 \end{cases}\\
    \text{and}\\
    \begin{cases} A - 0.905B = 0.1\\ A + 0.905B = 0.025 \end{cases}
    $$
*   Linear Equation Solutions

    Addition:

    $$
    A - 0.905B + A + 0.905B = 0.2 + 0.1\\
    2A = 0.3\\
    A = 0.15
    $$

    $$
    A - 0.905B + A + 0.905B = 0.2 + 0.1\\
    2A = 0.125\\
    A = 0.0625
    $$

    Subtraction:

    $$
    A + 0.905B - (A - 0.905B) = 0.1 - 0.2\\
    1.81B = -0.1\\
    B = -\frac{0.1}{1.81} \approx -0.0552
    $$

    $$
    A + 0.905B - (A - 0.905B) = 0.1 - 0.2\\
    1.81B = -0.075\\
    B = -\frac{0.1}{1.81} \approx -0.0414
    $$
*   Final Functions

    Thus, the functions are:

    $$
    I(x) = 0.15 - 0.0552 \tanh\left(\frac{x - 0.15}{0.1}\right)\\
    I(x) = 0.0625 - 0.0414 \tanh\left(\frac{x - 0.85}{0.1}\right)
    $$

    $$
    I(x) = \begin{cases} 0.15 - 0.0552 \tanh\left(\frac{x - 0.15}{0.1}\right) & \text{for } 0 \leq x \leq 0.3, \\ 0.1 & \text{for } 0.3 < x < 0.7, \\ 0.0625 - 0.0414 \tanh\left(\frac{x - 0.85}{0.1}\right) & \text{for } 0.7 \leq x \leq 1 \end{cases}
    $$

    These functions should meet the conditions at $$x = 0$$, $$x = 0.3$$, $$x = 0.7$$ and $$x = 1$$ with a smoothing factor $$s = 0.1$$.

{% embed url="https://www.desmos.com/calculator/nuuzuaqtjb?embed" %}

#### _**ZEST**_** Inflation Adjustment Function**

The relationship between the destruction rate of _YOKE_ and the inflation rate of _ZEST_ is modeled using a Sigmoid function.

*   Problem Statement

    Find parameters $$k$$ such that the function satisfies $$I(0) = 0$$ and $$I(1) = 1$$.
*   Function Form

    $$
    y = \frac{1}{1 + e^{-k(x - x_0)}}
    $$
*   Conditions

    $$
    \begin{cases} I(0) = 0\\ I(1) = 1 \end{cases}
    $$
*   Solving the Equations

    Assuming $$x_0 = 0.5$$ as a midpoint, we calculate:

    $$
    0 = \frac{1}{1 + e^{-k(0 - 0.5)}}\\ 1 = \frac{1}{1 + e^{-k(1 - 0.5)}}\\ k \approx 10.7
    $$
*   Final Function

    Thus, the function is:

    $$
    I(x) = \frac{1}{1 + e^{-10.7(x - 0.5)}}
    $$

    This function should meet the conditions at $$x = 0$$ and $$x = 1$$.

{% embed url="https://www.desmos.com/calculator/u85raxlyzc?embed" %}
{% embed url="https://www.desmos.com/calculator/nxtydl7ouj?embed" %}

## **Copyright**

Copyright and related rights waived via [CC0](https://creativecommons.org/public-domain/cc0/).
