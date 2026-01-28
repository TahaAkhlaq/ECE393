# Project 3: Active Mixer

Discrete active downconversion chain consisting of an LC BJT local oscillator, a differential LO phase-splitting stage, an NMOS commutating mixer core, and a differential post-amplifier with low-pass filtering to recover the downconverted baseband component.

> **Schematic**  
> ![Schematic](./Schematic.png)

> **Circuit diagram**  
> ![Circuit diagram](./Image%20of%20Circuit.png)

---

## Theory of Operation

### Local oscillator
The local oscillator is a BJT common-emitter stage with an LC tank that sets the oscillation frequency. In the ideal lossless approximation,

$$
f_{LO}\approx \frac{1}{2\pi\sqrt{L\,C_{\mathrm{eq}}}}.
$$

The oscillator starts when loop gain exceeds unity and reaches steady state when device nonlinearity reduces the effective loop gain toward 1.

### Differential LO phase splitter
The LO is converted into complementary differential drives, $LO^+$ and $LO^-$. The intended relationship is equal amplitude with approximately $180^\circ$ separation:

$$
LO^-(t)\approx -LO^+(t),
\qquad
\angle LO^- - \angle LO^+\approx 180^\circ.
$$

These complementary waveforms improve commutation symmetry in the switching core and reduce sensitivity to common-mode feedthrough.

### NMOS commutating mixer core
The mixer core operates as a commutating multiplier: the RF-driven device current is steered by the LO between the two load branches, producing frequency translation. The dominant product is the difference term

$$
f_{IF}=|f_{RF}-f_{LO}|.
$$

Because the commutation is effectively a square-wave multiplication, additional components occur at

$$
|f_{RF}\pm k f_{LO}|,
\quad
k=1,2,3,\dots
$$

and at harmonics of the LO.

### Differential amplification and low-pass filtering
The differential amplifier converts the switching core output into a usable differential baseband signal and provides gain. The low-pass filter suppresses residual $f_{RF}$ content, LO feedthrough, and higher-order mixing products so the baseband component dominates at the output.

For a first-order low-pass stage, the corner frequency is approximated by

$$
f_c\approx \frac{1}{2\pi R C}.
$$

---

## Simulation Results

### Local oscillator steady-state behavior
The following plots characterize steady-state LO amplitude and frequency.

> **LO waveform summary: frequency and amplitude**  
> ![LO waveform summary](./LO%20Waveforms%20%E2%80%93%20Frequency%2C%20Amplitude.png)

> **LO output: frequency measurement**  
> ![LO output frequency](./LO%20Output%20with%20Frequency.png)

> **LO output: peak-to-peak amplitude**  
> ![LO output peak to peak](./LO%20Output%20with%20Peak%20to%20Peak.png)

### Differential LO validation
The phase splitter is validated by confirming complementary LO waveforms and the intended phase separation.

> **Differential LO waveforms**  
> ![Differential LO waveforms](./Differential%20LO%20Waveforms.png)

> **LO phase difference**  
> ![LO phase difference](./LO%20Waveforms%20%E2%80%93%20Phase%20Difference.png)

### Output spectrum and filtering
FFT plots show the translated component and mixing products. The low-pass filter attenuates higher-frequency content while preserving the downconverted component.

> **FFT output without low-pass filter**  
> ![FFT without LPF](./FFT%20Output%20Without%20Low%20Pass%20Filter.png)

> **FFT output with low-pass filter**  
> ![FFT with LPF](./FFT%20Output%20With%20Low%20Pass%20Filter.png)

### Time-domain comparison of filtered vs unfiltered output
Direct time-domain comparison of the output before and after low-pass filtering.

> **Output comparison: filtered vs unfiltered**  
> ![Output comparison](./Output%20with%20Low%20Pass%20Filter%20%28green%29%20and%20without%20%28blue%29.png)
