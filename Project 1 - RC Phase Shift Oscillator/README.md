# Project 1 — RC Phase-Shift Oscillator

---

## Project Overview
This project designs, simulates, and analyzes a **single-transistor RC phase-shift oscillator** composed of a **common-emitter (CE) BJT amplifier** and a **three-section RC feedback network**.  
The CE stage provides an inherent **180° phase inversion**; the RC ladder contributes the remaining **~180°** at the oscillation frequency \(f_0\). When both **Barkhausen conditions** are satisfied,
$$
|A\beta| = 1, \qquad \angle(A\beta)=0^\circ\;(\text{mod }360^\circ),
$$
small noise is reinforced and the circuit settles to a **self-sustained sinusoid**. As amplitude grows, device nonlinearities reduce the effective loop gain slightly below unity, naturally **stabilizing** the waveform.

> **Schematic**  
> ![RC Phase-Shift Oscillator Schematic](./schematic.png)

---

## Theory of Operation

### Oscillation frequency (three equal sections)
For three identical RC sections,
$$
f_0 \approx \frac{1}{2\pi\,R\,C\,\sqrt{6}}.
$$
Plaintext: `f0 ≈ 1 / (2π·R·C·√6)`.

### Minimum amplifier gain
To overcome the ladder’s attenuation at \(f_0\), the CE amplifier must provide approximately
$$
|A| \gtrsim 29 \quad (\text{≈ 29 V/V } \approx 29.2\text{ V/V} \approx 29~\text{gain} \approx 29).
$$
(≈ 30 dB in logarithmic units.) An **emitter bypass** capacitor reduces AC degeneration to meet this requirement while the DC bias network preserves operating-point stability.

---

## Circuit Design

### Common-Emitter Amplifier (2N2222)

| Component | Symbol | Value | Purpose |
|:--|:--:|:--:|:--|
| Collector resistor | \(R_C\) | 2.2 kΩ | Sets collector load / gain |
| Emitter resistor | \(R_E\) | 820 Ω | DC bias stabilization |
| Emitter bypass capacitor | \(C_E\) | 100 nF | Restores AC gain (reduces degeneration) |
| Bias divider (top) | \(R_{B1}\) | 30 kΩ | Sets \(V_B\) |
| Bias divider (bottom) | \(R_{B2}\) | 10 kΩ | Sets \(V_B\) |
| Coupling capacitors | \(C_{in}, C_{out}\) | 100 nF | AC coupling |
| Supply | \(V_{CC}\) | 10 V | DC rail |

**Design notes.** The bias is placed near mid-supply to maximize undistorted swing; \(C_E\) is sized so the midband gain comfortably exceeds the ≈ 29× threshold around \(f_0\).

### RC Feedback Network (equal sections)

| Component | Symbols | Value | Purpose |
|:--|:--:|:--:|:--|
| Ladder resistors | \(R_{ps1},R_{ps2},R_{ps3}\) | 1.5 kΩ | Series elements |
| Ladder capacitors | \(C_{ps1},C_{ps2},C_{ps3}\) | 680 pF | Shunt elements to ground |

> The equal-section formula above predicts \(f_0\) within a few percent; small offsets are expected due to **loading** between sections and **transistor parasitics**.

---

## Simulation Results

Simulations evaluate startup, steady-state behavior, and **open-loop amplifier gain** (with the RC ladder disconnected for the AC sweep).

### Transient — Startup and RC Node Voltages
Oscillations build from noise because \(|A\beta|>1\) near \(f_0\). Amplitudes taper down the ladder and phases progress section-to-section, as predicted.

> ![Transient: Startup and RC Nodes](./transient_nodes.png)

### Transient — Steady-State Output
After the build-up, the loop settles to a clean sinusoid near the design frequency.

> ![Steady-State Output](./output.png)

### AC Analysis — Amplifier **Open-Loop** Gain (Magnitude in dB)
The RC ladder is **disconnected**, and a **1 V AC** test source drives the amplifier. The Bode magnitude (dB) shows a **midband gain ≳ 30 dB**, providing comfortable startup margin once feedback is restored.

