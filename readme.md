# CCNA: Switching, Routing, and Wireless Essentials — Дэлгэрэнгүй Cheat Sheet

**Хэл:** Монгол  
**Зорилго:** CCNA SRWE / Switching, Routing, and Wireless Essentials шалгалтын өмнөх давтлага  
**Тайлбар:** Энэ материал нь шалгалтын үеэр хууль бусаар ашиглах зориулалттай биш, харин ойлголт бататгах, Packet Tracer дээр дадлага хийхэд зориулсан study cheat sheet юм.

---

# Гарчиг

1. Суурь IOS командууд
2. Ethernet ба Switching
3. ARP
4. VLAN
5. IEEE 802.1Q Trunking
6. DTP
7. Inter-VLAN Routing
8. STP / RSTP / PVST+
9. EtherChannel
10. PAgP
11. LACP
12. CDP ба LLDP
13. Static Routing
14. Default Route
15. Floating Static Route
16. IPv6 Routing үндэс
17. DHCPv4
18. DHCP Relay
19. SLAAC
20. DHCPv6
21. NDP / ICMPv6
22. FHRP
23. HSRP
24. VRRP
25. GLBP
26. Port Security
27. DHCP Snooping
28. Dynamic ARP Inspection
29. WLAN / IEEE 802.11
30. CAPWAP
31. WPA / WPA2 / WPA3
32. 802.1X
33. Wireless Controller Concepts
34. Troubleshooting командууд
35. Шалгалтын нийтлэг алдаа
36. Түргэн давтлагын хүснэгт

---

# 1. Суурь IOS командууд

## 1.1 Mode-ууд

Cisco IOS дээр команд бичихдээ mode-оо зөв ялгах шаардлагатай.

| Mode | Prompt | Үүрэг |
|---|---|---|
| User EXEC | `Router>` | Хязгаарлагдмал харах команд |
| Privileged EXEC | `Router#` | Бүх `show`, `copy`, `debug` команд |
| Global configuration | `Router(config)#` | Төхөөрөмжийн үндсэн тохиргоо |
| Interface configuration | `Router(config-if)#` | Interface тохиргоо |
| Line configuration | `Router(config-line)#` | Console, VTY тохиргоо |

## 1.2 Суурь тохиргоо

```bash
enable
configure terminal
hostname SW1
no ip domain-lookup
enable secret class
service password-encryption
banner motd # Unauthorized access prohibited #
```

## 1.3 Console ба VTY

```bash
line console 0
password cisco
login
exit

line vty 0 15
password cisco
login
transport input ssh
exit
```

## 1.4 SSH тохиргоо

```bash
ip domain-name example.com
username admin secret Admin123
crypto key generate rsa
ip ssh version 2

line vty 0 15
login local
transport input ssh
```

## 1.5 Хадгалах

```bash
copy running-config startup-config
```

---

# 2. Ethernet ба Switching

## 2.1 Ethernet гэж юу вэ?

Ethernet нь LAN орчинд frame дамжуулах Layer 2 технологи юм. Switch нь Ethernet frame-ийг MAC address ашиглан дамжуулна.

## 2.2 Ethernet frame-ийн үндсэн хэсгүүд

| Талбар | Үүрэг |
|---|---|
| Destination MAC | Хүлээн авагчийн MAC |
| Source MAC | Илгээгчийн MAC |
| Type/Length | Дээд түвшний protocol эсвэл урт |
| Data | Дамжуулах өгөгдөл |
| FCS | Error шалгах |

## 2.3 Switch хэрхэн сурдаг вэ?

Switch frame ирэхэд:

1. **Source MAC**-ийг харна.
2. Тэр MAC address аль port-оос ирснийг MAC address table-д хадгална.
3. Destination MAC table-д байвал тухайн port руу forwarding хийнэ.
4. Destination MAC мэдэгдэхгүй бол тухайн VLAN-ийн бүх port руу flood хийнэ.
5. Broadcast frame бол тухайн VLAN дотор flood хийнэ.

## 2.4 Шалгах командууд

```bash
show mac address-table
show interfaces status
show interfaces fa0/1
show running-config
```

## 2.5 Exam tip

- Switch нь **source MAC**-ийг сурдаг.
- Unknown unicast болон broadcast frame нь flood хийнэ.
- VLAN бүр тусдаа broadcast domain.

---

# 3. ARP — Address Resolution Protocol

## 3.1 ARP-ийн зорилго

ARP нь IPv4 address-ийг MAC address болгон хөрвүүлдэг protocol.

Жишээ:

PC1 нь `192.168.1.10` руу packet явуулах гэж байвал тухайн IP-ийн MAC address-ийг мэдэх хэрэгтэй. Мэдэхгүй бол ARP Request илгээнэ.

## 3.2 ARP ажиллагаа

1. PC ARP cache-аа шалгана.
2. MAC address байхгүй бол broadcast ARP Request илгээнэ.
3. Зорилтот host өөрийн MAC address-ийг ARP Reply-р буцаана.
4. Илгээгч ARP cache-д хадгална.
5. Дараагийн packet шууд MAC address ашиглаж явна.

## 3.3 ARP Request vs ARP Reply

| Төрөл | Destination MAC | Тайлбар |
|---|---|---|
| ARP Request | `ff:ff:ff:ff:ff:ff` | Broadcast |
| ARP Reply | Илгээгчийн MAC | Unicast |

## 3.4 Команд

```bash
show arp
show ip arp
clear arp-cache
```

## 3.5 Security concern

ARP нь authentication хийхгүй. Иймээс ARP spoofing халдлагад өртөмтгий. Үүнийг хамгаалахад **Dynamic ARP Inspection** хэрэглэнэ.

---

# 4. VLAN — Virtual LAN

## 4.1 VLAN гэж юу вэ?

VLAN нь нэг physical switch дотор олон logical network үүсгэдэг Layer 2 segmentation арга юм.

## 4.2 VLAN хэрэглэх шалтгаан

| Шалтгаан | Тайлбар |
|---|---|
| Broadcast багасгах | VLAN бүр тусдаа broadcast domain |
| Security | Department бүрийг тусгаарлаж болно |
| Management | Сүлжээг логикоор зохион байгуулах |
| Flexibility | Хэрэглэгчийн байрлалаас үл хамааран VLAN оноох |

## 4.3 VLAN төрөл

| VLAN | Тайлбар |
|---|---|
| Data VLAN | User traffic |
| Default VLAN | Cisco switch дээр VLAN 1 |
| Native VLAN | Trunk дээр untagged traffic |
| Management VLAN | Switch management IP байрлах VLAN |
| Voice VLAN | IP phone traffic |

