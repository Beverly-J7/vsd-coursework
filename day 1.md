# How to Talk to Computers
## Introduction to QFN-48 Package, chip, pads, core, die and IPs
- Package has chip inside
  - Connect chip to pins
  - Core has the actual logic and parts and stuff
   - die is all the stuff on the wafer (has more than 1 core)
- Inside chip -> Foundry IPs, Macros(<pure digital logic)
  - Foundry is where the chip is manufactured
## Intro to RISC-V
- RISC-V is instruction set architecture (ISA)
- Example:
  - C program -> RISC-V (assembly language) -> machine language (binary)-> (implementation) HDL (ex: picorv32 cpu core)->hardware
## From Software Applications to Hardware
- App software->system software->hardware
  - System software:
    - OS (IO operations, allocates memory, low level system functions, converts into assembly)
    - Compiler: (Normal programming languages -> compiler -> instructions (.exe file)) 
    - Assembler: (instructions->machine language (binary))
    - Machine language -> hardware -> performs functions based on input
    - ISAs fit in here:
    - <img width="537" height="290" alt="image" src="https://github.com/user-attachments/assets/ee6db2ff-fdc5-4c83-b424-01be2a70db7e" />
- Assembler output -> RTL HDL implementation ->synthesized into netlist ->physical implementation of the netlist 
  - Netlist is just representing the gates and logic and stuff
# SoC Design, OpenLane
## Introduction to all components of open-source digital asic design
- ASIC digital design:
  - HDLs models of functions, including RTL of IPs 
  - EDA tools for design
  - PDK data
    - PDK is interface between fab and designers (process design kit)
    - the files used to model fab process for EDA tools
      - Design rules, device models, standard cell libraries, I/O libraries
      - Often shared under NDA
      - Google and Skywater open sourced PDK in 2020 for 130 nm chip (first open source PDK)
        - 130nm is not bad for stuff that is not that advanced, can run decently fast
- IC design used to integrated tightly w/ manufacturing for each company
- Separation of fabrication and design:
  - Fabless design company = design company that doesn’t do fabrication
  - Pure Play Fab = only does fabrication 
Simplified RTL to GDS flow
synthesis:
RTL -> circuit with components from standard cell library (SCL)
Each standard cell has different views/models for different uses
floor and power planning:
Planning silicon area
Chip floor planning -> partition chip die between different system building blocks, placing IO pads
Macro floor planning -> plan dimensions, pin locations, define rows
Power planning -> planning power pins (min resistance)
Placement:
Placing components on chip
2 steps: 
Global: optimal for all cells
Detailed: manual tweaking of global
Clock Tree Synthesis
Deliver clock to all sequential elements
Min skew
Usually tree shape
Routing:
Finding pattern of wires in chip that work using available metal layers in chip
Properties of metal layers in PDK
Nets: connects cells together
Make routing layers from metal layer tracks
Divide and conquer approach used for routing:
Global: generating routing guides
Detailed: using guides to implement wiring
Signoff:
Verifications:
Physical: Design rules check (DRC), Layout vs. Schematic (checking against each other) (LVS)
Timing verification: Static timing analysis (STA) to make sure time constraints met and circuit will run
Introduction to OpenLANE and Strive chipsets
Issue with open source EDA:
Tools qualification and calibration along with missing tools 
openLANE:
Started as open source flow for true open source tapeout experiment
Strive - open everything SoC (PDK, EDA, RTL)
striVe:

Main goal of openLANE: cleanGDSII tool with no human intervention
Clean means no verification violations (timing still WIP)
Tuned for Skywater 130nm PDK from earlier (also tested for XFAB180 and GF130G)
Containerized -> functions out of the box
Generate layout (harden) macros and chips
2 modes:
Autonomous (config, automates), interactive (look at steps one by one)
Design space exploration (feature) to find best set of flow configs
Has a lot of design examples (43 designs with best configs, more soon)
Introduction to OpenLANE detailed ASIC design flow

RTL synthesis
RTL -> Yosys (other open source project) -> logic circuit w/generic components -> cells (using abc, needs guidance from abc scripts (synthesis strategies))
Synthesis exploration: 
Report of delay vs. area using different synthesis strategies (pick best for needs)
Design exploration:
Used to sweep design configs to generate report (use to find best config)
Can be used for regression testing 
Run tests on design for best known configs (compare results to best known configs and report differences)
DFT (design for test) (optional, allows for test)
Fault (another open source software): scan insertion, automatic test pattern gen, fault coverage, fault simulation
Adds extra logic that allows for testing
Physical implementation (openroad app)
Aka place and route
Does these things:

LEC (logic equivalence checking)
Make sure physical implementation and netlist have same logic
Must be done every time netlist is changed (make sure no changes in function)
Addressing antenna rules violation
Metal wire can act as an antenna (causes accumulated charge)
Can damage transistors
Solution 1: bridging (going up layers)

Solution 2: antenna diode to leak away charges

Openlane makes fake antenna diode next to every cell input
Run antenna checker -> violation -> replace with real diode
STA done with openSTA
Magic used for DRC and SPICE extraction from the layout
Magic and Netgen are both used for LVS (comparing extracted SPICE and Verilog)
Familiarize with open source EDA tools
OpenLANE Directory structure in detail
Files are in Desktop/work/tools
We will be using green highlighted file below

For workshop, we will be using skywater 130nm pdk

skywater - pdk has all the pdk stuff
Open_pdk covert foundry pdks to work with open source tools
sky130A is open source compatible
Inside sky130A is libs.ref and libs.tech
Libs.tech has stuff for all of the software tools and libs.ref has technology/foundry related processes

We will be using sky130_fd_sc_hd (skywater foundry standard cell (hd is a variant))

Openlane directory:

Design preparation setup
Open Openlane like this (type in “ls -lrth” after the bash-4.2$ appears)
Then type “./flow.tcl -interactive”

Then type “package require openlane 0.9” to open packages needed (NEED TO DO THIS EVERY TIME)
Openlane’s designs are in the design folder, and we are interested in picorv32a 


There is a config.tcl file inside, which has some specifications 

Need to make sure that openlane can do stuff correctly 
Typing “prep -design picorv32a” -> makes a place that openlane can store results (NEED TO DO THIS EVERY TIME)
Cell and technology .lef files are merged to have 1 place to store

It creates the runs directory (see above)

Everything will be empty in here outside of the tmp directory, which will have merged files 
this config.tcl stores the parameters used by the run (numbers directory name is the date)
Use this to check if changes reflected in execution
Check the openlane efabless github for resources

Printed statistics section
Calculate the flip-flop ratio: 1613/14876 -> about 10.8%
 this file is the synthesized netlist
 this prints the statistics
The opensta.main.timing.rpt is the STA report

