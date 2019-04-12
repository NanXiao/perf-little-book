# Profile system in real time

Just like `top` command:  

	# top
	top - 17:38:28 up 1 day,  8:44,  2 users,  load average: 0.00, 0.00, 0.00
	Tasks:  95 total,   1 running,  94 sleeping,   0 stopped,   0 zombie
	%Cpu(s):  0.0 us,  3.1 sy,  0.0 ni, 96.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
	MiB Mem :   3898.8 total,   2528.2 free,     85.2 used,   1285.3 buff/cache
	MiB Swap:      0.0 total,      0.0 free,      0.0 used.   3541.0 avail Mem
	
	  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
	 4395 root      20   0   10336   3352   2904 R   6.2   0.1   0:00.01 top
	    1 root      20   0   35804   9920   7708 S   0.0   0.2   0:03.06 systemd
	......

`perf top` command show system activity in real time:  

	# perf top
	Samples: 2K of event 'cycles:pp', 4000 Hz, Event count (approx.): 1153602589 lost: 0/0 drop: 0/0
	Overhead  Shared Object       Symbol
	   7.21%  [kernel]            [k] acpi_processor_ffh_cstate_enter
	   6.67%  libc-2.28.so        [.] __GI_____strtoull_l_internal
	   6.65%  [kernel]            [k] vsnprintf
	   5.64%  [kernel]            [k] kallsyms_expand_symbol.constprop.1
	   5.00%  perf                [.] rb_next
	......