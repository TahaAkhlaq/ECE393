# Project 2: Colpitts Oscillator

A Colpitts LC oscillator using a 2N2222 common-emitter stage with capacitive-divider feedback. Designed at \(V_{CC}=10\ \text{V}\). Bias is set near mid-supply for headroom. Startup is ensured by \(|A_v\beta|>1\) around the tank resonance.

> **Schematic**  
> ![Schematic](./Schematic.png)

> **Breadboard (annotated)**  
> ![Breadboard](./breadboard_annotated.png)

---

## Theory of Operation

### Colpitts feedback and loop gain
The capacitive divider \(C_1\)–\(C_2\) returns a fraction of the collector swing to the emitter:
\[
\beta \approx \frac{C_1}{C_2}\qquad\text{for } C_2 \gg C_1
\]
Required midband voltage gain for startup:
\[
|A_v| \gtrsim \frac{1}{|\beta|} \approx \frac{C_2}{C_1}
\]
With the simulated values \(C_1=10\ \text{pF}\), \(C_2=100\ \text{pF}\Rightarrow \beta\approx 0.1\), so \(|A_v|\gtrsim 10\).

### Resonant frequency
The tank is \(L\) in series with \(C_1\) and \(C_2\) forming an equivalent
\[
C_{\text{eq}}=\frac{C_1C_2}{C_1+C_2},\qquad
f_0=\frac{1}{2\pi\sqrt{LC_{\text{eq}}}}
\]

### Amplitude control in practice
As the loop builds, the transistor’s nonlinearity limits growth by pushing the stage into **saturation** near the negative peak and into **cutoff** near the positive peak. At the negative peak the collector–emitter voltage hits
\[
V_{CE}\approx V_{CE(\text{sat})}
\]
which produces bottom clipping. This reduces the effective loop gain toward unity and arrests growth.

### Inductive kickback and overshoot
When the transistor abruptly enters cutoff, the tank current cannot change instantaneously. The inductor forces current through \(R_C\), generating a positive overshoot at the collector:
\[
V_{\text{KICK}}\approx L\frac{di}{dt}
\]
The peak collector voltage is therefore
\[
V_{C,\text{max}} \lesssim V_{CC}+V_{\text{KICK}}
\]
which explains measured peaks that exceed the DC rail without requiring supply violations.

### Why the output node “centers low”
The plot for \(V_{\text{out}}\) is taken **after** the coupling capacitor \(C_{\text{out}}\). DC is blocked, so the true DC of that node is \(0\ \text{V}\) in simulation. In hardware, the apparent midpoint sits near \(V_E\) because the LC tank, probe capacitance, and return path form an AC network with finite impedance. The RMS current required through that network produces an average drop that visually shifts the waveform baseline downward, even though the true DC at the node is effectively \(0\ \text{V}\) in simulation.

---

## Simulation Results

> **Steady-state output: frequency**  
> ![Frequency](./waveform_freq.png)

> **Steady-state output: peak-to-peak amplitude**  
> ![Vpp](./waveform_vpp.png)

> **Average current and DC power**  
> ![Current and Power](./sim_current_power.png)

---

## Measured vs Simulated Summary

| Parameter | Simulated | Observed | Change [+/-] | Error [%] |
|:--|--:|--:|--:|--:|
| Frequency [MHz] | 11.798 | 7.41 | −4.388 | 37.19 |
| \(V_{\text{out,pp}}\) [V] | 10.2 | 8.5 | −1.7 | 16.67 |
| Current [mA] | 2.295 | 4.045 | +1.75 | 76.25 |
| Power [mW] | 22.77 | 40.69 | +17.92 | 78.69 |
| FoM [dB] | 91.98 | 83.87 | −8.11 | 8.82 |

---

## Why the lab results differ from simulation

### 1) Frequency is lower \((7.41\ \text{MHz} \text{ vs } 11.8\ \text{MHz})\)
**Cause:** extra capacitance raises \(C_{\text{eq}}\Rightarrow f_0\downarrow\).  
**Sources:** scope probe \(10\times\) tip capacitance \(10\text{–}15\ \text{pF}\), BJT \(C_{\mu},C_{\pi}\), wiring, breadboard.  
**Mechanism:** 
\[
f_0=\frac{1}{2\pi\sqrt{L\,(C_1\parallel C_{\text{par}})\,(C_2\parallel C_{\text{par}})/(C_1\parallel C_{\text{par}}+C_2\parallel C_{\text{par}})}}
\]
Any added \(C_{\text{par}}\) lowers \(f_0\).

