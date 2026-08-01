# Qubit Characterisation: T1/T2 Measurement Pipeline
Track: Pulse-Level Control · Open Quantum Systems · Decoherence · Curve Fitting

## Overview

A qubit is not an ideal two-level system — it is a physical object embedded
in an environment that continuously disturbs its state (decoherence). This
project builds a full simulation pipeline to extract a qubit's two key
coherence timescales — **T1** (energy relaxation) and **T2** (dephasing) —
using the same measurement protocols used on real superconducting hardware
(e.g. IBM Quantum devices), and to identify which physical noise channel is
dominant.

The pipeline moves from first-principles discrete noise modeling, through a
continuous-time Lindblad master-equation simulation, to full measurement
protocols (inversion recovery, Ramsey, Hahn echo) with nonlinear
least-squares curve fitting — mirroring how real quantum hardware engineers
characterize a new qubit.

---

## Project Structure

```
├── step1_kraus_operators.py         # Discrete Kraus-operator simulation (T1 & T2 decay)
├── step2_lindblad_model.py          # Lindblad master-equation model (Qiskit Dynamics)
├── step2_lindblad_steady_state.png
├── step3_t1_measurement.py          # T1 inversion-recovery protocol + curve fit
├── step3_t1_inversion_recovery.png
├── step4_t2_ramsey.py               # T2* Ramsey protocol (with quasi-static noise)
├── step4_t2_ramsey.png              
├── step5_hahn_echo.py               # Hahn-echo protocol (noise refocusing)
├── step5_hahn_echo.png
├── step5_results.npz
├── step6_parameter_sweep.py         # Γφ sweep: T2* & T2_echo vs. dephasing rat

└── README.md
```

Run the scripts in order (1 → 6); Steps 5 and 6 `.

---

## Pipeline Summary

| Step | What it does | Key result |
|---|---|---|
| **1. Kraus operators** | Discrete-time simulation of amplitude & phase damping | Matches analytical decay to ~1e-14 |
| **2. Lindblad model** | Continuous-time transmon model (Qiskit Dynamics), ωq/2π = 5 GHz | Reaches 99.3% ground-state population after 5×T1 |
| **3. T1 measurement** | Inversion recovery, 35 points, `scipy.curve_fit` | **T1 = 100.15 ± 1.66 µs** (true: 100 µs) |
| **4. T2 Ramsey** | π/2–τ–π/2 sequence, 1 MHz detuning, ensemble-averaged over quasi-static noise | **T2\* = 17.07 ± 0.99 µs** |
| **5. Hahn echo** | π/2–τ/2–π–τ/2–π/2 sequence | **T2_echo = 80.51 ± 2.06 µs** (≈4.7× improvement over T2*) |
| **6. Parameter sweep** | Vary Γφ = 1/Tφ over one order of magnitude | T2_echo tracks 1/T2 = 1/(2T1) + 1/Tφ exactly; T2* stays floor-limited by quasi-static noise |

---

## Key Physical Insight

Simple **Markovian** (memoryless) Lindblad noise cannot be refocused by a
Hahn echo — there is no "memory" for the echo pulse to undo. To reproduce
realistic behavior, a **quasi-static (shot-to-shot) detuning noise** term was
added on top of the intrinsic Lindblad channels, representing slow classical
noise sources such as charge/flux drift or two-level-system (TLS) defects.

The simulation shows:
- **T2_echo ≈ intrinsic Markovian T2** (noise refocused away)
- **T2\* ≪ T2_echo** (quasi-static noise dominates the raw Ramsey signal)

This ~4.7× gap between T2* and T2_echo is the diagnostic signature used in
`D6_hardware_memo.md` to identify **quasi-static charge/flux noise or TLS
defects** — not simple Purcell (T1-limited) decay — as the dominant
coherence-limiting mechanism.

---

## Requirements

```bash
pip install numpy scipy matplotlib qiskit qiskit-dynamics --break-system-packages
```

---

## Deliverables Checklist

- [x] D1 — Simulation setup (Kraus + Lindblad, steady-state verified)
- [x] D2 — T1 inversion recovery (decay curve + fit + 95% CI)
- [x] D3 — T2 Ramsey (oscillating decay + fit + 95% CI)
- [x] D4 — Hahn echo (T2_echo vs. T2* comparison + explanation)
- [x] D5 — Parameter sweep (T2*, T2_echo vs. Γφ, theory overlay)


---

## References

1. Krantz, P. et al. (2019). *A Quantum Engineer's Guide to Superconducting Qubits.* Applied Physics Reviews 6, 021318.
2. Oliver, W.D., Welander, P.B. (2013). *Materials in superconducting quantum bits.* MRS Bulletin 38, 816.
3. Nielsen, M.A., Chuang, I.L. *Quantum Computation and Quantum Information*, Ch. 8.
4. Qiskit Dynamics documentation — [qiskit-community.github.io/qiskit-dynamics](https://qiskit-community.github.io/qiskit-dynamics/)

---

## Author

**Saksham** — Integrated M.Tech, Applied Geophysics, IIT (ISM) Dhanbad
GitHub: [saksham966](https://github.com/saksham966)
