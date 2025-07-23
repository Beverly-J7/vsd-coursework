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


-set up openlane normally, but add ` -tag [whatever run you want to continue with] -overwrite` after `prep -design picorv32a`, along with two extra commands for the .lef files, and then we just `run_synthesis`
<img width="1786" height="621" alt="image" src="https://github.com/user-attachments/assets/6840bbdd-e3bb-445e-b005-c20596b0194f" />

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
## lab steps to optimize synthesis to reduce setup violations
## Lab steps to do basic timing ECO
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
## Lab steps to verify CTS runs
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

