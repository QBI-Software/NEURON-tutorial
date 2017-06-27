---
lesson: 5
layout: default
title: Network Model
---
# A Network Model
Now we are going to build a network of two cells. We will build these between two ball and stick neurons but they equally apply to

## Part A:

### STEP A1:

Lets create two of our bs cells.

1. Modify your init.hoc file like so:

```
//=================================================
// Create our cell from the template
//=================================================

n_bsa = 2

objectvar bsa[n_bsa]

for i = 0, n_bsa-1 {
    bsa[i] = new BScellAxon()
}
```
This creates *two* (**n_bsa**) cells; **bsa[0]** and **bsa[1]** both based on the **BScellAxon** template. To create more simply change **n_bsa**.

If you want to make any modifications to both cells simply modify the template bscellaxon.hoc file.

1. Lets also **comment** out the call for the synapse generation within our init() procedure in bscellaxon.hoc:
```
proc init() {
    topol()
    subsets()
    geom()
    biophys()
    geom_nseg()
    //synlist = new List()
    //synapses()
    x = y = z = 0 // only change via position
}
```

1. Do the same for the line that centres the x, y, z position of the cells.

1. Let's have a look at what we have created. Double click init.hoc and type  into the command line:
```
forall {psection()}
```

1. Change Tstop to 200ms and observe how it takes some time for both neurons to approach a plateau potential.

1. Finally, let's add an alpha synapse to just one cell.
Example code: (hidden by dropdown menu)

```
//==================================================
//Stimulation
//==================================================
objectvar syn       // this must be outside the procedure
proc synapses() { // add synapse to the dendrite
  BScellAxon[$1].dend syn = new AlphaSynapse($2) // position is end of dendrite
  syn.onset = 100     // time to onset in ms
  syn.tau	  = 0.1   // rise time constant in ms
  syn.gmax  = 10    // peak conductance	in %mmicro;S (umho)
  syn.e	    = -15   // reversal potential in	mV
  syn.i     = 0     //	nA
}

synapses(0, 1)

```


If you observe the example code provided, we have used two **place holders**, **$1** and **$2**. These placeholders allow you to specify the value in this location when you run the procedure. For example, if we change **synapses(0, 1)** to **synapse(1, 0.5)**, this will instead place our alpha synapse into BScellAxon[1] halfway down the dendrite.

### STEP A2:
Setting the scene.
1. Run init.hoc.

2. Open a runcontrol window and press init & run.

3. Graph a voltage axis and plot the voltage at the soma(0.5) of both cells in different colours (hint right click>color/brush, select the desired cell in the top right).

4. Save the runcontrol and voltage axis in seperate .ses files and make your init.hoc file open them *(make sure to load the voltage axis after you have created the cells in the init.hoc file)*.

### STEP A3:
Now lets try connecting our cells.

Lets make BScellAxon[0] the presynaptic cell, and BScellAxon[1] postsynaptic.

The **NetCon** is the method NEURON uses to connect cells. It works by taking a particular *event* which then drives a *point process* in the post synaptic cell. NetCon cannot drive an Alphasynapse, rather, you can use **ExpSyn** for an alpha-like conductance, or **Exp2Syn** to be able to define the rise and decay time constants.

1. Define the location you want to put the synapse:

```
objectvar net_syn[1]
BScellAxon[1].dend net_syn[0] = new ExpSyn(0.5) //places a ExpSyn point process halfway down the dend of BScellAxon[1]
```

1. The parameters of the synaptic connection a defined when you generate the **NetCon**. This takes the form:
```
new NetCon(&source_v, synapse, threshold, delay, weight)
```
    *source_v* = source voltage - we will use the voltage at the soma of BScellAxon[0]

    *synapse* = refers to the synaptic object receiving the event - net_syn

    *threshold* = threshold of the *source_v* to generate the synaptic process

    *delay* = connection delay

    *weight* = connection weight strength at the synapse. Setting the strength this way allows one cell to generate synapses in multiple locations of different weights.

1. Lets do it!

```
objectvar con_01
con_01 = new NetCon(&BScellAxon[0].axon.v(1), net_syn, -20, 1, 0.5) //places a connection that will trigger net_syn when BScellAxon[0] axon's voltage exceeds -20mV

```

Congratulations!!! You have modelled a simple neural network.

### STEP A4:

Why don't you clean up your network code a little? Its looking filthy.

1. Embed your definitions of net_syn and con_01 into a **proc** like we did for the alpha synapse. However, make sure you declare the **objectvar** **outside** the proc.

1. Why not try setting all your important network parameters as stand alone variables that is called by the *procs* as placeholders? E.g.

```
pre_cell = 0
pre_loc = 1
pre_threshold = -20
net_syn_weight = 1
con_delay = 0.5

objectvar con_01
proc connect_cells () {
  a=$1 //presynaptic cell
  b=$2 //voltage source location
  c=$3 //threshold voltage
  d=$4 //synaptic weight
  e=$5 //synaptic delay
  print "Setting network connection..."
  print "Presynaptic cell: BScellAxon[", a,"], Location: ", b, ", Threshold: ", c, "mV, Synaptic weight: ", d, ", Synaptic delay: ", e, "ms"
  BScellAxon[a].axon con_01 = new NetCon(&v(b), net_syn, c, d, e)
}
connect_cells(pre_cell, pre_loc, pre_threshold, net_syn_weight, con_delay)
```

Why would you do this to yourself? This is an extreme example, but using some of these ideas such as setting variables and using placeholders, will make your code easier to decipher. Also, they come especially in handy when you want to change the parameters of your simulation! Instead of having to go back through your code to find all the places you have to change the particular presynaptic cell, you can just change the pre_cell variable and you are done!

1. Make sure to always add comments to tell yourself and others what it is you are actually doing.  

## Extras:

Have a play around! You get to play god with your own little neural system.

Try creating a 3 cell network e.g. A connects to B connect to C connects back to A.

Try creating a 100 cell network where 99 cells converge on 1 cell but that cell **inhibits** the 99.

Try making the post-synaptic cell the real neuron from the **Complex model** section.

--------

## References
http://web.mit.edu/neuron_v7.4/nrntuthtml/tutorial/tutC.html
