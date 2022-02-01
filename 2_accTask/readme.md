## NVC compiler 
#### gpu
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
#### serial (default-optimized)
```
$ nvc++ -acc=host -Minfo accTask.cpp  -o accTask && ./accTask 4 10000
main:
     41, Loop is parallelizable
Duration 2.37e-07 second
Final sum is 4
```
#### cpu-multicore
```
$ nvc++ -acc=multicore -Minfo accTask.cpp  -o accTask && ./accTask 4 10000
main:
     39, Generating Multicore code
         41, #pragma acc loop gang
     39, Generating reduction(+:sum)
Duration 0.0205164 second
Final sum is 4
```
---
## GCC compiler 
#### -O4

```
$ g++ -O4 accTask.cpp  -o accTask && time ./accTask 4 10000
Duration 3.7463e-05 second
Final sum is 4

real    0m0,002s
user    0m0,002s
sys     0m0,000s
```
#### no optimization
```
$ g++ -fopt-info accTask.cpp  -o accTask && time ./accTask 4 10000
Duration 107.616 second
Final sum is 4

real    1m47,618s
user    1m47,545s
sys     0m0,020s
```
