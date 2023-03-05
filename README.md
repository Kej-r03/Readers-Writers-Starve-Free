# Starve Free Readers-Writers Problem

## Problem Description
The readers-writers problem is a standard synchronizing problem, consisting 2 processes, reading and writing in a shared memory.
The major constraint is that at a time, only either readers or writers can access the shared memory, but not both types simultaneously. The simple solution to this problem suffers from the flaw of starvation, in which either reader or writer can starve.
In this assignment, a starve free solution is presented.

# Starve Free Solution
## Pseudocode

#### Semaphores:
```
// Variable to count readers
int readcount = 0;            // current number of reading processes

// Binary semaphores
semaphore writer_mutex = 1;       // mutex for writers
semaphore reader_mutex = 1;      // mutex for readers
semaphore turn = 1;  // to decide reader/writer turn
```

### Reader Process:
```
Reader() {
  .
  .
  .
    wait(turn);  //waiting for reader's turn in queue for execution
    wait(reader_mutex); //ensuring only one read process gets the chance to             
                      // update read_count
    readcount++;
    if(readcount==1){
        wait(writer_mutex); //first reader will ask for resource 
                            //access for readers
    }
    signal(reader_mutex); //releasing mutex lock for next reader
    signal(turn);

    /reading is performed/
    /critical section/

    wait(reader_mutex); //ensuring only one read process gets the chance
                        // to update readcount
    readcount--;
    if(readcount==0){
        signal(writer_mutex); //freeing the resources for writers to write
    }
    signal(reader_mutex);   //releasing mutex lock for next reader
  .
  .
  .
}
```

### Writer Proess:

```
Writer(){
  .
  .
  .
    wait(turn); //waiting for writer's turn in queue for execution
    wait(writer_mutex); //waiting for reader to finish reading

    /writing is performed/
    /critical section/

    signal(writer_mutex); //releasing the resource for the other reader
                        // or writer process
    signal(turn); 
  .
  .
  .
}
```

## Explanation

We have used an extra semaphore 'turn'. As per the queue, whichever amongst reader/writer is next has turn set to 1, consequently enters in the same sequence as in queue. **The next reader/writer process is entered only after "turn" is released.** <br />
The logic followed for reader and writer mutual exclusion is that all the readers that occur before the next writer is encountered are counted by the readcount, and they are allocated the shared memory, which is now locked by "writer_mutex", and it is released only when all the reader processes finish. Then writer executes.
Thus starving is saved in readers-writers problem.