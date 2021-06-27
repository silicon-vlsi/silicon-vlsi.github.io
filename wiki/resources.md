---
sort: 5
---
# Resources

## VLSI Design Preparation

### Digital Design - 1

- **MUX-Based Circuits**
  - Designing different logic gates using MUX: variants -> effect of swapped inputs, certian input of zero, same logic with different approaces.
  - Desiging sequential circuit (Latch or Flipflop) using MUX
    - Master-slave flipflop concept 
    - Different types of latches: Positive triggered / Negative triggered
    - Flipflop using only positive latch, only negative latch , 1 positive and 1 negative latch
  - 4x1 MUX using 2x1 MUX : 
    - variants -> swapped input, swapped MSB/LSB
    - Which one has more gates 4X1 Mux direct Vs 4x1 using 2x1?
      - This question can be extended till transistor level.
      - Bubble reduction also come into the picture at this place.
      - NAND/NOR how it can be converted into different Inverted input OR/AND
  - Designing MUX using transistors: Only PMOS, Only NMOS, CMOS, Pass-transistor, Transmission gate.

- **Decoder Based Circuit**
  - 4:16 or 3:8 using 2:4 decoder.
    - Importance of Enable Signal
    - From Memory design point of view, they check how candidate knows concepts
    - Gate count / Transistor count – Interviewer can go till that level. How to optimize numbers. What’s the effect of those numbers?
    - Realization of different circuit using Decoder

- **Gate-count /Transistor count related concepts**
  - From Area point of view – how it will affect the design.
    - Sometime more overall area is also good for design (if there are small -small units) – How and why?
  - From Power point of view which one is best solution: Current / Power loss and all those concepts 
  - From Timing point of view which one give more time and which configuration will give less time.
  - Gate delay point of view

- **Finite-State Machine (FSM)**
  - FSM problem will be asked to solve and observe the approach of how you design state diagram, state table, the transition table and KMAP 
    Counter / Sequence detector all these can easily designed using FSM concepts but most of the students remember this as direct circuit.

- **Flip-flop (Part 1)**
  - Setup and Hold Concepts of Flipflop
  - Propagation delay of Flip-flop
  - Frequency at which circuit can work. Flipflop conversion can be asked here. Like what’s the minimum frequency of a circuit where output of D Flipflip is feedback to input of D flipflop.
  - Frequency divider circuit, frequency multiplier circuit – can be termed as clock generators.
  - Timing cpnepts related to Static Time Analysis (STA)

- **Flip-flop (Part 2)**
  - 1-bit register, 2-bit register
  - Different type of registers (shift registers)
  - Concepts of FIFO, Pipeline, Parallel In-Serial out and others
  - Application of these in design.
  - This can be asked as design of 16-bit adder using 4-bit adder
    - 2 approaches
      - Using 4, 4-bits adder and some connection and other logic gates
      - Using 1, 4 bits adder and Register configuration
      - Interviewer need to see your approaches and concepts of Clock and cycle time saving concepts 

- **Propagation Delay, Transition delay**
  - Logic Gates
  - Flipflop, Latches
  - Rise time – Fall time – Capacitance concepts – current concepts
  - Interviewer can shift to Transistor level from here.

- **Noise Margin concepts**
  - Input-Output Voltages, Current levels (VOL,VOH, VIL, VIL and similarly for current)
  - Based on these – Noise margin concepts
  - Compatibility of gates based on their input and output specifications. 

- **Tristate buffers, Tristate Inverters**
  - Use model, Transistor level circuit
  - Truth Table

- **Clock Gating circuit**
  - Basically, use of AND / OR/ MUX at this place.
  - Sequential circuit-based Clock Gating circuit (Latch based, Flipflop Based)
  - Waveform of Clock and input and output

- **Different Timing waveform-based concepts**
  - Input and Output waveform of Logic gates
  - Input and Output waveform of Flipflops, Shift registers, counters, Mod-counters
  - Propagation delay concepts in those waveforms
  - Through these they need to understand Glitches and Hazard concepts

- **High-tie, Low-Tie concepts**
  - How it effects circuits?
  - Transistor level implementations
  - Sometime they can link this till power
