# Linux perf little book
When talking about `perf` in `Linux`, it actually refers to `2` things:  

a) `Perf_events` (also be called as `perf` for short): a subsystem which was merged into Linux `kernel` since `2.6.31`;  

b) A powerful and comprehensive user-space tool: `perf`, which leverages `perf_events` subsystem to do performance analysis. 

`Perf` is a really powerful tool. As Brendan Greeg wrote in his [Choosing a Linux Tracer (2015)](http://www.brendangregg.com/blog/2015-07-08/choosing-a-linux-tracer.html):  

>  If there's one tracer I'd recommend people learn, it'd be perf, as it can solve a ton of issues, and is relatively safe.
  
In this small tutorial, I will give a whirlwind tool of the user-space `perf` utility.  




