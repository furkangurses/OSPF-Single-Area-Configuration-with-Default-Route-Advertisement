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

![Topology Diagram]()


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



