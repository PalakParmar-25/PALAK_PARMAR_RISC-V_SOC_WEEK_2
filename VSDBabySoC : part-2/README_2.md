<div align="center">
 
# Week 2 : Part 2
# BabySoC Fundamentals & Functional Modelling

</div>

<div align="center">
 
[![RISC-V](https://img.shields.io/badge/RISC--V-SoC%20Tapeout-blue?style=for-the-badge&logo=riscv)](https://riscv.org/)
[![VSD](https://img.shields.io/badge/VSD-Program-orange?style=for-the-badge)](https://vsdiat.vlsisystemdesign.com/)
![Week](https://img.shields.io/badge/Week-2-pink?style=for-the-badge)

</div>

## Overview

## 🛠️ Step Involved in Setup :

### 1. Adding the VSDBabySoc directory

You clone any BabySoC project repository or mannu add the file and directories needed which fullfill the below given tree structure :

```bash
VSDBabySoC/
├── src/
│   ├── include/      # Header files (*.vh)
│   ├── module/       # Verilog + TLV modules
│   │   ├── vsdbabysoc.v   # Top-level module
│   │   ├── rvmyth.v       # CPU
│   │   ├── avsdpll.v      # PLL
│   │   ├── avsddac.v      # DAC
│   │   └── testbench.v    # Testbench
└── output/           # Simulation outputs
```
I cloned the git hub repo using the following code in the VLSI directory (made in week 1):
```bash
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
```

### 2. Conversion of a TLV file(.tlv) to a verilog (.v) file

When I cloned the reposirtory I found that all the verilog files which were needed were there but rvmyth.v was not present it was in the form of rvmyth.tlv so, to convert rvmyth.tlv file to rvmyth.v I used the following code :

```bash
# Install tools
sudo apt update
sudo apt install python3-venv python3-pip

# Create virtual env
python3 -m venv sp_env
source sp_env/bin/activate

# Install SandPiper-SaaS
pip install pyyaml click sandpiper-saas

# Convert TLV → Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```
After using these codes rvmyth.v was succesfully add to the directory as shown in the week2_tree.png .

### 2. Pre - Synthesis Simulation
As I have already installed Icarus Verilog (iverilog) and GTKwave , going ahead with synthesis :
I used the below written code :

```bash
mkdir -p output/pre_synth_sim

iverilog -o output/pre_synth_sim/pre_synth_sim.out \
  -DPRE_SYNTH_SIM \
  -I src/include -I src/module \
  src/module/testbench.v

cd output/pre_synth_sim
./pre_synth_sim.out
```
To view the simulation waveforms :
```bash
gtkwave output/pre_synth_sim/pre_synth_sim.vcd
```
<img width="1366" height="768" alt="week_2_gtkwave" src="https://github.com/user-attachments/assets/5eabd1e9-059e-447e-aa89-2d84f274da60" />

## Observations from simulation waveform :

### Signal-by-Signal Analysis

#### 1. CLK (System Clock)  
**Type** : `reg` (register)  
**Waveform** : Bright green periodic square wave  
**Characteristics** :
  - Clean, uniform clock signal
  - Regular period (appears to be ~50ns based on zoom)
  - This is the **input reference clock** to the PLL module
  - Drives the entire synchronous design

#### 2. CPU_dmem_addr_a4[3:0] (CPU Data Memory Address)
**Type**: 4-bit bus  
**Waveform**: Shows rapid transitions with values like `0`, `A`, `0`, `F`, `0`, `1`, `2`, `3`, `5`  
**Characteristics**:
  - Memory address bus from CPU  
  - Changes indicate CPU is accessing different memory locations
  - High activity shows active processing

#### 3. OUT[9:0] (Output Bus)
**Type**: 10-bit output bus  
**Waveform**:   
  - Starts with **red region** marked with `x` (unknown/uninitialized)
  - Transitions to a **sawtooth/triangular analog-like pattern** (green)  
**Characteristics**:
  - This is likely a **DAC (Digital-to-Analog Converter) output**
  - The analog waveform suggests digital values being converted
  - Pattern shows periodic ramp-up behavior

#### 4. reset (Reset Signal)
**Type**: wire  
**Waveform**: 
  - **Starts HIGH (active)** - shown in green
  - **Transitions LOW** after initial period (~200ns)
  - Remains LOW during normal operation  
  **Analysis**:
  - This is an **active-high asynchronous reset**

#### 5. Dext[10:0] (External Data Bus)
**Type**: 11-bit bus  
**Waveform**: Shows hex values: `xxx`, `011`, `000`, `001`, `003`, `006`, `00A`, `00F`  
**Characteristics**:
  - Starts with unknown state (`xxx`)
  - After reset, begins incrementing
  - Appears to be **data being read/written**

#### 6. EN (Enable Signal)
**Type**: wire  
**Waveform**:
  - Shows "man" label (likely metadata)
  - Appears mostly high during operation  
**Characteristics**: General enable control  

#### 7. NaN (Not-a-Number Flag)
**Type**: wire   
**Waveform**: Shows "nan"   
**Characteristics**: Indicates invalid floating-point operations or error conditions    

#### 8. Dext[10:0] (Second instance)
- Same as signal #5, showing data progression

#### 9. VREFH (Voltage Reference High)
**Type**: wire  
**Waveform**: Constant HIGH (`1`)  
**Characteristics**: Reference voltage for analog conversions  
 
#### 10. VREFL (Voltage Reference Low)
**Type**: wire   
**Waveform**: Constant LOW (`0`)   
**Characteristics**: Ground reference for analog conversions   

#### 11. OUT (Analog Output)
**Type**: Real (analog)  
**Waveform**: Beautiful inusoidal/triangular wave  
**Characteristics**:
  - Smooth analog waveform
  - This is the **actual DAC analog output**
  - Shows the PLL/DAC is generating a periodic signal

#### 12. CLK (PLL Output Clock)
**Type**: reg  
**Waveform**: High-frequency square wave (much faster than input CLK)   
**Characteristics**:   
  - **Frequency-multiplied clock** from PLL
  - Used for downstream high-speed operations

#### 13. ENb_CP (Enable Charge Pump - inverted)
**Type**: wire  
**Waveform**: Mostly LOW with occasional pulses   
**Characteristics**: Control signal for PLL charge pump circuit  

#### 14. ENb_VCO (Enable VCO - inverted)
**Type**: wire   
**Waveform**: Appears mostly inactive (LOW)   
**Characteristics**: Control signal for Voltage-Controlled Oscillator  

#### 15. REF (Reference Signal)
**Type**: wire  
**Waveform**: Periodic pulses (lower frequency than CLK)   
**Characteristics**: 
  - Reference clock for PLL phase comparison
  - Used by phase detector

#### 16. VCO_IN (VCO Input Control Voltage)
**Type**: wire  
**Waveform**: Periodic pulses  
**Characteristics**: Control signal driving the VCO frequency  

#### 17. lastedge (Last Edge Timestamp)
**Type**: real  
**Values**: `0`, `100`, `383.33`, `666.66`, `949.99`, `1233.32`  
**Characteristics**:
  - Timestamps of detected clock edges
  - Shows approximately **283ns spacing** between edges
  - Used for PLL phase detection

#### 18. period (Clock Period)
**Type**: real  
**Values**: `25`, `35.41625`, `35.41625000000001`, `35.4162498`  
**Characteristics**:
  - Measured clock period
  - Shows PLL **lock stabilization** (settling around 35.4ns)
  - Initial value of 25 during initialization

#### 19. refpd (Reference Phase Detector)
**Type**: real (highlighted in blue)  
**Values**: `0`, `283.33`, `283.329999`  
**Characteristics**:
  - Phase difference measurement
  - Critical for PLL feedback loop
  - Stabilizes as PLL locks

### Detailed Analysis

#### 1. Reset Operation ✅

- **Active-high reset** starts at t=0
- Reset is asserted for approximately **180-200ns**
- During reset:
  - `OUT[9:0]` shows **red 'x' values** (unknown/uninitialized)
  - `Dext` shows **'xxx'** (tri-state/unknown)
  - Analog output remains at initial state

**Post-Reset:**
- Reset goes LOW (~200ns mark)
- System begins normal operation:
  - `OUT` transitions from red to green (valid data)
  - `Dext` starts incrementing from `000`
  - Analog output begins generating waveform
  - PLL starts locking process

#### **2. Clocking** ⏰

**A. Input Reference Clock (CLK - top)**
- **Period**: ~50ns (20 MHz)
- **Duty cycle**: ~50%
- Clean, stable square wave

**B. PLL Output Clock (CLK - middle section)**
- **Much higher frequency** than input
- Appears to be **frequency multiplied**
- Used for high-speed internal operations

**C. REF Clock**
- **Lower frequency** reference for phase comparison
- **Periodic pulses** rather than continuous square wave

**Clock Relationship:**
```
Input CLK (20 MHz) → PLL → Output CLK (higher freq) + REF (phase reference)
```

### **3. Dataflow Between Modules** 🔄

**Data Flow Topology:**

```
CPU → CPU_dmem_addr_a4[3:0] → Memory/DAC Input
                                      ↓
                                  Dext[10:0] (Data values)
                                      ↓
                                  OUT[9:0] (Digital samples)
                                      ↓
                                  DAC Conversion
                                      ↓
                                  OUT (Analog waveform)
```

### Key Observations:

1. **CPU Activity**:
   - `CPU_dmem_addr_a4` shows active memory addressing
   - Values sequence: `0→A→0→F→0→1→2→3→5`
   - Indicates **non-sequential access pattern** (possibly lookup table for waveform generation)

2. **Data Progression**:
   - `Dext` increments: `011→000→001→003→006→00A→00F`
   - This appears to be **sample values** for waveform generation
   - Values match CPU addressing pattern

3. **Digital-to-Analog Conversion**:
   - `OUT[9:0]` shows the digital representation
   - Lower analog `OUT` shows **smooth waveform**
   - The sawtooth/triangular pattern in digital `OUT` becomes sinusoidal in analog domain

4. **PLL Control Loop**:
   ```
   REF → Phase Detector (refpd) → Charge Pump (ENb_CP) 
        → VCO (VCO_IN) → Output CLK → Feedback
   ```

5. **Timing Relationships**:
   - `lastedge` values: `100→383.33→666.66→949.99→1233.32`
   - Spacing: **~283ns between edges**
   - `refpd` stabilizes at **283.33** (phase difference)
   - This shows **PLL achieving lock**


### Operating Sequence:
1. Reset asserted → all signals initialized
2. Reset released → PLL begins lock acquisition  
3. CPU starts generating address sequence
4. Data samples fetched from memory (`Dext`)
5. Samples converted to digital waveform (`OUT[9:0]`)
6. DAC converts to analog output (smooth waveform)
7. PLL stabilizes with ~283ns phase detector reading
