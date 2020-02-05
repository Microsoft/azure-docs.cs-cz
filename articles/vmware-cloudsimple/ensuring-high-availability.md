---
title: Zajistěte vysokou dostupnost aplikace při spuštění v prostředí VMware v Azure
description: Popisuje funkce služby AVS s vysokou dostupností pro řešení běžných scénářů selhání aplikací pro aplikace běžící v privátním cloudu služby AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b32f7f3f38098f935382cce46d8251340784b940
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025346"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Zajistěte vysokou dostupnost aplikace při spuštění v prostředí VMware v Azure

Řešení AVS poskytuje vysokou dostupnost pro vaše aplikace běžící na VMware v prostředí Azure. V následující tabulce jsou uvedeny scénáře selhání a související funkce vysoké dostupnosti.

| Scénář selhání | Je aplikace chráněná? | Funkce HA platformy | Funkce VMware HA | Azure HA – funkce |
------------ | ------------- | ------------ | ------------ | ------------- |
| Selhání disku | Ano | Rychlé nahrazení neúspěšného uzlu | [O výchozích zásadách úložiště síti vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Selhání ventilátoru | Ano | Redundantní ventilátory, rychlá výměna neúspěšného uzlu |  |  |
| Selhání síťové karty | Ano | Redundantní síťová karta, rychlá výměna neúspěšného uzlu
| Selhání napájení hostitele | Ano | Redundantní zdroj napájení |  |  |
| Selhání hostitele ESXi | Ano | rychlé nahrazení neúspěšného uzlu | [VMware vSphere vysoká dostupnost](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Chyba virtuálního počítače | Ano | [Nástroje pro vyrovnávání zatížení](load-balancers.md)  | [VMware vSphere vysoká dostupnost](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer pro bezstavové virtuální počítače VMware |
| Selhání portu přepínání listů | Ano | Redundantní síťová karta |  |  |
| Selhání přepínání listů | Ano | Redundantní přepínače na list |  |  |
| Selhání racku | Ano | Skupiny umístění |  |  |
| Síťové připojení k místnímu řadiči domény | Ano  | Redundantní síťové služby |  | Redundantní okruhy ER |
| Síťové připojení k Azure | Ano | |  | Redundantní okruhy ER |
| Selhání datacentra | Ano |  |  | Zóny dostupnosti |
| Regionální selhání | Ano  |  |  | Oblasti Azure |

Řešení Azure VMware pomocí služby AVS nabízí následující funkce vysoké dostupnosti.

## <a name="fast-replacement-of-failed-node"></a>Rychlé nahrazení neúspěšného uzlu

Software plochy ovládacího prvku AVS nepřetržitě monitoruje stav clusterů VMware a detekuje, kdy dojde k chybě uzlu ESXi. Pak automaticky přidá nového hostitele ESXi do ovlivněného clusteru VMware z fondu snadno dostupných uzlů a převezme neúspěšný uzel z clusteru. Tato funkce zajišťuje, že se v clusteru VMware rychle obnoví volná kapacita, aby se obnovila odolnost clusteru poskytovaná síti vSAN a VMware HA.

## <a name="placement-groups"></a>Skupiny umístění

Uživatel, který vytváří privátní cloud služby AVS, může vybrat oblast Azure a skupinu umístění v rámci vybrané oblasti. Skupina umístění je sada uzlů rozložená mezi více stojanů, ale ve stejném segmentu sítě páteře. Uzly v rámci stejné skupiny umístění se můžou vzájemně navázat, což může mít maximálně dva segmenty dalšího přecházení. Skupina umístění je vždy v rámci jedné zóny dostupnosti Azure a zahrnuje více stojanů. Rovina řízení služby AVS distribuuje uzly privátního cloudu služby AVS napříč různými racky na základě nejlepšího úsilí. U uzlů v různých skupinách umístění je zaručeno, že budou umístěny do různých stojanů.

## <a name="availability-zones"></a>Zóny dostupnosti

Zóny dostupnosti jsou nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny dostupnosti jsou zvláštní fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Každá oblast má jednu zónu dostupnosti. Další informace najdete v tématu [co je zóny dostupnosti v Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundantní okruhy Azure ExpressRoute

Připojení datového centra k virtuální síti Azure pomocí ExpressRoute má redundantní okruhy k zajištění připojení k síti s vysokou dostupností.

## <a name="redundant-networking-services"></a>Redundantní síťové služby

Všechny síťové služby funkce AVS pro privátní cloud služby AVS (včetně sítě VLAN, brány firewall, veřejných IP adres, internetu a sítě VPN) jsou navržené tak, aby byly vysoce dostupné a mohly podporovat smlouvu SLA služby.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure vrstvy 7 Load Balancer pro bezstavové virtuální počítače VMware

Uživatelé můžou do Azure vrstvy 7 umístit Load Balancer před bezstavovým virtuálním počítačům s nestavovou vrstvou běžící v prostředí VMware, aby dosáhli vysoké dostupnosti pro webovou vrstvu.

## <a name="azure-regions"></a>Oblasti Azure

Oblast Azure je sada datových center nasazených v hraničním prostředí definovaném latencí a připojená prostřednictvím sítě s nízkou latencí. Podrobnosti najdete v tématu [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions).
