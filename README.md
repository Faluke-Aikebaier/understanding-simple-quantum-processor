# Superconducting Qubit Simulations

Jupyter notebooks for simulating coherence, Lindblad dynamics, and improvement strategies in superconducting transmon qubit systems — from a single qubit to a four-qubit square lattice.

These notebooks are the computational companion to a textbook on superconducting quantum computing. Every parameter is tagged with its physical origin (`[MATERIAL]`, `[GEOMETRY]`, `[OPERATING]`, `[MEASURED]`), and every result is connected back to the underlying circuit physics.

---

## Repository Structure

```
superconducting-qubits/
│
├── README.md                          ← this file
├── superconducting_qubits_intro.md    ← physics introduction and workflow guide
│
├── 01_single_qubit_T1_T2.ipynb        ← single transmon: T1, T2, sensitivity
├── 02_two_qubit_lindblad.ipynb        ← two qubits: Lindblad, iSWAP, Bell state
├── 03_four_qubit_lindblad.ipynb       ← four-qubit square lattice: GHZ, gate fidelity
└── 04_coherence_improvement.ipynb     ← improvement levers, regime analysis, conflicts
```

---

## Notebooks at a Glance

### 01 — Single Qubit: $T_1$ and $T_2$ Workflow

**What it does:** Builds the complete single-qubit coherence model from junction parameters outward.

**Parameter chain:**
$$R_N \xrightarrow{\text{AB}} E_J \xrightarrow{} \omega_{01},\alpha \xrightarrow{} g,\chi \xrightarrow{} \Gamma_P,\Gamma_\text{TLS},\Gamma_\text{qp} \xrightarrow{} T_1 \xrightarrow{} T_2$$

**Key results (baseline device):**
- $f_{01} = 4.977$ GHz, $\alpha/(2\pi) = -258$ MHz, $E_J/E_C = 51$
- $T_1 = 31\,\mu$s: Purcell 44%, TLS 29%, QP 27%
- $T_2 = 48\,\mu$s (Mixed regime: $T_2/2T_1 = 0.77$)

**Sections:**
1. Physical constants
2. Device parameters (tagged by origin)
3. Ambegaokar–Baratoff: junction → $E_J$, $E_C$
4. Transmon spectrum: $\omega_{01}$, $\alpha$
5. Readout resonator: $g$, $\chi$, dispersive limit check
6. $T_1$ channel decomposition: Purcell, TLS, quasiparticles
7. Dephasing: 1/f flux noise, photon shot noise
8. $T_2$ and regime diagnosis
9. Summary dashboard (6 panels)
10. Full numerical summary
11. Sensitivity analysis: $d\ln T/d\ln p$ for every parameter

---

### 02 — Two Qubits: Lindblad Equation and iSWAP Gate

**What it does:** Adds a second qubit and a tunable SQUID coupler. Derives the effective two-qubit Hamiltonian via Schrieffer–Wolff, finds the OFF point, and solves the Lindblad equation numerically with QuTiP.

**Why Lindblad must be solved numerically:** The $4\times4$ density matrix has 15 coupled equations. The ZZ coupling $\zeta_\text{eff}\sigma_z^{(1)}\sigma_z^{(2)}$ mixes coherences — there is no closed-form solution.

**Why the rotating frame:** Qubit frequencies ~5 GHz mean oscillation periods ~190 ps, but simulation times ~100 μs — a ratio of $6\times10^5$. The lab frame integrator fails. The rotating frame removes fast oscillations; only slow ZZ terms remain in $H$.

**Key results:**
- $T_1^{(1)} = 23.4\,\mu$s, $T_2^{(1)} = 37.9\,\mu$s
- $T_1^{(2)} = 33.4\,\mu$s, $T_2^{(2)} = 51.3\,\mu$s (Q2 longer: lower frequency)
- Bell state $T_2^\text{ent} = 11.3\,\mu$s $< \min(T_2^{(i)})$ — both qubits must stay coherent simultaneously
- iSWAP gate: 15 ns, fidelity 99.96%

**Sections:**
1–2. Constants and device parameters
3. $E_J$, $E_C$, frequencies
4. Coupling strengths $g_1$, $g_2$, $g_{12}$
5. Schrieffer–Wolff: $J_\text{eff}(\Phi)$, $\zeta_\text{eff}(\Phi)$
6. Coupling landscape visualisation
7. Two-qubit coherence rates and ZZ-induced dephasing
8. Lindblad in rotating frame: Hamiltonian + jump operators
9–11. QuTiP solutions: $T_1$, $T_2$, Bell state coherence
12. iSWAP gate fidelity
13. Summary dashboard
14. Sensitivity analysis

