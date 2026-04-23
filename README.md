# Ltsplicecircuits
# LTspice Filter Circuits

This repository contains LTspice simulation files for a low-pass RC filter and two band-pass filter designs (RC-RC and RLC).

---

## 1. Low-Pass RC Filter

### Circuit

```
Vin ──── R ──── Vout
                 |
                 C
                 |
                GND
```

**Component values:**
- R = 500 Ω
- C = 50 µF

---

### Transfer Function Derivation

The output is taken across the capacitor. Using the voltage divider rule:

$$H(s) = \frac{Z_C}{Z_R + Z_C}$$

Where:

$$Z_R = R, \quad Z_C = \frac{1}{sC}$$

Substituting:

$$H(s) = \frac{\frac{1}{sC}}{R + \frac{1}{sC}}$$

Multiplying numerator and denominator by $sC$:

$$\boxed{H(s) = \frac{1}{1 + sRC}}$$

---

### Cutoff Frequency Derivation

Substituting $s = j\omega$:

$$H(j\omega) = \frac{1}{1 + j\omega RC}$$

The magnitude is:

$$|H(j\omega)| = \frac{1}{\sqrt{1 + (\omega RC)^2}}$$

The cutoff frequency $\omega_c$ is where the magnitude drops to $\frac{1}{\sqrt{2}}$ (the half-power point / -3dB point):

$$\frac{1}{\sqrt{1 + (\omega_c RC)^2}} = \frac{1}{\sqrt{2}}$$

$$1 + (\omega_c RC)^2 = 2$$

$$\omega_c RC = 1$$

$$\omega_c = \frac{1}{RC}$$

Converting to Hz:

$$\boxed{f_c = \frac{1}{2\pi RC}}$$

Plugging in values:

$$f_c = \frac{1}{2\pi \times 500 \times 50 \times 10^{-6}} = \frac{1}{2\pi \times 0.025} \approx 6.37 \text{ Hz}$$

**Verified in simulation: -3dB at 6.41 Hz **

---

### Behavior Summary

| Frequency | Output |
|-----------|--------|
| $f \ll f_c$ | $\|H\| \approx 1$ — signal passes fully |
| $f = f_c$ | $\|H\| = 0.707$ — half power (-3dB) |
| $f \gg f_c$ | $\|H\| \approx 0$ — signal blocked |

**Roll-off:** -20 dB/decade (first order filter)

---

---

## 2. Band-Pass Filter — RC-RC Design

### Circuit

```
Vin ──C1──┬──R2──Vout
          |       |
          R1      C2
          |       |
         GND     GND
```

> **Note:** HPF stage must come first (C1, R1), then LPF stage (R2, C2).
> If the order is reversed, the loading effect between stages shifts the cutoff frequencies and degrades the response.

**Component values:**
- C1 = 159 nF (HPF capacitor)
- R1 = 1 kΩ (HPF resistor)
- R2 = 1 kΩ (LPF resistor)
- C2 = 15.9 nF (LPF capacitor)

---

### Design Equations

**HPF cutoff (lower edge):**

$$f_1 = \frac{1}{2\pi R_1 C_1} = \frac{1}{2\pi \times 1000 \times 159 \times 10^{-9}} \approx 1 \text{ kHz}$$

**LPF cutoff (upper edge):**

$$f_2 = \frac{1}{2\pi R_2 C_2} = \frac{1}{2\pi \times 1000 \times 15.9 \times 10^{-9}} \approx 10 \text{ kHz}$$

---

### Transfer Function Derivation

The overall transfer function is the product of the HPF and LPF stages:

$$H(s) = H_{HPF}(s) \times H_{LPF}(s)$$

**HPF stage** (output across R1):

$$H_{HPF}(s) = \frac{sR_1C_1}{1 + sR_1C_1}$$

**LPF stage** (output across C2):

$$H_{LPF}(s) = \frac{1}{1 + sR_2C_2}$$

**Combined transfer function:**

$$\boxed{H(s) = \frac{sR_1C_1}{(1 + sR_1C_1)(1 + sR_2C_2)}}$$

---

### Q Factor Derivation

**Center frequency:**

$$f_0 = \sqrt{f_1 \times f_2} = \sqrt{1000 \times 10000} \approx 3162 \text{ Hz}$$

**Bandwidth:**

$$BW = f_2 - f_1 = 10000 - 1000 = 9000 \text{ Hz}$$

**Theoretical Q** (assuming no loading between stages):

$$Q_{theoretical} = \frac{f_0}{BW} = \frac{3162}{9000} \approx 0.35$$

**Actual Q** (accounting for loading effect between stages):

$$Q_{actual} = \frac{\sqrt{R_1C_1 \times R_2C_2}}{R_1C_1 + R_2C_2}$$

