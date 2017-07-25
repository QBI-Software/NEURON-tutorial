---
lesson: 3
layout: default
title: Complex Model
---
# A complex model

As you may have imagined, it would be very time-consuming and difficult to build an actual neuron in CellBuilder.  Fortunately, there is a way of loading data obtained from neuron tracing directly into NEURON. We will use a simplified thick-tufted layer 5 pyramidal neuron from the rat primary visual cortex which was filled with biocytin through a patch pipette and visualized with Streptavidin-Alexa 488. The filled cell was imaged using spinning disk confocal microscopy and reconstructed using [Neurolucida] software.

![pyramidalneuron]

### Preliminary setup

1. Launch a fresh version of NEURON via `nrngui`
1. If you haven't already done so, download the [datafile] to your working directory

### STEP 1: Importing the cell data

1. To import a neuron's morphology from a digital reconstruction, click
**Tools -> Miscellaneous -> Import 3D**

2. In the **Import3d** window, click on **choose a file** and select the downloaded datafile.
>*If you get multiple windows pop up, you may have a neuron that is not fully connected.*

1. In the next window, the morphology of the neuron is displayed. Unselect **show points** to improve clarity.

![importneuron1] ![importneuron2]

1. You can use **Zoom**, **Translate**, and **Rotate**, in combination with click and drag movements with the mouse in order to observe the morphology. Use this to inspect your cell to see if it is complete.

1. By unselecting **Show diam**, only the skeleton of the neuron will be shown.

1. When you have finished inspecting, select **Export** and **Cellbuilder**.

<button class="btn btn-info" data-toggle="collapse" data-target="#tip1">Guidelines for tracing neurons</button>

<div id="tip1" class="collapse alert alert-info">
<p>Here are some useful guidelines on tracing neurons in programs such as Neurolucida to ensure they are loaded correctly in NEURON</p>
<ol>
<li>All dendrites and axons from a particular neuron must start from within the soma</li>
<li>All branches must be connected to their parent branch</li>
<li>Delete any additional content, such as contours demarking layers, etc.</li>
<li>NEURON will import all 3-dimensional data and the diameter of the processes therefore it is important to reconstruct these as accurately as possible.</li>
<li>To ensure that NEURON will detect the volume of the soma correctly in 3D:
<ol>
<li>Reconstruct the soma via a series of concentric contours demarking the boundary of the cell body</li>
<li>Start at either the top or bottom (in the z-axis) of the soma</li>
<li>Trace the boundary in the first z-plane</li>
<li>Move one z plane down/up and repeat</li>
<li>You MUST move only in one direction – the order in which you trace each contour is important for how NEURON reads the soma data. If it is out of order, NEURON will incorrectly create the somatic volume.</li>
<li>You may manually move individual points to correct for errors, but you cannot delete parts and redraw them. If you have to redraw, you must delete all contours past that point.</li>
<li>Once you reach the end of the cell body. Select all the contours and right click and select "Set as cell body". Alternatively, make sure the contour is called "Cell body" or "Soma"</li>
</ol></li>
<li>If you end up with multiple import3D windows with only a soma it may be because you have not maintained the order of the contour series. Unfortunately, the quickest way to remedy this is to retrace the soma in most instances.</li>
<li>The file must be saved as an ascii file format (extension is .asc  or .ASC)</li>
</ol>
</div>


### STEP 2: Save with the Print &amp; File Window Manager

So far we have been saving our entire session using **File -> Save Session**. However, it is very useful to specify which windows you want to save.

![printfilemanager]

1. From the Main Menu, select **Window -> Print &amp; File Window Manager**
1. The display shows red and blue boxes. The left red square represents your screen, and the blue squares are individual NEURON windows.
  1. Click on those windows you want to save in your session - these will move to the right red square
  1. Identify which square is which by moving the actual window around
1. Click **Session -> Save selected** or **Session ->Save all**  

You can save this session to a file called **pyramidal.ses**.

![printfilemanager1]  ![printfilemanager2]

### STEP 3: Viewing the cell morphology

1. Select **Topology** in **CellBuilder** to display your neuron.
1. Use the *right click menu* or click on the small square in the top left corner of the canvas
1. Select **View = Plot**, **zoom in/out** or **translate** to observe your neuron.

![cellbuilder_topol]

>*Be careful not to left click as you will create new sections! If you make changes you do not want you can always close and reload your model. This is why it is vital to keep saving!*


### STEP 4: Defining a complex neuron

#### 4A: Grouping sections

1. Select **Subsets**  to group different sections into groups that may be meaningful to us.
1. Fortunately, NEURON can read different categories of processes reconstructed in **Neurolucida** and so all our sections are already in grouped in useful subsets. Click each subset to see which branches are included.

#### 4B: Morphology

1. Select the **Geometry** tab.
1. As in the previous lessons, we can set the geometrical parameters of every section or for a group of sections defined as a subset.
1. We can set the length (L), diameter (diam) and the degree of compartmentalization of the model (spatial grid). Since our morphology is already imported, including the length and diameter of each segment, all we need to define is the spatial grid.
1. Select `all` (all segments should be red) then select `d_lambda`. We will use the default value of 0.1.

![spatialgrid]


#### 4C: Biophysical properties - General

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

#### 4D: Biophysical properties - Specific

We would also like our neuron to be able to fire action potentials. Let's add some basic HH conductances into our `soma` and `axon`.

| Subset | Ref  | Biophysics |
| ---- | ---- |  ----|
| Somatic | `somatic`  | hh|
| Axonal | `axonal`  | hh |
| Basal | `basal`  | reduced hh |
| Apical | `apical`  | reduced hh |

