# 📚 Cloud & Big Data Exam Notes: Classic Data Center (CDC) - Pt. 2

> [!NOTE] Still lazy. Still no fluff. Just the essentials. You got this.

---

## 🛡️ Redundant Array of Independent Disks (RAID) - Deep Dive

### 🧠 What is RAID?
*   Uses *multiple* disks as one.
*   **Why?**: More protection (data safety), more capacity, better performance.
*   **How?**: Serves I/Os (reads/writes) from multiple disks at once.

### 📊 RAID Techniques (The Big Three)

1.  **Striping (RAID 0)**:
    *   Splits data into chunks and writes them across *multiple disks*.
    *   **Goal**: *Faster performance*. Data is written/read in parallel.
    *   **Catch**: **No fault tolerance**. If one disk dies, ALL data is lost. Think of it as a wider, faster road with no railings.

    ```mermaid
    graph TD
        A[Data A] -->|Split| A1(Disk 1) & A2(Disk 2) & A3(Disk 3);
        style A1 fill:#F9E79A,stroke:#333,stroke-width:2px
        style A2 fill:#F9E79A,stroke:#333,stroke-width:2px
        style A3 fill:#F9E79A,stroke:#333,stroke-width:2px
        linkStyle 0 stroke:#666,stroke-width:2px,fill:none;
        linkStyle 1 stroke:#666,stroke-width:2px,fill:none;
        linkStyle 2 stroke:#666,stroke-width:2px,fill:none;
    ```
    *(Imagine data `A` being split into `A1`, `A2`, `A3` and written simultaneously.)*

2.  **Mirroring (RAID 1)**:
    *   Writes *identical copies* of data to *two or more disks*.
    *   **Goal**: *Maximum fault tolerance*. If one disk dies, the copy is still there.
    *   **Catch**: Uses double the storage space. Think of it as having an exact backup copy always.

    ```mermaid
    graph TD
        A[Data A] -->|Copy| AD1(Disk 1) & AD2(Disk 2);
        style AD1 fill:#F9E79A,stroke:#333,stroke-width:2px
        style AD2 fill:#F9E79A,stroke:#333,stroke-width:2px
        linkStyle 0 stroke:#666,stroke-width:2px,fill:none;
        linkStyle 1 stroke:#666,stroke-width:2px,fill:none;
    ```
    *(`A` is written to Disk 1 and Disk 2 exactly.)*

3.  **Parity (RAID 5, 6, etc.)**:
    *   Uses a special calculated block (**parity**) that can reconstruct data if a disk fails.
    *   **Goal**: *Fault tolerance with better space efficiency* than mirroring.
    *   **Catch**: Slightly slower for writes due to parity calculation.

    ```mermaid
    graph TD
        A[Data A] --> A1(Disk 1) & A2(Disk 2);
        A --> AP(Parity Disk);
        style A1 fill:#F9E79A,stroke:#333,stroke-width:2px
        style A2 fill:#F9E79A,stroke:#333,stroke-width:2px
        style AP fill:#F9E79A,stroke:#333,stroke-width:2px
        linkStyle 0 stroke:#666,stroke-width:2px,fill:none;
        linkStyle 1 stroke:#666,stroke-width:2px,fill:none;
        linkStyle 2 stroke:#666,stroke-width:2px,fill:none;
    ```
    *(`A1`, `A2` are data, `AP` is the calculated parity from A1 and A2.)*

### 📋 RAID Levels (Just the Table)

| RAID Levels | Definition                                                     |
| :---------- | :------------------------------------------------------------- |
| RAID 0      | Striping with no fault tolerance                               |
| RAID 1      | Disk mirroring                                                 |
| Nested      | Combinations of RAID levels; Example: RAID 1 + RAID 0          |
| RAID 3      | Parity RAID with dedicated parity disk (byte-level striping)   |
| RAID 5      | Parity RAID with distributed parity across all disks in the set |
| RAID 6      | Distributed parity RAID with dual parity                       |

