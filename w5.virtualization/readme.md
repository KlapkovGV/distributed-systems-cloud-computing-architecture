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