## 4.4 VLAN үүсгэх

```bash
vlan 10
name STAFF
vlan 20
name STUDENTS
vlan 99
name MANAGEMENT
```

## 4.5 Access port тохируулах

```bash
interface fa0/1
switchport mode access
switchport access vlan 10
```

## 4.6 Voice VLAN

```bash
interface fa0/5
switchport mode access
switchport access vlan 10
switchport voice vlan 150
```

## 4.7 Шалгах командууд

```bash
show vlan brief
show interfaces switchport
show running-config interface fa0/1
```

## 4.8 Exam tip

- VLAN үүсгээгүй байхад port-д оноовол асуудал үүснэ.
- VLAN хооронд routing хийхгүй бол хоорондоо ping хийхгүй.
- VLAN 1-ийг management/native VLAN болгон ашиглах нь security талаасаа муу.

---

# 5. IEEE 802.1Q Trunking

## 5.1 Trunk гэж юу вэ?

Trunk link нь олон VLAN-ийн traffic-ийг нэг physical link-ээр дамжуулдаг.

Switch-switch, switch-router, switch-layer3 switch хооронд ашиглагдана.

## 5.2 802.1Q tag

802.1Q нь Ethernet frame дотор VLAN tag нэмдэг стандарт.

Tag дотор VLAN ID хадгалагдана. VLAN ID-ийн утга 1–4094 байна.

## 5.3 Native VLAN

Native VLAN-ийн traffic trunk дээр tag-гүй явна. Хоёр талын native VLAN ижил байх ёстой.

## 5.4 Trunk тохируулах

```bash
interface g0/1
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 10,20,99
```

## 5.5 Allowed VLAN өөрчлөх

```bash
switchport trunk allowed vlan 10,20
switchport trunk allowed vlan add 30
switchport trunk allowed vlan remove 20
```

## 5.6 Шалгах команд

```bash
show interfaces trunk
show interfaces g0/1 switchport
```

## 5.7 Common issues

| Асуудал | Шалтгаан |
|---|---|
| VLAN traffic дамжихгүй | Allowed VLAN жагсаалтад байхгүй |
| Native VLAN mismatch | Хоёр тал өөр native VLAN |
| Trunk үүсэхгүй | Port access mode-д түгжигдсэн |
| Router-on-a-stick ажиллахгүй | Switch тал trunk биш |

---

# 6. DTP — Dynamic Trunking Protocol

## 6.1 DTP гэж юу вэ?

DTP нь Cisco proprietary protocol бөгөөд switch port-ууд trunk болох эсэхийг автоматаар тохиролцдог.

## 6.2 DTP mode-ууд

| Mode | Үйлдэл |
|---|---|
| `access` | Trunk болохгүй |
| `trunk` | Заавал trunk болно |
| `dynamic desirable` | Trunk болохыг идэвхтэй хүснэ |
| `dynamic auto` | Нөгөө тал хүсвэл trunk болно |

## 6.3 Mode combination

| Нэг тал | Нөгөө тал | Үр дүн |
|---|---|---|
| trunk | trunk | trunk |
| trunk | auto | trunk |
| trunk | desirable | trunk |
| desirable | desirable | trunk |
| desirable | auto | trunk |
| auto | auto | access |
| access | any | access |

## 6.4 DTP унтраах

Security-ийн хувьд DTP-г унтраах нь сайн.

```bash
interface g0/1
switchport mode trunk
switchport nonegotiate
```

## 6.5 Exam tip

- `dynamic auto + dynamic auto = trunk болохгүй`.
- Security best practice: trunk хэрэгтэй port дээр static trunk, бусад port дээр static access.

---

# 7. Inter-VLAN Routing

## 7.1 Яагаад хэрэгтэй вэ?

VLAN бүр тусдаа Layer 2 broadcast domain. VLAN 10-ийн host VLAN 20-ийн host руу packet явуулахын тулд Layer 3 routing хэрэгтэй.

## 7.2 Арга 1: Router-on-a-Stick

Нэг router physical interface дээр олон subinterface үүсгэж VLAN бүрийн gateway болгоно.

### Router тал

```bash
interface g0/0
no shutdown

interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

interface g0/0.99
encapsulation dot1Q 99 native
ip address 192.168.99.1 255.255.255.0
```

### Switch тал

```bash
interface g0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,99
```

## 7.3 Арга 2: Layer 3 Switch SVI

Layer 3 switch дээр VLAN interface буюу SVI үүсгэнэ.

```bash
ip routing

interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown

interface vlan 20
ip address 192.168.20.1 255.255.255.0
no shutdown
```

## 7.4 Routed port

Layer 3 switch port-ийг router interface шиг ашиглаж болно.

```bash
interface g0/1
no switchport
ip address 10.0.0.1 255.255.255.252
no shutdown
```

## 7.5 Шалгах команд

```bash
show ip interface brief
show ip route
show interfaces trunk
show vlan brief
ping
traceroute
```

## 7.6 Common mistakes

| Алдаа | Засах |
|---|---|
| Router subinterface дээр `encapsulation dot1Q` байхгүй | VLAN tag тохируулах |
| Switch port trunk биш | `switchport mode trunk` |
| PC default gateway буруу | VLAN gateway IP зөв тавих |
| L3 switch дээр `ip routing` байхгүй | `ip routing` асаах |
| SVI down | VLAN үүссэн эсэх, active port байгаа эсэх шалгах |

---

# 8. STP — Spanning Tree Protocol

## 8.1 STP-ийн зорилго

STP нь Layer 2 loop-оос хамгаалдаг protocol. Redundant switch link байхад loop үүсэх эрсдэлтэй. Loop үүсвэл broadcast storm, MAC table instability, duplicate frames гарна.

## 8.2 STP хэрхэн ажилладаг вэ?

STP нь switch-үүдийн хооронд BPDU солилцож, loop үүсгэж болзошгүй зарим port-ыг blocking state-д оруулдаг.

## 8.3 BPDU гэж юу вэ?

BPDU буюу Bridge Protocol Data Unit нь STP-ийн мэдээлэл дамжуулах frame юм.

BPDU-д дараах мэдээлэл орно:

- Root Bridge ID
- Sender Bridge ID
- Path cost
- Port ID
- Timer мэдээлэл

## 8.4 Root Bridge сонголт

Root Bridge = хамгийн бага Bridge ID бүхий switch.

