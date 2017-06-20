---
lesson: 2
layout: default
title: Simple Model
---
## A simple model

We will now create a "Ball and Stick" soma-dendrite model with the following characteristics:

Resting potential = -65 mV throughout the cell.
Capacitance (cm) = 1 &micro;f/cm<super>2</super>
Membrane resistance (Ra) = 160 ohm cm.

| Section | Ref | Length (&micro;m) | Diameter (&micro;m) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Soma | `soma` | 20 | 20 | hh [1]|
| Apical dendrite (hillock) | `ap` | 400 | 2 | reduced hh |
| Apical dendrite | `ap[1]` | 300 | 1 | reduced hh |

> Note: **reduced HH** indicates the Na+ and K+ conductances are reduced to 10% of normal and the equilibrium potential of the HH leak current is set to -64 mV.

### STEP 1: Launch CellBuilder

CellBuilder is a graphical interface used to generate the basic morphology of a neuron.  
From the **Build** menu on the Main Menu window, select **CellBuilder**
![alt text][cellbuilder]

### STEP 2: Create morphology with CellBuilder

1. Click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a soma is created automatically.
1. Click on **Basename** and type in `ap` and **Accept**
![alt text][basename]
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. This generates `ap` and `ap[1]`

![alt text][canvas]

1. To ease the pain, we will group the dendrite components so click on **Subsets**
1. Click on **Select Subtree**
1. Click on the `ap` segment (root of the subtree)
1. Click on **New Section List**
1. In the popup window, enter `apicals` and **Accept**
1. This new list should appear in the sets.

![alt text][subsets]

<div class="alert alert-info">
<h4>Save Me</h4> <p>Now save this to a file called <em>bscell.ses</em> using the <b>File->Save session</b> command from the Main Menu window.</p>
</div>

### STEP 3: Specify dimensions of components

1. Click on **Geometry**
1. `all` should be selected by default - if not, select `all` in the side panel
1. Ensure **Specify Strategy** is ticked
1. Select `L`, `diam` from **Distinct values over subset**
1. Select `d_lambda` from **Spatial grid** (This parameter specifies a maximum length for each segment as a fraction of the AC length constant ensuring segments are dynamically distributed [2]). 
![alt text][strategy]

Now, we need to specify our lengths and diameters:
1. Unselect **Specify Strategy**
1. `d-lambda` is by default set to 0.1 which is suitable for most models
1. In the number box next to `soma.L (um)`, enter 20 as per our table above
1. Enter the remaining values in the appropriate fields so that the final result looks like:
![alt text][strategy_done]

<div class="alert alert-info">
<h4>Save Me</h4> 
</div>

### STEP 4: Specify biophysical characteristics

The next part of defining the neuron is to specify the biophysical characteristics such as membrane resistance (Ra), capacitance (cm), ion channels, buffers and pumps.

1. Click on **Biophysics**
1. Ensure **Specify Strategy** is selected.
1. **Ra** and **cm** are uniform in this particular model, so we select the `all` subset and then click on the `Ra` and `cm` checkboxes.
1. We will add **HH** to `soma` and dendrites (`apicals`) by selecting each then click on `hh`
![alt text][biophys]

Now we will add our values:
1. Now deselect **Specify Strategy**
1. Select `Ra` under `all` and enter `160`
1. The default for `cm` is 1 &micro;f/cm<super>2</super> which is fine
![alt text][biophys_all]

For the dendrites, we need to enter a reduced HH which means altering the standard HH conductances to be 10% of their initial values.
1. Select `hh` under `apicals` and enter 10% of the Na+ (`gnabar_hh`) and  K+ (`gnakbar_hh`) conductance values
1. No change to leak current of HH (`gl_hh`) 
1. For the equilibrium potential (`el_hh`), change this to `-64mV`
![alt text][biophys_reducedhh]

<div class="alert alert-info">
<h4>Save Me</h4>
</div>

### STEP 5: Running a simple simulation

We are now ready to load the specifications of our model into the NEURON simulator.  We will use the **Continuous Create** method which is very useful when initially testing a model. So let's go.

1. Click on the **Continuous Create**
1. We will need to create a stimulus which is known as a **Point Process** so from the Main Menu window, select **Tools** -> **Point Processes** -> **Managers** -> **Point Manager**
1. In the **PointProcessManager** window, click on **SelectPointProcess**, then select **IClamp**
1. We will accept the default of `soma(0.5)` which means the stimulus has been placed in the middle of the soma
1. We will insert a 0.6nA current of 1ms pulse width starting at t=5ms (allows for initialization)
![alt text][pointprocess]

1. Now from the Main Menu window, select **Tools**->**RunControl**. This is our stimulus parameter window and is where the simulation is launched.
1. We will need to see an output of the simulation, so from the Main Menu, select 


--------

## References
[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)

[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod
[2]: https://www.neuron.yale.edu/neuron/static/docs/d_lambda/d_lambda.html

[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.PNG "Cell builder for morphology"

[basename]: {{ site.github.repository_url }}/raw/gh-pages/img/Basename.PNG "Enter Basename of component"

[canvas]: {{ site.github.repository_url }}/raw/gh-pages/img/Canvas.PNG "Cell builder canvas with Ball and Stick model"

[subsets]: {{ site.github.repository_url }}/raw/gh-pages/img/Subsets.PNG "Geometry subsets"

[strategy]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_all.PNG "Strategy for Geometry"

[strategy_done]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_done.PNG "Manually entered Geometry"

[biophys]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophys.PNG "Strategy for Biophysics"

[biophys_all]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophys_all.PNG "Biophysics properties for ALL"

[biophys_reducedhh]: {{ site.github.repository_url }}/raw/gh-pages/img/Biophys_rhh.PNG "Biophysics properties for reduced HH"
