# Timing Modelling using delay tables
## Lab steps to convert grid info to track info
- openlane is a place and route tool -> does not use all the .mag file info (no logic parts) 
- .lef files only have the openlane used materials
- guidelines for standard cell making:
  - input and output must lie at intersection of vertical and horizontal tracks
  - width must be in odd multiples of track's horizontal pitch, while the length must be the same for the vertical pitch
- tracks specify metal routes, pitch is spacing between different ones

you can see info on them in this file:
<img width="1392" height="41" alt="image" src="https://github.com/user-attachments/assets/10a3724a-dcd1-4532-bf11-ace15d2d7929" />
<img width="364" height="305" alt="image" src="https://github.com/user-attachments/assets/7425ba7d-637d-4dec-99d0-3c311eeb4bdd" />

(formatting: `[name] [x/y (horizontal/vertical] [origin offset] [pitch]`)
the inputs and outputs for this one are on the li1 layer so use the numbers for that
- press g to enable the grid
- type grid command like so:
<img width="1726" height="570" alt="image" src="https://github.com/user-attachments/assets/716c877a-48f4-4cff-a4ef-24407d5523d8" />

as the gridlines intereset with input and output (a, y) this satisfies the first guideline
## Lab steps to convert magic layout to std cell LEF
- the second rule actually corresponds to the inner box of the design (see white box below)
<img width="382" height="624" alt="image" src="https://github.com/user-attachments/assets/5e7b1e23-1fec-492f-a985-fb6765834572" />

the width and height both work in that regard

we need to define ports for the .lef file
to define ports, select the area, and go to edit > text and fill out as follows
> note: the enable is specifying the order it will be written in the lef file. lower number = earlier
<img width="1394" height="702" alt="image" src="https://github.com/user-attachments/assets/765089b6-ada0-44f8-814f-089121f1117a" />

better/more instructions: https://github.com/nickson-jose/vsdstdcelldesign#create-port-definition 
- you can save files by typing `save [whatever you want to name the file]` into tkcon
- to make a .lef, type `lef write [file name to save it under]` (if not specified, file name will be same as .mag file)
<img width="601" height="786" alt="image" src="https://github.com/user-attachments/assets/908e0f76-7d25-4046-94b2-2bd1fd002b77" />

- as you can see, pins are specified

## Introduction to timing libs and steps to include new cell in synthesis
<img width="1032" height="16" alt="image" src="https://github.com/user-attachments/assets/a0584ee0-d41e-46e6-a359-17b30620aef0" />

these files has the entire characterization in it (the 'fast', 'slow', etc. is for different temp, voltage, etc)

- edit the config file like this: (you need to have the part after the (OPENLANE_ROOT) the path after the openlane directory)
<img width="1231" height="615" alt="image" src="https://github.com/user-attachments/assets/42d23d58-eb48-4f92-8185-da8513c16e35" />

- set up openlane normally, but add ` -tag [whatever run you want to continue with] -overwrite` after `prep -design picorv32a`, along with two extra commands for the .lef files, and then we just `run_synthesis`
<img width="1786" height="621" alt="image" src="https://github.com/user-attachments/assets/6840bbdd-e3bb-445e-b005-c20596b0194f" />

- to confirm it ran correctly, make sure it's included in the printed stats
<img width="473" height="212" alt="image" src="https://github.com/user-attachments/assets/107e747d-2c0a-4bb4-9b12-d487f20d598a" />

## Introduction to delay tables
<img width="771" height="306" alt="image" src="https://github.com/user-attachments/assets/09c1b1d6-fa29-41f9-bd49-914a0b3ebd6e" />

we made some assumptions in a simplified model of this clock tree
- in reality the capacitance and load varies -> input transition also varies
- this is why we use delay tables -> axis of input transition and output load -> calculate delay

## Delay table usage Part 1
- all gates will have their own delay tables
- find delay with corresponding input slew and output load
- the labels are to define what the w/l ratio of the pmos and nmos is and the size of the device (this varies resistance)
- if the delay is not listed you can interpolate or extrapolate based off of existing data
## Delay table usage Part 2
<img width="859" height="351" alt="image" src="https://github.com/user-attachments/assets/93aa53b0-a55c-4a7f-9c45-81e9fe89a221" />

- for something like this, you can also calculate output slew as a function of input slew and output load, which you feed to the next input
- skew = difference between delay at same level (this is why it is good to have the same load and same type at each level of the tree)
## Lab steps to configure synthesis settings to fix slack and include vsdinv

