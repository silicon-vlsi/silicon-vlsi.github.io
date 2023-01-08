---
sort: 2
---

# USER GUIDES

## Verilog RTL Coding Guideline

### REFFERENCES

- Michael Keating, "Reuse Methodology Manual"

- Mike Turpin, "The Dangers of Living with an X (bugs hidden in your Verilog)", ARM ltd.,
Cambridge, UK  http://www.arm.com/files/pdf/Verilog_X_Bugs.pdf

- Clifford E. Cummings, "SystemVerilog's priority & unique -- A solution to Verilog's "full_case" & "parallel_case" Evil Twins!"  http://www.sunburst-design.com/papers/

### PURPOSE/SCOPE

####   Purpose
      To define a unified set of Verilog RTL coding guidelines for the digital design team. Goals of this include:

Code is more readable by other members of design team
Avoid pitfalls of synthesizable RTL (i.e. synthesis vs simulation mismatches)
Aid in top level "stitching together" of sub-modules designed by various members of the team



####    Scope

      The following guidelines are largely based on the Reuse Methodology Manual (RMM) widely accepted by the industry.  Various other resources such as ARM and Cliff Cummings were also referenced as part of this set of guidelines. The idea is to provide enough of a framework to keep coding consistent without smothering each designer's freedom of expression.

###    BASIC CODING PRACTICES

3.1.1    General Naming Conventions

Use lowercase letters for all signal names, variable names, and port names
Use uppercase letters for names of constants and user-defined types
Use consistent name for the clock signal, such as 'clk'.  If there are multiple clocks in the design, use 'clk' as the prefix for all clock signals (i.e. clk1, clk2, or clk_adc, etc.)
Use the same name for all clock signals driven from the same source
For active-low signals, end the signal name with an underscore followed by a lowercase character (i.e. _b or _n). Use the same lowercase underscore character consistently to indicate active-low signals throughout the design.
Use a consistent name for reset signals (reset or reset_n).
When describing multibit buses, use a consistent ordering of bits such as [x:0]. When possible use these signal naming conventions:
ConventionUseStrictness*_rOutput of a register (i.e. count_r)Discretionary*_aAsynchronous signals (i.e. addr_strobe_a)Discretionary*_dnSignal used in the nth phase/stage/cycle (i.e. enable_d2)Required*_nxtData before being registered into a register with the same
nameRequired*_zTristate internal signalDiscretionaryModule file names should match the module declaration
Top level signal naming should be consistent to make top level connections more obvious.  For example naming sub-module ports that are to connect with other portions of the design at the higher hierarchical levels with
<source_module_trigram>_<signal_name>_<destination_module_trigram> (i.e.address bus
from DMA block to the OTP block could be 'dma_addr_otp').



3.1.2    Include Informational Headers in Source Files

   Include a commented, informal header at the top of every source file, including scripts. The header must contain:
o Legal statement: confidentiality, copyright, restrictions, on reproduction
o Filename
o Author
o Description of function and list of key features of the module
o Date the file was created
o Modification history including dat, name of modifier, and description of the change.

3.1.3    Use comments

   Use comments appropriately to explain design intent
   Use comments to explain ports, signals, and variables or groups of signals or variables.
Comments should be placed logically, near the code that they describe. Comments should be brief, concise, and explanatory. Insert comments before procedural statements, rather than embedded in it, in order not to interrupt the flow of the code.

3.1.4    Keep Commands on Separate Lines

   Use a separate line for each HDL statement.

3.1.5    Line Length

   Keep line length to 72 characters or less

3.1.6    Indentation

Use indentation to improve the readability of continued code lines and nested loops.
Use indentation of 2-4 spaces. Larger indentation (i.e. 8 spaces) restricts line length when there are several levels of nesting.
   Avoid using tabs. Differences in editors and user setups make the position of tabs unpredictable and can corrupt the intended indentation. If using emas with Verilog mode tabs will be replaced with spaces automatically.

3.1.7    Do Not Use HDL Reserved Words

   Do not use VHDL or Verilog reserved words for names of any elements in the RTL source files.

3.1.8    Port Ordering

