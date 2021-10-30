---
sort: 5
---
# RESOURCES

## LINUX BASICS
   
**X WINDOW SYSTEM**
   - XFree86 was the de facto X Server till 2004 after which a fork of it is maintained by non-profit X.Org foundation which is the predominant implementation now.
   - Key components of a X Window System:
     - _Display Manager (DM)_: Job is to authenticate users, log them in and startup initial environment using startup scripts. The DM also start the X Server.
     - _X Server_: defines an abstract interface to the system's bitmapped displays and input devices. It understands only basic drawing primitives over a network API which allows it to run on computers which are seprate from the client and, support variety of window managers and widgets.
     - _Window Manager_: which allows users to move, resize, minimize and maximize windows and allows use virtual desktops as well.
   - **Display Manger** presents the user with a graphical login screen which is not necessary and some users prefer to start X from the console or from their **.login** script by running **startx** which is a wrapper for **xinit** that starts the X server. **xdm** (X display manager) is the original DM but **gmd** (GNOME) and **kdm** (KDE) are the most popular ones now. Configuration files for **xdm, gdm** or **kdm** are in **/etc/X11**. The display manager's final duty is to execute **Xsession**, a shell script to setup desktop environment which is often found in **/etc/X11/{xdm,gdm,kdm}**. The **Xsession** also executes the user's personal **~/.xession**. All the commands are run in the background except the _last one_ which is the _Window Manager_. Once you exit the WM the DM logs the user out and the session ends.
  
   - To **run an X application**, clients must be told what display to connect to and what screen to inhabit on that display. Once connected, the clients must **authenticate** themselves to the X server. Note the X's intrinsic security is weak so most of the time it's tunneled securely eg. use SSH for X connection.

   - The X applications consults **DISPLAY** environment variable to find out where to display themselves. For example: ```export DISPLAY=servername.domain.com:10.2``` points X application at the machine `servername.domain.com`, display `10 and, screen `2`. 

   - For **client authentication**, the X display manager generates a large random number, called a _cookie_, early in the login process. The cookie for the server is written to the **~/.Xauthority**. Users can run the `xauth` command to view existing cookies and new ones. For example, if you get an error to open display on the client side ```client$ xprogram -display server:0```, probably the client doesn't have the _auth cookie_. On the server, you can run ```server$ xauth list``` to list all the cookies for each interface (ethernet, local, unix domain). The easiest way to add the cookie to the client is adding the cookie using **xauth** (when not using SSH, which negotiates the cookie autmatically for you): ```client$ xuath add server:0 MIT-MAGIC-COOKIE-1 <cookieFromServer>```.

    - **SSH** can forward arbitrary network data including X protocol data, over a secure channel. Since SSH is X-aware, you gain some additional features, including a pseudo-display on the remote machine and the negotiated transfer of magic cookies. You typically **ssh** from the machine running X-server to the machine running the X programs. This arrangement can be confusing to read about because the SSH _client_ is run on the same machine as the X _server_, and it connects to to an SSH _server_ that is on the same machine as the X _client_applications. To make it worse, the virtual display that SSH creates for your X server is local to the remote system. The _DISPLAY_ variable and authentication information are setup automatically by **ssh**. The display number starts at `:10.0` and increments by one for each SSH connection that is forwarding X traffic. You can see the transaction details during a connection using the verbose option '-v': ```x-server$ ssh -v -X x-client.domain.com```. If things are not working properly, along with the verbose output, check the global setting in **/etc/ssh** to make sure X forwarding has not been disabled.
   
    - X server **Xorg** configuration was once notoriouly difficult to configure for a given hardware but lot of effort has been put to make Xorg work out of the box. If your system is running without a Xorg configthen great! Most probably it's using the KMS module. The **Xorg** configuration is typically located in **/etc/X11/xorg.conf**.


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
