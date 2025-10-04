<div align="center">
 
# Week 2 : Part 1
# BabySoC Fundamentals & Functional Modelling

</div>

<div align="center">
 
[![RISC-V](https://img.shields.io/badge/RISC--V-SoC%20Tapeout-blue?style=for-the-badge&logo=riscv)](https://riscv.org/)
[![VSD](https://img.shields.io/badge/VSD-Program-orange?style=for-the-badge)](https://vsdiat.vlsisystemdesign.com/)
![Week](https://img.shields.io/badge/Week-2-pink?style=for-the-badge)

</div>

## Overview :
A System-on-Chip (SoC) is a complete computer system packed onto a single chip, combining a processor (often RISC-V), memory, and various peripheral modules.
RISC-V is a popular open-source processor architecture chosen for SoCs because of its flexibility, efficiency, and customizability.
SoC designs use RISC-V CPU cores to control different hardware blocks, manage memory, and communicate with external devices, making them central to modern electronics from wearables to smart appliances. 
Building and simulating an SoC allows designers to experiment, optimize, and verify hardware before manufacturing, making innovation more accessible for engineers and students.

## Table of Content :
1. What is an SoC?
2. SoC Dedign flow
3. SoC Main Building Blocks
4. Why Use RISC-V in an SoC?
5. What is BabySoC
6. The role of functional modeling before RTL and physical design



### 1. What is an SoC?
An SoC (System-on-Chip) is basically a tiny computer built onto a single microchip. 
It combines a processor (the part that runs programs) with memory, input/output parts (like buttons, sensors, or displays), and sometimes special-purpose blocks. 
Instead of being separate chips on a board, everything is packed together. 
This is how things like phones, watches, or smart kitchen machines have real smarts inside.


### 2. SoC Dedign flow :

<div align="center">
<img width="900" height="400" alt="SoC-Design-Services" src="https://github.com/user-attachments/assets/4d4cabea-2495-4a5f-be80-18a6ec9b7831" />
</div>

The diagram illustrates a process for SoC (System on a Chip) design, with "SoCompiler" as a central component. The process is broken down into four main phases:
#### Specification (Architecture Exploration): 
This initial phase involves creating a functional block diagram and configuring the Intellectual Property (IP) and Design for Testability (DFT). 

#### Integration (Integration Express): 
This phase focuses on connecting different parts of the design. 
It involves creating a top-level RTL (Register-Transfer Level) netlist and performing pin assignment.

#### Verification (Verification Express): 
This phase ensures the design's functional soundness. 
It uses a UVM (Universal Verification Methodology) testbench and C models to verify the design. 
This stage is linked to the "SoReal Virtual Platform," which facilitates both hardware and software verification. 

#### Implementation: 
The final phase involves generating the physical design. 
This includes setting up Clocking, SDC (Synopsys Design Constraints) Timing Constraints, and performing PPA (Power, Performance, Area) and floor planning.

### 3. SoC Main Building Blocks
<div align="center">
<img width="555" height="345" alt="Screenshot 2025-10-05 at 03-01-00 PowerPoint Presentation - Intro-SoC pdf" src="https://github.com/user-attachments/assets/37a06b72-e5d6-4111-b9e2-0f784beb770e" />
</div>

A System-on-Chip (SoC) integrates multiple essential components into a single microchip, enabling complete computing systems in compact, power-efficient packages.
The main building blocks of an SoC are:

#### 1. Processor Core (CPU)
- The brain of the SoC that runs programs by executing instructions.
- Modern SoCs may have multiple CPU cores (multi-core) for parallel processing.
- Common architectures include RISC-V, ARM Cortex, and DSP cores for specialized computation (like signal processing).


#### 2. Memory
- Provides storage for program instructions and data.
- Includes volatile memory like RAM (SRAM, DRAM) for temporary data and non-volatile memory like ROM or flash for permanent storage.
- Often incorporates cache memory close to the processor for faster access.
- Memory hierarchy is critical for performance optimization.

#### 3. Peripherals and Interfaces
- Connect the SoC to the outside world or other subsystems.
- Examples include USB, UART (serial communication), SPI, I2C, HDMI, Ethernet, Wi-Fi, and Bluetooth.
- These interfaces enable communication with sensors, displays, storage, and other devices.

#### 4. Graphics Processing Unit (GPU)
- Specialized unit for accelerating image, video, and graphical computations.
- Important for user interfaces, gaming, and media playback.

#### 5. Digital Signal Processor (DSP)
- Specialized for real-time processing of signals such as audio, video, and sensor data.
- Performs complex mathematical operations faster and more efficiently than general CPUs in these domains.
  
#### 6. Bus and Interconnect Network
- Internal highways connecting all building blocks within the SoC.
- Common bus protocols include AXI, AHB, and APB, facilitating data transfer among CPU, memory, and peripherals.

#### 7. Power Management Unit (PMU)
- Manages voltage regulation, power distribution, and energy-saving modes.
- Essential for battery-powered and low-power devices to maximize efficiency and runtime.

#### 8. Security Modules
- Includes hardware for encryption, secure boot, and protection against unauthorized access.
- Increasingly important for privacy and data protection.

#### 9. Mixed-Signal Components
- Convert between analog and digital signals, such as ADC (Analog-to-Digital Converter) and DAC (Digital-to-Analog Converter).
- Critical for interacting with real-world sensors and actuators.
  
### 4. Why Use RISC-V in an SoC?
RISC-V is an open and flexible set of instructions that your processor understands. 
It’s basically like a universal language for the chip’s brain, and you don’t need to pay to use it.
RISC-V is popular for learning, experimenting, and designing modern chips because anyone can use or customize it.

### 5. What is BabySoC ?

<div align="center">
  <img width="624" height="349" alt="BabySoC_block" src="https://github.com/user-attachments/assets/ccb40401-fc2d-4e48-82e9-e7dbdc01c5f7" />
</div>

In chip design, a BabySoc refers to a small, simplified System on a Chip (SoC) used for testing and educational purposes.
Unlike the complex SoCs found in commercial products like smartphones, a BabySoc contains a minimal set of essential components. 
This smaller, less complex design is a cost-effective and efficient way to test new intellectual property (IP) 
cores and calibrate analog parts of a system before integrating them into a full-scale chip. 

#### Architecture of a BabySoc
The main components of the VSDBabySoC are a RISC-V based microprocessor, a Phase-Locked Loop (PLL), and a Digital-to-Analog Converter (DAC). 
##### RVMYTH microprocessor:
A simple RISC-V-based central processing unit (CPU). It executes instructions from its internal memory (imem) and outputs data to a specific register (register #17).
##### AVSDPLL (8x-PLL): 
An analog IP core used to generate a stable, synchronized clock signal for the microprocessor. It takes a reference clock as input and produces an output clock that is a multiple of the input frequency.
##### AVSDDAC (10-bit DAC): 
A 10-bit digital-to-analog converter that takes the digital output from the RVMYTH processor and converts it into an analog signal. This allows the chip to interface with external analog devices.

### 5. Why BabySoC is a simplified model for learning SoC concepts ?

#### Encourages learning by doing: 
The purpose of such a model is not to perfectly replicate the complexity of a real-world system but to provide a hands-on foundation for students.
By working with a simpler version, students can focus on the core concepts before advancing to more sophisticated designs.
#### Focuses on core functionality: 
In a real SoC, many interconnected and complex components exist.
A "Baby" model would strip away non-essential features to highlight the basic components of an SoC,
such as a simple processor core, memory, and bus, making the data flow easier to understand.
#### Reduces complexity: 
The complex nature of real-world SoCs involves vast amounts of logic, power management, and clocking, which can overwhelm a novice. 
A simplified model isolates the fundamental principles, allowing learners to build their knowledge incrementally.
#### Enables independent learning: 
A simplified model lowers the barrier to entry, enabling students to explore and experiment with the core concepts independently.
This reduces reliance on high-cost industry tools and specialized hardware, promoting wider educational access.

### 6. The role of functional modeling before RTL and physical design
Functional modeling is an abstract representation of a system's behavior before delving into detailed hardware design. It is a critical, high-level step that defines what a system does, rather than how the hardware will implement it. 
#### Behavioral definition and validation: 
Functional models, often written in high-level languages like C/C++ or SystemVerilog, serve as an executable specification of the system's behavior. Designers can use this model to confirm that the design correctly meets its requirements before any hardware coding begins.
#### Design exploration and trade-offs: 
At this stage, designers can quickly experiment with different algorithms and architectures to evaluate performance, cost, and power consumption. This flexibility is far more efficient than making changes after the detailed RTL or physical design is underway.
#### Improved productivity and cost: 
By validating the design at a high level, functional modeling helps identify and fix architectural issues early in the process. This prevents costly and time-consuming changes later in the RTL and physical implementation stages.
#### Implementation gap reduction:
A high-level functional model provides a clear reference for the RTL and physical design teams. This reduces the "implementation gap" by providing a golden, functional reference against which the more detailed hardware implementation can be verified.
#### Hardware/software co-design: 
In modern SoCs, functional models are essential for hardware-software co-simulation. This allows developers to create and test software long before the physical hardware is available. 





