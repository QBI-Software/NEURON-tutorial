---
layout: default
title: Quick setup
---

# Quick Setup Guide for NEURON

## Download

NEURON can be downloaded for Windows, MAC and Linux from: [http://www.neuron.yale.edu/neuron/download](http://www.neuron.yale.edu/neuron/download)

We will be using an actual pyramidal neuron traced in Neurolucida, courtesy of Lee Fletcher and the Williams Lab.  

1. Download the [datafile] to your working directory

## Test

To test your installation, click on (WIN) **NEURON demo** OR (MAC) **neurondemo**.  

![demo]

If you don't get a series of windows popping up, check the console window for errors and check the Troubleshooting pages for your OS on the download page.  

If it all works, exit the application interface from the window called "NEURON Main Menu" under the File menu.

## Changing default temperature

NEURON sets the default temperature to `6.3 celsius` in line with the squid axon experiments by Hodgkin and Huxley.  For our purposes, this should be changed to match physiological or lab conditions.

1. From the main menu, select **Tools -> Distributed mechanisms -> celsius**
1. Enter required value (eg `37`)
1. This will be saved when the session is saved otherwise it will need to manually done each time.


## Text editor

A plain text editor is required for editing the HOC code. (Programs like Microsoft Word and Wordpad are not plain text editors and cannot be used for code.)  The following editors are recommended if you don't have a favourite already.

1. [ATOM](https://atom.io) - the only editor known with HOC syntax highlighting!  - Multi-platform and very cool
  1. to install this, go to Packages, search for `language-hoc` and install or download from https://atom.io/packages/language-hoc
1. [JEdit](http://jedit.org) - extremely easy to use and Multi-platform
1. Notepad Plus (Windows) - already installed
1. nano (Linux) - convenient but not necessarily intuitive

## Running on MAC

You may get a notification of 'unidentified developer' preventing you from running NEURON on a MAC.  Try these steps to handle this:

1. Once installed, go to Finder -> Applications
1. Right-click (cmd-click) on `nrngui` and select 'Open'
1. In the popup window, click 'Open' button and this should save this for future launches
1. You can also drag `nrngui` from the Applications list to the toolbar to make it easier to find


## Running from commandline

As we make changes to the files, we will need to reload them cleanly in NEURON.  
1. In can be time consuming to relaunch the `nrngui` every time and change into your working directory via **File -> recent dir**.  
1. An alternative is to run the `bash` command downloaded with NEURON, change to your working directory and type `nrngui` then Enter.
1. HOC and session files can be launched directly as shown.
1. To quit the `oc>` type `quit()` and use key 'UP' to rerun the `nrngui` command

```
cd /d/Projects/Tutorial/Tutorial
nrngui "bs_run.hoc"

```

![bash]




[bash]: {{site.github.repository_url}}/raw/gh-pages/img/bash.PNG "NEURON bash console"

[datafile]: {{site.github.repository_url}}/raw/gh-pages/_data/pyramidal.ASC "Pyramidal neuron Datafile"
