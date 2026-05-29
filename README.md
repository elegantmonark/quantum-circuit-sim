# QSIM: Quantum Circuit Simulator

<div align="center">

**A browser-based quantum circuit simulator with visual circuit building, state-vector simulation, noise channels, Bloch sphere analysis, and algorithm templates.**

[Live Demo](https://quantum-circuit-sim.onrender.com) | [Run Locally](#run-locally) | [Technical Approach](#technical-approach) | [API](#api)

</div>

![QSIM Screenshot](ss/qsimv1.png)

## At A Glance

| Area | Details |
| --- | --- |
| Interface | Drag-and-drop browser circuit builder |
| Backend | FastAPI simulation API with NumPy state-vector logic |
| Scale | Up to 10 qubits, 23 gates, multi-shot measurement |
| Analysis | Bloch vectors, probability distributions, polar amplitudes, entropy indicator |
| Algorithms | Bell, GHZ, QFT, inverse QFT, Grover, Deutsch-Jozsa, teleportation, superdense coding, Shor N=15 |
| Deployment | Live Render deployment plus local FastAPI server |

## Overview

QSIM is an interactive quantum circuit simulator built around a drag-and-drop circuit builder and a Python simulation backend. It lets users construct circuits visually, simulate the resulting quantum state, inspect measurement probabilities, view per-qubit Bloch vectors, and test common quantum algorithm templates.

The project is designed as a portfolio-grade quantum computing tool with a research edge: the interface is approachable, but the backend still exposes real state-vector mechanics, tensor contraction, shot sampling, entanglement entropy, and basic noise modelling.

## Why This Matters

Quantum circuits are often taught as diagrams, but the behaviour underneath is high-dimensional, probabilistic, and difficult to inspect directly. QSIM turns circuits into an interactive system where the user can see how gates change amplitudes, phases, probabilities, Bloch vectors, and entanglement.

This also connects to a larger direction: building software that makes quantum systems easier to control, debug, visualise, and reason about.

## Research Edge

QSIM is not just a circuit drawing tool. The useful part is the inspection layer around the simulation: how amplitudes move, how phases appear, how measurements sample from probability distributions, and how individual-qubit Bloch vectors behave when the full state may be entangled.

This makes it a useful base for future work in quantum workflow tooling, noisy-circuit comparison, and lower-level QPU-runtime experiments.

## What It Simulates

- State-vector evolution for up to 10 qubits
- Single-qubit, two-qubit, and three-qubit quantum gates
- Multi-shot measurement using the Born rule
- Reduced-density-matrix Bloch vectors for individual qubits
- Von Neumann entropy as an entanglement indicator
- Depolarizing, dephasing, and amplitude damping noise channels
- Prebuilt templates for well-known quantum circuits and algorithms

## Features

- **Visual circuit builder**: drag and drop gates onto qubit wires
- **23 quantum gates**: includes Pauli gates, rotations, CNOT, SWAP, Toffoli, Fredkin, controlled rotations, and U3
- **Algorithm templates**: Bell, GHZ, QFT, inverse QFT, Grover, Deutsch-Jozsa, teleportation, superdense coding, and Shor's algorithm for N=15
- **State analysis**: state vector, probability distribution, polar amplitude diagram, and shot histogram
- **Bloch sphere visualisation**: interactive per-qubit Bloch spheres with expanded views
- **Noise controls**: configurable depolarizing, dephasing, and amplitude damping channels
- **API-backed simulation**: FastAPI backend with JSON endpoints for simulation, gates, templates, and health checks

## Circuit Templates

| Template | Qubits | Description |
| --- | ---: | --- |
| Bell State | 2 | Maximally entangled Bell pair |
| GHZ State | 3 | Three-qubit Greenberger-Horne-Zeilinger state |
| Quantum Teleportation | 3 | Transfers the state of one qubit using a Bell pair and classical correction logic |
| QFT | 4 | Quantum Fourier Transform |
| Inverse QFT | 4 | Inverse transform used in phase-estimation-style workflows |
| Grover's Search | 2 | Finds the marked state `|11>` in one iteration |
| Deutsch-Jozsa | 3 | Demonstrates balanced oracle detection |
| Superdense Coding | 2 | Encodes two classical bits using one transmitted qubit and prior entanglement |
| Shor's Algorithm, N=15 | 7 | Demonstrates period finding for factoring 15 into 3 and 5 |

## Gate Reference

| Category | Gates |
| --- | --- |
| Single-qubit | H, X, Y, Z, S, S-dagger, T, T-dagger, Rx, Ry, Rz, P, U3 |
| Two-qubit | CNOT, SWAP, CZ, CP, CRx, CRy, CRz |
| Three-qubit | Toffoli, Fredkin, CCZ |

## Technical Approach

### State-Vector Engine

An `n`-qubit system is represented as a complex vector in a `2^n`-dimensional Hilbert space, initialized to `|0...0>`. Gates update this vector directly, which keeps the simulator intuitive and fast for small circuits.

### Tensor Contraction

Gate application is implemented with NumPy tensor contraction via `np.einsum`. This allows one-qubit, two-qubit, and three-qubit gates to be applied without manually expanding every gate into a full `2^n x 2^n` matrix.

```python
gate_tensor = gate_matrix.reshape([2] * k + [2] * k)
state_tensor = state.reshape([2] * num_qubits)
new_state = np.einsum(
    gate_tensor,
    gate_indices,
    state_tensor,
    state_indices,
    result_indices,
)
```

### Measurement

Measurement probabilities are calculated using the Born rule:

```text
P(|x>) = |<x|psi>|^2
```

For multi-shot experiments, the simulator samples from the probability distribution and returns shot counts for histogram visualisation.

### Bloch Vectors

Each qubit's Bloch vector is extracted from its reduced density matrix:

```text
x = Tr(rho * sigma_x)
y = Tr(rho * sigma_y)
z = Tr(rho * sigma_z)
```

This makes individual-qubit behaviour easier to inspect even when the full system state is entangled.

### Entanglement

QSIM estimates entanglement using Von Neumann entropy:

```text
S(rho) = -Tr(rho * log2(rho))
```

An entropy value near 0 indicates a separable qubit state, while higher entropy indicates stronger entanglement with the rest of the system.

### Noise Channels

Noise is applied after gates using the Kraus operator formalism. The simulator supports:

- **Depolarizing noise**: random Pauli error with probability `p`
- **Dephasing noise**: random phase error with probability `p`
- **Amplitude damping**: relaxation toward `|0>` with probability `gamma`

## Limitations

- QSIM is a state-vector simulator, so memory usage grows exponentially with qubit count.
- The 10-qubit cap is intentional for browser responsiveness and local usability.
- The noise model is educational and experimental; it is not a full hardware-calibrated backend.
- The Shor's algorithm template demonstrates `N=15`; it is not a general-purpose factoring engine.
- The simulator does not currently model pulse-level control, device topology, queueing, or hardware-native compilation.

## Roadmap

- Add more screenshots and short demo clips to document key workflows
- Add export/import for circuit JSON
- Add named presets for noise experiments
- Improve template explanations with expected results
- Add fidelity comparison between ideal and noisy circuit runs
- Add circuit-depth and gate-count diagnostics
- Add a simple hardware-topology constraint mode
- Explore a lower-level quantum instruction format for future QPU-runtime experiments

## Current Focus

QSIM is the public quantum-computing interface repo. The next useful step is to connect the simulator more closely to hardware-aware workflows: circuit depth diagnostics, topology constraints, noisy-versus-ideal fidelity comparison, and eventually a lower-level instruction representation that can feed future QPU-runtime experiments.

## Run Locally

```bash
git clone https://github.com/elegantmonark/quantum-circuit-sim.git
cd quantum-circuit-sim
pip install -r requirements.txt
uvicorn main:app --reload
```

Open [http://localhost:8000](http://localhost:8000) in your browser.

Requires Python 3.10+. If using a Python build without NumPy wheels, use a stable Python release such as Python 3.12.

## API

### `POST /simulate`

```json
{
  "num_qubits": 2,
  "circuit": [
    [{"gate": "H", "target": 0, "params": {}}],
    [{"gate": "CNOT", "target": 1, "control": 0, "params": {}}]
  ],
  "shots": 1024,
  "noise": {
    "depolarizing": 0.05,
    "dephasing": 0.02,
    "amplitude_damping": 0.0
  }
}
```

Returns state vector data, probabilities, Bloch vectors, entanglement entropy, noise status, and optional shot counts.

### Other Endpoints

| Endpoint | Description |
| --- | --- |
| `GET /gates` | Full gate catalogue with matrices and descriptions |
| `GET /templates` | List available circuit templates |
| `GET /templates/{name}` | Return a specific template circuit |
| `GET /health` | Health check endpoint |

## Project Structure

```text
quantum-circuit-sim/
|-- main.py              # FastAPI app, routes, request models
|-- simulator.py         # State-vector engine, measurement, Bloch, entropy
|-- gates.py             # Gate matrix definitions
|-- noise.py             # Kraus-operator noise channels
|-- circuit_templates.py # Prebuilt quantum circuits
|-- requirements.txt
|-- Procfile             # Render deployment entrypoint
|-- ss/
|   `-- qsimv1.png       # README screenshot
|-- static/
|   `-- style.css        # Frontend styling
`-- templates/
    `-- index.html       # Browser UI
```

## Author

**Trishan Biswas**

## License

[MIT License](LICENSE)
