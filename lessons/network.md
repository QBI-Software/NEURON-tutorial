---
lesson: 5
layout: default
title: Network Model
---
# A Network Model
Now we are going to build a small network of connected cells. We will use the template we created in the previous lesson (**BScell**) but the steps apply equally to more complex models such as those based on neuron reconstructions you have uploaded.

## NetCon

In NEURON, a connection between cells is created with a Network Connection object (**NetCon**). A NetCon is defined by the following five parameters:

| Component     | Description    |
| :------------- | :------------- |
| Source       | The source is normally a reference to a membrane potential but any range variable can be used. The source may also be a POINT_PROCESS with a NET_RECEIVE block which contains a call to net_event (these serve as entire artificial cells).  The source may even be a NULLObject.  |
| Target  |The target must be a POINT_PROCESS that defines a NET_RECEIVE procedure. The target is allowed to be nil (NULLObject) in which case the NetCon is always inactive but this can be useful for recording the spike train from an output cell.|
| Threshold |(Optional) If not specified the default value is 10 (mV). For all NetCon objects that share a source, you can only specify one threshold. |
| Delay |(Optional) If not specified the default value is 1 (ms) |
| Weight |(Optional) If not specified the default value is 0 |

When the `source` passes `threshold`, the `target` will receive an **event** at time t (`delay`) along with `weight` information. There is no limit on delay and there is no limit on the number of events pending delivery.


### STEP 1: Creating multiple cells

We will use the template BScell to create multiple cells.

>See Help page for an easy way to rerun HOC files

1. Open **bs_run.hoc** in an editor and save it as a new file **bs_net_run.hoc**
1. We will change the single cell `objectvar bs` to a list of 2 cells `objectvar bs[2]` where the square brackets represents an array of items.
1. We can then add a cell to each place in the list where `0` is the first place:

```c
bs[0] = new BScell()
bs[1] = new BScell()

```

1. To make this more versatile, we can instead create a programming loop where the number of cells to be created is stored as `n_bs` so if more cells are required, simply change the value of `n_bs`.
1. The format of the for loop is: `for var = lower-bound, upper-bound command`

```c
//=================================================
// Create multiple cells from the template
//=================================================

n_bs = 2

objectvar bs[n_bs]

for i = 0, n_bs-1 {           // i is the counter, set to 0 to start then
    bs[i] = new BScell()      // increments by 1 each loop until n_bs - 1
}
```

### STEP 2: Changes to template

Any modifications to the template `bscell.hoc` file will affect both cells.

1. As we will not be requiring the AlphaSynapse component on every cell, **comment** out the call for the synapse generation within our `init()` procedure
1. We will need to create one stimulating synapse however, so we will add the `synapses()` proc as a public variable so we can call it from another file

```c
public init, topol, basic_shape, subsets, geom, biophys, geom_nseg, biophys_inhomo, synapses

...

proc init() {
    topol()
    subsets()
    geom()
    biophys()
    geom_nseg()
    synlist = new List()
    //synapses()
    x = y = z = 0 // only change via position()
}
```

As we are adding multiple cells, to view them in the space plot, we will need to change the topology or they will lie exactly on top of each other.

1. We can reposition cells dynamically by adding a call to `position()`  in **bs_net_run.hoc**

```c
for i = 0, n_bs-1 {           // i is the counter, from 0 to n_bs-1
    bs[i] = new BScell()      // a new cell for each loop
    z = i * 100
    x = i * 10
    y = i * 10
    bs[i].position(x,y,z)      // reposition cells for viewing
}

```

### STEP 3: Creating an ExpSyn

We have created two cells which we will now connect.

![netcondiagram]

Returning to the **bs_net_run.hoc**, we have a list called `bs` which contains 2 cells at [0] and [1].  We will make `bs[0]` the presynaptic cell, and `bs[1]` the postsynaptic cell.

1. As in the diagram, we will include an AlphaSynapse as our initial stimulus.  We have the code for this already so we can just refer to it and apply it to our presynaptic cell:

```c
//Create a stimulus on cell 0
bs[0].synapses()

```

Now we will create a receiving synapse on the postsynaptic cell.

1. Declare a variable to hold the postsynaptic POINT_PROCESS called `net_syn`
1. Create a **ExpSyn** POINT_PROCESS and locate it halfway down the dendrite (0.5)

```c
//Create a receiving synapse on cell 1
objectvar net_syn[1]
bs[1].dend net_syn[0] = new ExpSyn(0.5)

```

### STEP 4: Creating a connection with NetCon

1. We will create the actual connection with a **NetCon** object which will trigger when the presynaptic source reaches threshold
1. The parameters of the synaptic connection are defined on creation of the **NetCon**