$$= \frac{\sqrt{159 \times 10^{-6} \times 15.9 \times 10^{-6}}}{159 \times 10^{-6} + 15.9 \times 10^{-6}}$$

$$= \frac{50.3 \times 10^{-6}}{174.9 \times 10^{-6}} \approx 0.287$$

> **Why is the actual Q lower than theoretical?**
> When Stage 2 is connected to Stage 1's output, it draws current from the intermediate node.
> This makes Stage 2's input impedance appear **in parallel** with R1, reducing the effective impedance at the node.
> As a result, the HPF cutoff frequency shifts upward and the LPF cutoff shifts downward, **widening the effective bandwidth** beyond the designed 9 kHz.
> Since $Q = f_0 / BW$, a wider bandwidth means a **lower Q**.
> This is called the **loading effect** — it is unique to passive RC stages .

---

---

## 3. Band-Pass Filter — RLC Design

### Circuit

```
Vin ──R──L──┬── Vout
            C
            |
           GND
```

**Component values:**
- R = 1 kΩ
- L = 17.7 mH
- C = 143 nF

---

### Design Equations

**Center frequency:**

$$f_0 = \sqrt{f_1 \times f_2} = \sqrt{1000 \times 10000} \approx 3162 \text{ Hz}$$

$$\omega_0 = 2\pi f_0 \approx 19872 \text{ rad/s}$$

**Bandwidth:**

$$BW = f_2 - f_1 = 9000 \text{ Hz}$$

$$\omega_{BW} = 2\pi \times BW \approx 56548 \text{ rad/s}$$

**Inductance:**

$$L = \frac{R}{BW} = \frac{1000}{56548} \approx 17.7 \text{ mH}$$

**Capacitance:**

$$C = \frac{1}{\omega_0^2 \times L} = \frac{1}{19872^2 \times 0.0177} \approx 143 \text{ nF}$$

---

### Transfer Function Derivation

Using the voltage divider — output is taken across the parallel combination of L and C (actually across C in series RLC):

The impedances in series: $Z_R = R$, $Z_L = sL$, $Z_C = \frac{1}{sC}$

Output across C:

$$H(s) = \frac{Z_C}{Z_R + Z_L + Z_C} = \frac{\frac{1}{sC}}{R + sL + \frac{1}{sC}}$$

Multiplying through by $sC$:

$$H(s) = \frac{1}{sRC + s^2LC + 1}$$

Dividing by $LC$:

$$\boxed{H(s) = \frac{\frac{1}{LC}}{s^2 + s\frac{R}{L} + \frac{1}{LC}}}$$

> **Note:** This is a standard second-order low-pass response. For a bandpass output, take the output across R instead:

$$H_{BP}(s) = \frac{\frac{R}{L} \cdot s}{s^2 + s\frac{R}{L} + \frac{1}{LC}}$$

---

### Q Factor Derivation

Comparing with the **standard second-order form:**

$$s^2 + \frac{\omega_0}{Q}s + \omega_0^2$$

From our transfer function, the coefficient of $s$ is $\frac{R}{L}$:

$$\frac{\omega_0}{Q} = \frac{R}{L}$$

$$Q = \frac{\omega_0 L}{R} = \frac{1}{R}\sqrt{\frac{L}{C}}$$

Plugging in values:

$$Q = \frac{1}{1000}\sqrt{\frac{0.0177}{143 \times 10^{-9}}} = \frac{1}{1000} \times 351.8 \approx 0.35$$

**Relationship to damping ratio $\zeta$ and damping coefficient $\alpha$:**

$$\alpha = \frac{R}{2L}, \quad \zeta = \frac{\alpha}{\omega_0} = \frac{1}{2Q}$$

For our circuit:

$$\zeta = \frac{1}{2 \times 0.35} \approx 1.43 \quad \text{(overdamped)}$$

> **Why does RLC have higher Q than RC-RC for the same bandwidth?**
> In RLC, the inductor L and capacitor C **exchange energy** back and forth at $\omega_0$ — the inductor stores energy as a magnetic field and the capacitor stores it as an electric field.
> Only the resistor R dissipates energy. So $Q \propto \frac{\text{energy stored}}{\text{energy lost}}$ is determined by how small R is.
> In RC-RC, **resistors are present in both stages** and continuously dissipate energy with no energy exchange mechanism, which is why Q cannot exceed ~0.5 without using active components like op-amps.
> Additionally, the RC-RC design suffers from the **loading effect** (see above), which further reduces Q below the theoretical value.

---

## Simulation Files

| File | Description |
|------|-------------|
| `lowpass.asc` | RC low-pass filter, R=500Ω, C=50µF, fc≈6.37Hz |
| `tworcbandpass.asc` | RC-RC band-pass filter, passband 1kHz–10kHz |
| `rlcbandpass.asc` | RLC band-pass filter, passband 1kHz–10kHz |
