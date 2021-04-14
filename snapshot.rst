Snapshots of Distributed Systems
================================
Processes have individual *state*, which is pretty much all the events that have happened up to a given point.

How do we get the state of an entire distributed system (a "global snapshot") at a given time?

We can't use time-of-day clocks, since they aren't guaranteed to be synchronized across all processes.

.. image:: _static/snapshot1.png
    :width: 250

**Property that we want**: If we have events *A* and *B* where :math:`A \to B`, and *B* is in the snapshot, then
*A* is also in the snapshot.

Chandy-Lamport Algorithm
------------------------

.. data:: channel

    connection from one process to another, with FIFO ordering
    (e.g. :math:`C_{12}`: the channel from :math:`P_1` to :math:`P_2`)

    .. image:: _static/snapshot2.png
        :width: 250

    :math:`m_2` is *in* :math:`C_{21}`, :math:`C_{21}=[m_2, m_3]`

Some examples of snapshots:

.. image:: _static/snapshot3.png
    :width: 750