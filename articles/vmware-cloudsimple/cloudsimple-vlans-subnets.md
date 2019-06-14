---
title: Sítě VLAN a podsítě v řešení VMware podle CloudSimple – Azure
description: Další informace o sítě VLAN a podsítě v privátním cloudu CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d29445054848d798476fed8184b89f9b6c1210f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497587"
---
# <a name="vlans-and-subnets-overview"></a>Přehled sítě VLAN a podsítě

CloudSimple poskytuje sítě v jedné oblasti, ve kterém je nasazená CloudSimple služby.  Síť je jeden adresní prostor TCP vrstvy 3 se směrováním ve výchozím nastavení povolená.  Všechny privátní cloudy a podsítí v této oblasti vytvořit může mezi sebou komunikovat bez jakékoli další konfigurace.  Vytváření skupin distribuované portů v systému vCenter pomocí sítě VLAN.

![CloudSimple síťové topologie](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>Sítě VLAN

Sítě VLAN (síťové vrstvy 2) se vytvoří jeden privátní cloud.  Provoz vrstvy 2 zůstává v rámci hranice privátního cloudu, abyste mohli izolovat místní provoz v rámci privátního cloudu.  Síť VLAN vytvořeny v privátním cloudu slouží k vytváření skupin portů distribuované jenom v cloudu.  Vytvoření privátního cloudu sítě VLAN je automaticky konfigurována na všechny přepínače připojený k hostiteli privátního cloudu.

## <a name="subnets"></a>Podsítě

Při vytváření sítě VLAN, tak, že definujete adresní prostor podsítě je možné vytvořit podsíť. IP adresu z adresního prostoru je přiřazen jako podsíť brány. Jeden privátní adresní prostor vrstvy 3 se přiřazuje zákazníka a oblast. Libovolný RFC 1918 překrývat adresní prostor, s vaší místní síti nebo virtuální síť Azure, můžete nakonfigurovat ve vaší oblasti sítě.

Všechny podsítě můžete komunikovat mezi sebou ve výchozím nastavení, snížení režie konfiguraci směrování mezi privátních cloudů. Východ západ data pro počítače ve stejné oblasti zůstává ve stejné síti vrstvy 3 a přenáší přes místní síťové infrastruktury v rámci oblasti. Žádné výchozí přenos dat je nutné pro komunikaci mezi privátních cloudů v oblasti. Tento přístup se eliminují žádné snížení výkonu sítě WAN a odchozí přenos v nasazení různé úlohy v různých privátních cloudech.

## <a name="vspherevsan-subnets-cidr-range"></a>rozsah CIDR podsítě vSphere/sítě vSAN

Privátní Cloud je vytvořen jako izolované zásobníku VMware (hostitele ESXi, vCenter, sítě vSAN a NSX) prostředí spravované serverem vCenter.  Součásti správy jsou nasazené v síti, vybraný pro **vSphere/síť vSAN podsítě CIDR**.  Síť rozsah CIDR je rozdělen do různých podsítí během nasazení.

Minimální vSphere/síť vSAN podsítě CIDR rozsahu předpony: **/24** maximální vSphere/síť vSAN podsítě CIDR rozsahu předpony:   **/21**

> [!CAUTION]
> IP adresy v rozsahu CIDR vSphere/síť vSAN jsou vyhrazené pro použití infrastruktury privátního cloudu. Nepoužívejte IP adresy v tomto rozsahu na jakýkoli virtuální počítač.


### <a name="vspherevsan-subnets-cidr-range-limits"></a>omezení rozsahu podsítě CIDR vSphere/sítě vSAN

Výběr velikosti rozsah CIDR podsítě vSphere/síť vSAN má vliv na velikosti vašeho privátního cloudu.  Následující tabulka ukazuje maximální počet uzlů, které můžou mít na základě velikosti vSphere/síť vSAN podsítě CIDR.

| Délka předpony pro určené vSphere/síť vSAN podsítě CIDR | Maximální počet uzlů |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Podsítě správy vytvořeny v privátním cloudu

Tyto podsítě správy se vytvoří při vytvoření privátního cloudu. 

* **Správa systému** -sítě VLAN a podsítě pro správu hostitele ESXi sítě, DNS server, vCenter server.
* **Řešení VMotion** -VLAN a podsítě pro síť vMotion hostitelích ESXi.
* **Síť VSAN** -VLAN a podsítě pro síť vSAN hostitelích ESXi.
* **NsxtEdgeUplink1** -VLAN a podsítě pro síť VLAN odchozí připojení k externí síti.
* **NsxtEdgeUplink2** -VLAN a podsítě pro síť VLAN odchozí připojení k externí síti.
* **NsxtEdgeTransport** -sítě VLAN a podsítě pro přenos zóny řídit dosah sítě vrstvy 2 ve NSX T.
* **NsxtHostTransport** -sítě VLAN a podsítě pro hostitele přenosu zóny.

### <a name="management-network-cidr-range-breakdown"></a>Správa sítě CIDR rozsahu rozdělení

Zadaný rozsah CIDR podsítě vSphere/síť vSAN je rozdělen do několika podsítí.  Následující tabulka ukazuje příklad rozdělení pro povolené předpony.  V příkladu **192.168.0.0** jako rozsah CIDR.

Příklad:

| Zadané vSphere/síť vSAN podsítě/předpona CIDR | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Správa systému | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| řešení vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Přenos hostitele NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Přenos Edge NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink2 hrany NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Další postup

* [Vytvoření a Správa sítě VLAN a podsítě](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
