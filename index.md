## Welcome to the QBI NEURON tutorial

NEURON is a powerful simulation environment, allowing the detailed simulation of the nervous system in a wide range of scales - from channels to individual neurons to networks of neurons. In addition to the thousands of studies that have used NEURON, it is also the simulation environment used for large-scale projects such as the Blue Brain/Human Brain Projects. 

We are offering an introductory workshop based on the recommended tutorials as well as actual experience used in generating models for research publications.  This is a one day workshop running through the basics of the NEURON interface then working up to loading experimental data and running simulations. 

### Course syllabus

| Time | Lesson | Duration |
| ------------- | ------------- | ------------- |
| 9:00-9:30 | [Intro and overview](lessons/intro) (info from Powerpoint and website)with step-by-step setup | 30 mins|
| 9:30-10:30 | UK tutorial (focussing on navigating the interface, compiling hoc files, running a basic model) - PARTS A,B,C | 60 mins|
| 10:30-10:45 | Morning tea | 15 mins |
| 10:45-12:00 | UK tutorial - PARTS D,E | 75 mins | 
| 12:00-13:00 | Lunch | 60 mins | 
| 13:00-14:30 | ACAN tutorial showing how it might actually be used with a real Neurolucida model. | 90 mins | 
| 14:30-14:45 | Afternoon tea | 15 mins | 
| 14:45-16:30 | Final session to cover using a ModelDB model, Simple networks | 135 mins | 


### Installation

NEURON software is freely available from [https://www.neuron.yale.edu/neuron/](https://www.neuron.yale.edu/neuron/).
Prior to the tutorial, please download and install NEURON on your system.

### Code
```python
proc init() { local dtsav, temp
  finitialize( v_init)
  t = -1e10
  dtsav = dt
  dt = 1e9
  // if cvode is on, turn it off to do large fixed step
  temp = cvode.active()
  if (temp!=0) { cvode.active(0) }
  while (t<-1e9) { fadvance() }
  // restore cvode if necessary
  if (temp!=0) { cvode.active(1) }
  dt = dtsav
  t = 0
  if (cvode.active()) {
    cvode.re_init()
  } else {
    fcurrent()
  }
  frecord_init()
}
