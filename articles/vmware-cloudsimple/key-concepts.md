---
title: Klíčové koncepty pro správu řešení VMware Azure podle CloudSimple
description: Popisuje klíčové koncepty pro správu řešení VMware Azure podle CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358165"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Klíčové koncepty pro správu řešení VMware Azure podle CloudSimple

Správa řešení VMware Azure podle CloudSimple vyžaduje znalost následujících konceptů.

* Služba CloudSimple (zobrazené jako řešení VMware Azure podle CloudSimple - Service)
* Uzel CloudSimple (podle CloudSimple - uzlu zobrazuje jako řešení VMware Azure)
* CloudSimple privátního cloudu
* Síť služby
* CloudSimple virtuálního počítače (podle CloudSimple – virtuální počítač zobrazený jako řešení VMware Azure)

## <a name="cloudsimple-service"></a>CloudSimple služby

Služba CloudSimple umožňuje vytvářet a spravovat všechny prostředky, které jsou spojené s řešeními VMware podle CloudSimple z portálu Azure portal. Vytvořte prostředek služby v každé oblasti, kde máte v úmyslu používat službu. 

Další informace o [CloudSimple služby](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>CloudSimple uzlu

Uzel CloudSimple je vyhrazený, holých počítačů, hyperkonvergovaného výpočetní výkon a úložiště hostitele, do kterého hypervisor VMware ESXi nasazený. Tento uzel je pak součástí VMware vSphere, vCenter, sítě vSAN a NSX platformy. CloudSimple síťových služeb a síťových služeb edge jsou také povoleny. Každý uzel slouží jako jednotka úložnou a výpočetní kapacitu, který si můžete zakoupit k vytvoření [CloudSimple privátních Cloudů](cloudsimple-private-cloud.md). Jste je zakoupili nebo rezerva uzly v oblasti, kde je k dispozici služba CloudSimple.


Další informace o [CloudSimple uzly](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>CloudSimple privátního cloudu

CloudSimple privátního cloudu je izolované prostředí zásobníku VMware spravuje server vCenter ve vlastní doméně pro správu. VMware stack zahrnuje hostitele ESXi, vSphere, vCenter, sítě vSAN a NSX.  Zásobníku se spustí na vyhrazené uzly (izolované a vyhrazené holé počítače hardware) a je využívána uživatelům prostřednictvím nativních nástrojů VMware, které zahrnují vCenter a NSX správce. Vyhrazené uzly jsou nasazené v umístění Azure a spravuje Azure. Každý privátní Cloud může být segmentované a zabezpečená pomocí síťové služby, jako jsou sítě VLAN a podsítě a tabulky s branou firewall.  Připojení k místním prostředím a síť Azure se vytvoří s použitím privátní, zabezpečené sítě VPN a připojení Azure ExpressRoute.

Další informace o [CloudSimple privátního cloudu](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Síťové služby

Služba CloudSimple poskytuje sítě v jedné oblasti, ve kterém je nasazená CloudSimple služby. Síť je jeden adresní prostor TCP vrstvy 3 se směrováním ve výchozím nastavení povolená. Všechny privátní Cloudy a podsítí v této oblasti vytvořit vzájemně komunikovat bez jakékoli dodatečné konfigurace. Vytváření skupin distribuované portů v systému vCenter pomocí sítě VLAN.  Následující funkce sítě můžete použít ke konfiguraci a zabezpečení vašich úloh prostředků do privátního cloudu.

* [Sítě VLAN a podsítě](cloudsimple-vlans-subnets.md)
* [Brána firewall tabulky](cloudsimple-firewall-tables.md)
* [VPN Gateway](cloudsimple-vpn-gateways.md)
* [Veřejná IP adresa](cloudsimple-public-ip-address.md)
* [Připojení k síti Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuálního počítače

Služba CloudSimple umožňuje spravovat virtuální počítače VMware na webu Azure Portal. Jeden nebo více clusterů nebo fondy prostředků z vašeho prostředí vSphere lze mapovat na předplatné, na kterém je služba vytvořena.

Další informace:

* [CloudSimple virtuálních počítačů](cloudsimple-virtual-machines.md)
* [Mapování předplatného Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
