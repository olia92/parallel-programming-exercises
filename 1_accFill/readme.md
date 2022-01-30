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
`-acc = mutlicore ` : parallelize for a multicoreCPU  
`-acc = host ` : run serially on host CPU  
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
