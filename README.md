# quantum-monte-carlo-wiser2025
## Project Name: Quantum Monte Carlo Distribution Generator
## Team Name: EL-Ikhlas
## Participant: Douaa Elikhlas Hennane 

## 📌 Implementation Overview

This project implements a **Quantum Galton Board (QGB)** simulator inspired by  
*"Universal Statistical Simulator"* by Mark Carney and Ben Varcoe (2022).  
The QGB simulates the probabilistic behavior of a classical Galton board (bean machine)  
using **quantum gates** to achieve exponential parallelism.

Instead of tracking each ball trajectory classically, the quantum circuit creates a  
**superposition of all possible paths**, enabling the simulation of `2^n` trajectories  
with `O(n²)` resources.


## ⚙️ Implementation Details

This project implements a **Quantum Galton Board (QGB)** simulator using **Qiskit**.  
The implementation is based on the method described in *Universal Statistical Simulator* by Carney & Varcoe (2022), with adaptations for simulation in Python/Colab.

---

### 1. Quantum Peg Construction
At the heart of the QGB is the **quantum peg** — a reusable quantum circuit module that replicates the action of a ball hitting a peg in a classical Galton board.

- **Control Qubit (`q0`)**  
  Initialized in a superposition with a **Hadamard (`H`) gate**, acting like a quantum coin toss that decides whether the ball goes left or right.

- **Ball Qubit (middle position)**  
  Initialized with an **`X` gate** to represent the ball starting at a specific peg position.

- **Movement through the peg**  
  - **Controlled-SWAP (`cswap`) gates** move the ball left or right depending on the control qubit’s state.  
  - A **CNOT** operation updates the control qubit’s state for correct propagation.

This module can be connected in series to form multi-row Galton boards.

---

### 2. Scaling to Multi-Level QGB
A **multi-level QGB** is constructed by cascading quantum pegs:

1. **Reset and Reuse**  
   The control qubit is reset (`reset q[0]`) after each peg so it can be reused for the next level, reducing the total qubit requirement.

2. **Connecting Pegs**  
   The output position from one peg becomes the input position for the next peg.

3. **Gate Count Efficiency**  
   For each peg:
   - `1 × Hadamard`
   - `1 × X` (ball initialization)
   - `2 × Controlled-SWAP`
   - `1 × CNOT`
   - Plus **reset** and **measurement** operations.

This design achieves **O(n²)** complexity for an **n-level QGB**.

---

### 3. Simulation and Testing
Two main simulations were implemented:

#### **A. Single-Peg Test (4-Qubit Circuit)**
- 1 control qubit + 3 position qubits.
- Verified that:
  - The ball is put into a superposition of positions.
  - Measurement results match expected one-hot encodings.

#### **B. Full Multi-Level QGB**
- **4-level QGB** using:
  - 10 qubits (1 control + 9 position qubits)
  - 4 cascaded peg modules.
- Simulated with **Qiskit Aer** `qasm_simulator` (20,000 shots).
- Produced a **quantum-generated distribution** similar to the classical bell curve.

---

### 4. Post-Processing
The raw quantum output is **one-hot encoded** — only one bit in the position register is `1` at a time.

Processing steps:
1. Reverse the measured bitstring (due to Qiskit’s ordering).
2. Ignore the control qubit.
3. Find the index of the `1` bit → **bin index**.
4. Count frequencies for each bin.
5. Plot histogram with **Matplotlib**.

---

### 5. Features Implemented
- ✅ **Unbiased QGB** — produces a symmetric distribution.
- ✅ **Efficient qubit reuse** with resets.
- ✅ **Scalable design** — supports multiple levels.
- 🔜 **Biased QGB** — replacing `H` with `Rx(θ)` to skew probabilities.
- 🔜 **Fine-grained bias control** — per-peg bias tuning.

---

### 6. Example Output
Example from a **4-level simulation** (20,000 shots):