```c
new NetCon(&source_v, synapse, threshold, delay, weight)

```

    `source_v` = source will be the presynaptic membrane potential (`bs[0].soma.v(1)`)

    `synapse` = refers to the ExpSyn receiving the event stored in an array (`net_syn[0]`)

    `threshold` = potential in mV of the `source_v` to trigger the event

    `delay` = time (ms) after the triggered event is received

    `weight` = which represents the strength of the synapse

1. Add the NetCon object to the synapse list in the postsynaptic cell, with reference to the soma membrane potential (mV) of the presynaptic cell.

```c
objectvar con
con = new NetCon(&bs[0].soma.v(1), net_syn[0], -20, 1, 0.5)
bs[0].soma bs[1].synlist.append(con)

```
### Overview

The full code for **bs_net_run.hoc** should now look like:

```c
/* bs_net_run.hoc for BScell
*/

// start the GUI
load_file("nrngui.hoc")
// load our hoc file
load_file("bscell.hoc")
//=================================================
// Create our cell from the template
//=================================================
n_bs = 2

objectvar bs[n_bs]

for i = 0, n_bs-1 {           // i is the counter, set to 0 to start then
    bs[i] = new BScell()      // increments by 1 each loop until n_bs - 1
    z = i * 100
    x = i * 10
    y = i * 10
    bs[i].position(x,y,z)      // reposition cells for viewing
}

//=================================================
// Create our connections for neural network
// We will stimulate cell 0 and record from cell 1
//=================================================
//Create a stimulus on cell 0
bs[0].synapses()
//Create a receiving synapse on cell 1
objectvar net_syn[1]
bs[1].dend net_syn[0] = new ExpSyn(0.5)
//Create a connection from cell0 to cell1
objectvar con
con = new NetCon(&bs[0].soma.v(1), net_syn[0], -20, 1, 0.5)
bs[0].soma bs[1].synlist.append(con)

//==================================================
//Run Control and temperature
//==================================================
celsius = 6.3 //default as using HH defaults
tstop = 20
dt = 0.025 //ms 40khz

```

### STEP 5: Run network simulation

1. Load the **bs_net_run.hoc** into a fresh version of NEURON with `nrngui`.
1. Open the following windows:
   1. Run Control
   1. Graph voltage:
       1. Select **Plot what?**
       1. Click on **Show**
       1. Select **Object refs**
       1. Double-click on `bs[0]`
       1. In the middle panel, double-click on `soma`
       1. In the third panel, double-click on `v(0.5)`
       1. Click on **Accept**
       1. Repeat these steps for `bs[1]`
       1. Right-click menu, select **Delete**, click on `v(0.5)` to remove it
       1. Right-click menu, select **Color/Brush**, select a color, click on `bs[0].soma.v(0.5)`
       1. Repeat for `bs[1]` with another color
   1. Graph shape plot:
       1. Right-click menu, select **Shape plot**
       1. Right-click menu, select **3D rotate**
       1. Drag cursor along z-axis until 2 lines are showing (these are your cells!)
       1. Right-click menu, select **Shape style** then **Show diam** (this indicates which is the axon)

1. Save the session as **bs_net.ses**
1. At the end of the **bs_net_run.hoc**, add a line

```
load_file("bs_net.ses")
```
Now when the **bs_net_run.hoc** file is loaded, the windows will also appear, ready to run your simulation.

![SimpleNetwork]

Try increasing the value of the `weight` variable to `0.8` - makes a difference right?!

![SimpleNetwork2]

>Congratulations!!! You have modelled a simple neural network.

### STEP 6: Improving the code

We have provided this code within the **bs_net_run.hoc** but it is often better to separate this into a separate file such as **stimulus.hoc** and include a call in the **bs_net_run.hoc** to load it.  This allows you to use the same code for different models.  However, the code itself must be modified to use variable parameters that do not explicitly refer to the template. To manage this:

1. Put code into procedures to reduce duplication of code and thus reduce likelihood of errors. *Note, make sure you declare the `objectvar` outside the proc so it can be accessed by other procs.*
1. Set parameters as **variables** to reduce the effort in finding all the places a certain parameter is referenced. *Note use obvious names rather than a,b,c etc so it is clear what they are referring to but also check that they do not clash with global variables*
1. Refer to parameters with placeholders (ie $1, $2) which allows them to be loaded as arguments to the procedures.
1. It is best to add comments to tell yourself and others what it is you are actually doing but you can also use **print** statements which output to the console and let you keep track of what is going on.  

