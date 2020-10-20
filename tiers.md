# Tiers of Execution in a high-performance interpreter for CPython

## The tiers

All programs have parts that are executed very often and other parts that are executed infrequently or not at all.
It makes sense to treat these different parts differently. For parts of the code that are never executed, or executed just once,
it makes sense to spend the minimum amount of effort loading that code, even if it slows down execution a bit.
For code that is executed frequently, it makes sense to streamline its execution at much as possible,
even if that slows down loading considerably and costs more of other resources, such as memory.

It is, of course, impossible to know in general which parts of a program will be "hot", that is run very frequently,
and which parts will be "cold", that is nver run or run only once. Not only that, but there is not simple distinction between
hot and cold, but a range.
Much code is "warm", that is run reasonably often, but considerably less than the hottest part of the program.

To address this range of runtime characteristics, several tiers of execution should be employed.
We will number these 0 to 3. A minimum viable implementation would consist of tiers 0 and 1.
Tier 2 can then be added later for improved performance.
Tier 3 can then be added for better performance of longer running programs.

The higher a tier, the hotter the code that it will execute. So in terms of VM instructions executed, the majority will be executed by the higher tiers.

Before describing the tiers, we need some definitions

* Cold code: Code that is never run, run once or run very rarely.
* Warm code: Code that is run several times, but does not represent a significant part of the total runtime.
* Hot code: Code that is run many times, forming the majority of the total execution time of the program.
* Region: The extent of code that is treated as a unit by a tier. May be unrelated to the source code.

Since it is impossible, in general, to know beforehand which code will be hot and which will be cold.
All code must be treated as cold initially, only to be treated as warm or hot as it executes.
Consequently all code will be executed by tier 0, to start with. As it proves hotter, its execution will move to higher tiers.

Regions may be very small, only a few instructions, or contain thousands of lines of code.
They may be a whole function, part of a function or cross function boundaries.

### Tier 0

Tier 0 is designed to execute cold code. For simplicity we define "cold" code as code that has never been run, or run only once.
Once code has been run at tier 0, the next time it is executed, it will be at tier 1.
This simple cut off is useful, as module level and class level code will be executed exactly once, and much error handling code will
never be at all. 

The tier 0 execution engine, or interpreter, is designed to minimize the cost of loading the code from disk into memory
and to minimize the amount of memory used.
For tier 0, a code region is a code object. That is, exactly one function, class, or module (not including nested scopes).


### Tier 1

Tier 1 is for executing "warm" code. Its design should be a compromise between fast execution and keeping resource comsumption low.
To allow efficient promotion from tier 0 to tier 1, tier 1 regions match tier 0 regions. 

### Tier 2

Tier 2 is designed to execute "hot" code. It should be designed for fast execution without execessive use of resources.
Tier 2 regions are likely to be small, to avoid execessive optimization time and memory consumption.

### Tier 3

Tier 3 is designed to execute the hottest code. Its design is designed for the fastest execution with little or no consideration of resource use.
Tier 3 regions are likely to be larger than tier 2 regions and derived from those regions. The larger regions offer more oppportunity for optimization,
but will be more expensive to optimize.

### Tier -1

This is the slowest tier and exists to provide legacy profiling and debugging, via `sys.settrace`.
It is expected that tools will move over to the high performance profiling and debugging API that will be provided.
The sole design criterion is to not negativitely impact the performance or memory use of the other tiers.
Performance is expected to be very poor, although no worse that CPython 3.9 using `sys.settrace`.

### Comparison with the CPython 3.9 interpreter

The CPython 3.9 interpreter should be regarded as somewhere between a tier 0 and tier 1 interpreter,
probably nearer tier 0 but with much of the overhead of a tier 1 interpreter. 
When loading bytecode, a relatively expensive unmarshalling phase is required,
but no quickening or specialization is used in the interpreter.

## Optimizations

In order to speed up Python code, the execution engine must transform that code in a way that 
takes advantage of patterns in the code and is more suited to the underlying hardware.
The two key optimizations are specialization and compilation to machine code.

Specialization takes advantage of patterns in the code by tailoring code for particular types, that are seen at runtime.

Compilation to machine code removes the overhead of interpretation, and allows some traditional compiler optimizations 
to be applied.

