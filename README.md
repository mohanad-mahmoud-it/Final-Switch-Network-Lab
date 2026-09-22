<div align="center">

# 🌐 Final Switch Network Lab

**Cisco Packet Tracer — Enterprise Networking Laboratory**

`VLANs` · `802.1Q` · `LACP EtherChannel` · `PVST` · `Port Security` · `DHCP` · `Inter-VLAN Routing`

</div>

<br>

---

## 📌 Project Overview

**Final Switch Network Lab** is a Cisco Packet Tracer project that simulates a small enterprise network built around **VLAN segmentation**, redundant switch-to-switch trunking, and centralized routing.

The lab implements two departmental VLANs — **IT** and **Sales** — across two access switches, connected through a **LACP EtherChannel** trunk and secured with **Port Security**. A router configured for **Router-on-a-Stick** handles inter-VLAN routing and DHCP services for both VLANs.

Every stage of the design is backed by real command output captured directly from the running lab.

---

## 🏗️ Architecture Overview

| Component | Role |
|---|---|
| **R-Mans** | Router — Router-on-a-Stick, inter-VLAN routing, DHCP server |
| **SW1-LAB1** | Access/Distribution switch — VLANs, EtherChannel, Port Security |
| **SW2-LAB2** | Access switch — VLANs, EtherChannel |
| `VLAN 10` | IT department network |
| `VLAN 20` | Sales department network |

The two switches are interconnected using a **LACP EtherChannel** trunk carrying both VLANs over **802.1Q**, while the router terminates both VLANs on subinterfaces to provide routing between them.

<p align="center">
  <img src="./screenshots/01-network-topology%283%29.png" alt="Cisco Packet Tracer network topology" width="850">
</p>

<p align="center"><em>Real Cisco Packet Tracer topology used throughout this lab.</em></p>

---

## 🧩 VLAN Architecture

| VLAN ID | Name | Network | Gateway |
|---|---|---|---|
| `10` | IT | `192.168.10.0/24` | `192.168.10.1` |
| `20` | Sales | `192.168.20.0/24` | `192.168.20.1` |

**SW1-LAB1 Management IP:** `192.168.1.50`

The VLAN configuration was verified using the Cisco IOS VLAN table.

![VLAN Configuration](./screenshots/02-vlan-configuration%282%29.png)

---

## 🔗 IEEE 802.1Q Trunking

Trunk links between the switches and toward the router were configured to carry traffic for both `VLAN 10` and `VLAN 20` using **802.1Q** tagging.

![802.1Q Trunking](./screenshots/03-8021q-trunking.png)

> 🔗 **Trunking:** Both VLANs tagged and carried across all inter-switch and switch-to-router links.

---

## ⚡ LACP EtherChannel

Interfaces `Fa0/23` and `Fa0/24` on both switches were bundled into a single logical link, `Po1`, negotiated dynamically using **LACP**. This provides increased bandwidth and link redundancy between `SW1-LAB1` and `SW2-LAB2`.

![LACP EtherChannel](./screenshots/04-lacp-etherchannel.png)

Port-channel status confirms the EtherChannel is active and forwarding.

![Port-Channel Status](./screenshots/14-port-channel-status.png)

> ⚡ **LACP EtherChannel:** `Po1` using `Fa0/23` and `Fa0/24` — active and forwarding.

---

## 🌳 Spanning Tree Protocol (PVST)

Spanning Tree runs in **Per-VLAN Spanning Tree (PVST)** mode, preventing Layer 2 loops while maintaining an independent topology calculation for each VLAN.

![Spanning Tree Overview](./screenshots/05-spanning-tree-overview.png)

Root bridge and port roles were verified independently for each VLAN.

![Spanning Tree – VLAN 10](./screenshots/06-spanning-tree-vlan10.png)

![Spanning Tree – VLAN 20](./screenshots/07-spanning-tree-vlan20.png)

---

## 🛡️ Port Security

Access ports were secured using **Sticky MAC** address learning with a **Restrict** violation mode, allowing only learned MAC addresses to forward traffic while logging any violation without shutting down the port.

![Port Security Verification](./screenshots/09-port-security-verification.png)

> 🛡️ **Security:** Sticky MAC learning with Restrict violation mode on all access ports.

---

## ⚙️ Switch Configuration

The running configuration on the switches reflects VLAN assignments, trunk settings, EtherChannel members, and port security policy.

![Switch Running Configuration – Part 1](./screenshots/10-switch-running-config-1.png)

![Switch Running Configuration – Part 2](./screenshots/11-switch-running-config-2.png)

Interface and MAC address table status confirm correct port operation and learned host addresses.

![Interface Status](./screenshots/13-interface-status.png)

![MAC Address Table](./screenshots/12-mac-address-table.png)

---

## 🌐 Router-on-a-Stick