---

### 03 — Four Qubits: Square Lattice, GHZ State, Spectator Errors

**What it does:** Extends to a square-lattice 4-qubit system with 4 SQUID couplers. Demonstrates the key scaling physics: individual $T_1$, $T_2$ are unchanged at idle, but system-level coherence shortens with every added qubit.

```
Q1 ── C12 ── Q2
|              |
C13           C24
|              |
Q3 ── C34 ── Q4
```

**New effects compared to two qubits:**

| Feature | 2 qubits | 4 qubits |
|---|---|---|
| Hilbert space | $4$ | $16$ |
| Density matrix | $4\times4$, 15 eqns | $16\times16$, 255 eqns |
| Couplers | 1 | 4 |
| ZZ pairs | 1 | 4 nearest + 2 next-nearest |
| New error channels | — | Spectator errors, frequency crowding |

**Key results:**
- Individual $T_2$: Q1=25, Q2=38, Q3=51, Q4=64 μs (shorter for higher-frequency qubits)
- GHZ state $T_2^\text{GHZ} = 7.3\,\mu$s — **4.5× shorter than average single-qubit $T_2$**
- iSWAP (Q1–Q2) isolated: fidelity 99.96%
- iSWAP (Q1–Q2) with spectators Q3, Q4: fidelity 99.41% — spectator errors cost 55×10⁻⁴
- Frequency crowding check: all qubit-qubit detunings > 485 MHz ✓
- All dispersive limits satisfied: $g/|\Delta_{qr}| < 0.033$ ✓

**Sections:**
1–2. Constants and 4-qubit device parameters
3. Frequencies, frequency crowding check
4. Coupling strengths for all 4 pairs
5. Schrieffer–Wolff for all pairs; OFF and gate points
6. Coupling landscape (4-panel figure)
7. Coherence rates: all 4 qubits, multi-neighbour ZZ
8. 16×16 Lindblad construction
9. QuTiP: individual $T_1^{(i)}$, $T_2^{(i)}$
10. QuTiP: GHZ state coherence
11. iSWAP gate with spectator qubits
12. Summary dashboard
13. Full numerical summary
14. Scaling: coherence vs entanglement size

---

### 04 — Coherence Improvement: Levers, Regimes, and Conflicts

**What it does:** Systematically explores every available improvement to $T_1$ and $T_2$, starting from the junction critical current $I_c$ and working outward. Explains why combined optimisation requires multiple chip generations.

**Baseline diagnosis:** All four qubits are **$T_1$-limited** ($T_2/2T_1 = 0.74$–$0.86$). The dominant $T_1$ channel for Q1/Q2 is Purcell (55–68%). Fixing dephasing does essentially nothing in this regime.

**Eight improvement levers:**

| Lever | Realistic $T_1$ gain | Physical origin |
|---|---|---|
| Purcell filter | **×3.2** | Suppresses $(g/\Delta_{qr})^2\kappa$ |
| Resonator detuning $\Delta_{qr}$ | ×2.1 | $\Gamma_P \propto 1/\Delta_{qr}^2$ |
| Junction $R_N$ (lower $I_c$) | ×2.1 | Lower $\omega_{01}$, $E_J$ → less TLS, QP |
| Ta film ($\tan\delta$ ×3) | ×1.5 | Better oxide chemistry |
| Geometry ($p_\text{surf}$ ×2) | ×1.4 | Less E-field in lossy oxide |
| IR shielding ($x_\text{qp}$ ×10) | ×1.3 | Fewer quasiparticles |
| Shunting cap $C_\text{shunt}$ | ×1.2 | Mainly controls anharmonicity |
| Flux sweet spot | ×1.02 | Only useful when dephasing-limited |

**Regime analysis:** The priority ordering completely changes as the device improves:
- *$T_1$-limited*: $T_1$ fixes give ×3–5 on $T_2$; dephasing fixes give ×1.0
- *Mixed* (after Purcell filter): both matter; dominant $T_1$ channel still leads
- *Dephasing-limited* (after all $T_1$ fixes): sweet spot gives ×5.5; $T_1$ fixes give ×1.0

**Conflicts between levers** (why ×50 requires multiple generations):
- $R_N$ change → all qubit frequencies shift → coupler redesign required
- $p_\text{surf}$ improvement → $C_c$ changes → Purcell rate changes
- Ta film → two-material fabrication (Ta pads + Al junctions)
- IR shielding → impedance matching conflicts with Purcell filter output chain
- All qubits at sweet spot → coupler OFF points may be geometrically inconsistent

