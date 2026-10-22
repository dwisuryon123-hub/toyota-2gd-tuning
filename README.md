# Toyota 2GD-FTV ECU Tuning & Performance Database

Technical documentation for Denso ECU remapping, fueling parameters, VNT turbo control, Stage 2/3 upgrades, fuel adaptation (BioSolar vs Dex), and safety limits for the Toyota 2GD-FTV platform.

---

## Supported Vehicles

* Toyota Kijang Innova Reborn (2.4 Diesel, AN140)
* Toyota Fortuner VRZ / G / GR Sport (2.4 Diesel, AN160)
* Toyota Hilux Revo / Conquest (2.4 Diesel Single/Double Cab, AN120/AN130)
* Toyota HiAce Commuter / Premio (2.4 Diesel)

---

## Technical Tools & Interface

* ECU Flashing: PCMFlash (Module 35 / Module 46 - Toyota Denso / Fujitsu Ten)
* Editing Software: WinOLS / ECM Titanium (Damos / Map Pack 2GD-FTV)
* Diagnostic & Live Data: Toyota Techstream (TIS)
* PassThru Hardware: Scanmatik 2 Pro (SM2 Pro) / Tactrix Openport 2.0

---

## 1. Fuel Grade Adaptation: BioSolar (B35/B40) vs Pertamina Dex

| Parameter | BioSolar (B35/B40 ~48 CN) | Pertamina Dex / High-Cetane (53+ CN) |
| :--- | :--- | :--- |
| Fuel Characteristics | High sulfur/water content, lower ignition quality | Low sulfur, high Cetane rating, clean burn |
| Main Issues | Injector clogging, incomplete combustion, carbon buildup | Higher cost |
| Tuning Strategy | Advance SOI (+2.0° to +3.5°), Increase Rail Pressure | Standard/Aggressive SOI, Increased Duration Map |

### BioSolar (Low-Cetane Fuel) Calibration Rules
1. Start of Injection (SOI) Adjustment:
   * Map Address Offset: `0x1E5200` (`SOI_Main_Map`)
   * Calibration: Advance injection timing by +2.0° to +3.5° in the low-to-mid RPM range.
   * Purpose: Compensates for the ignition delay of lower-Cetane fuels, preventing excessive soot build-up on nozzle tips.
2. High Atomization Rail Pressure:
   * Map Address Offset: `0x1F2810` (`Rail_P_Max`)
   * Calibration: Increase target rail pressure to 1750–1800 bar (Stock: 1600 bar).
   * Purpose: Maximizes atomization density for heavier fuel blends.
3. Hardware Maintenance Requirements:
   * Install an auxiliary water separator kit upstream of the OEM filter.
   * Replace the primary fuel filter at 5,000 km intervals.

---

## 2. Base Mapping Parameters (Stage 1 Daily)

| Parameter / Map Name | Hex / Map Address (Denso 2GD) | Target Value (Stock -> Tuned) | Description |
| :--- | :--- | :--- | :--- |
| EGR Valve Control | `0x1E42A0` / `EGR_Map_1` | `0 / Closed` | Disables EGR valve operation to prevent intake contamination. |
| Rail Pressure Target | `0x1F2810` / `Rail_P_Max` | `1600 bar -> 1800 bar` | Raises rail pressure for improved atomization. |
| Driver Demand / Throttle | `0x1A1200` / `Pedal_Map_1` | Linear / Sport Curve | Removes throttle lag and dead zones. |
| Torque Limiter | `0x1C34B0` / `TQ_Lim_Main` | `+20% to +30%` | Increases allowable engine torque limits. |
| Boost Target (Stock) | `0x1D8900` / `Boost_Target` | `1.3 Bar -> 1.5 Bar` | Increases turbocharger target boost pressure. |

---

## 3. High-Output Fueling & Turbo Setup (Stage 2 / Stage 3)

