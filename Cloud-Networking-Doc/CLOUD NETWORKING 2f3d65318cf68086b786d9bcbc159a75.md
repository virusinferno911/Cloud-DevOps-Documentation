# CLOUD NETWORKING

---

## Module 1: Fundamentals of Networking

### **1.1 What is a Network?**

A network is a system connecting two or more computing devices to facilitate communication and resource sharing.

- **Communication:** Exchange of data (e.g., Emails, Instant Messaging, Video Calls).
- **Resource Sharing:** Joint access to hardware (Printers, Scanners) or Software (Databases, File Servers).

### **1.2 Key Hardware Components**

| **Component** | **Function** | **Analogy** |
| --- | --- | --- |
| **NIC (Network Interface Card)** | The physical hardware connecting a device to the network. | The "Mouth" of the computer. |
| **MAC Address** | A unique, 48-bit physical identifier burned into the NIC by the manufacturer. It is permanent and unchangeable. | The computer's "Fingerprint". |
| **Transmission Media** | The physical path data travels (Ethernet Cables, Fiber Optics, Wi-Fi). | The "Roads". |

### **1.3 Evolution of Network Devices**

- **Hub (Layer 1):** A "dumb" device that broadcasts data to **everyone** on the network.
    - *Drawback:* High noise, lack of privacy, and frequent data collisions.
- **Switch (Layer 2):** An "intelligent" device that uses a **MAC Address Table**.
    - *Function:* It learns where each device is seated and sends data **only** to the specific destination.
- **Router (Layer 3):** The "Gateway" device.
    - *Function:* Connects **different** networks together (e.g., your home LAN to the Internet WAN). Without a router, you cannot leave your local network.

---

## Module 2: IP Addressing & Architecture

### **2.1 IP Address Structure**

- **IPv4:** A 32-bit address system (e.g., `192.168.1.1`). It allows for approx 4.3 billion unique addresses.
- **IPv6:** A 128-bit address system (Hexadecimal). Created to solve the exhaustion of IPv4 addresses.

### **2.2 The 5 IP Classes**

IPs are categorized by the size of the network they are designed for.

| **Class** | **First Octet Range** | **Intended Use** | **Default Subnet Mask** |
| --- | --- | --- | --- |
| **Class A** | `1 - 126` | Large Organizations (16M hosts) | `255.0.0.0` (/8) |
| **Class B** | `128 - 191` | Medium Organizations (65K hosts) | `255.255.0.0` (/16) |
| **Class C** | `192 - 223` | Small Networks/Home (254 hosts) | `255.255.255.0` (/24) |
| **Class D** | `224 - 239` | Multicasting | N/A |
| **Class E** | `240 - 255` | R&D / Experimental | N/A |

### **2.3 Communication Types**

- **Unicast:** One-to-One (Direct Message).
- **Broadcast:** One-to-All (Shouting to the room).
- **Multicast:** One-to-Group (Subscription list).
- **Anycast:** One-to-Nearest (Routing to the closest available server).

---

## Module 3: The Mathematics of Private IPs

### **3.1 The "Magic Number" 255**

An IP address consists of 4 octets. Each octet has 8 bits.

If all 8 bits are turned ON (11111111), the decimal value is calculated as:

128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 = 255

- **Rule:** An octet value can never exceed 255.

### **3.2 Private IP Ranges (RFC 1918)**

These ranges are reserved for internal use (Home/Office) and cannot be routed on the public internet.

| **Class** | **Range Start** | **Range End** | **CIDR** | **The Logic (Why it ends there)** |
| --- | --- | --- | --- | --- |
| **Class A** | `10.0.0.0` | `10.255.255.255` | **/8** | First octet (`10`) is frozen. The remaining 3 octets are free to range from 0–255. |
| **Class B** | `172.16.0.0` | `172.31.255.255` | **/12** | First **12 bits** are frozen. In the second octet, this locks the binary value between `00010000` (16) and `00011111` (31). |
| **Class C** | `192.168.0.0` | `192.168.255.255` | **/16** | First two octets (`192.168`) are frozen. The remaining 2 octets are free to range from 0–255. |

---

## Module 4: Advanced Concepts

### **4.1 Subnetting (CIDR Classless Inter-domain router )**

- **Definition:** The practice of dividing a single large network into smaller, efficient sub-networks to prevent IP wastage.
- **Analogy:** Slicing a large pizza into specific sizes rather than forcing one person to eat the whole thing.
- **Mechanism:** Borrowing bits from the **Host portion** to give to the **Network portion**.

### **4.2 The OSI Model (7 Layers)**

A standard framework for how data is transmitted.

1. **Physical:** Cables, signals, binary.
2. **Data Link:** MAC Addresses, Switches.
3. **Network:** IP Addresses, Routers.
4. **Transport:** TCP (Reliable) vs UDP (Fast).
5. **Session:** Establishing/Ending connections.
6. **Presentation:** Encryption, Formatting (JPEG, ASCII).
7. **Application:** User Interface (Chrome, Outlook).

### **4.3 Ports & Protocols**

An IP Address connects to the **Computer**; a Port Number connects to the **Application**.

- **Port 20/21 (FTP):** File Transfer Protocol.
- **Port 22 (SSH):** Secure Shell (Remote Login).
- **Port 53 (DNS):** Domain Name System.
- **Port 80 (HTTP):** Web Traffic (Unsecured).
- **Port 443 (HTTPS):** Web Traffic (Secured).
- **Port 3306 (SQL):** Database Connection.

### **4.4 Traffic Flow**

- **North-South:** Traffic entering/leaving the datacenter (External). *Needs Firewalls.*
- **East-West:** Traffic moving between servers inside the datacenter (Internal). *Needs Segmentation.*