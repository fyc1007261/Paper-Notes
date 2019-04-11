# Zookeeper: Wait-free coordination for Internet-scale systems

### Introduction

- A service for coordinating processes of distributed applications.
- Aims to provide a simple and high performance kernel for building more complex coordination primitives at the client.
- Wait-free and FIFO per client.
- Allow application developers to design new primitives.

### Service Overview

- Provides the abstraction of a set of data nodes (*znodes*).

  - Use the similar hierarchy namespace to file systems.
  - Two types of *znodes*:
    - Regular: Clients manipulate regular *znodes* by creating and deleting them explicitly.
    - Ephemeral: Clients explicitly create these nodes, and then delete them either explicitly, or let the system delete them automatically when the session that creates the *znodes* terminates.
  - Clients can set a sequential flag (auto increasing)
  - *Znodes* are not for general data storage. Instead, they map to abstraction of the client application. (Though they do store some data sometimes) 

- ZooKeeper guarantees:

  - **Linearizable writes**: all requests that update the state of ZooKeeper are serializable and respect precedence.
  - **FIFO client order**: all requests from a given client are executed in the order that they were sent by the client.

  - The system needs to make sure the atomicity of configuration modification when a new leader comes into power
    - Other processes can access the configurations only when the file *ready* exists.
    - The leader deletes *ready*, change the configuration and then creates *ready*. 
    - Special case 1: a process sees that *ready* exists before the new leader starts to make a change. Ordering guarantee for the notifications is involved. The client will be notified before it can read any of the new configuration.
    - Special case 2: Clients may share other communication channels other than ZooKeeper. Use `sync` operation, which causes a server to apply all pending writes before processing the read without the overhead of a full write.