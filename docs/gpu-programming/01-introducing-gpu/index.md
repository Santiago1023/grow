# 1. GPU Programming Introduction

In this course I will write about GPU programming techiniques that profesor Esteban teach me in the classroom. 
In particular I am so interested in learn CUDA, to know how take advantage of the use efficiently of computer resources, for me is very interesting try to understand the way in which CPU and GPUs works in the industry, how companies are solving complex computive tasks by parallelizing their codes to quickly obtain results.

Other important thing for me is that I want to found the relation that I can make with the use of CUDA and Kubernetes, how to combine these two technologies to build applications, or projects really interesting that allow me prepare and gain skills to look for a job related with cloud native, gpu's and architectures topics.

I have seen in these recent years the use of kubernetes as orchestration techology to allow us to have resilient, self-healing, and scalable applications, and CUDA as a tool to exploit the parallelization paradigm in training of AI models, these both combined can built and support the future of AI running on kubernetes.


Session by session I want write things that I learn in order to improve my documentation skills, and looking for coworkers or collegues that are interested in these same topics, and learn together, and of course that this show some kind of my learning process to future recluters and see the differents things that I like.


- Parallel computing fundamentals and its rules
- Programming model for GPUs (CUDA)
- Performance analysis models for parallel programs
- Applications of GPU programming

```cuda title="vector_add.cu" linenums="1" hl_lines="2"
__global__ void add(int *a, int *b, int *c) {
    int i = threadIdx.x;
    c[i] = a[i] + b[i];
}
```

## Introduction to parallel (and distributed) computing

Why is important parallelism?

For me is important because there are many applications, functions, or algorithms that run in a sequential way and this consume a lot of hardware resources, and also takes a lot of time, making the energy efficiency of the machines, and the performance of the application a real target for bottlenecks.

So, for this reason became important try to thinking in different ways of take advantages of new paradigms, and new architecture devices such as GPUs, to execute multiple task at the same time in parallel in order to reduce the time computation.

There are two ways of parallelism:

1) task parallelism -> you distribute the tasks concurrently across different processors that are executed through processes or threads.
2) data parallelism -> concurrent execution of a task on different data, you can chunk or split your data, and send it for different process unit.

Distributed computing is basically many machines working together, communicating by networks

Flynn's Taxonomy

Single Instruction, single data stream (SISD) -> mono-core/uniprocessor:
    - single processor executes a single instruction

Single Instruction, multiple data stream (SIMD) -> vector processor or array processor (you can exploit data parallelism):
    - each processing element (PE) has an associated data memory, so that each instruction operates different sets of data by different processors

Multiple Instruction, single data stream (MISD) -> (Not so used)

Multiple Instruction, multiple data stream (MIMD): Most gpus works here:
    Execute different instruction sequences on different data sets.
    - shared memory (tightly coupled) -> Symmetric multiprocessor (SMP) or Nonuniform memory access (NUMA)
    Shared Memory (SM) systems: multiple CPUs or SMPs share the same address space (there is only one memory)
    - distributed memory (loosely coupled) -> cluster
    Distributed Memory (DM) systems: each CPU/SMP has its own associated memory


## CUDA (Compute Unified Device Architecture)
CUDA is a parallel computing platform and programming model that enables developers to use the power of NVIDIA GPUs for general-purpose computing.
CUDA supports the development of Parallel programs (kernels)


## Contents:

- Introduction to parallel computing
- GPU concurrency and organization
- GPU programming fundamentals

> 🚧 Coming soon

**📅 Session:** YYYY-MM-DD · **🎥 Recording:** [YouTube - Cloud Native Colombia](https://www.youtube.com/@CloudNativeColombia) · **📖 Book:** Chapter 1

## 🎯 What we covered

What the exam contains, architecture of Kubernetes.

## 🧪 Exercises

- [CKA Exercises in Killercoda](https://killercoda.com/chadmcrowell/course/cka)

## 💡 Useful commands

```bash
# kubectl ...
```

## 🧠 Reflection

### ✅ What I learned

### ❓ What was challenging / open questions
