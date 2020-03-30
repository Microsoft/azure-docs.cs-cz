---
title: Zajištění vysoké dostupnosti aplikací při spuštění ve službě VMware v Azure
description: Popisuje cloudsimple funkce vysoké dostupnosti k řešení běžných scénářů selhání aplikací pro aplikace spuštěné v cloudu CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025346"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Zajištění vysoké dostupnosti aplikací při spuštění ve službě VMware v Azure

Řešení CloudSimple poskytuje vysokou dostupnost pro vaše aplikace spuštěné ve službě VMware v prostředí Azure. V následující tabulce jsou uvedeny scénáře selhání a související funkce vysoké dostupnosti.

| Scénář selhání | Aplikace chráněna? | Funkce HA platformy | Funkce VMware HA | Funkce Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Selhání disku | ANO | Rychlé nahrazení neúspěšného uzlu | [O výchozích zásadách úložiště vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Selhání ventilátoru | ANO | Redundantní ventilátory, rychlá výměna neúspěšného uzlu |  |  |
| Selhání nic | ANO | Redundantní nic, rychlé nahrazení neúspěšného uzlu
| Selhání napájení hostitele | ANO | Redundantní napájení |  |  |
| Selhání hostitele ESXi | ANO | rychlá výměna neúspěšného uzlu | [Vysoká dostupnost vMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Selhání virtuálního mísa | ANO | [Služby vyrovnávání zatížení](load-balancers.md)  | [Vysoká dostupnost vMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Balance Balancer pro virtuální počítače VMware bez stavů |
| Selhání portu přepínače listu | ANO | Redundantní nekonto |  |  |
| Selhání přepínače listu | ANO | Redundantní přepínače listů |  |  |
| Selhání racku | ANO | Skupiny umístění |  |  |
| Síťové připojení k místnímu řadiči domény | ANO  | Redundantní síťové služby |  | Redundantní obvody ER |
| Síťové připojení k Azure | ANO | |  | Redundantní obvody ER |
| Selhání datového centra | ANO |  |  | Zóny dostupnosti |
| Regionální selhání | ANO  |  |  | Oblast Azure |

Řešení Azure VMware by CloudSimple poskytuje následující funkce vysoké dostupnosti.

## <a name="fast-replacement-of-failed-node"></a>Rychlé nahrazení neúspěšného uzlu

CloudSimple řízení rovina software nepřetržitě sleduje stav clusterů VMware a zjistí, kdy uzel ESXi selže. Potom automaticky přidá nového hostitele ESXi do postiženého clusteru VMware z fondu snadno dostupných uzlů a převezme uzel, který selhal, z clusteru. Tato funkce zajišťuje, že volná kapacita v clusteru VMware je obnovena rychle tak, aby odolnost clusteru poskytované vSAN a VMware HA je obnovena.

## <a name="placement-groups"></a>Skupiny umístění

Uživatel, který vytvoří privátní cloud, může vybrat oblast Azure a skupinu umístění v rámci vybrané oblasti. Skupina umístění je sada uzlů rozprostřených ve více stojanech, ale v rámci stejného segmentu sítě páteře. Uzly ve stejné skupině umístění se mohou vzájemně oslovit maximálně dvěma dalšími přepínacími směrováními. Skupina umístění se vždy nachází v rámci jedné zóny dostupnosti Azure a zahrnuje více racků. Rovina ovládacího prvku CloudSimple distribuuje uzly privátního cloudu na příčky na základě maximálního úsilí. Uzly v různých skupinách umístění jsou zaručeny umístěny v různých regálech.

## <a name="availability-zones"></a>Zóny dostupnosti

Zóny dostupnosti jsou nabídka s vysokou dostupností, která chrání vaše aplikace a data před selháním datového centra. Zóny dostupnosti jsou speciální fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Každá oblast má jednu zónu dostupnosti. Další informace najdete v tématu [Co jsou zóny dostupnosti v Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundantní okruhy Azure ExpressRoute

Připojení datového centra k virtuální síti Azure pomocí ExpressRoute má redundantní okruhy, které poskytují vysoce dostupné připojení k síti.

## <a name="redundant-networking-services"></a>Redundantní síťové služby

Všechny síťové služby CloudSimple pro privátní cloud (včetně sítě VLAN, brány firewall, veřejných IP adres, Internetu a VPN) jsou navrženy tak, aby byly vysoce dostupné a schopné podporovat službu SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Balancer pro virtuální počítače VMware bez stavů

Uživatelé mohou umístit Azure Layer 7 Balancer před bezstavové virtuální počítače webové vrstvy spuštěné v prostředí VMware k dosažení vysoké dostupnosti pro webovou vrstvu.

## <a name="azure-regions"></a>Oblast Azure

Oblast Azure je sada datových center nasazených v rámci obvodu definovaného latencí a připojených prostřednictvím vyhrazené regionální sítě s nízkou latencí. Podrobnosti najdete v [tématu Oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions).
