## Full Virtualization

Full Virtualization is the foundational first-generation solution for x86/x64 server virtualization. Its defining characteristic is complete transparency; the fuest operating system (OS) functions under the illusion that it is executing directly on physical hardware. This architectural approach is highly significant because it allows various operating systems to run without any modifications to their core kernel, facilitating high compatibility across different environments.

The central mechanism enabling this transoarency is **Dynamic Binary Translation (DBT)**. Since the gues OS is unaware of the virtual environment, it attempts to execute privileged or dangerous instractions that could interfere with the host system. The virtualization layer intercepts these instructions in real-time and translates them into safe operations that can be executed on the host. Then hypervisor maintains control over the physical resources while presenting a standard, fooled environment to the guest OS. 

According to the lecture, this model involves a trade-off between compatibility and performance. Because every hardware component—including the CPU, memory, and peripherals—is emulated or translated via software, there is a significant performance overhead. The emulation layer acts as an intermediary between the guest and the host OS, which in turn interacts with the physical hardware. This "Hosted" architecture ensures that the guest is isolated, but it adds additional layers of processing for every instruction.

Prominent open-source examples of this technology include QEMU and Bochs. They are historically critical steps in the evolution of virtualization, proving that multiple, disparate operating systems can coexist on a single physical machine through software-driven hardware simulation.

The hierarchy of this system, illustrating the path from an application's request to the physical hardware, is represented below:

