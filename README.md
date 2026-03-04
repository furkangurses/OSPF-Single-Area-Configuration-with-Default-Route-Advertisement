![Topology Diagram](https://github.com/furkangurses/OSPF-Single-Area-Configuration-with-Default-Route-Advertisement/blob/main/1.PNG?raw=true)

# OSPF Single-Area Configuration with Default Route Advertisement

---

## 🎯 Lab Objective

This lab demonstrates the configuration of **OSPF in a single-area topology (Area 0)**, including:

- Loopback interface configuration
- OSPF activation using different `network` statements
- Passive-interface implementation
- Default route advertisement
- Basic OSPF verification and analysis

The goal is to understand how OSPF operates in a small enterprise network and how default routing is propagated.

---

## 🌐 Topology Overview

- 4 Routers (R1, R2, R3, R4)
- Single OSPF Area (Area 0)
- Loopback interface on each router (used for Router ID stability)
- R1 connected to an ISP router
- R1 configured as **ASBR** (redistributes default route)

Key Design Points:

- Loopbacks use /32 masks
- OSPF not enabled on ISP-facing interface
- Passive interfaces configured where neighbor adjacency is not required
- Default route distributed from R1 to internal routers

---

## ⚙️ Configuration Steps

1. Configure basic hostname and IP addressing.
2. Create Loopback0 interface on each router.
3. Enable OSPF with different process IDs (local significance).
4. Activate OSPF on interfaces using various `network` statement methods.
5. Configure passive interfaces where appropriate.
6. Configure static default route on R1.
7. Advertise default route using `default-information originate`.
8. Verify neighbor adjacency, LSDB, and routing tables.

---

## 📝 Commands Used

```
enable
configure terminal

interface loopback0
 ip address X.X.X.X 255.255.255.255

router ospf <process-id>
 network 0.0.0.0 255.255.255.255 area 0
 network <ip> 0.0.0.0 area 0
 network <subnet> <wildcard-mask> area 0

passive-interface loopback0
passive-interface g0/0

ip route 0.0.0.0 0.0.0.0 203.0.113.2
default-information originate

show ip interface brief
show interface loopback0
show ip protocols
show ip ospf neighbor
show ip ospf database
show ip ospf interface
show ip route
```

---

## 🧠 Technical Explanation

This lab teaches how OSPF:

- Builds neighbor relationships using Hello packets
- Forms a Link-State Database (LSDB)
- Calculates best paths using SPF (Dijkstra algorithm)
- Selects Router ID (highest loopback IP if not manually set)
- Supports Equal-Cost Multi-Path (ECMP)

Key Technical Concepts:

- **Process ID is locally significant**
- `network` command activates OSPF on matching interfaces
- `passive-interface` stops Hello packets but still advertises the network
- `default-information originate` requires an existing default route
- R1 becomes an **ASBR** by injecting external default route
- R4 performs load balancing due to equal OSPF cost paths

---

## 🌍 Real-World Use Case

This scenario represents:

- A headquarters router (R1) connected to the Internet
- Branch routers (R2, R3, R4)
- Centralized internet breakout
- Internal OSPF domain
- Default route distribution from core to access layer

Common in:

- Enterprise campus networks
- Multi-branch corporate environments
- Hub-and-spoke designs

---

## 🛠️ Skills Gained

- OSPF single-area deployment
- Loopback configuration for Router ID stability
- Understanding wildcard masks
- Passive interface implementation
- Default route propagation
- OSPF verification and troubleshooting
- Recognizing ECMP behavior
- Identifying ASBR functionality

---

## ⚡ Possible Improvements

- Implement multi-area OSPF design
- Configure OSPF authentication
- Adjust OSPF reference bandwidth
- Introduce route summarization
- Add stub or NSSA area configuration
- Simulate link failure and convergence testing

---

## 🧩 Troubleshooting Notes

Common issues and checks:

- No neighbor adjacency → Check Area, subnet match, timers
- Default route not advertised → Ensure static default route exists
- Incorrect Router ID → Clear OSPF process after changes
- No route in routing table → Verify `network` statement match
- Unexpected load balancing → Verify OSPF cost calculation

Verification Commands:

```
show ip ospf neighbor
show ip route
show ip protocols
show ip ospf database

```

---


![Topology Diagram](https://github.com/furkangurses/OSPF-Single-Area-Configuration-with-Default-Route-Advertisement/blob/main/2.PNG?raw=true)


# OSPF Interface-Based Configuration & Advanced Features Lab

---

## 🎯 Lab Objective

This lab demonstrates advanced OSPF configuration techniques using interface-based activation instead of the traditional `network` command.

Key objectives:

- Configure OSPF directly on interfaces
- Implement passive interfaces
- Modify OSPF reference bandwidth
- Advertise a default route into OSPF
- Analyze OSPF Hello packets at Layer 3

---

## 🌐 Topology Overview

**Routers:** R1, R2, R3, R4  
**Area:** OSPF Area 0  
**Protocol:** OSPFv2 (Process ID 1)

- R1 connected to ISP (203.0.113.2)
- Internal links use FastEthernet and GigabitEthernet
- Loopback0 configured on each router
- R4 connected to R2 and R3

All routers participate in Area 0.

---

## ⚙️ Configuration Steps

### 1️⃣ Enable OSPF Directly on Interfaces

Instead of using the `network` command, OSPF was enabled explicitly per interface using:

- `interface range`
- `ip ospf 1 area 0`

Loopback interfaces were included.

---

### 2️⃣ Configure Passive Interfaces

Loopback interfaces were configured as passive:

- Prevents Hello packets
- Advertises connected network
- Improves security and efficiency

On R4, `passive-interface default` was tested and selectively removed from active interfaces.

---

### 3️⃣ Adjust Reference Bandwidth

Configured:

- auto-cost reference-bandwidth 10000


Reason:

OSPF Cost = Reference Bandwidth / Interface Bandwidth

With 10000 Mbps reference:

- FastEthernet (100 Mbps) → Cost 100
- GigabitEthernet (1000 Mbps) → Cost 10

Ensures proper path selection in modern networks.

---

### 4️⃣ Advertise Default Route

On R1:

- Enabled OSPF default advertisement
- Configured static default route toward ISP

Observed external OSPF Type 2 (E2) route behavior.

---

### 5️⃣ Analyze OSPF Hello Packet

Simulation mode was used to inspect:

- Multicast destination: 224.0.0.5
- Protocol number: 89 (0x59 hex)
- OSPF Version: 2
- Message Type: 1 (Hello)
- Default timers: Hello 10s / Dead 40s
- Area ID: 0.0.0.0

---

## 📝 Commands Used

- enable
- conf t

- interface range g0/0, f1/0, l0
- ip ospf 1 area 0

- router ospf 1
- passive-interface l0

- router ospf 1
- passive-interface default
- no passive-interface f1/0
- no passive-interface f2/0

- auto-cost reference-bandwidth 10000

- default-information originate

- ip route 0.0.0.0 0.0.0.0 203.0.113.2

- show ip protocols
- show ip ospf neighbor
- show ip ospf interface
- show ip route


---

## 🧠 Technical Explanation

This lab teaches:

- Interface-based OSPF activation for precise control
- How passive interfaces prevent neighbor formation while advertising routes
- Importance of reference bandwidth consistency across routers
- Behavior of OSPF External Type 2 (E2) routes
- OSPF packet structure at Layer 3

It reinforces both configuration skills and protocol-level understanding.

---

## 🌍 Real-World Use Case

- Enterprise edge router advertising internet default route
- Optimizing OSPF cost in mixed-speed environments
- Securing routing by limiting unnecessary adjacencies
- Troubleshooting neighbor state changes
- Understanding external route behavior in production networks

---

## 🛠️ Skills Gained

- Advanced OSPF configuration
- Interface-level routing control
- OSPF metric calculation
- External route analysis (E2)
- Neighbor state troubleshooting
- Packet-level protocol inspection
- Routing table validation

---

## ⚡ Possible Improvements

- Replicate lab in GNS3 or real hardware for accurate ECMP behavior
- Implement OSPF authentication
- Introduce multi-area OSPF design
- Compare E1 vs E2 external routes
- Add route summarization

---

## 🧩 Troubleshooting Notes

- Passive interfaces immediately drop neighbors
- All routers must share identical reference bandwidth
- Default route requires both static route and `default-information originate`
- Packet Tracer may not install equal-cost external routes correctly
- OSPF Hello multicast address: 224.0.0.5
- OSPF protocol number: 89

---

![Topology Diagram](https://github.com/furkangurses/OSPF-Single-Area-Configuration-with-Default-Route-Advertisement/blob/main/3.PNG?raw=true)


# OSPF Configuration and Troubleshooting Lab

---

## 🎯 Lab Objective

This lab focuses on configuring and troubleshooting **OSPF (Open Shortest Path First)** in a multi-router topology.

Objectives:

- Configure a serial link between R1 and R2
- Enable OSPF on required interfaces
- Identify and fix OSPF adjacency issues
- Resolve network type mismatches
- Fix OSPF timer inconsistencies
- Configure and advertise a default route
- Verify LSDB consistency

---

## 🌐 Topology Overview

- 5 Routers (R1–R5)
- Serial connection between **R1–R2**
- Ethernet multi-access networks
- OSPF Area 0 used throughout the topology
- R5 connected to an external network (Internet simulation: `8.8.8.8`)
- PCs used for end-to-end connectivity testing

Key Networks:

- 192.168.12.0/30 (R1–R2 Serial)
- 192.168.34.0/30 (R3–R4)
- 192.168.245.0/29 (R2–R4–R5)
- 10.0.2.0/24 (Internal LAN)
- 0.0.0.0/0 (Default route via R5)

---

## ⚙️ Configuration Steps

### 1️⃣ Configure Serial Link (R1–R2)

- Assign IP addresses
- Configure clock rate on DCE side
- Enable interfaces

R1:
- `192.168.12.1/30`
- Clock rate 128000 (DCE)

R2:
- `192.168.12.2/30`

---

### 2️⃣ Enable OSPF on Interfaces

- OSPF Process ID: 1
- Area: 0
- Enabled directly on interfaces using:
  
```
ip ospf 1 area 0
```

---

### 3️⃣ Fix Missing Route to 10.0.2.0/24

Problem:
- R4 had OSPF adjacency with R3 but did not learn route.

Root Cause:
- Network type mismatch:
  - R3 → point-to-point
  - R4 → broadcast

Solution:
- Remove point-to-point configuration from R3

---

### 4️⃣ Fix OSPF Neighbor Issue with R5

Problem:
- R2 and R4 were not forming adjacency with R5.

Root Cause:
- OSPF timer mismatch
  - R5 Hello: 5
  - R5 Dead: 20
  - Others: 10/40 (default)

Solution:
- Reset R5 timers to default

---

### 5️⃣ Fix Default Route Advertisement

Problem:
- PCs could not ping 8.8.8.8

Root Cause:
- R5 configured `default-information originate`
- But no default route existed in routing table

Solution:
- Configure static default route:
```
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

---

## 📝 Commands Used

```bash
enable
configure terminal
interface s0/0/0
ip address 192.168.12.1 255.255.255.252
clock rate 128000
no shutdown

ip address 192.168.12.2 255.255.255.252
no shutdown

ip ospf 1 area 0
no ip ospf network point-to-point

no ip ospf hello-interval
no ip ospf dead-interval

ip route 0.0.0.0 0.0.0.0 203.0.113.2

show ip interface brief
show controllers s0/0/0
show ip protocols
show ip ospf interface
show ip ospf neighbor
show ip route
show ip ospf database
show running-config | section ospf

ping 10.0.2.1
ping 8.8.8.8
```

---

## 🧠 Technical Explanation

This lab demonstrates core OSPF operational concepts:

- OSPF adjacency formation requirements
- Network type impact (Broadcast vs Point-to-Point)
- Hello/Dead timer consistency
- DR/BDR election behavior
- LSDB synchronization
- LSA Types:
  - Type 1 → Router LSA
  - Type 2 → Network LSA
  - Type 5 → AS External LSA
- Default route injection using `default-information originate`

It reinforces that **OSPF requires exact parameter matching** to form stable neighbor relationships.

---

## 🌍 Real-World Use Case

In enterprise networks:

- Timer mismatches commonly break OSPF adjacency
- Network type misconfiguration causes partial routing failures
- Default route advertisement is critical for Internet access
- Engineers must validate LSDB consistency across routers

This lab simulates real-world troubleshooting scenarios faced by:

- Network Engineers
- NOC Engineers
- CCNA-level Administrators

---

## 🛠️ Skills Gained

- OSPF interface-based configuration
- Serial DCE/DTE understanding
- Troubleshooting adjacency issues
- Identifying network type mismatches
- Diagnosing timer inconsistencies
- Default route configuration and redistribution
- LSDB analysis
- End-to-end connectivity validation

---

## ⚡ Possible Improvements

- Convert interface-based OSPF config to network statements for consistency
- Implement passive interfaces where appropriate
- Add authentication (OSPF MD5)
- Implement multi-area OSPF design
- Add route summarization
- Simulate link failure and convergence testing

---

## 🧩 Troubleshooting Notes

Common OSPF adjacency failure causes:

- Area mismatch
- Subnet mismatch
- Hello/Dead timer mismatch
- Network type mismatch
- Missing `no shutdown`
- Missing default route for redistribution

Verification strategy:

1. Check interfaces
2. Verify OSPF neighbors
3. Confirm routing table
4. Inspect LSDB
5. Test with ping/traceroute

---

**Lab Level:** CCNA  
**Focus Area:** OSPF Configuration & Troubleshooting  
**Environment:** Cisco IOS (Simulated Lab)