Bridge ID = Priority + MAC address

Default priority = 32768  
Priority нь 4096-ийн алхамтай байна.

## 8.5 Root bridge тохируулах

```bash
spanning-tree vlan 10 root primary
spanning-tree vlan 10 root secondary
```

эсвэл

```bash
spanning-tree vlan 10 priority 4096
```

## 8.6 Port roles

| Role | Тайлбар |
|---|---|
| Root Port | Non-root switch дээр root bridge рүү хүрэх хамгийн сайн port |
| Designated Port | Segment дээр forwarding хийх port |
| Alternate Port | Backup path, blocking/discarding |
| Disabled Port | Админаар унтраасан |

## 8.7 STP port states

| State | Тайлбар |
|---|---|
| Blocking | Frame forward хийхгүй, BPDU сонсоно |
| Listening | Loop-free topology тооцоолно |
| Learning | MAC address сурна, frame forward хийхгүй |
| Forwarding | Frame forward хийнэ |
| Disabled | Унтарсан |

## 8.8 STP path cost

Bandwidth өндөр байх тусам cost бага байна. Cost бага замыг илүү сайн гэж үзнэ.

## 8.9 PortFast

Access port дээр host хурдан холбогдох боломж олгоно. Listening/Learning state-ийг алгасаж forwarding болно.

```bash
interface fa0/1
spanning-tree portfast
```

Global:

```bash
spanning-tree portfast default
```

## 8.10 BPDU Guard

PortFast port дээр BPDU ирвэл port-ыг err-disabled болгоно. Энэ нь unauthorized switch залгахаас хамгаална.

```bash
interface fa0/1
spanning-tree bpduguard enable
```

Global:

```bash
spanning-tree portfast bpduguard default
```

## 8.11 RSTP

RSTP буюу Rapid Spanning Tree Protocol нь STP-ийн хурдан convergence хувилбар.

| STP | RSTP |
|---|---|
| IEEE 802.1D | IEEE 802.1w |
| Удаан convergence | Хурдан convergence |
| Blocking/listening/learning/forwarding | Discarding/learning/forwarding |

## 8.12 PVST+ ба Rapid PVST+

Cisco switch дээр VLAN бүр тусдаа spanning-tree instance ажиллуулж болно. Үүнийг PVST+ гэнэ.

Rapid PVST+ нь VLAN бүр дээр RSTP-ийн зарчмаар ажиллана.

```bash
spanning-tree mode rapid-pvst
```

## 8.13 Шалгах команд

```bash
show spanning-tree
show spanning-tree vlan 10
show spanning-tree summary
```

## 8.14 Exam tip

- Root bridge сонгохдоо хамгийн бага priority, тэнцвэл хамгийн бага MAC.
- Root bridge дээр бүх designated port forwarding байна.
- PortFast-ийг switch-to-switch link дээр бүү ашигла.

---

# 9. EtherChannel

## 9.1 EtherChannel гэж юу вэ?

EtherChannel нь олон physical Ethernet link-ийг нэг logical link буюу port-channel болгон нэгтгэдэг технологи юм.

## 9.2 Давуу тал

| Давуу тал | Тайлбар |
|---|---|
| Bandwidth нэмэгдэнэ | Олон link хамт ашиглагдана |
| Redundancy | Нэг link унасан ч port-channel ажиллана |
| STP-friendly | STP port-channel-ийг нэг link гэж харна |
| Load balancing | Traffic-ийг links дээр хуваарилна |

## 9.3 EtherChannel шаардлага

Нэг port-channel-д орох interfaces дараах зүйлсээр ижил байх ёстой:

- Speed
- Duplex
- Access эсвэл trunk mode
- Allowed VLAN list
- Native VLAN
- EtherChannel protocol/mode
- Layer 2 эсвэл Layer 3 төрөл

## 9.4 Protocol types

| Protocol | Тайлбар |
|---|---|
| PAgP | Cisco proprietary |
| LACP | IEEE стандарт |
| Static | Negotiation байхгүй, `mode on` |

## 9.5 Layer 2 EtherChannel

```bash
interface range g0/1 - 2
channel-group 1 mode active
exit

interface port-channel 1
switchport mode trunk
switchport trunk allowed vlan 10,20
```

## 9.6 Layer 3 EtherChannel

```bash
interface range g0/1 - 2
no switchport
channel-group 1 mode active
exit

interface port-channel 1
no switchport
ip address 10.0.0.1 255.255.255.252
```

## 9.7 Шалгах команд

```bash
show etherchannel summary
show etherchannel port-channel
show interfaces port-channel 1
```

---

# 10. PAgP — Port Aggregation Protocol

## 10.1 PAgP гэж юу вэ?

PAgP нь Cisco proprietary EtherChannel negotiation protocol юм. Зөвхөн Cisco төхөөрөмжүүдийн хооронд ашиглахад тохиромжтой.

## 10.2 PAgP mode

| Mode | Тайлбар |
|---|---|
| desirable | EtherChannel үүсгэхийг идэвхтэй хүснэ |
| auto | Нөгөө тал хүсвэл хариу өгнө |

## 10.3 Mode combination

| Нэг тал | Нөгөө тал | Үр дүн |
|---|---|---|
| desirable | desirable | EtherChannel үүснэ |
| desirable | auto | EtherChannel үүснэ |
| auto | auto | Үүсэхгүй |

## 10.4 Тохиргоо

```bash
interface range g0/1 - 2
channel-group 1 mode desirable
```

## 10.5 Exam tip

- `auto + auto = үүсэхгүй`.
- Cisco-only орчинд PAgP хэрэглэж болно.
- Ихэнх vendor-neutral орчинд LACP илүү зөв сонголт.

---

# 11. LACP — Link Aggregation Control Protocol

## 11.1 LACP гэж юу вэ?

LACP нь IEEE 802.3ad / 802.1AX стандартын EtherChannel negotiation protocol юм.

## 11.2 LACP mode

| Mode | Тайлбар |
|---|---|
| active | Идэвхтэй negotiation хийнэ |
| passive | Нөгөө тал эхэлбэл хариу өгнө |

## 11.3 Mode combination

| Нэг тал | Нөгөө тал | Үр дүн |
|---|---|---|
| active | active | EtherChannel үүснэ |
| active | passive | EtherChannel үүснэ |
| passive | passive | Үүсэхгүй |

## 11.4 Тохиргоо

```bash
interface range g0/1 - 2
channel-group 1 mode active
```

## 11.5 Exam tip

