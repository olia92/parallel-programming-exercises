```
$ g++ accFill_ex2.cpp -o accFill_ex2_gcc

$ nvc++ accFill_ex2.cpp -o accFill_ex2_nvc -Minfo=all
```

#### time
|       | g++      | nvc++   |
|---    |---       |---      |
|real   | 0m7,265s |0m5,021s |
|user   | 0m6,913s |0m4,604s |
|sys    | 0m0,344s |0m0,407s |


---

## `nvc compiler`
```
nvc++ -acc -ta=multicore,tesla -Minfo ...
```
`-acc` : enable OpenACC directives (default GPU)  
`-acc = mutlicore` : parallelize for a multicoreCPU  
`-acc = host` : run serially on host CPU  
`-gpu=cc35` : target NVIDIA GPU  

---

```
$ nvc++ -acc -gpu=cc35 accFill_ex2.cpp -Minfo
main:
     30, Generating implicit copy(sum) [if not already present]
         Generating create(status[:nCount]) [if not already present]
     31, Loop is parallelizable
         Generating Tesla code
         31, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
     37, Loop is parallelizable
         Generating Tesla code
         37, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
             Generating reduction(+:sum)

```
```
$ nvc++ -acc=multicore  accFill_ex2.cpp -Minfo -o accFill_mul
main:
     31, Loop is parallelizable
         Generating Multicore code
         31, #pragma acc loop gang
     37, Loop is parallelizable
```
```
$ nvc++ -acc=host  accFill_ex2.cpp -Minfo -o accFill_mul
main:
     31, Loop is parallelizable
         Memory set idiom, loop replaced by call to __c_mset1
     37, Loop is parallelizable

```
|    |  gpu       | multicore  | host      |
|--- | ---        | ---        | ---       |
real |   0m0,484s |   0m1,516s |  0m1,007s |
user |   0m0,291s |   0m3,943s |  0m0,611s |
sys  |   0m0,145s |   0m0,472s |  0m0,386s |

---

## Kernel Construct
```c++
#pragma acc kernels create ( status[0:nCount] ) 
  {
   for(int i=0; i < nCount; i++)
     status[i] = 1;

   
   
#pragma acc loop vector reduction(+:sum)
   for(int i=0; i < nCount; i++)
     sum += status[i];
  }
```
```
$ nvc++ -acc -gpu=cc35 -Minfo=all accFill_ex2.cpp -o accFill_ex2 && time ./accFill_ex2
main:
     30, Generating implicit copy(sum) [if not already present]
         Generating create(status[:nCount]) [if not already present]
     31, Loop is parallelizable
         Generating Tesla code
         31, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
     37, Loop is parallelizable
         Generating Tesla code
         37, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
             Generating reduction(+:sum)
ncount = 1000000000
Sum = 1000000000
Final sum is 1000 millions

real    0m0,451s
user    0m0,314s
sys     0m0,117s

```
## Parallel Construct
```c++
#pragma acc parallel create(status[0:nCount])
{
#pragma acc loop
   for(int i=0; i < nCount; i++)
     status[i] = 1;

   // Sanity check to see that array is filled with ones
   
#pragma acc loop vector reduction(+:sum)
   for(int i=0; i < nCount; i++)
     sum += status[i];
} 
```

```
$ nvc++ -acc -gpu=cc35 -Minfo=all accFill_ex1-2.cpp -o accFill_ex1-2 && time ./accFill_ex1-2
main:
     27, Generating Tesla code
         29, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
         35, #pragma acc loop gang, vector(128) /* blockIdx.x threadIdx.x */
             Generating reduction(+:sum)
     27, Generating create(status[:nCount]) [if not already present]
         Generating implicit copy(sum) [if not already present]
     35, Loop is parallelizable
Sum = 1000000000
Final sum is 1000 millions

real    0m0,325s
user    0m0,179s
sys     0m0,120s
```

