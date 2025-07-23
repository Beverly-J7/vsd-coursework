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