**Sections:**
1. Baseline device (diagnosis)
2–8. Individual lever scans (parameter sweeps with gain figures)
9b. Regime analysis: priority ordering in each regime
9. Summary table: all levers with conservative/realistic/max gains
10. Visual summary: gain bar chart and sequential improvement path
11. Why combined optimisation is not realistic (conflicts)
12. Final printed summary and diagnostic workflow

---

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/superconducting-qubits.git
cd superconducting-qubits

# Install dependencies
pip install numpy scipy matplotlib qutip

# Launch Jupyter
jupyter notebook
```

**Tested with:**
- Python 3.11
- NumPy 1.26
- SciPy 1.12
- Matplotlib 3.8
- QuTiP 5.2

---

## Key Physical Concepts by Notebook

| Concept | Notebook |
|---|---|
| Ambegaokar–Baratoff formula | 01, 02, 03, 04 |
| Transmon spectrum ($E_J$, $E_C$, $\omega_{01}$, $\alpha$) | 01, 02, 03, 04 |
| Dispersive readout ($g$, $\chi$, $\kappa$) | 01, 02, 03 |
| Purcell decay | 01, 02, 03, 04 |
| TLS loss (surface participation) | 01, 02, 03, 04 |
| Quasiparticle decay | 01, 02, 03, 04 |
| 1/f flux noise and sweet spot | 01, 02, 03, 04 |
| Schrieffer–Wolff transformation | 02, 03 |
| Tunable coupler, OFF point | 02, 03 |
| $J_\text{eff}(\Phi)$, $\zeta_\text{eff}(\Phi)$ | 02, 03 |
| Lindblad equation (QuTiP) | 02, 03 |
| Rotating frame | 02, 03 |
| ZZ-induced dephasing | 02, 03 |
| Bell state coherence | 02 |
| iSWAP gate fidelity | 02, 03 |
| Frequency crowding | 03 |
| Spectator errors | 03 |
| GHZ state coherence scaling | 03 |
| $T_1$/$T_\varphi$ regime diagnosis | 01, 04 |
| Purcell filter | 04 |
| Pareto frontier: $T_1$ vs gate speed | 04 |
| Sequential chip generation strategy | 04 |

---

## The Central Physical Insight

There are two very different ways coherence time shortens with system size:

**Individual qubit $T_1$, $T_2$ at idle:** These are set by each qubit's own junction parameters, resonator, and local noise. Adding more qubits to a well-designed chip (with good OFF points, $J_\text{eff} \approx 0$) does *not* shorten these.

**Entangled-state coherence:** This always shortens:
$$\frac{1}{T_2^{(n\text{-qubit})}} = \sum_{i=1}^{n} \frac{1}{T_2^{(i)}}$$

A 4-qubit GHZ state is ~4–6× less coherent than the best individual qubit, because all four must maintain coherence simultaneously. This is not a flaw — quantum error correction exploits this structure to protect logical information by encoding it redundantly, recovering coherence at the cost of physical qubit overhead.

---

## Notation Reference

| Symbol | Meaning | Unit |
|---|---|---|
| $E_J$ | Josephson energy | J (or GHz in frequency units) |
| $E_C$ | Charging energy | J (or MHz) |
| $\omega_{01}$ | Qubit transition frequency | rad/s |
| $\alpha$ | Anharmonicity ($= \omega_{12}-\omega_{01}$) | rad/s |
| $g$ | Qubit-resonator coupling | rad/s |
| $\chi$ | Dispersive shift | rad/s |
| $\kappa$ | Resonator linewidth | rad/s |
| $\Delta_{qr}$ | Qubit-resonator detuning | rad/s |
| $J_\text{eff}$ | Effective qubit-qubit exchange | rad/s |
| $\zeta_\text{eff}$ | Effective ZZ coupling | rad/s |
| $T_1$ | Energy relaxation time | s |
| $T_2$ | Total coherence time | s |
| $T_\varphi$ | Pure dephasing time | s |
| $x_\text{qp}$ | Quasiparticle density (normalised) | dimensionless |
| $p_\text{surf}$ | Surface participation ratio | dimensionless |
| $\tan\delta$ | Loss tangent of surface oxide | dimensionless |
| $A_\Phi$ | 1/f flux noise amplitude | Wb/√Hz |

---

## License

MIT License. See `LICENSE` for details.