*   **RAID 3 vs RAID 4**:
    *   RAID 3 uses **byte-level striping**.
    *   RAID 4 uses **block-level striping**.
    *   Both RAID 3 & 4 have a **dedicated parity disk**.

*   **RAID 10 (1+0)**: A *nested* RAID. It's RAID 1 (mirroring) *on top of* RAID 0 (striping).
    *   **Benefits**: Combines RAID 0's speed with RAID 1's fault tolerance.
    *   **Cost**: Expensive, as it requires a lot of disks.

    ![[Pasted image 20240722135048.png]]
    *(Imagine pairs of mirrored disks (RAID 1) that are then striped together (RAID 0).)*

## 🧠 Intelligent Storage System (ISS)

*   Basically, a highly optimized **RAID array** with extra smarts.
*   **Key features**:
    *   **Big Cache**: Uses lots of fast memory (cache) to speed up I/O.
    *   **Smart OS**: Has its own operating environment to manage storage resources, cache, and allocate storage efficiently.

### 🏗️ Components of an Intelligent Storage System

![[Pasted image 20240722135118.png]]

*   **Compute**: Your servers.
*   **Network**: Connects compute to the storage system.
*   **Intelligent Storage System (ISS)**:
    *   **Front-end**: Receives requests from servers (via network ports).
    *   **Cache**: Super-fast memory for temporary data storage (improves performance).
    *   **Back-end**: Manages the physical disks and RAID sets.
    *   **Physical Disks**: The actual hard drives or SSDs where data lives.

### 📦 Presenting Storage (LUNs)

*   **LUN (Logical Unit Number)**: A way to present a piece of storage from the ISS to a server.
*   Even though it's a part of a larger RAID set on physical disks, the server sees it as a single, independent "disk."

![[Pasted image 20240722135140.png]]
*(Compute sees LUN 0 and LUN 1, but they are carved out from a bigger RAID Set on the Physical Disks.)*

## 🌐 Storage Networking Technologies

### 🖥️➡️🖥️ Compute to Compute Communication
*   **How**: Usually Ethernet with TCP/IP (LAN, MAN, WAN).
*   **Components**:
    *   **NIC (Network Interface Card)**: The network adapter in your server (has a MAC address).
    *   **Switches/Routers**: Connect computers and networks.
    *   **Cables**: Copper (twisted pair, coax) or fiber (optical).

### 🖥️➡️📦 Compute to Storage Communication
*   More specialized, uses things like HBAs and CNAs.
*   **HBA (Host Bus Adapter)**: A special card in the server that handles I/O to storage, takes workload off the main CPU.
*   **CNA (Converged Network Adapter)**: A fancy adapter that combines both NIC (for regular network) and HBA (for storage network) functions into one card.

#### Channel vs. Network Technologies

| Feature            | Channel Technology                                | Network Technology                                       |
| :----------------- | :------------------------------------------------ | :------------------------------------------------------- |
| Connection Type    | Direct channel (e.g., PCI, SCSI)                  | Over a network (e.g., Ethernet)                          |
| Protocol Overhead  | Low (tightly coupled)                              | High (network protocols add overhead)                    |
| Distance           | Short distances only                              | Long distances possible                                  |
| Fixed Connections  | Yes, point-to-point or direct attachment          | Flexible, network-based                                  |
| Examples           | PCI, IDE/ATA, SCSI                                | iSCSI (SCSI over IP), FCoE (FC over Ethernet), FC (Fiber Channel) |

### 💬 Communication Protocols (Storage Specific)

*   **PCI (Peripheral Component Interconnect)**: How CPU talks to devices directly inside the computer. (e.g., PCI Express for fast throughput).
*   **IDE/ATA**: Older, popular for connecting disk drives. Serial ATA (SATA) is the newer version. Low cost.
*   **SCSI (Small Computer System Interface)**: Preferred for high-end storage. Better performance, more scalable than ATA. Serial Attached SCSI (SAS) is the serial version.
*   **TCP/IP**: Standard internet protocol. Traditionally compute-to-compute, but now also **compute-to-storage** (e.g., iSCSI, FCoE).

