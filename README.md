# ESE 123 Lab 5: Capacitors & the Oscilloscope

**Interactive page:** https://cad281.github.io/ese123-lab5/

All labs: [⌂ Home](https://cad281.github.io/ese123-fall2026/) · [Lab 1: DC Power & Ohm's Law](https://cad281.github.io/ese123-lab1/) · [Lab 2: Digital Multimeter](https://cad281.github.io/ese123-lab2/) · [Lab 3: SPICE & Soldering](https://cad281.github.io/ese123-lab3/) · [Lab 4: Function Generator & Scope](https://cad281.github.io/ese123-lab4/) · [Lab 5: Capacitors & Scope](https://cad281.github.io/ese123-lab5/) · [Lab 6: Operational Amplifiers](https://cad281.github.io/ese123-lab6/) · [Lab 7: Assembly Language & Buzzer](https://cad281.github.io/ese123-lab7/)

A TA-written guide for ESE 123 students: **what** you do in Lab 5, **why** it matters and **how** to do it. The interactive page has a live two-channel scope simulation of the RC filter, animated cursors and measurements, current flowing through the capacitor branch, and part cards for the board build.

---

## What this lab is about

| You will… | Using… |
|---|---|
| Measure Vpp, period and frequency **three ways**: counting boxes, **cursors**, **automatic measurements** | DSO-X 3012A |
| Learn why **N-Cycle** measurements beat full-screen ones | Avg-FS vs Avg-Cyc, AC RMS – N Cycles |
| Measure the **frequency response** of an RC filter from 3 kHz to 1 Hz: amplitude and **phase** | Function generator + 2 scope channels |
| Set the scope **manually** when Autoscale stops working | V/div, time/div |
| Solder the filter, the power-supply **bypass capacitors**, ferrite bead and a **voltage divider** | Project PCB |
| Power the board and measure **AVDD** and **TP2** | Programming fixture + DMM |

## Why it matters

- **Capacitors make circuits frequency-dependent.** Their reactance X_C = 1/(2πfC) falls as frequency rises, so a capacitor blocks DC and slow signals but passes fast ones. That is the basis of every filter.
- **Cursors and automatic measurements** are faster and more precise than counting boxes, but only if you know what they measure and when they can be fooled.
- **Phase** (one wave arriving earlier than another) is something only a scope can show you.
- **Bypass capacitors** keep the microcontroller's supply steady when it draws current in bursts.

## How to do it, step by step

### Part 1: three ways to measure (steps 1–8.7)
1. Plugboard: **R1 = 150 kΩ** (brown-green-black-orange-brown) from `top` to `mid`, in parallel with **C1 = 100 nF** ("104"); **R2 = 4.75 kΩ** (yellow-violet-green-brown-brown) from `mid` to ground. Use the precision 1% parts from your TA and don't kink the leads (they go on your PCB later).
2. Generator: **1 V rms sine, 900 Hz**, output on. **CH1** probe on `top`, ground clip on ground. **Autoscale.**
3. **Count boxes**: Vpp ≈ 5.6 boxes at 1 V/div; period ≈ 5.6 boxes at 200 µs/div → f = 1/T.
4. **Cursors** key → Mode **Manual** → Source **CH1**. Y1 to the valley, Y2 to the peak → **ΔY = Vpp ≈ 5.66 V**. X1 on a rising zero crossing, X2 one period later → **ΔX ≈ 1.111 ms**, **1/ΔX ≈ 900 Hz**.
5. **Meas** → Clear Meas → **Clear All**. Source CH1. Add **Peak-Peak**, **Period**, **Frequency**, and **AC RMS – N Cycles** (≈ **2 V rms**, twice the setting; see Lab 4).

### Part 2: frequency response (steps 9–21)
1. **CH2** on `mid`. Autoscale. Keep only CH1 AC RMS, add **CH2 AC RMS – N Cycles**.
2. **Phase**: Type = Phase, Settings → Source1 = CH1, Source2 = CH2, back, Add Measurement.
3. Record CH2 RMS and phase at **3 kHz, 1 kHz, 300 Hz, 100 Hz, 30 Hz, 10 Hz, 3 Hz, 1 Hz** (900 Hz you already have).
4. Adjust time/div to show a few cycles at each frequency. **Below ~20 Hz Autoscale fails**: set time/div ≈ (3 × period)/10 and shrink CH2's V/div as V_mid gets small. At 1 Hz each screen takes seconds to draw; wait for at least one full cycle.

### Part 3: board build (steps 22–35)
1. Tear down the plugboard; trim kinked leads.
2. **Op-amp filter:** C7 = 100 nF (104), R12 = 150 kΩ, R13 = 4.75 kΩ. Solder, trim.
3. **Power supply filter:** C6 = 100 nF (104), C1 = 10 µF (106), **C2 = 470 µF polymer can: polarised, long lead in the "+" hole**, blue mark toward the board edge; L1 ferrite bead (either way). Solder, trim.
4. **Voltage divider:** R6 = 59 kΩ (green-white-black-red-brown), R7 = 150 kΩ, C9 = 1 nF (102). Solder, trim.

### Part 4: power-up check (steps 36–44)
1. Board on the programming fixture (text right side up, steel pins through the holes, J2 mated). Adapter in, switch right side down; LED lights.
2. DMM DC volts: **AVDD ≈ 5 V ± 0.25 V**, then **TP2**. Switch off, remove the board, unplug.

## What you should observe, and why

| Observation | Why it happens |
|---|---|
| Counting boxes gives ~5.6 V, cursors/auto give 5.66 V | Your eye can only read about ±0.2 box. |
| AC RMS reads ~2 V for a 1 V setting | The generator assumes a 50 Ω load; the circuit is ~155 kΩ. |
| Avg-FS isn't zero but Avg-Cyc is | Full-screen includes a partial cycle; N-Cycle uses whole cycles only. |
| At 3 kHz V_mid ≈ V_top, phase ≈ 0° | X_C ≈ 530 Ω ≪ 150 kΩ, so C1 shorts R1. |
| At 1 Hz V_mid ≈ 61 mV | X_C ≈ 1.6 MΩ, so C1 is nearly open: just the R1/R2 divider. |
| Phase bump, largest around 60 Hz (~70°) | Capacitor current leads its voltage by 90°; when C1 carries part of the current, `mid` leads `top`. |
| Scope phase readout is negative | Scope convention per the handout: negative = channel 2 arrives earlier. |
| Autoscale fails below ~20 Hz, scope is sluggish at 1 Hz | Too few edges per second to lock onto; a 3-cycle screen at 1 Hz takes 3 s to draw. |

## Formula sheet (what, when, example)

### Capacitive reactance

```
XC = 1 / (2π f C)
```

**Use it when:** Finding how much a capacitor “resists” AC at a given frequency. Small at high f (like a wire), huge at low f (like an open switch).

- `f`: frequency (Hz)
- `C`: capacitance (F)

**Example:** 100 nF at 3 kHz → 1/(2π·3000·100n) = 531 Ω;  at 1 Hz → 1.59 MΩ.

### Time constant

```
τ = R · C
```

**Use it when:** Judging how fast an RC circuit charges. Compare τ with the period T of the signal (pre-lab).

**Example:** 1 kΩ × 1 µF = 1 ms. T = 100 ms ≫ τ → VC follows the input.

### Corner (crossover) frequency

```
fc = 1 / (2π R C)
```

**Use it when:** The frequency where XC = R: the boundary between “capacitor acts open” and “capacitor acts like a wire”.

**Example:** 150 kΩ, 100 nF → 10.6 Hz. Same parts give the ~10 Hz op-amp filter in Lab 6.

### Low-frequency limit of the lab filter

```
Vmid = Vtop · R2 / (R1 + R2)
```

**Use it when:** Very low f, when C1 is effectively open: just a resistor divider.

**Example:** 2 V × 4.75k / 154.75k = 61 mV rms.

### High-frequency limit of the lab filter

```
Vmid ≈ Vtop
```

**Use it when:** High f, when C1 shorts out R1.

**Example:** At 3 kHz Vmid ≈ 1.99 V rms of 2 V.

### Full transfer function of the lab filter

```
Vmid/Vtop = R2 / (R2 + Z1),  Z1 = R1 / (1 + j2πf R1 C1)
```

**Use it when:** Predicting Vmid and phase at any frequency (the theory table on the page).

**Example:** At 900 Hz: |H| = 0.934 → 1.87 V rms, mid leads by 20°.

### RMS, peak and peak-to-peak (sine)

```
Vrms = Vp/√2,   Vpp = 2Vp = 2√2 Vrms
```

**Use it when:** Converting between the generator setting, cursor ΔY and the AC RMS measurement.

**Example:** 2 V rms → 2.83 V peak → 5.66 Vpp.

### Generator into a high-impedance load

```
Vactual ≈ 2 × Vset
```

**Use it when:** Explaining why 1 V rms set reads ~2 V rms on the scope (50 Ω assumption, see Lab 4).

**Example:** Set 1 V rms → scope AC RMS ≈ 2.0 V.

### Frequency from period

```
f = 1 / T  (scope: 1/ΔX)
```

**Use it when:** Counting boxes or using X cursors to get the period, then frequency.

**Example:** ΔX = 1.111 ms → 900 Hz.

### Phase from a time shift

```
φ = 360° × Δt / T
```

**Use it when:** Estimating phase by hand from the time between matching zero crossings of CH1 and CH2.

**Example:** Δt = 62 µs at 900 Hz (T = 1.111 ms) → 20°.

### Choosing time/div manually

```
time/div ≈ (cycles wanted × T) / 10
```

**Use it when:** Low frequencies where Autoscale fails (below ~20 Hz).

**Example:** 3 cycles of 3 Hz → 3 × 0.333 s / 10 ≈ 100 ms/div.

### Capacitor code

```
C = (first two digits) × 10^(third digit) pF
```

**Use it when:** Identifying ceramic capacitors before soldering.

**Example:** 104 → 10 × 10⁴ pF = 100 nF;  106 → 10 µF;  102 → 1 nF.

### Voltage divider (TP2)

```
VTP2 = AVDD · Rbottom / (Rtop + Rbottom)
AVDDmax = 4.3 V × (Rtop + Rbottom) / Rbottom
```

**Use it when:** Question 4. Check the schematic for which of R6/R7 is on the AVDD side.

**Example:** R6 59k on top, R7 150k bottom: 5.00 V → 3.589 V; AVDDmax = 5.99 V.

### Percent error

```
(measured − calculated) / calculated × 100%
```

**Use it when:** Question 4b and every theory-vs-measurement comparison.

**Example:** 3.57 V measured vs 3.589 V → −0.5%.


## Practice questions

Same concepts and formulas as the Brightspace quiz, with new numbers (not the real quiz questions). Try each one before opening the answer.

**Q1.** What is the reactance of a 220 nF capacitor at 500 Hz?

<details><summary>Show answer</summary>

**1.45 kΩ**. XC = 1/(2π × 500 × 220×10⁻⁹) = 1/(6.91×10⁻⁴) = 1447 Ω.

</details>

**Q2.** At what frequency does a 47 nF capacitor have XC = 10 kΩ?

<details><summary>Show answer</summary>

**339 Hz**. f = 1/(2π XC C) = 1/(2π × 10⁴ × 47×10⁻⁹) = 339 Hz.

</details>

**Q3.** What is the time constant of 4.7 kΩ and 2.2 µF?

<details><summary>Show answer</summary>

**10.3 ms**. τ = RC = 4700 × 2.2×10⁻⁶ = 0.01034 s.

</details>

**Q4.** CH2 crosses zero 0.25 ms before CH1 on a 500 Hz signal. What is the phase difference?

<details><summary>Show answer</summary>

**45° (CH2 leads)**. T = 1/500 = 2 ms. φ = 360° × 0.25/2 = 45°.

</details>

**Q5.** The scope reads AC RMS = 1.5 V for a sine. What Vpp should the cursors show?

<details><summary>Show answer</summary>

**4.24 V**. Vpp = 2√2 × Vrms = 2.828 × 1.5 = 4.24 V.

</details>

**Q6.** The lab filter is rebuilt with R1 = 100 kΩ, R2 = 10 kΩ, C1 = 100 nF and 2 V rms at top. What is Vmid at very low frequency, and where is the R1–C1 crossover?

<details><summary>Show answer</summary>

**0.182 V rms; 15.9 Hz**. Low f: C1 open → 2 × 10k/110k = 0.182 V. Crossover where XC = R1: f = 1/(2π × 100k × 100n) = 15.9 Hz.

</details>

**Q7.** Autoscale fails on a 5 Hz signal. You want about 4 cycles on screen. What time/div?

<details><summary>Show answer</summary>

**100 ms/div (80 ms minimum)**. 4 × 0.2 s / 10 divisions = 80 ms/div. Nearest knob setting at or above that: 100 ms/div.

</details>

**Q8.** Decode the capacitor markings 473 and 225.

<details><summary>Show answer</summary>

**47 nF and 2.2 µF**. 473 = 47 × 10³ pF = 47 000 pF = 47 nF. 225 = 22 × 10⁵ pF = 2.2 µF.

</details>

**Q9.** A divider has 100 kΩ on top and 47 kΩ on the bottom. With AVDD = 5.1 V, what is TP? What AVDD would make TP = 4.3 V?

<details><summary>Show answer</summary>

**1.63 V; 13.4 V**. TP = 5.1 × 47/147 = 1.631 V. AVDD = 4.3 × 147/47 = 13.45 V.

</details>


## Report guide (Google Sheets → PDF)

1. Table: Vpp, period, frequency × (counting boxes, cursors, automatic).
2. Plot **V_mid rms vs frequency** with a **log** frequency axis.
3. Plot **phase vs frequency** with a log frequency axis (record the sign as the scope shows it).
4. Divider: (a) expected TP2 from your measured AVDD, (b) % error vs measured TP2, (c) AVDD that would make TP2 = 4.3 V.

---
*Made for ESE 123 students by a TA. Theory values assume ideal parts; real parts are ±1% (resistors) and up to ±10% (capacitors).*
