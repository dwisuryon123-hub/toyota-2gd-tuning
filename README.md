# Toyota 2GD-FTV ECU Tuning & Performance Database

**Maintained by: Dwi Suryo Nugroho**

Dokumentasi teknis parameter Remap ECU Denso (SH72544 / SH72539), penyesuaian Fueling/Rail Pressure, VNT Turbo Control, panduan upgrade Turbo Stage 2/3, racikan khusus BioSolar (B35/B40) vs Pertamina Dex, serta setup transmisi matik AC60F untuk platform mesin **Toyota 2GD-FTV (2.4L Inline-4 Common Rail Turbo Diesel)**.

---

## 🚗 Supported Vehicles

Panduan dan parameter tuning dalam dokumentasi ini berlaku untuk platform kendaraan Toyota bermesin **2GD-FTV**:

- **Toyota Kijang Innova Reborn** (2.4 Diesel, AN140)
- **Toyota Fortuner G / VRZ / GR Sport** (2.4 Diesel, AN160 - Pre-facelift & Facelift)
- **Toyota Hilux Revo / Conquest / GR Sport** (2.4 Diesel Single/Double Cab, AN120/AN130)
- **Toyota HiAce Commuter / Premio** (2.4 Diesel)

---

## ⛽ Fuel Grade Comparison & Calibration Strategy

| Parameter | BioSolar / Solar Murah (B35/B40 ~48 CN) | Pertamina Dex / Shell Diesel (53+ CN) |
| :--- | :--- | :--- |
| **Karakteristik** | Cetane rendah, viskositas tinggi, kandungan sulfur & air tinggi | Cetane tinggi, viskositas rendah, pembakaran cepat |
| **Resiko Operational** | Fuel delay lama, knocking, akumulasi kerak pada nozzle & EGT tinggi | Harga tinggi |
| **Fokus Kalibrasi** | Advance SOI (Start of Injection), Rail Pressure tinggi pada RPM rendah-menengah | Durasi injeksi maksimal, target boost tinggi |

---

## 🧪 RACIKAN KHUSUS SOLAR BUSUK (BioSolar Heavy-Duty Setup)

Untuk menjaga performa mesin 2GD-FTV tetap optimal menggunakan BioSolar tanpa merusak komponen common rail:

### 1. Adjust Start of Injection (SOI) / Advance Timing
- **Hex Address:** `0x1E5200` (`SOI_Main_Map`)
- **Penyesuaian:** Majukan timing penyemprotan sebesar **+2.0° BTDC s/d +3.5° BTDC** pada area Low-Mid RPM (1400 - 2800 RPM) dengan load 40% - 80%.
- **Analisis:** Cetane number yang rendah menyebabkan *ignition delay* lebih panjang. Dengan memajukan SOI, puncak tekanan pembakaran ($P_{max}$) dicapai pada derajat engkol yang tepat setelah TDC, mencegah BBM terbakar terlambat di saluran buang yang dapat melonjakkan EGT.

### 2. Rail Pressure Target & Atomization
- **Hex Address:** `0x1F2810` (`Rail_P_Max`) & `0x1F2A00` (`Rail_Pressure_Base`)
- **Penyesuaian:** Naikkan Rail Pressure Target dari **1600 Bar menjadi 1750 – 1800 Bar** pada peta beban menengah-tinggi.
- **Analisis:** Peningkatan tekanan atomisasi memecah droplet BioSolar yang lebih kental menjadi mikron yang lebih kecil untuk mempercepat proses evaporasi dan pencampuran molekul oksigen.

### 3. Pilot Injection Control
- **Hex Address:** `0x1E6100` (`Pilot_Quantity_Map`)
- **Penyesuaian:** Tambahkan volume pilot injection sebesar **+5% s/d +8%** pada area idle s/d 2200 RPM.
- **Analisis:** Mengurangi *diesel knock* (suara ngelitik) akibat ignition delay BioSolar dan mengondisikan suhu ruang bakar sebelum main injection.