### Turbocharger Configurations
1. Stage 2 (Hybrid Turbo Upgrade)
   * Hardware: Stock housing with billet compressor wheel (44mm / 46mm).
   * Target Boost Pressure: 1.8 Bar – 2.0 Bar.
   * Characteristics: Fast spool, minimal lag, suitable for street use.
2. Stage 3 (Full Turbo Replacement)
   * Hardware: Heavy-duty aftermarket turbocharger unit (F44 / F55 / RHF4 series) with custom manifold.
   * Target Boost Pressure: 2.2 Bar – 2.5+ Bar.
   * Characteristics: High flow capacity at mid-to-high RPM range.

### Fueling & Boost Map Address Offset

| Map Parameter | Hex / Map Address | Tuned Parameter / Value | Description |
| :--- | :--- | :--- | :--- |
| Injection Duration | `0x1E8100` / `Inj_Dur_Main` | `+12% to +20% Duration` | Extends injection pulse width under high load. |
| AFR / Smoke Map | `0x1E9420` / `Lambda_Smoke` | `14.2:1 -> 13.5:1 (WOT)` | Adjusts air-fuel ratio limits for maximum fuel delivery. |
| VNT Duty Cycle | `0x1D9100` / `VNT_Spool_Map` | Revised Duty Cycle | Optimizes vane position for rapid boost building. |
| Boost Limiter Max | `0x1D8F50` / `Boost_Lim_Abs` | `2.2 Bar - 2.5 Bar` | Raises absolute boost pressure limit. |

---

## 4. Safety Protection Parameters

| Protection Circuit | Address Offset | ECU Map / Hardware Target | Function / Control Logic |
| :--- | :--- | :--- | :--- |
| VNT Closed-Loop Control | `0x1D9500` | `VNT Duty Cycle Map` | Adjusts vane angles dynamically to suppress overboost spikes. |
| Barometric Boost Limit | `0x1D8E00` | `Boost Limiter vs Baro Pressure` | Reduces target boost at higher altitudes to prevent turbo overspeed. |
| EGT Safety Protection | `0x1F12B0` | `EGT Temperature Cutback` | Reduces fuel quantity if EGT exceeds 800°C for >5 seconds. |
| Engine Coolant Cutback | `0x1B2100` | `ECT Fuel Cut / Fan Control` | Triggers radiator fan activation at 95°C and fuel cutback if ECT exceeds limit. |

---

## 5. Hardware Upgrade Checklist

| Component | Specification | Purpose |
| :--- | :--- | :--- |
| Exhaust System | Freeflow Downpipe & Frontpipe | Reduces exhaust backpressure and lowers EGT. |
| EGR Plate | Stainless / Billet Aluminum Blanking Plate | Physically blocks exhaust gas recirculation. |
| Intercooler Kit | Front-Mount Aluminum Intercooler Kit | Lowers Intake Air Temperature (IAT). |
| Transmission Cooling | External ATF Cooler (10–15 Row) | Maintains ATF temperature below 90°C under heavy load. |
| Monitoring Gauges | High-Precision EGT & Boost Gauge | Provides real-time thermal and boost feedback. |
| Air Intake | High-Flow Replacement Filter | Increases total volumetric intake airflow. |
| Fuel Injectors | High-Flow Nozzles (+30% to +50%) | Required for Stage 3 applications to maintain acceptable injection duration. |

---

## 6. TCU Tuning (AC60F 6-Speed Automatic)

| Parameter | Hex / Offset TCU | Tuned Value | Description |
| :--- | :--- | :--- | :--- |
| Line Pressure / Clutch Torque | `0x2A110` | `+15% to +20% Pressure` | Increases clutch clamping force to prevent slip under high torque (>500 Nm). |
| Shift Speed Target | `0x2B450` | Fast Engagement | Reduces shift duration during Wide-Open Throttle (WOT). |

---

## 7. Execution Guidelines

1. Always verify checksums using WinOLS or PCMFlash prior to writing to the ECU.
2. Monitor Exhaust Gas Temperature (EGT) continuously during road logging.
3. Ensure ATF temperature remains below 105°C during high-torque testing.
