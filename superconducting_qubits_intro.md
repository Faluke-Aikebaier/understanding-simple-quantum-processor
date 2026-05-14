# Superconducting Qubits: From Josephson Junction to Multi-Qubit Coherence

A companion document to the four Jupyter notebooks in this repository.  
This introduction follows the workflow of the textbook and explains the physics behind each notebook.

---

## 1. What Is a Superconducting Qubit?

A superconducting qubit is an electrical circuit that behaves quantum mechanically at millikelvin temperatures. The key ingredient is the **Josephson junction** — a thin insulating barrier between two superconductors. Unlike a classical inductor, the Josephson junction provides a *nonlinear* inductance, which is what makes the circuit anharmonic and therefore usable as a qubit.

The two lowest energy levels ($|0\rangle$ and $|1\rangle$) are addressed at frequency $\omega_{01}$. Because the spacing between levels is not uniform (anharmonicity $\alpha = \omega_{12} - \omega_{01} \neq 0$), we can drive the $|0\rangle \leftrightarrow |1\rangle$ transition without exciting $|1\rangle \leftrightarrow |2\rangle$.

---

## 2. The Transmon: Making Charge Noise Irrelevant

The simplest Josephson circuit is the **Cooper pair box**, which is exquisitely sensitive to offset charge noise. The **transmon** solves this by shunting the junction with a large capacitor $C_\text{shunt}$, making $E_J/E_C \gg 1$. In this regime:

$$\omega_{01} \approx \frac{\sqrt{8 E_J E_C} - E_C}{\hbar}, \qquad \alpha = -\frac{E_C}{\hbar}$$

Charge noise sensitivity decays exponentially as $e^{-\sqrt{8E_J/E_C}}$. At $E_J/E_C \sim 50$–100, the qubit is essentially immune to charge noise. The cost is a smaller anharmonicity $|\alpha| = E_C/\hbar$, which sets the minimum gate duration.

**Parameter origins:**
- $E_J = \pi\hbar\Delta_{sc}/4e^2R_N$ — set by junction area ($R_N$ is [GEOMETRY]) and material ($\Delta_{sc}$ is [MATERIAL])
- $E_C = e^2/2C_\Sigma$ — set purely by capacitor geometry ([GEOMETRY])

---

## 3. The Three $T_1$ Channels

Energy relaxation $T_1$ is limited by three independent loss mechanisms:

### Purcell decay
The qubit is dispersively coupled to a readout resonator ($g \ll |\Delta_{qr}|$). The resonator leaks photons at rate $\kappa$, and the qubit inherits a fraction of this loss:
$$\Gamma_P = \left(\frac{g}{\Delta_{qr}}\right)^2 \kappa$$
This is purely a circuit design issue — it can be suppressed by a **Purcell filter** or by increasing $|\Delta_{qr}|$.

### Two-level system (TLS) loss
The surface oxide on the metal electrodes hosts microscopic two-level defects that absorb microwave energy:
$$\Gamma_\text{TLS} = p_\text{surf} \cdot \omega_{01} \cdot \tan\delta$$
where $p_\text{surf}$ is the fraction of electric field energy in the lossy oxide ([GEOMETRY] — controlled by pad shape), and $\tan\delta$ is the oxide loss tangent ([MATERIAL/MEASURED] — improved by switching to tantalum).

### Quasiparticle loss
Non-equilibrium quasiparticles (broken Cooper pairs) tunnel through the junction and dissipate energy:
$$\Gamma_\text{qp} = \frac{8E_J}{\pi\hbar}\sqrt{\frac{2\Delta_{sc}}{\hbar\omega_{01}}} \cdot x_\text{qp}$$
where $x_\text{qp}$ is the quasiparticle density ([MEASURED] — reduced by infrared shielding and phonon traps).

---

## 4. Dephasing and $T_2$

The coherence time $T_2$ obeys:
$$\frac{1}{T_2} = \frac{1}{2T_1} + \frac{1}{T_\varphi}, \qquad T_2 \leq 2T_1$$

Pure dephasing $T_\varphi$ has two main contributions:

**1/f flux noise** — low-frequency magnetic field fluctuations shift $\omega_{01}$ stochastically:
$$\Gamma_\varphi^{1/f} \approx \left|\frac{d\omega_{01}}{d\Phi}\right| \cdot A_\Phi \cdot \sqrt{2\ln 2}$$
This is eliminated by operating at the **flux sweet spot** ($\Phi = 0$), where $d\omega_{01}/d\Phi = 0$ to first order.

**Photon shot noise** — thermal photons in the resonator shift $\omega_{01}$ via the dispersive coupling:
$$\Gamma_\varphi^\text{photon} = \frac{8\chi^2 \bar{n}}{\kappa}$$
This is negligible at 15 mK where $\bar{n}_\text{th} \approx 10^{-12}$.

