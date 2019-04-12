# An example of profiling application

Let's see a simple example:  

	$ cat add_vec.cpp
	#include <algorithm>
	#include <iostream>
	#include <vector>
	
	constexpr int vec_size = 10000;
	
	void mul(std::vector<int>& a, std::vector<int>& b, std::vector<int>& c)
	{
	    std::transform(
	        a.begin(),
	        a.end(),
	        b.begin(),
	        c.begin(),
	        [](auto op1, auto op2) {return op1 * op2;}
	        );
	}
	
	std::vector<int> A(vec_size, 1);
	std::vector<int> B(vec_size, 2);
	std::vector<int> C(vec_size);
	
	int main()
	{
	    mul(A, B, C);
	    for (auto const& v : C)
	    {
	        if (v != 2)
	        {
	            std::cout << "Oops, something happened!\n";
	            return 1;
	        }
	    }
	
	    return 0;
	}

Build and run "`perf record`" command to profile it:  

	$ g++ add_vec.cpp -o add_vec
	$ perf record ./add_vec
	[ perf record: Woken up 1 times to write data ]
	[ perf record: Captured and wrote 0.003 MB perf.data (27 samples) ]

A "perf.data" file will be generated. Use "`perf report`" command to analyze it:  

	$ perf report
	Samples: 27  of event 'cycles:uppp', Event count (approx.): 2149376
	Overhead  Command  Shared Object     Symbol
	  33.67%  add_vec  ld-2.28.so        [.] do_lookup_x
	  26.37%  add_vec  ld-2.28.so        [.] _dl_lookup_symbol_x
	  11.43%  add_vec  [unknown]         [k] 0xffffffffb2200a87
	   9.16%  add_vec  libc-2.28.so      [.] _dl_addr
	   8.43%  add_vec  add_vec           [.] main
	   4.21%  add_vec  ld-2.28.so        [.] _dl_relocate_object
	   2.76%  add_vec  libc-2.28.so      [.] strlen
	   2.45%  add_vec  ld-2.28.so        [.] strcmp
	   1.52%  add_vec  ld-2.28.so        [.] _dl_next_ld_env_entry
	
At least from output, `do_lookup_x` in `ld-2.28.so` is sampled mostly. Highlight `main` function and press `a` (which will call "`perf annotate`" command to show annotated code):  

	Samples: 27  of event 'cycles:uppp', 4000 Hz, Event count (approx.): 2149376
	main  /home/xiaonan/perf_example/add_vec [Percent: local period]
	Percent│      mov    %rax,%rdi
	       │    → callq  std::vector<int, std::allocator<int> >::begin
	       │      mov    %rax,-0x28(%rbp)
	       │      mov    -0x18(%rbp),%rax
	       │      mov    %rax,%rdi
	       │    → callq  std::vector<int, std::allocator<int> >::end
	       │      mov    %rax,-0x20(%rbp)
	       │5c:   lea    -0x20(%rbp),%rdx
	       │      lea    -0x28(%rbp),%rax
	       │      mov    %rdx,%rsi
	       │      mov    %rax,%rdi
	       │    → callq  __gnu_cxx::operator!=<int*, std::vector<int, std::allocator<int> > >
	       │      test   %al,%al
	       │    ↓ je     b6
	       │      lea    -0x28(%rbp),%rax
	       │      mov    %rax,%rdi
	       │    → callq  __gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >::operator*
	       │      mov    %rax,-0x10(%rbp)
	       │      mov    -0x10(%rbp),%rax
	100.00 │      mov    (%rax),%eax
	       │      cmp    $0x2,%eax
	       │    ↓ je     a8
	......

If you want to map the assembly code to source code, try to build program with `-g` option:  

	$ g++ add_vec.cpp -g -o add_vec

Another useful option of using "`perf record`" is `-g`, which records function call stack information.

	$ perf record -g ./add_vec
	[ perf record: Woken up 1 times to write data ]
	[ perf record: Captured and wrote 0.004 MB perf.data (28 samples) ]
	$ perf report
	Samples: 28  of event 'cycles:uppp', Event count (approx.): 2044515
	  Children      Self  Command  Shared Object     Symbol
	+   48.61%     0.00%  add_vec  [unknown]         [.] 0x5541f689495641d7                                                ▒
	+   48.61%     0.00%  add_vec  libc-2.28.so      [.] __libc_start_main                                                 ▒
	-   48.61%    18.12%  add_vec  add_vec           [.] main                                                              ▒
	   - 30.48% main                                                                                                       ▒
	        mul                                                                                                            ◆
	      - std::transform<__gnu_cxx::__normal_iterator<int*, std::vector<int, std::allocator<int> > >, __gnu_cxx::__normal▒
	         + __gnu_cxx::operator!=<int*, std::vector<int, std::allocator<int> > >                                        ▒
	   - 18.12% 0x5541f689495641d7                                                                                         ▒
	        __libc_start_main                                                                                              ▒
	        main   
	......

This time, every line had a '`+`' at the beginning which can show the call stack clearly (from upper lever to lower). Take `main` function as an example:  

	int main()
	{
	    mul(A, B, C);
	    for (auto const& v : C)
	    {
	        if (v != 2)
	        {
	            std::cout << "Oops, something happened!\n";
	            return 1;
	        }
	    }
	
	    return 0;
	}
The percentage of `mul` function's consume time is ~`30.48%`, `for-loop` accounts for ~`18.12%`. For column "`Children`", it denotes all the time spent in this function; while column "`Self`" will exclude other sub-function calls, e.g., `main` will exclude `mul`.