### 📤 Direct Attached Storage (DAS)
*   **What**: Storage (internal or external) connected *directly* to a single server.
*   **Benefits**: Simple, cheap for local needs.
*   **Challenges**:
    *   **Limited scalability**: Can't grow much.
    *   **Limited sharing**: Can't easily share with other servers.
    *   **"Islands of storage"**: Data stuck on one server, resources wasted.

### 📈 Emergence of Storage Networking (Why we needed SAN/NAS)
*   DAS couldn't keep up with business needs for:
    *   Just-in-time info
    *   Flexible, resilient storage
*   **Solution**: Dedicated storage networks!
    *   **Fibre Channel SAN (FC SAN)**
    *   **Network Attached Storage (NAS)**
    *   **Internet Protocol SAN (IP SAN)**
    *   **Fibre Channel over Ethernet (FCoE)**

---

## 🚀 Advanced Storage Networking

### 🪢 Fibre Channel SAN (FC SAN)
*   **What**: A **dedicated, high-speed network** just for servers and storage.
*   **How**: Uses **SCSI over FC protocol**.
*   **Access**: Provides **block-level data access** (like a direct disk connection).
*   **Benefits**:
    *   Consolidates & shares storage.
    *   Centralized management.
    *   High scalability & performance.
    *   Reduces cost (by sharing, not buying DAS for every server).

    ![[Pasted image 20240722135248.png]]
    *(Servers connect to storage arrays through the FC SAN cloud.)*

#### Components of FC SAN
*   Node ports (connections on devices)
*   Cables (fiber optic)
*   Connectors
*   Interconnecting Devices (FC Switches)
*   Storage Arrays
*   SAN Management software

#### Fibre Channel Fabric
*   A network of interconnected **FC switches**.
*   **Inter Switch Link (ISL)**: The link connecting two FC switches in a fabric.

    ![[Pasted image 20240722135316.png]]
    *(Servers and storage connect to switches, which are linked by ISLs.)*

#### FC Port Types (Important!)
*   **N-Port (Node Port)**: An **endpoint** port (on a server HBA or storage array).
*   **F-Port (Fabric Port)**: A **switch port** that connects to an N-Port.
*   **E-Port (Expansion Port)**: A **switch port** that connects to another switch (forms an ISL).

    ![[Pasted image 20240722135338.png]]
    *(Trace the connections: N-Port to F-Port, F-Port to N-Port, E-Port to E-Port.)*

### 🌐 Internet Protocol SAN (IP-SAN)
*   **What**: Transfers **block-level data over a regular IP network**.
*   **Why use it?**:
    *   Easier management (you already know IP).
    *   Leverages existing network infrastructure (cheaper, no new cables).
    *   Cost-effective compared to new FC SAN hardware.
    *   Good for long-distance disaster recovery.
    *   Leverages mature IP security.

#### Block Storage Over IP - Protocols

1.  **iSCSI (SCSI over IP)**:
    *   Encapsulates SCSI commands/data inside IP packets.
    *   Uses regular Ethernet NICs or specialized iSCSI HBAs.
    *   Can use an iSCSI Gateway to connect to existing Fibre Channel storage.

    ![[Pasted image 20240722135402.png]]
    *(Server with iSCSI HBA talks over IP to an iSCSI Gateway, which then talks to the FC storage.)*

    *   **iSCSI Topologies**:
        *   **Native**: Server with iSCSI HBA connects directly to the storage array over IP. No FC components needed.
        *   **Bridged**: Server (iSCSI) talks to an iSCSI Gateway (the "bridge"), which then translates to Fibre Channel to talk to the FC SAN.

        ![[Pasted image 20240722135422.png]]

2.  **FCIP (Fibre Channel over IP)**:
    *   Creates a "tunnel" (bridge) over an IP network to connect two *separate* Fibre Channel SANs.
    *   Used for **distance extension** (e.g., disaster recovery between two data centers).

    ![[Pasted image 20240722135446.png]]
    *(Two FC SANs connected over a regular IP network via FCIP gateways.)*

