# MapReduce and the New Software Stack

## Intro
- Leverage commodity hardware, not supercomputers.
- MapReduce: enable large-scale data analysis with fault tolerance on commerical hardware.

## Distributed File Systems
- Commodity hardware
- Parallel-computing architecture
- Nodes stored on a rack, connected by a network (Gigabit ethernet)
- Racks connected by a switch
- Nodes can fail and racks can fail.
  - We don't want the computation to end if one of these fails. So..
  1. Files must be stored redundantly so another node can continue the job of a failed node
  2. Computations must be divided into tasks so one task can be restarted without affecting the other tasks
- Distributed File System (DFS)
  - Only useful if we have large files
  - Files are rarely updated. Mostly read for calculations.
  - Files divided into chunks (usually 64 megabytes in size),
    - Replicate across (generally 3) nodes in the cluster.
    - Master/name node keeps track of where chunks are (also replicated)

## MapReduce
- large-scale computations tolerant of hardware faults
- write two functions: map and reduce
  - Map task
    - Receives chunks outputs key-value pairs
    - Input Chunks -> [(k_0, v_0),..., (k_n, v_n)]
    - Key do not have to be unique
  - (Group by Keys)
    - Pairs collected by _master controller_ and sorted by key
    - keys divided up among all Reduce tasks
    - all key-value pairs with same key end up in same Reduce task
  - Reduce task
    - (k_i, [v_0, v_1, ..., v_m]) -> output_i
    - Handles one key at a time
    - Combines all keys with a given value in some way

### Word Count Example
- Goal: Count # of occurences of each word in a collection of documents
- Map function: string with n words -> (w_1, 1), (w_2, 1), ..., (w_n, 1)
- Note: multiple of the same word => we will have duplicate keys
- Reduce function: (w_i, counts=[1, 1, 1, ..., 1]) -> sum(counts)

### Group by Key
- After map function, system groups each key into (key, list of values)
- r reduce tasks (typically given by the user)
- master controller picks a hash function that applies to keys
  - produces a bucket number from 0 to r - 1
  - keys output from map are hashed
  - key-value pairs put into one of r local files (for the reduce tasks)
- (k,v_1), (k,v_2), ..., (k, v_n) becomes (k, [v_1, v_2, ..., v_n])

### Reduce Tasks
- Input is a pair with a key and a list of values
  - (key, [v_1, v_2, ..., v_n])
- Application of reduce funciton to a single key/values as a reducer
- Reduce task executes one or more reducers
- Output of all reduce tasks are merged into a single file

### Combiners
If reduce function is associative and communative then
  - Results can be combined in any order
  - Reducer function can be applied in the map tasks in parallel
  - (E.g. word count would have a pair of (w,m), where m is count)

### Reducers, Reduce Tasks, Compute Nodes, and Skew
One reduce tasks can execute each reducer at a different compute node
  - Maximum parallelism
  - Problem is that there is overhead in creating tasks
  - Often far more keys than compute nodes
  - Often significant variation in length of values
    - Reducers will take a different amount of time
    - So they will have skew, a significant difference in task time
    - If we send keys randomly to reduce tasks, we reduce skew
    - Reduce skew by using more Reduce tasks than compute nodes
    - Hope is that
      - long tasks occupy a compute node fully and
      - several shorter tasks run sequentially at a single compute node

### MapReduce Execution
- User program forks...
  - a Master controller process
  - some number of workers
- workers are generally Map workers or Reduce workers but not both
- tasks assigned to workers from Master
- generally creates one Map tasks for every chunks
- less than that for Reduce tasks (the Reduce task creates an intermediate file)
- Master keeps track of Map and Reduce workers (with periodic pings)
- Worker alerts master when it is finished with a task
- Map task creates a file for each reduce tasks
  - on local disk of the Worker that executes the Map tasks
  - Master is informed of location and sizes of each of these files, and target Reduce task
  - Reduce tasks is assigned by the master to a worker process with all files from Map
- Reduce task executes the code written by user and writes to output file

### Coping with Node Failures
- Worst case: compute node with Master fails, and whole jobs needs to be restarted
- If Map worker fails, Master detects it and sends tasks from that worker to another worker.
  - Needs to be done even if it completed, because files for reduce worker live on that worker's location.
  - Alerts the reduce nodes that location of input has changed
- If Reduce worker fails, Master sets status of currently excuting Reducer tasks to idle
  - Tasks will be rescheduled on another reduce worker later


## Exercise 2.2.1
- a) If each chunk is a document, then the number of words will probably differ greatly. There will be significant skew if we do not use a combiner at the Maps tasks and there will be a long list of words for some documents and very short lists for others. Using the combiner will be the length of unique words, which is more likely to be similar for the documents.

- b) The skew will be pretty small with 10 tasks as the documents will be randomly distributed across these 10 tasks and will be on average the same size. If we have 10,000 reducer tasks it is more likely that some will take longer than other tasks creating some skew.

- c) Using a combiner at the 100 Map tasks will reduce the skew significantly. The number of words in a document seems to different greatly depending on the document. The amount of unique words used is more likely to be similar across all documents. This list of unique words is what we get when using a combiner at the Map tasks.