### Regime diagnosis
$$T_2 \approx 2T_1 \Rightarrow \text{T1-limited: fix relaxation channels}$$
$$T_2 \ll 2T_1 \Rightarrow \text{Dephasing-limited: fix flux/photon noise}$$

---

## 5. Readout: The Dispersive Regime

The qubit is read out through a microwave resonator. In the dispersive limit ($g \ll |\Delta_{qr}|$), the resonator frequency shifts by $\pm\chi$ depending on the qubit state:
$$\chi = \frac{g^2}{\Delta_{qr}}$$
A microwave tone sent through the resonator acquires a qubit-state-dependent phase, enabling non-destructive measurement. Resolved readout requires $|\chi| > \kappa/2$.

---

## 6. Two-Qubit Coupling: The Tunable Coupler

Two qubits are coupled via a **SQUID coupler** — a third transmon whose frequency $\omega_c(\Phi)$ is tunable by an external flux. The Schrieffer–Wolff transformation eliminates the coupler and gives an effective two-qubit Hamiltonian:

$$H_\text{eff} = \hbar J_\text{eff}(\Phi)(\sigma_+^{(1)}\sigma_-^{(2)} + \text{h.c.}) + \hbar\zeta_\text{eff}(\Phi)\sigma_z^{(1)}\sigma_z^{(2)}$$

where:
$$J_\text{eff} = \frac{g_1 g_2}{2}\left(\frac{1}{\Delta_1}+\frac{1}{\Delta_2}\right) + g_{12}, \qquad \zeta_\text{eff} = -\frac{g_1^2 g_2^2}{2\alpha_c}\left(\frac{1}{\Delta_1^2\Delta_2}+\frac{1}{\Delta_1\Delta_2^2}\right)$$

**At the OFF point** (a specific flux $\Phi_\text{off}$), $J_\text{eff} \approx 0$ — the qubits are decoupled at idle. The residual $\zeta_\text{eff}$ causes ZZ-induced dephasing:
$$\Gamma_\text{ZZ}^{(i)} = 2\zeta_\text{eff}^2 T_1^{(j)}$$

**At the gate point**, $|J_\text{eff}|$ is maximised. An iSWAP gate runs for time $t = \pi/2|J_\text{eff}|$.

### Why the Lindblad equation must be solved numerically

For a single qubit the Lindblad has closed-form solutions ($\rho_{11}(t) = e^{-t/T_1}$, $\rho_{01}(t) = e^{-t/T_2}$).

For two or more qubits, the density matrix is $2^n \times 2^n$ with $(4^n - 1)$ coupled equations. The ZZ coupling mixes coherences — qubit 1's phase evolution depends on qubit 2's state and vice versa. There is no simple closed form. We use **QuTiP** to solve numerically.

**Rotating frame:** Qubit frequencies are ~5 GHz (periods ~200 ps) but $T_1$, $T_2$ are ~10–100 μs — a ratio of $10^5$–$10^6$. Simulating in the lab frame requires $\sim10^6$ integration steps. The rotating frame removes the fast free-precession terms, leaving only slow terms ($\zeta_\text{eff}$, detuning) in $H$. The jump operators are frame-independent.

---

## 7. Four-Qubit System: New Effects

Scaling to four qubits on a square lattice introduces qualitatively new physics:

### Frequency crowding
Eight circuits (4 qubits + 4 couplers) must all have distinct frequencies. Collisions cause spurious coupling and Purcell-like decay. The minimum qubit-qubit detuning should exceed the coupling strength ($|\delta_{ij}| \gg g_{ij}$).

### Multiple OFF points
Each of the four couplers has its own OFF flux $\Phi_\text{off}^{(k)}$. All four must simultaneously satisfy $J_\text{eff} \approx 0$. This is achievable for nearest-neighbour pairs but requires careful coupler design.

### Spectator errors
Running a gate on Q1-Q2 creates flux pulses that leak into neighbouring coupler coils (crosstalk). This temporarily shifts $J_\text{eff}$ for Q1-Q3 and Q2-Q4 away from their OFF points, causing unwanted entanglement between the gate pair and spectator qubits.

### System-level coherence
Individual $T_1$, $T_2$ at idle are unchanged by adding more qubits (the OFF point design ensures $J_\text{eff} \approx 0$). But entangled-state coherence always shortens:
$$\frac{1}{T_2^\text{GHZ}} = \sum_{i=1}^{n} \frac{1}{T_2^{(i)}}$$
A 4-qubit GHZ state is $\sim$4–6× less coherent than the best individual qubit.

