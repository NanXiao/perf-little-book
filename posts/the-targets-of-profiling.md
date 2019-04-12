# The targets of profiling

[An example of profiling application](an-example-of-profiling-application.md) demonstrates how to profile a program. Besides it, `perf` can also profile targets based on other qualifications:  

<table>
  <tr>
    <th>Target</th>
    <th>Option</th> 
  </tr>
  <tr>
    <th>Program</th>
    <th>"perf record program".</th> 
  </tr>
  <tr>
    <th>Process</th>
    <th>"perf record -p pid". Pid is process ID.</th> 
  </tr>
  <tr>
    <th>Thread</th>
    <th>"perf record -t tid". Tid is thread ID.</th> 
  </tr>
  <tr>
    <th>User</th>
    <th>"perf record -t uid". Uid can be user ID or name.</th> 
  </tr>
  <tr>
    <th>CPU</th>
    <th>"perf record -C cpuid". Cpuid the CPU ID.</th> 
  </tr>
  <tr>
    <th>System-wide</th>
    <th>"perf record -a". Profile all CPUs in the system. This is the default behaviour.</th> 
  </tr>
</table> 

More words about `-a` option: Since it is the default behavior when no target is designated, "`perf record -a`" is synonym to "`perf record`". A common example is to profile the system in `10` seconds: 

	# perf record -F 99 -a sleep 10

`-F` option denotes the sampling frequency. Use an odd number, such as `99`, is to avoid lockstep sampling (Please refer [perf CPU Sampling](http://www.brendangregg.com/blog/2014-06-22/perf-cpu-sample.html) and [What is lockstep sampling?](https://stackoverflow.com/questions/45470758/what-is-lockstep-sampling)).
