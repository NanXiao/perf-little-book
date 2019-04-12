# Trace system calls of command

`perf trace` works like `strace`, which show system calls of command:  

	# perf trace ls
         ? (         ): ls/14593  ... [continued]: execve())                                           = 0
     0.045 ( 0.003 ms): ls/14593 brk()                                                                 = 0x55699fe1a000
     0.051 ( 0.002 ms): ls/14593 arch_prctl(option: 0x3001, arg2: 0x7ffcf8e915b0)                      = -1 EINVAL (Invalid argument)
     0.077 ( 0.005 ms): ls/14593 access(filename: 0xd87603a0, mode: R)                                 = -1 ENOENT (No such file or directory)
     0.092 ( 0.006 ms): ls/14593 openat(dfd: CWD, filename: 0xd875d891, flags: RDONLY|CLOEXEC)         = 3
     0.100 ( 0.003 ms): ls/14593 fstat(fd: 3, statbuf: 0x7ffcf8e90790)                                 = 0
     0.105 ( 0.004 ms): ls/14593 mmap(len: 99497, prot: READ, flags: PRIVATE, fd: 3)                   = 0x7febd8722000
     0.111 ( 0.002 ms): ls/14593 close(fd: 3)                                                          = 0
     0.129 ( 0.006 ms): ls/14593 openat(dfd: CWD, filename: 0xd8766c70, flags: RDONLY|CLOEXEC)         = 3
     0.137 ( 0.003 ms): ls/14593 read(fd: 3, buf: 0x7ffcf8e90958, count: 832)                          = 832
     0.143 ( 0.002 ms): ls/14593 fstat(fd: 3, statbuf: 0x7ffcf8e907f0)                                 = 0
     0.147 ( 0.004 ms): ls/14593 mmap(len: 8192, prot: READ|WRITE, flags: PRIVATE|ANONYMOUS)           = 0x7febd8720000
	......