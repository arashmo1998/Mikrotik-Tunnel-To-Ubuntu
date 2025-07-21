
<div align="center">

# 🚀 فعال سازی تانل میکروتیک به اوبونتو با GRE (IPv4 & IPv6) 🚀

![Ubuntu](https://img.shields.io/badge/Ubuntu-Server-orange?logo=ubuntu&logoColor=white)
![MikroTik](https://img.shields.io/badge/RouterOS-7.x-blue?logo=mikrotik&logoColor=white)
![GRE](https://img.shields.io/badge/Tunnel-GRE-success?logo=linux&logoColor=white)
![IPv4](https://img.shields.io/badge/IPv4-Supported-brightgreen)
![IPv6](https://img.shields.io/badge/IPv6-Optional-lightgrey)

**با این آموزش می‌تونی بین میکروتیک و سرور اوبونتو یه تانل امن و پایدار راه‌اندازی کنی**

---

### 🔥 تانل GRE بر روی IPv4 🔥

✨ از همان ابتدای کار فایلی رو ایجاد میکنیم که در داخل آن دستورات قرار گرفته و پس از ذخیره‌سازی، خودکار اجرا می‌شود و حتی بعد از ریستارت سرور هم دوباره فعال خواهد شد.

</div>

---

## 🚀 نحوه استفاده  (IPv4)

### ✅ 1. سرور خارج (اوبونتو)

- ابتدا با دستور زیر فایل `rc.local` رو ایجاد میکنیم و بهش اجازه دسترسی بعد از هر بار ریستارت شدن سرور رو میدهیم:

```shell
sudo nano /etc/rc.local && sudo chmod +x /etc/rc.local && sudo bash /etc/rc.local
```

- متن زیر را در فایل قرار می‌دهیم و فایل رو ذخیره می‌کنیم:

> ⚠️ **نکته:** `[IPv4-IRAN]` = آی‌پی پابلیک ایران | `[IPv4-KHAREJ]` = آی‌پی پابلیک خارج

```shell
#!/bin/bash
sudo ip tunnel add gre1 mode gre remote [IPv4-IRAN] local [IPv4-KHAREJ] ttl 255
sudo ip link set gre1 mtu 1420
sudo ip link set gre1 up
sudo ip addr add 10.10.10.2/30 dev gre1
nohup ping 10.10.10.1 &

exit 0
```

---

### ✅ 2. سرور ایران (میکروتیک)

وارد ترمینال میکروتیک بشین و دستورات زیر را وارد کنید تا خودکار اینترفیس تانل و آیپی آدرس داخلی رو ایجاد کند:

> ⚠️ **نکته:** `[IPv4-IRAN]` = آی‌پی پابلیک ایران | `[IPv4-KHAREJ]` = آی‌پی پابلیک خارج

```shell
/interface gre add name=gre1 local-address=[IPv4-IRAN] remote-address=[IPv4-KHAREJ] mtu=1420
/ip address add address=10.10.10.1/30 interface=gre1
```

✅ تانل GRE بین دو سرور خارج و ایران برقرار شده و اگر همه چیز درست باشه در مقابل اینترفیس تانل کلمه **R (RUN)** رو می‌بینید.

---

<div align="center">

### 🌐 آیپی‌های داخلی

| سرور | آیپی داخلی |
|------|------------|
| 🇮🇷 ایران | `10.10.10.1` |
| 🌍 خارج | `10.10.10.2` |

</div>

---

### ✅ تست پینگ

**سرور ایران:**

```shell
ping 10.10.10.2
```

**سرور خارج:**

```shell
ping 10.10.10.1
```

---

### 🔄 ارسال ترافیک ورودی میکروتیک به سرور مقصد

برای انتقال ترافیک بر روی تانل وارد ترمینال میکروتیک بشین و دستورات زیر رو وارد کنید:

> ⚠️ اگر پورت `Winbox` شما متفاوت است، مقدار `dst-port` را تغییر دهید.

```shell
/ip firewall nat add chain=srcnat action=masquerade
/ip firewall nat add chain=dstnat protocol=tcp dst-port=!8291 action=dst-nat to-addresses=10.10.10.2
```


---

<div align="center">

## 🔥 تانل GRE بر روی IPv6 🔥

![IPv6](https://img.shields.io/badge/IPv6-Full%20Support-blueviolet?logo=ipv6&logoColor=white)

✨ این بخش دقیقاً مشابه مراحل IPv4 است و تنها تفاوت آن استفاده از آدرس‌های IPv6 برای ساخت تانل است.

</div>

---

## 🚀 نحوه استفاده (IPv6)

### ✅ 1. سرور خارج (اوبونتو)

- ابتدا دستور زیر را اجرا کنید تا تانل GRE با IPv6 ساخته شود:

> ⚠️ **نکته:** `[IPv6-IRAN]` = آی‌پی پابلیک IPv6 ایران | `[IPv6-KHAREJ]` = آی‌پی پابلیک IPv6 خارج

```shell
sudo ip -6 tunnel add GRE6Tun_IR mode ip6gre remote [IPv6-IRAN] local [IPv6-KHAREJ]
sudo ip addr add 192.168.55.2/30 dev GRE6Tun_IR
sudo ip link set GRE6Tun_IR mtu 1400
sudo ip link set GRE6Tun_IR up
nohup ping 192.168.55.1 &
```

---

### ✅ 2. سرور ایران (میکروتیک)

- وارد ترمینال میکروتیک شوید و دستورات زیر را وارد کنید:

> ⚠️ **نکته:** `[IPv6-IRAN]` = آی‌پی پابلیک IPv6 ایران | `[IPv6-KHAREJ]` = آی‌پی پابلیک IPv6 خارج

```shell
/interface gre6 add name=GRE6Tun_IR local=[IPv6-IRAN] remote=[IPv6-KHAREJ] mtu=1400
/ip address add address=192.168.55.1/30 interface=GRE6Tun_IR
```

✅ پس از اجرای صحیح، وضعیت اینترفیس باید **R (RUN)** باشد.

---

<div align="center">

### 🌐 آیپی‌های داخلی (تانل IPv6)

| سرور | آیپی داخلی |
|------|------------|
| 🇮🇷 ایران | `192.168.55.1` |
| 🌍 خارج | `192.168.55.2` |

</div>

---

### ✅ تست پینگ

**سرور ایران:**

```shell
ping 192.168.55.2
```

**سرور خارج:**

```shell
ping 192.168.55.1
```

---

### 🔄 ارسال ترافیک ورودی میکروتیک به سرور مقصد (IPv6)

> ⚠️ اگر پورت `Winbox` شما متفاوت است، مقدار `dst-port` را تغییر دهید.

```shell
/ip firewall nat add chain=srcnat action=masquerade
/ip firewall nat add chain=dstnat protocol=tcp dst-port=!8291 action=dst-nat to-addresses=192.168.55.2
```

---
<div align="center">

✅ **نویسنده:** [Arash Mohebbati](https://github.com/arashmohebbati)  
⭐ اگر این پروژه برات مفید بود، یک ستاره به ریپو بده!

</div>
