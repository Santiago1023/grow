# Software Networking

!!! note "Course mission"
    This course focuses on software networks. Topics related with control plane components, programmable data planes, network orchestration, and the Linux networking stack.

--- 

## 1) **Architecture of network devices**

---

### Data vs Control vs Managemente plane

Basic functionalities of:

- Data plane: in charge of handling packets, as fast as possible.

- Control plane: it controls how data plane has to behave.

- Management plane: it supervises the network device and the services running in it.

### Requirements: management and control planes

- In the management is like you are the administrator and you can configure things.

- In the control plane is in charge of complex tasks, but executed with low frequency (routing protocols, periodic updates, configuration commands), they require a large amount of code, but little processing power. The control plane not operate per packet. 

- We can make changes in the data that we are transfer, here is relevant data plane component, that receive, procces, and transmit the packets. The data plane operate per packet and is mainly in charge of very simple tasks, but repeated at high speed.

    There is a really important constraint here, high network speed introduce a pressure on data processing, because the time to handle the packet is limited before the arrival of the next one, is for that we must design our devices capable of work with this constraint and to avoid have bottlenecks.

---

### Data plane and memory latency

The type of memory and how we access it are so important.

In this part, there were many concepts that I did not really understand, so here Claude defined them:


#### What is data locality:

Data locality means keeping the data that a program uses **close together**, in memory and in time, so it can be served from the cache instead of from slow main memory.

- **Temporal locality**: data used now will probably be used again soon.
- **Spatial locality**: data next to the one used now will probably be used soon.

The CPU does not read single bytes from memory, it reads a full **cache line** (usually 64 B). Good locality means that most accesses are cache *hits*. In the data plane this is why lookup tables are kept small and compact, and why frameworks like DPDK process packets in **batches**: the code and data stay hot in the cache.

#### Static memory vs dynamic memory:

In programming, "static vs dynamic memory" means: memory reserved at compile time vs memory allocated at runtime (`malloc`, `new`).

#### Static RAM (SRAM):

**In one sentence:** the small and very fast memory used to build the CPU caches.

- It is called *static* because once a value is written, it simply stays there, without any extra work, as long as the computer is on.
- That makes it very fast (a few nanoseconds).
- But each bit takes a lot of space on the chip, so it is expensive and we can only have a little of it (a few MB).

#### Dynamic RAM (DRAM):

**In one sentence:** the big and cheaper memory used as main memory, the "RAM" modules of the computer.

- Each bit is stored as a tiny electric charge that slowly disappears, like a battery that discharges. To not lose the data, the memory has to re-write ("refresh") all the values many times per second. That constant work is why it is called *dynamic*.
- Because of this, and because it is outside the CPU chip, it is slower (~50–100 ns).
- But each bit takes very little space, so it is cheap and we can have a lot of it (GB).

!!! tip "Easy way to remember"
    **SRAM** = small and fast → used for the **cache**.
    **DRAM** = big and slower → used for the **main memory**.


#### Fast memory:

"Fast memory" means memory with **low access latency**. Since fast memory is expensive and small, computers use a **memory hierarchy**: a little very fast memory close to the CPU, and a lot of slower memory further away.

| Level | Approx. latency | Approx. size |
|---|---|---|
| CPU registers | < 1 ns | bytes |
| L1 cache (SRAM) | ~1 ns | tens of KB |
| L2 cache (SRAM) | ~3–5 ns | hundreds of KB – few MB |
| L3 cache (SRAM) | ~10–20 ns | tens of MB |
| Main memory (DRAM) | ~50–100 ns | GB |

Network devices also use special fast memories, like **TCAM** (Ternary Content-Addressable Memory), which can compare a packet header against all the table entries in parallel in one cycle. Routers use it for longest prefix match and ACLs.

#### The memory hierarchy (the pyramid):

The pyramid shows the different kinds of memory and storage ordered by speed. From the top to the bottom:

```text
            /\
           /  \        Registers        ┐
          /----\                        │
         /      \      Cache (L1-L3)    │ attached to the CPU
        /--------\                      │
       /          \    Main memory      ┘
      /------------\
     /              \  Storage (SSD, HDD)   accessed as I/O devices
    /----------------\
```

Going **down** the pyramid, memory gets bigger and cheaper per byte, but also slower. Going **up**, it gets faster but smaller and more expensive.

**What does "attached to the CPU" mean?**

Registers, cache and main memory are the memories that the CPU can use **directly with its instructions** (`load` / `store`):

- **Registers** are inside each CPU core. They are the only place where the CPU actually does its operations, so all data must end up here to be processed.
- **Cache** is also inside the CPU chip. L1 and L2 are usually private to each core, and L3 is shared by all the cores.
- **Main memory** (DRAM) is outside the chip, but it is connected directly to the CPU's memory controller through dedicated channels (DDR).

Everything below (SSD, HDD, network storage) is **not** attached to the CPU. It is accessed as an I/O device through a bus like PCIe or SATA, with the help of the operating system and drivers, and data must first be copied into main memory before the CPU can use it.

