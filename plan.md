# Implementation plan for speeding up CPython

## Overview

The overall aim is to speed up CPython by a factor of (approximately) five.
We aim to do this in four distinct stages, each stage increasing the speed of CPython by (approximately) 50%.

1.5**4 ≈ 5

Each stage will be targetted at a separate release of CPython.
A faster schedule is possible, but we believe that predictable and reliable performance improvements
are more important than squeezing out the maximum performance for each release.
Of course delays in software development are all too common,
so a release might need to be skipped.


### Platforms that prohibit runtime code generation

Some platforms do not allow runtime code generation, for security reasons.
The prime example of this is the iOS platform for Apple mobile devices.
Only the first two stages would help performance on those platforms.

For these platforms a `x5` speedup will not be possible, but a doubling of speed should be.

### Tiered execution

The ultimate goal is to have several tiers of execution, each tailored to some subset of the program.
See [tiers](./tiers.md) for more details.

## The stages to high performance

### Stage 1 -- Python 3.10

The key improvement for 3.10 will be an adaptive, specializing interpreter.
The interpreter will adapt to types and values during execution, exploiting
type stability in the program, without needing runtime code generation.

### Stage 2 -- Python 3.11

This stage will make many improvements to the runtime and key objects.
Stage two will be characterized by lots of "tweaks", rather than any "headline" improvement.
The planned improvements include:

* Improved performance for integers of less than one machine word.
* Improved peformance for binary operators.
* Faster calls and returns, through better handling of frames.
* Better object memory layout and reduced memory management overhead.
* Zero overhead exception handling.
* Further enhancements to the interpreter
* Other small enhancements.

### Stage 3 -- Python 3.12 (requires runtime code generation)

Simple "JIT" compiler for small regions. 
Compile small regions of specialized code, using a relatively simple, fast compiler.

### Stage 4 -- Python 3.13 (requires runtime code generation)

Extend regions for compilation.
Enhance compiler to generate superior machine code.

## Design and implementation guidelines

### Maintainability

CPython is developed using an open-source, collaborative model.
That means that any complexity in the implementation should be understandable, and maintainable by someone other than the author(s). In order to do this we will:

* Clearly document all parts of the implementation
* Explain the design using standard terminology and undergraduate-level computer science.
* Use code generators to separate concerns where not possible in C or Python. For example:
    * Separate semantics of a bytecode from execution stategy.
    * Allow declarative layout of objects that can be shared by interpreter and GC.

### Performance

Performance should be reasonably predictable and not change by large amounts for small changes in program or data.
Performance should improve for all code, not just loop-heavy and numeric code.

For example, the iteration speed and memory use of a list of 1 000 000 `int`s and a list of 999 999  `int`s and one `None`, should be close enough that the difference cannot be easily measured.
That is not to say that iterating over homegenous lists cannot be faster than for heterogenous lists, but that there should be no sharp edges in performance.

#### Speed of optimization

There is no guarantee that optimized code will be run, nor that code falls neatly into "hot" and "cold" categories. In order to prevent sharp edges in performance, the promotion of code from one tier to the next must be fast. 

Fast compilation is not without cost. The quality of the generated code may be degraded.
Performance may be worse on loop-heavy and numerical code, than for a heavyweight optimizer, but performance of application code should be more robust.

## High performance debugging and profiling

Currently debugging and profiling in Python are supporting by providing a general purpose tracing interface, `sys.settrace`. However, this is very slow. 
To avoid the complexity of trying to support this general purpose tracing in a high performance interpreter, we plan to provide an API for inserting debugging and profiling points into the bytecode at runtime. This can be incorporated into the "quickening" phase of the tier 1 interpreter, allowing debugging code at near full speed, and profiling at speeds comparable to CPython 3.9.
