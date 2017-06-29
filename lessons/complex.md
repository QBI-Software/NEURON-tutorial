---
lesson: 3
layout: default
title: Complex Model
---
# A complex model

In this lesson, we will build a simplified thick-tufted layer 5 pyramidal neuron from the rat primary visual cortex and conduct simulations using the NEURON GUI. We will use realistic morphology obtained from a cell that has been filled with biocytin through patch pipette and visualized with Streptavidin-Alexa 488. This cell has then been imaged using spinning disk confocal microscopy and reconstructed using **Neurolucida** software.

### Preliminary setup

1. Launch a fresh version of NEURON via `nrngui`
1. Download the [datafile] to your working directory

## Step 1. Importing the cell data

1. To import a neuron's morphology from a digital reconstruction, click
**Tools -> Miscellaneous -> Import 3D**

>*If you get multiple windows pop up, you may have a neuron that is not fully connected.*

2. In the **Import3d** window, click on **choose a file** and select the downloaded datafile.

![pyramidalneuron]

1. In the next window, the morphology of the neuron is displayed. Unselect **show points** to improve clarity.

![importneuron1] ![importneuron2]

1. You can use **Zoom**, **Translate**, and **Rotate**, in combination with click and drag movements with the mouse in order to observe the morphology. Use this to inspect your cell to see if it is complete.

1. By unselecting **Show diam**, only the skeleton of the neuron will be shown.

1. When you have finished inspecting, select **Export** and **Cellbuilder**.


![cellbuilder]

**Important notes** on reconstructing neurons NEURON with neuron tracing programs such as Neurolucida:

>1. All dendrites and axons from a particular neuron must start from within the soma

>1.	All branches must be connected to their parent branch

>1.	Delete any additional content – e.g. such as contours demarking layers etc.

>1.	NEURON will import all 3-dimensional data and the diameter of the processes. Therefore it is important to reconstruct these as accurately as possible.

>1.	Reconstructing the soma is a little particular; in Neurolucida to create a 3D soma that NEURON will detect the volume of correctly you must reconstruct it in the following way:
>>*	Reconstruct the soma via a series of concentric contours demarking the boundary of the cell body
>>*	Start at either the top or bottom (in the z-axis) of the soma
>>*	Trace the boundary in the first z-plane
>>*	Move one z plane down/up and repeat
>>*	You MUST move only in one direction – the order in which you trace each contour is important for how NEURON reads the soma data. If it is out of order, NEURON will incorrectly create the somatic volume.
>>*	You may manually move individual points to correct for errors, but you cannot delete parts and redraw them. If you have to redraw, you must delete all contours past that point.
>>*	Once you reach the end of the cell body. Select all the contours and right click and select “Set as cell body”. Alternatively, make sure the contour is called “Cell body” or “Soma”

>6.	If you end up with multiple import3D windows with only a soma it may be because you have not maintained the order of the contour series. Unfortunately, the quickest way to remedy this is to retrace the soma in most instances.

>7.	The file must be saved as an .asc ascii file format


![printfilemanager]

<div class="alert alert-info">
 <h4>Save Me Using the Print &amp; File Window Manager</h4>
 <ol>
 <i>So far we have been saving our entire session. However, it is very useful to save only certain windows.</i>
 <li>From the Main Menu, select <b>Window</b> then <b>Print &amp; File Window Manager</b></li>
 <li>The display shows red and blue boxes. The left red square represents your screen, and the blue squares are individual NEURON windows.</li>
 <li>You can click on those windows you want to save in your session. These will translate to the right red square, which denotes the windows being saved into the session.</li>
 <li>Click <b>Session -> Save selected</b> OR click <b>Session ->Save all</b> to save all windows open. </li>
 <li>You can save this session to a file called <i>pyramidal.ses</i>. You must put in the suffix <i>.ses</i>.  </li>
 Delete the filter to see all files in the folder and use the ./ to refresh, .// to go back a folder.

 </ol>
</div>

![printfilemanager1]  ![printfilemanager2]

## Step 2. Viewing the cell morphology

![cellbuilder_topol]

1. Select **Topology** to display your neuron.
1. Use the *right click menu* and **View = Plot**, **zoom in/out** or **translate** to observe your neuron.
1. *Be careful not to left click as you will create new sections! If you make changes you do not want you can always close and reload your model. This is why it is vital to keep saving! Alternatively, observe your neuron in the subsets tab.*

## Step 3. Building the neuron in NEURON

#### Grouping sections

1. Select **Subsets**  to group different sections into groups that may be meaningful to us.
1. Fortunately, NEURON can read different categories of processes reconstructed in **Neurolucida** and so all our sections are already in grouped in useful subsets. Click each subset to see which branches are included.

