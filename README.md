# ⚡ 555 Timer — Variable-Frequency Astable Oscillator

![NE555 Timer](https://img.shields.io/badge/IC-NE555_Timer-7F77DD?style=flat-square)
![Mode](https://img.shields.io/badge/Mode-Astable-E67E22?style=flat-square)
![Frequency](https://img.shields.io/badge/Frequency-Variable-1D9E75?style=flat-square)
![Supply](https://img.shields.io/badge/Supply-6V_DC-185FA5?style=flat-square)
![University](https://img.shields.io/badge/Benha_University-Shoubra_Faculty-534AB7?style=flat-square)

> A free-running square-wave oscillator built around the NE555 IC in astable configuration.  
> A potentiometer replaces the fixed timing resistor, enabling continuous real-time frequency tuning —  
> visible as alternating LED flashing that speeds up or slows down as resistance changes.

---

## 📋 Table of Contents

- [Circuit Diagram](#-circuit-diagram)
- [Components List](#-components-list)
- [How It Works](#-how-it-works)
- [Frequency Calculations](#-frequency-calculations)
- [How to Build — Wiring Guide](#-how-to-build--wiring-guide)
- [Results & Observations](#-results--observations)
- [Project Info](#-project-info)

---

## 🔌 Circuit Diagram

```
        +6V
         |
        [R1] ← Potentiometer (1kΩ – 10kΩ)  ← Frequency control
         |
        [R2] 10kΩ
         |
    ─────┬──────────────────────────────────────────────────────────
         |          ┌──────────────────┐
         ├──Pin 7───┤  DIS         VCC ├─── +6V
         |          │                  │
         ├──Pin 6───┤  THR         RST ├─── +6V
         |          │                  │
         └──Pin 2───┤  TRG    NE555    │
                    │                  │
                    │  GND     OUT ├───┤── R3 (270Ω) ──┬── LED1 ── +6V
                    │                  │               │
                    │  CV              │               └── D1 ── LED2
                    └──────────────────┘
         |
        [C1] 100µF
         |
        GND
```

> **Note:** Pin 5 (CV) connects to GND through a 10 nF bypass capacitor to filter noise on the internal 2/3 VCC reference.

---

## 🧰 Components List

| Ref | Component | Value | Role |
|-----|-----------|-------|------|
| IC1 | NE555 Timer | — | Astable oscillator core |
| R1 | Potentiometer | 1 kΩ – 10 kΩ | Variable timing resistor — controls frequency |
| R2 | Resistor | 10 kΩ | Fixed charge/discharge resistor |
| R3 | Resistor | 270 Ω | Current limiter for LED network |
| C1 | Electrolytic capacitor | 100 µF | Timing capacitor |
| C2 | Ceramic capacitor | 10 nF | Pin 5 (CV) bypass — noise filter |
| LED1 | LED | Red | ON during HIGH output state |
| LED2 | LED | Green | ON during LOW output state |
| D1 | Signal diode | 1N4148 | Steers current to LED2 on LOW |
| VCC | Power supply | 6 V DC | — |

---

## ⚙️ How It Works

**1. Power ON — Charging phase**  
C1 starts charging through R1 (pot) + R2 toward VCC. Pin 3 output goes **HIGH** → LED1 lights up.

**2. Upper threshold reached**  
When voltage across C1 hits **2/3 × VCC = 4 V**, the internal comparator trips the SR flip-flop. Output switches **LOW** → LED1 turns off.

**3. Discharge phase**  
Pin 7 (discharge transistor) pulls C1 down through R2 only. D1 steers output current to LED2 → LED2 lights up.

**4. Lower threshold reached**  
When C1 falls to **1/3 × VCC = 2 V**, the lower comparator fires, flip-flop resets, output returns **HIGH**. Cycle repeats.

**↻ Frequency control**  
Turning the potentiometer changes R1, which directly scales both t_H and t_L — the LEDs flash **faster at low resistance**, **slower at high resistance**.

---

## 📐 Frequency Calculations

### Formulas

| Parameter | Formula |
|-----------|---------|
| High time (t_H) | `0.693 × (R1 + R2) × C1` |
| Low time (t_L) | `0.693 × R2 × C1` |
| Period (T) | `t_H + t_L` |
| Frequency (f) | `1.44 / ((R1 + 2×R2) × C1)` |
| Duty cycle (D) | `(R1 + R2) / (R1 + 2×R2) × 100%` |

### Calculated Range (R2 = 10 kΩ, C1 = 100 µF)

| R1 (pot) | t_H | t_L | Period (T) | Frequency (f) | Duty cycle |
|----------|-----|-----|------------|---------------|------------|
| 1 kΩ (min) | 0.762 s | 0.693 s | 1.455 s | **0.687 Hz** | 52.4% |
| 5 kΩ (mid) | 1.040 s | 0.693 s | 1.733 s | **0.577 Hz** | 60.0% |
| 10 kΩ (max) | 1.386 s | 0.693 s | 2.079 s | **0.481 Hz** | 66.7% |

> **Key insight:** The fixed low time (0.693 × R2 × C1 ≈ 0.693 s) is constant regardless of R1.  
> Only t_H changes with the potentiometer, so duty cycle also varies with R1.

---

## 🔧 How to Build — Wiring Guide

### Pin Connections

| Pin | Name | Connect to |
|-----|------|------------|
| Pin 1 | GND | Negative rail (0 V) |
| Pin 2 | Trigger | Tied to Pin 6 → top of C1 |
| Pin 3 | Output | R3 (270 Ω) → LED network |
| Pin 4 | Reset | Positive rail (6 V) |
| Pin 5 | Control Voltage | 10 nF cap → GND |
| Pin 6 | Threshold | Tied to Pin 2 → top of C1 |
| Pin 7 | Discharge | Junction of R1 (wiper) and R2 |
| Pin 8 | VCC | Positive rail (6 V) |

### Step-by-Step

1. Place the NE555 IC across the center divide of the breadboard.
2. Connect Pin 8 and Pin 4 to the **+6V rail**.
3. Connect Pin 1 to the **GND rail**.
4. Wire the potentiometer (R1) between **+6V and Pin 7**, with R2 between **Pin 7 and the Pin 2/6 node**.
5. Connect C1 between the **Pin 2/6 node and GND** (observe polarity).
6. Add a 10 nF cap from **Pin 5 to GND**.
7. From **Pin 3**, wire R3 (270 Ω) to the anode of LED1 and to D1's anode. LED1 cathode → +6V rail. D1 cathode → LED2 anode → GND.

> ⚠️ **Always include R3 (≥ 270 Ω) in series with the LEDs.**  
> The 555 output can source/sink ~200 mA max — without a current limiter the LED will burn out instantly.

---

## 📊 Results & Observations

- Frequency is continuously adjustable across the full R1 range — from slow flash at max resistance to faster flash at minimum resistance.
- Duty cycle stays **above 50% at all R1 settings** because charging uses R1 + R2 while discharging uses R2 only.
- The fixed low time (≈ 0.693 s) sets a floor — LED2 is always ON for roughly the same duration regardless of the pot position.
- The 10 nF bypass cap on Pin 5 prevents noise on the internal 2/3 VCC reference from jittering the output frequency.
- Measured LED currents remain within safe limits (≈ 10–15 mA) across the full supply range due to R3.

---

## 👤 Project Info

| | |
|---|---|
| **Student** | Zeyad Bilal Gamal |
| **Bench No.** | 26 |
| **Section** | 2 |
| **Academic Year** | Second Year (2025) |
| **Course** | Electronics |
| **University** | Benha University |
| **Faculty** | Faculty of Engineering in Shoubra |
| **Department** | Communication and Computer Engineering |
