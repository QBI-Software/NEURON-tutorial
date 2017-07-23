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
| Source       | The source is normally a reference to a membrane potential but any range variable can be used. The source may also be a POINT_PROCESS with a NET_RECEIVE block which contains a call to net_event. PointProcesses like this serve as entire artificial cells.  The source may be a NULLObject.  |
| Target  |The target must be a POINT_PROCESS that defines a NET_RECEIVE procedure. The target is allowed to be nil (NULLObject) in which case the NetCon is always inactive but this can be useful for recording the spike train from an output cell.|
| Threshold |(Optional) If not specified the default value is 10 (mV). For all NetCon objects that share a source, you can only specify one threshold. |
| Delay |(Optional) If not specified the default value is 1 (ms) |
| Weight |(Optional) If not specified the default value is 0 |

When the `source` passes `threshold`, the `target` will receive an **event** at time t (`delay`) along with `weight` information. There is no limit on delay and there is no limit on the number of events pending delivery.



### STEP 1: Creating multiple cells

We will use the template BSCell to create multiple cells.

1. Open `bs_run.hoc` in an editor
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

1. As we will not be using the AlphaSynapse component, **comment** out the call for the synapse generation within our `init()` procedure, so it won't run
1. Do the same for the line that centres the x, y, z position of the cells as this will exactly overlay the cells so they won't be seen

```c
proc init() {
    topol()
    subsets()
    geom()
    biophys()
    geom_nseg()
    //synlist = new List()
    //synapses()
    //x = y = z = 0 // only change via position
}
```

### STEP 3: Creating an ExpSyn

We have created two cells which we now will connect.

Returning to the **bs_run.hoc**, we will make `BScell[0]` the presynaptic cell, and `BScell[1]` the postsynaptic cell.

1. Declare a variable to hold the postsynaptic POINT_PROCESS called `net_syn`
1. Create a **ExpSyn** POINT_PROCESS and locate it halfway down the dendrite (0.5)

```c
objectvar net_syn[1]
BScell[1].dend net_syn[0] = new ExpSyn(0.5) //places an ExpSyn halfway on dend of BScell[1]

```

### STEP 4: Creating a connection with NetCon

1. Create a **NetCon** object which will trigger when the presynaptic source reaches threshold
1. The parameters of the synaptic connection are defined on creation of the **NetCon**

```c
new NetCon(&source_v, synapse, threshold, delay, weight)
```
    *`source_v`* = source will be the presynaptic membrane potential (`BScell[0].axon.v(1)`)

    *`synapse`* = refers to the ExpSyn receiving the event (`net_syn[0]`)

    *`threshold`* = potential in mV of the *source_v* to trigger the event

    *`delay`* = time (ms) after the triggered event is received

    *`weight`* = which represents the strength of the synapse

1. Add the following code in **bs_run.hoc**

```c
objectvar con
con = new NetCon(&BScell[0].axon.v(1), net_syn[0], -20, 1, 0.5)

```

>Congratulations!!! You have modelled a simple neural network.

### STEP 5: Improving the code

We have provided this code within the **bs_run.hoc** but it is often better to separate this into a separate file such as **stimulus.hoc** and include a call in the **bs_run.hoc** to load it.  This allows you to use the same code for different models.  However, the code itself must be modified to use variable parameters that do not explicitly refer to the template. To manage this:

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
  BScell[i].axon con = new NetCon(&v(vm), net_syn, t0, weight, t)
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

as well as whole networks and synapses and more.  Usually the code can viewed online and often models can be run directly on line using an embedded interpreter. When you find one you are really interested in, download and carefully follow the instructions for running the code.  *Be aware that differences in versions of c libraries may cause issues on some systems and errors may be difficult to fix.*

QBI has NEURON available on the HPC cluster but the NEURON community has also provided an online accessible HPC environment: [NeuroscienceGateway](https://www.nsgportal.org/software.html)

There are also many resources listed on this site: [https://senselab.med.yale.edu/ModelDB/mdbresources.cshtml](https://senselab.med.yale.edu/ModelDB/mdbresources.cshtml) although some are more reliable than others!

--------
## Experiments

Have a play around! You get to play god with your own little neural system.

1. Try creating a 3 cell network e.g. A connects to B connect to C connects back to A.

1. Try creating a 100 cell network where 99 cells converge on 1 cell but that cell **inhibits** the 99.

1. Try making the post-synaptic cell the real neuron from the **Complex model** section.

--------

## Resources
[ExpSyn](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#ExpSyn)

[Exp2Syn](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#Exp2Syn)

[NetCon](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/classes/netcon.html#NetCon)

[NMODL](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/nmodl/nmodl.html)