#### Spatial grid

1. Select the **Geometry** tab.
1. As in the previous lessons, we can set the geometrical parameters of every section or for a group of sections defined as a subset.
1. We can set the length (L), diameter (diam) and the degree of compartmentalization of the model (spatial grid). Since our morphology is already imported, including the length and diameter of each segment, all we need to define is the spatial grid.

1. Select `all` (all segments should be red) then select `d_lambda`.

![spatialgrid]

1. Uncheck **Specify strategy** which allows us to set a value for `d_lambda`. Let's go with the default, `0.1`.



#### Biophysical properties

Lastly, we need to specify the biophysical properties of our neuron.

We can start with an assumption that all regions of our neuron have the same values for the following:

| Property    | Ref     | Value | Units|
| :------------- | :------------- | :------------- |
| axial resistivity       | `Ra`       | 100 | ohm-cm |
| specific membrane capacitance | `cm` | 1 | &micro;F/cm2 |
| passive conductance | `g_pas` | 1/20000 |  S/cm2 |
| passive conductance reversal potential| `e_pas` | -65 | mV |

1. Select the **Biophysics** tab.
1. Select `all` and declare that we would like to set `Ra`, `cm` and `pas` for all sections.

![biophys1]

We would also like our neuron to be able to fire action potentials. Let's add some basic HH conductances into our `soma` and `axon`.

| Subset | Ref  | Biophysics |
| ---- | ---- |  ----|
| Somatic | `somatic`  | hh|
| Axonal | `axonal`  | hh |
| Basal | `basal`  | reduced hh |
| Apical | `apical`  | reduced hh |

1.  Select `somatic` and check `hh` and repeat for `axonal`.

![biophys2]

1. Finally, check **Continuous create**. We want our model to update in real time if we change any of its properties.

<div class="alert alert-info">
<h4>Save Me</h4><p>We will modularize the model, meaning to create different aspects of the model in parts. This allows each part of the model to be individually modified more easily. Hence, select just the <b>Cellbuilder</b> window to save and <b>session -> save selected</b>. Save as <i>pyramidal_cb.ses</i>.</p>
</div>

1. As before, we can create a run file to keep it together.  In a text editor, create a new file called *model_run.hoc*.
1. It should contain the following:

```
load_file("nrngui.hoc")
load_file("pyramidal_cb.ses")

```

>Test it out! Close NEURON. And double click your *model_run.hoc* file. Make sure all your values have loaded up correctly. Its good practice to restart your model often. This will help you detect any bugs and know at what step they arose.

### Step 4. Creating a virtual patch clamp rig.

The first experiment we will conduct with our neuron is to inject a current step into the soma and measure the corresponding voltage change at the soma.

1. Open **Tools -> Run control**
1. Set `Tstop` to `1000` ms. This makes our recorded period 1000 ms long.

![runcontrol]

Next, we will create a current clamp electrode (a type of **Point process**).
1. Open **Tools > Point Processes > Managers > Point manager**
1. By default, the IClamp point process is positioned half-way along the `soma`.  We will accept this default.

![iclamp]

1. Click **SelectPointProcess** and select **IClamp**.
1. Create a step current of duration `600 ms`, amplitude of `100 pA` and a delay of `200 ms`.

![menu_iclamp]  ![iclamp_params]

Finally, lets create some output graphs:
1. From the Main Menu, select **Graph -> voltage axis** to show the *somatic membrane potential vs time*  
1. select **Graph -> current axis** to show the *current vs time*

![vgraph]  ![igraph]

Now we have a whole rig:

![iclamp_rig]

<div class="alert alert-info">
<h4>Save Me</h4><p>Save the rig (4 windows) to a new session file as <i>iclamprig.ses</i>.</p>
<p>Add a line to load the rig in your <i>model_run.hoc</i> file and restart your model.</p>
</div>

## Step 5. Conducting a virtual patch clamp experiment.

1. Hit **Init &amp; Run** in the **RunControl** window. What do you see?

We haven't yet told NEURON what we want to measure. The default voltage axis measures the voltage at the soma (0.5) (the soma is the current default section), and the current axis displays nothing.

### Voltage graph

Lets make sure the voltage is displaying what we want.
1. *Right click menu* and select **Delete**.
1. *Left click* on **v(0.5)**. This will delete the whole trace.
1. Try running your simulation.

1. Select **Plot what?** in the voltage axis
1. scroll down to `soma` and double click
1. scroll down to the `v(0.5)`. This will autofill the text section. Keep note of how this is worded.

### Current graph

1. *Right click* on the graph space of the current axis and go to **Plot what?**
1. Type `IClamp[0].i`. This will display what your point process called `IClamp[0]` is doing in terms of `current`.

