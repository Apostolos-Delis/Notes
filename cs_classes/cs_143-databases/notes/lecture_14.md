# Lecture 14 - Transactions and Locking 5/20/19

## Transactions

* A transaction is a logical group of read and write operations
* This group must operate atomically, so it either all succeeds or fails
* When multiple transactions access and modify data concurrently, we can get a whole
    slew of problems with consistency, particularly when each transaction can write.
* What happens when a transaction $T_1$ tries to write to the same block at the same time
    as $T_2$?
* With $n$ participating transactions, there are $n!$ possible serial schedules
* A schedule is conflict serializable if it is serial, or if it is equivalent
    in result to a serial schedule.
* If two instructions acting on different data points, we can swap the order of these two
    transactions without affecting the result
* If the two transactions are both reads, then order never matters. If the first is a
    read and the second is a write, then order matters; the same is true for vice versa.
* When two writes happen at the same time, both transactions will write to the value of
    $X$, so these transactions conflict
* Transactions will transform a database from a consistent state to another consistent
    state if there are no system failures, and the transactions are executed in
    isolation.

#### Schedule Serializability

* For each consecutive pair of operations from different transactions $T_i$ and $T_j$ in
    some schedule $S$, if the operations don't conflict, the order can be swapped to get
    a new schedule $S^{\prime}$
* Operations continue to get swapped until there are no more conflicting operations
* A schedule is **serial** if $T_i$ finishes all of its operations before $T_j$ begins
* Terminology:
    1. $S$ and $S^{\prime}$ are called **conflict equivalent** if we can convert $S$ into
       $S^{\prime}$ by swapping non-conflicting operators
    2. Not all serial schedules are conflict equivalent.
    3. If a schedule S is conflict equivalent to a serial schedule, we call it conflict
        serializable.
    4. Two schedules can produce the same result but not be conflict equivalent.
* Consider some schedule $S$. A **precedence graph** $G$ from $S$. The vertices consist
    of the individual transactions $T_i$, with an edge from $T_i$ to $T_j$ if:
    1. $T_i$ executes `write(X)` before $T_j$ executes `read(X)`
    2. $T_i$ executes `read(X)` before $T_j$ executes write
    3. $T_i$ executes `write(X)` before $T_j$ executes `write(X)`
* If $T_j\rightarrow T_j$ exists in the graph, then in any serial schedule $S^{\prime}$
    equivalent to $S$, $T_i$ must finish executing before $T_j$ begins
* If there are no cycles in the graph, then $S$ is conflict serializable

#### Transaction Isolation Levels and Implementation

* Isolation levels:
    1. **Serializability**: Serializable typically ensures serializable execution, easy
       to understand with limited concurrency
    2. **Repeatable Read**: only commited data may be read and no transaction may write
       between two reads of the same data point
    3. **Read Commited**: similar to Repeatable Read, but allows a transaction $T_2$ to
       update a data point in between reads of it in $T_1$ and then commit the change
    4. **Read Uncommited**: allows uncommited data to be read
* Regardless of isolation level, a transaction $T_j$ should not modify a data point that
    has already been modified/written by another transaction $T_i$. - These are called
    **Dirty Writes**
* SQL has an option called autocommit, when disabled, the keyword `COMMIT` must be
    included to commit the result of the transaction, so each query needs a `COMMIT`
* Each transaction can aqcuire a lock for each data item it accesses
    * Locks are held long enough to ensure serializability, but not enough to degrade
        performance
* Each transaction has a timestamp associated with it. Each data item maintains two
    timestamps, one of the most recent read and one of the most recent write
* The transaction then accesses each data item in order of the timestamps if two
    transactions conflict
* The transaction only reads the local copy of the database which is private to the
    transaction, and is thus isolated from other transactions.
* Then, all modifications within the transaction are made on the transaction’s
    private copy of the data
* The transaction engine stores a record of what changes need to be made to the
    database. After the transaction succeeds, the engine then checks if any other
    transaction modified the same data items. If it did, the transaction aborts and we
    rollback. Otherwise, the transaction commits.
* The major advantage of this is that you never have to wait to write data, and reads
    never are aborted, the disadvantage however is none of the transactions can see the
    other's writes, we will not know about this until we try to commit
* Snapshot isolation is very expensive and only worth if the probability of write
    conflicts are low

## Locking

* The most common way to ensure isolation is with locks
* While one transaction is accessing data, no other transaction can modify that data
* **Shared:**: If a transaction holds a shared-mode lock on some data item P, it can
    read from the item, but not to write it.
* **Exclusive**: If a transaction holds an exclusive-mode lock on some data item P,
    it can both read from and write to the item.
* Each time a transaction needs access to a data item, it must request a lock
* Multiple transactions can hold a read lock on the same data item, but as soon as a
    transaction wants to write to that item, it must wait until all shared-mode locks are
    released
* Starvation is prevented by whenever a transaction $T_i$ requests a lock on data item
    $A$ in a particular mode $M$, the lock manager will grant the lock if:
    1. No other transaction holds a lock on $A$ that conflicts with $M$
    2. There is no transaction that is waiting for lock $A$ and made its request before
       $T_i$
* When a lock has complete using item $P$, it will release its lock, but potentially not
    immediately, releasing too early loses serializability, since other transacitons can
    make clobbering writes, if we unlock too late, transactions will be unreasonably
    blocked, and if we unlock too late, the transactions get unreasonably blocked
* Another problem stems from deadlock, since if we unlock the lock too soon we get
    inconsistent states, but unlocking too late will get you deadlock
* The lock manager can also determine when a deadlock is about to occur, and tell the
    transaction to rollback.
* An important locking strategy that ensures serializability is the **Two-Phase Locking
    Protocol (2PL)**: which requires that each transaction makes lock and unlock requests
    in two phases:
    1. **Growing Phase**: The transaction may require locks, but not release any
    2. **Shrinking Phase**: The transaction may release locks, but not aquire any
* This strategy may result in transactions for a long time, increasing isolation and
    serializability, but decreasing performance
* Once deadlock is detected, the following procedure is performed:
    1. Choose a transaction that will be rolled rollback, this can depends on how long a
       transaction has been running, how much data it has and needs, and how many other
       transacitons will be rolled back in cascade
    2. Rollback the transaction
    3. Repeat until no deadlock
* Instead of requesting locks on individual data items, we can request locks on a group 
    of data items based on some tree-like hierarchy.
* Similarly, we lock the entire table or database by requesting a lock on the root, and 
    this implicitly locks all data in the table or database.
