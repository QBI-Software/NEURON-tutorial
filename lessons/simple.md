---
lesson: 2
layout: default
title: Simple Model
---
# A Simple Model

We start with the most basic model possible so we can focus on introducing you to the core components of the NEURON application and navigating the graphical user interface (GUI).

## Part A: "Ball and Stick" model

To introduce the sequence of events involved in setting up a model, we will create an extremely simple neuron consisting solely of a soma and a dendrite called a "Ball and Stick" model.

### STEP A1: Launch CellBuilder

CellBuilder is a graphical interface used to generate the basic *morphology* and *biophysics* of a neuron.  
From the **Build** menu on the Main Menu window, select **CellBuilder** and read the descriptions provided.

![cellbuilder]


### STEP A2: Create morphology with CellBuilder

First we will specify the two components of our "ball and stick" model: a soma and a dendrite.

1. First, let's click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a `soma` is created automatically
1. Create the dendrite component by clicking anywhere on the canvas **once**
1. This generates a `dend` section as specified by **Basename** which is set by default to `dend` (Hint: click the button to change)

![topol]


### STEP A3: Specify morphological dimensions

The dimensions of the components we created can now be defined in the **Geometry** window.

>NEURON refers to each component as a **section**.  Each section is divided into one or more **segments**.  A **segment** is the core fundamental unit consisting of a cylinder of uniform electrotonic characteristics based on  cable theory.

The `soma` and `dendrite` are each cylindrical sections of uniform dimensions.  We can now specify exactly what dimensions we would like them to have.


| Section | Ref | Length (&micro;m) | Diameter (&micro;m) |
| ---- | ---- | ---- | ---- |
| Soma | `soma` | 20 | 20 |
| Dendrite | `dend` | 1000 | 5 |


1. Click on **Geometry**
1. `all` should be selected by default - if not, select `all` in the side panel
1. Ensure **Specify Strategy** is ticked.
1. Select length `L`, diameter `diam` from **Distinct values over subset**
1. Select `d_lambda` from **Spatial grid** (This parameter ensures segments are dynamically distributed - see ref [1]).


![geo]

**What is Spatial Grid?**
<div class="alert alert-success">
<p>The Spatial Grid here refers to your model and is a computational issue rather than a biological one. A section is divided into one or more segments of uniform conductance properties. A finer Spatial Grid will therefore produce a more accurate result but this will also increase computational time.</p>
<p>The Spatial Grid can be defined three ways:
<ol><li>The <code>nseg</code> button sets the number of segments.
<a data-toggle="collapse" data-target="#tip1">More info...</a></li>
<li>The <code>d_X</code> parameter specifies the spacing of segments by <strong>physical</strong> length, in &micro;m</li>
<li>The <code>d_lambda</code> parameter specifies the spacing of segments by <strong>electrotonic</strong> length, expressed as a fraction of the AC length constant at 100 Hz for a cylindrical cable with the same diameter, Ra, and cm. <strong>This is often the best choice to use.</strong></li>
</ol></p>


<div id="tip1" class="collapse">
<p>It is best practice to make the number of segments equal an odd number with <code>nseg</code>. Why? This will ensure there is always a segment at the centre of the section (0.5).</p>
<table class="table">
<tr><th><code>nseg</code></th><th>section centre</th></tr>
<tr><td>1</td><td><b>0.5</b></td></tr>
<tr><td><i>2</i></td><td><i>0.33, 0.66</i></td></tr>
<tr><td>3</td><td>0.25, <b>0.5</b>, 0.75</td></tr>
</table>
<p>If your code calls for a segment that doesn't exist, then NEURON will round to the nearest segment - this may impact the accuracy of your results.</p>
</div>
</div>



Now, we need to specify our lengths and diameters:
1. Unselect **Specify Strategy**
1. `d-lambda` is by default set to 0.1 which is suitable for most models
1. In the number box next to `soma.L (um)`, enter `20` as per our table above
1. In the number box next to `dend.L (um)`, enter `1000` as per our table above
1. In the number box next to `soma.diam (um)`, enter `20` as per our table above
1. In the number box next to `dend.diam (um)`, enter `5` as per our table above
1. so that the final result looks like:

![geo_done]

<div class="alert alert-info">
<h3>Ball and Stick</h3>
<p>We have now created a neuron with a soma that is a cylinder 20&micro;m long, 20&micro;m wide, with one dendrite protruding out 1000&micro;m long, 5&micro;m wide. Certainly lives up to the name "Ball and Stick"! </p>
</div>


### STEP A4: Specify biophysical characteristics

The next part of defining the neuron is to specify the biophysical characteristics such as the electrical resistance within the cytoplasm (`Ra`), the capacitance of the plasma membrane (`cm`), membrane ion channels, buffers and pumps. NEURON specifies **ion channel density** by setting the *maximum combined conductance* (in Siemens) of those channels in that particular segment.

