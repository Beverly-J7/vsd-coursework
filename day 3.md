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
- etch thin oxide in hydroflouric acid solution -> gate, source, drain all open
- deposit titanium through sputtering (titanium -> hit with argon -> particles of titanium will 'sputter' out -> deposit on substrate)
- heat in nitrogen ambient for about a minute -> chemical reaction -> formation of some TiSi<sub>2</sub> aka titanium disilicide (low resistance) and TiN (only for local communication due to resistivity)
- have to decide which connections are added locally (here) or with higher metal layers
- use mask 11 to do photolithography, etch off extra titanium nitride etched off with RCA (de ionized water, ammonium hydroxide, hydrogen peroxide in 5:1:1: ratio)
<img width="572" height="351" alt="image" src="https://github.com/user-attachments/assets/48fb0bfd-bdb4-49c2-bfe5-46f246350b93" />

## Higher level metal formation
- previous TiN situation is not flat, so it's not great
- deposit 1um layer of silicon dioxide, doped with phosphorous or boron (phosphorous is to act as partition for sodium ions(?) and boron reduces temp) (this stuff is known as phosphosilicate glass or borophosphosilicate glass)
- chemical mechanical polishing (cmp) -> planarize surface
- drill contact holes with more photolithography
  - mask 12 makes contact holes through initial doped silicon dioxide -> remove photoresist, put on 10nm layer of TiN
  - blank tungsten layer, more cmp for planarizing so that it is level with the doped silicon dioxide layer (also removes outside TiN)
  - aluminum layer -> mask 13, plasma etched off excess
  - more silicon dioxide and cmp, mask 14 to make holes again, more TiN and tungsten
  - mask 15 makes thicker aluminum layer
  - top dielectric layer with silicon nitride -> open contack holes with mask 16
<img width="593" height="402" alt="image" src="https://github.com/user-attachments/assets/6ad48cfb-ad63-4dcf-8c2f-59be09fe5df0" />

## Lab introduction to Sky130 basic layers layout and LEF using inverter
<img width="292" height="721" alt="image" src="https://github.com/user-attachments/assets/c081972c-7d3b-489a-afd5-3cf957c90636" />

these things on the side here are layers, if you hover over, the name pops up in the top brown bar
<img width="646" height="427" alt="image" src="https://github.com/user-attachments/assets/710154cb-858b-4e05-9233-715def2f0858" />

you can use the select and `what` in the tkcon here too to figure out what things are
<img width="520" height="391" alt="image" src="https://github.com/user-attachments/assets/c874290f-76cd-4286-9627-4e8ccd51fd21" />

if you hit s multiple times when hovering over a component, it will select progressively more stuff that it is connected to (this case, 3 times)
- we use this to check connectivity of different layers
- LEF = library exchange format which only has the metal layers (abstract below), and we are viewing a layout
<img width="297" height="278" alt="image" src="https://github.com/user-attachments/assets/935b14f7-e26c-4cde-b89b-9541a139ca20" />

- this is important when placing, as you only really need to know where the pins are
- it also protects logic IP
## Lab Steps to Create STD Cell Layout and Extract SPICE Netlist
<img width="299" height="243" alt="image" src="https://github.com/user-attachments/assets/10d9b499-16f3-45b9-b0db-d81a0eaf3ac3" />

- top is creating a fixed BBOX
- rest is parameters (ll and ur mean lower left and upper right, those things are defining cooridnates for corners of the box)
- cmos is then built step by step (power/ground pins on locali -> standard cell rows for power/ground are on metal 1)
<img width="464" height="308" alt="image" src="https://github.com/user-attachments/assets/14baf068-8b9a-42cd-be44-74f49759b7c6" />

- li con = dark blue color (between locali and metal1)
- n substrate contact is n well -> locali, similar for
<img width="697" height="519" alt="image" src="https://github.com/user-attachments/assets/296329c2-7888-41bb-8694-50ae716ec82c" />

- DRC in corner-> as long as it is 0, no violations
  - use the find next error in the Drc dropdown to find where the issue is (it will zoom in and highlight and also say in the tkcon)
