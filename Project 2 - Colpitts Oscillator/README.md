# Project 2: Colpitts Oscillator

A Colpitts LC oscillator using a 2N2222 common-emitter stage with a capacitive-divider feedback network. Designed at $V_{CC}=10\ \text{V}$ with bias near mid-supply; startup ensured by $|A_v\beta|>1$.

> **Schematic**  
> ![Schematic](./Schematic.png)

> **Breadboard (annotated)**  
> ![Breadboard annotated](./breadboard_annotated.png)

---

## Theory of Operation

### Colpitts feedback and loop gain

The divider formed by $C_1$ and $C_2$ returns a fraction of the collector swing to the emitter.

$$
\beta \approx \frac{C_1}{C_2}\qquad(C_2\gg C_1)
$$

Required midband voltage gain for startup:

$$
|A_v|\gtrsim \frac{1}{|\beta|}\approx \frac{C_2}{C_1}
$$

With $C_1=10\ \text{pF}$ and $C_2=100\ \text{pF}$: $\beta\approx 0.1$ and $|A_v|\gtrsim 10$.

### Resonant frequency

The tank has $L$ in series with $C_1$ and $C_2$; the equivalent capacitance and frequency are

$$
C_{\mathrm{eq}}=\frac{C_1C_2}{C_1+C_2},
\qquad
f_0=\frac{1}{2\pi\sqrt{L C_{\mathrm{eq}}}}.
$$

### Amplitude control

As $|A_v\beta|>1$ grows the signal, the BJT limits the swing by entering saturation near the negative peak and cutoff near the positive peak. At the bottom,

$$
V_{CE}\approx V_{CE(\mathrm{sat})}
$$

so **bottom clipping** reduces effective loop gain toward unity and stabilizes amplitude.

### Inductive kickback and overshoot

When the transistor cuts off, inductor current cannot change instantly. The tank drives current through $R_C$, producing a positive overshoot:

$$
V_{\mathrm{KICK}}\approx L\frac{di}{dt},\qquad
V_{C,\max}\lesssim V_{CC}+V_{\mathrm{KICK}}.
$$

### Why the output baseline appears low

$V_{\mathrm{out}}$ is taken **after** the coupling capacitor $C_{\mathrm{out}}$. True DC is blocked so the node is $0\ \text{V}$ in simulation. In hardware the LC tank, probe capacitance, and return path form a finite-impedance AC network; the required RMS current through that network creates an average drop that **visually** shifts the plotted baseline toward $V_E$ even though the actual DC is near $0\ \text{V}$.

---

## Simulation Results

> **Steady-state output: frequency**  
> ![waveform freq](./waveform_freq.png)

> **Steady-state output: $V_{\mathrm{out,pp}}$**  
> ![waveform vpp](./waveform_vpp.png)

> **Supply current and DC power (sim)**  
> ![sim current and power](./sim_current_power.png)

---

## Measured vs Simulated Summary

| Parameter | Simulated | Observed | Change [+/−] | Error [%] |
|:--|--:|--:|--:|--:|
| Frequency [MHz] | 11.798 | 7.41 | −4.388 | 37.19 |
| $V_{\mathrm{out,pp}}$ [V] | 10.2 | 8.5 | −1.7 | 16.67 |
| Current [mA] | 2.295 | 4.045 | +1.75 | 76.25 |
| Power [mW] | 22.77 | 40.69 | +17.92 | 78.69 |
| FoM [dB] | 91.98 | 83.87 | −8.11 | 8.82 |

---

## Why lab differs from simulation

### 1) Frequency is lower

**Cause:** added capacitance raises $C_{\mathrm{eq}}$ and reduces $f_0$.

$$
f_0=\frac{1}{2\pi}\sqrt{\frac{1}{L\,\left(\dfrac{(C_1\parallel C_{\mathrm{par}})(C_2\parallel C_{\mathrm{par}})}{C_1\parallel C_{\mathrm{par}}+C_2\parallel C_{\mathrm{par}}}\right)}}
$$

**Sources:** scope probe 10× tip $(\approx 10\text{–}15\,\text{pF})$, BJT $(C_{\mu},\,C_{\pi})$, wiring, breadboard.

### 2) Output $V_{\mathrm{out,pp}}$ is lower

**Cause:** probe and wiring load the tank and amplifier.

At MHz, the probe capacitance has low reactance

$$
X_C=\frac{1}{2\pi f C},
$$

which shunts signal to ground, reducing effective $A_v$. Oscillation settles when $|A_v\beta|\approx 1$, so a lower $A_v$ yields a smaller $V_{\mathrm{out,pp}}$.

### 3) Power is higher

**Cause:** parasitic resistance dissipates energy each cycle. Tank ESR, wiring, and probe returns convert circulating RF current into heat; the transistor must replace this energy, raising average current $I_{\mathrm{avg}}$:

$$
P_{\mathrm{DC}}=V_{CC}\,I_{\mathrm{avg}}.
$$

---

## Design change to reduce clipping and drive

**Adjustment:** decrease $R_C$ to increase headroom and lower gain.

- $R_C: 2.2\ \text{k}\Omega \rightarrow 1\ \text{k}\Omega$
- $V_C\uparrow \Rightarrow V_{CE}\uparrow$ so more downward headroom before saturation
- $A_v\downarrow$ so $|A_v\beta|$ is closer to 1 at steady state

**Result (simulation):** $f\approx 13.827\ \text{MHz}$, $V_{pp}\approx 8.654\ \text{V}$, $P\approx 28.9\ \text{mW}$, FoM $\approx 90.9\ \text{dB}$; clipping is mitigated and the waveform is cleaner.

---

## Reproduction steps

1. **Open** `colpitts.asc`.

2. **Bias and startup transient**
   `spice
   .tran 0 15m startup
   `

Probe $V_C$, $V_E$, and $V_{\mathrm{out}}$. Verify the bottom flat near $V_{CE(\mathrm{sat})}$ and the positive overshoot above $V_{CC}$.

3. Frequency and amplitude  
Measure period $T$ and compute

$$
f_0=\frac{1}{T}
$$

Read $V_{\mathrm{out,pp}}$ from the waveform.

4. Power  
Plot supply current and compute

$$
P_{DC}=V_{CC}\,I_{\text{avg}}
$$

---

## Files

- `colpitts.asc` — LTspice schematic  
- `Schematic.png` — Circuit diagram  
- `breadboard_annotated.png` — Hardware photo with notes  
- `waveform_freq.png` — Steady-state frequency measurement  
- `waveform_vpp.png` — Steady-state amplitude  
- `sim_current_power.png` — Average current and DC power
