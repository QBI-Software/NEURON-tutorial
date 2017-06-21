---
lesson: 3
layout: default
title: Synaptic Model
---
# A synaptic model

In this lesson, we will be stimulating our neuron with a synaptic input instead of current injection.  There are two types of synaptic input available in NEURON:
1. **AlphaSynapse [1]** : represents a single conductance transient of a particular peak amplitude that starts at a fixed time. It is not meant to connect neurons but to simulate the impact of a synaptic type of stimulus.
1. **ExpSyn[2] and Exp2Syn [3]** : represent event-driven conductance-changing weighted mechanisms (with single or double decay rate constants, respectively).  They will not activate unless triggered by a Network Connection object (NetCon) where the amplitude is controlled by the NetCon weight but the time course and reversal potential is controlled by the ExpSyn or Exp2Syn.

In NEURON, these are managed as **Point Process** stimuli.

## Part A: Using CellBuilder

1. Firstly, we will add an **AlphaSynapse** to our *Ball and Stick cell* created in the previous lesson.
1. Launch a fresh version of NEURON via `nrngui`
1. Load up the "Ball and Stick cell" from the file *bs_cell.ses*
1. In the **CellBuilder** window, click on the **Continuous Create** button
1. From the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess**, then select **AlphaSynapse**
1. By default, the point process is placed on the soma. To change this click on **Show** then **Shape**
1. Click at the other end of the line which places the synapse at the end of the dendrite. You should see:

```
AlphaSynapse[0]
at: dend[1]
```
![alphasyn]

The panel for control of the **AlphaSynapse** has four parameters, enter the initial values:

| Parameter | Description    | Value | Units |
| :------------- | :------------- | :------------- |:------------- |
| `onset` | The time before the change in postsynaptic conductance begins | `0`|ms|
| `tau` | The time to peak of the conductance change | `0.1`| ms|
| `gmax` | The peak conductance change | `10`|µS|
| `e` | The reversal potential for the synaptic current | `-15`|mV|

1. Open a **RunControl** and **Graph -> voltage axis**
1. Run with **Init &amp; Run** where `Tstop` = `10`

![simplealpha_params]  ![simplealpha]  


## Part B: Using HOC

We are now ready to take a look under the hood of NEURON at the code which runs through the simulator.  The scripting code is written in **HOC (High Order Calculator)** which is an interpretive language loosely resembling C code developed in ancient times as a UNIX interpreter for calculations.  It is now only found in NEURON.  If you have not had any exposure to programming constructs, have a read of the [Help page](help) and we will aim to allow you to understand the code rather than having to write it.

**Recommended structure of a HOC program:**

+ specify model topology (create sections, connect sections)
+ specify model geometry (stylized (L, diam) or pt3d method as appropriate)
+ specify instrumentation (IClamps, SEClamps, other Point Processes, graphs)
+ specify simulation flow control (RunControl panel is sufficient for most purposes)

### STEP 1: Generate a cell class

From our CellBuilder model, we can generate HOC code to get started.

1. From the **CellBuilder** window, select **Management** then **Cell Type**
1. Click on **Classname** and enter `BScell`
1. Click on **Save hoc code in file** and provide a filename (and path) - here we are calling it *bscell.hoc*

Creating a cell type class, allows us to use this in network models as well as allowing more complexity to be added.  We are now going to see what has been generated.

### STEP 2: Understanding the HOC code

1. Open a plain text editor of your choice (a few recommendations are on the [Setup](setup) page)
2. Open the *bscell.hoc* file
The class is defined by the lines at the top and bottom of the file:

```
begintemplate BScell
...
endtemplate BScell
```

**Global variables** are indicated by the starting word: `public`. You should recognize a few names from our CellBuilder model: `soma`, `ap`, etc.

```
public soma, dend
public all
```

When the code is first loaded in the NEURON interpreter, it creates the `soma` and `dend`:

```
create soma, dend
```

Then it will look for a method called `init()` (which stands for initialize).

```c
proc init() {
  topol()               //Runs first
  subsets()             //Runs second
  geom()
  biophys()
  geom_nseg()
  synlist = new List()
  synapses()
  x = y = z = 0 // only change via position
}
```

Each procedure listed in `init()` is run in sequential order.  So to begin, the first call is to `topol()` (topology). This was where we added the soma and two dendrites in the CellBuilder.  `topol()` is defined as:

```c
proc topol() { local i
  connect dend(0), soma(1)
  basic_shape()
}
```

So here we see that the `dend` is connected to the soma object. Each segment is indexed from 0 to 1 along it's length so that `dend(0)` refers to the **start of the segment** and `dend(1)` refers to the **end of the segment**.  The method then calls `basic_shape()` then returns to `init()` to run the next procedure in the list which is `subsets()` and so on.

### STEP 3: Adding an axon segment via HOC

Now if we want to add an **axon**, we can add this to the code rather than returning to **CellBuilder**.

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Axon | `axon` | 800 | 1 | hh |

1. Add `axon` as a variable - append this to the line `public soma, dend`

```
public soma, dend, axon
```