<img width="752" height="424" alt="image" src="https://github.com/user-attachments/assets/b1caf176-a938-42b1-9f89-3495de153a94" />

- to add stuff to your layout in magic: select area, hover over layer, press middle mouse button (drc will reduce automatically)
to know logic -> extract to spice
- to extract: `extract all` in tkcon will extract to wherever the .inv file was
<img width="860" height="128" alt="image" src="https://github.com/user-attachments/assets/591df854-28a4-488d-a33e-84bd3f55562d" />

- need to make spice file with the .ext file -> `ext2spice cthresh 0 rthres 0` -> extract all paracsitic info
- then type `ext2spice` to make SPICE file
<img width="1387" height="188" alt="image" src="https://github.com/user-attachments/assets/c5b30f0a-33ff-484f-91cc-9536f0141b8d" />

## Lab steps to create std cell layout and extract spice netlist
<img width="883" height="455" alt="image" src="https://github.com/user-attachments/assets/a75d6d78-9030-4bcc-96d7-b46fcad90695" />

- view spice file with `nano [file]`
- the one that says nfet is likely an nmos, while the one that says pfet is likely a pmos
- the order after the name [x1, ex2, etc) are the nodes corresponding to drain, gate, and source
type `box` into tkcon to get the minimum grid box dimensions (grid behind window)
<img width="604" height="219" alt="image" src="https://github.com/user-attachments/assets/24553654-7327-441b-9d89-cc42e737c960" />

- we need the scaling to be correct (the option scale and the root box size should be the same)
- as you can see here, it is not
<img width="443" height="401" alt="image" src="https://github.com/user-attachments/assets/65785514-c258-43f9-8350-33ba096dd2cf" />

-unfortunately, mine is indeed not a square so I just put .01u at someone's suggestion
<img width="853" height="565" alt="image" src="https://github.com/user-attachments/assets/c5013bf7-5580-4756-8756-8db5869f608d" />

- add stuff to change the scale, add vdd and vss, comment out (`//`) some existing commands, and run analysis code
<img width="1156" height="46" alt="image" src="https://github.com/user-attachments/assets/605e4d39-170a-41e0-a6b0-9adf7c315459" />

- in the pshort and nshort files here, the model for the transistors is actually listed under a different name (see below)
<img width="428" height="362" alt="image" src="https://github.com/user-attachments/assets/a13abb6e-31b3-4609-bcad-d05a166774ed" />

- we need to rename these model names so that they match
<img width="741" height="65" alt="image" src="https://github.com/user-attachments/assets/19361a0c-3e15-4ed8-9b6c-1d46b71dd58e" />

- we also need to change the headers to M1000 and M1001 like in the video to get things to read
<img width="754" height="550" alt="image" src="https://github.com/user-attachments/assets/95d89546-3766-40e1-a0c4-ab5fee0126c7" />

- C3 was also changed due to the value being too low and causing spikes
## Lab steps to characterize inverter using sky130 model files

- type `plot y vs time a` to get a graph
<img width="1209" height="734" alt="image" src="https://github.com/user-attachments/assets/cbb784cb-f4c4-4320-bbac-29a3630ec4d0" />

- find values:
  - rise transition -> 20% of max voltage (.66V) to 80% of max voltage (2.64V) (max is 3.3)
  - zoom in by drag selecting with right mouse button, left click on specific point to get values here
  <img width="339" height="95" alt="image" src="https://github.com/user-attachments/assets/0e19fa72-5e20-440d-8b83-952b6f25de57" />

the top value is at 20% and and the bottom value is at 80%
- y is voltage so we take the differense of these x values (ns  = 1e-9s) -> 2.27845 ns - 2.1974 ns = 0.0815 ns for rise time
- you can find the fall time in a similar way

-you can also find the cell rise propagation delay in a similar way, but by zooming in on the 50% point -> 0.07871 ns
<img width="344" height="123" alt="image" src="https://github.com/user-attachments/assets/3f8addf2-9ea8-4be8-8ffd-99199452f812" />

