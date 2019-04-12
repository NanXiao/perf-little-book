# Check cache false sharing

Generally speaking, cache false sharing is one processor modifies the data in one cache line, the cache protocol requires other processors who access the same data need to refresh the cache line. "`perf c2c`" command is used to debug this issue.

Check following code:  

	$ cat false_share.c
	#include <omp.h>

	#define N 100000000
	#define THRAED_NUM 8
	
	int values[N];
	
	int main(void)
	{
	    int sum[THRAED_NUM];
	
	    #pragma omp parallel for
	    for (int i = 0; i < THRAED_NUM; i++)
	    {   
	        for (int j = 0; j < N; j++)
	        {
	            sum[i] += values[j] >> i;
	        }
	    }
	    
	    return 0;
	}

The size of `sum` array is `64` bytes on my `X64` platform, and resides in one cache line. Because all `8` threads will write it simultaneously:  
  
	sum[i] += values[j] >> i;
It will cause cache false sharing issue. Build and use "`perf c2c record`" command to profile it:  

	$ gcc -fopenmp -g false_share.c -o false_share
	$ perf c2c record ./false_share

Use "`perf c2c report`" to analyze it, and "`HITM`" event is the central issue:  

	$ perf c2c report --stdio
	......
	=================================================
	            Trace Event Information
	=================================================
	  Total records                     :      65407
	  ......
	  LLC Misses to Local DRAM          :       36.9%
      LLC Misses to Remote DRAM         :       33.8%
      LLC Misses to Remote cache (HIT)  :        0.0%
      LLC Misses to Remote cache (HITM) :       29.2%
	  ......
	=================================================
      Shared Cache Line Distribution Pareto      
	=================================================
	#
	#        ----- HITM -----  -- Store Refs --  --------- Data address ---------                      ---------- cycles ----------    Total       cpu                           Shared                        
	#   Num      Rmt      Lcl   L1 Hit  L1 Miss              Offset  Node  PA cnt        Code address  rmt hitm  lcl hitm      load  records       cnt              Symbol       Object       Source:Line  Node
	# .....  .......  .......  .......  .......  ..................  ....  ......  ..................  ........  ........  ........  .......  ........  ..................  ...........  ................  ....
	#
	  -------------------------------------------------------------
	      0       41      446   159354    22984      0x7fff35cd5c40
	  -------------------------------------------------------------
	          58.54%   59.19%    0.00%    0.00%                0x18     0       1      0x55bfa7082232      1734      1122      1150    72662        14  [.] main._omp_fn.0  false_share  false_share.c:17   0  1
	          41.46%   38.57%    0.00%    0.00%                0x18     0       1      0x55bfa7082264      1341      1186      1151    71575        14  [.] main._omp_fn.0  false_share  false_share.c:17   0  1
	           0.00%    0.00%    3.90%    1.41%                0x20     0       1      0x55bfa708226d         0         0         0     6541         1  [.] main._omp_fn.0  false_share  false_share.c:17   1
	           0.00%    0.67%    0.00%    0.00%                0x24     0       1      0x55bfa708223b         0      4398        42     1484         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   13.84%   14.21%                0x24     0       1      0x55bfa708226d         0         0         0    25327         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.67%    0.00%    0.00%                0x28     0       1      0x55bfa708223b         0      1262        43     1498         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   13.99%   14.04%                0x28     0       1      0x55bfa708226d         0         0         0    25520         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.22%    0.00%    0.00%                0x2c     0       1      0x55bfa708223b         0       694        44     1661         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   13.60%   16.43%                0x2c     0       1      0x55bfa708226d         0         0         0    25449         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   14.00%   13.86%                0x30     0       1      0x55bfa708226d         0         0         0    25501         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.22%    0.00%    0.00%                0x34     0       1      0x55bfa708223b         0      1742        42     1548         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   13.14%   12.81%                0x34     0       1      0x55bfa708226d         0         0         0    23880         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.22%    0.00%    0.00%                0x38     0       1      0x55bfa708223b         0       286        42     1467         1  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   14.08%   13.62%                0x38     0       1      0x55bfa708226d         0         0         0    25569         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.22%    0.00%    0.00%                0x3c     0       1      0x55bfa708223b         0       278        44     1420         2  [.] main._omp_fn.0  false_share  false_share.c:17   0
	           0.00%    0.00%   13.44%   13.62%                0x3c     0       1      0x55bfa708226d         0         0         0    24551         2  [.] main._omp_fn.0  false_share  false_share.c:17   0

The nifty feature is the report shows which line of source code causes cache false sharing.  

Modify the code to avoid false sharing:  

	#include <omp.h>

	#define N 100000000
	#define THRAED_NUM 8
	
	int values[N];
	
	int main(void)
	{
	    int sum[THRAED_NUM];
	
	    #pragma omp parallel for
	    for (int i = 0; i < THRAED_NUM; i++)
	    {   
	        int local_sum;
	        for (int j = 0; j < N; j++)
	        {
	            local_sum += values[j] >> i;
	        }
	        sum[i] = local_sum;
	    }
	    
	    return 0;
	}


This time the "`perf c2c report`" outputs a very beautiful summary:  

	=================================================
            Trace Event Information              
	=================================================
	  ......
	  LLC Misses to Local DRAM          :       88.7%
      LLC Misses to Remote DRAM         :        9.9%
      LLC Misses to Remote cache (HIT)  :        0.0%
      LLC Misses to Remote cache (HITM) :        1.4%
	  ......