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
```
$ nvc++ -acc=host -Minfo accTask.cpp  -o accTask && ./accTask 4 10000
main:
     41, Loop is parallelizable
Duration 2.37e-07 second
Final sum is 4
```
```
$ nvc++ -acc=multicore -Minfo accTask.cpp  -o accTask && ./accTask 4 10000
main:
     39, Generating Multicore code
         41, #pragma acc loop gang
     39, Generating reduction(+:sum)
Duration 0.0205164 second
Final sum is 4
```
