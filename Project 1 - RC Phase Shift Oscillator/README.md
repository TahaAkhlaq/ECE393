# Project 1 — RC Phase-Shift Oscillator

---

## Project Overview
This project designs, simulates, and analyzes a **single-transistor RC phase-shift oscillator** composed of a **common-emitter (CE) BJT amplifier** and a **three-section RC feedback network**.  
The CE stage provides an inherent **180° phase inversion**; the RC ladder contributes the remaining **~180°** at the oscillation frequency **f₀**.  
When both **Barkhausen conditions** are satisfied:

**|Aβ| = 1** and **∠(Aβ) = 0° (mod 360°)**,

small noise is reinforced, and the circuit settles to a **self-sustained sinusoid**.  
As amplitude grows, device nonlinearities reduce the effective loop gain slightly below unity, naturally **stabilizing** the waveform.

> **Schematic**  
> ![RC Phase-Shift Oscillator Schematic](./schematic.png)

---

## Theory of Operation

### Oscillation Frequency (Three Equal Sections)
For three identical RC sections:

**f₀ ≈ 1 / (2π·R·C·√6)**

This formula predicts the oscillation frequency assuming equal resistor and capacitor values.  
Plaintext reference: `f0 ≈ 1 / (2π·R·C·√6)`.

### Minimum Amplifier Gain
To overcome the ladder’s attenuation at **f₀**, the CE amplifier must provide approximately:

**|A| ≳ 29** (≈ **29 V/V**, ≈ **30 dB**)

An **emitter bypass capacitor** reduces AC degeneration to meet this requirement, while the DC bias network preserves operating-point stability.

---

## Circuit Design

### Common-Emitter Amplifier (2N2222)

| Component | Symbol | Value | Purpose |
|:--|:--:|:--:|:--|
| Collector resistor | R<sub>C</sub> | 2.2 kΩ | Sets collector load and gain |
| Emitter resistor | R<sub>E</sub> | 820 Ω | Provides DC bias stabilization |
| Emitter bypass capacitor | C<sub>E</sub> | 100 nF | Restores AC gain (reduces degeneration) |
| Bias divider (top) | R<sub>B1</sub> | 30 kΩ | Sets base voltage V<sub>B</sub> |
| Bias divider (bottom) | R<sub>B2</sub> | 10 kΩ | Sets base voltage V<sub>B</sub> |
| Coupling capacitors | C<sub>in</sub>, C<sub>out</sub> | 100 nF | Enable AC coupling |
| Supply | V<sub>CC</sub> | 10 V | DC rail |

**Design Notes:**  
The bias is placed near mid-supply to maximize undistorted swing.  
C<sub>E</sub> is sized so that the midband gain comfortably exceeds the ≈ 29× threshold around **f₀**.

---

### RC Feedback Network (Equal Sections)

| Component | Symbol(s) | Value | Purpose |
|:--|:--:|:--:|:--|
| Ladder resistors | R<sub>ps1</sub>, R<sub>ps2</sub>, R<sub>ps3</sub> | 1.5 kΩ | Series elements |
| Ladder capacitors | C<sub>ps1</sub>, C<sub>ps2</sub>, C<sub>ps3</sub> | 680 pF | Shunt elements to ground |

> The equal-section formula above predicts **f₀** within a few percent.  
> Small offsets are expected due to **loading** between sections and **transistor parasitics**.

---

## Simulation Results

Simulations evaluate startup, steady-state behavior, and **open-loop amplifier gain** (with the RC ladder disconnected for the AC sweep).

### Transient — Startup and RC Node Voltages
Oscillations build from noise because **|Aβ| > 1** near **f₀**.  
Amplitudes taper down the ladder and phases progress section-to-section, as predicted.

> ![Transient: Startup and RC Nodes](./transient_nodes.png)

### Transient — Steady-State Output
After startup, the loop settles to a clean sinusoid near the design frequency.

> ![Steady-State Output](./output.png)

### AC Analysis — Amplifier Open-Loop Gain (Magnitude in dB)
The RC ladder is **disconnected**, and a **1 V AC** test source drives the amplifier.  
The Bode magnitude (in dB) shows a **midband gain ≳ 30 dB**, providing comfortable startup margin once feedback is restored.

