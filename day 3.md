# Labs for CMOS Inverter ngspice simulations
## IO placer revision
- you can change the variables in the interactive (`./flow.tcl -interactive`) by using `set :: [variable] [number]` (find variables and the commands in the floorplan.tcl file in openlane/configurations)
- example: `set ::env(FP_IO_MODE) 2` changes the algorithm used for pin configurations
<img width="570" height="335" alt="image" src="https://github.com/user-attachments/assets/144ceb81-9be7-4f0b-b0f2-ffcb6c35445c" />

- pins are now all in this corner (different pin algorithm)
## SPICE deck creation for CMOS inverter
- step 1: create SPICE deck (connectivity info, input, tap points for outputs of a netlist)
- step 2: define componenent values
 - first value on transistors is the channel width, second is the channel length
<img width="452" height="397" alt="image" src="https://github.com/user-attachments/assets/f6e475a2-57b2-4cd0-8d4e-d6f9c99f7366" />

- step 3: identify nodes (nodes = there is a component between 2 nodes because we need to define positions relative to nodes)
<img width="549" height="432" alt="image" src="https://github.com/user-attachments/assets/cff768d7-e8a4-4e5b-ba67-d0121b7f17dc" />

- step 4: name the nodes
- step 5: write SPICE
- spice deck syntax for pmos and nmos is:  `[name] [drain node] [gate node] [source node] [substrate node] [transistor type] W=[width] L=[length]`


## SPICE simulation lab for CMOS inverter 
- capacitor syntax: `[name] [node] [node] [capacitance]`
- supply voltage and input voltage syntax: `[name] [node] [node] [voltage]`
- simulation commands:
  -  to sweep voltage: `.dc [voltage source] [start voltage] [end voltage] [voltage of step]`
- making model file:
    - model file has all the parameters for the transistors
    - in this case, `.LIB [file name] CMOS MODELS`
  <img width="970" height="391" alt="image" src="https://github.com/user-attachments/assets/10038e07-7139-4dad-980a-d04dfdacb619" />

how to spice simulation:
- open ngspice
- `source [file path]` to source the circuit 
- `run` to execute it
- `setplot` which will give you some options to plot
<img width="639" height="43" alt="image" src="https://github.com/user-attachments/assets/fda12ceb-6e77-4418-8ed3-73c037b041fd" />

- type the name of the plot (left column)
- `display` to display to give table of different voltages at nodes to plot
<img width="636" height="182" alt="image" src="https://github.com/user-attachments/assets/00a85169-2008-4719-8e4e-46a897b803c4" />

- `plot [node] vs [other node]` to plot two different voltages
<img width="619" height="515" alt="image" src="https://github.com/user-attachments/assets/73637e3f-60c3-437b-bbe7-b7dcdb000d57" />

## Switching Threshold Vm
<img width="815" height="470" alt="image" src="https://github.com/user-attachments/assets/475e9253-1db5-4ff4-836c-c0ea7c5f9a18" />

- shapes of graphs very similar -> cmos very robust
- switching threshold (diagonal line) = V<sub>in</sub>=V<sub>out</sub>
 - both pmos and nmos are in saturation region here, and both will likely have leakage
 - a lot of current flowing from power to ground in this area
- pmos on nmos off at high area, nmos on pmos off at low area
<img width="449" height="339" alt="image" src="https://github.com/user-attachments/assets/27c4c843-46a0-4182-9ad7-61f7cc2983c3" />

- when at switching threshold, the current from drain to source for the pmos is equal to the negative of the drain to source current for the nmos

## Static and dynamic simulation of CMOS inverter
- to do dynamic sim -> input is pulse, and we will do transient analysis
- to format pulse on input: `[name] [node] [other node] [idk what this is] pulse [start voltage] [high voltage] [shift, 0 = start at 0] [rise time] [fall time] [pulse width] [complete cycle]`
<img width="848" height="445" alt="image" src="https://github.com/user-attachments/assets/0e194e68-1270-4d91-877d-e2faced02117" />

- to do this:
 - source file again
 - `run` again
 - `setplot` again -> will say it is "tran" analysis
 - type `tran2` after the qurstion mark
 - type `display` again to see options
 - `plot out vs time in`
<img width="744" height="612" alt="image" src="https://github.com/user-attachments/assets/40704409-2c43-4223-aa4d-190a96db0ca2" />
- zoom in by selecting (zooming into the 50% point)
- you can click on points of the graph and the terminal window will tell you what it is and you can just find the difference between the times
<img width="241" height="221" alt="image" src="https://github.com/user-attachments/assets/1465a22b-72e6-41ef-94d7-1d7b90450e32" />

