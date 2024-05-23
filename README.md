# UID 305932226

# Hash Hash Hash
Hash Hash Hash is a project about implementing mutex locks for concurrency, analyzing the performance of two different locking strategies, and comparing them to the base hash table implementation.

## Building
To build, run command `make`

## Running
To run the program, run with options -t and -s like the examples below
./hash-table-tester -t 4 -s 50000
Generation: 35,662 usec
Hash table base: 194,696 usec
  - 0 missing
Hash table v1: 471,885 usec
  - 0 missing
Hash table v2: 72,456 usec
  - 0 missing

./hash-table-tester -t 4 -s 100000
Generation: 70,919 usec
Hash table base: 923,240 usec
  - 0 missing
Hash table v1: 1,694,835 usec
  - 0 missing
Hash table v2: 362,218 usec
  - 0 missing

The -t option specifies the number of threads, and the -s option specifies the number of hash table entries to test on

To check for memory links, use valgrind like the example below
valgrind ./hash-table-tester -t 4 -s 50000
...
==2932== 
==2932== HEAP SUMMARY:
==2932==     in use at exit: 0 bytes in 0 blocks
==2932==   total heap usage: 600,038 allocs, 600,038 frees, 16,268,529 bytes allocated
==2932== 
==2932== All heap blocks were freed -- no leaks are possible
==2932== 
==2932== For lists of detected and suppressed errors, rerun with: -s
==2932== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)

## First Implementation
In the `hash_table_v1_add_entry` function, I added to lock the lock before the first line and unlock before the function returns (at the last line of the function and before the return statement in the if statement). The lock created is a global variable. It is initialized in hash_table_v1_create and destroyed in hash_table_v1_destroy.

### Performance
./hash-table-tester -t 4 -s 50000
Generation: 35,778 usec
Hash table base: 191,450 usec
  - 0 missing
Hash table v1: 436,737 usec
  - 0 missing

Version 1 is slower than the base version, because the lock created locks the entire function, which is not the most efficient way to implement concurrency. When multiple threads try to access the function, the contention for this global lock will force only one thread to access the function. However, multiple threads are actually able to access the function as long as they don't update the same hash table entry. The overhead of acquiring and releasing the lock could also slow things down. These altogether cause a decrease in efficiency and slowdown. 

## Second Implementation
In the `hash_table_v2_add_entry` function, I lock the hash table entry before getting the list head and unlock before the function returns (at the last line of the function and before the return statement in the if statement). The lock created is a member variable in the hash_table_entry struct. Every lock is initialized in hash_table_v1_create and destroyed in hash_table_v1_destroy.

### Performance
./hash-table-tester -t 4 -s 50000
Generation: 35,591 usec
Hash table base: 192,759 usec
  - 0 missing
Hash table v1: 464,799 usec
  - 0 missing
Hash table v2: 74,998 usec
  - 0 missing

V2 is faster than V1 and base, because I implemented a finer-grain locking method. Instead of using a global lock, I have a lock for each hash table entry. This allows multiple threads to access the function and work on different hash table entries, while making sure that multiple threads working on the same hash table entry obey concurrency rules to avoid race conditions. This reduces lock contention than V1. Although V2 will create more lock overheads than V1, as there are more locks implemented, the overhead is not as significant as compared to the concurrency it allows for. With 4 threads, V2 is at least 2x and sometimes 3x faster than base and V1. Depending on the number of threads we use, the increase in speed could vary, but it is always faster than V1 and base. The runtime of V2 is roughly <= base / (#cores - 1).

## Cleaning up
To clean up, run command `make clean`