- `passive + passive = үүсэхгүй`.
- LACP нь open standard тул шалгалтад хамгийн зөв сонголт байх нь элбэг.
- Link mismatch бол `show etherchannel summary` дээр `I`, `s`, `D`, `P` flags-ийг шалга.

---

# 12. CDP ба LLDP

## 12.1 CDP — Cisco Discovery Protocol

CDP нь Cisco proprietary Layer 2 discovery protocol. Шууд холбогдсон Cisco төхөөрөмжийн мэдээллийг харуулна.

## 12.2 CDP харуулах мэдээлэл

- Device ID
- Local interface
- Holdtime
- Capability
- Platform
- Port ID
- IP address

## 12.3 CDP команд

```bash
show cdp neighbors
show cdp neighbors detail
```

Global унтраах:

```bash
no cdp run
```

Interface дээр унтраах:

```bash
interface g0/1
no cdp enable
```

## 12.4 LLDP — Link Layer Discovery Protocol

LLDP нь open standard discovery protocol. Cisco бус төхөөрөмжтэй ажиллахад тохиромжтой.

```bash
lldp run
show lldp neighbors
show lldp neighbors detail
```

## 12.5 Security tip

Discovery protocol нь сүлжээний мэдээлэл ил болгодог тул untrusted port дээр унтраах нь сайн.

---

# 13. Static Routing

## 13.1 Static route гэж юу вэ?

Static route нь administrator гараар тохируулсан route юм. Dynamic routing protocol ашиглахгүй үед эсвэл жижиг сүлжээнд их хэрэглэнэ.

## 13.2 IPv4 static route

```bash
ip route 192.168.20.0 255.255.255.0 10.0.0.2
```

эсвэл exit interface:

```bash
ip route 192.168.20.0 255.255.255.0 g0/0
```

## 13.3 Fully specified static route

```bash
ip route 192.168.20.0 255.255.255.0 g0/0 10.0.0.2
```

## 13.4 IPv6 static route

```bash
ipv6 route 2001:db8:acad:2::/64 2001:db8:acad:1::2
```

эсвэл link-local next-hop ашиглавал exit interface хэрэгтэй:

```bash
ipv6 route 2001:db8:acad:2::/64 g0/0 fe80::2
```

## 13.5 Шалгах команд

```bash
show ip route
show ipv6 route
show running-config | include ip route
```

## 13.6 Exam tip

- Next-hop reachable байх ёстой.
- IPv6 link-local next-hop ашиглах бол exit interface заавал бичнэ.
- Static route-ийн AD default = 1.

---

# 14. Default Route

## 14.1 Default route гэж юу вэ?

Routing table-д destination network таарахгүй үед ашиглах route.

## 14.2 IPv4 default route

```bash
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

## 14.3 IPv6 default route

```bash
ipv6 route ::/0 2001:db8:acad:1::1
```

## 14.4 Хэзээ хэрэглэдэг вэ?

- Stub network
- Internet gateway
- Branch router
- Жижиг сүлжээ

## 14.5 Exam tip

`0.0.0.0/0` болон `::/0` нь “үл мэдэгдэх бүх destination” гэсэн утгатай.

---

# 15. Floating Static Route

## 15.1 Floating static route гэж юу вэ?

Floating static route нь backup route юм. AD өндөр учраас primary route байх үед routing table-д идэвхгүй байна. Primary route унавал идэвхжинэ.

## 15.2 Жишээ

Primary route:

```bash
ip route 192.168.30.0 255.255.255.0 10.0.0.2
```

Backup route:

```bash
ip route 192.168.30.0 255.255.255.0 10.0.1.2 5
```

## 15.3 Exam tip

- Static route default AD = 1.
- Floating static route-ийн AD-г primary route-ээс өндөр тавина.
- Backup ажиллаж байгаа эсэхийг `show ip route`-оор шалгана.

---

# 16. IPv6 Routing үндэс

## 16.1 IPv6 асаах

Router дээр IPv6 packet forwarding идэвхжүүлэх:

```bash
ipv6 unicast-routing
```

## 16.2 Interface IPv6 тохируулах

```bash
interface g0/0
ipv6 address 2001:db8:acad:1::1/64
ipv6 address fe80::1 link-local
no shutdown
```

## 16.3 IPv6 address төрөл

| Төрөл | Prefix | Тайлбар |
|---|---|---|
| Global Unicast | 2000::/3 | Internet-routable |
| Link-local | fe80::/10 | Local link дотор |
| Multicast | ff00::/8 | Group communication |
| Loopback | ::1/128 | Өөрийгөө заана |
| Unspecified | ::/128 | Address байхгүй |

## 16.4 Шалгах команд

```bash
show ipv6 interface brief
show ipv6 route
ping ipv6-address
```

---

# 17. DHCPv4 — Dynamic Host Configuration Protocol for IPv4

## 17.1 DHCPv4-ийн зорилго

DHCPv4 нь host-д автоматаар дараах мэдээллийг өгнө:

- IPv4 address
- Subnet mask
- Default gateway
- DNS server
- Domain name
- Lease time

## 17.2 DHCPv4 DORA процесс

| Алхам | Message | Тайлбар |
|---|---|---|
| 1 | Discover | Client DHCP server хайна |
| 2 | Offer | Server IP санал болгоно |
| 3 | Request | Client санал болгосон IP-г хүснэ |
| 4 | Acknowledgment | Server баталгаажуулна |

## 17.3 DHCP server тохируулах

```bash
ip dhcp excluded-address 192.168.10.1 192.168.10.20

ip dhcp pool VLAN10
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
domain-name example.com
```

## 17.4 Шалгах команд

```bash
show ip dhcp binding
show ip dhcp pool
show running-config | section dhcp
```

## 17.5 Troubleshooting

| Асуудал | Шалгах |
|---|---|
| Client IP авахгүй | DHCP pool, excluded address |
| Wrong gateway | `default-router` |
| Wrong subnet | `network` statement |
| Server өөр subnet-д | `ip helper-address` |
| Pool дууссан | `show ip dhcp pool` |

---

# 18. DHCP Relay

## 18.1 DHCP Relay гэж юу вэ?

DHCP broadcast нь router-ээр дамждаггүй. Client ба DHCP server өөр subnet-д байвал router эсвэл Layer 3 switch дээр DHCP relay тохируулна.

## 18.2 `ip helper-address`

```bash
interface vlan 10
ip helper-address 192.168.100.10
```

эсвэл router interface дээр:

```bash
interface g0/0.10
ip helper-address 192.168.100.10
```

## 18.3 Exam tip

- `ip helper-address` нь client талын gateway interface дээр тавигдана.
- Server талын interface дээр тавихгүй.
- DHCP request-ийг unicast болгон server рүү дамжуулна.

---

# 19. SLAAC — Stateless Address Autoconfiguration

## 19.1 SLAAC гэж юу вэ?

SLAAC нь IPv6 host-д DHCP serverгүйгээр өөрөө IPv6 address үүсгэх боломж олгодог арга.

## 19.2 SLAAC ажиллагаа

1. Host Router Solicitation илгээнэ.
2. Router Router Advertisement буцаана.
3. Host prefix авна.
4. Interface ID-г өөрөө үүсгэнэ.
5. Duplicate Address Detection хийж address давхцаагүй эсэхийг шалгана.

## 19.3 Router тохиргоо

```bash
ipv6 unicast-routing

