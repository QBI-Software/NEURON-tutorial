---
lesson: 2
layout: default
title: Simple Model
---
# A Simple Model

## Part A: "Ball and Stick" model

To introduce the sequence of events involved in setting up a model, we will create an extremely simple neuron consisting solely of a soma and a dendrite called a "Ball and Stick" model.
It will have the following characteristics:

+ Resting potential (`Vm`) = -65 mV throughout the cell.
+ Specific capacitance (`cm`) = 1 &micro;F/cm^2
+ Cytoplasmic resistance (`Ra`) = 160 ohm cm.
+ Dendritic membrane resistance (`Rm`) = 10000 ohm cm^2.

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Soma | `soma` | 20 | 20 | hh [1]|
| Dendrite | `dend` | 1000 | 5 | 10% reduced hh |

> Note: **hh** is an in-built component in NEURON which refers to the combined conductances of voltage-gated Na+ and K+ channels, and the passive leak conductance defined by Hodgkin and Huxley.

> **reduced hh** indicates the Na+ and K+ conductances are reduced to 10% of normal HH and the equilibrium potential of the HH leak current is set to -64 mV.


### STEP A1: Launch CellBuilder

CellBuilder is a graphical interface used to generate the basic *morphology* and *biophysics* of a neuron.  
From the **Build** menu on the Main Menu window, select **CellBuilder**

![cellbuilder]

There are tabs here to create the **topology** of the neuron, place sections of the neuron into **subsets**, determine the neuron’s **geometry** and **biophysics**, and to **manage** aspects of the cell you create.

### STEP A2: Create morphology with CellBuilder

NEURON models neuronal cells as distributed electrical cables. Each cell is subdivided into **sections** which are into subdivided into **segments**. Each **section** such as the soma, dendrites, or axon is modelled as a cylinder with set **length** and **diameter**, which either has a sealed or open end depending whether its connected to another section.

1. First, let's click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a soma is created automatically.
1. Note **Basename** is set as default to `dend` (Hint: click the grey box)
1. Create the dendrite component by clicking anywhere on the canvas
1. This generates a `dend` section

![topol]

In the topology window, the size and angle of the **sections** we create do not matter - we define these in the **geometry** tab. Sections are initially created the same, however, once we start defining rules for each type of section, then the categories we call them - axon, dend, soma etc, will matter.

### STEP A3: Specify morphological dimensions

1. Click on **Geometry**
1. `all` should be selected by default - if not, select `all` in the side panel
1. Ensure **Specify Strategy** is ticked. *(Having **select strategy** checked allows us to select which aspects of the geometry we want to alter)*
1. Select length `L`, diameter `diam` from **Distinct values over subset**
1. Select `d_lambda` from **Spatial grid** (This parameter ensures segments are dynamically distributed [2]).


![geo]

**What is Spatial Grid?**
<div class="alert alert-success">
<p>The Spatial Grid here refers to your model and is a computational issue rather than a biological one. A section is divided into one or more segments of uniform conductance properties. A finer Spatial Grid will therefore produce a more accurate result but this will also increase computational time.</p>
<p>The Spatial Grid can be defined three ways:
<ol><li>The <code>nseg</code> button sets the number of segments.
<a data-toggle="collapse" data-target="#tip1">More info...</a></li>
<li>The <code>d_X</code> button allows specification of the maximum physical length, in &micro;m, for each segment.</li>
<li>The <code>d_lambda</code> button allows specification of the maximum length for each segment, expressed as a fraction of the AC length constant at 100 Hz for a cylindrical cable with the same diameter, Ra, and cm. <b>This is often the best choice to use.</b></li>
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

We now have created a neuron with a soma that is a cylinder 20um long, 20um wide, with one dendrite protruding out 1000um long, 5um wide. Certainly lives up to the name *ball and stick*!

### STEP A4: Specify biophysical characteristics

The next part of defining the neuron is to specify the biophysical characteristics such as the electrical resistance within the cytoplasm (Ra), the capacitance of the plasma membrane (cm), membrane ion channels, buffers and pumps.

