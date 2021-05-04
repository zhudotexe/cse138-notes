Consensus
=========
Consensus is *hard*.

WHen do you need it? When you have a bunch of processes, and...

- they need to deliver the same messages, in the same order (**totally-ordered/atomic broadcast**)
- they need to each know what other processes exist, and keep those lists up to date
  (**group membership problem, failure detection**)
- one of them needs to play a particular specific role and the others need to agree who that is (**leader election**)
- they need to be able to take turns accessing a resource that only one can access at a time
  (**mutual exclusion problem**)
- they're participating in a transaction and need to agree on a commit/abort decision
  (**distributed transaction commit**)