![type2_virtualisation_stack](https://github.com/user-attachments/assets/00b79fe1-b72d-470b-a44c-d9b6366706c7)

## Paravirtualization (Collaborative Virtualization)

Paravirtualization represents a step from full virtualization by moving awat from the illusion of direct hardware access. In this model, the guest operating system is fully aware that it is running in a virtualized environment. Paravirtualization relies on a collaborative relationship between the guest and the hypervisor. This method can be implemented across both Type 1 (bare-metal) and Type 2 (hosted) hypervisors. 

The core technical shift in paravirtualization is the modification of the guest operating system's kernel. Sensitive or privileged instructions, which would normally trigger a complex binary translation process in full virtualization, are replaced with specific software interrupts known as **Hypercalls**. These hypercalls act similarly to system calls in a standard OS; when the gues needs to perform a privileged operation, it explicity requests the hypervisor to execute it on its behalf.

As noted in the lecture, this "cooperative" approach drastically reduces the overhead associated with emulation and binary translation. By allowing the guest OS to communicate directly with the hypervisor for critical tasks, the system achieves much higher performance and efficiency. However, this comes at the cost of compatibility. Not every operating system is suited for this model because it requires deep access to and modification of the kernel source code. This results in a trade-off where performance is maximized at the expense of the "plug-and-play" flexibility found in full virtualization.

### The privilege ring architecture

The architecture of paravirtualization is often explained through the concept of pretection rings, as illustrated in the diagram. In a standard non-virtualized system, the kernel occupies Ring 0 (the most privileged), while applications run in Ring 3. In a paravirtualized stack:
- **Ring 3** remains the domain for user applications, which execute their standard requests;
- **Ring 0** is occupied by the Paravirtualized Guest OS. Because it is modified, it does not attempt to execute forbidden hardware instructions directly. Instead, it issues hypercalls to the hypervisor layer sitting between it and the physical hardware;
- **Hypervisor Layer** manages the actual hardware interaction, acting as the final authority and ensuring isolation between multiple guest systems.

![paravirtualisation_clean](https://github.com/user-attachments/assets/68e7c2a9-2372-4f4a-a605-7c1cb451c839)

## Hardware-Assisted Virtualization

Hardware-assisted virtualization represents a pivotal shift where virtualization moved from being a purely software-based challenge to one supported directly by physical processor. In this model, CPU manufactures like Intel and AMD intefrated specialized instruction sets into their hardware — specifically **Intel Virtualization Technology (VT-x)** for Xeon processors and **AMD-Virtualization (AMD*V)** for Opteron processors — to handle the complexities of virtualization at the silicon level. 

The core breakthrough of this technology is the elimination of the CPU emulation bottleneck. In previous generations, the hypervisor had to rely on software-intensive techniques like binary translation to intercept and manage privileged instructions. 

According to the lecture, this technology acts as a bridge. While a hypervisor layer still exists for management and orchestration, it can now delegate many critical tasks directly to the CPU's built-in virtualization features. This results in a system that is not only faster but also significantly more stable. The hardware-level support reduces the "overhead" or additional burden that virtualization typically places on a system, making it possible to run a higher density of virtual machines on a single physical server without a major drop in performance.

Today, hardware-assisted virtualization is a standard expectation and a fundamental requirement for modern large-scale data centers and cloud infrastructures.

![type1_hypervisor_corrected](https://github.com/user-attachments/assets/588e3e4e-954f-4751-8998-8c1d1fc19e68)

## Memory Virtualization

Memory virtualization is a core architectural concept that allows an operating system to manage physical RAM not as a single contiguos block, but as an abstracted, flexible resource. At the hear of this technology is the concept of a **Page**. Physical memory is divided into small, fized-size blocks called pages, which serves as the fundamental unit for memory management and allocation.

Instead of allowing a process to interact directly with physical hardware addresses, the operating system creates a **Virtual Memory** address space for every running process. This abstraction ensures that each process operates within its own private "view" of memory, providing essential isolation and security. To the process, it appears as though it has access to a continuous range of memory, whereas in reality, its data might be scattered across various non-contiguous locations in the physical RAM.

The translation between these two worlds is handled by a specialized data structure known as the **Page Table**. When the CPU generates a virtual address, the system uses the Page Table to map that request to the corresponding physical frame in the RAM. This mapping mechanism is highly efficient because it allows the operating system to place pages into any available physical slot, drastically reducing memory fragmentation and increasing overall system utilization.

A significant advantage of this model is the integration with **Secondary Memory** (such as a hard drive or SSD). When physical RAM becomes exhausted, the operating system can move inactive pages from the main memory to the secondary storage—a process often referred to as swapping or paging. This effectively allows the system to run applications that require more memory than is physically available, presenting an illusion of "unlimited" memory to the software layer. This core competency is vital in large-scale systems and cloud environments, where efficient resource management directly impacts performance and energy consumption.

![memory_virtualisation_paging](https://github.com/user-attachments/assets/e2dae74f-413b-4ff8-9058-fe7cc0d923f7)

### Shadow Page Table

Memory management in a virtualized environment introduces a layer of complexity that is not present in standard operating systems. While a typical OS translates virtual addresses to physical ones in a single step, a virtualized system must handle address translation across multiple layers. The Guest Operating System (Guest OS) continues to manage its own memory as if it were on physical hardware, creating an internal mapping from Guest Virtual to Guest Physical addresses. However, since the "Guest Physical" memory is itself a virtualized abstraction, it does not correspond to actual hardware locations.

To bridge this gap and maintain total authority over the physical RAM, the hypervisor implements **Shadow Page Tables**. These tables are a critical mechanism that maps the Guest OS's virtual memory addresses directly to the **Host Physical** (actual hardware) addresses. This allows the CPU to perform memory access efficiently without needing to consult the guest OS for every operation. The hypervisor creates these shadow tables by combining the information from the guest's own page tables with its own internal **Physical Map (pmap)**.

This architecture is primarily driven by the need for isolation. By having the hypervisor act as the final gatekeeper through Shadow Page Tables, different virtual machines are strictly separated from one another. A guest OS cannot access the memory space of another VM or the host itself. 

![shadow_page_tables_corrected](https://github.com/user-attachments/assets/5db43806-5edb-4ffa-954b-6ed52062d763)

### Hypervisor Memory Management

The hypervosr acts as a central coordinator, ensuring that multiple virtual machines can share the same physical hardware efficiently. It serves as a bridge between the physical memory (the guest's abstracted view) and the actual Machine Memory (the real hardware RAM).

The system relies on two critical mechanisms, **Page Fault** and **Hypercalls**. Page Fault occurs when a process attempts to access a page that is not currently present in the machina memory. Hypercalls used primarily in collaborative virtualization.

![hypervisor_memory_management](https://github.com/user-attachments/assets/0049df73-87a6-4202-baa0-e3d3a7ec0960)


## Local Disk I/O Virtualization

Local disk I/O virtualization ensures that every guest operating systems under the illusion that it has exclusive ownership of the physical disk. The hypervisor creates **virtual disks** by allocating large files on the physical storage and presenting them to the guest as real disk hardware. This abstraction allows the hypervisor to manage I/O operations by translating block numbers requested by the guest into specific file offsets on the host system.

The **Hypervisor I/O Stack** serves as the critical intermediary in this architecture. Because hardware-level processes like Direct Memory Access (DMA) require real physical addresses, the hypervisor performs necessary translations to ensure the guest's requests reach the correct physical location. Additionally, the hypervisor employs **multiplexing** to collect and redirect I/O requests from various virtual machines to the physical device, providing enterprise-level performance and ensuring that different VMs do not interfere with each other’s data.

Note: Disk requests do not go directly to the hardware; they are captured, translated, and controlled by the hypervisor to maintain system-wide coordination and resource sharing.

![local_disk_io_virtualisation](https://github.com/user-attachments/assets/e17e2896-70d2-432e-89b7-7e2155ebbafe)

## Local Network I/O Virtualization

Local network virtualization manages the flow of data packets between physical hardware and virtual machines through an intermediate layer known as the VMkernel. This architecture ensures that network traffic is never delivered directly to a Guest OS without hypervisor oversight.

The transmission of a network packet follows a strictly controlled five-step sequence:
1. Direct Memory Access (DMA)
2. Physical Interrupt
3. Packet Analysis
4. Copying to Queue
5. Virtual Notification

## Virtual Machine Scheduling

VM scheduling is the process by which a hypervisor manages the distribution of physical CPU resources among multiple virtual machines. Rather than just running the machines, the hypervisor acts as a central coordinator that determines how much processing power each VM receives. The architecture relies on mapping virtual CPUs (vCPUs) to specific physical processor cores. This abstraction allows the hypervisor to manage privileged commands and tasks depending on the type of virtualization used (Full or Paravirtualization). A critical feature of this mapping is **performance isolation**. Шf a specific physical CPU becomes overloaded, only the vCPUs directly tied to that core will experience a slowdown. Other physical cores and their respective VMs remain unaffected.

<img width="950" height="674" alt="1" src="https://github.com/user-attachments/assets/09cc79ac-e7bd-4d36-a485-fdfefc985ec5" />


## Virtual CPU (vCPU) Multiplexing

Virtual CPU multiplexing is a technique that allows multiple virtual machines to share the same physical processor, leading to significantly more efficient resource utilization. The core logic of this approach is to move away from **Separate VM Sizing**, where each machine is allocated resources based on its individual peak demand (S1 and S2). 

In contrast, **VM Multiplexing** consolidates these fluctuating workloads into a shared physical pool. The hypervisor leverages the fact that different virtual machines have different peak timing; as a result, the total capacity required (S3) is less than or equal to the sum of the individual peaks (S3≤S1+S2).

<img width="790" height="515" alt="2" src="https://github.com/user-attachments/assets/f2de1edf-dc50-4346-8f64-c20f20f019b0" />

## Virtual Machine Encapsulation

A primary advantage of virtualization is VM Encapsulation, which allows an entire virtual machine—including its operating system, applications, and data—to be stored as a single, portable file. Also, it preserves the specific state of the system's memory and virtual devices. 

The encapsulation model enables the use of Snapshots and Clones, which captures the current state of the virtual machine, allowing restoration to a point in time. This functionality allows for fast system provisioning, backup, and remote mirroring.

As mentioned in the lecture, the 'create once, run anywhere' capability simplifies the distribution of pre-configured applications and virtual appliances.

![server_file_export_diagram (1)](https://github.com/user-attachments/assets/671179d2-3ff8-4de0-b335-3f906a24aeaf)


## Virtual Machine Image Compatibility

The architectural abstraction provided by virtualization enables Hardware Independence, where the physical components of the host system are hidden from the Guest OS by the virtualization layer. The virtual machine is presented with a standardized set of virtual hardware components.

This independence facilitates the "Create Once, Run Anywhere" paradigm. Because the virtual environment is consistent, virtual machines can be moved between different physical hosts without encountering configuration conflicts or driver issues. 

Furthermore, virtualization provides a critical lifeline for Legacy VMs. Older operating systems that were designed for obsolete hardware (such as DOS) can continue to function on modern infrastructure. By using virtual IDE or vLance device drivers, these legacy systems can map their outdated requirements to modern high-performance hardware, such as Storage Area Networks (SAN) and Gigabit Ethernet (GigE).
