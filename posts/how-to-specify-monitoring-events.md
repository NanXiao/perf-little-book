# How to specify monitoring events

The power of `perf` embodies it can monitor various of events (hardware, software, etc). Currently, `4` subcommands support `-e` option which can specify events: `perf record`, `perf stat`, `perf top` and `perf trace`. Use `perf list` to show available events:  

	# perf list
	
	List of pre-defined events (to be used in -e):
	
	  branch-instructions OR branches                    [Hardware event]
	  branch-misses                                      [Hardware event]
	  bus-cycles                                         [Hardware event]
	  cache-misses                                       [Hardware event]
	  cache-references                                   [Hardware event]
	  cpu-cycles OR cycles                               [Hardware event]
	  instructions                                       [Hardware event]
	  ref-cycles                                         [Hardware event]
	
	  alignment-faults                                   [Software event]
	  bpf-output                                         [Software event]
	  context-switches OR cs                             [Software event]
	  cpu-clock                                          [Software event]
	......  

`Perf` also provides plentiful formats of specifying interested events:  

(1) Standard event name:  
This is the most straightforward method: just use event name showed in "`perf list`". E.g.:"`perf stat -e cache-misses`".  

(2) Raw PMU event:  
PMU, abbreviated for "Performance Monitoring Unit", are hardware counters which record CPU utilities. PMU events are defined in `/sys/bus/event_source/devices/<pmu>/events/` directory. Either Use "`pmu/param1=0xxx,param2/`" or "`rNNNN`" (`r` denotes raw, and `N` is a hex digit. It is composed of "event + mask"). Still use "cache-misses" as an example:  
 
    # cat /sys/bus/event_source/devices/cpu/events/cache-misses
	event=0x2e,umask=0x41

So both following instructions monitor "cache-misses" event:  

	# perf stat -e cpu/event=0x2e,umask=0x41/
	# perf stat -e r412e  

(3) Hardware breakpoint:
The format is "`mem:addr[/len][:access]`", and access can be `r` (read), `w` (write) and `x` (execute). E.g., profile write accesses in `[0x1000~1008)`:    

	#  perf stat -e  mem:0x1000/8:w


(4) Event group. E.g,:  

	# perf stat -e "{LLC-load-misses,cache-misses}"