1. Click on **Biophysics**
1. Ensure **Specify Strategy** is selected.
1. **Ra** and **cm** are uniform in this particular model, so we select the `all` subset and then click on the `Ra` and `cm` checkboxes.
1. We will add **HH** to `soma` and `dend` by selecting each then click on `hh`

![biophys]

Now we will add our values:
1. Now deselect **Specify Strategy**
1. Select `Ra` under `all` and enter `160`
1. The default for `cm` is 1 &micro;F/cm^2 which is fine

![biophys_all]

NEURON specifies ion channel density by setting the maximum combined conductance in Siemens of those channels in that particular section.

The generic `hh` conductance is actually 3 conductances: the **voltage-gated Na+ channel** component, **voltage-gated K+** channel component, and the combined **passive leak** conductance.

*Note: If you have no `hh` you will need to add the passive leak conductance by selecting `pas` in specify strategy.*

For the dendrite, we need to enter a reduced HH which means altering the standard HH conductances to be 10% of their initial values.
1. Select `hh` under `dend` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`)
1. For the equilibrium potential (`el_hh`), change this to `-64mV`

![biophys_reducedhh]


<div class="alert alert-info">
 <h4>Save Me</h4> <p>You can save this to a file called <i>bs_cell.ses</i> using the <b>File->Save session</b> command from the Main Menu window.</p>
 <p><i>TIP: set your working directory via <b>File -> working dir</b> first	</i></p>
</div>


### STEP A5: Running a simple simulation

We are now ready to load the specifications of our model into the NEURON simulator.  We will use the **Continuous Create** method which is very useful when initially testing a model as it updates parameters in real time.

1. Click **Continuous Create**.
1. We will need to create a stimulus which is known as a **Point Process** so from the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess** then select **IClamp**
1. We will accept the default of `soma(0.5)` which means the stimulus has been placed in the middle of the soma
1. We will insert a `0.6nA` current of `1ms` pulse width starting at t=`5ms` (a delay allows for initialization of any conductances with dependencies) so enter the values as shown.

![pointprocess]


1. Now from the Main Menu window, select **Tools**->**RunControl**. This is our stimulus parameter window and is where the simulation is launched. <a data-toggle="collapse" data-target="#tip2">More ...</a>

<div id="tip2" class="alert alert-info collapse">
<h3>Runcontrol window parameters </h3>
<ul>The following options can be set:
<li><code>Init(mV)</code> : determines the voltage we start at (generally keep this at the resting membrane potential you will expect from your ion channels you have placed in – the further away it is from that, the longer your cell will take to reach an equilibrium at the start</li>
<li> <code>Tstop</code> : controls the duration of our experiment, and</li>
<li> <code>dt/points</code> and <code>plotted/ms</code> : controls our temporal resolution of the experiments.</li>
</ul>
</div>


1. We will accept the default values, except for the time, so enter `20` for both `t(ms)` and `Tstop(ms)`.

![runcontrol]

1. We will need to see an output of the simulation, so from the Main Menu, select **Graph** -> **Voltage axis**
1. Now in the **RunControl** window, click **Init &amp; Run**

<div class="alert alert-success">
<h3><span class="glyphicon glyphicon-check alert-success"></span>  Success!</h3>
<p>Have a look in the graph and you should see your neuron respond to the current.</p>
</div>

![ap1]

Because we are running in **Continuous Create** mode, we will make changes in **CellBuilder** and rerun the simulator to see the effect of increasing the current injection amplitude.

1. In the **PointProcess** window, change `0.6` to `1.0` in the `amp(nA)`
1. Now rerun in the **RunControl** window, by clicking **Init &amp; Run** and we get a lovely AP.

![ap2]

<div class="alert alert-info">
 <h4>Save Me</h4> <p>It would be a nuisance to have to repeat all the procedures above, so we can save the session to a file called <i>bs_iclamprig.ses</i> using the <b>File->Save session</b> command from the Main Menu window.</p>
 <p><i>TIP: set your working directory via <b>File -> recent dir</b> first.</i></p>
</div>


### STEP A6: Reloading sessions

The saved session can now be reloaded with all the parameters already set.

1. Close down the NEURON application via **File -> Quit**
1. Restart with `nrngui`
1. You may need to return to your working directory with **File -> recent dir**
1. Under **File -> Load session**, select *bs_iclamprig.ses* and Voila!


## Part B: Simple neuron model

We can now add some more features to our "Ball and Stick" model to create a "Simple neuron".

### STEP B1: Multiple dendrites

We will now replace our single dendrite with a tree of branching apical dendrites with the following characteristics:

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Apical dendrite | `ap` | 400 | 2 | reduced hh |
| Apical dendrite branch | `ap[1]` | 300 | 1 | reduced hh |
| Apical dendrite branch | `ap[2]` | 300 | 1 | reduced hh |

1. Restart NEURON and load the *bs_cell.ses* session as above
1. Ensure **Continuous Create** is not selected
1. From the **CellBuilder**, select **Topology**
1. Select **Delete Section** then click on `dend` to remove this
1. Click on **Basename** and type in `ap` and **Accept**

<!-- Lee - I don't get this error -?windows vs mac - perhaps save this session before proceeding
1. Click through all the tabs in the **CellBuilder** to update NEURON
1. Go back to **Topology** and click on **Basename** and type in `ap` and **Accept**
-->

![basename]
1. Click on **Make Section**
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. Add a third dendrite as a branch by click and hold from the joint of the first two dendrites, then drag and release.  Errors can be rectified by selecting the commands as required.
1. This generates `ap` (root), `ap[1]` and `ap[2]`

![canvas]

1. As they share common properties, we will group the dendrite components so click on **Subsets**
1. Click on **Select Subtree**
1. Click on the `ap` segment (root of the subtree)
1. Click on **New Section List**
1. In the popup window, enter `apicals` and **Accept**
1. This new list should appear in the sets.

We will need to specify the morphological characteristics of the new dendrites
1. Click on **Geometry**
1. Ensure **Specify Strategy** is unticked
1. Enter the lengths and diameters of the `ap` sections as per the table

![strategy_done]

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

<div class="alert alert-info">
 <h4>Save Me</h4> <p>You can save this to a file called <i>simple_cell.ses</i> using the <b>File->Save session</b> command from the Main Menu window.</p>
 <p><i>TIP: set your working directory via <b>File -> recent dir</b> first	</i></p>
</div>


--------
<div class="alert alert-warning">
<h4>Extra Task</h4>
<p>To check you've got the hang of it, go back to the CellBuilder and add a basal dendrite and an axon with the following characteristics:</p>
<table>		 
   <thead> 		  
   <tr><th>Section</th><th>Ref</th><th>Length (&micro;m)</th><th>Diameter (&micro;m)</th><th>Biophysics</th></tr>		 
   </thead>		
   <tbody>		
   <tr><td>Basal dendrite</td><td><code class="highlighter-rouge">bas</code></td><td>200</td><td>3</td><td>pas</td></tr>		
   <tr><td>Axon</td><td><code class="highlighter-rouge">axon</code></td><td>800</td><td>1</td><td>hh</td></tr>		
   </tbody>		
 </table>

<p>where <i>pas</i> is a passive current with a reversal potential (<code>e_pas</code>) of <code>-65mV</code>.
Now rerun the simulation.</p>
</div>

--------

## Resources
[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)

[Units in NEURON](https://www.neuron.yale.edu/neuron/static/docs/units/unitchart.html)

[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod
[2]: https://www.neuron.yale.edu/neuron/static/docs/d_lambda/d_lambda.html

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

[ap1]: {{ site.github.repository_url }}/raw/gh-pages/img/AP_bs_1.PNG "AP Output of BS Model"

[ap2]: {{ site.github.repository_url }}/raw/gh-pages/img/AP_bs_2.PNG "AP Output of BS Model"

[simpleoutput]: {{ site.github.repository_url }}/raw/gh-pages/img/SimpleOutput.PNG "Output of Simple Model"
