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

- we add these to the config.tcl file:
<img width="1292" height="525" alt="image" src="https://github.com/user-attachments/assets/f59d09dd-836c-4ec6-ac25-7ce590ed42b1" />

-set up openlane normally, but add ` -tag [whatever run you want to continue with] -overwrite` after `prep -design picorv32a`
<img width="925" height="158" alt="image" src="https://github.com/user-attachments/assets/51a6319a-08b0-4dba-be92-a89e4a2df36e" />

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