Declare ports in a logical order, and keep this order consistent throughout the design. Declare one port per line, with a comment following it (preferably on the same line). For each interface, declare the ports in the following order:
o Inputs:
??????    Clocks
??????    Resets
??????    Enables
??????    Other control signals
??????    Data and address lines
o Outputs:
??????    Clocks
??????    Resets
??????    Enables
??????    Other control signals
??????    Data
   Use comments to describe groups of ports.
3.1.9    Port Maps and Generic Maps

   Always use explicit mapping for ports and generics, using named association rather than positional association.

3.1.10 Use Functions

   Use functions when possible, instead of repeating the same sections of code. If possible, generalize the function to make it reusable. For example, if your code frequently converts address data from one format to another, use a function to perform the conversion and call the function whenever you need to.

3.1.11 Use Loops and Arrays

Use loops and arrays for improved readability of the source code. For example describing a shift register, PN-sequence generator, or Johnson counter with a loop construct can greatly reduce the number of lines of source code while still retaining excellent readability
   Arrays are significantly faster to simulate than for loops. To improve simulation performance, use vector operations on arrays rather than for loops whenever possible.

3.1.12 Use Meaningful Labels

Label each process block with a meaningful name. this is very helpful for debug. For example, you can set a breakpoint by referencing the process label.

3.2    Coding for Portability:

3.2.1    Do Not Use Hard-Coded Numeric Values

   Example:
//Not Recommended
Wire[7:0] my_in_buss;