interface g0/0
ipv6 address 2001:db8:acad:1::1/64
no shutdown
```

## 19.4 SLAAC flags

Router Advertisement дээр дараах flags чухал:

| Flag | Утга |
|---|---|
| A flag | Autonomous address configuration |
| M flag | Managed address via DHCPv6 |
| O flag | Other information via DHCPv6 |

## 19.5 Exam tip

- SLAAC address-ийг host өөрөө үүсгэнэ.
- Default gateway нь router-ийн link-local address байдаг.
- DNS зэрэг нэмэлт мэдээлэлд stateless DHCPv6 хэрэглэж болно.

---

# 20. DHCPv6

## 20.1 DHCPv6 гэж юу вэ?

DHCPv6 нь IPv6 host-д address болон бусад network information өгдөг protocol.

## 20.2 DHCPv6 төрөл

| Төрөл | Address | DNS зэрэг бусад info |
|---|---|---|
| SLAAC only | SLAAC | RA эсвэл manual |
| Stateless DHCPv6 | SLAAC | DHCPv6 |
| Stateful DHCPv6 | DHCPv6 | DHCPv6 |

## 20.3 Stateless DHCPv6

Router address prefix-ийг RA-р өгнө. DHCPv6 server DNS гэх мэт нэмэлт мэдээлэл өгнө.

```bash
ipv6 dhcp pool STATELESS
dns-server 2001:4860:4860::8888
domain-name example.com

interface g0/0
ipv6 address 2001:db8:acad:1::1/64
ipv6 nd other-config-flag
ipv6 dhcp server STATELESS
```

## 20.4 Stateful DHCPv6

DHCPv6 server address хүртэл өгнө.

```bash
ipv6 dhcp pool STATEFUL
address prefix 2001:db8:acad:1::/64
dns-server 2001:4860:4860::8888
domain-name example.com

interface g0/0
ipv6 address 2001:db8:acad:1::1/64
ipv6 nd managed-config-flag
ipv6 dhcp server STATEFUL
```

## 20.5 Шалгах команд

```bash
show ipv6 dhcp pool
show ipv6 dhcp binding
show ipv6 interface g0/0
```

## 20.6 Exam tip

- `managed-config-flag` = stateful DHCPv6.
- `other-config-flag` = stateless DHCPv6.
- DHCPv6 default gateway өгдөггүй; default gateway нь RA-аас авсан router link-local address.

---

# 21. NDP / ICMPv6

## 21.1 NDP гэж юу вэ?

Neighbor Discovery Protocol нь IPv6-д ARP-ийн үүргийг орлох ICMPv6-based protocol юм.

## 21.2 NDP үүрэг

| Үүрэг | Тайлбар |
|---|---|
| Address resolution | IPv6 address-аас MAC олох |
| Router discovery | Default router олох |
| Prefix discovery | Network prefix авах |
| DAD | Duplicate Address Detection |
| Neighbor reachability | Neighbor амьд эсэх шалгах |

## 21.3 ICMPv6 message-үүд

| Message | Үүрэг |
|---|---|
| RS — Router Solicitation | Router advertisement хүсэх |
| RA — Router Advertisement | Prefix/default gateway мэдээлэл өгөх |
| NS — Neighbor Solicitation | MAC address асуух |
| NA — Neighbor Advertisement | MAC address хариулах |

## 21.4 Шалгах команд

```bash
show ipv6 neighbors
show ipv6 interface
```

## 21.5 Exam tip

IPv6-д ARP байхгүй. NDP нь ICMPv6 ашиглаж хөрш discovery хийнэ.

---

# 22. FHRP — First Hop Redundancy Protocol

## 22.1 FHRP гэж юу вэ?

FHRP нь default gateway redundancy үүсгэдэг protocol бүлэг юм. Host-ууд нэг virtual gateway IP ашиглана. Active router унавал standby router virtual gateway-г үргэлжлүүлнэ.

## 22.2 Яагаад хэрэгтэй вэ?

PC-ийн default gateway ганц router байвал router унахад сүлжээ гадагшаа гарахгүй. FHRP нь gateway availability-г нэмэгдүүлнэ.

## 22.3 FHRP төрлүүд

| Protocol | Стандарт | Тайлбар |
|---|---|---|
| HSRP | Cisco proprietary | Active/Standby |
| VRRP | Open standard | Master/Backup |
| GLBP | Cisco proprietary | Load balancing дэмжинэ |

---

# 23. HSRP — Hot Standby Router Protocol

## 23.1 HSRP гэж юу вэ?

HSRP нь Cisco proprietary FHRP protocol. Нэг router active, нөгөө router standby байна.

## 23.2 HSRP components

| Нэр | Тайлбар |
|---|---|
| Virtual IP | Host default gateway болгож ашиглана |
| Virtual MAC | Active router ашиглана |
| Active router | Traffic forward хийнэ |
| Standby router | Active унавал takeover хийнэ |
| Priority | Active router сонгоход ашиглана |
| Preempt | Priority өндөр router буцаж active болох боломж |

## 23.3 HSRP тохиргоо

Router R1:

```bash
interface g0/0
ip address 192.168.10.2 255.255.255.0
standby 1 ip 192.168.10.254
standby 1 priority 110
standby 1 preempt
```

Router R2:

```bash
interface g0/0
ip address 192.168.10.3 255.255.255.0
standby 1 ip 192.168.10.254
standby 1 priority 100
standby 1 preempt
```

## 23.4 Шалгах команд

```bash
show standby
show standby brief
```

## 23.5 Exam tip

- Default HSRP priority = 100.
- Өндөр priority active болно.
- Preempt байхгүй бол priority өндөр router буцаж ирсэн ч active болохгүй байж болно.

---

# 24. VRRP — Virtual Router Redundancy Protocol

## 24.1 VRRP гэж юу вэ?

VRRP нь open standard FHRP protocol. Master router traffic forward хийнэ, backup router standby байна.

## 24.2 HSRP vs VRRP

| HSRP | VRRP |
|---|---|
| Cisco proprietary | Open standard |
| Active/Standby | Master/Backup |
| Default priority 100 | Default priority 100 |
| Virtual IP тусдаа | Real interface IP-г virtual IP болгож болно |

## 24.3 Conceptual тохиргооны жишээ

Cisco IOS дээр VRRP support platform-аас хамаарна.

```bash
interface g0/0
vrrp 1 ip 192.168.10.254
vrrp 1 priority 110
```

## 24.4 Exam tip

VRRP нь multi-vendor environment-д тохиромжтой.

---

# 25. GLBP — Gateway Load Balancing Protocol

## 25.1 GLBP гэж юу вэ?

GLBP нь Cisco proprietary FHRP protocol бөгөөд gateway redundancy + load balancing өгдөг.

## 25.2 GLBP roles

| Role | Тайлбар |
|---|---|
| AVG | Active Virtual Gateway, virtual MAC тараана |
| AVF | Active Virtual Forwarder, traffic forward хийнэ |

## 25.3 Давуу тал

HSRP/VRRP дээр ихэвчлэн нэг router traffic forward хийдэг бол GLBP дээр олон router зэрэг traffic forward хийж болно.

## 25.4 Exam tip

- GLBP нь load balancing дэмждэг FHRP.
- Cisco proprietary.
- CCNA түвшинд ихэвчлэн conceptual асууна.

---

# 26. Port Security

## 26.1 Port Security гэж юу вэ?

Port Security нь switch access port дээр зөвшөөрөгдөх MAC address-ийг хязгаарлах security feature юм.

## 26.2 Яагаад хэрэгтэй вэ?

- Unauthorized device холбохоос хамгаална.
- MAC flooding халдлагыг багасгана.
- Access layer security нэмнэ.

## 26.3 Тохиргоо

```bash
interface fa0/1
switchport mode access
switchport port-security
switchport port-security maximum 2
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

