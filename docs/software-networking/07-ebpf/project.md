# eBPF and io_uring Project

---

In this section I document, step by step, the complete process of taking on a project (proposed by my professors in the Software Networking course at Politecnico di Torino) without any previous knowledge of the technology, while I learn about eBPF.


---
!!! abstract "Understanding the New eBPF and io_uring Integration for In-Kernel I/O Offload"
    **Context**

    eBPF and io_uring are two of the most influential additions to the Linux kernel for networking.

    - eBPF marks a generational shift, making the kernel programmable and allowing custom network functions to be offloaded and run directly in kernel space.

    - io_uring rethinks asynchronous I/O, offering better performance and a more usable interface than traditional APIs.

    **Problem**

    Although the two technologies are closely related in spirit, they have so far developed mostly independently, with few opportunities to combine them. Linux 7.1 changes this by introducing two eBPF kfuncs that bridge the gap and open the door to new use cases.

    **Purpose**

    - The student will first build a solid understanding of both eBPF and io_uring and of the role each plays in modern high-performance software. Building on this foundation, the student will study the new kfuncs in depth and develop sample applications that demonstrate the capabilities they unlock, such as generating and terminating traffic directly from eBPF or accessing the file system from eBPF programs. 

    - The project will possibly culminate in one or more higher-level applications that showcase the integration end to end, for example a web server that runs entirely within the kernel.

    **Student:** Santiago Bedoya Diaz. **Tutors:** Davide Miola, Fulvio Risso

---
## Why I chose this project

This project is really special to me. I have been thinking about how fear stops us from making decisions and taking risks with projects, opportunities, and things that could change our lives. I chose this project for many reasons:

- I want to see how far I can go when I am fully focused and ready to get involved and dive deep into hard topics.

- I want to start something and see it through to the end, to have the satisfaction and the experience of taking a technology I know nothing about, and being able to learn it, understand it, and speak about it with confidence and authority.

- I would like to do my thesis with Professor Fulvio.

- I am one of the CNCF Medellin group leaders, and I want to be involved in Cloud Native and CNCF topics, technologies and projects, to teach and talk with community members about them.

I am starting this project from scratch, so let's see where I am in a few months! :)

---

## Roadmap

- [ ] eBPF basics
    - [ ] What is eBPF and how it runs in the kernel
    - [ ] Write and load a first eBPF program
- [ ] io_uring basics
- [ ] The new kfuncs (Linux 7.1)
- [ ] Sample applications
- [ ] In-kernel web server

## Progress log

### 2026-11-07

- Started documenting the project and why I chose it.
- Created the structure of the project and planned how I will work on it.
- Read the project description carefully and broke it down into smaller tasks, to go step by step. 


## Glossary

Coming soon

## Resources

- [eBPF documentation](https://docs.ebpf.io/)
- [Code examples from Liz Rice's Learning eBPF book](https://github.com/lizrice/learning-ebpf)