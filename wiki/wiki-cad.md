---
sort: 2
---

# CAD 
This wiki contains all the details (except the private and propreitary info) for the CAD tools used at Advanced VLSI Lab at SIT, BBSR.

## CADENCE

### Ocean

**Quick Command Reference**

- `openResults("/simDir/input.raw")`: open the simulation results from the simulation directory.
  - `results()`: Shows available results in the current opened result.
- `selectResult('tran/'ac/etc)`: select one of the available results.
  - `outputs()`: shows the available output nodes in the current selected simulation type (ie. ac/tran/etc).
- `plot(v("out1") v("out2") ?expr list("legend1" "legend2")`
- `plot(vout ?strip list(2))`: Puts the plot in the current window but second strip. Helpful when you have a bunch of plots and you want to add another one in a separate strip.

**Procedure**:

```
procedure( procName(arg1 arg2)
    prog( (localVar1 localVar2)
        <ocean statements>
        return(var)
    )
)
```

**Ocean Functions**
- **gainMargin**: `getData("gainMargin" ?result "stb_margin")`
  - **NOTE** For PSTB, you need evaluate the above function at the 0th harmonic ie. `value(getData("gainMargin" ?result "pstb_margin") 0)`.
- **phaseMargin**: `getData("phaseMargin" ?result "stb_margin")`
  - See note for gainMargin.
- **stb/pstb loopGain**: `getData("loopGain" ?result "stb/pstb")`
- **Onoise**: `getData("out" ?result "pnoise/noise")`
- **DC Operating Points**: `pv("I0.I1.Mdev1/V1" "id/vth/vds/i/etc" ?result "dcOpInfo" ?resultsDir "sims/input_0.raw")`
- **Integrated Nosie**: `sqrt(integ(v("/out")**2))`
- **Noise Summary**: `noiseSummary('spot/integrated ?resultsDir "sims/input.raw" ?result 'noise ?output "noiseSum.txt" ?frequency 1 ?noiseUnit "V" ?truncateType 'top ?truncateData 16 ?deviceType 'all)`
  - **NOTE1** you can extract the summary of one of the sweep values by passing `?paramValues 1.0(say)`. See `noiseSummarySweep()` to see the usage.
  - **NOTE2** When the __noise Unit__ is set to `V`, the percentage reported is as percent of total power ie. v_n = \sqrt{x} v_t. where v_n is the noise reported noise of each device in V, x is the reported percentage and v_t is the total power in `V`. Therefore, when plotting the contribution in `V`, the differences are compressed due to the square-root relation.
    * **NOTE-3 (Weight File Format)**: The weight file can be magnitude(mag)(**IMPORTANT: weight of the noise power**) or decibels(dB) format:

```
mag
10, 0.1
20, 0.12
.
```

```
db
10, -20
20, -30
.
```

**EXAMPLES**:
- `noiseSummary( 'spot ?resultsDir "/usr/simulation/lowpass/spectre/schematic" ?result 'noise ?frequency 100M )`
  - Prints a report for a spot noise analysis at a frequency of 100M.
- `noiseSummary( 'integrated ?resultsDir "/usr/simulation/lowpass/spectre/schematic" ?result 'noise )`
  - Prints a report for an integrated noise analysis for the results from a different run (stored in the schematic directory).
- `noiseSummary('integrated ?truncateType 'none ?digits 10 ?weightFile "./weights.dat")`
  - Prints the weighted noise for an integrated noise analysis using information in the weight file weights.dat.
- `noiseSummary('integrated ?output "./NoiseSum1" ?noiseUnit "V" ?truncateData 20 ?truncateType 'top ?from 10 ?to 10M ?deviceType list("bjt" "mos" "resistor"))`
  - Prints a report for an integrated noise analysis in the frequency range 10-10M for 20 components with deviceType bjt, mos or resistor.
- `noiseSummary( 'integrated ?from 1 ?to 100M  ?truncateType 'top ?truncateData 20 ?deviceType 'all ?noiseUnit "V^2" ?output "./filename.ns" ?paramValues list(2.47e-9))`
  - Prints a report for an integrated noise analysis at a specific swept value.
