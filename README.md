![Topology Diagram]()

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

```
