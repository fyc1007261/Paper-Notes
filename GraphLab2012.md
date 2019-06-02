# Distributed GraphLab

#### Introduction

- A high-level abstraction that targets the **asynchronous, dynamic, graph-parallel** computation.
- MLDM (Machine learning and data mining) algorithm properties
  - **Graph structured computation**: dependencies between data
  - **Asynchronous iterative computations**: faster than synchronous ones, whose time is determined by the slowest process. Spark deals with iterative while still not asynchronous.
  - **Dynamic computation**: Often a large number of parameters converge quickly while the rest converge slowly. Therefore, we need to **update parameters not equally often**.
  - **Serializability**: to ensure correctness when concurrency is involved.

