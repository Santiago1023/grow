# SIMD programming and framework setup

The goals of this laboratory is:
- practice and gain experience in the programming of SIMD vector coprocessors in modern CPUs
- setting up the Nvidia Nano GPU platform and the remote access to a GPU

## Exercise 1: Programming with SIMD coprocessors in CPUs

There are some modern CPUs that support vector coprocessors, that is also a way of you can exploit parallelization with the use of the SIMD paradigms, a single instruction can execute different parts of the data.

We will use the Intel's SSE extensions to develop two exercises code.
- SAXPY (Single Precission A*X Plus Y) application that computes Y = aX + Y, where a is a constant, and X and Y are vectors.

- Dot Product for angle calculation computes the dot product of two vectors to determine the angle between them
A*B = |A||B|cos(tetha)
tetha = arccos(AB / |A||B|)

a) Describe in C/C++ a program to compute SAXPY and Dot Product. The program must load from file the input vector sizes ranging from 4 to 40.000 elements. Employ a timing library (<sys/time.h> or <chronos>) to compute the execution of the program. Compares the difference between the measurements.

b) Describe in C/C++ a program to compute SAXPY and Dot Product. Take advantage of use SIMD Extenssions (SSE) or the AVX(CPU Adavanced Vector Extensions). The program must load from file the input vector sizes ranging from 4 to 40.000 elements. Employ a timing library (<sys/time.h> or <chronos>) to compute the execution of the program. Compares the difference between the measurements.

Compare the results obtained in a and b, and analyze their cost in terms of latency when the input vector sizes vary.
