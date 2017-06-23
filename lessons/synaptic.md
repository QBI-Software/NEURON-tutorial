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

Click on **Show** then **Parameters** which shows the panel for control of the **AlphaSynapse** which has four parameters:

| Parameter | Description    | Value | Units |
| :------------- | :------------- | :------------- |:------------- |
| `onset` | The time before the change in postsynaptic conductance begins | `0`|ms|
| `tau` | The time to peak of the conductance change | `0.1`| ms|
| `gmax` | The peak conductance change | `10`|µS|
| `e` | The reversal potential for the synaptic current | `-15`|mV|

1. Enter the values as shown in the table
1. Open a **RunControl** and **Graph -> voltage axis**
1. Run with **Init &amp; Run** where `Tstop` = `10`

![simplealpha_params]  ![simplealpha]  


## Part B: Using HOC

We are now ready to take a look under the hood of NEURON at the code which runs through the simulator.  The scripting code is written in **HOC (High Order Calculator)** which is an interpretive language loosely resembling C code developed in ancient times as a UNIX interpreter for calculations.  It is now only found in NEURON.  If you have not had any exposure to programming constructs, have a read of the [Help page](help) and we will aim to allow you to understand the code more than having to write it.

**Recommended structure of a HOC program:**

1. specify model topology (create sections, connect sections)
1. specify model geometry (stylized (L, diam) or pt3d method as appropriate)
1. specify instrumentation (IClamps, SEClamps, other Point Processes, graphs)
1. specify simulation flow control (RunControl panel is sufficient for most purposes)

#### Commands in the console

A console window running the HOC interpreter is launched when `nrngui` is run. The HOC code can be run directly from the commandline which can be useful.  In the console window, check you see `oc>` (if not, just press "Enter" on the keyboard) then type:

```
oc> soma psection()
```

![console_soma]

The output will be explained as we investigate HOC further.


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
#### Variables

+ **Global variables** are indicated by the starting word: `public`. You should recognize a few names from our CellBuilder model: `soma`, `dend`
+ **Local variables** are indicated by the starting word: `objref` or `objvar `

```
public soma, dend
public all
```
#### Program Sequence

When the code is first loaded in the NEURON interpreter, it creates the `soma` and `dend` sections:

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

So here we see that the `dend` is connected to the soma object. Each section is indexed from 0 to 1 along it's length so that `dend(0)` refers to the **start of the section** and `dend(1)` refers to the **end of the section**.  

![connect]

The method then calls `basic_shape()` then returns to `init()` to run the next procedure in the list which is `subsets()` and so on.

### STEP 3: Adding an axon section via HOC

Now if we want to add an **axon**, we can add this to the code rather than returning to **CellBuilder**.

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Axon | `axon` | 800 | 1 | hh |

1. Declare `axon` as a variable - append this to the line `public soma, dend`

```
public soma, dend, axon
```

#### Geometry parameters explained

It is important to refer to the `soma` and `dendrite` as *sections* rather than *segments*. A **section** contains one or more segments (`nseg`) and it is by dividing the section this way into smaller compartmental units which can provide the fine-grained detail underlying a more realistic model.

Each section has the following parameters:

| Parameter     | Symbol | Description     |
| :------------- | :------------- |:------------- |
| Length | L  | For each section, L is the length of the entire section in microns.       |
| Segments | nseg | The section is divided into nseg compartments of length L/nseg. Membrane potential will be computed at the ends of the section and the middle of each compartment. |
| Diameter | diam | The diameter in microns. Note that diam is a range variable and therefore must be respecified whenever nseg is changed.|
| Resistivity | Ra | Axial resistivity in ohm-cm.|
| connectivity | connect | Defines the parent of the section, which end of the section is attached to the parent, and where on the parent the attachment takes place [4].|


1. Add `axon` in the `topol()` and `basic_shape()` procedures

```c
proc topol() { local i
  connect dend(0), soma(1)
  connect axon(0), soma(0)     //Add this line
  basic_shape()
}

proc basic_shape() {
  //The parameters for this function are: pt3dadd(x,y,z,diam pt)
  soma {pt3dclear() pt3dadd(0, 0, 0, 1) pt3dadd(15, 0, 0, 1)}      
  dend {pt3dclear() pt3dadd(15, 0, 0, 1) pt3dadd(150, 0, 0, 1)}
  //Add this line
  axon {pt3dclear() pt3dadd(0, 0, 0, 1) pt3dadd(-119, 0, 0, 1)}    
}

```


