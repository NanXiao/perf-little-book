# Install and run perf

Most `Linux` distros enable `perf_events` subsystem in kernel and ship `perf` utility by default. If not, the distro should provide package already. E.g., On `Arch Linux`, setting up `perf` is easy:  

	# pacman -S perf

BTW, [Acme](https://github.com/acmel) is the maintainer of `perf`, so you can always build and try state-of-the-art feature from his `perf/core` branch:  

	# git clone git://git.kernel.org/pub/scm/linux/kernel/git/acme/linux -b perf/core
	# cd linux/tools/perf
	# make menuconfig
	# make

When `perf` is ready, you can launch it in terminal:  

	# perf
	
	 usage: perf [--version] [--help] [OPTIONS] COMMAND [ARGS]
	
	 The most commonly used perf commands are:
	   annotate        Read perf.data (created by perf record) and display annotated code
	   archive         Create archive with object files with build-ids found in perf.data file
	   bench           General framework for benchmark suites
	......

This will list all supported `perf` commands (The `main` function code of `perf` is [here](https://github.com/torvalds/linux/blob/54c490164523de90c42b1d89e7de3befe3284d1b/tools/perf/perf.c#L424)).  

There is a `/proc/sys/kernel/perf_event_paranoid` file which controls use of the performance events system by unprivileged users (without `CAP_SYS_ADMIN`), and the default value is 2:  

	# cat  /proc/sys/kernel/perf_event_paranoid
	2
The meaning of its value is like following:  

> -1: Allow use of (almost) all events by all users  
     Ignore mlock limit after perf_event_mlock_kb without CAP_IPC_LOCK  
>\>=0: Disallow ftrace function tracepoint by users without CAP_SYS_ADMIN   
     Disallow raw tracepoint access by users without CAP\_SYS\_ADMIN  
\>=1: Disallow CPU event access by users without CAP\_SYS\_ADMIN  
\>=2: Disallow kernel profiling by users without CAP\_SYS\_ADMIN

This means you may not use some functions of `perf` if you are not privileged user (e.g., `perf mem`). So if you meet something strange, such as `perf` can't sample data, please try run as privileged user (i.e., `sudo`) or check the value of `/proc/sys/kernel/perf_event_paranoid` file.
    