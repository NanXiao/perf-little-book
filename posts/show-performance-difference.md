# Show performance difference

`perf diff` command is used to displays the performance difference amongst two or more `perf.data` files captured via `perf record` command. If no input files provided, it will compare `perf.data` and `perf.data.old` by default. Check following example:  

	# perf diff
	# Event 'cycles:pp'
	#
	# Baseline  Delta Abs  Shared Object       Symbol
	# ........  .........  ..................  ..............................................
	#
	    20.49%     -0.28%  stream              [.] main._omp_fn.4
	    20.53%     -0.26%  stream              [.] main._omp_fn.5
	    24.62%     -0.25%  stream              [.] main._omp_fn.7
	     0.44%     -0.21%  [kernel.kallsyms]   [k] preempt_count_sub
	    24.70%     -0.20%  stream              [.] main._omp_fn.6
	     0.91%     -0.17%  stream              [.] main._omp_fn.2
	               +0.12%  [kernel.kallsyms]   [k] task_tick_fair
	               +0.09%  [kernel.kallsyms]   [k] perf_event_task_tick
	     0.01%     +0.08%  [kernel.kallsyms]   [k] sugov_should_update_freq
	     0.25%     +0.08%  [kernel.kallsyms]   [k] get_page_from_freelist
		......
`Baseline` is from `perf.data.old`, and `perf diff` only show events  comparison result matching both specified files.
