---
title: "Questions"
teaching: 10
exercises: 5
questions:
- "How do we execute a task in parallel?"
objectives:
- "Understand how to run a parallel job on a cluster."
keypoints:
- "Parallelism is an important feature of HPC clusters."
- "MPI parallelism is a common case."
- "The queuing system facilitates executing parallel tasks."
---

We now have the tools we need to run a multi-processor job. This
is a very important aspect of HPC systems, as parallelism is 
one of the primary tools we have to improve the performance of
computationnal tasks.

## Running the Parallel Job

We will run an example that uses the Message Passing Interface (MPI)
for parallelism -- this is a common tool on HPC systems.


Is it what you expected? How good is the value for pi?
