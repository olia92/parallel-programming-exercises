for `nCount=10000` and `nLoop=10000`  
gpu --> **Failing in Thread:1  
call to cuStreamSynchronize returned error 702: Launch timeout**  

```
$ nvc++ -acc -gpu=cc35  -Minfo accTask.cpp  -o accTask && time ./accTask 10000 10000
task(int, int):
     12, Generating Tesla code
         14, #pragma acc loop worker, vector /* threadIdx.y threadIdx.x */
             Generating implicit reduction(+:counter)
     14, Loop is parallelizable
main:
     40, Generating Tesla code
         42, #pragma acc loop gang /* blockIdx.x */
             Generating reduction(+:sum)
     40, Generating implicit copy(sum) [if not already present]
Failing in Thread:1
call to cuStreamSynchronize returned error 702: Launch timeout


real    0m20,362s
user    0m20,239s
sys     0m0,092s
```
! host & multicore execute normally
