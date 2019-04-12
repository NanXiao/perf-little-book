# Profile memory access

`perf mem` command can be used to profile memory access. I.e, `perf mem record` samples while `perf mem report` shows the results. By default, `perf mem record` will count both load and store operations, and `-t` option can be used to specify one of them (e.g, `-t load`). Check following example:  

	# perf mem record ./stream
  	# perf mem report --stdio
	# To display the perf.data header info, please use --header/--header-only options.
	#
	#
	# Total Lost Samples: 0
	#
	# Samples: 4K of event 'cpu/mem-loads,ldlat=30/P'
	# Total weight : 203745
	# Sort order   : local_weight,mem,sym,dso,symbol_daddr,dso_daddr,snoop,tlb,locked
	#
	# Overhead       Samples  Local Weight  Memory access             Symbol                            Shared Object      Data Symbol             Data Object        Snoop         TLB access              Locked
	# ........  ............  ............  ........................  ................................  .................  ......................  .................  ............  ......................  ......
	#
	     9.03%           575  32            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     8.97%           554  33            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     7.19%           431  34            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     6.79%           395  35            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     5.35%           303  36            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     4.16%           229  37            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     3.71%           199  38            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No
	     3.08%           153  41            L1 or L1 hit              [.] main                          stream             [.] 0x00007ffcac77e3c8  [stack]            None          L1 or L2 hit            No

	......