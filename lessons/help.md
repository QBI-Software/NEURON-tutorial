
## Some programming concepts for non-programmers

### Variable

Programming languages allow you to store values which can be changed during the running of a program. These are given names of your choice and then set to a value with the `=` (equals) sign.

In HOC, a variable must first be *declared*:

1. `objref` : stands for object reference and is most commonly used with a local scope
1. `global` : indicates the variable will be available to all files loaded in the interpreter. With multiple files, globals can cause unexpected problems.

### Procedure

A program will run through a series of steps which can be grouped together as a *procedure* and given a name of your choice followed by parentheses `()`.  *A procedure is called a &quot;method&quot; in most other languages.* 

1. A procedure must first be *defined* which is written as `proc methodname()` and contains the actual code to run within curly brackets `{}`
1. To run the procedure, it is *called* by the name and parentheses `methodname()`.

### Numbers and Arrays

In programming, numbers can be stored as integers or floats of varying precision but in HOC, numbers are all doubles (floats) of high precision with scientific notation available (`e` or `E`).
PI, E, FARADAY, R are built-in variables which can be used anywhere in the code.

An array is a sequential list of objects commonly used to group values of the same type. For example, a = [1,2,3,4] where a is an array of 4 objects.  To get one of the values in a, it can be referenced by its position, starting with zero. For example,

```
a = [1,2,3,4]
print a[2]
```

will output the value `3` which is the corresponding position of 2 (indexes of 0, 1, 2 and 3).

In HOC, an array is very useful in grouping components and is declared as below indicating an array of 10 items:

```
objref myarray[10]
myarray[0] = dendrite1
myarray[1] = dendrite2
```
