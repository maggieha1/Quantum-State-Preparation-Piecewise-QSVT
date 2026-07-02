# Quantum State Preparation via Piecewise QSVT: Biological Anomaly Classifier

## Overview
This project implements a Quantum Machine Learning (QML) data-loading pipeline using Quantum Singular Value Transformation (QSVT). It addresses the fundamental data-loading bottleneck by avoiding exponentially deep amplitude encoding circuits. 

We simulate a Biological Anomaly Classifier that evaluates the energy levels (singular values) of a $2 \times 2$ biological transition matrix. A Step Function is applied as a quantum filter:
* **Healthy** (Output 0): Singular value $< 0.5$.
* **Diseased** (Output 1): Singular value $\ge 0.5$.

## Architecture & Qubit Setup
This piecewise classification utilizes a 3-qubit architecture:
* **System Qubit (1):** Holds the raw biological data.
* **Auxiliary Qubit (1):** Provides the mathematical slack space to block-encode the non-unitary biological matrix $A$ into a strictly unitary matrix $U$.
* **Routing Qubit (1):** Acts as a quantum IF statement to route the data based on the Step Function threshold.

## The Analysis: Polynomial Degree vs. Circuit Depth
Applying a sharp, non-linear function (like a Step Function) directly to a quantum circuit is physically impossible. It must be classically approximated using a polynomial first. This introduces the most critical engineering trade-off in QSVT.

| Metric | Low Degree Polynomial | High Degree Polynomial |
| :--- | :--- | :--- |
| **Circuit Depth** | Shallow (Less noise) | Deep (High decoherence risk) |
| **Matrix Queries** | Few operations of $U$ | Many operations of $U$ |
| **Accuracy** | Poor (Lazy slope) | High (Sharp threshold) |
| **Gibbs Phenomenon** | Minimal | Severe ringing near the jump |

In QSVT, the degree of the polynomial ($d$) is exactly equal to the number of times the quantum circuit must query the block-encoded matrix $U$. A high-degree polynomial accurately models the vertical jump of the Step function, but it requires a deeply nested circuit (e.g., $d$ alternating gate sequences). On near-term hardware, this depth introduces fatal noise. 

Furthermore, approximating a discontinuous step with continuous polynomials inherently introduces the **Gibbs Phenomenon** oscillatory ringing or squiggles immediately before and after the jump.

By utilizing a **Piecewise** approach, this project partitions the singular value domain, applying simpler, lower-degree polynomials to specific segments. This maintains mathematical fidelity while keeping the overall circuit depth shallow enough to survive physical hardware constraints.

## Installation & Execution
1. Clone this repository.
2. Install dependencies: `pip install -r requirements.txt`
3. Open and run all cells in `QSVT_Biological_Classifier.ipynb`.
