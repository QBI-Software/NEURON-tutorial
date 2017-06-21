---
lesson: 3
layout: default
title: Synaptic Model
---
# A synaptic model

In this lesson, we will be stimulating our neuron with a synaptic input instead of current injection.  There are two types of synaptic input available in NEURON:
1. **Alpha Synapse (AlphaSynapse)** [1] : represents a single conductance transient of a particular peak amplitude that starts at a fixed time. It is not meant to connect neurons but to simulate the impact of a synaptic type of stimulus.
1. **Exponential Synapses (ExpSyn and Exp2Syn)** [2][3] : represent event-driven conductance-changing weighted mechanisms (with single or double decay rate constants, respectively).  They will not activate unless triggered by a Network Connection object (NetCon) where the amplitude is controlled by the NetCon weight but the time course and reversal potential is controlled by the ExpSyn or Exp2Syn.

In NEURON, these are managed as **Point Process** stimuli.

1. Firstly, we will add an **AlphaSynapse** to our Ball and Stick cell created in the previous lesson.
1. Then we will be ready to take a look under the hood of NEURON at the code which runs through the simulator.  The code is written in **HOC (High Order Calculator)** which is an interpretive language loosely resembling C code developed in ancient times as a UNIX interpreter for calculations.  It is now only found in NEURON.  If you have not had any exposure to programming constructs, have a read of the [Help page](help) and we will aim to allow you to understand the code rather than having to write it.

## Part A: Using CellBuilder

1. Launch a fresh version of NEURON via `nrngui`
1. Load up the "Ball and Stick cell" from the file *bs_cell.ses*
1. In the **CellBuilder** window, click on the **Continuous Create** button
1. From the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess**, then select **AlphaSynapse**
1. 

## Part B: Using HOC

### STEP 1: Generate a cell class

From our CellBuilder model, we can generate HOC code to get started.

1. From the **CellBuilder** window, select **Management** then **Cell Type**
1. Click on **Classname** and enter `BScell`
1. Click on **Save hoc code in file** and provide a filename (and path) - here we are calling it `bscell.hoc`

Creating a cell type class, allows us to use this in network models as well as allowing more complexity to be added.  We are now going to see what has been generated.

### STEP 2: Understanding the HOC code

1. Open a plain text editor of your choice (a few recommendations are on the [Setup](setup) page)
2. Open the `bscell.hoc` file
The class is defined by the lines at the top and bottom of the file:

```
begintemplate BSCell
...
endtemplate BSCell
```

**Global variables** are indicated by the starting word: `public`. You should recognize a few names from our CellBuilder model: `soma`, `ap`, etc.

```
public soma, ap
public all, apicals
```

When the code is first loaded in the NEURON interpreter, it runs:

```
create soma, ap[2]
```

This means it generates two variables, one called `soma` and the other is a list with 2 items called `ap` (denoted by square brackets with a 2)

When the NEURON interpreter loads this class, it will look for a method called `init()` ie, initialize

```
proc init() {
  topol()
  subsets()
  geom()
  biophys()
  geom_nseg()
  synlist = new List()
  synapses()
  x = y = z = 0 // only change via position
}
```

Each procedure listed in `init()` is run in sequential order.  So to begin, the first call is to `topol()` (topology). This was where we added the soma and two dendrites in the CellBuilder.  `topol()` is defined as:

```
proc topol() { local i
  connect ap(0), soma(1)
  connect ap[1](0), ap(1)
  basic_shape()
}
```

So here we see that the items in the `ap` list (which contains our dendrites, `ap` and `ap[1]`) are connected to the soma object and each other, respectively. `ap(0)` refers to the **start of the segment** and `ap(1)` refers to the **end of the segment**.  The method then calls `basic_shape()` then returns to `init()` to run the next procedure in the list which is `subsets()` and so on.

### STEP 3: Adding an axon segment via HOC

Now if we want to add an **axon**, we can add this to the code rather than returning to CellBuilder.

1. Add `axon` as a variable - append this to the line `public soma, ap`

```
public soma, ap, axon
```

1. Add axon in the `topol()` procedure

```
proc topol() { local i
  connect ap(0), soma(1)
  connect ap[1](0), ap(1)
  **connect axon(0), soma(0)**
  basic_shape()
}
```
1. Specify the length, diam in the `geom()` procedure
[TODO]

1. Specify Ra, cm and hh in the `biophys()` procedure
[TODO]

Hopefully, it is now apparent that the procedures parallel the tasks undertaken in CellBuilder.

### STEP 4: Adding an AlphaSynapse


### STEP 5: Inserting an NMDA receptor


### STEP 6: Compiling and running the simulation

--------
## References

[Point Process](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#pointprocesses)

[HOC keywords](http://www.neuron.yale.edu/neuron/static/new_doc/programming/ockeywor.html)

[HOC syntax](http://www.neuron.yale.edu/neuron/static/new_doc/programming/hocsyntax.html)

[1]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#AlphaSynapse)

[2]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#ExpSyn)

[3]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#Exp2Syn)
