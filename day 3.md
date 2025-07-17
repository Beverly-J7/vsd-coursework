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

> (interestingly, in voltage (the "other node") is the x axis, while the the voltage for the out is the graphed line)




  