## Lab introduction to Magic tool options and DRC rules
- here are some resources on magic: http://opencircuitdesign.com/magic/ (of note is the DRC section in the tech file manual)
- technology file: declares a lot of stuff including rules and layers
- CIF (caltech intermediate format) is human readable, GDS is not, both indicate mask data
## Lab introduction to Sky130 pdk's and steps to download labs
- skywater pdk documentation: https://skywater-pdk.readthedocs.io/en/main/
- implant layers not visible in magic -> use `cif see [layer]` command to highlight
- to download lab files: `wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz`
- you have to then extract them using `tar xfz drc_tests.tgz`
<img width="1407" height="295" alt="image" src="https://github.com/user-attachments/assets/32c5bb97-f6cb-435e-99a2-786cdb873678" />

-you can `magic -d XR` for what the instructor says is nicer graphics

## Lab introduction to Magic and steps to load Sky130 tech-rules
- to run file, you can put `magic -d XR met3` but you can also go to the file in the top right corner of the layout window and find it through open
<img width="1096" height="557" alt="image" src="https://github.com/user-attachments/assets/b2916185-8ec2-4e8d-ad06-e549ca2240c8" />

<img width="1079" height="547" alt="image" src="https://github.com/user-attachments/assets/7b62db0e-1d8f-45ad-94b7-69ea434ceb00" />

each of these numbers corresponds to a rule in the skywater PDK DRC 
- you can type `:` or `;` to redirect keystrokes to the tkcon while still having cursor over the layout window
- type `drc why` to get information on the drc violation
<img width="530" height="89" alt="image" src="https://github.com/user-attachments/assets/a63f5906-8872-4807-a3b3-95cfd29b3e3a" />

- to paint: select area, paint command or hover over desired layer in sidebar and hit `p` or middle mouse button
- using `cif see VIA2` -> see contact cuts for metal 3 contacts (these dark brown boxes represent mask layer for via2)
<img width="288" height="306" alt="image" src="https://github.com/user-attachments/assets/764455c8-5637-485b-ab62-e26d5292b3ec" />

- this view is called a feedback view, which you can dismiss by typing `feed clear`
- this tech file is set up on a 0.01 micron grid, but 5 nanometer grid may help you line up the cursor correctly -> `snap int`
- you can type `box` to view size of box selection
<img width="1069" height="278" alt="image" src="https://github.com/user-attachments/assets/e8457eb6-c22c-4524-bff1-a94201541383" />

- to erase: select area + blank space, hover over blank space and click on middle mouse button
## Lab exercise to fix poly.9 error in Sky130 tech-file

- we will be in the poly.mag file
- to input an _ for commands like ctrl+_, you do have to press shift
- go to aliases section to find if there is more appropriate names for stuff
- the process:
  - find mentions of rule by searching for name
  - implement changes
  
<img width="693" height="461" alt="image" src="https://github.com/user-attachments/assets/6663c378-6117-4dad-a950-613f7b28f917" />
<img width="511" height="241" alt="image" src="https://github.com/user-attachments/assets/2489765b-1223-4776-b5f8-607ec40d2917" />

(note: don't actually add those extra empty lines, it breaks stuff)
in this case, I added a rule that says to specify the distance betwen `allpolynonres` and the poly resistors
<img width="1422" height="635" alt="image" src="https://github.com/user-attachments/assets/04f4451e-a4d1-47f2-aa97-0e4ee7a11cb6" />

you have to reload the tech file with `tech load [file]` and then redo the `drc check`
<img width="524" height="285" alt="image" src="https://github.com/user-attachments/assets/c8c86daa-9469-45ca-a4ff-dd86037d9b4e" />

(I accidentally clicked something, but the gray rectangle is nsubstratendiff) the fix didn't fix all cases, so we have to go back
<img width="722" height="93" alt="image" src="https://github.com/user-attachments/assets/69c73d44-1cf1-4a8e-a94d-d0bfaaa7c7af" />

(highlighted is implemented change)

##









