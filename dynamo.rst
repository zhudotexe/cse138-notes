Dynamo
======
This section of notes discusses concepts found in the Dynamo paper, at
http://composition.al/CSE138-2021-03/readings/dynamo.pdf.

Concepts
--------

- availability
    - "every request will receive a response" (quickly? 99.9% of requests?)
- network partitions
    - some machine(s) can't talk to other(s)
    - temporary and unintentional
- eventual consistency
    - a liveness property
    - "replicas eventually agree if updates stop arriving"
    - Doug Terry, Bayou, 1995
- application-specific conflict resolution
    - the client can resolve conflicting states (e.g. merging shopping carts)
    - dynamo: deleted items can reappear in a cart (bug)
    - dynamo: if client has no specific conflict resolution, last write wins (LWW)

Consider the problem of adding items to a shopping cart: writes commute, which implies strong convergence (replicas that
deliver the same set of requests have the same state).

Disagreements
-------------
Dealing with replicas that disagree: the first step is to find out that some replicas disagree.

Dynamo has 2 concepts to deal with disagreements: anti-entropy and gossip.

.. data:: anti-entropy

    resolving conflicts in application state (a KVS)

.. data:: gossip

    resolving conflicts in view state / group membership (what nodes are alive?)

.. note::
    In general, anti-entropy and gossip are synonymous, but they have differing meanings in Dynamo.

Merkle Trees
------------
In Dynamo, KVS states are *large*, but the view state is generally small. To compare KVS states without sending the
entire state, Dynamo uses *Merkle trees* (hash trees) - each leaf is a hash of a KVS pair, and each parent is a hash
of its children. Two replicas can then compare their root node - if it's equal, all leaves must be equal; otherwise,
compare the direct children and so on.

.. image:: _static/dynamo1.png
    :width: 500

Tail Latency
------------
Recall: latency = time between the start and end of a single action

.. image:: _static/dynamo2.png
    :width: 500

But the top one is worse than the bottom! Tail latency examines the latency of the worst requests (e.g. the 99.9th
percentile).

.. image:: _static/dynamo3.png
    :width: 350

Dynamo has very good tail latency.
