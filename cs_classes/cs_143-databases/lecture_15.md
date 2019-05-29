# Lecture 15 - Stream Processing 2019-05-29

## Stream Processing

* All the systems for data seen are materialized, so they either store all the data or
    they have access to all the data
* Map-Reduce and ETL jobs read in a set of input files and produce a new set of output
    files. This data may then be written to RDBMS or Data Warehouses.
* The data is of a known and finite size. It’s bounded. We know what the first record
    is, and what the last record is. It is a countable set.
* However, this is not always the case, a lot of the data is unbounded, that is, it
    arrives as it is produced, the dataset is never complete
* We can process data in windows of time such as a day or hour, etc
* At the beginning of the window, we clear a buffer and start collecting data, when the
    window expires, the daa is processed and perhaps written to the RDBMS
* The problem with this approach is what happens if we need to do analysis across
    windows? The data isn't available until the window closes
* **Data Streams**
    * In RDBMS and batch processing, the input is usually a bunch of files that are then
        divided into records before the processing occurs
    * In stream processing, it’s usually individual records, called events and each
        event is partially identified with a timestamp.
* In the streaming system, there are generally two ends:
    1. **Publisher**: the node or process that generates the event
    2. **Subscriber**: consumer (or recipient): a node or process that receives events
        and does something with them.
* The system works by the publisher continuously generating events into the RDBMS while
    the subscriber constantly polls the RDBMS for new events
* Polling is expensive, so we want the system to "push" notifications to nodes, this can
    be done by triggers, but this is limited
* The other option is push notifications, where in between the publisher(s) and
    subscriber(s) lies a message bus, some connection between them.
* Unlike Unix pipes, a message bus in a streaming system has multiple publishers and
    multiple subscribers.
* Unlike batch processing that has a strict data reliability gaurantee, streaming systems
    are best effort, so if producers send messages faster than consumers can process
    them, it then becomes implementation specific
* When we want super low latency and are OK with losing some messages in the process, we
    can use UDP and encode reliability at the application layer.
* If we are can tolerate higher latency and require perfect reliability, use TCP.
* Instead, we can have a special database server called a "message broker" that sits in
    between the publishers and subscribers.
    * Here, publishers write messages to the broker, while subscribers read messages to
        the broker
* The broker can handle the case where nodes come and go. Durability is now handled by
    the broker rather than each node.
* Differences between a message broker and RDBMS
    1. RDBMS keeps data until it is deleted by the user. Message brokers delete data
        once it is delivered to the ubscribers. Not good for long term storage
    2. Low throughput. If there is a bottleneck in subscribers, performance degrades.
    3. RDBMS supports indexes for finding data. While brokers support some way of
        extracting particular topics, it is not as sophisticated as index
    4. RDBMS queries are based on snapshots of data. Brokers do not accept arbitrary
        queries, but they DO notify subscribers when data changes.
* If there are multiple subscribers, how does the broker determine which one gets the
    message?
    1. **Load Balancing**: the broker chooses one subscriber to receive the message
        either arbitrarily or basd on some shard or partition key.
    2. **Fan Out**: the broker delivers the message to ALL subscribers in particular
        groups. Then, each node may do something different with the message. (lambda
        architecture)
* Once the subscriber is done processing a message, it sends an ACK back to the broker
    to acknowledge receipt and processing of the message.
* Since the ACK may be lost in the network, the message broker uses a two-phase commit
    protocol (2PC) to manage the situation
* The transaction only commits once the ACK has been recieved by the broker

#### Load Balancing + Retransmits = Out of Order

* Usually, a message is processed in the same order as it was delivered by the
    publishers, but we can have a situation where messaes are processed out of order when
    using redelivery with load balancing
* Once the broker receives an ACK from the subscribers, the message is deleted from the
    broker.
* Brokers can instead use recovery logs to maintain order. In this case, a publisher
    sends a message to a broker and the broker appends the message to the end of a
    log.
* The subscriber then reads the log sequentially (`tail -f`). Within each topic, the logs
    can be partitioned to increase parallelism
* Within each partition, the broker maintains an integer offset for each message in the
    log. The subscriber and the broker also maintains an offset denoting the last message
    that was processed.
* Disk I/O is slow, but by partitioning these logs over many disks, we can allow fast
    throughput.
* Typically, the broker will assign an entire partition to a single subscriber and then
    that subscriber reads in the messages from the log in a single-threaded manner.
* Under the log-based model, the broker does not need to keep track of ACKs. Both the
    subscribers and the broker are synchronized via the offsets.
* To prevent exhausting disk space, the logs are divided into segments and after a period
    of time, segments are deleted from disk.
* If a subscriber falls so far behind the publishers that the next offset is part of the
    log that was deleted, we lose messages.
* The main purpose of log-based message brokers is to solve problems that occur when
    subscribers disappear and there is still work to be done, and to alleviate issues
    involving messages being processed out of order.
* In a mature software company, there are multiple copies of the data in many different
    types of databases, and these all need to be synchronized; this can be done with a
    streaming system
* To search a stream, we instead specify the search query a priori. We will miss all
    past events that match the query, but we will be able to percolate up any events that
    happen after the query is issued.