```c
//declare variables
pre_cell = 0
pre_loc = 1
pre_threshold = -20
net_syn_weight = 1
con_delay = 0.5

//declare proc
objectvar con
proc connect_cells () {
  i=$1 //presynaptic cell
  vm=$2 //voltage source location
  t0=$3 //threshold voltage
  weight=$4 //synaptic weight
  t=$5 //synaptic delay
  print "Setting network connection..."
  print "Presynaptic cell: BScell[", i,"], Location: ", vm, ", Threshold: ", t0, "mV, Synaptic weight: ", weight, ", Synaptic delay: ", t , "ms"
  bs[i].axon con = new NetCon(&v(vm), net_syn, t0, weight, t)
}

//call proc
connect_cells(pre_cell, pre_loc, pre_threshold, net_syn_weight, con_delay)

```

>So we have covered many of the basic functions of NEURON which should help you get started. There are many more functions available and it is recommended that you work through the NEURON online tutorials but also learn by example from the many models available in ModelDB.

## NMODL and NOCMODL

For a more advanced use of NEURON, membrane mechanisms with complex activation kinetics can be developed and inserted into sections.

The kinds of mechanisms that can be added are:
+ Channels in which the model consists of current-voltage relationships
+ Calculation of internal and external ionic concentration changes due to currents carried by specific ions

Many user defined mechanisms can be simultaneously inserted into sections in NEURON. NEURON will keep track of the total current for each ionic species used and the effect of that current on the membrane potential.

For example, suppose a calcium pump, sodium-calcium exchanger, calcium channel, radial calcium diffusion, and calcium activated potassium mechanisms are inserted into a cable section. Then the total calcium current is calculated as the sum of the individual currents from the calcium pump, exchanger, and channel. The internal calcium concentration just under the membrane is calculated from the total calcium current and diffusion away from the surface. The potassium current through the `cagk` channel is calculated from the internal calcium concentration next to the membrane and the membrane potential. And the membrane potential is calculated from the total current. (The above is only a partial list of the interactions among these channels. The point is that the ionic current, membrane voltage, and concentration computations are consistent regardless of the channels inserted into the cable section.)

Mechanisms are normally local. That is they do not depend on what is happening at other places on the neuron. However, a method exists for writing mechanisms that depend on variables of mechanisms at other locations. For example the calcium concentration at a presynaptic mechanism can be used to calculate the conductance change at a postsynaptic mechanism.  

The language for specifying these mechanisms is called NMODL (NEURON model description language) and the model description translator that emits code suitable for NEURON V3 is called NOCMODL. NMODL and NOCMODL handle identical input model descriptions, they differ merely in the output interface code.

Compiling the code for NMODL mechanisms is specific to the operating system you are on so they cannot just be copied from one system to another.  

+ A compiler called **mknrndll** is provided with your NEURON download which will generate the c libraries from NMODL.
+ A utility **modlunit** is provided which should be used to check for problems with the code.


## ModelDB

Ref: [https://senselab.med.yale.edu/modeldb/](https://senselab.med.yale.edu/modeldb/)
A great resource is ModelDB, a database of models for NEURON.  Models can be found by:
+ Cell type
+ Ion channel type
+ Receptor type
+ Synapse type
+ Networks

Usually the code can be viewed online and often models can be run directly online using an embedded interpreter. When you find one you are really interested in, download and carefully follow the instructions for running the code (not everyone follows recommended conventions).  *Be aware that differences in versions of c libraries may cause issues on some systems and errors may be difficult to fix.*

QBI has NEURON available on the HPC cluster but the NEURON community has also provided an online accessible HPC environment: [NeuroscienceGateway](https://www.nsgportal.org/software.html)

There are also many resources listed on this site: [https://senselab.med.yale.edu/ModelDB/mdbresources.cshtml](https://senselab.med.yale.edu/ModelDB/mdbresources.cshtml) although some are more reliable than others!

--------
## Experiments

Have a play around! You get to play god with your own little neural system.

1. Try creating a 3 cell network e.g. A connects to B connect to C connects back to A.

1. Try creating a 100 cell network where 99 cells converge on 1 cell but that cell **inhibits** the 99.

1. Try making the post-synaptic cell the real neuron from the **Complex model** section.

1. Turn the shape plot into a time plot, then select portions of each cell by clicking on them for a comparative plot.

1. Turn the shape plot into a space plot to see the potential change along the whole neuron - slow down the time by reducing dt.

--------

## Resources
[ExpSyn](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#ExpSyn)

[Exp2Syn](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#Exp2Syn)

[NetCon](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/classes/netcon.html#NetCon)

[NMODL](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/nmodl/nmodl.html)

[netcondiagram]: {{ site.github.repository_url }}/raw/gh-pages/img/Netcon_diagram.png "NetCon diagram"

[SimpleNetwork]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleNetwork.PNG "Simple Network"

[SimpleNetwork2]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleNetwork2.PNG "Simple Network - higher weight value"
