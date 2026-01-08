# Chapter 7: Transactions

## Introduction
Put simply, a **transaction** is a collection of actions to be performed on a database and the state of the database is either the state it was before the transaction XOR the state of the database after the transaction. 
Furthermore, this means that if some type of interruption were to happen in the middle of the collective actions of the transaction (database/application code error, network error), then all of the actions will be aborted. Aka, **all or nothing.**

This sounds like a great practice to have in all applications, but just like anything in distributed systems, "transactions have advantages and limitations" (223).

A big advantage of transactions are the safety guarantees represented by the acronym: **ACID**.


## ACID
*ACID* is an acronym created by “Theo Harder and Andreas Reuter in 1983 in an effort to establish precise terminology for fault-tolerant mechanisms in databases” (223). ACID stands for:

- A - Atomocity
- C - Consistency
- I - Isolaton
- D - Durability

Now getting into what each of these mean.

### Atomocity
This relates to the idea of “all or nothing”. More specifically, a thread cannot see the half result of some type of operation. Put formally, “The system can only be in the state it was before the operation or after the operation, not something in between” (224).

### Consistency
In the context of ACID, consistency is a collection of certain statements about the data (invariants) that must always be true (225). The example that Kleppmann uses is, “in an accounting system,credits and debits across all accounts must always be balanced” (225). 

An interesting part of consistency is that it is up to the engineer’s code to handle violations of invariants rather than the database’s. Moreover, consistency relies on the code of application and while the code can rely on the database’s properties of atomicity and isolation (discussed below), it ultimately falls to the developer. Therefore, Kleppmann believes that consistency does not belong to acid as they are supposed to be properties related to the database.

### Isolation
Considering the fact that databases are being accessed by multiple users at different/similar times, there poses the problem of concurrent transactions. When performing transactions, there need to be rules in place that keep them from stepping on each others’ toes (225). More specifically, concurrency issues such as race conditions involve scenarios where two different operations operate on the same piece of data and incorrect outputs can occur. 
For example, say there is a count = 1 in the database and user1 and user2 plan to increment it. The two users could end up reading “count” from the database before either user incremented the count so the final output ends up being 2 instead of 3. 

According to Kleppmann: “The classic database textbooks will formalize isolation as serializability, which means that each transaction can pretend that it is the only transaction running on the entire database” (225).

### Durability

This part of ACID ensures that once a transaction is committed, any data written will not be lost even if there is an error such as a hardware fault or database crashes. This can be done a number of ways I am guessing, but a method that Kleppmann mentions is data being written to non-volatile storage such as a hard drive or SSD using operations like write-ahead logs, which are talked about on page 82 (Kleppmann 226).

## Single-Object and Multi-Object Operations
*Multi-object* operations involve the process of updating/modifying multiple objects (rows, documents, records) at once while *single-object* operations involve as you would guess, singular operations such as writing a 20 KB JSON document to a database.

The whole idea of atomicity and isolation is easily applicable to single-object operations because either the whole 20 KB JSON document is written or it is not. If there is a network error in the middle of writing, it is not written. Additionally, updating a singular row or writing a 20 KB JSON document is isolated from other operations.

Multi-object operations seem very useful and helpful with consistency, but it can get in the way of availability, performance and it involves a lot of overhead to implement across partitions.

Overall, multi-object vs. singular-object mainly has to do with the idea of scope of the different operations.

## Weak Isolation Levels

For context, concurrency is the ability to handle multiple tasks at once but not execute on the task at the same time (parallelism). As mentioned earlier, race conditions are one of the problems faced when performing concurrency. In the context of software development, debugging concurrency issues can become difficult because it is hard to tell which parts of the application code are accessing the database (233). 
<br></br>
To offload some of this complexity, databases make it a goal to provide **transaction isolation** which allows the developer to PRETEND that no concurrency is happening. Transaction isolation does this by providing **serializable isolation**, which is the database guaranteeing the same effect as if they were to be run serially: one at a time, without any concurrency (233). But in some situations, concurrency is occurring and it is more an abstraction.

However, serializability has performance cost and thus databases use “weaker levels of isolation”. 
