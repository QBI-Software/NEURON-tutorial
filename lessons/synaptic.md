---
lesson: 3
layout: default
title: Synaptic Model
---
## A synaptic model

We would now like to make a more interesting model to simulate synaptic input.  In this lesson, we will take a look under the hood of NEURON at the code which runs through the simulator.  The code is written in HOC which is an interpretive version of C code developed specifically for NEURON.  

### STEP 1: Generate a cell class

From our CellBuilder model, we can generate HOC code to get started.

1. From the **CellBuilder** window, select **Management** then **Cell Type**
1. Click on **Classname** and enter `BScell`
1. Click on **Save hoc code in file** and provide a filename (and path) - here we are calling it `bscell.hoc`

Creating a cell type class, allows us to use this in network models as well as allowing more complexity to be added.  We are now going to see what has been generated.

### STEP2: Understanding the HOC code

<div class="well alert alert-info">
<p>For those new to programming, don't panic and we will step through the code. A couple of concepts to make it clearer:</p>

  <h4>Variable</h4>
  <p>Programming languages allow you to store values which can be changed during the running of a program. These are given names of your choice and then set to a value with the <code>=</code> (equals) sign.</p>

<h4>Procedure</h4>
<p>A program will run through a series of steps which can be grouped together as a <em>procedure</em> and given a name of your choice followed by parentheses <code>()</code>.  <em>A procedure is called a "method" in most other languages.</em> 
<ol>
  <li>A procedure must first be <em>defined</em> which is written as <code>proc methodname()</code> and contains the actual code to run within curly brackets <code>{}</code></li>
  <li>To get the procedure to run, it is <em>called</em> by the name and parentheses <code>methodname()</code></li>
</ol> 
</div>

----------

1. Open a plain text editor of your choice (a few recommendations are on the [Setup](setup) page)
2. Open the `bscell.hoc` file
The class is defined by the lines at the top and bottom of the file: 

```
begintemplate BSCell
...
endtemplate BSCell
```

**Global variables** are indicated by the starting word: `public`. You should recognize a few names from our CellBuilder model: `soma`, `ap`, etc.

```
public soma, ap
public all, apicals
```

When the code is first loaded in the NEURON interpreter, it runs:

```
create soma, ap[2]
```

This means it generates two variables, one called `soma` and the other is a list with 2 items called `ap` (denoted by square brackets with a 2)

When the NEURON interpreter loads this class, it will look for a method called `init()` ie, initialize

```
proc init() {
  topol()
  subsets()
  geom()
  biophys()
  geom_nseg()
  synlist = new List()
  synapses()
  x = y = z = 0 // only change via position
}
```

Each procedure listed in `init()` is run in sequential order.  So to begin, the first call is to `topol()` (ie, topology). This was where we added the soma and two dendrites in the CellBuilder.  `topol()` is defined as:

```
proc topol() { local i
  connect ap(0), soma(1)
  connect ap[1](0), ap(1)
  basic_shape()
}
```

So here we see that the items in the `ap` list (which contains our dendrites, `ap` and `ap[1]`) are connected to the soma object. `ap(0)` refers to the **start of the segment** and `ap(1)` refers to the **end of the segment**.  The method then calls `basic_shape()` which you can see shown next in the code.

As each procedure completes, the next in the list is run, so this would be followed by `subsets()` and so on.




