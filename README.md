# Delta

*Not maintained anymore. We suggest to use [ddsmt](https://ddsmt.readthedocs.io/)*.

**Delta debugging** describes a generic debugging approach based on automated testing.
Given a program and an input that provokes a certain behaviour (for example an error) delta debugging is the process of iteratively changing the input, retaining the specific behaviour.
Each small change to the input represents a **delta** and is the result of some transformation rule.
Whenever a change was successful, it is stored and the process continues from this intermediate result.
Eventually, there is no transformation left, such that the faulty behaviour is retained and the debugging process terminates.

This approach only works, if the transformation rules can neither be chained to form a loop, nor continue infinitely.
Usually, as the ultimate goal is a minimal example that triggers some bug, all transformation rules are designed to make the input smaller, in one way or another.

This approach has proven to be very valuable in the context of SAT and SMT solving. However, existing delta debugging tools (ddsmt) needed a preprocessed input and manual restarts to achieve a fix-point, hence, we decided to include our own delta debugging tool, `delta`, in SMT-RAT. It can be used completely independent of SMT-RAT and is built to be as generic as possible, but focuses on programs operating on SMT-LIB files.
It has some knowledge of the semantics of the corresponding logics, but only operates on nodes. Any SMT-LIB construct, that is either a constant or a braced expression, is a node.

The actual transformation rules are implemented in `operations.h` and are enabled in the constructor of the `Producer` class.
The implemented rules are rather simple: removing a node, replacing a node by a child node, simplifying a number, replacing a symbol by a constant or eliminating a let expression.
These transformations are designed such that they can be extended easily.
For a given input `delta` applies each transformation to each node.
Each application may produce arbitrarily many *candidate inputs* which are then tested. The first candidate that provokes the error is then adopted, the other candidates are rejected.

When analysing the behaviour, `delta` relies on the exit code of the program.
It will run the program on the original input and obtain the original exit code.
Whenever the program returns the same exit code, `delta` assumes that the program behaved the same.

## Using delta

### Building

* `sudo apt install libboost-program-options-dev`
* `mkdir build && cd build`
* `cmake ..`
* `make delta`

Then a binary `delta` is created.

### Usage

Using `delta` is rather easy.
It accepts the input file and the solver as its two main arguments: `./delta -i input.smt2 -s ./solver`.
There are a couple of other arguments that are documented in the help: `./delta --help`.

### Exit code
As delta relies on the exit code of the program, make sure that this event results in a unique exit code:

* **Specific assertions**: Return a unique exit code by replacing the assertion with `if (!assertion_condition) { std:exit(70); }`.
* **Faulty output**: Remove the `(set-info :status unsat)`/`(set-info :status sat)` line from the benchmark and use the script `utilities/result-incorrect.py` as solver. Note that you need to install z3 first.