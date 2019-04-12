# Measure scheduler latency

`perf sched` can be used to measure scheduler latencies. E.g,:  

	# perf sched record sleep 10
	# perf sched latency

	-----------------------------------------------------------------------------------------------------------------
	  Task                  |   Runtime ms  | Switches | Average delay ms | Maximum delay ms | Maximum delay at       |
	 -----------------------------------------------------------------------------------------------------------------
	  sleep:11599           |      1.367 ms |        2 | avg:    0.040 ms | max:    0.056 ms | max at: 271543.365213 s
	  perf:11598            |      0.808 ms |        1 | avg:    0.039 ms | max:    0.039 ms | max at: 271553.366926 s
	  rcuc/0:11             |      0.000 ms |      110 | avg:    0.029 ms | max:    0.046 ms | max at: 271551.278028 s
	  dhcpcd:416            |      0.113 ms |        1 | avg:    0.029 ms | max:    0.029 ms | max at: 271545.077962 s
	  rcu_preempt:10        |      0.000 ms |      334 | avg:    0.029 ms | max:    0.065 ms | max at: 271546.574674 s
	  kworker/u4:1-ev:11597 |      3.255 ms |       61 | avg:    0.026 ms | max:    0.065 ms | max at: 271548.704707 s
	  kworker/1:1H-kb:218   |      0.066 ms |        2 | avg:    0.026 ms | max:    0.028 ms | max at: 271548.704684 s
	  ksoftirqd/0:9         |      3.141 ms |      217 | avg:    0.025 ms | max:    0.067 ms | max at: 271550.464711 s
	  kworker/0:3-mem:8765  |      1.355 ms |       38 | avg:    0.024 ms | max:    0.099 ms | max at: 271550.464690 s
	  jbd2/sda-8:231        |      0.542 ms |        6 | avg:    0.021 ms | max:    0.042 ms | max at: 271549.775918 s
	  systemd:1             |      0.211 ms |        1 | avg:    0.020 ms | max:    0.020 ms | max at: 271543.826050 s
	......

`perf sched latency` shows the latency statistics for every task: average, minimum and maximum values.