//Recommended
`define MY_BUS_SIZE 8 or parameter MY_BUS_SIZE=8; Wire[MY_BUS_SIZE-1 : 0] my_in_bus;

Constants are more intelligible as they associate a design intention with the value
Constant values can be changed in one place
Compilers can spot typos in constants but not in hard-coded values.

3.2.2    Constant Definition Files

   Keep constant and parameter definitions in one or a small number of files with names such as design_name_constants.v or design_name_parameters.v
Read pp.98 of RMM. This is conflicting and strange
3.2.3    Avoid Embedding Synthesis Commands

3.2.4    Use Technology-Independent

3.3    Guidelines for Clocks and Resets

3.3.1    Avoid Mixed Clock Edges

   Avoid using both positive-edge and negative-edge triggered flip-flops in the design.

3.3.2    Avoid clock Buffers

   Avoid hand instantiating clock buffers in RTL code.

3.3.3    Avoid Gated Clocks

   Avoid coding fated clock in RTL.

3.3.4    Avoid Internally Generated Clocks

3.3.5    Gated Clocks and Low-Power Designs

   If you must use a gated clock, or an internally generated clock or reset, keep the clock and/or reset generation circuitry as a separate module at the top level of the design. Partition the design so that all logic in a single module uses a single clock and a single reset.
   If your design requires a gated clock, model it in RTL using synchronous load registers. This will allow the synthesis tool to insert the actual clock gating logic.

3.3.6    Avoid Internally Generated Resets

   Avoid internally generated, conditional resets if possible. Generally, all the registers in the macro should be reset at the same time.
   If a conditional reset is required, create a separate signal for the reset signal, and isolate the conditional reset logic in a separate module.

3.3.7    Reset Logic Function

   The only logic function for the reset signal should be a direct clear of all flip-flops. Never use the reset inputs of a flop to implement a state machine functionality.

3.3.8    Single-Bit Synchronizers

   Use two flip-flop stages to transfer single bits between clock domains,.

3.3.9    Multiple-Bit Synchronizers

   Do not use multiple copies of a single-bit synchronizer to transfer multiple bit fields (such as FIFO address fields) between clock domains. Instead, use a reliable handshake circuit or multibit coding scheme such as a gray code.
3.4    Coding for synthesis

3.4.1    Infer Registers

3.4.2    Avoid Latches

   As an exception, you can instantiate technology-independent GTECH D latches. However, all latches must be instantiated and you must provide documentation that lists each latch and describes any special timing requirements that result from the latch.
   Avoid inferred latches by using proper coding techniques: having an else for if-then-else statements, using ternary ? operator in continuous statements instead of procedural with sensitivity list, etc. Also new in SystemVerilog is the always_ff and always_comb.

3.4.3    If You Must Use a Latch

   Design it such that it is testable (add a MUX to switch between test_in and functional data at the D input with test_mode as the select)

3.4.4    Avoid Combinational Feedback

   Avoid combinational feedback; that is the looping of combinational processes.

3.4.5    Specify Complete Sensitivity Lists

3.4.6    Blocking and Nonblocking Assignments

   When writing synthesizable code, always use nonblocking assignments in procedural always blocks.

3.4.7    Case Statements vs if-then-else Statements

   The multiplexer is a faster circuit. Therefore, if the priority-encoding structure is not required, it is recommended using the case statement rather than and if-then-else statement.

3.4.8    Coding Sequential Logic

   Code sequential logic, including state machines, with on sequential process. Improve
readability by generating complex intermediate variables outside of the sequential process with assign statements.
In SystemVerilog create enumerated types for the state vector
Keep FSM and non-FSM logic in separate modules if they have different synthesis requirements.

3.4.9    Coding Critical Signals

   Keep late-arriving signals with critical timing closest to the output of a logic block.  For example, use the late-arriving signal early in an if-else block.
3.4.10 Avoid Delay Times

   Do not use any delay constants in synthesizable RTL code

3.4.11 Avoid full_case and parallel_case Pragmas

   Write case statements that cover all cases with no overlap. Do not use the full_case or parallel_case pragmas, because these cause a difference in code interpretation between synthesis and simulation.

3.5    Partitioning for Synthesis

3.5.1    Register All Outputs

   For each sub-block of hierarchical macro design, register all output signals from that sub-block.

3.5.2    Locate Related Combinational Logic in a Single Module

Keep related combinational logic together in the same module

3.5.3    Separate Modules That Have Different Design Goals

   For instance area optimization vs speed optimization

3.5.4    Asynchronous Logic

Avoid asynchronous logic
If asynchronous logic is required in the design, partition the asynchronous logic in a separate module from the synchronous logic.

3.5.5    Arithmetic Operators: Merging Resources

   For synthesis tools to consider resource sharing, all relevant resources need to be in the same level of hierarchy that is, within the same module.

3.5.6    Partitioning for Synthesis Runtime

3.5.7    Avoid Timing Exceptions

Avoid multicycle paths and other timing exceptions in your design.
If you must use timing exceptions, use start and end points which are guaranteed to exist and be valid at the chip level.
   Avoid false paths in your design, instead use multicycle path

3.5.8    Eliminate Flue Logic at the Top Level

   Do not instantiate gate-level logic at the top level of the macro hierarchy.
3.5.9    Chip-Level Partitioning

   Make sure that the top level of the design contains only the I/O pad ring and clock generator.
The next level of hierarchy contains JTAG boundary scane modules and the core logic.  The clock generation circuitry is isolated from the rest of the design as it is normally hand crafted and carefully simulated.




3.6    Designing with Memories

   Keep memory interface pins at the top level of a macrocell to allow user choise of memory implementation , interface, and test.
   Only use synchronous memories for SoC design.




3.7    unwanted affects of X semantics (from ARM: The Dangers of Living with an X)

3.7.1    For if statements

Never use if statements in combinatorial logic (use case or ternary ? instead)
Only use if statements for sequential elements (i.e. flip-flop with asynchronous reset) Add X-checking assertions to a clock-gating enables in sequential logic e.g. if (enable)

3.7.2    For casex and casez statements

Never use casex (it's far too dangerous)
Avoid casez if possible (Z-wildcard doesn't propagate X's)

3.7.3    For case statements

Always add a default line (to avoid X-latching)
Only use the default to assign X's (to avoid X-optimism)
Never use explicit X's in case items
Cover all reachable 2-state values with case items
Avoid using case for one-hot multiplexers on critical path (use sum-of-products instead)

3.7.4    Reduce the number of reachable X's

Discourage the widespread use of X-assignments as synthesis don't-cares
Consider pre-minimizing essential don't-care X's prior to RTL verification
Avoid flip-flops that are not reset (only exception should be for large datapath registers)

3.7.5    Avoid synthesis/simulation specific workarounds that change semantics

   Never use full_case or parallel_case synthesis pragmas.  However SystemVerilog now has priority and unique.
   Avoid translate_off/on pragmas that change RTL simulations (e.g. for casez X-propagation)
   When you cannot follow these rules for any reason, us X-checking assertions and formal property checking to verify RTL.