- the video had some significant slack violations, but it didn't show for me so we proceeed
- to fix for slack violations, look at the SYNTH_STRATEGY, SYNTH_BUFFERING, SYNTH_SIZING, and SYNTH_DRIVING_CELL variables (set the same way as in the config file, you can use `echo` instead of `set` to view the current value
- after running floorplan, we see that the slack violation was really small, so we didn't have to worry about it.
<img width="453" height="148" alt="image" src="https://github.com/user-attachments/assets/18713c74-8758-4262-850a-04ea323f07ab" />

- this floorplan came up with some macro errors, so I ran `init_floorplan`, `place_io`,`global_placement_or`,`detailed_placement`,`tap_decap_or`, and then `detailed_placement` again, as suggested by Sophia C. in my class's discord server.
- we can see the placement files here:
- <img width="1302" height="37" alt="image" src="https://github.com/user-attachments/assets/730f86c4-74f1-4200-a79e-132935f06e15" />

- open magic (outside the docker) with this: ` magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def` while in that directory, and we can see that it has placed:
<img width="1849" height="850" alt="image" src="https://github.com/user-attachments/assets/2d803698-15fa-4e21-8e7d-45eee0012531" />

- if we zoom in, we can see our cell:
<img width="251" height="284" alt="image" src="https://github.com/user-attachments/assets/4678b7e7-301b-43c1-b373-09043789aff4" />

- the overlap is so that the power/ground rails are shared (you can see overlapping contacts here)
<img width="985" height="476" alt="image" src="https://github.com/user-attachments/assets/0d661b4c-0467-4b03-b1c8-6e323836089b" />

# Timing analysis with ideal clocks using openSTA
## Setup timing analysis and introduction to flip-flop setup time
- We are going to be doing an ideal clock analysis (no clock tree) with a single clock setup
<img width="810" height="479" alt="image" src="https://github.com/user-attachments/assets/c7f998ae-2564-466a-80d1-b42fd12a419f" />

-theta = combinational delay
- T = time (of 1 period)
- For this to work, theta < T, because otherwise, the period is larger than the real clock period
<img width="778" height="284" alt="image" src="https://github.com/user-attachments/assets/7bd56f7a-c67d-40e7-bf37-e5c847239bf4" />

- however, components are not just black boxes, they have stuff inside
- these flip-flops have MUXs inside of them, which each have their own delay -> there is a finite time, setup time (S), that the flip-flop needs to take before the signal can reach Q<sub>M</sub
<img width="775" height="445" alt="image" src="https://github.com/user-attachments/assets/703beb6f-732d-425c-aafc-61f4ad6bb179" />

## Introduction to clock jitter and uncertainty
- clocks are not perfect -> going to have a bit of time variation-> jitter
- model jitter with uncertainty (SU or setup uncertainty, this is for setup analysis)
<img width="819" height="497" alt="image" src="https://github.com/user-attachments/assets/82ae0535-fdb4-48cc-a4c5-5af2a8ea64ca" />

- for example, with this circuit:
<img width="745" height="243" alt="image" src="https://github.com/user-attachments/assets/287128f0-45e6-4efa-8968-e20cfe3b51cb" />

- clock analysis is performed to make sure that the placement works with the specifications
## Lab steps to configure open STA for post synth timing analysis
- I had to create a pre_sta.conf file here, with these contents:
<img width="1849" height="344" alt="image" src="https://github.com/user-attachments/assets/71976631-f997-4fa7-a009-d129de0e1093" />

- additionally, I needed to create a my_base.sdc:
<img width="1849" height="734" alt="image" src="https://github.com/user-attachments/assets/06c6e0ce-c17f-47ff-b743-05c32ebb4d8a" />

- afterwards, we run STA with `sta pre-sta.conf`, and the slack violation time has changed
<img width="1139" height="265" alt="image" src="https://github.com/user-attachments/assets/6f456997-8f7d-4426-af6e-54ae50c84378" />

## lab steps to optimize synthesis to reduce setup violations
- since we had significant violations, I used the commands we were supposed to use earlier here, along with changing the synth max fanout
<img width="447" height="138" alt="image" src="https://github.com/user-attachments/assets/1f365ece-308e-46ad-b3d1-75f98a122dd3" />

- due to the fact that it wouldn't let me re-run synthesis normally, I had to exit and then re run synthesis with those commands included (IT RESETS)
- you can view the connections of the pins after running sta with `report_net -connections _[pin]_`
<img width="469" height="232" alt="image" src="https://github.com/user-attachments/assets/012e10ce-7cf5-435f-9da8-bae0e721c59c" />

- one of the ways to reduce the slack violations is to replace buffers with buffers of larger sizes to decrease the signal decay. we can do that with `replace_cell _14440_ sky130_fd_sc_hd__buf_4` (note that 14440 was the input for the above)

- you can check the output again with `report_checks -fields {net cap slew input_pins} -digits 4` (-fields specifies the fields to be checked, - digits specifies the sig figs)



## Lab steps to do basic timing ECO

- we can do similar things with the other cells that have very high delays and slews
  - since I don't have a lot of buffers, I used https://sky130-unofficial.readthedocs.io/en/latest/contents/libraries/sky130_fd_sc_hd/README.html to find different sizes for the cells.
- we can see that the the slack has gone down a bit
<img width="363" height="44" alt="image" src="https://github.com/user-attachments/assets/739a5626-c102-4d26-82e4-2267a3093415" />
- and that the tns and wns are improved a bit too:
<img width="167" height="95" alt="image" src="https://github.com/user-attachments/assets/bf0ae5a3-a1ea-4f59-bf32-98a698f0a823" />

# Clock tree synthesis TritonCTS and signal integrity
## Clock tree routing and buffering using H-Tree algorithm
- the point of the clock tree is to connect clock signal to components as best as possible (minimize skew)
- skew is the difference in time that the components are receiving the clock signal -> minimize for timing
- H-tree routing (a way to route clock tree) -> take middle point between all components that need clock to ensure that wire delay is same
<img width="567" height="451" alt="image" src="https://github.com/user-attachments/assets/b4e3c8a0-ccf7-4914-8aa7-c5cd7a699ff9" />

- issue right now is that this distance creates a lot of resistance and capacitance -> signal changes = bad
<img width="575" height="179" alt="image" src="https://github.com/user-attachments/assets/a09dfc98-ad7c-421d-a05b-56a6120b6c5a" />

- solution: more repeater buffers  (red buffers in image below)
  - for clocks specifically, these repeaters need equal rise/fall time
<img width="547" height="401" alt="image" src="https://github.com/user-attachments/assets/29935c95-f986-46f4-85bd-a075ea7556a1" />

## Crosstalk and clock net shielding
- crosstalk = bad, need to shield clock net
- to shield -> protect from outside (if the capacitance is big enough between wires it can cause coupling)
- coupling causes 2 issues: glitch, delta delay
  - glitch: with large enough capacitance, things happeing on one wire can affect results on other wire (cause changes in voltage -> binary wrong = many bad things depending on function of circuit)
  - delta delay: if the other wire switches while the 'victim' wire is switching -> it will intermittently try to charge and cause additional delay -> more skew
<img width="156" height="53" alt="image" src="https://github.com/user-attachments/assets/6b3d30a8-2c89-4bc7-821d-df26a57344b2" />

- shielding: 2 wires on the sides that are connected to power/ground -> idea is that it will not switch so the wire will not switch
- shield all critical nets (such as clock nets and critical data nets) to prevent (unfortunately cannot shield all because extra routing resources)
<img width="498" height="365" alt="image" src="https://github.com/user-attachments/assets/413bc0ba-5156-42aa-9cd0-efc13f974269" />

## Lab steps to run CTS using TritonCTS
- we can do the same thing as before to a few other cells:
<img width="522" height="152" alt="image" src="https://github.com/user-attachments/assets/89c60886-28a5-4ea9-8ad9-03b4be73fddf" />

<img width="513" height="106" alt="image" src="https://github.com/user-attachments/assets/540b186b-dc7e-4798-a549-6a61642d91b2" />

<img width="472" height="42" alt="image" src="https://github.com/user-attachments/assets/bf790640-3921-4e79-9469-c44b787faa59" />

- this gets our time down a bit:
<img width="401" height="55" alt="image" src="https://github.com/user-attachments/assets/4e976555-3921-421b-9e35-8d60e3a5b076" />

- since we have done some modifications, we need to update the verilog file in the results/synthesis under our current run (referenced by my_base.sdc)
- we do this with `write_verilog [verilog file path]`
- we can check if this did update by checking for a cell we modified in the verilog file
- we then run floorplan on this file (using the macro issue fix steps)
<img width="1855" height="637" alt="image" src="https://github.com/user-attachments/assets/58b23011-4423-4899-b702-1401b9fc6b6d" />
- the area is larger than before as we had to increase it to reduce slack
  - when it comes to chip design, it's always a tradeoff between timing, power, and area
- we then run cts using `run_cts` with default setttings
  - for CTS, it's always a tradeoff between QOR (quality of results) and runtime
<img width="954" height="404" alt="image" src="https://github.com/user-attachments/assets/10d0e0e1-d0bc-400e-8902-d5797b3cb23b" />

- once it's done, we can see that it created a new file:
<img width="1213" height="35" alt="image" src="https://github.com/user-attachments/assets/991adad2-28f4-4474-84d4-06dc6acfdf24" />

## Lab steps to verify CTS runs
- there are tcl command files for each process:
<img width="1773" height="41" alt="image" src="https://github.com/user-attachments/assets/2fcf0c59-6d68-4235-a687-1643439cb535" />

- if we go into the cts.tcl file, we can see procs, which are similar to functions or methods in other programming languages
<img width="1764" height="732" alt="image" src="https://github.com/user-attachments/assets/61e5a6b9-9ec2-4280-89db-506549ca2fde" />

what it's doing:
- ` puts_info "Running TritonCTS..."` displays message
- `TIMER::timer_start` sets timer
- `try_catch openroad -exit $::env(SCRIPTS_DIR)/openroad/or_cts.tcl |& tee $::env(TERMINAL_OUTPUT) [index_file $::env(cts_log_file_tag).log 0]` opens openroad, passes control to a script inside the openroad folder (or_cts.tcl)
  - if we go to that file directory, you can see that there are more .tcl files, and none for synthesis, as only some processes under openlane are handled by openroad
<img width="1344" height="55" alt="image" src="https://github.com/user-attachments/assets/e589ebeb-6a7b-480d-9484-c8fcb60be6aa" />

<img width="593" height="388" alt="image" src="https://github.com/user-attachments/assets/61d5fe36-ede4-4d2d-b0a0-b8b7fcd26e94" />

- we then open up the or_cts.tcl:
<img width="1811" height="801" alt="image" src="https://github.com/user-attachments/assets/e74d287f-a937-4369-acbd-8f003454e9e0" />

what it's doing:
- `if {[catch {read_lef $::env(MERGED_LEF_UNPADDED)} errmsg]}` flags an error message if there is no MERGED_LEF_UNPADDED
- CURRENT_DEF is the currently used .def file:
<img width="818" height="50" alt="image" src="https://github.com/user-attachments/assets/5b971087-9989-4f3d-b8c4-d65198cfbdff" />

- the next steps will used this file
- this is the actual CTS command, where control is passed to TritonCTS 
<img width="787" height="132" alt="image" src="https://github.com/user-attachments/assets/06065cc2-1db7-41c6-8ca5-d4c457dbf165" />

- CTS_CLK_BUFFER_LIST is the list of clock buffer cells
- CTS_ROOT_BUFFER, something found in the README from earlier, which is sky130_fd_sc_hd__clkbuf_16, and the max capacitance is the max capacitance of the output pin of this
  - you can find the specific information in the typical lib file from earlier
> note: I ommitted many this in the video because the vm seems to have different variables and some changes to the code overall compared to what is shown in the tutorial
# Timing Analysis with real clocks using openSTA
## Setup timing analysis using real clocks
- since this is now a real clock, you need buffers
<img width="768" height="437" alt="image" src="https://github.com/user-attachments/assets/41e42b89-4c90-4be4-b61d-36e68e22adb7" />

- these buffers have delay (corresponds to buffer number)
- the buffer times are simplifed with deltas below
<img width="613" height="206" alt="image" src="https://github.com/user-attachments/assets/64575125-5c02-4519-b27a-80f5b7658854" />

- the time span is shifted to when the signal arrives at the launch flop, to when the original clock signal is receieved by the capture flop
- add setup time and uncertainty (see below)
<img width="770" height="157" alt="image" src="https://github.com/user-attachments/assets/2b5810a0-a246-4008-b0a0-5f667c72a6c8" />

- slack is the data required time - data arrival time (good if it is positive or 0)

hold timing analysis
<img width="780" height="385" alt="image" src="https://github.com/user-attachments/assets/32cf9a17-f692-4258-89b1-af86416d3fd3" />

-instead of syncing with the rising edge at T, it is syncing with the same rising edge
- hold time is the second MUX delay (time for data to be sent from inside to outside)
  - capture flop says 'please hold data, launch flop, until existing data sent out' (this is hold time)
<img width="585" height="191" alt="image" src="https://github.com/user-attachments/assets/22905c41-777a-45f5-9e43-d5e62efed417" />

-deltas mean same thing as they did before (buffer delay)
## Hold timing analysis using real clocks
- jitter is less of an issue as we are using the same edge (uncertainty value kept relatively low)
- hold time also comes with uncertainty (HU)
- slack is now data arrival time - data required time (expect arrival time to be larger)
<img width="869" height="260" alt="image" src="https://github.com/user-attachments/assets/210dde7f-51fd-4afb-bff6-4882054f7420" />

-delta 1 and delta 2 are a combination of the buffer and wire delays to get the signal where it needs to go
<img width="884" height="472" alt="image" src="https://github.com/user-attachments/assets/d71ebed0-7daa-48d2-b1c0-116aa79dc058" />