1. Click on **Biophysics**
1. Ensure **Specify Strategy** is selected.
1. `Ra` and `cm` are uniform in this particular model, so we select the `all` subset and then click on the `Ra` and `cm` checkboxes.
1. We will add a Hodgkin-Huxley (`hh`) conductance to `soma` and `dend` by selecting each then click on `hh`

![biophys]

Now we will add our values:
1. Now deselect **Specify Strategy**
1. Select `Ra` under `all` and enter `160`
1. The default for `cm` is 1 &micro;F/cm^2 which is fine

![biophys_all]

#### Adding HH

<a data-toggle="collapse" data-target="#tip2">More ...</a>
<div id="tip2" class="alert alert-success collapse">
<p>The generic <code>hh</code> conductance is actually composed of three conductances:
<ul><li><strong>voltage-gated Na+</strong> channel component</li>
<li><strong>voltage-gated K+</strong> channel component</li>
<li>the combined <strong>passive leak</strong> conductance</li>
</ul>
</p>
<p><i>Note: If you have no <code>hh</code> you will need to add the passive leak conductance by selecting <code>pas</code> in specify strategy.</i></p>
</div>

We will now insert HH into the soma and dendrite.

For the dendrite, we will enter a `reduced hh` which means altering the standard HH conductances to be 10% of their initial values.
1. Select `hh` under `dend` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`)
1. For the equilibrium potential (`el_hh`), change this to `-64mV`

![biophys_reducedhh]


#### Save Me

You can save this to a file called **bs_cell.ses**.

![savesession]

1. Set your working directory using the **File -> working dir** command from the Main Menu window
1. Enter the file path in the window or double-click on folders as they appear.  The `../` will return you to the parent directory.
1. Select **Move To** which sets you up in the correct directory.
1. Under the **File -> Save session** command, enter the name of the file in the top window (the bottom window is used as a filter only)


### STEP A5: Running a simple simulation

We are now ready to load the specifications of our model into the NEURON simulator.  We will use the **Continuous Create** method which is very useful when initially testing a model as it updates parameters in real time.

1. Click **Continuous Create** in the **CellBuilder** window
1. We will need to create a stimulus which is known as a **Point Process** so from the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess** then select **IClamp**
1. We will accept the default of `soma(0.5)` which means the stimulus has been placed in the middle of the soma
1. We will insert a `0.6nA` current of `1ms` pulse width starting at del=`5ms` (a delay allows for initialization of any conductances with dependencies) so enter the values as shown.

![pointprocess]


1. Now from the Main Menu window, select **Tools**->**RunControl**. This is our stimulus parameter window and is where the simulation is launched. <a data-toggle="collapse" data-target="#tip3">More ...</a>

<div id="tip3" class="alert alert-info collapse">
<h3>Run control window parameters </h3>
<ul>The following options can be set:
<li><code>Init(mV)</code> : determines the voltage we start at (generally keep this at the resting membrane potential you will expect from your ion channels you have placed in – the further away it is from that, the longer your cell will take to reach an equilibrium at the start</li>
<li> <code>Tstop</code> : controls the duration of our experiment, and</li>
<li> <code>dt</code> and <code>Points plotted/ms</code> : controls our temporal resolution of the experiments.</li>
</ul>
</div>


![runcontrol]

1. We will accept the default values, except for the time, so enter `20` for `Tstop(ms)`.
1. To see an output of the simulation, from the Main Menu, select **Graph** -> **Voltage axis**
1. Now in the **RunControl** window, click **Init &amp; Run**

![ap1]

>Have a look in the graph and you should see your neuron respond to the current.


Now we will rerun the simulator to see the effect of increasing the current injection amplitude.

1. In the **PointProcess** window, change `0.6` to `1.0` in the `amp(nA)`
1. Now rerun in the **RunControl** window, by clicking **Init &amp; Run** and we get a lovely AP.

![ap2]

### STEP A6 Save Me

You can save this to a file called **bs_iclamprig.ses**. (Saving to a new file separates our simulation from our cell.)



## Part B: Simple neuron model

We have covered the basics of creating a simple model and simulation.  We can now turn our "Ball and Stick" model into a "Simple neuron" by replacing our single dendrite with a tree of branching apical dendrites with the following characteristics:

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Apical dendrite | `ap` | 400 | 2 | reduced hh |
| Apical dendrite branch | `ap[1]` | 300 | 1 | reduced hh |
| Apical dendrite branch | `ap[2]` | 300 | 1 | reduced hh |

### STEP B1: Reloading sessions

A saved session can now be reloaded with all the parameters and windows already set.

1. Close down the NEURON application via **File -> Quit**
1. Restart with `nrngui`
1. You may need to return to your working directory with **File -> recent dir**
1. Under **File -> Load session**, select the *bs_cell.ses* session created above

### STEP B2: Modifying neurons

We will now modify the topology of the "ball and stick" neuron.

![simpleneuron]

1. From the **CellBuilder**, select **Topology**
1. Select **Delete Section** then click on `dend` to remove this

![basename]

1. Click on **Basename** and type in `ap` and **Accept**
1. Click on **Make Section**
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. Add a third dendrite as a branch by click and hold from the joint of the first two dendrites, then drag and release.  Errors can be rectified by selecting the commands as required.
1. This generates `ap` (root), `ap[1]` and `ap[2]`

![canvas]

### STEP B3: Creating subsets

1. As they share common properties, we will group the dendrite components so click on **Subsets**
1. Click on **Select Subtree**
1. Click on the `ap` segment (root of the subtree)
1. Click on **New Section List**
1. In the popup window, enter `apicals` and **Accept**
1. This new list should appear in the sets.

![subsets]

### STEP B4: Modifying parameters - Geometry

We will need to specify the morphological characteristics of the new dendrites
1. Click on **Geometry**
1. Ensure **Specify Strategy** is unticked
1. Enter the lengths and diameters of the `ap` sections as per the table

![strategy_done]

### STEP B5: Modifying parameters - Biophysics

For all the dendrites, we need to enter a reduced HH which means altering the standard HH conductances to be 10% of their initial values.
1. Click on **Biophysics**
1. Ensure **Specify Strategy** is ticked
1. Select `apicals` then tick `hh`

![biophys2]

1. Now untick **Specify Strategy**
1. Select `hh` under `apicals` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`)
1. For the equilibrium potential (`el_hh`), change this to `-64mV`

