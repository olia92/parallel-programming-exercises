```
$ nvc++ -acc -Minfo accTask.cpp  -o accTask && ./accTask 4 10000
task(int, int):
     12, Generating Tesla code
         14, #pragma acc loop worker, vector /* threadIdx.y threadIdx.x */
             Generating implicit reduction(+:counter)
     14, Loop is parallelizable
main:
     39, Generating Tesla code
         41, #pragma acc loop gang /* blockIdx.x */
             Generating reduction(+:sum)
     39, Generating implicit copy(sum) [if not already present]
Duration 4.72409 second
Final sum is 4
```
