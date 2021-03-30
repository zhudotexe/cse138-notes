Introduction
============

> What is a distributed system?

.. data:: distributed system

    A system running on several nodes connected by a network, characterized by partial failure.

Failures
--------
There are two philosophies when dealing with failure:

- High-Performance Computing (HPC) philosophy
    - treat partial failure as total failure
    - checkpointing
- "cloud computing" philosophy
    - treat partial failure as expected
    - work around the failure
    - "everything fails, all the time"

Consider a simple system of two machines, M1 and M2. Suppose M1 asks M2 for the value of some variable *x*.

.. code-block:: text

    M1 -- x? --> M2
       <-- 5 ---

Some potential failures are:

- request from M1 is lost
- request from M1 is *slow*
- M2 crashes
- M2 is slow to respond
- response from M2 is slow or lost
- Byzantine faults - corrupt transmission/malicious response/etc

.. important::
    If you send a request to another node and don't receive a response, it is impossible to know why.

Timeouts
--------

Real systems try to cope with this using *timeouts*: after a certain amount of time, assume failure and try again.
However, this isn't a perfect system - for example, consider if M1 told M2 to increment *x*:

.. code-block:: text

    M1 -- x++ --> M2 (x=5)
                  x++
    M1  X<-- 6 -- x=6

If we knew the max delay *d* and processing time *r*, we could set the timeout to :math:`2d+r` and rule out a lot
of uncertainty. However, you can't get an accurate value for *d* and *r* in real life usually, just statistics.
Therefore, distributed systems have to account for both *partial failure* and *unbounded latency*.

Why?
----
So why do we make systems distributed and deal with all of this?

- make things faster
- more data than can fit on one machine
- reliability (more copies of data)
- throughput (data is physically closer)
