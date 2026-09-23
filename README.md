# CMOS Differential Amplifier with Active Current-Mirror Load

LTSpice design, simulation, and characterization of a 5-transistor CMOS OTA (NMOS differential pair with a PMOS current-mirror active load) — measuring differential gain, small-signal bandwidth, and linear input range.

## Overview

This project implements the classic 5-transistor operational transconductance amplifier (OTA): an NMOS current-mirror bias network sets the tail current for an NMOS differential pair, whose output is converted from differential to single-ended by a PMOS current-mirror active load. The design was simulated and characterized entirely in LTSpice using SPICE netlists (no schematic capture).

## Circuit Topology

- **Supply voltage:** 1.8 V
- **Bias network:** diode-connected NMOS reference mirrored into the differential pair's tail current source (~100 µA reference)
- **Differential pair:** NMOS input pair (M1, M2)
- **Active load:** PMOS current mirror (M5, M6), converting the differential signal to a single-ended output at node `outn`
- **Load capacitance:** 1 pF at the output node, used to characterize bandwidth

Transistor models are generic SPICE LEVEL=1 parameters (illustrative, 0.18 µm-scale) rather than a specific foundry PDK — the goal of this project was to demonstrate topology understanding and circuit-analysis methodology, not to hit fab-calibrated numbers.

**Functional block diagram** (signal/current flow between stages — not a transistor-level schematic):

![Block diagram](https://raw.githubusercontent.com/shyamch06/CMOS_DiffAmp_CurrentMirror_Load/main/Diagram/topology_block_diagram.jpeg)

## Files

| File | Purpose |
|---|---|
| `diffamp_AC.cir` | AC analysis — frequency response, differential gain, bandwidth |
| `diffamp_DC.cir` | DC sweep — voltage transfer characteristic, linear input range |
| `Diagram/topology_block_diagram.jpeg` | Functional block diagram of the circuit stages |
| `Results/ac_gain_plot.jpeg` | Screenshot of the AC gain/frequency response |
| `Results/dc_transfer_curve.jpeg` | Screenshot of the DC transfer curve with cursor measurements |

## How to Run

1. Open [LTSpice](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html) (free).
2. `File → Open`, set file type to "All Files", select `diffamp_AC.cir` or `diffamp_DC.cir`.
3. Click **Run**.
4. For the AC file: right-click the empty plot → **Add Trace** → type `db(v(outn))`.
5. For the DC file: the plot of `V(outn)` vs. `Vin1` appears automatically.
6. Click on the trace and use LTSpice's cursors (hold Ctrl for a second cursor) to read exact gain, frequency, and voltage values.

## Results

| Parameter | Measured Value |
|---|---|
| Low-frequency differential gain | **34 dB** |
| -3 dB bandwidth | **2.92 MHz** |
| Linear input range (around 0.9 V common-mode) | **~12.3 mV** |
| Large-signal transition slope (DC sweep) | **~98 V/V (≈39.8 dB)** |

**AC gain / frequency response** (cursor marks the -3 dB point at 2.92 MHz):

![AC gain plot](https://raw.githubusercontent.com/shyamch06/CMOS_DiffAmp_CurrentMirror_Load/main/Results/ac_gain_plot.jpeg)

**DC transfer characteristic** (cursors mark the edges of the linear input range):

![DC transfer curve](https://raw.githubusercontent.com/shyamch06/CMOS_DiffAmp_CurrentMirror_Load/main/Results/dc_transfer_curve.jpeg)

The large-signal slope from the DC sweep (≈39.8 dB) is reasonably consistent with the small-signal AC gain (34 dB) — the two are expected to differ somewhat since one is an incremental slope through the transition region and the other is a fixed-bias-point AC measurement.

## Analysis & Key Takeaways

- **The DC transfer curve is steep** (a ~12 mV input range spans nearly the full output swing), which is expected for an *open-loop* high-gain stage — it behaves close to a comparator. This is exactly why real op-amp circuits are almost always operated inside a feedback loop rather than open-loop.
- **A secondary pole-zero doublet** appears in the AC response in the 100 MHz–1 GHz range, attributable to parasitic capacitance at the current-mirror node.
- **Bandwidth is set by the load capacitance (1 pF) and the output resistance** at the `outn` node (approximately $r_{o2} \parallel r_{o6}$, the parallel combination of the NMOS and PMOS output resistances at that node) — this forms the amplifier's dominant pole.

## Future Work

- Measure CMRR via a common-mode AC sweep (tie both inputs together)
- Extend to a two-stage op-amp with Miller compensation and characterize phase margin
- Explore transistor sizing trade-offs (gain vs. bandwidth vs. output swing)

## Author

Cherukuri Shyam Sundhar 

B.Tech Electronics and Communication Engineering

IIT Bhubaneswar