#### Different possible notations which all do the same thing:

| Notation | Example |Explanation     |
| :------------- | :------------- |:------------- |
| Space Notation | `axon` `diam`  = 20     | separated by space       |
| Dot Notation | `axon.diam` = 20    | separated by dot |
| Stack Notation | `axon` { `diam` = 20 } | grouped with other parameters by {} |
| Default access | `access axon` |  sets the default section ... |
| |  `diam = 20` | ...so it can be followed without further specifying |


1. Specify the length and diam in the `geom()` procedure.

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
#### Repeated actions with Loops

HOC provides a **for loop** type construct to apply repeated actions to multiple sections. Most commonly this is the `forsec` (for section) function for example, `forsec all` which can also be written as `forall` [5].

1. To specify *hh* in the `biophys()` procedure, make a copy of the `soma` code and call it `axon`.
(Alternatively, the common code could be moved to the `forsec all` and `soma` and `axon` sections deleted but this makes it harder to manage later.)

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
   axon {                      //Copy soma here and rename as axon
     insert hh                 
       gnabar_hh = 0.12        
       gkbar_hh = 0.036
       gl_hh = 0.0003
       el_hh = -54.3
   }
 }

```
#### SectionList

Sections are added to a list called a **SectionList** to group components together under one reference name [6].
1. In the `subsets()` procedure add the `axon` to a SectionList called `all`:

```c
proc subsets() { local i
  objref all
  all = new SectionList()
    soma all.append()
    dend all.append()
    axon all.append()
}
```
------------------

<div class="alert alert-info">
 <h4>Save Me</h4> <p>You can save this to a file called <i>bscellaxon.hoc</i>.</p>
</div>


> Hopefully, it is now apparent that the HOC code is underlying the tasks undertaken in CellBuilder.

### STEP 4: Adding an AlphaSynapse

1. Locate the procedure `synapses()` near the end of the file. We will enter our code within the `{}`

```c
proc synapses() {}
```

1. To insert an AlphaSynapse, we create an instance of it with

```c
objectvar syn
syn = new AlphaSynapse(x)
```
where `x` is the location on the section.

1. So to insert an AlphaSynapse at the distal end of the dendrite, we specify the section first and the location as `1`:

```c
objectvar syn
dend syn = new AlphaSynapse(1)
```

2. Now we can set the properties as per our table, similar to the parameters window in the previous lesson:

```c
syn.onset = 0     // time to onset in ms
syn.tau	  = 0.1   // rise time constant in ms
syn.gmax  = 10    // peak conductance	in &mmicro;S (umho)
syn.e	    = -15   // reversal potential in	mV
syn.i     = 0     //	nA
```

so the final code is:

```c
objectvar syn       // this must be outside the procedure
proc synapses() {
  access dend       // add synapse to the dendrite
  syn = new AlphaSynapse(1) // position is end of dendrite
  syn.onset = 0     // time to onset in ms
  syn.tau	  = 0.1   // rise time constant in ms
  syn.gmax  = 10    // peak conductance	in %mmicro;S (umho)
  syn.e	    = -15   // reversal potential in	mV
  syn.i     = 0     //	nA
}

```




### STEP 5: Running the HOC code

So now how do we get this code into NEURON.

#### Loading the HOC

To recap, what we have done so far is create a *template* for a neuron.  The neuron itself will not exist until we run a command such as (where `bsa` is our neuron):

```c
objectvar bsa

bsa = new BScellAxon()
bsa init()              // not strictly necessary but ensures it is run
```

1. Create a file called `init.hoc` which we will use to set everything up.
1. We will need to load the NEURON libraries via `nrngui.hoc`
1. Then we load our template file `bscellaxon.hoc`
1. Then we add our code above to create an instance of our template called `bsa`

The contents of `init.hoc` should look like:

```c
/* init.hoc for BScellAxon
*/

// start the GUI
load_file("nrngui.hoc")
// load our hoc file
load_file("bscellaxon.hoc")
//=================================================
// Create our cell from the template
//=================================================
objectvar bsa

bsa = new BScellAxon()
bsa init()

//==================================================
//Run Control
//==================================================
tstop = 20
dt = 0.025 //ms 40khz

