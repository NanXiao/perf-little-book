# Use ftrace

`perf ftrace` is a simple wrapper for kernel's `ftrace` functionality, and only supports single thread tracing now. Check following example:  

	# perf ftrace ./lock
	   2)               |  switch_mm_irqs_off() {
	   2)   1.792 us    |    load_new_mm_cr3();
	   2)   9.157 us    |  }
	 ------------------------------------------
	   2)    <idle>-0    =>   <...>-5785
	 ------------------------------------------
	
	   2)               |  finish_task_switch() {
	   2)               |    _raw_spin_unlock_irq() {
	   2)   ==========> |
	   2)               |      smp_irq_work_interrupt() {
	   2)               |        irq_enter() {
	   2)   0.612 us    |          rcu_irq_enter();
	   2)   0.631 us    |          irqtime_account_irq();
	   2)   0.457 us    |          preempt_count_add();
	   2)   3.818 us    |        }
	   2)               |        __wake_up() {
	......
`-T` option can be used to specify only tracing interested functions:  

	# perf ftrace -T __kmalloc ./lock
	  20) + 17.698 us   |  __kmalloc();
	  20)   3.167 us    |  __kmalloc();
	  20)   0.952 us    |  __kmalloc();
