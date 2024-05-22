# UID 305932226

# Hash Hash Hash
Hash Hash Hash is a project about implementing mutex locks for concurrency, analyzing the performance of two different locking strategies, and comparing them to the base hash table implementation.

## Building
To build, run command `make`

## Running
To run the program, run with options -t and -s like the examples below
./hash-table-tester -t 4 -s 50000
Generation: 39,424 usec
Hash table base: 238,552 usec
  - 0 missing
Hash table v1: 479,144 usec
  - 0 missing
Hash table v2: 83,692 usec
  - 0 missing

./hash-table-tester -t 4 -s 100000
Generation: 71,327 usec
Hash table base: 1,357,806 usec
  - 0 missing
Hash table v1: 2,314,769 usec
  - 0 missing
Hash table v2: 382,777 usec
  - 0 missing

To check for memory links, use valgrind like the example below
valgrind ./hash-table-tester -t 4 -s 100000
...
==3765== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)

## First Implementation
In the `hash_table_v1_add_entry` function, I added to lock the lock before the first line and unlock before the function returns (at the last line of the function and before the return statement in the if statement). The lock created is a global variable. It is initialized in hash_table_v1_create and destroyed in hash_table_v1_destroy.

### Performance
./hash-table-tester -t 4 -s 50000
Generation: 38,417 usec
Hash table base: 374,748 usec
  - 0 missing
Hash table v1: 489,649 usec
  - 0 missing

Version 1 is slower than the base version, because the lock created locks the entire function, which is not the most efficient way to implement concurrency. When multiple threads try to access the function, the contention for this global lock will force only one thread to access the function. However, multiple threads are actually able to access the function as long as they don't update the same hash table entry. The overhead of acquiring and releasing the lock could also slow things down. These altogether cause a decrease in efficiency and slowdown. 

## Second Implementation
In the `hash_table_v2_add_entry` function, I TODO

In the `hash_table_v2_add_entry` function, I added lock for the specific entry before getting the list head and unlock at the last line of the function call and also if the function exited early. I added an entry lock in the hash_table_entry struct, initialized it in *hash_table_v1_create, and destroyed it in hash_table_v1_destroy.

### Performance
```shell
TODO how to run and results
```

TODO more results, speedup measurement, and analysis on v2

## Cleaning up
To clean up, run command `make clean`