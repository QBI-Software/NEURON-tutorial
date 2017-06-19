## Introduction to NEURON

"NEURON is an advanced simulation environment for realistic modeling of biological neurons and neural circuits" as quoted from the developers of NEURON.  NEURON was first developed in the early 1990's and has been increasingly used by neuroscientists world-wide with 1840 publications as of 31st December, 2016.  

Several commonly used features are provided with NEURON, some of which we will use:

1. Hodgkin-Huxley (HH) current[1]
2. Moore-Cox sodium channel
3. Ca channel
4. Ca-sensitive K channels

Additional standard mechanisms included are:

1. a Na/Ca exchange,
1. a metabolically driven Ca pump,
1. intracellular Ca binding, and
1. intracellular Ca diffusion.

## Overview of the tutorial

*The following overview assumes you have already installed NEURON on your system. If not, please go to [Setup](../setup).*

The main aim of this course is to understand how you can produce a simulated electrophysiological response with the NEURON application.  We will start with a simple model of a soma and single dendrite, commonly known as a "Ball and Stick" model.  Even with this model, the interactions between different channel types with specific characteristics, the influence of their strengths and locations along the dendrite, can be illustrated with NEURON. This is why it is known as a powerful neuronal simulator as it takes into account the conductance and activation characteristics of each channel.  Once you are familiar with how to model the electrophysiological components of a single neuron, we will then provide a second stimulating neuron and thus model a simple synaptic integration. We will then proceed to the establishment of a network model using these two neuronal types.

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

From the **Build** menu on the Main Menu window, select **CellBuilder**
![alt text][cellbuilder]

## References
[Using the CellBuilder](https://www.neuron.yale.edu/neuron/static/docs/cbtut/main.html)
[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod

[mainmenu]: {{ site.github.repository_url }}/raw/gh-pages/img/MainMenu.PNG "NEURON's main menu"
[cellbuilder]: {{ site.github.repository_url }}/raw/gh-pages/img/CellBuilder.PNG "Cell builder for morphology"