### 4. Hardware Filtration & Maintenance Requirement
- **Water Separator Kit:** Pasang pre-filter tambahan 10 micron sebelum filter solar bawaan.
- **Filter Solar Interval:** Ganti filter solar utama (OEM) maksimal tiap **5.000 km**.
- **Aditif anti-fungal / Cetane Improver:** Penggunaan aditif berbahan dasar 2-EHN (*2-Ethylhexyl Nitrate*) disarankan jika mengonsumsi B35/B40 secara terus-menerus.

---

## 🛠️ Hardware & Software Tools

- **Software Remap & Flashing:**
  * **PCMFlash:** Module 35 / Module 46 (Toyota Denso / Fujitsu Ten)
  * **WinOLS / ECM Titanium:** Membutuhkan Damos / A2L / Map Pack 2GD-FTV
  * **Toyota Techstream (TIS):** Read/Clear DTC, Live Data EGT, Rail Pressure, Data Compensation Code Injektor
- **Hardware OBD Interface:**
  * **Scanmatik 2 Pro (SM2 Pro)**
  * **Tactrix Openport 2.0 PassThru**

---

## 📌 1. Basic Tuning & Emissions Delete (Stage 1 Daily)

| Parameter / Map Name | Hex / Map Address (Denso 2GD) | Target Value (Stock -> Tuned) | Keterangan |
| :--- | :--- | :--- | :--- |
| **EGR Valve Control** | `0x1E42A0` / `EGR_Map_1` | `0 / Closed` | Mematikan alur sirkulasi EGR untuk mencegah penumpukan jelaga di intake manifold. |
| **Rail Pressure Target** | `0x1F2810` / `Rail_P_Max` | `1600 bar -> 1800 bar` | Mendorong atomisasi bahan bakar lebih presisi. |
| **Driver Demand / Throttle** | `0x1A1200` / `Pedal_Map_1` | `Linear / Sport Curve` | Mengatur linieritas respon pedal gas. |
| **Torque Limiter** | `0x1C34B0` / `TQ_Lim_Main` | `+20% - +30%` | Membuka batasan pembatas torsi bawaan ECU. |
| **Boost Target (Stock Turbo)** | `0x1D8900` / `Boost_Target` | `1.3 Bar -> 1.5 Bar` | Menaikkan tekanan turbo OEM secara aman untuk pemakaian harian. |

---

## 📌 2. Fueling Setup & Turbo Stage 2/3 Upgrade

### A. Tipe & Opsi Upgrade Turbo
1. **Stage 2: Stock Hybrid Turbo (Billet Compressor Wheel 44mm / 46mm)**
   * **Housing:** Standar OEM 2GD.
   * **Target Boost:** **1.8 Bar – 2.0 Bar**
   * **Karakter:** Response cepat, ideal untuk pemakaian harian dan lalu lintas *stop-and-go*.
2. **Stage 3: Full Upgrade Turbo Kit (F44 / F55 / RHF4 High-Flow)**
   * **Housing:** Replacement unit turbo aftermarket dengan exhaust manifold header.
   * **Target Boost:** **2.2 Bar – 2.5+ Bar**
   * **Karakter:** Suplai udara besar pada RPM menengah hingga tinggi.

### B. Map Address Fueling & Turbo Control

| Map Parameter | Hex / Map Address | Tuned Parameter / Value | Keterangan |
| :--- | :--- | :--- | :--- |
| **Injection Duration** | `0x1E8100` / `Inj_Dur_Main` | `+12% - +20% Duration` | Memperpanjang durasi pembukaan injektor pada High Load / High RPM. |
| **AFR / Smoke Map** | `0x1E9420` / `Lambda_Smoke` | `14.2:1 -> 13.5:1 (WOT)` | Pengaturan batas rasio udara-bahan bakar pada akselerasi penuh. |
| **VNT Duty Cycle** | `0x1D9100` / `VNT_Spool_Map` | `Modified Duty Cycle` | Mengatur sudut nozzle vane untuk mempercepat spooling turbo. |
| **Boost Limiter Max** | `0x1D8F50` / `Boost_Lim_Abs` | `2.2 Bar - 2.5 Bar` | Membuka pembatas tekanan maksimum turbo. |