**Constraints to keep in mind**

| Level | Approx. latency | Size | Data unit | Who manages it | Keeps data without power? |
|---|---|---|---|---|---|
| Registers | < 1 ns | bytes | word (8 B) | Compiler | No |
| Cache | ~1–20 ns | KB – tens of MB | cache line (64 B) | Hardware, automatically | No |
| Main memory | ~50–100 ns | GB | page (4 KB) | Operating system | No |
| SSD | ~10–100 µs | hundreds of GB – TB | block (4 KB) | OS / file system | Yes |
| HDD | ~5–10 ms | TB | block | OS / file system | Yes |

- **Speed vs size vs cost**: you cannot have memory that is fast, big and cheap at the same time. The hierarchy is the compromise.
- **Data only moves between neighbor levels**: to be processed, data travels up the pyramid (storage → main memory → cache → registers). Every time it is not found in a level (a *miss*) we pay the latency of the level below.
- **Volatility**: everything attached to the CPU loses its contents when the power goes off. Only storage is persistent.
- **Granularity**: each level moves data in a different unit size. Even if you need 1 byte, the cache loads 64 B and the disk reads at least one block.
- **Who controls it**: the programmer does not control the cache directly. It works automatically, so the only way to use it well is to write code with good **data locality**.
- **Shared resources**: the L3 cache and the memory bandwidth are shared between all the cores, so one core can slow down the others.
- **NUMA**: in servers with more than one CPU (socket), each CPU has its own attached main memory. Reading the memory attached to the *other* CPU is slower.


#### Computer architecture: how the CPU and memory are connected

Most computers follow the **von Neumann architecture**: the program (its instructions) and the data are both stored in main memory. The CPU reads them, processes them, and writes the results back.

```text title="Computer architecture"
┌─────────────────────────────── CPU chip ───────────────────────────────┐
│                                                                        │
│  ┌────────── Core 0 ──────────┐   ┌────────── Core 1 ──────────┐       │
│  │ Control unit + ALU         │   │ Control unit + ALU         │       │
│  │ Registers                  │   │ Registers                  │  ...  │
│  │ L1 cache                   │   │ L1 cache                   │       │
│  │ L2 cache                   │   │ L2 cache                   │       │
│  └────────────────────────────┘   └────────────────────────────┘       │
│                                                                        │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │                  L3 cache (shared by all cores)                  │  │
│  └──────────────────────────────────────────────────────────────────┘  │
│  ┌────────────────────┐                     ┌───────────────────────┐  │
│  │ Memory controller  │                     │ I/O controller (PCIe) │  │
│  └─────────┬──────────┘                     └───────────┬───────────┘  │
└────────────┼────────────────────────────────────────────┼──────────────┘
             │ memory bus (DDR)                           │ PCIe bus
             │                       ┌─────────────┬──────┴──────┐
             │                       │             │             │
   ┌─────────┴──────────┐       ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
   │ Main memory (DRAM) │       │   NIC   │   │   SSD   │   │   GPU   │
   │   program + data   │◄─DMA──│(network)│   │(storage)│   └─────────┘
   │   packet buffers   │       └─────────┘   └─────────┘
   └────────────────────┘
```

**Main components**

- **CPU**: the chip that executes the instructions. Inside it we find:
    - **Cores**: each core is an independent processor, so a CPU with 8 cores can run 8 things at the same time. Each core has:
        - a **control unit**, which reads the next instruction and decides what to do,
        - an **ALU** (Arithmetic Logic Unit), which does the math and logic operations,
        - its own **registers**, and its private **L1 and L2 caches**.
    - **L3 cache**: shared by all the cores.
    - **Memory controller**: connects the CPU with the main memory.
    - **I/O controller (PCIe)**: connects the CPU with the devices.
- **Main memory (DRAM)**: holds the programs that are running and their data, including the buffers where received packets are stored.
- **Buses**: the "roads" that carry the data. The **memory bus** (DDR) goes between the CPU and the main memory, and the **PCIe bus** between the CPU and the devices.
- **I/O devices**: the **NIC** (Network Interface Card, sends and receives packets), the **SSD** (storage), the **GPU**, etc.

**How the CPU works with memory**

A core repeats the same cycle all the time:

1. **Fetch**: the control unit reads the next instruction from memory.
2. **Decode**: it understands what the instruction asks for.
3. **Execute**: the ALU does the operation using the registers. Special instructions (`load` / `store`) move data between memory and the registers.

Every time the core needs data, it looks for it in L1 → L2 → L3 → main memory, in that order. This is the pyramid in action: each level where the data is not found adds more waiting time.

**The journey of a packet**

1. A packet arrives at the **NIC**.
2. The NIC copies the packet into a buffer in **main memory** using **DMA** (Direct Memory Access), without using the CPU.
3. The CPU learns that there is a new packet, either because the NIC sends an **interrupt** or because a core keeps checking (**polling**, like DPDK does).
4. A core reads the packet header, which travels from memory through the caches up to the **registers**, and looks up the forwarding table (hopefully already in cache).
5. The core decides what to do with the packet, and the NIC reads it from main memory (again with DMA) to send it out.

