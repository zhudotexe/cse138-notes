Safety & Liveness
=================

Previously:

- FIFO delivery
- Causal delivery
- Totally ordered delivery

These are all safety properties: properties that say a "bad" thing *won't* happen; on the other hand, a *liveness*
property says that a "good" thing *will* happen (e.g. eventual delivery).

.. note::
    The word *eventually* is often an indicator of a liveness property.

Now, formally:

.. data:: safety property

    - say that a "bad" thing won't happen
    - can be violated in a finite execution (can point to where it went wrong)

.. data:: liveness property

    - says that a "good" thing will (eventually) happen
    - cannot be violated in a finite execution (may take infinite time to satisfy)

It's the combination of safety and liveness that makes systems useful - for example, otherwise, you could just
drop every message to satisfy FIFO.

In fact, every property is either a safety property, a liveness property, or both.

Let's try and define reliable delivery:

(Take 1): Let :math:`P_1` be a process that sends a message to process :math:`P_2`. If neither :math:`P_1` not
:math:`P_2` crashes, then :math:`P_2` eventually delivers *m*.

Well, it's a start, but the whole "not crashing" thing is weak.

Fault Models
------------
A *fault model* tells you which kind of faults can occur. In the example system of two processes, one asking the
other for a value, we have the following faults:

- message from :math:`M_1` is lost - omission fault
- message from :math:`M_1` is slow - timing fault
- :math:`M_2` crashed - crash fault
- :math:`M_2` is slow - timing fault
- message from :math:`M_2` is slow - timing fault
- message from :math:`M_2` is lost - omission fault
- :math:`M_2` lies - byzantine fault

.. data:: crash fault

    a process fails by halting (stops sending/receiving messages) and not everyone necessarily knows about it

    (There is actually a subset, called "fail-stop faults", where a process halts and everyone *knows* it halts)

.. data:: omission fault

    a message is lost (a process fails to send/receive a single message)

.. data:: timing fault

    a process responds too late (or too early)

.. data:: Byzantine fault

    a process behaves in an arbitrary or malicious way

If protocol X tolerates crash faults and protocol Y tolerates omission faults, does Y also tolerate crash faults?

Yes! Crash faults are just a special case of omission faults where *all* messages to/from a process are lost.

In fact, Byzantine faults are also a superset of crash (and omission) faults.

.. image:: _static/safety1.png
    :width: 400

.. note::
    We left out timing faults in the above diagram, since we're dealing only in async in this class.

.. data:: fault model

    A *fault model* is a specification that says what kind of faults a system can exhibit, and this tells you what
    kinds of faults need to be tolerated.

In this class, we focus on the *omission model* (which includes the *crash model*).

Two Generals Problem
--------------------
*Tom Scott: https://www.youtube.com/watch?v=IP-rGJKSZ3s*

.. image:: _static/safety2.png
    :width: 500

In the omission model, it is impossible for Alice and Bob to attack and know for sure that the other will attack.

How do we mitigate?

Probabilistic Certainty
^^^^^^^^^^^^^^^^^^^^^^^
One option is to have Alice constantly send until she receives an ACK; then the longer Bob
goes without receiving a message, the more sure he is that she has received the ACK. Note that it's not 100%
guaranteed; every single message since then could have failed, but it works.

Common Knowledge
^^^^^^^^^^^^^^^^
There is common knowledge of *p* when everyone knows *p*, everyone knows that everyone knows *p*, everyone knows that
everyone knows that everyone knows *p*...
