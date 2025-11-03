Below is a complete `README.md` you can drop into your **Project 2** folder.
It uses MathJax for equations and your file names as uploaded.

---

# Project 2: Colpitts Oscillator

## Project Overview

A Colpitts LC oscillator using a 2N2222 **common-emitter** stage and a capacitive divider feedback network. Supply (V_{CC}=10,\text{V}). Bias is near mid-supply to maximize undistorted swing. Startup occurs when the **Barkhausen** conditions are met:
[
\lvert A_v \beta \rvert \ge 1,\qquad \angle(A_v\beta)=0^\circ
]

> **Schematic**
> ![Schematic](./Schematic.png)

> **Breadboard (annotated)**
> ![Breadboard](./breadboard_annotated.png)

---

## Theory of Operation

### Colpitts feedback and loop gain

The capacitive divider (C_1)–(C_2) returns a fraction of the collector swing to the emitter:
[
\beta \approx \frac{C_1}{C_2}\quad\text{(for }C_2\gg C_1\text{)}
]
Required midband voltage gain for startup:
[
A_v \gtrsim \frac{1}{\beta} \approx \frac{C_2}{C_1}
]
With your values (C_1=10,\text{pF},; C_2=100,\text{pF}\Rightarrow \beta\approx 0.1,; A_v\gtrsim 10).

### Resonant frequency

The tank is (L) in series with (C_1) and (C_2) forming an equivalent:
[
C_{\text{eq}}=\frac{C_1C_2}{C_1+C_2},\qquad
f_0=\frac{1}{2\pi\sqrt{LC_{\text{eq}}}}
]

### Amplitude control in practice

As the loop builds, the transistor’s nonlinearity limits further growth by pushing the stage into **saturation** near the negative peak and into **cutoff** near the positive peak. At the negative peak the collector–emitter voltage hits
[
V_{CE}\approx V_{CE(\text{sat})}
]
which produces **bottom clipping**. This clipping reduces the effective loop gain toward unity and arrests growth.

### Inductive kickback and overshoot

When the transistor abruptly enters cutoff, the tank current cannot change instantly. The inductor forces current through (R_C), generating a positive **overshoot** at the collector:
[
V_{\text{KICK}}\approx L,\frac{dI}{dt}\quad\Rightarrow\quad V_{C,\text{max}}<V_{CC}+V_{\text{KICK}}
]
This explains measured peaks that exceed the DC rail without requiring supply violations.

### Why the output node “centers low”

The output is taken **after** the coupling capacitor (C_{\text{out}}). DC is blocked, so the plotted waveform should average to 0 V. In hardware the apparent midpoint sits near (V_E) because the LC tank, probe capacitance, and return path form an AC network with finite impedance. The required RMS current through that network produces an average drop that visually shifts the waveform baseline downward, even though the true DC at the node is effectively (0,\text{V}) in simulation.

---

## Simulation Results

> **Steady-state output: frequency**
> ![waveform f](./waveform_freq.png)

> **Steady-state output: amplitude**
> ![waveform vpp](./waveform_vpp.png)

> **Current draw and power**
> ![sim current power](./sim_current_power.png)

---

## Measured vs Simulated Summary

| Parameter                        | Simulated | Observed | Change [+/−] | Error [%] |
| :------------------------------- | --------: | -------: | -----------: | --------: |
| Frequency ([\text{MHz}])         |    11.798 |     7.41 |       −4.388 |     37.19 |
| (V_{\text{out,pp}}) ([\text{V}]) |      10.2 |      8.5 |         −1.7 |     16.67 |
| Current ([\text{mA}])            |     2.295 |    4.045 |        +1.75 |     76.25 |
| Power ([\text{mW}])              |     22.77 |    40.69 |       +17.92 |     78.69 |
| FoM ([\text{dB}])                |     91.98 |    83.87 |        −8.11 |      8.82 |

---

## Why the lab results differ from simulation

### 1. Frequency is lower ((7.41 \text{ vs } 11.8,\text{MHz}))