## 26.4 MAC address modes

| Mode | Тайлбар |
|---|---|
| Static | Гараар MAC заана |
| Dynamic | Switch автоматаар сурна, config-д хадгалахгүй |
| Sticky | Автоматаар сурч running-config-д бичнэ |

## 26.5 Violation modes

| Mode | Traffic drop | Log/SNMP | Port shutdown |
|---|---|---|---|
| protect | Тийм | Үгүй | Үгүй |
| restrict | Тийм | Тийм | Үгүй |
| shutdown | Тийм | Тийм | Тийм |

## 26.6 Шалгах команд

```bash
show port-security
show port-security interface fa0/1
show port-security address
```

## 26.7 Err-disabled сэргээх

```bash
interface fa0/1
shutdown
no shutdown
```

эсвэл auto recovery:

```bash
errdisable recovery cause psecure-violation
errdisable recovery interval 300
```

## 26.8 Exam tip

- Port security нь access port дээр ашиглагдана.
- Default violation mode = shutdown.
- Sticky MAC хадгалах бол `copy running-config startup-config` хэрэгтэй.

---

# 27. DHCP Snooping

## 27.1 DHCP Snooping гэж юу вэ?

DHCP Snooping нь rogue DHCP server-ээс хамгаалах Layer 2 security feature юм.

## 27.2 Яагаад хэрэгтэй вэ?

Хуурамч DHCP server буруу default gateway/DNS өгч traffic-ийг өөр рүүгээ чиглүүлж болно.

## 27.3 Trusted ба Untrusted port

| Port төрөл | Тайлбар |
|---|---|
| Trusted | DHCP server, router, uplink тал |
| Untrusted | Client тал |

Default-аар бүх port untrusted.

## 27.4 Тохиргоо

```bash
ip dhcp snooping
ip dhcp snooping vlan 10,20
```

Trusted uplink:

```bash
interface g0/1
ip dhcp snooping trust
```

Client port дээр rate limit:

```bash
interface fa0/1
ip dhcp snooping limit rate 5
```

## 27.5 DHCP Snooping binding table

Switch нь DHCP snooping binding table үүсгэнэ.

Table-д:

- MAC address
- IP address
- Lease time
- VLAN
- Interface

## 27.6 Шалгах команд

```bash
show ip dhcp snooping
show ip dhcp snooping binding
```

## 27.7 Exam tip

- DHCP server талын port trusted байх ёстой.
- DHCP snooping binding table нь DAI-д ашиглагдана.
- Trunk/uplink port дээр trust хийхээ мартвал DHCP ажиллахгүй.

---

# 28. Dynamic ARP Inspection — DAI

## 28.1 DAI гэж юу вэ?

Dynamic ARP Inspection нь ARP spoofing халдлагаас хамгаалдаг feature. DHCP snooping binding table ашиглан ARP packet үнэн эсэхийг шалгана.

## 28.2 Яагаад хэрэгтэй вэ?

ARP spoofing хийвэл attacker өөрийгөө gateway гэж зарлаж traffic interception хийж болно.

## 28.3 Тохиргоо

```bash
ip arp inspection vlan 10,20
```

Trusted uplink:

```bash
interface g0/1
ip arp inspection trust
```

## 28.4 DHCP snooping-той холбоо

DAI нь dynamic host-уудын IP-MAC mapping-ийг DHCP snooping binding table-ээс авна.

Static IP host байвал ARP ACL шаардлагатай байж болно.

## 28.5 Шалгах команд

```bash
show ip arp inspection
show ip arp inspection vlan 10
show ip dhcp snooping binding
```

## 28.6 Exam tip

- DAI ажиллуулахын өмнө DHCP snooping зөв ажиллаж байх ёстой.
- Trusted port-ыг зөв сонгохгүй бол legitimate ARP drop болно.

---

# 29. WLAN / IEEE 802.11

## 29.1 WLAN гэж юу вэ?

WLAN нь wireless LAN буюу Wi-Fi сүлжээ юм. IEEE 802.11 standard-ууд дээр ажиллана.

## 29.2 WLAN basic terms