!!! info "Key idea"
    Every packet passes through main memory, and every decision is made in the registers of a core. The time spent moving data between these two places is what limits how fast the data plane can be.

#### NIC (Network Interface Card):

**In one sentence:** the hardware that connects a computer to the network. It sends and receives the packets.

In the computer architecture figure, the NIC is the device connected to the CPU through the PCIe bus. It has one or more physical ports where the cable (copper or fiber) is plugged in.

What a normal NIC does:

- **Receives and sends frames**: it turns the signals on the cable into bits and frames, and the other way around.
- **Simple checks**: it discards frames with errors and, usually, frames that are not for its MAC address.
- **Copies the packets to main memory** using DMA, so the CPU does not have to do that copy.
- **Uses queues**: received and outgoing packets are placed in queues (*rings*) in main memory. Modern NICs have many queues and spread the flows among them (**RSS**, Receive Side Scaling), so several cores can process packets in parallel.
- **Small offloads**: some simple and repetitive tasks are done in hardware, like calculating checksums or splitting a big TCP message into packets (TSO).

Everything else (deciding where a packet goes, firewall rules, NAT, encryption...) is done by the **CPU**, in the operating system or in the application.

#### SmartNIC:

**In one sentence:** a NIC with its own processors and memory, so it can run part of the data plane by itself, instead of the CPU.

**Why do they exist?** At 100 Gbps or more, processing packets uses many CPU cores. In a cloud server those cores should run the customers' applications, not network functions. A SmartNIC takes that work off the CPU (it *offloads* it).

**What is inside?** There are three main types:

| Type | What does the processing | Pros | Cons |
|---|---|---|---|
| ASIC-based | A chip designed for specific network tasks | Very fast, low power | Only does what it was designed for |
| FPGA-based | Hardware that can be reprogrammed | Fast and flexible | Hard to program |
| SoC-based (DPU) | General purpose cores (usually Arm) running Linux, plus hardware accelerators | Easy to program, very flexible | Slower than pure hardware |

The SoC-based ones are often called **DPUs** (Data Processing Units). Examples: NVIDIA BlueField, AMD Pensando, Intel IPU. AWS Nitro cards follow the same idea.

**What can they do?**

- Virtual switching for VMs and containers (e.g. offloading Open vSwitch).
- Firewalls, load balancing and NAT.
- Encryption (IPsec, TLS).
- Run **programmable data plane** code, like **eBPF/XDP** or **P4** programs, directly on the card.
- Telemetry, to measure the traffic without using the CPU.

**Things to keep in mind**

- They are more expensive and harder to program than a normal NIC.
- They have their own (limited) memory, so the same memory hierarchy constraints apply inside the card.
- They fit the control vs data plane separation: the **control plane** can stay on the server CPU, while the **data plane** runs on the SmartNIC.

| | NIC | SmartNIC |
|---|---|---|
| Main job | Send and receive packets | Send, receive **and process** packets |
| Processing of the packets | Done by the server CPU | Done (partly) on the card |
| Programmable | No, fixed functions | Yes (FPGA, Arm cores, P4, eBPF) |
| Cost | Low | High |

#### SYN flooding attack:

A denial-of-service (DoS) attack that abuses the TCP three-way handshake (`SYN` → `SYN-ACK` → `ACK`):

1. The attacker sends a huge number of `SYN` packets, usually with fake (spoofed) source IPs, simulating many new connections.
2. The server answers each one with `SYN-ACK` and keeps a **half-open connection** in memory, waiting for the final `ACK`.
3. The `ACK` never arrives, so the queue of half-open connections fills up and the server can no longer accept connections from real users.

Common defenses are **SYN cookies** (the server does not store state until the handshake is completed), rate limiting, and dropping the malicious packets as early as possible in the data plane, for example with **XDP/eBPF** before they reach the kernel TCP stack.

#### OSPF:

**Open Shortest Path First** is a link-state routing protocol used *inside* one network or autonomous system (an IGP, Interior Gateway Protocol).

- Each router sends information about its links (LSAs, Link State Advertisements) to all the other routers.
- Every router builds the same map of the network (the link-state database).
- Each router runs **Dijkstra's algorithm** on that map to compute the shortest path to every destination. The cost of a link is usually based on its bandwidth.
- Big networks are divided into **areas**, all connected to the backbone area 0.

OSPF is a good example of a **control plane** task: it runs on the CPU, with low frequency and a lot of code. Its result (the routing table) is installed in the forwarding table that the **data plane** uses for every packet.

#### ASICs (Application-specific integrated circuits):

Microchip designed for a special application.

#### Network Processing Units (NPU):

Device for network processing tasks that is as programmable as a CPU but as fast as an ASIC.
NPUs are not general-purpose processors.

Limited use nowadays, but interesting to analyze their hardware architecture.

