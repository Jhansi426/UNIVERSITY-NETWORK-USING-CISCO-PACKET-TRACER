# UNIVERSITY-NETWORK-USING-CISCO-PACKET-TRACER

---

## 📖 Table of Contents

1. [Project Overview](#project-overview)
2. [Campus Topology](#campus-topology)
3. [Building-by-Building Breakdown](#building-by-building-breakdown)
4. [IP Addressing & VLAN Design](#ip-addressing--vlan-design)
5. [Key Hardware Components](#key-hardware-components)
6. [Data Flow and Routing](#data-flow-and-routing)
7. [IMAGES](#IMAGES)
8. [Security & Management](#security--management)
9. [Benefits & Scalability](#benefits--scalability)
10. [Getting Started](#Getting Started)


---

## 🚀 Project Overview

This project documents the design and implementation of a multi-building university campus network. The network connects:

* **Building A**: Departments of Management, HR, Finance, Business
* **Building B**: Departments of Electronics & Communication (E\&C), Art & Design
* **Building C**: Student Computer Labs, IT services, Web & FTP servers
* **Smaller Building**: Additional lab and administrative workspace
* **Core Infrastructure**: Central router, multilayer core switch, email server

Each department or lab is served by its own switch, with inter-building connectivity via high-speed fiber trunks and routed subnets. This design ensures high performance, security segmentation, and easy management.

---

## 🌐 Campus Topology

```text
                                                                 Email Server
                                                                    │ 
                                                                ┌───┴────┐
                                                                │ Router2│ (2911)
                                                                └───┬────┘
                                                                    │
    ┌──────────────────────────────────────────────────────┐     ┌───┴────┐     ┌─────────┐
    │                Main Core Cluster                    │◀────┤Router0 │────▶│ Router1 │
    │                                                      │     └───┬────┘     └───┬─────┘
    └──────────────────────────────────────────────────────┘         │              │
         │      │          │              │                        ┌───────────┴───────────┐
         │      │          │              │                        │ Smaller Campus Switch │ 
         │      │          │              │                        └───────────┬───────────┘
         │      │          │              │                                    │
┌────────┴┐ ┌───┴─────┐┌───┴───────┐┌─────┴─────┐                   ┌────────┴───────┐
│Building A││Building B││Building C││ Building C│                   │Small Building │
│Switch18  ││Switch19  ││Switch20  ││Switch21   │                   │Switch (2960)  │
└────┬─────┘└────┬─────┘└────┬─────┘└────┬─────┘                    └────┬──────────┘
     │           │           │           │                               │
     ▼           ▼           ▼           ▼                               ▼
Departments  E&C & Art  Student Lab  IT / Web & FTP           Staff & Student Lab
Switches      & Design  & Printer    Servers & Printers         & Printer
```

> **Note:** Replace the ASCII diagram with a detailed image at `/docs/campus-topology.png`.

---

## 🏢 Building-by-Building Breakdown

### Building A (192.168.1.0/24)

* **Switch 18 (2960-24TT)** connects four department switches:

  * **Management** (PC0: 192.168.1.10)
  * **HR** (PC1: 192.168.2.0)
  * **Finance** (PC2: 192.168.3.0)
  * **Business** (PC3: 192.168.4.0)
* Each department switch uplinks to Switch18; each PC has a local printer.

### Building B (192.168.5.0/24)

* **Switch 19 (2960-24TT)** connects two departments:

  * **Electronics & Communication** (PC4: 192.168.5.0)
  * **Art & Design** (PC5: 192.168.6.0)
* Printers at each department; VLAN IDs 50 & 60 respectively.

### Building C (192.168.7.0/24 & 192.168.8.0/24)

* **Switch 20 (2960-24TT)**:

  * **Student Lab** (PC6: 192.168.7.0)
  * Printer6 for student use
* **Switch 21 (2960-24TT)** in IT room:

  * **Web Server** (Server-PT: IP 192.168.8.0, VLAN 80)
  * **FTP Server** (Server-PT: IP 192.168.9.0)

### Smaller Campus Network (192.168.9.0/24 & 192.168.10.0/24)

* **Multilayer Switch (3650-24PS)** uplinks to core, serves two switches:

  * **Switch18 Replica**: Staff PC (PC8: 192.168.9.0, VLAN 90), Printer8
  * **Switch19 Replica**: Student PC (PC9: 192.168.10.0, VLAN 100), Printer9

### Small Building (Adjacent Campus) (192.168.11.0/24)

* **Switch (2960)** connecting:

  * Staff workstation
  * Student lab PC
  * Dedicated printer

---

## 🎯 IP Addressing & VLAN Design

| Location              | Subnet          | VLAN ID | Devices              |
| --------------------- | --------------- | ------- | -------------------- |
| Building A Management | 192.168.1.0/24  | 10      | PC0, Printer0        |
| Building A HR         | 192.168.2.0/24  | 20      | PC1, Printer1        |
| Building A Finance    | 192.168.3.0/24  | 30      | PC2, Printer2        |
| Building A Business   | 192.168.4.0/24  | 40      | PC3, Printer3        |
| Building B E\&C       | 192.168.5.0/24  | 50      | PC4, Printer4        |
| Building B Art & Des. | 192.168.6.0/24  | 60      | PC5, Printer5        |
| Building C Student    | 192.168.7.0/24  | 70      | PC6, Printer6        |
| Building C IT/Web     | 192.168.8.0/24  | 80      | WebServer, FTPServer |
| Smaller Campus Staff  | 192.168.9.0/24  | 90      | PC8, Printer8        |
| Smaller Campus Stud.  | 192.168.10.0/24 | 100     | PC9, Printer9        |
| Small Building        | 192.168.11.0/24 | 110     | PC10, Printer10      |

---

## 🔧 Key Hardware Components

* **Cisco 2911 Routers** (Router0, Router1, Router2): Inter-building routing, static and dynamic protocols
* **Cisco 3850-24PS Core Switch**: Multilayer switching, ACLs, VLANs, QoS
* **Cisco 3650-24PS**: Smaller campus distribution with Layer 3 capabilities
* **Cisco 2960-24TT Switches**: Department and lab access switches
* **Servers**:

  * Email Server on 20.0.0.0/30 segment
  * Web Server, FTP Server in IT room
* **Fiber/Copper Trunks**: 10 Gbps links between core and distribution

---

## 🔄 Data Flow and Routing

1. **Inter-VLAN Traffic** (e.g., Finance → Web Server):

   * PC2 ↔ Dept Switch → Core Switch → IT Room Switch → Web Server
2. **Inter-Building Traffic** (e.g., Management → Art & Design):

   * PC0 → Switch18 → Core Router → Switch19 → PC5
3. **Email Delivery**:

   * Any PC → Core Router → Router2 → Email Server
4. **Internet Access**:

   * All user VLANs → Core Router → Campus Backbone → Internet

---
## IMAGES:
**NETWORK**
![Screenshot 2025-06-08 003325](https://github.com/user-attachments/assets/407e1b46-b336-4626-9ec3-6904b9af1363)

**MAIN CAMPUS NETWORK**
![Screenshot 2025-06-08 004125](https://github.com/user-attachments/assets/ad18e49c-88df-4f03-8b03-1f40af3a70ac)

**SMALL CAMPUS NEWORK**
![Screenshot 2025-06-08 004130](https://github.com/user-attachments/assets/07af247a-775e-4636-99ea-474159dafce0)

**ROUTER SETUP**
![Screenshot 2025-06-08 004134](https://github.com/user-attachments/assets/622165c0-49fd-4aa7-bd85-d93e9fdb9c62)

---

## 🔒 Security & Management

* **VLAN Segmentation**: Separates traffic to limit broadcast domains and improve security
* **ACLs on Core**: Permit only required traffic between VLANs and to servers
* **Port Security**: On access switches to lock MAC addresses per port
* **SNMP & NetFlow**: Central monitoring using NMS software
* **Redundancy**: Secondary uplinks between core and distribution to prevent single points of failure

---

## 🚀 Benefits & Scalability

* **Modular Design**: New buildings or departments add VLANs and switches without redesign
* **High Performance**: 10 Gbps trunks and Layer 3 switching minimize latency
* **Easy Troubleshooting**: Clear segmentation and monitoring pinpoint issues quickly
* **Strong Security Posture**: VLANs, ACLs, and port security protect sensitive data

---

## 🛠️ Getting Started

1. **Clone Repository**:

   ```bash
   git clone https://github.com/your-username/university-network-project.git
   cd university-network-project
   ```
2. **Review Documentation**:

   * `/docs/campus-topology.png`
   * `/docs/configs/` for router & switch config samples
3. **Simulate in Packet Tracer**:

   * Open `packet_tracer/university_network.pkt`
4. **Deploy to Live Hardware**:

   * Upload configs to each router/switch via SSH