---

## 🛡️ 3. Safety Limits & Parameter Proteksi

Untuk mencegah kerusakan komponen (*cylinder head crack, turbo surge, EGT berlebih, atau over-temperature transmisi*), parameter pembatas berikut wajib diaktifkan:

| Proteksi Safety | Address Offset | Parameter ECU / Hardware | Mekanisme Proteksi |
| :--- | :--- | :--- | :--- |
| **VNT Closed-Loop Control** | `0x1D9500` | `VNT Duty Cycle Map` | Buka vane turbin jika tekanan melebihi target untuk mencegah boost surge. |
| **Barometric Boost Limit** | `0x1D8E00` | `Boost Limiter vs Baro` | Memotong target boost secara otomatis di dataran tinggi (tekanan udara rendah). |
| **EGT Safe Limiter** | `0x1F12B0` | `EGT Cutback Map` | Kurangi durasi injeksi jika suhu gas buang mendeteksi >800°C selama lebih dari 5 detik. |
| **Coolant Temperature Cut** | `0x1B2100` | `ECT Fuel Cut / Fan Control` | Aktifkan kipas maksimum dan kurangi torsi jika suhu pendingin mencapai 95°C. |

---

## 🔩 4. Hardware Upgrade Checklist

| Komponen Hardware | Spesifikasi | Fungsi Utama |
| :--- | :--- | :--- |
| **Downpipe & Frontpipe** | Stainless Steel 2.5 - 3.0 Inch Freeflow | Menurunkan backpressure gas buang dan EGT. |
| **EGR Blanking Plate** | Stainless / Billet Aluminum | Menutup alur gas buang ke saluran intake secara fisik. |
| **Intercooler Kit** | Front-Mount Intercooler & Piping Alumunium | Menurunkan Intake Air Temperature (IAT) untuk kerapatan udara lebih baik. |
| **ATF Cooler (Automatic)** | External Cooler 10-15 Row | Mempertahankan suhu oli transmisi di bawah 90°C. |
| **Gauges (EGT & Boost)** | High-Precision Digital/Analog Sensor | Monitoring parameter vital secara real-time. |
| **Filter Udara** | High-Flow Replacement Filter | Meningkatkan volume udara masuk. |
| **Injektor High-Flow** | +30% s/d +50% Flow Capacity | Khusus untuk penggunaan Stage 3+ agar durasi injeksi tidak terlalu panjang. |

---

## 📌 5. Transmission Control (TCU AC60F 6-Speed Automatic)

| Parameter | Hex / Offset TCU | Tuned Value | Keterangan |
| :--- | :--- | :--- | :--- |
| **Line Pressure / Clutch Torque** | `0x2A110` | `+15% - +20% Pressure` | Mencegah slip pada clutch pack transmisi saat torsi >500 Nm. |
| **Shift Speed Target** | `0x2B450` | `Fast Engagement` | Mempercepat waktu perpindahan gigi pada kondisi pedal gas penuh. |

---

## ⚠️ Safe Tuning Operating Rules

1. **EGT Monitoring:** Segera kurangi beban mesin jika EGT menyentuh 800°C.
2. **ATF Temperature Limit:** Suhu oli transmisi matik tidak boleh melebihi 105°C.
3. **Checksum Verification:** Pastikan fungsi Checksum Correction aktif di PCMFlash atau WinOLS sebelum proses penulisan file (*Write ECU*) untuk mencegah ECU terkunci/bricked.