1. Add `axon` in the `topol()` and `basic_shape()` procedures

#### Geometry parameters explained

| Parameter     | Symbol | Description     |
| :------------- | :------------- |:------------- |
| Length | L  | For each section, L is the length of the entire section in microns.       |
| Segments | nseg | The section is divided into nseg compartments of length L/nseg. Membrane potential will be computed at the ends of the section and the middle of each compartment. |
| Diameter | diam | The diameter in microns. Note that diam is a range variable and therefore must be respecified whenever nseg is changed.|
| Resistivity | Ra | Axial resistivity in ohm-cm.|
| connectivity | | Established with the connect command and defines the parent of the section, which end of the section is attached to the parent, and where on the parent the attachment takes place. To avoid confusion, it is best to attach the 0 end of a section to the 1 end of its parent.[4]|

```c
proc topol() { local i
  connect dend(0), soma(1)
  connect axon(0), soma(0)                                        //Add this line
  basic_shape()
}

proc basic_shape() {
  soma {pt3dclear() pt3dadd(0, 0, 0, 1) pt3dadd(15, 0, 0, 1)}      //pt3dadd(x,y,z,diam pt)
  dend {pt3dclear() pt3dadd(15, 0, 0, 1) pt3dadd(150, 0, 0, 1)}
  axon {pt3dclear() pt3dadd(0, 0, 0, 1) pt3dadd(-119, 0, 0, 1)}    //Add this line
}

```

1. Specify the length, diam in the `geom()` procedure.
Note the different possible notations here which all do the same thing:
    1. `axon` `diam` = 20   //separated by space
    1. `axon.diam` = 20     //separated by dot
    1. `axon` {             //grouped with other parameters by {}
      `diam` = 20
    }
    1. `access axon`    //sets the default section
       `diam = 20`      //so it can be followed without further specifying


```c
proc geom() {
  forsec all {  }
   soma.L = 20
   dend.L = 1000
   soma.diam = 20
   dend.diam = 5
   axon.L = 800             //Add this
   axon.diam = 1            //Add this
}
```

1. To specify *hh* in the `biophys()` procedure:
 - make a copy of the `soma` code and call it `axon`
(Alternatively, the common code could be moved to the `forsec all` and `soma` and `axon` sections deleted but this makes it harder to manage if more sections are added later or if parameters are changed.)

```c
proc biophys() {
  forsec all {
     Ra = 160
     cm = 1
   }
   soma {
     insert hh
       gnabar_hh = 0.12
       gkbar_hh = 0.036
       gl_hh = 0.0003
       el_hh = -54.3
   }
   dend {
     insert hh
       gnabar_hh = 0.012
       gkbar_hh = 0.0036
       gl_hh = 0.0003
       el_hh = -64
   }
   axon {                              //Copy soma here and rename as axon
     insert hh                         //insert HH Mechanism
       gnabar_hh = 0.12                //define the properties of the HH Mechanism
       gkbar_hh = 0.036
       gl_hh = 0.0003
       el_hh = -54.3
   }
 }

```

1. The final addition is the `subsets()` procedure where the axon is added to the SectionList:

```c
proc subsets() { local i
  objref all
  all = new SectionList()
    soma all.append()
    dend all.append()
    axon all.append()
}
```
1. Now save this to a new file *bscellaxon.hoc* and load into NEURON with **File -> load hoc**
1. Have a look in CellBuilder at the new axon

![axoncell]

> Hopefully, it is now apparent that the procedures parallel the tasks undertaken in CellBuilder.

### STEP 4: Checking sections in the console

The **NEURON GUI** is really a wrapper around code which can be run directly from the commandline.  
In the console window, type

```
oc> soma psection()
oc> forall psection()
```

![console_soma]


### STEP 4: Adding an AlphaSynapse


### STEP 5: Inserting an NMDA receptor


### STEP 6: Compiling and running the simulation

--------
## References

[Point Process](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#pointprocesses)

[HOC keywords](http://www.neuron.yale.edu/neuron/static/new_doc/programming/ockeywor.html)

[HOC syntax](http://www.neuron.yale.edu/neuron/static/new_doc/programming/hocsyntax.html)

[1]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#AlphaSynapse) "AlphaSynapse definition"

[2]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#ExpSyn) "ExpSynapse definition"

[3]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#Exp2Syn) "Exp2Synapse definition"

[4]:(https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/geometry.html) "Geometry definition"

[alphasyn]: {{ site.github.repository_url }}/raw/gh-pages/img/Alphasynapse_shape.PNG "AlphaSynapse shape"

[simplealpha]: {{ site.github.repository_url }}/raw/gh-pages/img/Simplealpha.PNG "Simple AlphaSynapse"

[simplealpha_params]: {{ site.github.repository_url }}/raw/gh-pages/img/Simplealpha_params.PNG "Simple AlphaSynapse Parameters"

[axoncell]: {{ site.github.repository_url }}/raw/gh-pages/img/Axoncell.PNG "Axon added to BS cell"

[console_soma]:{{ site.github.repository_url }}/raw/gh-pages/img/Console_soma.PNG "Console with soma"
