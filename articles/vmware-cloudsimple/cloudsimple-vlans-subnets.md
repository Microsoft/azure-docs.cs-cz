---
title: SÍTĚ VLAN a podsítě v řešeních Azure VMware (AVS)
description: Informace o sítích VLAN a podsítích v privátním cloudu pro funkci AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d0ce15c782ae70e16f55a28ec8c4b70f3b080f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024887"
---
# <a name="vlans-and-subnets-overview"></a>Přehled sítí VLAN a podsítí

Služba AVS poskytuje síť na oblast, ve které je nasazená vaše služba AVS. Ve výchozím nastavení je síť jedním adresním prostorem TCP vrstvy 3 s povoleným směrováním. Všechny privátní cloudy a podsítě pro funkci AVS vytvořené v této oblasti můžou vzájemně komunikovat bez jakékoli další konfigurace. Distribuované skupiny portů můžete v vCenter vytvořit pomocí sítí VLAN.

![Síťová topologie AVS](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Sítě VLAN

Síť VLAN (síť 2) se vytvoří pro každý privátní cloud služby AVS. Provoz vrstvy 2 zůstává v rámci hranice privátního cloudu služby AVS, což vám umožní izolovat místní provoz v rámci privátního cloudu služby AVS. SÍŤ VLAN vytvořená v privátním cloudu pro funkci AVS se dá použít k vytvoření distribuovaných skupin portů jenom v takovém privátním cloudu pro funkci AVS. SÍŤ VLAN vytvořená v privátním cloudu služby AVS se automaticky nakonfiguruje na všech přepínačích připojených k hostitelům privátního cloudu služby AVS.

## <a name="subnets"></a>Podsítě

Podsíť můžete vytvořit, když vytvoříte síť VLAN tak, že definujete adresní prostor podsítě. IP adresa z adresního prostoru je přiřazena jako brána podsítě. Jeden adresní prostor privátní vrstvy 3 je přiřazený pro zákazníka a oblast. V oblasti sítě můžete nakonfigurovat libovolný adresní prostor RFC 1918 bez překrývání adresního prostoru s vaší místní sítí nebo virtuální sítí Azure.

Všechny podsítě můžou ve výchozím nastavení vzájemně komunikovat a snížit tak režijní náklady na konfiguraci směrování mezi privátními cloudy služby AVS. Data východní sítě v různých počítačích ve stejné oblasti zůstanou ve stejné síti vrstvy 3 a přenos přes infrastrukturu místní sítě v rámci dané oblasti. Pro komunikaci mezi privátními cloudy služby AVS v oblasti se nevyžaduje žádný výstup. Tento přístup eliminuje snížení výkonu sítě WAN a odchozích dat při nasazování různých úloh v různých privátních cloudech AVS.

## <a name="vspherevsan-subnets-cidr-range"></a>Rozsah CIDR vSphere/síti vSAN v podsíti

Privátní cloud služby AVS je vytvořený jako izolovaný zásobník VMware (ESXi hosts, vCenter, síti vSAN a NSX), který spravuje Server vCenter. Součásti pro správu se nasazují v síti vybrané pro směrování vSphere/síti vSAN pro sítě. Rozsah směrování sítě je v průběhu nasazení rozdělen do různých podsítí.

* Minimální předpona rozsahu CIDR podsítě vSphere/síti vSAN: **/24**
* Maximální předpona rozsahu CIDR podsítě vSphere/síti vSAN: **/21**

> [!IMPORTANT]
> IP adresy v rozsahu vSphere/síti vSAN CIDR jsou vyhrazené pro použití v infrastruktuře privátního cloudu služby AVS. Nepoužívejte IP adresu v tomto rozsahu na žádném virtuálním počítači.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>omezení rozsahu vSphere/síti vSAN podsítí CIDR

Výběr velikosti rozsahu CIDR v podsítích vSphere/síti vSAN má vliv na velikost privátního cloudu služby AVS. Následující tabulka uvádí maximální počet uzlů, které můžete mít na základě velikosti směrování vSphere/síti vSAN v podsítích.

| Zadaná délka předpony vSphere/síti vSAN podsítí CIDR | Maximální počet uzlů |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-an-avs-private-cloud"></a>Podsítě pro správu vytvořené v privátním cloudu služby AVS

Při vytváření privátního cloudu služby AVS se vytvoří následující podsítě pro správu.

* **Správa systému**. Síť VLAN a podsíť pro virtuální sítě pro správu hostitele ESXi, server DNS a vCenter Server.
* **VMotion**. Síť VLAN a podsíť pro vMotion hostitele ESXi.
* **Síti vSAN**. Síť VLAN a podsíť pro síti vSAN hostitele ESXi.
* **NsxtEdgeUplink1**. SÍŤ VLAN a podsíť pro odchozí připojení VLAN k externí síti.
* **NsxtEdgeUplink2**. SÍŤ VLAN a podsíť pro odchozí připojení VLAN k externí síti.
* **NsxtEdgeTransport**. SÍŤ VLAN a podsíť pro transportní zóny řídí dosah sítí vrstvy 2 v NSX-T.
* **NsxtHostTransport**. SÍŤ VLAN a podsíť pro zónu přenosu hostitelů.

### <a name="management-network-cidr-range-breakdown"></a>Rozpis rozsahu CIDR sítě pro správu

zadaný rozsah vSphere/síti vSAN podsítí CIDR je rozdělen do několika podsítí. V následující tabulce je uveden příklad rozpisu pro povolené předpony. V příkladu se jako rozsah CIDR používá 192.168.0.0.

Příklad:

| Zadané vSphere/síti vSAN podsítě CIDR/předpona | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Správa systému | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Přenos hostitelů NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX – T Edge Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX – T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX – T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí](create-vlan-subnet.md)
