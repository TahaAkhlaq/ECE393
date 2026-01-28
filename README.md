# ECE393 - Junior Electrical Engineering Projects I

This repository contains coursework and projects from **ECE393: Junior Electrical Engineering Projects I** at [The Cooper Union](https://cooper.edu/engineering/courses/electrical-and-computer-engineering-undergraduate/ece-393), instructed by **Professor Koo**.

---

## Course Overview

ECE393 is an upper-level, lab-intensive design course that builds upon the foundation of **analog electronics and circuit analysis** introduced in earlier courses. It focuses on developing the ability to design, implement, and characterize complete analog systems while reinforcing theoretical concepts through practical experimentation.

Students progress from analyzing individual amplifier stages to **integrating multiple subcircuits** into functional analog systems such as oscillators, active filters, and feedback amplifiers. The course emphasizes iterative design, bias optimization, gain stabilization, and feedback theory, combining analytical problem-solving with simulation and hardware verification.

Key learning experiences include:

* **Practical Circuit Design** – Applying biasing, small-signal models, and frequency response analysis to create working circuits.
* **Simulation and Verification** – Using CAD tools such as **LTspice**, **Cadence Virtuoso**, and **Spectre** to verify theoretical predictions and tune real-world performance.
* **Team-Based Collaboration** – Working in design teams to troubleshoot, document, and present circuit results with engineering precision.
* **Professional Communication** – Writing detailed lab-style documentation and giving technical presentations to communicate design intent and results clearly.

---

## Learning Objectives

* Design, simulate, and analyze analog electronic subsystems such as **amplifiers, oscillators, and active filters** using BJTs and MOSFETs.
* Apply **Barkhausen criteria**, feedback theory, and small-signal analysis to achieve stable and sustained oscillations.
* Evaluate and interpret **gain, phase, bandwidth, and distortion** in both simulated and experimental environments.
* Balance design trade-offs between **power consumption, frequency response, and linearity** for robust circuit operation.
* Use **CAD tools** to simulate, validate, and refine analog designs prior to implementation.
* Collaborate effectively in engineering teams to complete design projects, reports, and presentations.
* Develop technical writing and communication skills for professional reporting and documentation.

---

## Projects

### [Project 1: RC Phase-Shift Oscillator](./Project%201%20-%20RC%20Phase%20Shift%20Oscillator/ "Read More")

A single-transistor BJT **common-emitter** oscillator using a three-stage RC ladder network. The design demonstrates the **Barkhausen criteria** and phase-shift feedback required for sustained sinusoidal oscillation.

> **Schematic**
![RC Oscillator Schematic](./Project%201%20-%20RC%20Phase%20Shift%20Oscillator/schematic.png)

### [Project 2: Colpitts Oscillator](./Project%202%20-%20Colpitts%20Oscillator/ "Read More")

A Colpitts LC oscillator using a 2N2222 common-emitter stage with capacitive divider feedback.
Designed at $V_{CC}=10\,\mathrm{V}$; bias near mid-supply; startup ensured by $\lvert A_v\beta\rvert>1$.

> **Schematic**  
![Colpitts Schematic](./Project%202%20-%20Colpitts%20Oscillator/Schematic.png)

### [Project 3: Active Mixer](./Project%203%20-%20Active%20Mixer/ "Read More")

An active downconversion chain combining a BJT LC local oscillator, an op-amp differential LO phase splitter, an NMOS commutating mixer that steers the RF-driven tail current between load resistors, and a differential amplifier plus RC low-pass filtering to recover the baseband output.

> **Schematic**  
![Active Mixer Schematic](./Project%203%20-%20Active%20Mixer/schematic.png)

---

## Copyright & Licensing

Copyright (C) 2025 **Taha Akhlaq** — [takhlaq04@gmail.com](mailto:takhlaq04@gmail.com)
Distributed under the MIT License. See `LICENSE` for details.

For more information on my academic and technical projects, please visit my [GitHub profile](https://github.com/TahaAkhlaq).
