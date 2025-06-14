# 📚 Cloud & Big Data Exam Notes: Virtualized Data Center – Networking (Lazy Edition)

> [!NOTE] This module is about making networks smart and flexible, just like we did with compute. Think "network software" instead of "network hardware."

---

## ☁️ Module 5: Virtualized Data Center – Networking

### 🎯 What You Need to Know
*   What's network virtualization?
*   What are the bits and pieces of a VDC network (both real and fake)?
*   What are VLANs and VSANs, and why do we care?
*   How do we manage all this network traffic?

---

## 🌐 Lesson 1: VDC Networking Overview

### 💡 What is Network Virtualization?
*   **Fancy talk**: Logically segmenting or grouping physical networks.
*   **Simple talk**: Making one physical network act like many separate, independent "virtual networks."
*   It's like drawing lines on one big whiteboard to create several smaller whiteboards, but they all share the same physical surface.

    ```mermaid
    graph TD
        A[Physical Network] --> B{Network Virtualization};
        B --> C[Virtual Network 1];
        B --> D[Virtual Network 2];
        B --> E[Virtual Network n];
    ```

### 🤔 Why Virtualize Networks? (Benefits!)
*   **Share Resources**: Virtual networks can share the same physical network hardware.
*   **Communication within VM Network**: VMs in the *same* virtual network can talk without needing a router (it's like they're on the same switch).
*   **Routing between Virtual Networks**: If VMs are in *different* virtual networks, they need a router (or something acting like one) to communicate.
*   **Security/Isolation**: Stops traffic (especially broadcast storms!) from one virtual network from messing with another. Like putting virtual walls up.
*   **Grouping**: Easily group virtual nodes (VMs) together.

### 🏗️ Network Infrastructure in VDC (Physical vs. Virtual)

#### 🧱 Physical Network
*   **What it is**: The *real* cables, switches, routers, etc.
*   **Components**: Physical NICs (Network Interface Cards), switches, routers, cables, etc.
*   **Connects**:
    *   Physical servers (hypervisors) to each other.
    *   Physical servers to clients.
    *   Physical servers to storage systems.

    ![[Pasted image 20240722143003.png]]
    *(Clients and servers connected to a physical network, which is then connected to storage. Hypervisors use PNICs to connect to this.)*

#### 👻 VM Network (Virtual Network)
*   **What it is**: Resides *inside* the physical server (within the hypervisor).
*   **Components**: Mainly **Virtual Switches** (logical switches), **vNICs** (virtual NICs).
*   **Connects**:
    *   VMs *within* the same physical server.
    *   VMs to the **Hypervisor Kernel** (for management, storage, VM migration traffic).
    *   The VM Network connects to the **Physical Network** via **PNICs**.

    ![[Pasted image 20240722143022.png]]
    *(VMs have vNICs that connect to a Virtual Switch. The Virtual Switch connects to the Hypervisor Kernel and also to the Physical Network via a PNIC.)*

### 🛠️ Network Virtualization Tools
*   **Physical Switch OS**: Needs to support network virtualization features.
*   **Hypervisor**: The main enabler.
    *   Uses **built-in** functions to create virtual switches and networks.
    *   Or, can use **third-party software** (installed *on* the hypervisor) that replaces/enhances its native networking.

### 💪 Benefits Summary Table (Quick Review!)

| Benefit                       | Description                                                                                                                                                                                             |
| :---------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Enhances Security**         | Restricts access between virtual networks, isolates sensitive data.                                                                                                                                     |
| **Enhances Performance**      | Restricts broadcast traffic (improves efficiency) within virtual networks.                                                                                                                              |
| **Improves Manageability**    | Centralized management of virtual networks, easy grouping/regrouping of nodes.                                                                                                                          |
| **Improves Utilization & Reduces CAPEX** | Multiple virtual networks share same physical network (more efficient use of hardware), reduces need for separate physical networks for different groups (saves money). |

---

## 📡 Lesson 2: VDC Network Infrastructure

### 🧩 Components of VDC Network Infrastructure
This table sums up the key parts:

| Component                    | Description                                                                                                                                                                                 |
| :--------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Virtual NIC (vNIC)**       | Connects VMs to the virtual switch. Has its own MAC/IP, sends/receives VM traffic. Acts like a physical NIC but for a VM.                                                                    |
| **Virtual HBA (vHBA)**       | Enables a VM to access FC RDM disk/LUNs (raw disk access). Uses NPIV (N_Port ID Virtualization) to let *one* physical HBA act like *many* virtual HBAs, each with its own WWN (World Wide Name). |
| **Virtual Switch**           | A logical Layer 2 (Ethernet) switch *inside* the physical server. Created by the hypervisor. Connects vNICs, forwards VM traffic, and directs traffic for the hypervisor kernel (management, storage, VM migration). |
| **Physical Adapter (NIC, HBA, CNA)** | The *real* network cards in the physical server. Connect physical servers to the physical network, forward VM/hypervisor traffic.                                                            |
| **Physical Switch, Router**  | The *real* network hardware. Forms the physical network. Connects physical servers to each other, to storage, and to clients. Supports various protocols (Ethernet, FC, iSCSI, FCoE).           |

