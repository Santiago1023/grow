# Exploring the memory hierarchy in GPUs to boost performance in Parallel kernels

## Exercise 1 - 1D Stencil

Stencils are fundamental operator processing simple (max, min, avg) or to elaborate (convolution, filters) from information that is organized in a consecutive manner.

Consider an accumulative stencil and its execution as a 1D stencil on a 1D array of elemnts.

Each orange element is  the sum of the input elements (green and orange) within a given radius.

GPUs and other Domain Specific Accelerators (DSAs) can implement a stencil operation by asssigning a thread for the computation of every output element.


- Trying to understand the problem, I understand that the program will give me a input vector with information that I will use to compute an output vector with the results of the operations.
- I can define the number of thread that operates over the input vector knowing the size of the input vector, and know the window that I will use, for example if I have an input vector of 10 elements, and a radius of 2, so I must keep in mind the threads that will not be executed, for example the first elements of the input maybe could launch an error.

0,1,2,3,4,5,6,7,8,9

In this case a window will be of 5 elements in total, because I take the left and right neighbors (2+2) and in the middle (+1). So the constraint is start in the element 2, index 2 of the vector. 