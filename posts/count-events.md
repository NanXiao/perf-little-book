# Count events

`perf stat` is used to count interested events:  

	# perf stat
	^C
	 Performance counter stats for 'system wide':
	
	           2376.91 msec cpu-clock                 #    2.000 CPUs utilized
	               123      context-switches          #    0.052 K/sec
	                 1      cpu-migrations            #    0.000 K/sec
	                 0      page-faults               #    0.000 K/sec
	         102707402      cycles                    #    0.043 GHz                      (50.10%)
	          34857921      instructions              #    0.34  insn per cycle           (75.00%)
	           7832558      branches                  #    3.295 M/sec                    (75.02%)
	            168058      branch-misses             #    2.15% of all branches          (74.88%)
	
	       1.188558385 seconds time elapsed
	
The above example shows some default events which "`perf stat`" will count. `-I` option can be used to set the frequency (unit is `ms`) of displaying result, and `--interval-count` is to specify how many times you want to run "`perf stat`". Check following example:  

	# perf stat -I 1000 --interval-count 5 -e cycles
	#           time             counts unit events
	     1.000454837            5005909      cycles
	     2.001134074            3579497      cycles
	     3.001690949            4350491      cycles
	     4.002221214            3504926      cycles
	     5.002936768            4941014      cycles
	