```

(*Note we have also set a couple of variables for the RunControl panel*)

1. Now relaunch NEURON (`nrngui`)
1. Reset working dir with **File -> recent dir**
1. Open `init.hoc` with **File -> load hoc**
1. All you should now see is a console window as shown here (any errors will show up here):

![console_init]

To test that our code has actually loaded we will use the console commands:

```
oc> bsa
oc> bsa.soma psection()
oc> bsa.dend psection()

```

This should show the following output:

![console_bsa]

If it works, you can see that
+ the `bsa` object is our template `BScellAxon`
+ the `soma` has all the properties we set in the template code
+ the `dendrite` also has the `AlphaSynapse` connected and ready to go


#### Runnning the HOC

Now we will launch a couple of windows to run the code (this can also be automated but let's just do it this way for now).

1. Open the **File -> RunControl** (you will see our parameters in the `init.hoc` are set)
1. Open a **Graph -> Voltage Axis** window
1. Click on **Init &amp; Run** -> Voila!

![hocoutput]

> CONGRATULATIONS! This is no mean feat and you deserve a MEDAL!!

#### Creating a Space Plot

A space plot allows you to view the changes in conductance along a section.

1. To create a space plot, select **Shape plot** from the **Graph** menu in the NEURON Main Menu.
1. From this window, you can select from the plot menu with the *right mouse button*.
1. Select a **Space Plot**.
1. By default, voltage is the variable to plot, but you can change this with the **Plot What?** menu item.
1. The plot shown is a schematic of our neuron so just looks like a straight line.

![space1]

1. To create the space plot graph, you need to select a section of the neuron to include in the space plot by clicking the left mouse button at the beginning of the section, dragging the mouse across the section you want to plot (while holding the mouse button down), and releasing the mouse button when you have covered the sections you want to plot. A line will appear from where you first clicked the mouse button to the current location of the pointer. When you release the mouse button, the sections you selected will be highlighted in colour, and a new window with the space plot will be opened.
1. Press the **Init &amp; Run** button in the **RunControl** window to see the space plot in action.
1. You may have to zoom in with **right-click menu -> View... -> View = plot**

![space2]

2. You can rotate the axes by selecting **3D Rotate** from the **Graph Properties menu**. (This won't do much with our model but good for branching dendrites.)
3. The middle mouse button is used to move the whole representation.

--------
## References

[Point Process](https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#pointprocesses)

[HOC keywords](http://www.neuron.yale.edu/neuron/static/new_doc/programming/ockeywor.html)

[HOC syntax](http://www.neuron.yale.edu/neuron/static/new_doc/programming/hocsyntax.html)

[Programming HOC guide](http://www.neuron.yale.edu/neuron/static/new_doc/modelspec/programmatic.html)

[1]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#AlphaSynapse "AlphaSynapse definition"

[2]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#ExpSyn "ExpSynapse definition"

[3]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/mech.html#Exp2Syn "Exp2Synapse definition"

[4]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/geometry.html "Geometry definition"

[5]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/secspec.html "Section access definition"

[6]:https://www.neuron.yale.edu/neuron/static/docs/help/neuron/neuron/classes/seclist.html#SectionList "SectionList definition"

[alphasyn]: {{ site.github.repository_url }}/raw/gh-pages/img/Alphasynapse_shape.PNG "AlphaSynapse shape"

[simplealpha]: {{ site.github.repository_url }}/raw/gh-pages/img/Simplealpha.PNG "Simple AlphaSynapse"

[simplealpha_params]: {{ site.github.repository_url }}/raw/gh-pages/img/Simplealpha_params.PNG "Simple AlphaSynapse Parameters"

[axoncell]: {{ site.github.repository_url }}/raw/gh-pages/img/Axoncell.PNG "Axon added to BS cell"

[console_soma]:{{ site.github.repository_url }}/raw/gh-pages/img/Console_soma.PNG "Console with soma"

[space1]:{{ site.github.repository_url }}/raw/gh-pages/img/space2.gif "Space plot window"

[space2]:{{ site.github.repository_url }}/raw/gh-pages/img/spaceplot.gif "Space plot trace"

[connect]:{{ site.github.repository_url }}/raw/gh-pages/img/sthAeqsec.gif "Connecting sections"

[console_init]:{{ site.github.repository_url }}/raw/gh-pages/img/Console_init.PNG "Console init"

[console_bsa]:{{ site.github.repository_url }}/raw/gh-pages/img/Console_bsa.PNG "Console with bsa output"

[hocoutput]:{{ site.github.repository_url }}/raw/gh-pages/img/hocoutput.PNG "Running our HOC code"