- do this for both the rise and fall delay
## Lab Steps to git clone vsd std cell design
- copy the url from here
<img width="1837" height="792" alt="image" src="https://github.com/user-attachments/assets/5dac72e5-85f1-4bc4-89b5-ed2459f823d4" />

- go to the /openlane and `git clone [link]` (the link is https://github.com/nickson-jose/vsdstdcelldesign)
- copy the ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech file to the vsdstdcelldesign directory with cp
 - `cp [path of what you want to copy] [path the where you are copying it to]`
- then go to where the file is copied and type `magic -T sky130A.tech sky130_inv.mag &`
- the inverter should look like this:
<img width="851" height="695" alt="image" src="https://github.com/user-attachments/assets/ad05a81f-d908-466b-b1a1-f0563c26cf91" />

# Inception of layout for CMOS fabrication process
## Create Active Regions
16 mask cmos fabrication:
- select substrate: most common is p type silicon (p doping 10<sup>15</sup>/cm<sup>3</sup> <- less than well)
- creating active region:
 - 1. 40nm of silicon dioxide (SiO<sub>2</sub>)
 - 2. 80 nm of Silicon Nitride (Si<sub>3</sub>N<sub>4</sub>)
 - 3. photoresist
 - 4. mask 1 over places you don't want to get removed
 - 5. apply UV light -> chemical reaction on exposed photoresist, wash off in solution
 - 6. remove mask 1
 - 7. etch off exposed silicon nitride
 - 8. remove photoresist
 - 9. the areas pf silicon dioxide not covered by silicon nitride will grow in oxidation furnace (locos -> local oxidation of silicon), the edges of the growing places are called 'bird's beak'
 - 10. strip silicon nitride in hot phosphoric acid
 <img width="562" height="328" alt="image" src="https://github.com/user-attachments/assets/1939c057-4d82-4613-91d4-f0031581345a" />

## Formation of N and P well
next step: n and p well formation
- more photoresist over the entire thing, mask 2 (about half)
- do the UV light thing again, remove mask
- now with photoresist on one half, make p well -> diffuse boron ions (ion implementation) (p-type material) through silicon dioxide
- do the same stuff with the mask on the other half, diffuse phoshorous through silicon dioxide
- put full thing into high-temp drive-in furnace, which drives the the boron and the phosphoous into the substrate
<img width="540" height="280" alt="image" src="https://github.com/user-attachments/assets/49daf3d2-cae6-470d-b41c-079cfe3c46ff" />

## Formation of gate terminal
- gate is where you control your threshold voltage, gate terminal fabrication is very important
- these two factors below are most important to controlling the threshold voltage
<img width="801" height="396" alt="image" src="https://github.com/user-attachments/assets/fce458a5-3063-46fb-995b-f6f1f383b944" />

- mask 4 and photoresist (exposed p well), implantation of low energy boron at the surface of the p well (similar to mask 2)
- mask 5 and photoresist (exposed n well), use phosphorous or arsenic at surface of n well
- repair oxide: rine extra silicon dioxide using hydrofrouric acid over the wells and regrow it to control thickness (~10 nm)
- grow about .4 um  of polysilicon -> dope with n type impurities (arsenic, phosphourus)
- mask 6 and photoresist for the gate, etch away additional polysilicon
 <img width="532" height="243" alt="image" src="https://github.com/user-attachments/assets/6d0f6c06-086a-45b4-8dd5-7d8c1687195e" />

## Lightly doped drain (LDD) formation
- reason:
 - hot electron effect: device size decreases, electric field increases (power supply does not change)
  - high energy can break si-si bonds -> more unexpected electrons and holes
  - energy too high -> crosses 3.2V barrier between si conduction band and silicon dioxide conduction band -> issues
 - short channel effect: drain field penetrates the channel -> gate cannot control current
- mask 7 over n well -> n type impurity implantation in p - well (only a bit at surface) -> n- channel because light concentration
- do same thing on the other side but with p type impurity (also lightly doped) -> p-
- thick ~0.1 um silicon nitride or silicon dioxide layer -> plasma anistropic etching to create these side-wall spacers that protect parts of the lightly doped regions from further source/drain formation
<img width="524" height="240" alt="image" src="https://github.com/user-attachments/assets/38a80ea1-ddec-4a61-be97-060ceaae84d3" />

## Source – drain formation
- the screen oxide is added to avoid channeling (when doping goes too deep/fills up substrate (very bad))
- mask 9 over n well, same deal, arsenic added to p well side to make source (n+)
- mask 10 for p+ (slightly less voltage) with boron
- furnace -> high temp annealing -> pushes the p+ and n- more into the n and p well
<img width="535" height="240" alt="image" src="https://github.com/user-attachments/assets/752910cc-d826-40c7-a3e7-3b020ef80a49" />

## Local interconnect formation
