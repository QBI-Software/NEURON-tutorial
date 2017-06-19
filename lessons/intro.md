
## Introduction to NEURON

> NEURON is an advanced simulation environment for realistic modeling of biological neurons and neural circuits

NEURON was first developed in the early 1990's and has been increasingly used by neuroscientists world-wide with 1840 publications as of 31st December, 2016.  

The influence of different channel types with specific conductance and activation characteristics in the production or prevention of action potential generation, can be illustrated with NEURON. This is why it is known as a powerful neuronal simulator as it takes into account the characteristics of each channel type and produces an overall summation which is dependent on the location of the detecting point as well as temperature and ion concentrations.

Several commonly used features are provided with NEURON, some of which we will use:

1. Hodgkin-Huxley (HH) current [1]
2. Moore-Cox sodium channel
3. Ca channel
4. Ca-sensitive K channels

Additional standard mechanisms included are:

1. Na/Ca exchange
1. Metabolically driven Ca pump
1. Intracellular Ca binding
1. Intracellular Ca diffusion.

## Overview of the tutorial

*The following overview assumes you have already installed NEURON on your system. If not, please go to [Setup](../setup).*

The main aim of this course is to understand how you can produce a simulated electrophysiological response with the NEURON application.  
1. We will start with a simple model of a soma and single dendrite, commonly known as a "Ball and Stick" model. 
1. Once you are familiar with how to model the electrophysiological components of a single neuron, we will then provide a second stimulating neuron and thus model a simple synaptic integration. 
1. We will then proceed to the establishment of a network model using these two neuronal types.

## Getting started

So let's go! 

### STEP 1: Launch the GUI version of NEURON with nrngui.

From your NEURON folder created during installation, click on **nrngui**.
*For those preferring the commandline, type the following from the NEURON directory:*
```
load_file(“nrngui.hoc”)
```
You will get a tiny window called **NEURON Main Menu**
![alt text][mainmenu]



### STEP 2: Create morphology with CellBuilder

We will now create a "Ball and Stick" soma-dendrite model with the following characteristics:

| Section | Ref | Length (um) | Diameter (um) | Biophysics |
| ---- | ---- | ---- | ---- | ----|
| Soma | `soma` | 20 | 20 | hh [1]|
| Apical dendrite (hillock) | `ap[0]` | 400 | 2 | <a href="#" data-toggle="tooltip" title="gnabar_hh and gkbar_hh are reduced to 10% of normal and el_hh (equilibrium potential of the hh leak current) is changed to -64 mV.">reduced hh</a> |
| Apical dendrite | `ap[1]` | 300 | 1 | <a href="#" data-toggle="tooltip" title="gnabar_hh and gkbar_hh are reduced to 10% of normal and el_hh (equilibrium potential of the hh leak current) is changed to -64 mV.">reduced hh</a>|


From the **Build** menu on the Main Menu window, select **CellBuilder**
![alt text][cellbuilder]

1. Click on **Topology**
1. The components will be displayed graphically in the white *canvas* region - a soma is created automatically.
1. Click on **Basename** and type in `ap` ![alt text][basename]
1. Create two apical dendrite components by clicking anywhere on the canvas (twice!)
1. This generates `ap` and `ap[1]`

![alt text][canvas]

<div class="alert alert-success">
  <strong>Save</strong> Now save this to file called *bscell.ses* using the **File->Save session** command from the Main Menu window
</div>

### STEP 3: Add conductances



## References
[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)

[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod


[mainmenu]: {{ site.github.repository_url }}/raw/gh-pages/img/MainMenu.PNG "NEURON's main menu"

[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.PNG "Cell builder for morphology"

[basename]: {{ site.github.repository_url }}/raw/gh-pages/img/Basename.PNG "Enter Basename of component"

[canvas]: {{ site.github.repository_url }}/raw/gh-pages/img/Canvas.PNG "Cell builder canvas with Ball and Stick model"
