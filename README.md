# Navic-l1-receiver
Implementation of a NavIC (IRNSS) L1 receiver chain

---

## 📡 1. NavIC L1 Signal Generation (`l1_tx_sim.py`)

This script simulates the **NavIC L1 satellite transmitter and propagation channel**.

### 1.1 Signal Components
- Navigation data bits (100 bps)
- Pilot overlay bits
- Data channel spreading code
- Pilot channel spreading code

### 1.2 Modulation
- Complex baseband I/Q modulation
- Data and pilot channels combined
- Supports multiple satellites (PRN 1–64)

### 1.3 Channel Modeling
- Doppler frequency offsets (per satellite)
- Integer delay (sample-level)
- Fractional delay (sub-sample)
- Free-space path loss–based power scaling
- Additive White Gaussian Noise (AWGN)

### 1.4 Outputs
- Complex IQ waveform
- Quantized SC16Q11 samples
- CSV and binary IQ files
- Reference navigation bits and pilot overlay bits

### Purpose
This module is used to:
- Generate **controlled test signals**
- Validate acquisition and tracking performance
- Compare transmitted vs received navigation data

---

## 📶 2. NavIC L1 Receiver (`navic_l1_boc11.py`)

This script implements a **full software NavIC L1 receiver**.

---

### 2.1 Acquisition (Cold Start)

- Uses **Parallel Code Phase Search (PCPS)**
- Doppler search range: ±5 kHz
- Doppler resolution: configurable
- Searches pilot and data channels
- Outputs:
  - PRN ID
  - Doppler estimate
  - Code phase estimate

Once a satellite is detected, tracking is initialized.

---

### 2.2 Tracking Loops

For each visible satellite:

#### DLL (Delay Lock Loop)
- Tracks code phase
- Uses multiple correlators
- Maintains code alignment

#### PLL (Phase Lock Loop)
- Tracks carrier phase
- Ensures coherent demodulation

#### FLL (Frequency Lock Loop)
- Assists PLL during high Doppler or pull-in

#### Additional Features
- CN₀ estimation
- Lock detection
- Automatic re-lock handling

---

### 2.3 Pilot Overlay Synchronization

- Correlates received pilot overlay bits with local replica
- Resolves:
  - Bit alignment
  - Polarity inversion
- Determines first valid subframe boundary

This step is **critical for correct nav decoding**.

---

### 2.4 Navigation Data Decoding

- Decodes:
  - Subframe 1 (9 bits)
  - Subframe 2 (600 bits)
  - Subframe 3 (274 bits)
- LDPC / FEC decoding
- CRC verification using polynomial division
- Extracts navigation parameters

---

### 2.5 Pseudorange Estimation

- Uses:
  - Sample counters
  - Remaining code phase
  - Reference satellite alignment
- Computes pseudorange as:


