---
sort: 1
---

# Projects 

## A 32-bit RISC-V-based Microcontroller in 180nm CMOS Technology for Low-Power IoT Applications
While India is poised to emerge as the _Hotbed for IoT Opportunities_, it's semiconductor dependencies is a glaring reminder of a long road ahead before being technologically independent. This project is a step towards the goal of bridging that gap. This microcontroller is intended for use in low-power IoT based applications such as environment (temperature, humidity, air-quality) monitoring systems. Some of the major design efforts in this project are:

- Generating a small-footprint and low-power RISC-V core  using the open-source [PULPino](https://github.com/pulp-platform/pulpino) platform developed at ETH Zurich.
- Verification of the core with IoT application C-programs.
- Implementing the PULPino core on a Xilinx's Artix-7 FPGA development board to emulate the microcontroller and test the intended applications in real time. 
- Creating and verifying a GDS-II of the core for 180nm CMOS technology.
- A 10-bit, 100 kHz Successive Approximation Resistor (SAR) ADC for interfacing sensors to the microcontroller.
- Temperature-independent voltage and current reference for biasing internal circuits.
- Temperature compensated Ring-Oscillator for generating internal clock signal. 

## A sub-nanoamp Programmable Current Reference Design in 28nm CMOS Technology
This is an industry-collaborated project developing various analog IP blocks for an ultra-efficient neuromorphic processor in 28-nanometer CMOS technology. Various analog and mixed-signal blocks require a programmable current reference all the way down to sub-nanoamp of current which extremely challenging in this technology. Major design efforts in this project are:
- Architecture of a temperature-independent voltage and current reference circuit.
- Design of the voltage and current reference circuits using enterprise Computer Aided Design (CAD) tools.
- Layout and verification (DRC/LVS/QRC) of all the designed blocks in the 28nm CMOS technology.

## Analog Building Blocks for a Power Management Chip in a High-Voltage 180nm Silicon-on-Insulator (SOI) CMOS Technology
This project was done in collaboration with a semiconductor startup developing power management solution for the LED lighting industry. Analog IP blocks were designed, simulated and laid out in a 200V 180nm SOI CMOS technology and sent for fabrication. Analog IP blocks developed were:
- Temperature-independent Bandgap voltage reference.
- Temperature-independent current reference for biasing internal circuits.
- 24-bit programmable-slope, w.r.t. temperature, current reference generator to temperature-compensate an internal ring-oscillator.
- A 32-byte calibration register programmable using SPI serial protocol.

## An Analog Front-End (AFE) Design for a PT-100 Temperature Sensor
Platinum Resistance (PT-100) temperature sensors are one of the most popular sensors for their accuracy and reliability. An Analog Front-End(AFE) is designed for a PT-100 sensor embedded in a temperature oven used for Integrated Circuit (IC) and electronic system temperature characterization. Python-based digital readout of the oven temperature is implemented using a PC-based, pocket-sized multi-instrument, [Analog Discovery 2](https://reference.digilentinc.com/reference/instrumentation/analog-discovery-2/start). This will enable our Lab to do industrial-grade temperature characterization from -40C till 125C. Some of the main features of this project are:
- Design of an Analog Front-End (AFE) for the PT-100 sensor to convert resistance to voltage, using off-the-shelf components.
- PCB for the design using the CAD tool Eagle.
- Developing an Python-based application for digital readout of the oven temperature.
- Developing Python- and LabView-based applications for characterization of ICs and electronic systems. 

## A SPI-based SRAM and Compact Bandgap Reference in 0.6um CMOS Technology
This was a turn-key project completely developed in-house with the help of 12 undergraduate students. This IC contained a serial accessible Static Random Access Memory (SRAM) using the SPI protocol. An innovative Bandgap voltage reference circuit that is trimmable using the I2C protocol. The IC has been fabricated and characterized for it's full functionality. Some of the design activities for this project were:
- Design and implementation of a compact Bandgap Voltage Reference circuit.
- A minimum-size six-transistor (6T) SRAM cell.
- Differential-Amplifier-based sense amplifier for readout.
- Compact decoder design for the memory selection.
- FSM design of the controller to generate all relevant control signals using the SPI clock.
- Design and layout of the SPI and I2C interface circuitry.


