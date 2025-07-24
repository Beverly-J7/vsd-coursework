# Routing and design rule checking
## Introduction to maze routing lee algorithm
- Maze routing = Lee's algorithm
- routing = connecting 2 points with shortest path (try to have L shaped paths, minimize zigzag)

algorithm:
- algorithm creates grid (has standard dimensions)
- creates 2 points -> source (S) and target (T)
- labels obstructions such as the pre-placed (need to avoid these places when routing)
<img width="213" height="156" alt="image" src="https://github.com/user-attachments/assets/d55155f3-c1be-4bc7-9761-98d8b5228af7" />
- starts by labelling the horizontal and vertical adjacent to source as 1, and then expands similarly with each step incremeting number until it reaches target
<img width="351" height="334" alt="image" src="https://github.com/user-attachments/assets/34b66936-f881-442c-8107-a49ab8bb1820" />

## Lee’s Algorithm conclusion
- the algorithm follows the cell numbers and chooses the path with minimum bends in order to make the shortest path
<img width="435" height="468" alt="image" src="https://github.com/user-attachments/assets/1502258b-ca0e-4b99-965f-13836dd8e549" />
## Design Rule Check
<img width="300" height="253" alt="image" src="https://github.com/user-attachments/assets/4cca2946-8b71-41ff-8db7-6fa927c9f397" />

- for photolithography to work, wire width needs to be at least a minimum
<img width="310" height="304" alt="image" src="https://github.com/user-attachments/assets/8e06498c-3f51-40ce-8a4d-d8391e9da1fc" />

- pitch = distance between centers -> optimized photolithography process (which distance prints best)
<img width="296" height="149" alt="image" src="https://github.com/user-attachments/assets/e64b9942-b977-4a3f-b819-96f63bc02f51" />

- minimum distance between wires
<img width="305" height="284" alt="image" src="https://github.com/user-attachments/assets/69bacd17-4e49-4ddc-8fbe-396526435f9c" />

- short occurs when wires on same layer overlap -> functionality failure
- can be solved by moving one of the wire to a different layer
<img width="220" height="209" alt="image" src="https://github.com/user-attachments/assets/0fc88f8f-1251-4639-97fc-32405cde8834" />

- new rules to check: minimum wire width and minimum spacing
<img width="326" height="324" alt="image" src="https://github.com/user-attachments/assets/923069b3-6a0e-405c-a305-1fab37002cf8" />
<img width="275" height="225" alt="image" src="https://github.com/user-attachments/assets/a9d1d02c-0ac4-4d31-ba91-06c9a4611b98" />

# Power distribution network and routing
## Lab steps to build power distribution network

## Lab steps from power straps to std cell power
## Basics of global and detail routing and configure TritonRoute

(insert notes from first half here): 4:13
- tritonroute is used for routing when `run_routing` is done
- routing is divided into 2 steps, global/fast route and detailed route
  - global route is divded into grid cells for speed reasons, makes a route guide
  - detail route uses algorithm to find best connectivity based off of route guide
  <img width="846" height="438" alt="image" src="https://github.com/user-attachments/assets/78ecd4df-b0ec-476a-8fa2-f3f028184cba" />
  
# TritonRoute Features
## TritonRoute Feature 1 Honors pre processed route guides
- tritonroute performs the intial detail route, honoring pre-processed route guides from the global/fast route as much as possible
- it assumes that route guides satisfy inter-guide connectivity
- uses MILP based panel routing with intra-layer parallel and inter-layer sequential routing framework
  - for inter-layer routing, the vias are placed when the upper layer is routed
  - clarification: fast route is the engine, global route is the process
<img width="677" height="364" alt="image" src="https://github.com/user-attachments/assets/f689f6a6-e723-41f0-947b-437c6ad63bf0" />

- preprocessing route guides:
  - for this one, the preferred direction is vertical, so it needs to split this long horizontal bar to pieces of unit width (global cells are 1 unit width
<img width="229" height="193" alt="image" src="https://github.com/user-attachments/assets/de1ea0ed-796f-406b-8748-fe64f3bfee71" />

- the edges that work with the preferred direction are merged with other parts
<img width="202" height="190" alt="image" src="https://github.com/user-attachments/assets/cd063c65-3b5e-4422-ad91-932f09071913" />

- the M2 layer's preferred direction is horizontal, so the longer vertical segments are instead bridged on that layer (this is why its important to alternate preferred direction)
<img width="303" height="179" alt="image" src="https://github.com/user-attachments/assets/c4d94732-e52f-4db8-8ec2-19aba5b90b61" />

-this now satisfies th two requirements, unit width and preferred direction of the layer
## Feature2 & 3 Inter-guide connectivity and intra and inter layer routing
- two route guides are connected if they are on the same metal layer with touching edges or they are on neighboring metal layers with overlap
- unconnected terminals (e.g. pins for a standard cell) need to be overlapped by the route guide -> the routing goes along the tracks, which is why we needed to align the input and output pins in magic
(black dot = pin)
<img width="165" height="167" alt="image" src="https://github.com/user-attachments/assets/9f52d569-86c7-4d31-9ae1-9aee6b98d95c" />

- routing within layer is happening in parallel, routing between layers happening in sequenece
- there are these guides seperated by dashed lines -> panels (each with an index?)
- routing first happens on even index panels per layer, then odd index panels
(the panels with the arrows have been/are being routed)
<img width="336" height="267" alt="image" src="https://github.com/user-attachments/assets/e6697d24-fcd5-459d-b1f7-d4267e04aeda" />


## TritonRoute method to handle connectivity
- inputs: lef, def, preprocessed route guide
- outputs: detail route with optimized wire length and via count
- constraints: route guide must be honored, connectivity constraints, DRC
- Access point: on grid point on metal layer of route guide used to connect to segments on lower and upper layers, pins, IO ports
- access point cluster: union of al APs derived from the same lower layer segment, upper layer segment, pin, or IO port

<img width="374" height="243" alt="image" src="https://github.com/user-attachments/assets/2b00cdf4-24e1-45eb-8e76-ed42814a8ebd" />

(blue is M1, red is M2)
- the blue line is a routed segment, dashed lines are tracks
- essentially, you can connect the routed segment to any of the 5 dashed lines, which are APs -> APC.

<img width="368" height="227" alt="image" src="https://github.com/user-attachments/assets/2387b7b1-7ea2-485f-b2f2-f07f6f9cee88" />

-this is for connecting a pin (red rectangle, red x is access point)

<img width="366" height="201" alt="image" src="https://github.com/user-attachments/assets/a0bfd2d6-a104-4195-8225-e99212fcd5c3" />

- this just shows the from lower layer to upper layer connectivity
- the important thing to consider is how to connect one access point to another (as in 2 on 2 different layers coinciding), and how to do it optimally
- for routing over many layers, you have to consider the interactions between each set of consecutive layers
## Routing topology algorithm and final files list post-route
<img width="502" height="260" alt="image" src="https://github.com/user-attachments/assets/9a3f38b8-0d7b-408b-9205-945a472daa7f" />

- line 2-3: for each APC, need to find the associated cost
- line 6: do minimum spanning tree (mst) for this

