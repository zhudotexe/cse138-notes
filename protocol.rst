Protocol
========

.. data:: protocol

    A set of rules that processes use to communicate with each other.

e.g. "At any point a machine can wake up and send 'hi, how are you?', and the receiver must reply with
'good, thanks'."

.. image:: _static/time12.png
    :width: 500

These are all Lamport diagrams of valid runs of the protocol - even though the third doesn't have a response yet,
we may have just caught it before it has had a chance to reply.

There are in fact *infinite* diagrams representing a valid run of the protocol! We can also draw diagrams representing
a violation of the protocol:

.. image:: _static/time13.png
    :width: 200

The following are three different correctness properties of executions:

FIFO Delivery
-------------
if a process sends message :math:`m_2` after message :math:`m_1`, any process *delivering* both
delivers :math:`m_1` first.

.. note::
    - *Sending* a message is something you do
    - *Receiving* a message is something that happens to you
    - *Delivering* a message is something you *can* do with a message you receive (you can queue up receives and wait
      to deliver them)

.. image:: _static/time14.png
    :width: 400

Most systems programmers don't have to worry about this often - it's already part of TCP!

Sequence Numbers
^^^^^^^^^^^^^^^^
This can be implemented using *sequence numbers*:

- messages are tagged with the sender ID, and sender sequence number
- senders increment their sequence number after sending
- if a received message's sequence number is (previously received seq num) + 1, deliver it

However, this only works well if you also have *reliable delivery* - if not, and the receiver misses one, it will
buffer all future messages forever.

There are some solutions to this - like ignoring the missed message and delivering all buffered ones - but if the
missed one shows up later, it has to be dropped.

Also, just dropping every single message satisfies FIFO vacuously.

ACK
^^^
Another implementation is ACK - Alice waits for Bob to send an acknowledgement before sending the next message.

.. image:: _static/time15.png
    :width: 250

However, it's a lot slower since it requires a full round trip per message.

Causal Delivery
---------------
If :math:`m_1`'s send happened before :math:`m_2`'s send, then :math:`m_1`'s delivery must happen before :math:`m_2`'s
delivery.

The violation of FIFO delivery above is also a violation of causal delivery:

.. image:: _static/time14.png
    :width: 250

Note that however, this diagram does not violate FIFO delivery, since FIFO delivery only accounts for messages
sent from a single process.

.. image:: _static/protocol1.png
    :width: 250

It is a violation of *causal delivery*, though.

Totally Ordered Delivery
------------------------
If a process delivers :math:`m_1` then :math:`m_2`, then *all* processes delivering both :math:`m_1` and :math:`m_2`
deliver :math:`m_1` first.

The image below is a violation, since P2 delivers 1 then 2, but P3 delivers 2 then 1.

.. image:: _static/protocol2.png
    :width: 500