> ![Open-Loop Gain in dB](./db_gain.png)

### AC Analysis — Amplifier Open-Loop Gain (Magnitude in V/V)
Same sweep, plotted in **linear V/V**.  
Peak gain aligns with the operating band; when the ladder is reattached, the loop satisfies **|Aβ| ≈ 1** at **f₀**.

> ![Open-Loop Gain in V/V](./v_gain.png)

---

## Performance Targets & Evaluation (from Assignment)

- **Frequency target:** f₀ > 20 kHz (bonus for > 40 kHz)  
- **Power:** preference for < 90 mW  
- **Amplifier gain (V/V):** higher categories earn more points  
- **Output amplitude:** larger V<sub>pp</sub> categories earn more points  

This design meets the **frequency goal** (≈ 65 kHz), demonstrates **gain** well above the ≈ 29× threshold, and maintains modest **power** with strong **output swing**.

---

## Procedure

1. **Open the schematic:** `circuit.asc`

2. **Transient (with RC ladder connected)**  
   Add the directive:  
   `.tran 0 100m startup`  
   - Run the transient simulation and observe **V<sub>out</sub>** and the three RC node voltages.  
   - Measure the steady-state period **T** from the waveform and compute **f₀ = 1/T**.  
   - Compare with the theoretical estimate **f₀ ≈ 1 / (2π·R·C·√6)**.

3. **Open-loop AC gain (RC ladder disconnected)**  
   Disconnect the RC ladder from the amplifier input and drive the amplifier with a **1 V AC** source:  
   `.ac dec 200 1k 100Meg`  
   - Plot magnitude in **dB** and **V/V**.  
   - Verify that the midband gain is greater than **29 V/V** (≈ 30 dB) around the operating band.

---

## Simulation & Measurement Summary

| Parameter | Observed | Simulated | Error [%] |
|:--|--:|--:|--:|
| V<sub>out,pp</sub> [V] | 6.8000 | 7.3446 | 7.41 |
| V<sub>in,pp</sub> [mV] | 110.0000 | 117.3145 | 6.23 |
| Frequency [kHz] | 65.21 | 66.5068 | 1.95 |
| Gain [V/V] | 61.8 | 62.6 | 1.28 |
| Power [mW] | 24.6912 | 22.8978 | 7.83 |
| Current Drawn [mA] | 2.4545 | 2.2898 | 7.19 |
| V<sub>c</sub> [V] | 5.1974 | 5.3339 | 2.56 |
| V<sub>e</sub> [V] | 1.7937 | 1.7477 | 2.63 |
| V<sub>b</sub> [V] | 2.4294 | 2.4221 | 0.30 |
| V<sub>CC</sub> [V] | 10.0596 | 10.0000 | 0.60 |
| I<sub>c</sub> [mA] | N/A | 2.1209 | N/A |
| V<sub>be</sub> [V] | 0.6233 | 0.6745 | 7.59 |
| V<sub>ce</sub> [V] | 3.4037 | 3.5863 | 5.09 |

---

### Interpretation

- **Frequency:** Observed **65.21 kHz** and simulated **66.51 kHz** agree within ~2%, consistent with expected deviations from ideal RC·√6 due to loading and parasitics.  
- **Gain:** Open-loop amplifier gain ≈ **62 V/V** (~35.8 dB) exceeds the ≈ 29× threshold, explaining robust startup when feedback is connected.  
- **Amplitude:** V<sub>out,pp</sub> ≈ 6.8 V on a 10 V supply demonstrates strong headroom; the sinusoid is clean post-settling.  
- **Power:** ~23–25 mW dissipation is reasonable for a single-transistor CE stage at 10 V.  
- **Operating Point:** V<sub>c</sub>, V<sub>b</sub>, and V<sub>e</sub> closely match simulation, confirming the **bias network** places the transistor in the intended active region.

---

## Files

- `circuit.asc` — Simulation schematic  
- `schematic.png` — Circuit diagram  
- `transient_nodes.png` — Startup and RC node voltages  
- `output.png` — Steady-state waveform  
- `db_gain.png` — Open-loop magnitude (dB)  
- `v_gain.png` — Open-loop magnitude (V/V)
