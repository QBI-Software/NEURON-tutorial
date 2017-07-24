---
layout: default
title: Help
---
## Some programming concepts for non-programmers

NEURON uses a programming language called HOC which stands for Higher Order Calculator.  It is not a typical programming language.

### Variable

All programming languages allow you to store values which can be changed during the running of a program. These are called **variables** and given names of your choice.

In HOC, a variable must first be *declared* which means just writing the name without giving it a value:

1. `global` : indicates the variable will be available to all files loaded in the interpreter. With multiple files, globals can cause unexpected problems so use only when necessary and give them unique names.
1. `objref` : stands for object reference and is most commonly used with a local scope
1. `objvar` : stands for object variable and is most commonly used with a local scope

PI, E, FARADAY, R are built-in variables which can be used anywhere in the code.

### Procedure

A program will run through a series of steps which can be grouped together as a *procedure* and given a name of your choice followed by parentheses `()`.  

*A procedure is called a &quot;method&quot; in most other languages.*

1. A procedure must first be *defined* which is written as `proc methodname()` and contains the actual code to run within curly brackets `{}`
1. To run the procedure, it is *called* by the name and parentheses `methodname()`.

### Numbers, Arrays and Lists

Numbers can be stored as integers or decimals of varying precision but in HOC, numbers are all doubles (floats) of high precision with scientific notation available (`e` or `E`).

An **array** is a sequential list of objects commonly used to group values of the same type. For example, a = [1,2,3,4] where a is an array of 4 objects.  To get one of the values in a, it can be referenced by its position, starting with zero. For example,

```c
a = [1,2,3,4]   //list of 4 numbers
print a[2]      //command to print value at position 2 (starts at 0)
```

will output the value `3` which is the corresponding position of 2 (indexes of 0, 1, 2 and 3).

In HOC, an array must indicate its size when it is first declared and then have values assigned by position:

```c
objref myarray[10]       //a list of 10 items
myarray[0] = dendrite1   //add item to position 0 (first)
myarray[1] = dendrite2   //add item to position 1
```

A **List** is also a list of objects but it does not need to say what size it will be at the start (sometimes you don't know in advance). It is declared by the format:

```
mylist = new List()
```

### For loops

A **for loop** is a programming construct which repeats an action a set amount of times.
It generally takes the format, where `i` is a variable like a counter which increments from start to end and can also be used as an index of an array:
```
for (i=start; i < end; i++){
  print a[i]  //repeats this command
}
```

In HOC, the format is slightly different:
```c
for i=start, end command{   //command is optional
  print a[i]
}
```

HOC also provides a short hand for looping through sectionLists:
```c
forsec {}
forall {}
```
