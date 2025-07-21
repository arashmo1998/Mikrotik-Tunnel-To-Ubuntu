# 🚀 Setting Up a MikroTik to Ubuntu Tunnel with GRE (IPv4 & IPv6)

&#x20;  &#x20;

**This guide helps you establish a secure and stable tunnel between MikroTik and Ubuntu servers.**

---

### 🔥 GRE Tunnel over IPv4 🔥

✨ We'll create a script file containing the necessary commands that runs automatically and remains active even after server reboots.

---

## 🚀 How to Use (IPv4)

### ✅ 1. Foreign Server (Ubuntu)

- First, create the `rc.local` file and make it executable to ensure it runs after every server restart:

```shell
sudo nano /etc/rc.local && sudo chmod +x /etc/rc.local && sudo bash /etc/rc.local
```

- Paste the following content into the file and save it:

> ⚠️ **Note:** `[IPv4-IRAN]` = Public IP of Iran | `[IPv4-FOREIGN]` = Public IP of foreign server

```shell
#!/bin/bash
sudo ip tunnel add gre1 mode gre remote [IPv4-IRAN] local [IPv4-FOREIGN] ttl 255
sudo ip link set gre1 mtu 1420
sudo ip link set gre1 up
sudo ip addr add 10.10.10.2/30 dev gre1
nohup ping 10.10.10.1 &

exit 0
```

---

### ✅ 2. Iranian Server (MikroTik)

Log into the MikroTik terminal and run the following commands to create the GRE tunnel interface and assign the internal IP address:

> ⚠️ **Note:** `[IPv4-IRAN]` = Public IP of Iran | `[IPv4-FOREIGN]` = Public IP of foreign server

```shell
/interface gre add name=gre1 local-address=[IPv4-IRAN] remote-address=[IPv4-FOREIGN] mtu=1420
/ip address add address=10.10.10.1/30 interface=gre1
```

✅ The GRE tunnel between the two servers should now be established. If everything is configured correctly, you will see **R (RUN)** next to the tunnel interface.

---

### 🌐 Internal IP Addresses

| Server     | Internal IP  |
| ---------- | ------------ |
| 🇮🇷 Iran  | `10.10.10.1` |
| 🌍 Foreign | `10.10.10.2` |

---

### ✅ Ping Test

**Iranian Server:**

```shell
ping 10.10.10.2
```

**Foreign Server:**

```shell
ping 10.10.10.1
```

---

### 🔄 Redirect Incoming Traffic from MikroTik to Destination Server

To forward incoming traffic over the tunnel, run the following commands on MikroTik:

> ⚠️ If your `Winbox` port is different, adjust the `dst-port` value accordingly.

```shell
/ip firewall nat add chain=srcnat action=masquerade
/ip firewall nat add chain=dstnat protocol=tcp dst-port=!8291 action=dst-nat to-addresses=10.10.10.2
```

---

## 🔥 GRE Tunnel over IPv6 🔥



✨ This section follows the same steps as IPv4, except using IPv6 addresses to build the tunnel.

---

## 🚀 How to Use (IPv6)

### ✅ 1. Foreign Server (Ubuntu)

Run the following commands to create a GRE tunnel with IPv6:

> ⚠️ **Note:** `[IPv6-IRAN]` = Public IPv6 of Iran | `[IPv6-FOREIGN]` = Public IPv6 of foreign server

```shell
sudo ip -6 tunnel add GRE6Tun_IR mode ip6gre remote [IPv6-IRAN] local [IPv6-FOREIGN]
sudo ip addr add 192.168.55.2/30 dev GRE6Tun_IR
sudo ip link set GRE6Tun_IR mtu 1400
sudo ip link set GRE6Tun_IR up
nohup ping 192.168.55.1 &
```

---

### ✅ 2. Iranian Server (MikroTik)

Log into MikroTik terminal and run the following commands:

> ⚠️ **Note:** `[IPv6-IRAN]` = Public IPv6 of Iran | `[IPv6-FOREIGN]` = Public IPv6 of foreign server

```shell
/interface gre6 add name=GRE6Tun_IR local=[IPv6-IRAN] remote=[IPv6-FOREIGN] mtu=1400
/ip address add address=192.168.55.1/30 interface=GRE6Tun_IR
```

✅ Once configured properly, the interface status should display **R (RUN)**.

---

### 🌐 Internal IP Addresses (IPv6 Tunnel)

| Server     | Internal IP    |
| ---------- | -------------- |
| 🇮🇷 Iran  | `192.168.55.1` |
| 🌍 Foreign | `192.168.55.2` |

---

### ✅ Ping Test

**Iranian Server:**

```shell
ping 192.168.55.2
```

**Foreign Server:**

```shell
ping 192.168.55.1
```

---

### 🔄 Redirect Incoming Traffic from MikroTik to Destination Server (IPv6)

> ⚠️ If your `Winbox` port is different, adjust the `dst-port` value accordingly.

```shell
/ip firewall nat add chain=srcnat action=masquerade
/ip firewall nat add chain=dstnat protocol=tcp dst-port=!8291 action=dst-nat to-addresses=192.168.55.2
```

---

✅ **Author:** [Arash Mohebbati](https://github.com/arashmohebbati)\
⭐ If you found this project helpful, please star the repo!

