# Record event counts per thread

`perf record` can be used to record event counts for every thread when using `-s` option. Check following multi-thread program:  

```
$ cat sum.c
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
```

Build and use `perf record` to note down event counts for every thread:  

```
# gcc -fopenmp -g sum.c -o sum
# perf record -s ./sum
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 0.404 MB perf.data (8757 samples) ]
```

Use `perf report` to analyze it (`-T` option means displaying per-thread event counts):  

```
# perf report -T
......
#  PID   TID      cycles:uppp
  9960  9963        751824252
  9960  9961        750625307
  9960  9965        749742594
  9960  9967        749228142
  9960  9968                0
  9960  9970          9857914
  9960  9974          9150516
  9960  9977          8928628
......
```