Router `R-Mans` uses subinterfaces to route between VLANs over a single trunked physical link:

| Subinterface | IP Address | Purpose |
|---|---|---|
| `G0/1.1` | `192.168.1.1` | Native / management |
| `G0/1.10` | `192.168.10.1` | VLAN 10 gateway (IT) |
| `G0/1.20` | `192.168.20.1` | VLAN 20 gateway (Sales) |

![Router Interface Status](./screenshots/15-router-interface-status.png)

![IP Interface Status](./screenshots/08-ip-interface-status.png)

---

## 📡 DHCP Services

`R-Mans` provides DHCP addressing for both VLANs through two separate pools, each supplying a default gateway and DNS server (`8.8.8.8`).

![DHCP Configuration – Part 1](./screenshots/16-router-dhcp-configuration-1.png)

![DHCP Configuration – Part 2](./screenshots/17-router-dhcp-configuration-2.png)

Active DHCP bindings confirm successful address assignment to connected hosts.

![DHCP Bindings](./screenshots/18-dhcp-bindings.png)

---

## 🛣️ Routing

The router's routing table shows directly connected routes for `VLAN 10` and `VLAN 20`, enabling full inter-VLAN reachability.

![Routing Table](./screenshots/19-routing-table.png)

---

## 🧪 Connectivity Validation

Inter-VLAN connectivity was tested between hosts on `VLAN 10` and `VLAN 20`, confirming successful routing through `R-Mans`.

![Inter-VLAN Connectivity Test](./screenshots/20-inter-vlan-connectivity-test.png)

> ✅ **Result:** 4 packets sent · 4 packets received · **0% packet loss**

A sample host on `VLAN 20` received its configuration via DHCP, confirming correct addressing, gateway, and DNS.

![PC Network Configuration](./screenshots/21-pc-network-configuration.png)

| Parameter | Value |
|---|---|
| IP Address | `192.168.20.3` |
| Default Gateway | `192.168.20.1` |
| DNS Server | `8.8.8.8` |

---

## 🧠 Verification Commands

| Command | Purpose |
|---|---|
| `show vlan brief` | Display VLAN assignments |
| `show interfaces trunk` | Verify 802.1Q trunk status |
| `show etherchannel summary` | Verify LACP EtherChannel state |
| `show spanning-tree` | Verify PVST topology and roles |
| `show port-security` | Verify port security status |
| `show mac address-table` | View learned MAC addresses |
| `show ip interface brief` | Verify interface and subinterface status |
| `show ip dhcp binding` | View active DHCP leases |
| `show ip route` | Verify routing table |

---

## 🧰 Technologies & Concepts

`VLAN Segmentation` · `IEEE 802.1Q Trunking` · `LACP EtherChannel` · `PVST` · `Port Security (Sticky MAC / Restrict)` · `Router-on-a-Stick` · `DHCP` · `Inter-VLAN Routing` · `IP Addressing`

---

## 🎯 Learning Objectives

- Implement **VLAN segmentation** across multiple switches
- Configure and verify **802.1Q trunking**
- Build a **LACP EtherChannel** for link aggregation and redundancy
- Configure **PVST** and interpret per-VLAN spanning tree roles
- Apply **Port Security** using Sticky MAC and Restrict violation mode
- Configure **Router-on-a-Stick** for inter-VLAN routing
- Deploy and verify **DHCP services** for multiple VLANs
- Validate end-to-end connectivity and troubleshoot Layer 2/3 issues

---

## 📁 Project Structure

```
Final-Switch-Network-Lab/
├── FINAL-SW(1).pkt
└── screenshots/
    ├── 01-network-topology(3).png
    ├── 02-vlan-configuration(2).png
    ├── 03-8021q-trunking.png
    ├── 04-lacp-etherchannel.png
    ├── 05-spanning-tree-overview.png
    ├── 06-spanning-tree-vlan10.png
    ├── 07-spanning-tree-vlan20.png
    ├── 08-ip-interface-status.png
    ├── 09-port-security-verification.png
    ├── 10-switch-running-config-1.png
    ├── 11-switch-running-config-2.png
    ├── 12-mac-address-table.png
    ├── 13-interface-status.png
    ├── 14-port-channel-status.png
    ├── 15-router-interface-status.png
    ├── 16-router-dhcp-configuration-1.png
    ├── 17-router-dhcp-configuration-2.png
    ├── 18-dhcp-bindings.png
    ├── 19-routing-table.png
    ├── 20-inter-vlan-connectivity-test.png
    └── 21-pc-network-configuration.png
```

---

<div align="center">

## 👤 Author

### **Mohanad Mahmoud**

IT Support Engineer & IT Instructor

`Networking` · `Windows Server` · `Network Security`

📍 Mansoura, Egypt

LinkedIn · GitHub

</div>