* **Cause:** extra capacitance raises (C_{\text{eq}}\Rightarrow f_0 \downarrow).
* **Sources:** scope probe (10\text{–}15,\text{pF}), transistor (C_{\mu},C_{\pi}), wiring and breadboard.
* **Logic:** (f_0=\frac{1}{2\pi\sqrt{L(C_1\parallel C_{\text{par}})(C_2\parallel C_{\text{par}})/(C_1\parallel C_{\text{par}}+C_2\parallel C_{\text{par}})}}) and any added (C_{\text{par}}) lowers (f_0).

### 2. (V_{\text{out,pp}}) is lower ((8.5 \text{ vs } 10.2,\text{V}))

* **Cause:** probe and wiring **load** the tank and amplifier.
* **Mechanism:** at MHz, the probe capacitance has low reactance (X_C=1/(2\pi f C)) that shunts signal to ground, reducing effective (A_v). Oscillation settles when (\lvert A_v\beta\rvert \approx 1), so a lower (A_v) yields a smaller final amplitude.

### 3. Power is higher ((40.7 \text{ vs } 22.8,\text{mW}))

* **Cause:** parasitic **resistance** dissipates energy each cycle.
* **Mechanism:** ESR in the tank, wiring, and probe returns convert circulating RF current into heat. The transistor must supply this lost energy, increasing average collector current (I_{\text{avg}}) and therefore (P_{DC}=V_{CC}I_{\text{avg}}).

---

## Saturation analysis on the collector waveform

* **Headroom** at the negative peak:
  [
  V_C\downarrow \Rightarrow V_{CE}\downarrow \Rightarrow V_{CE}\to V_{CE(\text{sat})}
  ]
  bottom flattens, harmonic content appears, and (\lvert A_v\beta\rvert\to 1).
* **Exit from saturation:** as (I_B\downarrow), (I_C\downarrow), the drop on (R_C) eases, (V_C\uparrow), device re-enters the forward active region.
* **Overshoot above (V_{CC}):** when (I_C\to 0) at cutoff, inductor current reroutes through (R_C), producing (V_{\text{KICK}} \approx L,dI/dt) and a transient (V_C>V_{CC}).

---

## Design change to relieve clipping and reduce drive

**Adjustment:** decrease (R_C) to increase collector headroom and lower gain.

* (R_C: 2.2,\text{k}\Omega \rightarrow 1,\text{k}\Omega)
* Effect on bias: (V_C \uparrow\Rightarrow V_{CE} \uparrow) so more downward headroom before saturation.
* Effect on loop gain: (A_v \downarrow) so (\lvert A_v\beta\rvert) is closer to 1 at steady state.
* **Result (simulation):**
  (f\approx 13.827,\text{MHz}), (V_{pp}\approx 8.654,\text{V}), power (\approx 28.9,\text{mW}), FoM (\approx 90.9,\text{dB}).
  Clipping is mitigated and the waveform is cleaner while maintaining robust startup.

---

## Reproduction steps

1. **Open** `colpitts.asc`.
2. **Bias and startup transient**

   ```text
   .tran 0 15m startup
   ```

   Probe (V_C), (V_E), and (V_{\text{out}}). Verify the bottom flat near (V_{CE(\text{sat})}) and the positive overshoot relative to (V_{CC}).
3. **Frequency and amplitude**

   * Measure period (T) from `waveform_freq.png` or the live waveform and compute (f_0=1/T).
   * Read (V_{\text{out,pp}}) from `waveform_vpp.png`.
4. **Power**

   * Plot supply current and compute (P_{DC}=V_{CC}\cdot I_{\text{avg}}) as in `sim_current_power.png`.
5. **Variant with headroom fix**

   * Change (R_C\rightarrow 1,\text{k}\Omega). Re-run transient. Confirm cleaner tops/bottoms and reduced drive.

---

## Files

* `colpitts.asc` — LTspice schematic
* `Schematic.png` — Circuit diagram
* `breadboard_annotated.png` — Hardware photo with notes
* `waveform_freq.png` — Steady-state frequency measurement
* `waveform_vpp.png` — Steady-state amplitude
* `sim_current_power.png` — Average current and DC power