![iclamp_axis]

Now when you run the simulation you should see a black soma voltage trace and current trace. Zoom into the voltage trace and note the time features of the response, particularly at the start and end of the pulse.

<div class="alert alert-info">
<h4>Save. Reload.</h4>
</div>



## Step 6: Experiment

Now it's time to do your own investigations:

1. **Perform an IV.** Change parameters of your current clamp and see what happens.
1. **Measure voltage at different locations in the cell simultaneously** and compare how they differ. (*Hint: plot what?*)
1. **Inject current into different regions of the cell** (*Hint: right click on the shape plot of the point process and select section. Then left click on the neuron to move the blue circle.*)
1. **Add multiple inputs simultaneously**
1. **Modify values of Ra, Cm and g_pas in CellBuilder** and investigate the changes in voltage responses and voltage transfer across the neuron.

#### Distance function
> How do passive dendrites (as modeled here) influence axo-somatic voltage and action potential firing as a function of distance?

To find out path distance (distance along the neuron) of a particular section, in the console, type:
```c
forall {
soma distance()
print secname(), " ", distance(1)
}
```

This will print the name of the section and the path distance in microns from the soma (0.5) to the section (1). Change values if you would like to measure distance from/to somewhere else.

> You can add this to your model_run.hoc so every time you open the model, it will display the distances to each section. Alternatively, type it out and paste it into the console which opens with NEURON.

## Extra analysis – Impedance tools

NEURON is very powerful and has a number of extra additions that can aid your analysis of results. For example we can observe voltage decay throughout the cell using the impedance tools. Discover for yourself how these work and how they may aid your analysis.

![impedance]

---------------------
## References

[datafile]: {{ site.github.repository_url }}/raw/gh-pages/data/131014_S4L1_C_reconstruct.ASC "Pyramidal neuron Datafile"

[pyramidalneuron]: {{ site.github.repository_url }}/raw/gh-pages/img/pyramidal.png "Pyramidal Neuron (Neurolucida)"

[importneuron1]: {{ site.github.repository_url }}/raw/gh-pages/img/loadedneuron1.png "Pyramidal Neuron (Neurolucida) - loaded"

[importneuron2]: {{ site.github.repository_url }}/raw/gh-pages/img/loadedneuron2.png "Pyramidal Neuron (Neurolucida) - loaded"

[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.png "CellBuilder window"

[cellbuilder_topol]: {{ site.github.repository_url }}/raw/gh-pages/img/cellbuilder_topol.png "CellBuilder window"

[printfilemanager]: {{ site.github.repository_url }}/raw/gh-pages/img/printfilemanager.png "Print File manager window"

[printfilemanager1]: {{ site.github.repository_url }}/raw/gh-pages/img/printfilemanager1.png "Print File manager window"

[printfilemanager2]: {{ site.github.repository_url }}/raw/gh-pages/img/printfilemanager2.png "Print File manager window"

[spatialgrid]: {{ site.github.repository_url }}/raw/gh-pages/img/spatialgrid.png "Geometry strategy - specify"

[biophys1]: {{ site.github.repository_url }}/raw/gh-pages/img/biophys1.png "Biophysics strategy"

[biophys2]: {{ site.github.repository_url }}/raw/gh-pages/img/biophys2.png "Biophysics strategy - set"

[menu_runcontrol]:{{ site.github.repository_url }}/raw/gh-pages/img/menu_runcontrol.png "Menu Run Control"

[runcontrol]:{{ site.github.repository_url }}/raw/gh-pages/img/RunControl.png "Run control panel"

[menu_pointprocess]:{{ site.github.repository_url }}/raw/gh-pages/img/menu_pointprocess.png "Menu PointProcess"  

[iclamp]:{{ site.github.repository_url }}/raw/gh-pages/img/iclamp.png "IClamp panel"

[iclamp_axis]:{{ site.github.repository_url }}/raw/gh-pages/img/iclamp_axis.png "IClamp axis"

[menu_iclamp]:{{ site.github.repository_url }}/raw/gh-pages/img/menu_iclamp.png "Menu Iclamp"  

[iclamp_params]:{{ site.github.repository_url }}/raw/gh-pages/img/iclamp_params.png "IClamp panel params"

[vgraph]:{{ site.github.repository_url }}/raw/gh-pages/img/vgraph.png "Graph V vs t"

[igraph]:{{ site.github.repository_url }}/raw/gh-pages/img/igraph.png "Graph I vs t"

[iclamp_rig]:{{ site.github.repository_url }}/raw/gh-pages/img/iclamp_rig.png "Virtual IClamp rig"

[impedance]:{{ site.github.repository_url }}/raw/gh-pages/img/impedance.png "Impedance tools"