---

## 8. Coherence Improvement: Workflow and Conflicts

### The standard diagnostic workflow

```
1. Measure T1, T2
2. Is T2 ≈ 2T1?  → T1-limited → identify dominant T1 channel
   Is T2 ≪ 2T1?  → Dephasing-limited → identify dominant dephasing source
3. T1 channel diagnosis:
   Purcell >50% → add Purcell filter (biggest single gain, ×3)
   TLS >50%     → switch to tantalum film, improve pad geometry (×1.5–2)
   QP >50%      → improve IR shielding, add phonon traps (×1.3–2)
4. Fix dominant channel. Re-fabricate. Go to step 1.
5. Once T1-limited regime is resolved:
   Operate at flux sweet spot → eliminates 1/f dephasing (×5 in T2)
   Reduce resonator photon population → eliminates photon shot noise
```

### Priority table (current T1-limited device)

| Lever | Realistic gain ($T_1$) | Constraint |
|---|---|---|
| Purcell filter | ×3.2 | Narrower filter → slower readout |
| Junction $R_N$ (lower $I_c$) | ×2.1 | Smaller junctions, worse fab tolerance |
| Ta film ($\tan\delta$) | ×1.5 | Two-material fabrication process |
| Geometry ($p_\text{surf}$) | ×1.4 | Changing pads alters coupling caps |
| IR shielding ($x_\text{qp}$) | ×1.3 | Conflicts with Purcell filter chain |
| Flux sweet spot | ×1.02 | Negligible in T1-limited regime |

### Why combined optimisation requires multiple chip generations

Each improvement lever conflicts with others:
- **$R_N$ increase → coupling redesign** — changing qubit frequencies shifts all OFF points
- **$p_\text{surf}$ improvement → Purcell rate changes** — larger pads change $C_c$ and hence $g$
- **Ta film → two-material fab** — Ta pads and Al junctions require separate deposition steps
- **IR shielding → filter chain conflicts** — attenuators on every line must be impedance-matched with the Purcell filter
- **All qubits at sweet spot → coupler OFF points may be inconsistent** — a constrained nonlinear optimisation that may have no solution for a given layout

In practice, each generation of chip development improves one dominant channel, exposing the next:

| Era | Main improvement | $T_1$ achieved |
|---|---|---|
| 2013–2016 | Transmon, basic shielding | 10–50 μs |
| 2017–2019 | Better substrate, lower $x_\text{qp}$ | 50–200 μs |
| 2020–2022 | Tantalum film | 100–500 μs |
| 2023–2025 | Purcell filter + geometry | 300 μs–1 ms |

---

## 9. The Role of Quantum Error Correction

Individual qubit coherence determines the physical error rate per gate:
$$\epsilon_\text{gate} \approx \frac{t_\text{gate}}{T_1}$$

For a 16 ns iSWAP gate with $T_1 = 30\,\mu$s: $\epsilon \approx 5 \times 10^{-4}$ — well below the surface code threshold of $\sim 10^{-2}$.

However, system-level coherence degrades with scale:
- 1 qubit: $T_2 \approx 50\,\mu$s
- 4-qubit GHZ: $T_2^\text{GHZ} \approx 10\,\mu$s
- $n$-qubit logical: $T_2^\text{logical} \sim T_2/n$ without QEC

Quantum error correction (surface code) reverses this trend — below the error threshold, each additional round of syndrome measurement exponentially suppresses the logical error rate, even as the raw physical coherence stays constant. The surface code logical qubit's coherence can in principle exceed any individual physical qubit's coherence, at the cost of encoding one logical qubit in $O(d^2)$ physical qubits (distance $d$).

The four-notebook series in this repository builds the complete understanding needed to see why all of this is necessary — from the physics of a single junction to the system-level effects that QEC must overcome.

---

## References and Further Reading

- Koch et al., *Charge-insensitive qubit design derived from the Cooper pair box*, PRA 76, 042319 (2007) — original transmon paper
- Blais et al., *Circuit quantum electrodynamics*, Rev. Mod. Phys. 93, 025005 (2021) — comprehensive review
- Krantz et al., *A quantum engineer's guide to superconducting qubits*, Appl. Phys. Rev. 6, 021318 (2019) — practical guide
- Place et al., *New material platform for superconducting transmon qubits with coherence times exceeding 0.3 ms*, Nat. Commun. 12, 1779 (2021) — tantalum qubits
- Rol et al., *Fast, high-fidelity conditional-phase gate exploiting leakage interference*, PRL 123, 120502 (2019) — tunable coupler gates
- Google Quantum AI, *Suppressing quantum errors by scaling a surface code logical qubit*, Nature 614, 676 (2023) — QEC below threshold
