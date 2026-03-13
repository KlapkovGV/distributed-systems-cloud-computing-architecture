### Cloud Infrastructure and Scaling

The foundation of cloud infrastructure begins with the Low-end Server, which acts as the basic computational unit. These servers are organized into a Server Rack, a vertical chassis that houses multiple units to save space and centralize management. To form a massive cloud environment, these racks are interconnected using high-speed networking, typically 1Gbps or 10Gbps Ethernet. A Cluster Switch manages the traffic between these sets of racks, allowing thousands of individual servers to function as a single, cohesive pool of resources.

### Data Center Storage Architecture

Inside each server, the architecture follows a standard flow where the CPU communicates with DRAM via a Memory Controller. However, data center storage is categorized by how it connects to this system. Direct Attached Storage (DAS) involves drives connected via interfaces like SCSI or PCIe directly to the server's bus. In contrast, modern data centers rely heavily on Network Attached Storage (NAS) or Storage Area Networks. These use technologies such as Fibre Channel, Infiniband, or high-speed Ethernet to connect servers to external storage arrays through a central Switch. This decoupling allows storage to be shared across multiple servers, enhancing flexibility and data redundancy.

### Environmental Management: Cooling Systems

Maintaining the thermal stability of a data center is critical for hardware longevity. The most common method is the Raised Floor cooling design. In this setup, a CRAC (Computer Room Air Conditioning) Unit pushes pressurized Cold Air into the void beneath the floor. This air enters the server racks from the bottom or front, absorbs heat from the components, and exits as Hot Air from the top or back. This hot air is then pulled back into the CRAC unit to be cooled and recirculated, creating a continuous thermodynamic cycle that prevents the servers from overheating.

### Power Distribution and Redundancy

The power journey starts at a high-voltage Transformer connected to the electrical grid. To ensure 24/7 operation, an ATS (Automatic Transfer Switch) monitors the grid; if power fails, it immediately switches the load to a backup Generator. Before reaching the servers, electricity passes through an UPS (Uninterruptible Power Supply) to clean the signal and provide battery backup during the seconds the generator takes to start. Finally, PDUs (Power Distribution Units) distribute the regulated 208V/480V power to the server containers, ensuring each rack receives a stable energy supply.

### Power Usage Effectiveness (PUE)

Efficiency in a data center is measured by how much of the total incoming power is actually used for computing versus "overhead" like cooling. In the provided model, only 52% of the total power is used for Computing (Servers, Storage, Network), while 40% is consumed by the Cooling System and 8% is lost during power distribution. This relationship is defined by the PUE metric, calculated as Total Power divided by Computing Power. A PUE of 1.92 (as seen in the example) indicates that for every watt used to process data, nearly another watt is spent on supporting infrastructure. The industry goal is a "Good PUE" closer to 1.0, meaning almost all energy is dedicated to computing tasks.

### Fundamentals of Server Organization

At its core, a server consists of three primary components that must work in harmony: the CPU (Central Processing Unit), which handles all calculations and data processing; Memory (RAM), which provides temporary storage for active programs; and I/O Devices, such as network cards and disks, that allow the server to interact with the outside world. The performance of any system is critically dependent on the communication speed between these three elements. In a traditional physical machine, the software (Operating System) is tightly coupled with this hardware, controlling it directly. This rigid link often leads to a major industry problem: physical resources are rarely used to their full capacity, resulting in significant waste and inefficiency.

### The Limitations of Non-Virtualized Datacenters

Traditional datacenter structures are built on "silos." In this environment, every application is assigned to its own specific physical server. For example, a Windows application, an Ubuntu server, and a macOS system would each require separate hardware racks. Because these systems cannot share resources, a server running at only 5% capacity cannot "lend" its idle power to a nearby server that is struggling with a heavy workload. This lack of flexibility creates high maintenance costs and makes it impossible to move applications between servers without physical intervention. Consequently, these datacenters suffer from low resource utilization and a complete lack of scalability.

### The Objective: From Static to Dynamic Sharing

The ultimate goal of cloud computing is to move away from the Static Partitioning of the past and toward Dynamic Sharing. In a static environment, resources are fixed; if an application like Hadoop or MPI doesn't use its allocated 33% of the hardware, that power is simply lost. Cloud infrastructure (Shared IaaS) solves this by creating a pool of resources that can be distributed elastically. By managing resources more flexibly, the cloud increases overall performance while drastically reducing the energy and hardware waste seen in traditional setups.

### Virtualization as a Core Enabler

It is often said that there is "no cloud without virtualization." This technology allows for System Consolidation, which is the process of replacing many small, underutilized physical machines with a single, massive, and powerful server. Virtualization provides several key business benefits:

Workload Isolation: Different systems can be tested on the same hardware without interfering with each other.

High Availability: Applications can remain continuous even if parts of the physical hardware fail.

Elasticity: The system can shrink or grow based on real-time demand. The concept is not entirely new; IBM first pioneered this logic in 1972 with the VM/370, proving that you could change the underlying hardware to something more efficient without needing to change the software or applications themselves.

### The Virtualization Layer

Technically, virtualization is achieved by inserting a Virtualization Layer (also known as a hypervisor) between the physical hardware and the operating systems. In a non-virtualized system, a single OS has total control over the hardware platform. In a virtualized system, the virtualization layer allows a single physical platform to host multiple "Virtual Containers" or Virtual Machines. Each container acts as an independent computer with its own OS and apps, yet they all share the same underlying CPU, memory, and disk resources, allowing for the high-density computing required for modern cloud services.

### Comparative Architectural Models

