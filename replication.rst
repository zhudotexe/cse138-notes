Replication
===========
Why do we replicate state/data?

- fault tolerance: prevent data loss
- data locality (keep data close to clients that need it)
- dividing the work

But:

- higher cost
- hard to keep state consistent

Informally, a replicated storage system is *strongly consistent* when you cannot tell that the data is replicated.

Primary-Backup Replication
--------------------------
First, pick a process to be the primary. All other processes are then backups. In this scenario, clients
can only connect to the primary

When a client makes a write to the primary, the primary sends that write to all backups - when it then receives
an ACK from all backups, that is the *commit point* and it returns OK to the client.

.. image:: _static/replication1.png
    :width: 500

On a read, the primary just returns the value to the client without consulting with any backups.

.. image:: _static/replication2.png
    :width: 150

.. note::
    The client must ask the *primary* instead of a backup because there might be an uncommitted write in flight -
    a backup might know it, but the primary might not have committed it yet.

How well does it match our replication criteria?

- [x] fault tolerance
- [ ] data locality
- [ ] dividing work

Chain Replication
-----------------
*Chain Replication for Supporting High Throughput and Availability* - van Renesse, Schneider, 2004

.. image:: _static/replication3.png
    :width: 500

Writes go to the head, which forwards to the next one in the chain, and so on to the tail. The tail ACKs the write.

Reads go to the tail.

Well, how well does it do?

- [x] fault tolerance
- [~] data locality
- [x] dividing work
    - slightly better - reads and writes go to different processes

.. data:: throughput

    number of actions per unit of time

Depending on the workload, CR could give you better throughput than PB.

.. image:: _static/replication4.png
    :width: 500

For CR, the optimal workload ratio is about 15% writes to 85% reads.

So what's the downside of CR? Well, CR has a **higher write latency** (depending on # of nodes in chain).
(the two have about the same read latency)

.. data:: latency

    time between start and end of one action

Since PB broadcasts the write, it's processed in parallel by the backups, and it can be ACKed as soon as all backups
ACK. For CR, the write message is forwarded, and has to be processed by each process in the chain in series.

.. note::
    Regardless of which replication scheme you choose, the client and replicas have to agree on who's the primary/
    head/tail/etc, or else you lose the guarantees of replication!
