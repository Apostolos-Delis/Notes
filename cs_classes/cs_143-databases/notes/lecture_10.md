# Lecture 10 - Hashing and Other Indices- 5/8/19

**Note**: Can create an index in SQL with the following syntax:

```sql
CREATE INDEX highway_lookup ON caltrans
[ USING (btree | hash | gist | spgist | gin | brin) ]
(
    attribute1 [ASC | DESC] [NULLS {FIRST | LAST }],
    ...
);
```

## Hash Indices

* Hash indices are widely used for building indices in main
    memory/RAM but can also live on disks.
* They can permanently reside in RAM or on disk (for fast lookups),
    or be computed on the fly during a join.
* Hash indices are great at answering equality queries, not range queries
* We define a bucket as a container that houses one or more records.
    The bucket could link records together using a linked list of index
    entries (multilevel) or one of records. We will let B be the set of
    buckets.
* If the index is on disk, a bucket would be linked list of blocks
* Let K be the set of all search-key values. and let h be a hash
    function $h : K \rightarrow B$. That is, $h$ maps a key $K_i$ to a particular
    bucket $b$.
* To insert a new record into the index, we compute the hash function
    on the key $h(k_i)$ which gives a bucket address for the record. Then
    we add an index entry for the record to the list at offset i.
* To perform a lookup on $k_i$, we compute $h(k_i)$ then iterate through
    the bucket to process the records.
* It is possible that ${\exists i,j, i \ne j : h(k_i) = h(k_j)}$
* So the bucket $h(k_i)$ contains records with the key $k_i$ and $k_j$ so we have to
    search for records containing the proper key
* Deletions can be done by computing $h(k_i)$ and find all the records that have $k_i$
    and delete them from the linked list
* If the hash index is on disk, during insertion is done by locating the bucket and if
    there is space in the bucket, the record is stored, otherwise there is a bucket
    overflow so an overflow bucket is constructed for that record and subsequent records
* Can't store everything in one bucket because of block I/O, which means we want
    efficient storing of records
* Bucket overflow
    * **Insufficient Buckets**: for the number of records we want to index
    * **Bucket Skew**: Some buckets contain a lot of records and a
        lot of overflow buckets, whereas the rest do not have any
        overflow buckets. This is caused by non-uniform hash function or imbalanced data
* We want to minimize the probability of bucket overflow. We generally choose
    $\frac{n_{r}}{f}(1+d)$ where $n_r$ is the number of records, $f$ is the number of
    records per bucket and $d$ is a fudge faster (usually 0.2 so 20% of buckets are empty)
* We may waste some space, but we get quicker performance because we won’t have to
    iterate a linked list of overflow buckets.
* When we allocate the number of buckets a priori, this is called **static hashing**.
* To avoid bucket overflows, can do one of the following
    1. Choose a hash function based on the current file size. This option will result in
        performance degradation as the database grows.
    2. Choose a hash function based on the anticipated size of the
        file at some point in the future. Although performance
        degradation is avoided, a significant amount of space may be
        wasted initially.
    3. Periodically reorganize the hash structure in response to file
        growth: new hash function, recomputing hash values on every
        record, generating new bucket assignments. **THIS IS SLOW**
* Instead, we can grow or shrink the number of buckets we use incrementally, called
    dynamic hashing
    * linear hashing
    * extendible hashing

#### Extendible Hashing

* Under extendible hashing, the buckets replicate and coalesce as the
    database grows and shrinks.
* This allows for space efficiency while retaining performance* This allows for space
    efficiency while retaining performance
* Under extendible hashing, we use a hash function that is random
    and uniform, but generates values over a very large range
* Buckets are not preallocated, they are allocated on the fly. At any time, we only use
    i of the b bits, where $i \leq b$ initially. We treat this as a prefix for any future
    higher order buckets that are created. i changes as the database grows and shrinks.

## Indexing Other Content Types

* Sequential indexing, B+ trees and hash indices are the most common indexing methods.
* The other realistic data types you may encounter are spatial, temporal, and streaming
* You can index spatial data which will construct a system that lets you extract points
    that are within a certain distance of the user, usually this is a circle and is
    called a **nearest neighbor query**
* In the 2-3D plane, we use a special tree called a k-d tree where each boundary line
    represents one node in a k-d tree
* To index temporal data, a lot of it is borrowing over from spacial data in 1D

#### Straming Data

* Many times we only want to know if a particular key exists in a set,
    it without retrieving the records. If the set is extremely large and does not
    fit in RAM and would take forever to distribute in a network.
* If we have to stream the data across the slow network, faster throughput can be
    acheived by reducing space complexity
* Given some key $k$, we can query the Bloom Filter to determine
    whether or not $k$ is in the set. The Bloom Filter may respond yes
    or no.
* We can trust an answer of no from the Bloom Filter. That is, there are no false
    negatives.
* A Bloom Filter is a bit array of $m$ bits, initialized to 0. We have a set of hash
    functions $F$ where each hash function $f \in F$ hashes each input k to one of the
    $m$ bits. Assuming $f(k)$ is uniformly distributed. Usually the number of elements in
    F is way less than $m$
* To insert into the filter, computer $f(k)$ for all $f \in F$ to get a series of $|F|$
    bit positions. We set each of these bits to 1. This is $\mathcal{O}(|F|)$
* Deleting is not possible because it may lead to conflicts with other search keys which
    violates the perfect false negative
* A Bloom Filter is a class of data structures called probabilistic
    data structures. They do not always yield precise responses to
    queries, but are very important to streaming systems without access to past data
