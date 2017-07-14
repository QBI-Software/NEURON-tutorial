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

![alt text][demo]


If you don't get a series of windows popping up, check the console window for errors and check the Troubleshooting pages for your OS on the download page.  

If it all works, exit the application interface from the window called "NEURON Main Menu" under the File menu.

## Text editor

A plain text editor is required for editing the HOC code. (Programs like Microsoft Word and Wordpad are not plain text editors and cannot be used for code.)  The following editors are recommended if you don't have a favourite already.

1. [ATOM](https://atom.io) - the only editor known with HOC syntax highlighting! (to install this, go to Packages, search for `language-hoc` and install) - Multi-platform and very cool
1. [JEdit](http://jedit.org) - extremely easy to use and Multi-platform
1. Notepad Plus (Windows) - already installed
1. nano (Linux) - convenient but not necessarily intuitive

## Running on MAC

You may get a notification of 'unidentified developer' preventing you from running NEURON on a MAC.  Try these steps to handle this:

1. Once installed, go to Finder -> Applications
1. Right-click (cmd-click) on `nrngui` and select 'Open'
1. In the popup window, click 'Open' button and this should save this for future launches
1. You can also drag `nrngui` from the Applications list to the toolbar to make it easier to find


[demo]: {{ site.github.repository_url }}/raw/gh-pages/img/NeuronDemo.PNG "NEURON Demo for testing"
[datafile]: {{ site.github.repository_url }}/raw/gh-pages/data/pyramidal.ASC "Pyramidal neuron Datafile"
