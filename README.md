# Py.test plugin for iPython notebooks

This code idea is a modification of the plugin that extends the functionality 
of the `py.test` testing system for ipython notebooks 
( https://github.com/zonca/pytest-ipynb ).

It is mostly based on the template in https://gist.github.com/timo/2621679 
and the code of a testing system for notebooks https://gist.github.com/minrk/2620735
which we integrated and mixed with the `py.test` system.

## How it works
The extension looks through all the cells with code in an ipython notebook
an compare the outputs of the notebook (before being processed) with the 
outputs of the cells when they are executed. Thus, the notebook itself is
used as a testing function.

Roughly, the script initiates an iPython Kernel with a `shell` and
an `iopub` sockets. The `shell` is needed to execute the cells in
the notebook (send requests to the Kernel) and the `iopub` provides 
an interface to get the messages from the outputs. The contents
of the messages obtained from the Kernel are organised in dictionaries
with different information, such as time stamps of executions,
cell data types, cell types, the status of the Kernel, username, etc.

In general, the functionality of the ipython notebook system is 
quite complex, but a detailed explanation of the messages
and how the system works, can be found here 

http://ipython.org/ipython-doc/stable/development/messaging.html

## Execution
To execute this plugin, you need to execute `py.test` with the `ipynb` flag
to differentiate the testing from the usual python files:

    py.test --ipynb

This will execute all the `.ipynb` files in the current folder. Alternatively,
it can be executed:

    py.test --ipynb my_notebook.ipynb

for an specific notebook.


## Installation
For now, the project is called `Stollen`. After cloning this repository, the
plugin is installed doing

    sudo pip install .

from the main directory. It can be easily removed with:

    sudo pip uninstall . 

## Tests
The `py.test` system provides the base system, for the outputs in the console.
For example

    py.test --ipynb finmag_nb_test.ipynb

```
================================================ test session starts =================================================
platform linux2 -- Python 2.7.6 -- py-1.4.26 -- pytest-2.6.4
plugins: ipynb
collected 7 items 

finmag_nb_test.ipynb .....F.

====================================================== FAILURES ======================================================
_______________________________________________ cell 7: no description _______________________________________________
Notebook execution failed
Cell 7: no description

Input:
[np.random.rand() for i in range(10)]

Traceback:
mismatch text:
[0.23679291372468003,
 0.27501942679370517,
 0.6817146281770614,
 0.3082628193357294,
 0.647192050842091,
 0.6360479616745076,
 0.7507917597788405,
 0.7458041582949282,
 0.5964258144316087,
 0.9037044914313878]
  !=  
[0.9696300594926726,
 0.7287187988469214,
 0.9053184809954344,
 0.05173052644868459,
 0.9053938024346945,
 0.9316999257826623,
 0.38838202922057374,
 0.00300095736645567,
 0.057452995448194266,
 0.7619880389423896]

```

Currently, image files are not compared, but from the original script,
it can be implemented a function to take this into account
in the future.

## Sanitise
In the script there is a `sanitise` function that gets rid of strings
that are likely to fail, due to the execution dependent outputs
(such as timestamps, library versions, etc.) that some of the
code blocks produce. This function can be modified and extended to
suit the needs of iPython notebooks that use a specific Python library.

In the future, we can export this function to an external file with a
series of `regex` expressions or strings that need to be sanitised
in the notebook outputs.