### ↔️ Network Connectivity and Traffic Flow (The Different Scenarios)

These diagrams show how different types of traffic (VM, management, storage, migration) move through the VDC depending on the physical adapter setup.

1.  **Scenario 1: Everything through a PNIC (Ethernet)**
    *   VM, Management, IP Storage, VM Migration traffic all go through the Virtual Switch, out a PNIC, to the Physical Switch, and then to the respective destination (Storage Array, Clients).
    ![[Pasted image 20240722143120.png]]

2.  **Scenario 2: Dedicated FC/iSCSI HBA for Storage**
    *   VM, Management, VM Migration traffic go through the Virtual Switch and PNIC.
    *   FC or iSCSI Storage traffic goes through a *separate* dedicated **FC/iSCSI HBA** from the Hypervisor Kernel directly to the Physical Switch (FC/Ethernet), then to the storage array.
    ![[Pasted image 20240722143141.png]]

3.  **Scenario 3: Converged Network Adapter (CNA) with FCoE**
    *   **Consolidation is key here!** VM, Management, FC/IP Storage, VM Migration traffic *all* use the **CNA**.
    *   The CNA connects to a **Physical Switch (FCoE)** which understands both Ethernet and Fibre Channel over Ethernet.
    *   This means less cabling and fewer adapters.
    ![[Pasted image 20240722143202.png]]

### 💻 Virtual Network Component: Virtual Switch
*   **What**: Logical Layer 2 (Ethernet) switch.
*   **Location**: Resides *inside* a physical server (part of the hypervisor).
*   **Function**:
    *   Connects VMs to each other *within the same physical server*.
    *   Connects VMs to the physical network.
    *   Connects the hypervisor kernel to the network for management, IP storage, and VM migration.
*   **Load Balancing & Failover**: Can connect to multiple physical NICs to distribute network load and provide redundancy.

    ![[Pasted image 20240722143232.png]]
    *(Virtual switch connects to multiple PNICs for redundancy and load balancing.)*

*   **Internal Only**: A virtual switch doesn't *have* to connect to a physical NIC. It can just direct VM traffic *within* the physical server (e.g., a firewall VM that filters traffic between other VMs on the same host).

    ![[Pasted image 20240722143254.png]]
    *(Here, VM1 and VM2 talk to each other via their virtual switches, and then through a "VM with Firewall Application" which directs traffic without going out to a PNIC.)*

#### Virtual Switch: Ports and Port Group
*   **Types of Ports**:
    *   **Hypervisor Kernel Port**: For hypervisor traffic.
    *   **VM Port**: For VM's vNICs.
    *   **Uplink Port**: Connects to the physical NIC(s).
*   **VM Port Group**:
    *   A way to apply **uniform network policy settings** (security, load balancing, failover) to a *group* of VM ports.
    *   Simplifies management: configure policies once for the group, not individually for each VM.

    ![[Pasted image 20240722143323.png]]
    *(Multiple VM ports grouped together, sharing policy settings.)*

#### Distributed Virtual Switch (DVS)
*   **What**: Aggregates (combines) multiple virtual switches that are spread across *different physical servers*.
*   **Benefits**:
    *   **Centralized VM Network Management**: Manage all virtual networking from one place, regardless of which physical server a VM is on.
    *   **Policy Retention**: Network policies (like QoS, security) **remain consistent** even when VMs migrate between physical servers (VMotion). This is a *huge* advantage.

    ![[Pasted image 20240722143346.png]]
    *(Multiple separate virtual switches are replaced by one "Distributed Virtual Switch" abstraction.)*

### 🎛️ Physical Network Component: NIC, HBA, CNA (Deep Dive)

*   **Physical NIC (PNIC)**:
    *   Connects virtual and physical Ethernet switches.
    *   Transfers VM and hypervisor kernel traffic.
    *   **Key Point**: The *PNIC itself* typically doesn't have an IP or MAC address visible to the network for Layer 2/3 access. It just acts as a conduit. The **vNICs** and the **hypervisor kernel** *do* have their own IPs/MACs that are used as source addresses in Ethernet frames.

*   **iSCSI HBA**:
    *   Transfers storage I/Os (SCSI) to iSCSI storage systems.
    *   Has a built-in iSCSI initiator.
    *   Encapsulates SCSI into iSCSI frames, then into Ethernet frames.
    *   Uses its own MAC/IP addresses for Ethernet transmission.
    *   **Offloads iSCSI processing** from the hypervisor's CPU.

*   **FC HBA**:
    *   Transfers storage I/Os (SCSI) to Fibre Channel storage systems.
    *   Encapsulates SCSI data into FC frames.
    *   Uses its own FC address.

*   **CNA (Converged Network Adapter)**:
    *   A **multi-function adapter** that acts as *both* a NIC and an FC HBA (or iSCSI HBA).
    *   Acts as the link between virtual and physical switches.
    *   Provides hypervisor access to FC storage.
    *   **Goal**: Consolidate network and storage traffic onto a single adapter and physical cable plant.

---