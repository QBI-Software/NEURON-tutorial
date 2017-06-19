---
lesson: 2
layout: default
title: Simple Model
---
## A simple model

We will now create a "Ball and Stick" soma-dendrite model with the following characteristics:

| Section | Ref | Length (um) | Diameter (um) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Soma | `soma` | 20 | 20 | hh [1]|
| Apical dendrite (hillock) | `ap[0]` | 400 | 2 | reduced hh |
| Apical dendrite | `ap[1]` | 300 | 1 | reduced hh |

> Note: **reduced HH** indicates the Na+ and K+ conductances are reduced to 10% of normal and the equilibrium potential of the HH leak current is set to -64 mV.

### STEP 1: Launch CellBuilder

CellBuilder is a graphical interface used to generate the basic morphology of a neuron.  
From the **Build** menu on the Main Menu window, select **CellBuilder**
![alt text][cellbuilder]

### STEP 2: Create morphology with CellBuilder

1. Click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a soma is created automatically.
1. Click on **Basename** and type in `ap` 
![alt text][basename]
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. This generates `ap` and `ap[1]`

![alt text][canvas]

### STEP 3: Specify dimensions of components

1. Click on **Geometry**
1. `all` should be selected by default - if not, select `all` in the side panel
1. Ensure **Specify Strategy** is selected
1. Select `L`, `diam` from **Distinct values over subset**
1. Select `d_lambda` from **Spatial grid** (This parameter specifies a maximum length for each segment as a fraction of the AC length constant ensuring segments are dynamically distributed [2]). 
![alt text][strategy]

Now, we need to specify our lengths and diameters:
1. Unselect **Specify Strategy**
1. `d-lambda` is by default set to 0.1 which is suitable for most models
1. In the number box next to `soma.L (um)`, enter 20 as per our characteristics in the table above
1. Enter the remaining values in the appropriate fields so that the final result looks like:
![alt text][strategy_done]


<div class="alert alert-info">
<h4>Save Me</h4> <p>Now save this to a file called <em>bscell.ses</em> using the <b>File->Save session</b> command from the Main Menu window.</p>
</div>

--------

## References
[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)

[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod
[2]: https://www.neuron.yale.edu/neuron/static/docs/d_lambda/d_lambda.html

[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.PNG "Cell builder for morphology"

[basename]: {{ site.github.repository_url }}/raw/gh-pages/img/Basename.PNG "Enter Basename of component"

[canvas]: {{ site.github.repository_url }}/raw/gh-pages/img/Canvas.PNG "Cell builder canvas with Ball and Stick model"

[strategy]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_all.PNG "Strategy for Geometry"

[strategy_done]: {{ site.github.repository_url }}/raw/gh-pages/img/Strategy_done.PNG "Manually entered Geometry"
