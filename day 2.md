# Chip Floor Planning considerations
## Utilization factor and aspect ratio
- First step of physical design is defining the width of the core and the die
  - Netlist defines connectivity
  - In this example (2 flip flops and some gates), the dimensions of the chip is mostly dependent on the size of the gates/components
<img width="769" height="501" alt="image" src="https://github.com/user-attachments/assets/ec8e17ba-a5e7-4226-bf42-e8edfba999c3" />

  - When looking at dimensions of core and die, dimensions of the standard cells is most important
<img width="665" height="413" alt="image" src="https://github.com/user-attachments/assets/aaa44e13-1658-4ce0-a0f8-1b4c96ca25fd" />

  - You can place stuff like this to get rough dimensions (minimum size)
<img width="404" height="328" alt="image" src="https://github.com/user-attachments/assets/904e3e0b-70d8-4373-a515-b1f86af87e9f" />
<img width="644" height="546" alt="image" src="https://github.com/user-attachments/assets/4adb2c75-926e-40eb-9c25-d366de3c1906" />

  - Die is core + outside stuff
- % utilization -> how much of core space is used
  - Utilization factor = area occupied by netlist/total area of the core
  - 50-60% utilization is good generally because you may need to add stuff
- Aspect ratio = height/width
## Pre-placed cells
- Second step: define location of pre-placed cells
- Pre-placed cells: reusable blocks of logic placed earlier, cannot be moved 
  - Already implemented and blackboxed (can reuse these blocks)
  - If logic too complex it can be split into 2 parts which can be connected later
<img width="664" height="476" alt="image" src="https://github.com/user-attachments/assets/50f4b2fc-70d3-4b45-8c97-1516cb514a1c" />

  - Other IPs are available (memory, MUX, etc), 
    - Arrangement of these = floorplanning
## De-coupling capacitors
- In reality, voltage will drop across wires due to resistance
  - This dropped voltage may be in the undefined region (between defined values for 1 and 0. this is undefined behavior (not good))
  - Noise margin (areas for 1 and 0) = good, undefined region = bad
<img width="667" height="370" alt="image" src="https://github.com/user-attachments/assets/40f7c637-74b8-488c-b0f4-05f1aa1b24d6" />
- Decoupling capacitor: can be considered huge capacitor filled w/ charge
  - Circuit instead gets power from the capacitor (decouples from circuit)
  - Voltage across capacitor is similar to supply voltage
  - Replenishes charge from power supply
## Power Planning
- It is not feasible to add decoupling capacitors everywhere (only critical stuff)
  - A lot of capacitors discharging at once can cause a ground bounce (not enough drain fast enough, may move into undefined region)
  - A lot of capacitors charging at once can cause a voltage droop, (not enough voltage, may move into undefined region)
- Solution: split your power supply and ground to have components take from/dump to nearest instead of the same one
  - Makes power mesh (see below), an array of power/ground
<img width="665" height="487" alt="image" src="https://github.com/user-attachments/assets/f1c6feff-3a85-4242-bb90-064e9bbec617" />

## Pin placement and logical cell placement blockage
- Inter-clock timing: 2 different outputs driven by 2 different clocks
- Netlist info coded through verilog/vhdl
- Placement of ports varies and is specific to what is closest
- Clk ports (in and out) are larger because they drive cell continuously -> larger size reduces resistance
- Outside port area needs to be blocked off so that the program knows not to place stuff there
<img width="747" height="571" alt="image" src="https://github.com/user-attachments/assets/9caa3c88-e8f1-4f16-b910-d07407684d0e" />

-Floor plan now ready to place
## Review Floorplan Files and Steps to Review Floorplan
- The README has all of the variables depending on what .tcl file is being used (the other .tcls in the directory)
<img width="1064" height="39" alt="image" src="https://github.com/user-attachments/assets/0c005a9f-6370-4068-96c2-e0f68ecd3f28" />

- The other files contain code for variable setting
- Priority: 
  - 1. sky130 config.tcl file from earlier
  - 2. the config.tcl from earlier
  - 3. these above tcl files
  - Note: openlane horizontal and vertical metals are 1 more than specified numbers
- NOTE: you need to run synthesis every time before running floorplan
- To run floorplan: `run_floorplan`
- In the .def file in here: 
<img width="841" height="29" alt="image" src="https://github.com/user-attachments/assets/4189878c-ea1f-46d2-8c9b-16fd0f8ebf0d" />

- There is this, which gives you the coordinates for opposite corners of the die (first is lower left corner, second is the upper right corner)
- You can calculate area with the units distance (line above)
  - “Microns 1000” means 1 micron = 1000 database units
  - Thus, the area is 660685/1000*671405/1000 = 443587.2124 square micrometers
<img width="644" height="553" alt="image" src="https://github.com/user-attachments/assets/0a342c33-da95-4baa-a04d-4fde5770b462" />

## Netlist binding and initial place design
- This opens the floorplan in magic:
  - `magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def`
  - floorplan should look like this:
  <img width="693" height="707" alt="image" src="https://github.com/user-attachments/assets/ade40c81-4c71-4b11-813e-fbfc180e89d2" />
- Magic commands:
  - S = select what your cursor is over 
  - V = center
  - Z = zoom
There is the tkcon window too
  - Type what -> gives info on selection
<img width="754" height="153" alt="image" src="https://github.com/user-attachments/assets/5868ae5f-11fe-4674-9a49-0dc77a248571" />