1.  Select `somatic` and check `hh` and repeat for `axonal`, `basal` and `apical`.
1. Uncheck **Specify Strategy**
1. select `basal` then enter 10% reduced hh (`gnabar_hh` and `gkbar_hh`)
1. select `apical` then enter 10% reduced hh (`gnabar_hh` and `gkbar_hh`)


![biophys2]


### STEP 5: Creating a virtual patch clamp rig.

As in our previous lesson, we will inject a square-wave current step into the soma and measure the corresponding voltage change at the soma.

#### 5A: Adding a stimulus
1. In the **CellBuilder** window, select **Continuous Create**
1. Next, we will create a current clamp electrode (a type of **Point process**).

  1. Open **Tools > Point Processes > Managers > Point manager**
  1. By default, the IClamp point process is positioned half-way along the `soma`.  We will accept this default.
  1. Click **SelectPointProcess** and select **IClamp**.
  1. Create a step current of duration `600 ms`, amplitude of `100 pA` and a delay of `200 ms`.

![iclamp] ![menu_iclamp]  ![iclamp_params]

#### 5B: Adding output graphs

Previously, we added a graph and accepted defaults.  Now we will see how to customize what is displayed.

1. **Graph -> voltage axis** to show the *somatic membrane potential vs time*  
  1. The default voltage axis measures the voltage at the soma (0.5) which is fine
  1. If we wanted to change this, right click on the graph (or click on the square)
  1. Select **Plot what?**
  1. Find `soma` in the first panel and double-click
  1. Find `v(0.5)` in the second panel and double-click
  1. Click **Accept**
1. Multiple traces can be added in the same window for comparison
  1. From the right-click menu, under **Plot what?**
  1. From this menu, then select **Color/Brush** and pick a color to differentiate

  > If you select **Keep lines**, you can run successive traces on top of each other (useful for IV)


![vgraph]

1. **Graph -> current axis** to show the *current vs time*
  1. There is no default selected here so nothing will be displayed if it is run now
  1. Right click on the graph select **Plot what?**
  1. In the window, type `IClamp[0].i`. (This will display what your point process is doing in terms of current.)

![igraph] ![iclamp_axis]


#### 5C: Run the simulation

Now we have a whole rig:

1. Open **Tools -> RunControl**
    1. Set `Tstop` to `1000` ms. This makes our recorded period 1000 ms long.
    1. Increase the IClamp amplitude to `2nA`
    1. Hit **Init &amp; Run** in the **RunControl** window. What do you see?

![iclamp_rig]

1. If part of the trace is missing, right-click on the graph, select **View...** and **View=plot** to rescale

#### 5D: Creating a Space Plot

A space plot allows you to view the changes in conductance along the neuron.

1. To create a space plot, select **Shape plot** from the **Graph** menu in the NEURON Main Menu.
1. From this window, you can select from the plot menu with the *right mouse button*.
1. Select a **Space Plot**.
2. You can rotate the axes by selecting **3D Rotate** from the **Graph Properties menu**.
3. The middle mouse button is used to move the whole representation.

![space1]

1. To create the space plot graph, select a section of the neuron to include in the space plot by clicking the left mouse button at the beginning of the section, dragging the mouse across the section you want to plot (while holding the mouse button down), and releasing the mouse button when you have covered the sections you want to plot. (In our diagram, we have selected from top to bottom)
1. When you release the mouse button, the sections you selected will be highlighted in colour.
1. A new graph will appear representing voltage vs distance. (*By default, voltage is the variable to plot, but you can change this with the Plot What? menu item*)
1. Press the **Init &amp; Run** button in the **RunControl** window to see the space plot in action.
1. You may have to zoom in with right-click menu, then **View... -> View = plot**

![space2]



### STEP 6: Save Me

1. From the **Print &amp; File Manager** window
1. Select just the windows to save then select **session -> save selected** and
1. Save as **pyramidal_iclamprig.ses**

-------------------------------
### STEP 7: Experiments

Now it's time to do your own investigations:

1. Perform an IV : Change parameters of your current clamp and see what happens.
1. Measure voltage at different locations in the cell simultaneously: Compare how they differ.
  + (*Hint: plot what?*)
1. Inject current into different regions of the cell
  + (*Hint: right click on the shape plot of the point process and select section. Then left click on the neuron to move the blue circle.*)
1. Find the delay required to reach steady state
  + (*Hint: alter the delay parameter in Run Control and zoom into the Voltage graph*)
1. Modify values of Ra, Cm and g_pas in CellBuilder: Investigate the changes in voltage responses and voltage transfer across the neuron.
1. Investigate the origin of the action potential : Slow down the output to see what the voltage is doing in real time.
  + Try putting `50000` into the `Points plotted/ms` box in the **Run Control**.
  + Click `Init &amp; Run` and have a look at the **voltage axis** and **space plot**.

#### Impedance tools

NEURON is very powerful and has a number of extra additions that can aid your analysis of results. For example we can observe voltage decay throughout the cell using the impedance tools. Discover for yourself how these work and how they may aid your analysis.

![impedance]


---------------------
## Resources


[Neurolucida]:(http://www.mbfbioscience.com/neurolucida)

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

[space1]:{{ site.github.repository_url }}/raw/gh-pages/img/space1.PNG "Space plot window"

[space2]:{{ site.github.repository_url }}/raw/gh-pages/img/space2.PNG "Space plot trace"


[datafile]: {{ site.github.repository_url }}/raw/gh-pages/_data/pyramidal.ASC "Pyramidal neuron Datafile"
