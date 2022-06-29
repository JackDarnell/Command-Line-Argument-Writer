
# CL Args Generator

## Background

Programmers everywhere hate doing the same thing more times than they need to. It simply is not fun
having to do the same thing over and over again. Repetitive tasks that take time have inspired many
systems to make us programmers more efficient. Just look at CMake or Bazel - Sure, we could spend 
time manually building our projects, but that takes time and is boring. 

This project aims to create an answer to one of the boring things that programmers have to deal with..
that is creating command line arguments. No matter what language you're using, command line arguments 
are something which need to be integrated into applications. The challenge is that every language 
has their own argument parsers and ways to use the parsers. C++ has getopt, python has Click. 
This project aims to define a singular argument definition system, where a user can describe 
the command line arguments that they would like to integrate into their project, and then
generate a command line argument parser for the specified output language. This system will
take a lot of the heavy lifting associated with command line arguments and integrate them
into a small compiler. 

## Goals 

This projects aims to provide users an application that can be installed through an installation system such as npm, 
and then immediately generate the arguments for the outputted program. This will allow users to not 
care how the system works, all they will have to care about is just how great the system is.

# System

The entire system will be built by a number of standalone components which can be called as functions and will do
some described job. 

Currently, the system will have the following architecture. 
```
    ---------------------------       --------------------------------------
    | CL Arg Description File | ----> | CLA format to JSON format Converter|
    ---------------------------       --------------------------------------
                                                        |
                                                        \/
                                   --------------------------------------
                                   |Specified Language Generation Engine| 
                                   --------------------------------------
                                                |
                                                \/
                                    ----------------------------
                                    | Outputted Arg parser code |
                                    -----------------------------

```

And the `specified language generate engine` will be a generation engine 
for whichever output language the user would like (c++, python, c, go, ...)

## CL Arg Description File 

The CL Arg Description File is the file that the user edits which
allows them to define the CL arguments and other 
meta data about each argument

For example,

```
# Sample CLAD (command line argument description) file

a, my_arg, my_arg_one:
    type: flag
    description: "The first argument described in the CLAD file. The 
                description is printed when the user requests --help.
                This CLA is a flag, and thus either on or off."

s, string_arg:
    type: string
    required
    description: "This is a required string argument. This argument will be 
                   type checked for the user, and an error will be 
                   returned if the user does not input the argument 
                   properly" 

v, verbose:
    type: flag
    description: "We can declare a verbose argument, but the user will
                  be warned that they are overriding a defaulted CLA arg"

```

## CLAD format to JSON format Converter

The CLAD file is then compiled via the conversion engine to a JSON formant which is more descriptive and easier for the compiler to work with.

```json
{

"my_arg_one":{
    "aliases":["a", "my_arg", "my_arg_one"],
    "description":"The first argument described in the CLAD file. The 
                description is printed when the user requests --help.
                This CLA is a flag, and thus either on or off.",
    "type":"flag",
    "occurs":"optional"
},

"string_arg":{
    "aliases":["s", "string_arg"]
    "type":"string",
    "occurs":"required",
    "description":"This is a required string argument. This argument will be 
                   type checked for the user, and an error will be 
                   returned if the user does not input the argument 
                   properly"
},

"verbose":{
    "aliases":["v", "verbose"],
    "type":"flag",
    "description":"We can declare a verbose argument, but the user will
                  be warned that they are overriding a defaulted CLA arg",
    "occurs":"optional"
}

}
```

## Specified Language Generation Engine

The language generation engine takes the JSON and then converts it to
whatever language the generator was written for. This can then be written
to a file, displayed in the web, etc. 
