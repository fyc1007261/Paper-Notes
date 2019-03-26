# MapReduce: Simplified Data Processing on Large Clusters

## Introduction

- Most of our computations involved applying a map operation to each logical “record” in our input in order to compute **a set of intermediate key/value pairs**, and then applying a reduce operation to **all the values that shared the same key**.

### Execution Flow

- The library splits the input files into *M* pieces, and starts up many copies of the program on a cluster of machines.
- The **master** picks idle machines and assigns each one a map task or a reduce task.
- The *map* workers read the split files and parse the <key, value> pair, and write the pairs into *R* files periodically.
- The master forwards the files to the *reduce* workers. The *reduce* workers sort the key so that **all occurrences of the same key are grouped together.**  The sorting is needed because typically many different keys map to the same reduce task.
- The *reduce* workers iterate over the data and call the user-defined *Reduce* function.
- After all above completed, the master wakes the user application.



## Implementation 

### Fault Tolerance 

#### Worker Failure

- Master periodically pings workers. If no response is received, then the worker is marked failed.
- Completed map tasks are re-executed on a failure because their output is stored on the **local disk(s)** of the failed machine and is therefore inaccessible. Completed reduce tasks do not need to be re-executed since their output is stored in a **global file system**.
- When a map task is executed first by worker A and then later executed by worker B (because A failed), all workers executing reduce tasks are notified of the re-execution. Any reduce task that has not already read the data from worker A will read the data from worker B.

#### Master Failure

- Use checkpoints of the master data structures.
- Abort the process if master fails.

#### Semantics in the Presence of Failures

- Atomic commits of map and reduce task outputs.
- When a reduce task completes, the reduce worker atomically renames its temporary output file to the final output file.

### Backup Tasks

- The performance may be strongly affected by a "straggler" (with bad disk, etc.).
- When the MapReduce process is nearly completed, the master schedules backup executions of the remaining in-progress tasks, and takes either output as the result.