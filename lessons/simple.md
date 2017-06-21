---
lesson: 2
layout: default
title: Simple Model
---
# A Simple Model

## Part A: "Ball and Stick" model

We will now create a "Ball and Stick" soma-dendrite model with the following characteristics:

+ Resting potential (`Vm`) = -65 mV throughout the cell.
+ Specific capacitance (`cm`) = 1 &micro;F/cm^2
+ Cytoplasmic resistance (`Ra`) = 160 ohm cm.
+ Dendritic membrane resistance (`Rm`) = 10000 ohm cm^2.

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Soma | `soma` | 20 | 20 | hh [1]|
| Dendrite | `dend` | 1000 | 5 | reduced hh |

> Note: **reduced hh** indicates the Na+ and K+ conductances are reduced to 10% of normal HH and the equilibrium potential of the HH leak current is set to -64 mV.


### STEP A1: Launch CellBuilder

CellBuilder is a graphical interface used to generate the basic morphology of a neuron.  
From the **Build** menu on the Main Menu window, select **CellBuilder**

![alt text][cellbuilder]

### STEP A2: Create morphology with CellBuilder

1. Click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a soma is created automatically.
1. Note **Basename** is set as default to `dend`
1. Create the dendrite component by clicking anywhere on the canvas
1. This generates a `dend` section

![alt text][topol]


### STEP A3: Specify morphological dimensions

1. Click on **Geometry**
1. `all` should be selected by default - if not, select `all` in the side panel
1. Ensure **Specify Strategy** is ticked
1. Select `L`, `diam` from **Distinct values over subset**
1. Select `d_lambda` from **Spatial grid** (This parameter ensures segments are dynamically distributed [2]).

![alt text][geo]

Now, we need to specify our lengths and diameters:
1. Unselect **Specify Strategy**
1. `d-lambda` is by default set to 0.1 which is suitable for most models
1. In the number box next to `soma.L (um)`, enter `20` as per our table above
1. In the number box next to `dend.L (um)`, enter `1000` as per our table above
1. In the number box next to `soma.diam (um)`, enter `20` as per our table above
1. In the number box next to `dend.diam (um)`, enter `5` as per our table above
1. so that the final result looks like:

![alt text][geo_done]


### STEP A4: Specify biophysical characteristics

The next part of defining the neuron is to specify the biophysical characteristics such as resistance (Ra), capacitance (cm), ion channels, buffers and pumps.

1. Click on **Biophysics**
1. Ensure **Specify Strategy** is selected.
1. **Ra** and **cm** are uniform in this particular model, so we select the `all` subset and then click on the `Ra` and `cm` checkboxes.
1. We will add **HH** to `soma` and `dend` by selecting each then click on `hh`

![alt text][biophys]

Now we will add our values:
1. Now deselect **Specify Strategy**
1. Select `Ra` under `all` and enter `160`
1. The default for `cm` is 1 &micro;F/cm^2 which is fine

![alt text][biophys_all]

For the dendrite, we need to enter a reduced HH which means altering the standard HH conductances to be 10% of their initial values.
1. Select `hh` under `dend` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`)
1. For the equilibrium potential (`el_hh`), change this to `-64mV`

![alt text][biophys_reducedhh]

<aside class="notice">
**Save Me**
You can save this to a file called *bs_cell.ses* using the **File -> Save session** command from the Main Menu window.
*TIP:* set your working directory via **File -> working dir** first
</aside>

### STEP A5: Running a simple simulation

We are now ready to load the specifications of our model into the NEURON simulator.  We will use the **Continuous Create** method which is very useful when initially testing a model. So let's go.

1. Click on the **Continuous Create**
1. We will need to create a stimulus which is known as a **Point Process** so from the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess**, then select **IClamp**
1. We will accept the default of `soma(0.5)` which means the stimulus has been placed in the middle of the soma
1. We will insert a `0.6nA` current of `1ms` pulse width starting at t=`5ms` (allows for initialization) so enter the values as shown.

![alt text][pointprocess]

1. Now from the Main Menu window, select **Tools**->**RunControl**. This is our stimulus parameter window and is where the simulation is launched.
1. We will accept the default values, except for the time, so enter `20` for both `t(ms)` and `Tstop(ms)`.

![alt text][runcontrol]

1. We will need to see an output of the simulation, so from the Main Menu, select **Graph** -> **Voltage axis**
1. Now in the **RunControl** window, click **Init &amp; Run**

<aside class="warning">
Voila! Have a look in the graph and you should see your AP!
</aside>

![alt text][ap1]

Because we are running in **Continuous Create** mode, you can make changes in **CellBuilder** and rerun the simulator to see the effect. This is a bit of a miserable AP so let's increase the current injection amplitude.

1. In the **PointProcess** window, change `0.6` to `1.0` in the `amp(nA)`
1. Now rerun in the **RunControl** window, by clicking **Init &amp; Run** and we get a lovely AP.

![alt text][ap2]

<aside class="notice">
**Save Me**
You can save this to a **NEW** file called *bs_iclamprig.ses* using the **File -> Save session** command from the Main Menu window.
</aside>

### STEP A6: NEURON sessions

As the session was saved, it can be reloaded with all the parameters already set.

1. Close down the NEURON application via **File -> Quit**
1. Restart with `nrngui`
1. Under **File -> Load session**, select *bs_iclamprig.ses* and Voila!


## Part B: Simple neuron model

We can now add some more features to our Ball and Stick model to create a Simple neuron.

### STEP B1: Multiple dendrites

We will now replace our single dendrite with a tree of branching apical dendrites with the following characteristics:

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Apical dendrite | `ap` | 400 | 2 | reduced hh |
| Apical dendrite branch | `ap[1]` | 300 | 1 | reduced hh |
| Apical dendrite branch | `ap[2]` | 300 | 1 | reduced hh |

1. Restart NEURON and load the *bs_cell.ses* session
1. From the **CellBuilder**, select **Topology**
1. Select **Delete Section** then click on `dend` to remove this
1. Click on **Basename** and type in `ap` and **Accept**
![alt text][basename]
1. Click on **Make Section**
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. Add a third dendrite as a branch by click and hold from the joint of the first two dendrites, then drag and release.  Errors can be rectified by selecting the commands as required.
1. This generates `ap` (root), `ap[1]` and `ap[2]`

![alt text][canvas]

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

![alt text][strategy_done]

For all the dendrites, we need to enter a reduced HH which means altering the standard HH conductances to be 10% of their initial values.
1. Click on **Biophysics**
1. Ensure **Specify Strategy** is ticked
1. Select `apicals` then tick `hh`

![alt text][biophys2]

1. Now untick **Specify Strategy**
1. Select `hh` under `apicals` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`)
1. For the equilibrium potential (`el_hh`), change this to `-64mV`

![alt text][biophys_reducedhh2]

<aside class="notice">
**Save Me**
You can save this to a file called *simple_cell.ses* using the **File -> Save session** command from the Main Menu window.
</aside>

--------
<aside class="warning">
**"Too Easy" Task**
To check you've got the hang of it, go back to the CellBuilder and add a basal dendrite and an axon with the following characteristics:

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Basal dendrite | `bas` | 200 | 3 | pas|
| Axon | `axon` | 800 | 1 | hh |

where *pas* is a passive current with a reversal potential (`e_pas`) of `-65mV`.
Now rerun the simulation
</aside>

--------

## References
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
