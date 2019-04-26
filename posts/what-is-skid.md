# What is skid

Skid is the distance between events which trigger the issue and the events which are actually caught by `perf`. To improve sampling accuracy, `Intel` brings in `PEBS (Precise Event-Based Sampling)`, whilst `AMD` introduces `IBS (Instruction-Based Sampling)`. Check the output of "`perf list --help`":  

	# perf list --help
	......
	The p modifier can be used for specifying how precise the instruction address should be. The p modifier can be specified multiple times:

           0 - SAMPLE_IP can have arbitrary skid
           1 - SAMPLE_IP must have constant skid
           2 - SAMPLE_IP requested to have 0 skid
           3 - SAMPLE_IP must have 0 skid, or uses randomization to avoid
               sample shadowing effects.

	For Intel systems precise event sampling is implemented with PEBS which supports up to precise-level 2, and precise level 3 for some special cases

    On AMD systems it is implemented using IBS (up to precise-level 2). 

Following command is an example which requires no skid for sampling `mem-loads` event.

	# perf record -e "cpu/mem-loads/pp"