### 2) \(V_{\text{out,pp}}\) is lower \((8.5\ \text{V} \text{ vs } 10.2\ \text{V})\)
**Cause:** probe and wiring load the tank and amplifier.  
**Mechanism:** at MHz, probe capacitance has low reactance \(X_C=\frac{1}{2\pi f C}\) that shunts signal to ground, reducing effective \(|A_v|\). Oscillation settles when \(|A_v\beta|\approx 1\), so a lower \(|A_v|\) yields a smaller final amplitude.

### 3) Power is higher \((40.7\ \text{mW} \text{ vs } 22.8\ \text{mW})\)
**Cause:** parasitic resistance dissipates energy every cycle.  
**Mechanism:** ESR in the tank, wiring, and probe returns convert circulating RF current into heat. The transistor must supply this lost energy, increasing average collector current \(I_{\text{avg}}\), so
\[
P_{DC}=V_{CC}\cdot I_{\text{avg}}
\]
increases.

---

## Saturation analysis on the collector waveform

- **Headroom at the negative peak:**  
  \(V_C\downarrow \Rightarrow V_{CE}\downarrow \Rightarrow V_{CE}\) approaches \(V_{CE(\text{sat})}\). Bottom flattens, harmonic content appears, and \(|A_v\beta|\to 1\).

- **Exit from saturation:**  
  As \(I_B\downarrow, I_C\downarrow\). The drop on \(R_C\) eases, \(V_C\uparrow\). The device re-enters the forward active region.

- **Overshoot above \(V_{CC}\):**  
  When \(I_C\to 0\) at cutoff, inductor current reroutes through \(R_C\), producing
  \[
  V_{\text{KICK}}\approx L\,\frac{di}{dt}
  \]
  and a transient \(V_C>V_{CC}\).

---

## Design change to relieve clipping and reduce drive

**Adjustment:** decrease \(R_C\) to increase collector headroom and lower gain.

- \(R_C:\ 2.2\ \text{k}\Omega \rightarrow 1\ \text{k}\Omega\)  
- **Effect on bias:** \(V_C\uparrow\Rightarrow V_{CE}\uparrow\) so more downward headroom before saturation.  
- **Effect on loop gain:** \(|A_v|\downarrow\) so \(|A_v\beta|\) is closer to 1 at steady state.  
- **Result (simulation):** \(f\approx 13.827\ \text{MHz}\), \(V_{pp}\approx 8.654\ \text{V}\), power \(\approx 28.9\ \text{mW}\), FoM \(\approx 90.9\ \text{dB}\). Clipping is mitigated and the waveform is cleaner while maintaining robust startup.

---

## Reproduction steps

1. **Open** `colpitts.asc`.

2. **Bias and startup transient**  
   ```text
   .tran 0 15m startup
   ```

Probe \(V_C\), \(V_E\), and \(V_{\text{out}}\). Verify the bottom flat near \(V_{CE(\text{sat})}\) and the positive overshoot relative to \(V_{CC}\).

3. **Frequency and amplitude**  
Measure period \(T\) from `waveform_freq.png` or the live waveform and compute \(f_0=1/T\).  
Read \(V_{\text{out,pp}}\) as in `waveform_vpp.png`.

4. **Power**  
Plot supply current and compute \(P_{DC}=V_{CC}\cdot I_{\text{avg}}\) as in `sim_current_power.png`.

5. **Variant with headroom fix**  
Change \(R_C\rightarrow 1\ \text{k}\Omega\). Re-run transient. Confirm cleaner tops and bottoms and reduced drive.

---

## Files

- `colpitts.asc` — LTspice schematic  
- `Schematic.png` — Circuit diagram  
- `breadboard_annotated.png` — Hardware photo with notes  
- `waveform_freq.png` — Steady-state frequency measurement  
- `waveform_vpp.png` — Steady-state amplitude  
- `sim_current_power.png` — Average current and DC power