- Standard cells are this giant clump in the corner here
<img width="605" height="421" alt="image" src="https://github.com/user-attachments/assets/915c9b25-de7e-4fde-b0ef-c456f8e415a8" />

# Library binding and placement
## Netlist binding and initial place design
- Bind netlists with physical cells 
  - Netlists have the stuff laid out as a diagram with the gates in shapes, but in reality, most of them are rectangles
  - These things are sourced from a “shelf” called a library, which also has info on the cells along with the dimensions of the cells themselves
    - Library has different shape options for the same cell (larger = less resistance)
- Once size and shapes are determined, you need to do placement
  - Make sure no overlaps on the pre-placement components
  - Aim here is to minimize distance between parts and input/output for timing reasons

## Optimize placement using estimated wire-length and capacitance
- Issue: sometimes, due to existing stuff, layout may be a bit far
- Solution: optimize placement
  - Estimate wirelength and capacitance calculation (look at slew rate)-> too large distance
  - Use repeaters (buffers) that replicate original signal to maintain signal integrity
    - Trade off is loss of space
## Final placement optimization
- If a part of the chip needs to run quickly, you can put all of the components right next to each other for minimum delay
## Need for libraries and characterization
- Steps:
  - Logic synthesis: functionality -> hardware (represent with gates in RTL)
  - Floorplanning: netlist -> decide size of core and die
  - Placment: -> place logic componenents on chip
  - Clock tree synthesis: plan where clock signal(s) need to go
  - Routing: routing between cells
  - STA: analyze how long data takes
- the common factor is the gates or cells
  - library characterization is important for EDA to know what is going on
## Congestion aware placement using RePlAce
-  2 stages in of placement in openlane: global and detail
  - global is "coarse" placement -> no legalization
  - legalization = making sure the placment follows rules of putting cells on rows and no overlap
  - goal of global placement is to minimize wire length
    - openlane uses half parameter wire lenght (HPWL)
    - there is overflow -> try to minimize overflow to converge design
- run placement with `run_placement`
- once placed, use `magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &` to view stuff in magic
- placed componenents should look something like this:  <img width="1335" height="806" alt="image" src="https://github.com/user-attachments/assets/16679629-a952-450b-af10-a33ec515d04c" />
- you can see the cells if you zoom in <img width="1559" height="767" alt="image" src="https://github.com/user-attachments/assets/f7595c10-f253-4836-9cc8-0eb2ed298cf6" />
# Cell design and characterization flows
## Inputs for cell design flow
- standard cells in library, along with decaps, macros, etc.
  - has cells wiith different functionality and sizes (drive strength) and threshold voltages (decides speed)
    - smaller cell = less drive strength
- standard cell design flow:
  - inputs (from PDKS: DRC and LVS rules, SPICE models (parameters within them are very important), some library and user-defined specs (cell height/width, supply voltage, metal layers requirement, pin locations)
  - design steps
    - circuit design (implement transistors, design in a way that it will adhere to input),
    - layout design (implement values into layout, get Euler's path -> stick diagram)
    - characterization (steps)
      - Read SPICE models
      - Read extracted SPICE netist
      - Define buffer behavior
      - read subcircuits
      - attach power sources
      - apply stimulus
      - provide necessary output capacitance
      - provide simulation command
      - After all of this, it is fed into the characterization software GUNA which outputs timing, noise, power, .libs, and function
  - outputs
    - CDL from circuit design
    - GDSII from layout,
    - extract parasitics(?) and characterize in terms of timing
    - LEF for width/heignt of cell
    - extractied SPICE netlist (parasitics and resistance, capacitance)
    
# General Timing Characterisation Parameters
## Timing Threshhold Definitions
<img width="523" height="304" alt="image" src="https://github.com/user-attachments/assets/6a2157c6-39ca-4b30-836b-7f0f0a13d18d" />

above is the circuit we will be using to demo
(all graphs have x as time and y as power from power supply
<img width="454" height="474" alt="image" src="https://github.com/user-attachments/assets/bd290417-1cd8-4230-a20d-a1158ff5cf40" />

 red = input of second inverter, blue = output of second inverter
- on red: (use these two to calculate slew rise)
  - slew_low_rise_threshold (thr): about 20% of power
  - slew_high_rise_threshold: about 80% power 
- blue is the same, just replace rise with fall
<img width="472" height="480" alt="image" src="https://github.com/user-attachments/assets/8b4a21e2-7d34-4d2e-9f8c-04f1dcc2d88a" />

red = input of first inverter,  output of second inverter
- points to calculate rise delay (difference of time at these two points):
  - in_rise_thr: 50% power point of red
  - out_rise thr: 50% power point of blue
<img width="450" height="487" alt="image" src="https://github.com/user-attachments/assets/c8ff3d21-c2cd-447a-8c1e-fb5d0ebdc5f2" />

red = input of first inverter,  output of second inverter
- points to calculate fall delay (difference of time at these two points):
  - in_fall_thr: 50% power point of red
  - out_fall_thr: 50% power point of blue
## Propagation delay and transition time
- important to select threshold points correctly as not to get negative proapagation delay (unexpected results)
- transition time is the difference between the high and low slew times
<img width="930" height="425" alt="image" src="https://github.com/user-attachments/assets/9ddea3c8-f867-4c41-91f1-71a576a9599b" />