- **Unity GainBandwidth**: `cross(db20(getData("loopGain" ?result "stb")) 0 1 "either")`
- **3-db bandwidth**: `bandwidth(v("output") 3 "low")`
- **delay**: `delay(?wf1 wave1 ?value1 thresh1 ?edge1 'rising ?nth1 nth_rise_edge ?wf2 wave2 ?value2 thresh2 ?edge2 'rising ?nth2 nth_rise_edge )`
- **ocnPrint**: `ocnPrint(?output "out.dat"|pport wave1 wave2 .. ?numberNotation 'engineering)`
  - Do not confuse this with the local corner script 'OcnPrint'
- **display**: `display(object)` eg. display(gainMargin)
- **printf**: C-like.

[FIXME: change the rest of the Ocean formating from dokuwiki to markdown]

===== Functions for OcnPrint =====
To use the functions, first load in your icw window or put in your .cdsinit:
<code>
load("/home/srout/work/ocean/functions/freqUsedFns.il")
</code>
The following functions will be loaded:
==== stbSummary ====
<code>
stbSummary(?stbSim 'stb ?DCgainFreq 100 )
</code>
**Description**: This function when passed to ''OcnPrint'', it returns a string for each corner containing the Phase Margin, Gain Margin, Unity Gain Bandwidth and the DC gain.\\
**Arguments**:
  * ''?stbSim'' : The simulation analysis type ie. ''stb''(default) or ''pstb''.
  * ''?DCgainFreq'' : The frequency (in Hz) at which the gain is calculated. default: 100
**Output**: string eg. '' "PhaseMargin(deg)\t GainMargin(dB)\t DCgain(dB)\t 3-db Bandwidth(Hz)\t  Unity-Gain Bandwidth(Hz)" ''\\
**Example**:
<code>
stbSummary()
</code>
Will return stability results for the default settings ie. analysis type ''stb'' and and DC gain at 100Hz
<code>
stbSummary(?stbSim 'pstb ?DCgainFreq 1)
</code>
Will return results for a ''pstb'' simulation and DC gain at 1 Hz.

==== fnNonLin() ====
  * **FUNCTION**: ''fnNonLin(<output signal name> <input signal name> <frequency(Hz)> <start time> <end time> ?parametric nil)''\\
  * **EXAMPLE**:
    * Ocean:<code> fnNonLin(v("vout") v("vin") 1e7 1e-3 5e-3 ?parametric nil) </code>
    * ADE-1: <code>fnNonLin(VT("/vout") VT("/vin") 1e7 1e-3 5e-3 ?parametric nil) </code>
    * ADE-2: <code>fnNonLin(VT("/vout") vin_amp 1e7 1e-3 5e-3 ?parametric t) </code>
      * FIXME : Need to verify the ADE-2 code. When in doubt use the ADE-1 method.
  * **INPUT**: Either the a parametric simulation from ADE or a transient sweep using Spectre netlist.
  * **OUTPUT**: Plot with x-axis as the input amplitude and y-axis is non-linearity in percentage.
  * **DESCRIPTION**: This method calculates non-linearity of a circuit after running a sweep of different amplitude input signal and looking at the fundamental component of the output. The function linearizes about the two extreme points of the output and finds the error in percentage of the max output.
  * **NOTES** 
    * This is the same function as used in the parametric sweep, except the option ''parametric'' is set to **nil** to indicate it is NOT a parametric sweep and therefore the second variable is interpreted as the input voltage instead of the sweep variable name.


## CORNER SIMULATION SCRIPTS

**Quick-Start**
- Follow the instruction below to copy some of the example files to your work location.
- To create the corner-list file use the following command:
  - `createCorners TB-IO.par > TB-IO.clist`
- To create all the corner netlists in the directory say `cnrSims` with their appropriate corner variables:
  - `createEldoRuns TB-IO.clist TB-IO.cir -simdir=cnrSims`
- `cd cnrSims` to run the simulations.
- To test or debgug one corner say first corner: `./runsim 0`
- To run all corners: `./runsim all`
  - **IMPORTANT** Don't kill the simulations by pressing `Ctrl-C`, instead use the `kill` command OR `pkill eldo` and repeat till corners are killed.
- After running all the corners, use the script `xtractEldoRuns` to extract all the measured parameters from the corners runs and report in `.csv` format along with the min/max for each measures.
- Below shows the detail description of the corner scripts.

**/CAD/apps7/bin/createCorners**

This is a simulation independent script which generates a _corner list file_ from a simple input file. We will go through a simple example to demostrate the script. Before that, let's create a working directory say `~/work/sim` and `cd` to it. Let's copy few files to it:
- `cp /CAD/apps7/msim/TB-xt018-IO_CELLS_FC5V-BBSUD4FC.cir TB-IO.cir`
- `cp /CAD/apps7/bin/createCorners.par TB-IO.par`
- You can check the content of the parameter file `TB-IO.apr`:

```
// Example Parameter file
temp    20,125
VDDA    [4:1:5]
library default;3s;tm,default;3s;wp
{CLOAD,CZI}     1p;0.1p,10p;0.25p
//
simCMDoptions -alter 1 -aex
``` 

- Brief description of the parameter file:
  - `//` Comments start with two forward slashes.
  - `temp 20,125`: `temp` is a __reserved__ keyword for varying __temperature__. Values are seprated by __commas (,)__. For example in `Eldo/HSpice` this will result in two temperature: `.TEMP 20` and `.TEMP 125` 
  - `VDDA [4:1:5]`: `VDDA` is netlist parameter. Here the values are given as vector range `[<start>:<increment>:<stop>]`. This example will result in two values `VDDA=4` and `VDDA=5`.
  - `library default;3s;tm,default;3s;wp`: `library` is a __reserved__ keyword for varying library calls primarily __process__ variation. Since in a netlist there can be multiple library calls, the values are seprated by __semi-colon(;)__ and the corners are separated __commas (,)__. In this example when used with `TB-IO.cir` will result in following two corners:
  - Corner-1:
    - `.LIB <PATH>/config.lib default`
    - `.LIB <PATH>/param.lib 3s`
    - `.LIB <PATH>/xt018.lib tm`
  - Corner-2:
    - `.LIB <PATH>/config.lib default`
    - `.LIB <PATH>/param.lib 3s`
    - `.LIB <PATH>/xt018.lib wp` 
  - `{CLOAD,CZI}     1p;0.1p,10p;0.25p`: If you want to vary more than one variable for each corner, you define a composite variable like this with the values for each corner seprated by __semi-colons (;)__ and corners serprated by __commas (,)__. In this example for `Eldo/HSpice` will result in two corners:
    - Corner-1: `.PARAM CLOAD=1p CZI=0.1p`
    - Corner-2: `.PARAM CLOAD=10p CZI=0.25p`
  - The __ORDER__ of the variables are important. The varaiation starts from top to botomm for eg. in the above case, temperature will varied first, then `VDDA`, then `library` and so on.
  - `simCMDoptions -alter 1 -aex`: `simCMDoptions` is a _optional_ __reserved__ keyword which is passed to the simulator command-line option.

- Now you can create the _corner-list file_ by running the command:
  -`createCorners TB-IO.par > TB-IO.clist`
- If you view the corner-list file `TB-IO.clist`, the content will be something like this:

```
## Input Parameter file: "TB-IO.par",  Date/time: 12:20:6, 4-25-2023
## Corner summary:
##  temp: (2) 20 125
##  VDDA: (2) 4 5
##  library: (2) default;3s;tm default;3s;wp
##  {CLOAD,CZI}: (2) 1p;0.1p 10p;0.25p
## Total number of simulations: 16
##
## Simulator command-line options:
simCMDoptions: { -alter 1 -aex}
## Corner variables:
cnrVars: {CLOAD,CZI,library,VDDA,temp}
## Simulation cases:
cnrSim[0]: {1p,0.1p,default;3s;tm,4,20}
cnrSim[1]: {1p,0.1p,default;3s;tm,4,125}
cnrSim[2]: {1p,0.1p,default;3s;tm,5,20}
         :
         :
         :
cnrSim[14]: {10p,0.25p,default;3s;wp,5,20}
cnrSim[15]: {10p,0.25p,default;3s;wp,5,125}
```
- Brief description the corner list file:
  - First few commented (`#`) lines show the summary: no of variables, how many variations and total number of simulations. 
  - line starting with `simCMDoptions` contain anu simulator command-line options.
  - `cnrVars` show all the corner variables.
  - lines starting with `cnrSim[#]` shows the corner numer (`#`) followed by all the variables of the corners.
- The corner-list file will be used by a _simulation-specific_ script to generate all the corner netlist along with a shell-script to run those corners.


**/CAD/apps7/bin/createEldoRuns**:

- For `Eldo`, use `createEldoRuns` as follows:
  - `createEldoRuns TB-IO.clist TB-IO.cir -simdir=cnrSims`
  - This will create all the corner netlists in the directory `cnrSims` with their appropriate corner variables.
  - It will also create a shell-script `runsim` that you can run to simulate a single corner `./runsim 0` or all corners `./runsim all`


**/CAD/apps7/bin/xtractEldoRuns**:

This script is used to extract results from a Eldo corner run and display them in a `.csv` format. This script requires two files: the _Eldo netlist_ that contains all the `EXTRACT` statements and the _corner-list_ file that was created by the script `createCorners`.  You also should have run all corners using the `createEldoCorners`.

- _Usage_: `xtractEldoRuns <corner-list file> <netlist> [-simdir=<SIM DIR> typCnrNum=<CnrSimNum> -outCol -ext=aex ]`
  - `<corner-list>`: Name of the file containg all the corner details created using `createCorners`
  - `<netlist>`:  The Eldo/Spice netlist from which the `EXTRACT` stmnts are read from.
  - `-simdir=<>`:  [Optional] argument for specifying the directory with all the corner runs.  Default: Current directory.
  - `-typCnrNum=<>`: [Optional] arg to specify the corner number which will be considered as typical corner. Default: 0
  - `-outCol`:    Optional arg, if set, each corner will be displayed as a coloumn instead of a row (default)
  - `-ext=<str>`: Optional arg to change the extension of the file from which the `EXTRACT` labels are read. Deafult: aex
- _Example_: `xtractEldoRuns TB-IO.clist TB-IO.cir -simdir=cnrSims -typCnrNum=6 -outCol -ext=log > TB-IO-tranient.csv`

## OPEN-SOURCE CUSTOM DESIGN FLOW 
This section contains the instruction to setup open-source CAD tools (ngspice, Sue2, xschem, magic & netgen) in a Virtual Machine (Virtual Box) running a Ubuntu-based Linux distribution LXLE. Although the binaries of the CAD tools are compiled and tested on a 64-bit LXLE Linux distribution, it should run on other 64-bit Ubuntu or Debian based distribution like Xubuntu.

- [**YouTube PLaylist-OpenSourceEDA**](https://www.youtube.com/playlist?list=PL7R2OODNugWFY2qeZ7qlVFNIkN8ABhuSO):  Santunu Sarangi, Satabdi Panda and Pracheeta Mohapatra, "[15VLSI7T](https://github.com/silicon-vlsi/15VLSI7T):VLSI Design Course for 7th Sem AEI, 2020" -- Set of detailed videos of Labs conducted for the course using ngspice, sue2, magic and netgen.

### SETTING UP LXLE LINUX ON VIRTUAL BOX
- [**Linux on Virtual Box**](https://www.dropbox.com/s/2lovix0ntsw8yfw/2020-0917-Open%20Source%20EDA%20Setup.pdf): Step-by-Step instruction on how to setup LInux LXLE on Virtual Box. 
- Here are some of the essential steps:
  - Download and install Virtual Box (with default settings) from http://virtualbox.org
  - Download the 64-bit Linux LXLE Iso Image from http://www.lxle.net/download (eg. lxle-18043-64.iso). **NOTE** The CAD tools are all compiled for 64-bit OS.
  - Start Virtual Box and click **New** and follow the steps to complete the installation. Check the above [doc](https://www.dropbox.com/s/2lovix0ntsw8yfw/2020-0917-Open%20Source%20EDA%20Setup.pdf)

**DOWNLOAD AND SETUP THE CAD TOOLS**

[**YouTube Link**](https://www.youtube.com/watch?v=GUHCrM-v24w) demonstrating the download and install of the following tools:

- [**NGSPICE**](https://github.com/silicon-vlsi-org/eda-ngspice): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-ngspice#downloading-&-setting-up-ngspice).
- [**SUE2**](https://github.com/silicon-vlsi-org/eda-sue2Plus): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-sue2Plus)
- [**XSCHEME**](https://github.com/silicon-vlsi-org/eda-xschem): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-xschem#downloading-&-setting-up-xschem)
- [**MAGIC**](https://github.com/silicon-vlsi-org/eda-magic): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-magic#downloading-&-setting-up-magic)
- [**NETGEN**](https://github.com/silicon-vlsi-org/eda-netgen): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-netgen#downloading-&-setting-up-netgen)
- [**TECHNOLOGY**](https://github.com/silicon-vlsi-org/eda-technology): See Download and Setup instruction in the [gitHub page](https://github.com/silicon-vlsi-org/eda-technology)

## CLOUD-BASED MULTI-USER OPEN-SOURCE LAB (CMOSLab)

The following instuction illustrates the steps to setup open-source EDA tools (ngspice, sue2, magic, netgen, etc.) on a Linux Virtual Machine in cloud. The steps illustrate **Ubuntu 18.04** with **LXDE display manager** on a [**Digital Ocean**](https://digitalocean.com) Droplet (VM).

- Create a Virtual Machine (VM) or Droplet in Digital Ocean with password option for `root`.
- Login to the instance as root using PuTTy (or anything equivalent).
- Update the distro: `#apt update; apt upgrade`
- create a user for admin purpose: `#adduser <user>`
- Give the user `sudo` power: `#usermod -a -G sudo <user>`
- Install the **LXDE** Display Manager and it's depndencies+goodies: `#apt install lxde`
  - This will create all the necessary configs and session in `/etc/X11` eg. `Xsession` etc.
- Install the **tightvncserver**: `#apt install tightvncserver`
- Test vncserver by loging in as the user and set the vnc password: `$vncpasswd`
  - This will create `$HOME/.vnc` with a `xstartup` file with the LXDE Xsession.
- Start the vncserver: `$vncserver -depth 24 -geometry 1280x720 :1`
  - This will start the vncserver on port `5901` (5900+1)
- Use vnc client eg. `tightvnc` to connect to the instance using the <IP addre>:1 and the passwd set by `vncpasswd`.
- Install `wish` for `Sue2`:`apt install wish`
- Now clone all the EDA tools from github in the root location eg. `/cad` and tech/pdk in `/tech`
- Add all the env variables in `/etc/skel/.bashrc`
- Create all users and setup each users. For creating batch users, use ```newuser``` command, see [this article](https://www.tecmint.com/create-multiple-user-accounts-in-linux/)
- **CREATE SNAPSHOT** to use it to replicate VMs

**ALTERNATIVES/ETC**
- **Xfce4** is another alternative to **LXDE** but sue2 schematics are blank in xfce4 so we decided on LXDE.
  - Install packages `xfce4 xfce4-goodies` and some optional packages depending on your need eg. `xorg dbus-x11 x11-xserver-utils`
- [**noVNC**](https://novnc.com) is an alternative VNC client that connect through a webbrowser without having to download a client app.
  - git clone the [repo](https://github.com/novnc/noVNC)
  - Use the `novnc_proxy` script to automatically download and start websockify: `./utils/novnc_proxy --vnc localhost:5901`
  - Make sure the vncserver is running and the above script should output an URL that you can navigate to connect to the instance. Use the vnc password to authenticate. Rememeber to substitute the hostname with the Public IP.

- To **start vncserver at boot**, followed the instruction from [here](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04)
  - Create a service file for each user (see above doc): ```$ sudo vim /etc/systemd/system/vncserver-user1@.service```
  - Make system aware of the file: ```$ sudo systemctl daemon-reload```
  - Enable the unit file (@1 is the display number): ```$ sudo systemctl enable vncserver@1.service```
  - Start the service: ```$ sudo systemctl start vncserver@1```
  - You can see the status: ```$ sudo systemctl status vncserver@1```
  - Now it should start automatically on reboot.
  - Follow above steps for wach user
- If you want to connect using the **Windows RDP protocol** (For some reason was very slow for us):
  
```bash
sudo apt install xrdp;
sudo systemctl status xrdp;
sudo adduser xrdp ssl-cert;
sudo systemctl restart xrdp;

sudo ufw allow from 192.168.1.0/24 to any port 3389;
sudo ufw allow 3389
```

**DEVELOPER READY**
  
- Following packages were required to compile source codes for all the EDA tools:
  - ```sudo apt install build-essential linux-headers-‘uname -r‘```
  - ```sudo apt install bison flex libx11-dev libxaw7-dev libtool tcl-dev tk-dev tcsh tclsh tcllib wish libreadline-dev libncurces-dev```
  - ```sudo apt install automake autoconf texinfo``` (required for ngspice git repo )
- Additional packages needed for various other tools:
  - ```libffi-dev git graphviz xdot pkg-config python3 libglu1-mesa-dev freeglut3-dev```
  - ```clang-6.0 gsl-bin libgsl0-dev m4 swig tcllib```
  
- **RESOURCES**
  - [Tutorial: Installing and configure VNC on Ubuntu 18-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04)
  - [How to Create Multiple User Accounts in Linux](https://www.tecmint.com/create-multiple-user-accounts-in-linux/)
  - [Headless X Session with x11vnc](https://jasonmurray.org/posts/2021/x11vnc/)
  
## XILINX DESIGN SUITE
  This section will provide the steps to create a new project in Xilinx Design Suite

**Step 1:**
- Open Xilinx and create a ```New Project``` from the file menu.
- Specify the name and path of the project you want to create and set the "Top level source type: HDL(Read Only)".
- Under the **New Project Wizard** window, change the project specifications according to the board.
- Some of the general specifications are:
  - Property Name: Value
  - Evaluation Development Board: None Specified
  - Product Category: All
  - Family: Spartan6
  - Device: XC6SLX9
  - Package: TQG144
  - Speed: -3
  - Top-Level Source Type: HDL
  - Synthesis Tool: XST(VHDL/Verilog)  
  - Simulator: ISim(VHDL/Verilog)
  - Preferred Language: Verilog
  - Property Specification in Project File: Store all values
  - VHDL Source Analysis Standards: VHDL-93
  - Manual Compile Order: **Leave the box(Y/N) unchanged**
  - Enable Message Filtering: **Leave the box(Y/N) unchanged**
 
**Step 2:**
- A new project navigation window will appear with desired file name.
- In project navigation window, right click on the hierarchy code ```your file name``` and click on ```New Source``` and specify the name of the program.
- After clicking next a **New source wizard** window will appear. 
- In **New source wizard** window, click on ```Verilog Module```. Provide the file name and location and then click on ```Next```.
- In the next window provide the input, output or bus ports(MSB/LSB) required in the code(optional).
- In "New Source" window, write the respective verilog code and click on ```Save```.
- Right click on the source file and click on ```Set as Top Module```. This enables us to test and synthesize the code completely, otherwise only syntax check is possible. If its already set then skip the step.  
- Click on ```yes and continue```.
- Click on ```Check Syntax``` in **Process Window** under ```Synthesis XST``` and run other simulation options.

**Step 3:** 
- After syntax check, right click on the Verilog file name in the hierarchy and click on ```New Source```.
- Click on ```Implementation Constraints File```. This enables us to create a new User Constraint File(.ucf).
- Provide a name for the ucf file and click on ```Next```.  
- Under **Process Window**, click on ```Edit Constraints``` to edit the UCF.
- Edit the ucf providing all the required inputs and outputs providing the respective ports and ```Save``` the file.

**Step 4:**
- Under **Process Window**, right click on ```Generate Programming File``` and click on ```Rerun all```. This will lead to the synthesis of the code. 
- Now click on ```Configure Target Device```.
- A dialogue box will appear click on ```Ok```.
- An **ISE Impact** window will appear.
- On the top-left corner click on the ```IMPACT Flows```.
- Now double click on ```Boundary Scan```.
- Right click on the center of the window and click on ```Initialize Chain```. This will establish a relation between the PC and the FPGA board.

**Step 5:**
- Open the AHMY software from the task bar. 
  - Click on ```Connect```.
  - Click on ```Browse``` and open the .bit file created in the specified location.
  - Click on ```Show```.
  - Then click on ```Download```.
  - Switch on the respective switches on FPGA Board used for implementation of the design.
  
## PCB DESIGN USING EAGLE

Eagle is a popular electronic design automation (EDA) software that is widely used in the industry for designing printed circuit boards (PCBs). Follow the steps instructions to get started with Eagle software.
  
**Step 1: Download and Install Eagle Software**
- [Go to the Eagle website](https://www.autodesk.com/products/eagle/free-download) and click on the **Download Now** button.
- Create an account or sign into your existing account.
- Select the appropriate version of Eagle for your os (Windows, Mac, or Linux).
- Follow the on-screen instructions to complete the installation.
  
**Step 2: Creating a New Project**
- Open the Eagle software.
- Click on **File** and then **New** to create a new project.
- Enter a name for your project and choose a location to save it.
- Its a good practice to add description to your project do that by Right Click > Edit descripton
- Select a schematic template for your project.
  
**Step 3: Drawing Schematic**
A PCB design schematic can be described as a circuit diagram or the functional diagram of electronic circuits. Symbols are used to represent components and show how they are connected electrically.
- Open the schematic editor by clicking on **File** and then **New Schematic**
- Use the tools on the left-hand side of the screen to draw the components of your circuit. You can also use the search function to find specific components.
- Eagle comes with a built-in library of components that you can use in your designs but you can also create your own components or import them from other sources.
- Download [Adafruit & SparkFun](https://www.autodesk.com/products/fusion-360/blog/library-basics-install-use-sparkfun-adafruit-libraries-autodesk-eagle) library to get all the components.
- After downloading click on **Library** then select **Open library manager** Browse to the downloaded library and then hit **update all** in Library Menu.
- Use **Add Part** tool to get the components or just do `ctrl+L` to activate the command line mode and type **add** then a pop window will appear showing the components list.
- The most used tools include **Add**, **Move**, **Rotate**, and **Delete**.
- To do the above operations, Click on the red colour plus(+) sign which is present either near the specific component or in the center of the component.
- Use the **Net** tool or type **net** to draw wires between the pins of each component. Make sure to label each net with a unique name to avoid confusion.
- Value and labels provide information about the components in your circuit. To add value and labels to your schematic, use the **Value** and **Label** tools.
- Save your schematic by clicking on **File** and then **Save**.
- After completing all, click Tools and do Electric Rule Check(ERC). 
- After completing the schematic, if you want to generate a netlist. A netlist is a list of all the components and their connections in the circuit. Click on **File > Export > Netlist** and save the file.
- **Some hotkeys for drawing schematic are as follows:**
  - Add Part: `ctrl+shift+A`
  - Copy a component: `ctrl+shift+C`
  - Delete a component: `ctrl+D`
  - Move: `ctrl+M`
  - Rename a component: `ctrl+shift+N`
  - Change value of component: `ctrl+shift+v`
  - To run ERC: `ctrl+shift+E`
  - To see the error: `ctrl+E`
- **To transfer the schematic into layout in the schematic editor go the *File* and then *Switch to board***
  
**Step 4: Designing the PCB**
Once you have created a schematic, the next step is to design the PCB board or layout.
- Open the PCB editor by clicking on **File** and then **Switch to Board**
- Click on **File** and then **Import** to import your schematic.
- Use the tools on the left-hand side of the screen to place the components on the PCB.
- Plan a floor plan to place the components in their suitable places to use more effective area.
- Use the **Route** tool to connect the components with copper traces.
- Eagle provides 2-layer PCB in the free version in which you can route both in top and bottom layer.
- Add vias and through-holes as needed.
- Some more features are Polygon, ratsnest, renaming and changing the value all you can do using the tools available on the left hand side of the screen.
- Save your PCB design by clicking on **File** and then **Save**
- Before finalizing the design, check the design rules. Design rules ensure that the board meets the manufacturer''s specifications. Click on **DRC** and run the check.

  
**Step 5: Exporting the Design**
After completing the design, export it in the required format. Gerber files are the standard file format used by manufacturers. Click on **File > Cam processor** and save the files.
  
** RESOURCES **
- Great tutorials at [Sparkfun](https://sparkfun.com)
  - [Eagle Schematic](https://learn.sparkfun.com/tutorials/using-eagle-schematic/all)
  - [Eagle PCB Layout](https://learn.sparkfun.com/tutorials/using-eagle-board-layout)
  - [Install and Setup Eagle](https://learn.sparkfun.com/tutorials/how-to-install-and-setup-eagle)
  - [Creating SMD PCB](https://learn.sparkfun.com/tutorials/designing-pcbs-advanced-smd)
  - [Creating SMD footprints](https://learn.sparkfun.com/tutorials/designing-pcbs-smd-footprints)
  - [Creating Custom Footprints](https://learn.sparkfun.com/tutorials/making-custom-footprints-in-eagle)

## OpenROAD INSTALLATION

**RESOURCES**
- [OpenROAD Flow Scripts Tutorial](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#configuring-the-design)
- [Vijayan's Video Tutorials](https://drive.google.com/drive/folders/1gAh5-9hbRfipVhKopFb635S3WDPAS3_k?usp=sharing)

**CentOS 7 LOCAL INSTALLATION**

- `cd; mkdir ORFS; cd ORFS`
- `git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts`
- `cd OpenROAD-flow-scripts`
- `sudo ./etc/DependencyInstaller.sh`
- `source /opt/rh/devtoolset-8/enable`
- `source /opt/rh/llvm-toolset-7.0/enable`
- `./build_openroad.sh --local`
  - This will locally install all tools in the `tools` folder.
- `source ./setup_env.sh` to load the environement in a `bash` shell. You will see a following message:
  - `OPENROAD: <path>/OpenROAD-flow-scripts/tools/OpenROAD`
- You can follow the [OpenROAD Flow Scripts Tutorial](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#configuring-the-design) to start and continue with the flow.
- For a quick check of the flow:
  - `cd flow`
  - `make DESIGN_CONFIG=./designs/sky130hd/ibex/config.mk` 
  
**SYSTEM-WIDE INSTALLATION**

- After compiling the tools, `tar` the `OpenROAD-flow-scripts` directory and copy to the `srv03.vlsi.silicon.ac.in`
- Digital tools are installed in `/CAD2` which is mounted from `srv03` from `/cad/CAD2`
- Untar the ORFS tarball in `/cad/CAD2/opensrc`
- `cd <ORFS-PATH>/OpenROAD-flow-scripts; sudo ./etc/DependencyInstaller.sh` : Installs all required dependencies.
- create a `modulefile` with the following env variables:

```csh
set  orfsroot  /CAD2/opensrc/OpenROAD-flow-scripts/tools
setenv  OPENROAD $orfsroot/OpenROAD
append-path PATH $orfsroot/install/OpenROAD/bin:$orfsroot/install/yosys/bin:$orfsroot/install/LSOracle/bin

```

**QUICK TEST**

- The digital flow test in the ORFS directory is about 1G so don't run the test flow in your home directory so you don't run out disk space.
- `mkdir -p /home/local/simulation/<USER>/ORFS` and `cd` to that directory.
- `tar -xzf <PATH-TO-TARBALL>`
- `cd flow`
- `module load tools/opensrc/openROAD`
- `make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk ` (complete in about 3 min)
- `make DESIGN_CONFIG=./designs/sky130hd/ibex/config.mk ` (complete in about 20 min)


**SIMPLE TUTORIAL**
  
  -  Students can go through __OpenROAD SK3 Flow Structure__ to __OpenROAD SK9 flow tutorial__ from  [Vijayan's Video Tutorials](https://drive.google.com/drive/folders/1gAh5-9hbRfipVhKopFb635S3WDPAS3_k?usp=sharing) for understanding ORFS flow and how to use GUI.
  - It is similar to this [flow tutorial document](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#configuring-the-design)
  - For a simple example take `gcd` design (Flow complete in 3 mins) instead of `ibex` (Flow complete in 20 mins).
  - You can follow a step-by-step flow eg.:
    - `make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk synth`
    - `make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk floorplan`
    - `make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk finish`
  