> ![Open-Loop Gain in dB](./db_gain.png)

### AC Analysis — Amplifier **Open-Loop** Gain (Magnitude in V/V)
Same sweep, plotted in **linear V/V**. Peak gain aligns with the operating band; when the ladder is re-attached, the loop satisfies \(|A\beta|\approx1\) at \(f_0\).

> ![Open-Loop Gain in V/V](./v_gain.png)

---

## Performance Targets & Evaluation (from assignment)

- **Frequency target:** \(f_0 > 20\text{ kHz}\) (bonus for \(>40\text{ kHz}\)).  
- **Power:** preference for \(<90\text{ mW}\).  
- **Amplifier gain (V/V):** higher categories earn more points.  
- **Output amplitude:** larger \(V_{pp}\) categories earn more points.

The design meets the **frequency** goal (measured ≈ 65 kHz) and demonstrates **gain** well above the ≈ 29× threshold, while maintaining modest **power** and strong **output swing**.

---

## Procedure

1. **Open the schematic:** `circuit.asc`.

2. **Transient (with RC ladder connected)**
   Add the directive: `.tran 0 100m startup`

   * Run the transient simulation; observe Vout and the three RC node voltages.
   * Measure the steady-state period T from the waveform and compute f0 = 1/T.
   * Compare with the theoretical estimate f0 ≈ 1/(2π·R·C·√6).

3. **Open-loop AC gain (RC ladder disconnected)**
   Disconnect the RC ladder from the amplifier input and drive the amplifier with a 1 V AC source; add: `.ac dec 200 1k 100Meg`

   * Plot magnitude in dB and in V/V.
   * Verify that the midband gain is greater than 29 V/V (≈ 30 dB) around the operating band.

---

## Simulation & Measurement Summary

| Parameter          | Observed | Simulated | Error [%] |
| :----------------- | -------: | --------: | --------: |
| (V_{out,pp}) [V]   |   6.8000 |    7.3446 |      7.41 |
| (V_{in,pp}) [mV]   | 110.0000 |  117.3145 |      6.23 |
| Frequency [kHz]    |    65.21 |   66.5068 |      1.95 |
| Gain [V/V]         |     61.8 |      62.6 |      1.28 |
| Power [mW]         |  24.6912 |   22.8978 |      7.83 |
| Current drawn [mA] |   2.4545 |    2.2898 |      7.19 |
| (V_c) [V]          |   5.1974 |    5.3339 |      2.56 |
| (V_e) [V]          |   1.7937 |    1.7477 |      2.63 |
| (V_b) [V]          |   2.4294 |    2.4221 |      0.30 |
| (V_{CC}) [V]       |  10.0596 |   10.0000 |     0.596 |
| (I_c) [mA]         |      N/A |    2.1209 |       N/A |
| (V_{be}) [V]       |   0.6233 |    0.6745 |      7.59 |
| (V_{ce}) [V]       |   3.4037 |    3.5863 |      5.09 |

### Interpretation

* **Frequency:** Observed **65.21 kHz** and simulated **66.51 kHz** agree within ~**2%**, consistent with expected deviations from ideal (RC\sqrt{6}) due to loading and parasitics.
* **Gain:** Open-loop amplifier gain ≈ **62 V/V** (~**35.8 dB**) exceeds the ≈ **29×** threshold, explaining robust startup when feedback is connected.
* **Amplitude:** (V_{out,pp} \approx 6.8\ \text{V}) on a 10 V supply demonstrates strong headroom; the sinusoid is clean post-settling.
* **Power:** ~**23–25 mW** dissipation is reasonable for a single-transistor CE stage at 10 V.
* **Operating point:** (V_c), (V_b), (V_e) closely match simulation, confirming the **bias network** places the device in the intended region.

---

## Files

* `circuit.asc` — Simulation schematic.
* `schematic.png` — Circuit diagram.
* `transient_nodes.png` — Startup and RC node voltages.
* `output.png` — Steady-state waveform.
* `db_gain.png` — Open-loop magnitude (dB).
* `v_gain.png` — Open-loop magnitude (V/V).
