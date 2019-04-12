# What is profiling

Profiling should be an indispensable function for any performance analysis tool. But wait, what is profiling on earth? The following definition is from [Wikipedia](https://en.wikipedia.org/wiki/Profiling_(computer_programming)):  

> In software engineering, profiling ("program profiling", "software profiling") is a form of dynamic program analysis that measures, for example, the space (memory) or time complexity of a program, the usage of particular instructions, or the frequency and duration of function calls. Most commonly, profiling information serves to aid program optimization.

> Profiling is achieved by instrumenting either the program source code or its binary executable form using a tool called a profiler (or code profiler). Profilers may use a number of different techniques, such as event-based, statistical, instrumented, and simulation methods.  

As a profiler, `perf` provides two working mode: sampling (`perf record`) and counting (`perf stat`). Sampling is checking running system periodically to show the hotspot of the application, while counting is just a statistic of some events (mostly need support from underlying hardware).  