| Нэр | Тайлбар |
|---|---|
| AP | Wireless client-үүдийг wired network-д холбодог төхөөрөмж |
| SSID | Wi-Fi network name |
| BSSID | AP radio-ийн MAC address |
| ESS | Олон AP нэг SSID-г хамт цацах |
| Channel | Radio frequency-ийн хэсэг |
| Roaming | Client AP хооронд шилжих |

## 29.3 802.11 standards

| Standard | Band | Тайлбар |
|---|---|---|
| 802.11b | 2.4 GHz | Хуучин, удаан |
| 802.11g | 2.4 GHz | b-с хурдан |
| 802.11n | 2.4/5 GHz | MIMO дэмжинэ |
| 802.11ac | 5 GHz | Илүү өндөр throughput |
| 802.11ax | 2.4/5/6 GHz | Wi-Fi 6/6E, OFDMA |

## 29.4 2.4 GHz vs 5 GHz vs 6 GHz

| Band | Давуу тал | Сул тал |
|---|---|---|
| 2.4 GHz | Хол явна, wall penetration сайн | Interference их, channel цөөн |
| 5 GHz | Хурдан, channel олон | Range богино |
| 6 GHz | Илүү цэвэр spectrum | Шинэ төхөөрөмж шаарддаг |

## 29.5 2.4 GHz non-overlapping channels

2.4 GHz дээр overlap багатай үндсэн channel:

```text
1, 6, 11
```

## 29.6 WLAN frame types

| Frame type | Үүрэг |
|---|---|
| Management | Association, authentication, beacon |
| Control | RTS/CTS, ACK |
| Data | User traffic |

## 29.7 Exam tip

- SSID бол network name.
- BSSID бол AP radio MAC.
- 2.4 GHz дээр 1/6/11 channel ашиглах нь зөв planning.
- Interference ба coverage нь WLAN troubleshooting-ийн гол асуудал.

---

# 30. CAPWAP

## 30.1 CAPWAP гэж юу вэ?

CAPWAP буюу Control And Provisioning of Wireless Access Points нь lightweight AP болон Wireless LAN Controller-ийн хооронд control/data traffic дамжуулах protocol юм.

## 30.2 Яагаад хэрэгтэй вэ?

Centralized WLAN architecture-д AP-ууд controller-оос configuration, policy, firmware, security тохиргоо авна.

## 30.3 CAPWAP traffic

| Traffic | Тайлбар |
|---|---|
| Control traffic | AP management, configuration |
| Data traffic | Client data forwarding |

## 30.4 CAPWAP ашиглах port

CAPWAP ихэвчлэн UDP ашиглана.

| Port | Үүрэг |
|---|---|
| UDP 5246 | Control |
| UDP 5247 | Data |

## 30.5 AP discovery methods

Lightweight AP controller олох аргууд:

- DHCP option 43
- DNS lookup
- Broadcast discovery
- Previously known controller
- Static configuration

## 30.6 Exam tip

- CAPWAP нь AP-WLC хооронд ашиглагдана.
- Lightweight AP local config бага, ихэнх policy WLC дээр байна.
- AP controller олохгүй бол WLAN service бүрэн ажиллахгүй.

---

# 31. WPA / WPA2 / WPA3

## 31.1 Wireless security evolution

| Security | Тайлбар |
|---|---|
| Open | Encryption байхгүй |
| WEP | Маш сул, хэрэглэхгүй |
| WPA | WEP-ийн түр сайжруулалт, TKIP |
| WPA2 | AES/CCMP, өргөн хэрэглэгддэг |
| WPA3 | SAE, илүү хүчтэй authentication |

## 31.2 WPA2-Personal

Pre-Shared Key ашиглана. Гэрийн болон жижиг оффисын Wi-Fi-д түгээмэл.

## 31.3 WPA2-Enterprise

802.1X + RADIUS server ашиглана. Байгууллагын сүлжээнд хэрэглэнэ.

## 31.4 WPA3

WPA3 нь SAE ашиглаж offline password guessing халдлагад илүү тэсвэртэй.

## 31.5 Encryption

| Encryption | Тайлбар |
|---|---|
| TKIP | Хуучин, WPA үед |
| AES/CCMP | WPA2 үед стандарт сайн сонголт |
| GCMP | Илүү шинэ standard-уудад хэрэглэгдэнэ |

## 31.6 Exam tip

- WEP ашиглахгүй.
- WPA2/WPA3 + AES бол сайн practice.
- Enterprise mode нь RADIUS authentication ашиглана.

---

# 32. IEEE 802.1X

## 32.1 802.1X гэж юу вэ?

802.1X нь port-based network access control standard юм. Wired болон wireless орчинд хэрэглэгдэнэ.

## 32.2 802.1X roles

| Role | Тайлбар |
|---|---|
| Supplicant | Client device |
| Authenticator | Switch эсвэл AP |
| Authentication Server | RADIUS server |

## 32.3 Ажиллах зарчим

1. Client network-д холбогдоно.
2. Switch/AP client-ийг authentication хийхийг шаардана.
3. Client credential илгээнэ.
4. Authenticator RADIUS server рүү дамжуулна.
5. Server accept/reject өгнө.
6. Зөвшөөрөгдвөл network access нээгдэнэ.

## 32.4 EAP

802.1X нь EAP framework ашиглана. EAP нь authentication method-уудыг дэмждэг.

## 32.5 Exam tip

- 802.1X нь authentication.
- Switch/AP нь authenticator.
- RADIUS server нь authentication decision гаргана.
- WPA2-Enterprise = 802.1X + RADIUS.

---

# 33. Wireless Controller Concepts

## 33.1 Autonomous AP vs Lightweight AP

| Төрөл | Тайлбар |
|---|---|
| Autonomous AP | AP тус бүр дээр config хийнэ |
| Lightweight AP | WLC-аас удирдагдана |

## 33.2 WLC үүрэг

Wireless LAN Controller нь:

- SSID тохируулах
- Security policy удирдах
- AP management хийх
- RF management хийх
- Client roaming дэмжих
- Centralized monitoring хийх

## 33.3 WLAN тохиргооны үндсэн хэсгүүд

- WLAN profile name
- SSID
- VLAN mapping
- Security mode
- Authentication method
- Interface / dynamic interface

## 33.4 Exam tip

WLC дээр SSID үүсгээд VLAN mapping хийх нь wired VLAN-тэй wireless client-ийг холбох үндсэн санаа.

---

# 34. Troubleshooting командууд

## 34.1 Switching

```bash
show interfaces status
show interfaces fa0/1
show vlan brief
show interfaces trunk
show mac address-table
show spanning-tree
show etherchannel summary
```

## 34.2 Routing

