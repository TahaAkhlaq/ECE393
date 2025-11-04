# Project 2: Colpitts Oscillator

A Colpitts LC oscillator using a 2N2222 common-emitter stage with a capacitive-divider feedback network. Supply \(V_{CC}=10\ \text{V}\). Bias near mid-supply provides headroom. Startup requires \(|A_v\beta|>1\) at the tank resonance.

> **Schematic**  
> ![Schematic](./Schematic.png)

> **Breadboard (annotated)**  
> ![Breadboard](./breadboard_annotated.png)

---

## Theory of Operation

### Colpitts feedback and loop gain
The divider formed by \(C_1\) and \(C_2\) returns a fraction of the collector swing to the emitter.

$$
\beta \approx \frac{C_1}{C_2}\quad \text{for } C_2 \gg C_1
$$

Required midband voltage gain for startup:

$$
|A_v| \gtrsim \frac{1}{|\beta|} \approx \frac{C_2}{C_1}
$$

With \(C_1=10\ \text{pF}\) and \(C_2=100\ \text{pF}\): \(\beta\approx 0.1\) and \(|A_v|\gtrsim 10\).

### Resonant frequency
Equivalent tank capacitance and oscillation frequency:

$$
C_{\text{eq}}=\frac{C_1 C_2}{C_1+C_2},\qquad
f_0=\frac{1}{2\pi\sqrt{L\,C_{\text{eq}}}}
$$

### Amplitude control
Growth pushes the BJT into saturation near the negative peak and into cutoff near the positive peak. At the bottom:

$$
V_{CE}\approx V_{CE(\text{sat})}
$$

Bottom clipping reduces effective loop gain toward unity and stabilizes amplitude.

### Inductive kickback and overshoot
When the transistor cuts off, inductor current cannot change instantaneously. The tank forces current through \(R_C\), producing a positive overshoot:

$$
V_{\text{KICK}} \approx L\,\frac{di}{dt},\qquad
V_{C,\max} \lesssim V_{CC}+V_{\text{KICK}}
$$

### Why the output baseline appears low
\(V_{\text{out}}\) is taken after the coupling capacitor \(C_{\text{out}}\). True DC is blocked so the node is 0 V in simulation. In hardware, the LC tank, probe capacitance, and return path form a finite-impedance AC network. The RMS current through that network creates an average drop that visually shifts the plotted baseline toward \(V_E\) even though the DC is effectively 0 V.

---

## Simulation Results

> **Steady-state output: frequency**  
> ![Frequency](./waveform_freq.png)

> **Steady-state output: peak-to-peak**  
> ![Vpp](./waveform_vpp.png)

> **Average current and DC power**  
> ![Current and Power](./sim_current_power.png)

---

## Measured vs Simulated Summary

| Parameter | Simulated | Observed | Change [+/−] | Error [%] |
|:--|--:|--:|--:|--:|
| Frequency [MHz] | 11.798 | 7.41 | −4.388 | 37.19 |
| \(V_{\text{out,pp}}\) [V] | 10.2 | 8.5 | −1.7 | 16.67 |
| Current [mA] | 2.295 | 4.045 | +1.75 | 76.25 |
| Power [mW] | 22.77 | 40.69 | +17.92 | 78.69 |
| FoM [dB] | 91.98 | 83.87 | −8.11 | 8.82 |

---

## Why lab differs from simulation

### 1) Frequency is lower
Cause: added capacitance raises \(C_{\text{eq}}\) and reduces \(f_0\).

$$
f_0=\frac{1}{2\pi\sqrt{L\,(C_1\parallel C_{\text{par}})\,(C_2\parallel C_{\text{par}})/(C_1\parallel C_{\text{par}}+C_2\parallel C_{\text{par}})}}
$$

Sources: scope probe 10× tip \(10\text{–}15\ \text{pF}\), BJT \(C_\mu, C_\pi\), wiring, breadboard.

### 2) Output \(V_{\text{pp}}\) is lower
Cause: probe and wiring load the tank and the amplifier.

$$
X_C=\frac{1}{2\pi f C}\ \text{is small at MHz} \Rightarrow \text{shunt to ground} \Rightarrow |A_v|_{\text{real}}<|A_v|_{\text{sim}}
$$

Oscillation settles where \(|A_v\beta|\approx 1\), so a smaller \(|A_v|\) yields a smaller \(V_{\text{pp}}\).

### 3) Power is higher
Cause: parasitic resistance dissipates energy each cycle. ESR in the tank, wiring, and probe returns increases average collector current.

$$
P_{DC}=V_{CC}\,I_{\text{avg}}
$$

---

## Saturation analysis on \(V_C\)

- Headroom at negative peak:

$$
V_C\downarrow \Rightarrow V_{CE}\downarrow \Rightarrow V_{CE}\to V_{CE(\text{sat})}
$$

- Exit from saturation:

$$
I_B\downarrow,\ I_C\downarrow \Rightarrow \Delta V_{RC}\downarrow \Rightarrow V_C\uparrow
$$

- Overshoot above \(V_{CC}\):

$$
I_C\to 0 \Rightarrow V_{\text{KICK}}\approx L\,\frac{di}{dt},\quad V_C>V_{CC}\ \text{transiently}
$$

---

## Design change to reduce clipping and drive

Adjustment: decrease \(R_C\) to increase headroom and lower gain.

- \(R_C: 2.2\ \text{k}\Omega \rightarrow 1\ \text{k}\Omega\)
- Bias effect: \(V_C\uparrow\Rightarrow V_{CE}\uparrow\) so more downward headroom.
- Loop-gain effect: \(|A_v|\downarrow\) so \(|A_v\beta|\) is closer to 1.
- Result (simulation): \(f\approx 13.827\ \text{MHz}\), \(V_{pp}\approx 8.654\ \text{V}\), \(P\approx 28.9\ \text{mW}\), FoM \(\approx 90.9\ \text{dB}\). Clipping is mitigated and the waveform is cleaner.

---

## Reproduction steps

1. Open `colpitts.asc`.

2. Bias and startup transient
   `text
   .tran 0 15m startup
   `

Probe \(V_C\), \(V_E\), and \(V_{\text{out}}\). Verify the bottom flat near \(V_{CE(\text{sat})}\) and the positive overshoot above \(V_{CC}\).

3. Frequency and amplitude  
Measure period \(T\) and compute

$$
f_0=\frac{1}{T}
$$

Read \(V_{\text{out,pp}}\) from the waveform.

4. Power  
Plot supply current and compute

$$
P_{DC}=V_{CC}\,I_{\text{avg}}
$$

5. Variant with headroom fix  
Change \(R_C\rightarrow 1\ \text{k}\Omega\) and re-run.

---

## Files

- `colpitts.asc` — LTspice schematic  
- `Schematic.png` — Circuit diagram  
- `breadboard_annotated.png` — Hardware photo with notes  
- `waveform_freq.png` — Steady-state frequency measurement  
- `waveform_vpp.png` — Steady-state amplitude  
- `sim_current_power.png` — Average current and DC power