![biophys_reducedhh2]

### STEP B6 Save Me

You can save this to a file called **simple_cell.ses**.

--------
### B. EXPERIMENTS

Now it's time for you to complete the simple neuron.  To check you've got the hang of it, go back to the CellBuilder and add a basal dendrite and an axon with the following characteristics:

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Basal dendrite | `bas` | 200 | 3 | pas (`e_pas` =-65mV) |
| Axon | `axon` | 800 | 1 | `hh` |

*Don't forget to save this to simple_cell.ses - we will be using it later.*

Check it looks like <a data-toggle="collapse" data-target="#tip4">THIS</a>

<div id="tip4" class="collapse alert alert-success">

![simpleneuron2]

</div>
--------

## Resources

[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)

[Units in NEURON](https://www.neuron.yale.edu/neuron/static/docs/units/unitchart.html)

[2]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod
[1]: https://www.neuron.yale.edu/neuron/static/docs/d_lambda/d_lambda.html

[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.PNG "Cell builder for morphology"

[basename]: {{ site.github.repository_url }}/raw/gh-pages/img/Basename.PNG "Enter Basename of component"

[topol]: {{ site.github.repository_url }}/raw/gh-pages/img/Topology_bs.PNG "Cell builder canvas with Ball and Stick model"

[geo]: {{ site.github.repository_url }}/raw/gh-pages/img/Geometry_bs.PNG "Strategy for Geometry"

[geo_done]: {{ site.github.repository_url }}/raw/gh-pages/img/Geometry_bs_done.PNG "Specify Geometry"

[canvas]: {{ site.github.repository_url }}/raw/gh-pages/img/Canvas.PNG "Cell builder canvas with Simple neuron model"

[subsets]: {{ site.github.repository_url }}/raw/gh-pages/img/Subsets.PNG "Geometry subsets"

[strategy]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_all.PNG "Strategy for Geometry"

[strategy_done]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_done.PNG "Manually entered Geometry"

[biophys]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophysics_bs.PNG "Strategy for Biophysics"

[biophys2]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophysics.PNG "Strategy for Biophysics"

[biophys_all]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophysics_bs_all.PNG "Biophysics properties for ALL"

[biophys_reducedhh]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophysics_bs_rhh.PNG "Biophysics properties for reduced HH"

[biophys_reducedhh2]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophysics_rhh.PNG "Biophysics properties for reduced HH"

[pointprocess]: {{ site.github.repository_url }}/raw/gh-pages/img/PointProcess_bs.PNG "iClamp point process"

[runcontrol]: {{ site.github.repository_url }}/raw/gh-pages/img/RunControl.PNG "Run control window"

[savesession]: {{ site.github.repository_url }}/raw/gh-pages/img/SaveSession.PNG "Set working directory and save session"

[ap1]: {{ site.github.repository_url }}/raw/gh-pages/img/AP_bs_1.PNG "AP Output of BS Model"

[ap2]: {{ site.github.repository_url }}/raw/gh-pages/img/AP_bs_2.PNG "AP Output of BS Model"

[simpleoutput]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleOutput.PNG "Output of Simple Model"

[simpleneuron]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleNeuron.PNG "Modifying cell topology"

[simpleneuron2]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleNeuron_complete.PNG "Simple neuron"

[subsets]: {{ site.github.repository_url }}/raw/gh-pages/img/Subsets.PNG "Creating subsets"