### 🔄 Fibre Channel over Ethernet (FCoE)
*   **What**: Encapsulates Fibre Channel frames (data) within **Enhanced Ethernet networks**.
*   **Big Deal**: **Consolidation!** It merges SAN traffic (Fibre Channel) and regular Ethernet traffic onto a *single 10 Gigabit Ethernet infrastructure*.
*   **Benefits**:
    *   Lower cost (less cabling, fewer adapters).
    *   Reduces power/cooling.
    *   Consolidates network infrastructure.
    *   Lower Total Cost of Ownership (TCO).

    ![[Pasted image 20240722135508.png]]
    *(See how FCoE links from hosts go into FCoE switches, which then connect to both Storage Arrays and the LAN.)*

    *   **Before FCoE**: Separate networks for Ethernet and Fibre Channel.
        ![[Pasted image 20240722135531.png]]
    *   **After FCoE**: Converged Network Adapter on server, FCoE Switch, single set of cables for both types of traffic.
        ![[Pasted image 20240722135549.png]]

#### Components of FCoE
*   **CNA (Converged Network Adapter)**: Multi-function adapter combining NIC and HBA.
*   **FCoE Switch**: Contains Ethernet bridge and Fibre Channel Forwarder (FCF) to encapsulate/decapsulate frames.
*   **CEE (Converged Enhanced Ethernet)**: Extensions to standard Ethernet to make it "lossless" (important for storage traffic).

---

## ⏱️ Business Continuity (BC) & Backup

### 🩹 Business Continuity (BC)
*   **Goal**: Keep business operations running, no matter what.
*   **Focus**: Ensuring "information availability" even during outages or disasters.

### 📝 BC Terminologies

| Terminology        | Description                                                                                                                              |
| :----------------- | :--------------------------------------------------------------------------------------------------------------------------------------- |
| **Disaster Recovery (DR)** | Process of restoring systems, data, and infrastructure after a disaster.                                                                 |
| **Hot Site**       | A fully equipped, active site where operations can immediately move during a disaster. All hardware/software ready. Costly.                      |
| **Cold Site**      | A site with minimal IT infrastructure (space, power, cooling) but *not activated*. Cheaper, but takes longer to get up and running after a disaster. |
| **Cluster**        | Group of servers working together as a single system (improves availability).                                                           |

### 💼 CDC Management

### 📊 Key Management Activities
*   **Monitoring & Alerting**: Watching resources (compute, storage, network) and getting notified of issues.
    *   **Parameters to Monitor**: Accessibility, Capacity (free space), Performance, Security (unauthorized access, config changes).
    *   **Alert Levels**:
        *   **Information**: Just FYI, no action needed.
        *   **Warning**: Needs attention, e.g., disk filling up.
        *   **Fatal**: Immediate action, e.g., power failure, disk crash.
*   **Reporting**: Gathering data on resource utilization, performance, and charging departments (`Chargeback`).
*   **Availability Management**: Setting rules to ensure high availability.
    *   **Key Techniques**:
        *   **Eliminating Single Points of Failure (SPOF)**: Don't have one component whose failure brings everything down.
        *   **Multipathing**: Having multiple paths (cables, HBAs, switches) between servers and storage.
            *   **Role of Software**:
                1.  **Automatic Failover**: If one path breaks, automatically switch to another.
                2.  **Load Balancing**: Distribute I/O across multiple paths for better performance.
        *   **RAID Protection**
        *   **Redundant Fabrics**
        *   **Data Backup & Replication**
*   **Capacity Management**: Ensuring you have enough space and resources.
*   **Performance Management**: Keeping things fast.
*   **Security Management**: Protecting everything.

### 🌐 Managing Information - Challenges
*   **Exploding Digital Universe**: Data is growing crazy fast.
*   **Increasing Dependency**: Businesses rely more and more on information.
*   **Changing Value**: Data that's critical today might be useless tomorrow, so managing its lifecycle is key.

---