```bash
show ip interface brief
show ip route
show ipv6 interface brief
show ipv6 route
show arp
show ipv6 neighbors
ping
traceroute
```

## 34.3 DHCP

```bash
show ip dhcp binding
show ip dhcp pool
show ip dhcp snooping
show ip dhcp snooping binding
```

## 34.4 Security

```bash
show port-security
show port-security interface fa0/1
show ip arp inspection
show access-lists
```

## 34.5 Discovery

```bash
show cdp neighbors
show cdp neighbors detail
show lldp neighbors
show lldp neighbors detail
```

---

# 35. Шалгалтын нийтлэг алдаа

| Алдаа | Тайлбар |
|---|---|
| VLAN үүсгээгүй | Access port VLAN руу оноосон ч VLAN байхгүй |
| Trunk allowed VLAN буруу | VLAN traffic trunk-ээр явахгүй |
| Native VLAN mismatch | Trunk хоёр талын native VLAN өөр |
| Router subinterface дээр dot1Q байхгүй | Inter-VLAN routing ажиллахгүй |
| SVI down | VLAN active port байхгүй эсвэл SVI shutdown |
| `ip routing` мартсан | Layer 3 switch routing хийхгүй |
| EtherChannel mismatch | Speed/duplex/VLAN/trunk setting өөр |
| DHCP relay буруу interface дээр | Client IP авахгүй |
| HSRP preempt байхгүй | Priority өндөр router буцаж active болохгүй |
| Port security shutdown | Interface err-disabled болно |
| DHCP snooping trust буруу | DHCP offer drop болно |
| DAI trust буруу | Legitimate ARP drop болно |

---

# 36. Түргэн давтлагын хүснэгт

## 36.1 Protocol summary

| Protocol / Feature | Layer | Гол үүрэг | Санах зүйл |
|---|---:|---|---|
| Ethernet | L2 | LAN frame дамжуулах | MAC address ашиглана |
| ARP | L2/L3 boundary | IPv4 → MAC | Broadcast request, unicast reply |
| VLAN | L2 | Broadcast domain хуваах | VLAN хооронд routing хэрэгтэй |
| 802.1Q | L2 | VLAN tagging | Native VLAN untagged |
| DTP | L2 | Trunk negotiation | Cisco proprietary |
| STP | L2 | Loop prevention | Root bridge сонгоно |
| RSTP | L2 | Faster STP | Rapid convergence |
| EtherChannel | L2/L3 | Link aggregation | STP нэг link гэж харна |
| PAgP | L2 | Cisco EtherChannel negotiation | desirable/auto |
| LACP | L2 | Standard EtherChannel negotiation | active/passive |
| CDP | L2 | Cisco neighbor discovery | Cisco proprietary |
| LLDP | L2 | Standard neighbor discovery | Multi-vendor |
| DHCPv4 | App | IPv4 config автоматаар өгөх | DORA |
| SLAAC | IPv6 | IPv6 address auto config | RA ашиглана |
| DHCPv6 | App | IPv6 config өгөх | Stateful/stateless |
| NDP | ICMPv6 | IPv6 neighbor discovery | ARP орлоно |
| HSRP | L3 | Gateway redundancy | Cisco proprietary |
| VRRP | L3 | Gateway redundancy | Open standard |
| GLBP | L3 | Gateway redundancy + load balancing | Cisco proprietary |
| CAPWAP | App/Transport | AP-WLC communication | UDP 5246/5247 |
| WPA2/WPA3 | L2 | Wi-Fi security | AES/SAE |
| 802.1X | L2 | Access authentication | Supplicant/Auth/RADIUS |

## 36.2 Administrative Distance

| Route source | AD |
|---|---:|
| Connected | 0 |
| Static | 1 |
| EIGRP | 90 |
| OSPF | 110 |
| RIP | 120 |

## 36.3 Must-know commands

```bash
show running-config
show ip interface brief
show ipv6 interface brief
show vlan brief
show interfaces trunk
show spanning-tree
show etherchannel summary
show mac address-table
show ip route
show ipv6 route
show arp
show ipv6 neighbors
show port-security
show ip dhcp binding
show ip dhcp snooping binding
show cdp neighbors detail
```

## 36.4 Troubleshooting order

1. Physical link up байна уу?
2. VLAN зөв үү?
3. Access/trunk mode зөв үү?
4. Allowed VLAN зөв үү?
5. Gateway/SVI/router interface up байна уу?
6. Routing table-д route байна уу?
7. DHCP address зөв авсан уу?
8. Security feature traffic drop хийж байна уу?
9. STP/EtherChannel topology зөв үү?
10. Ping/traceroute ашиглаж хаана тасарч байгааг ол.

---

# Богино цээжлэх хэсэг

- VLAN = broadcast domain хуваана.
- Trunk = олон VLAN нэг link-ээр явна.
- 802.1Q = VLAN tag.
- Native VLAN = untagged traffic.
- STP = Layer 2 loop хамгаална.
- Root Bridge = хамгийн бага Bridge ID.
- EtherChannel = олон link нэг logical link.
- LACP = active/passive.
- PAgP = desirable/auto.
- DHCPv4 = DORA.
- DHCP relay = `ip helper-address`.
- SLAAC = IPv6 host өөрөө address үүсгэнэ.
- NDP = IPv6-ийн ARP орлуулагч.
- HSRP = Cisco gateway redundancy.
- VRRP = open standard gateway redundancy.
- GLBP = gateway redundancy + load balancing.
- Port Security = MAC хязгаарлалт.
- DHCP Snooping = rogue DHCP хамгаалалт.
- DAI = ARP spoofing хамгаалалт.
- WPA2/WPA3 = Wi-Fi security.
- 802.1X = authentication with RADIUS.

---

# Packet Tracer дээр хийх санал болгосон lab-ууд

1. VLAN 10/20/99 үүсгээд access port оноох
2. Switch хооронд trunk үүсгэх
3. Router-on-a-stick inter-VLAN routing хийх
4. Layer 3 switch дээр SVI routing хийх
5. STP root bridge тохируулах
6. EtherChannel LACP үүсгэх
7. DHCPv4 server ба DHCP relay тохируулах
8. Port security violation турших
9. DHCP snooping ба DAI тохируулах
10. WPA2-PSK wireless WLAN үүсгэх
11. HSRP default gateway redundancy simulation хийх

---

Амжилт хүсье. Энэ cheat sheet-ийг команд бичиж дагаж хийх үед хамгийн сайн тогтоно.
