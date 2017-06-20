---
lesson: 2
layout: default
title: Introduction
---

## Introduction to NEURON

> NEURON is an advanced simulation environment for realistic modeling of biological neurons and neural circuits

NEURON was first developed in the early 1990's and has been increasingly used by neuroscientists world-wide with 1840 publications as of 31st December, 2016.  

The influence of different channel types with specific conductance and activation characteristics in the production or prevention of action potential generation, can be illustrated with NEURON. This is why it is known as a powerful neuronal simulator as it takes into account the characteristics of each channel type and produces an overall summation which is dependent on the channel distribution location of the detecting point as well as temperature and ion concentrations.

Several commonly used features are provided with NEURON, some of which we will use:

+ Hodgkin-Huxley (HH) current [1]
+ Moore-Cox sodium channel
+ Ca channel
+ Ca-sensitive K channels

Additional standard mechanisms included are:

+ Na/Ca exchange
+ Metabolically driven Ca pump
+ Intracellular Ca binding
+ Intracellular Ca diffusion

## Overview of the tutorial

The main aim of this course is to understand how you can produce a simulated electrophysiological response with the NEURON application.  
1. **Simple Model**: We will start with a simple model of a soma and single dendrite, commonly known as a "Ball and Stick" model and cover the basics of running a simple simulation. 
1. **Synaptic Model**: Once you are familiar with how to model the electrophysiological components of a single neuron, we will use a more complex neuron and then provide a second stimulating neuron to model a simple synaptic integration.
1. **Complex Model**:To provide a more realistic scenario, we will load actual Neurolucida data and create a model from experimental data using NEURON's own integrative language called HOC.
1. **Network Model**: We will then proceed to the establishment of a simple network model.

## Background knowledge

NEURON models electrophysiological data and a good background understanding of neuronal electrophysiology is essential for creating accurate models.  This tutorial is a practical guide on how to input data into NEURON to create models so it will not cover any of the theories behind the algorithms. The main caveat to be aware of, is the basic **segment** component used in NEURON which is a cylinder of uniform conductance based on cable theory [2].  

## Getting started

*The tutorial assumes you have already installed NEURON on your system. If not, please go to [Setup]({{ site.data.nav.docs[0].url }}).*

So let's go! 

### STEP 1: Launch the GUI version of NEURON with nrngui.

From your NEURON folder created during installation, click on **nrngui**.
*For those preferring the commandline, type the following from the NEURON directory:*
```
load_file(“nrngui.hoc”)
```
You will get a tiny window called **NEURON Main Menu**
![alt text][mainmenu]

Don't lose this window!  We will be using it to launch:

1. Cell Builder
2. Current clamp processes
2. Simulation controls
3. Save our files
4. Load our saved files


--------

## References
[What is NEURON](https://www.neuron.yale.edu/neuron/what_is_neuron).

[1]: http://www.neuron.yale.edu/hg/neuron/nrn/file/d887332b34c3/src/nrnoc/hh.mod

[2]: http://www.scholarpedia.org/article/Neuronal_cable_theory

[mainmenu]: {{ site.github.repository_url }}/raw/gh-pages/img/MainMenu.PNG "NEURON's main menu"

