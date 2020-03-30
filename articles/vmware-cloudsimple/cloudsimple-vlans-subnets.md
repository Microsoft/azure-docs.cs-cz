---
title: Sítě VLAN a podsítě v řešení Azure VMware podle CloudSimple
description: Informace o sítích VLAN a podsítích v privátním cloudu CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024887"
---
# <a name="vlans-and-subnets-overview"></a>Přehled sítí VLAN a podsítí

CloudSimple poskytuje síť podle oblasti, kde je nasazena vaše služba CloudSimple.  Síť je jeden adresní prostor vrstvy TCP vrstvy 3, ve kterých je směrování povoleno.  Všechny privátní cloudy a podsítě vytvořené v této oblasti mohou vzájemně komunikovat bez jakékoli další konfigurace.  Skupiny distribuovaných portů můžete vytvořit v programu vCenter pomocí programů VLAN.

![CloudSimple síťová topologie](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Vlan

Pro každý privátní cloud se vytvoří síť VLAN (síť vrstvy 2).  Provoz vrstvy 2 zůstává v rámci privátního cloudu, což vám umožní izolovat místní provoz v rámci privátního cloudu.  Síť VLAN vytvořenou v privátním cloudu lze použít k vytvoření distribuovaných skupin portů pouze v tomto privátním cloudu.  Síť VLAN vytvořená v privátním cloudu je automaticky konfigurována na všech přepínačích připojených k hostitelům privátního cloudu.

## <a name="subnets"></a>Podsítě

Při vytváření sítě VLAN můžete vytvořit podsíť definováním adresního prostoru podsítě. Ip adresa z adresního prostoru je přiřazena jako brána podsítě. Jeden soukromý adresní prostor vrstvy 3 je přiřazen pro každého zákazníka a oblast. Můžete nakonfigurovat libovolný nepřekrývající se adresní prostor RFC 1918 v místní síti nebo virtuální síti Azure ve vaší oblasti sítě.

Všechny podsítě mohou komunikovat mezi sebou ve výchozím nastavení, snížení režie konfigurace pro směrování mezi privátní cloudy. Východozápadní data mezi počítači ve stejné oblasti zůstávají ve stejné síti vrstvy 3 a přenáší se přes infrastrukturu místní sítě v rámci oblasti. Pro komunikaci mezi privátními cloudy v oblasti není vyžadován žádný odchozí přenos. Tento přístup eliminuje jakékoli snížení výkonu WAN/egress při nasazování různých úloh v různých privátních cloudech.

## <a name="vspherevsan-subnets-cidr-range"></a>řada CIDR podsítí vSphere/vSAN

Privátní cloud se vytvoří jako izolované prostředí vMware (hostitelé ESXi, vCenter, vSAN a NSX) spravované serverem vCenter.  Součásti správy jsou nasazeny v síti vybrané pro podsítě vSphere/vSAN CIDR.  Rozsah CIDR sítě je během nasazení rozdělen do různých podsítí.

* Minimální předpona rozsahu řady VSphere/vSAN: **/24**
* Předpona rozsahu CIDR vSphere/vSAN: **/21**

> [!CAUTION]
> IP adresy v rozsahu CIDR vSphere/vSAN jsou vyhrazeny pro použití infrastrukturou Privátního cloudu.  Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN podsítě CIDR limity rozsahu

Výběr velikosti rozsahu CIDR vSphere/vSAN má vliv na velikost vašeho privátního cloudu.  V následující tabulce je uveden maximální počet uzlů, které můžete mít na základě velikosti podsítí vSphere/vSAN CIDR.

| Zadaná délka předpony CIDR podsítě vSphere/vSAN | Maximální počet uzlů |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Podsítě pro správu vytvořené v privátním cloudu

Následující podsítě pro správu jsou vytvořeny při vytváření privátního cloudu.

* **Správa systému**. Síť VLAN a podsíť pro síť pro správu hostitelů ESXi, DNS server, server vCenter.
* **VMotion**. VLAN a podsíť pro síť vMotion hostitelů ESXi.
* **VSAN**. VLAN a podsíť pro síť vSAN hostitelů ESXi.
* **NsxtEdgeUplink1**. Síť VLAN a podsíť pro uplinky VLAN do externí sítě.
* **NsxtEdgeUplink2**. Síť VLAN a podsíť pro uplinky VLAN do externí sítě.
* **NsxtEdgeTransport**. Síť VLAN a podsíť pro dopravní zóny řídí dosah sítí vrstvy 2 v NSX-T.
* **NsxtHostTransport**. Síť VLAN a podsíť pro hostitelskou přenosovou zónu.

### <a name="management-network-cidr-range-breakdown"></a>Členění rozsahu správy sítě CIDR

Zadaný rozsah CIDR podsítí vSphere/vSAN je rozdělen do více podsítí.  V následující tabulce je uveden příklad rozdělení povolených předpon.  Příklad používá 192.168.0.0 jako rozsah CIDR.

Příklad:

| Zadané podsítě vSphere/vSAN CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Správa systému | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vPohyb | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Přenos hostitele NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Přenos hran NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Okraj Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí](create-vlan-subnet.md)