In a Non-virtualized System, a single operating system holds direct and total management over all hardware resources. Consequently, all applications must run under this specific OS, and resource sharing is extremely limited because every application is dependent on that single OS instance. Conversely, a Virtualized System introduces a virtualization layer as a mediator between the hardware and the applications. This enables every application to run within its own isolated Virtual Machine, allowing multiple different operating systems to function independently on the same physical platform.

### The Virtualization Hierarchy and Multi-Tenancy

The virtualized infrastructure in the cloud is divided into three distinct layers. The Physical Hardware Layer contains the actual architectural components like memory, disk drives, and servers. Above this, the Virtualization (Hypervisor) Layer creates virtual versions of these resources, maps them to the physical ones, and ensures strict isolation between different environments. At the top, the Virtual Machine Layer consists of proxies that possess the same interfaces and functions as physical resources. This setup enables Multi-tenancy, where pooled hardware resources are shared among many different users simultaneously. Within cloud hardware, we distinguish between Server Virtualization, which shares CPU and RAM among VMs, and Network Functions Virtualization (NFV), which virtualizes network components to provide greater scalability.

### Classification of Hypervisors

Hypervisors are categorized based on their relationship with the underlying hardware. A Type-1 Hypervisor, often called a "Bare-metal" hypervisor, runs directly on the physical hardware to manage virtual machines. Because there is no intermediary operating system, it is highly efficient and is the standard choice for professional data centers; common examples include VMware ESXi, Microsoft Hyper-V, and Xen. A Type-2 Hypervisor, known as a "Hosted" hypervisor, runs on top of a traditional operating system like Windows or macOS. While these are more flexible and easier to set up for individual users, they offer lower performance due to the extra overhead of the host OS. Examples include Oracle VirtualBox and VMware Workstation.

### Access Protection Rings and Security

System security and authorization are managed through a mechanism called Protection Rings. These rings define privilege levels within the computer architecture, ranging from Ring 0, which is the Kernel Mode with the highest privileges, to Ring 3, which is the User Mode where normal applications run. Middle levels like Rings 1 and 2 are traditionally reserved for device drivers or system services, though most modern operating systems primarily utilize only Rings 0 and 3. The fundamental rule of access is that code at a higher-privileged level (like Ring 1) can access lower-level rings (Rings 2 and 3), but code in a low-privilege ring (Ring 3) cannot directly access the more sensitive data in the higher rings (Rings 0 or 1).

### Evolution of Hypervisor Solutions

The technology has evolved through three distinct generations. The 1st Generation (Full Virtualization) relied on software-based binary rewriting and dynamic translation to manage the guest OS, a method popularized by early VMware and Microsoft solutions. The 2nd Generation (Paravirtualization) introduced a collaborative approach where the "guest" operating system is modified to be aware that it is running in a virtual environment, improving efficiency through cooperation with the hypervisor. Finally, the 3rd Generation (Hardware-assisted Virtualization) utilizes silicon-based technologies. In this modern era, the hardware itself is "virtualization-aware," allowing unmodified guest operating systems to run with near-native performance on platforms like Xen or VMware.

### Paravirtualization

Paravirtualization, often referred to as "collaborative virtualization," which serves as an alternative to full virtualization. Unlike traditional methods where the guest operating system is unaware of the virtual environment, paravirtualization requires modifying the guest OS kernel. By doing so, sensitive or non-virtualizable instructions are proactively replaced with specialized instructions known as Hypercalls. These hypercalls allow the guest OS to communicate directly with the hypervisor, requesting it to perform privileged operations that the guest cannot execute on its own. This collaborative approach can be implemented in both Type-1 and Type-2 hypervisors to enhance overall system efficiency.

The architectural logic relies on a modified execution flow within the Protection Rings. In this model, applications continue to reside in Ring 3, where they execute standard user-level operations directly. However, the Paravirtualized Guest OS operates at the Ring 0 level in a way that allows it to function similarly to a user program making system calls. When the guest OS encounters a task that requires direct hardware access, it triggers a hypercall to the Hypervisor Layer. This layer, positioned between the guest OS and the physical hardware, manages the request and executes the privileged task on behalf of the virtual machine.

A direct comparison highlights the shift from "True Virtualization" to "Paravirtualization." In true virtualization, an unmodified operating system, such as Windows, attempts to execute sensitive instructions which must be "trapped" and emulated by the hypervisor, often causing performance overhead. In contrast, paravirtualization uses a Modified Kernel (frequently a modified Linux distribution) that essentially talks to a microkernel or hypervisor. This direct communication path bypasses the need for complex instruction translation, leading to a more streamlined and responsive environment where the software and virtualization layer work in tandem rather than through a process of trial and error.

### Full Virtualization

This slide explores Full Virtualization, which represents the first generation of solutions developed for x86 and x64 server virtualization. In this model, the system provides a complete simulation of the underlying hardware, allowing a guest operating system to run in total isolation without being aware that it is functioning within a virtual environment. This "transparency" is a key feature, as it means the guest OS requires no modifications to its kernel to function.

The technical core of full virtualization is a method known as Dynamic Binary Translation. Because the guest OS thinks it is running on real hardware, it attempts to execute privileged instructions that would normally require direct access to the physical CPU. The hypervisor (or emulation layer) intercepts these instructions in real-time and translates them into safe, emulated sequences that the host system can execute. While this provides a high degree of compatibility and security, it often introduces performance overhead because every sensitive instruction must be processed through this additional translation layer.

In this architecture, every component—including the CPU, memory, and networking devices—is completely emulated. This abstraction layer acts as a bridge between the virtual machine's drivers and the physical hardware. Prominent open-source examples of such emulators include QEMU and Bochs, which are widely used to create these fully independent and isolated digital environments on top of a host operating system.











