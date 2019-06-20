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
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165190"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Klíčové koncepty pro správu řešení VMware Azure podle CloudSimple

Správě řešení VMware Azure podle CloudSimple vyžaduje znalost následujících konceptů:

* CloudSimple služba, která je zobrazena jako řešení VMware Azure CloudSimple – služby
* Uzel CloudSimple, která je zobrazena jako řešení VMware Azure CloudSimple - Node
* CloudSimple privátního cloudu
* Síťové služby
* CloudSimple virtuálního počítače, které se zobrazují jako řešení VMware Azure ve CloudSimple – virtuální počítač

## <a name="cloudsimple-service"></a>CloudSimple služby

Ve službě CloudSimple můžete vytvořit a spravovat všechny prostředky, které jsou spojené s řešeními VMware podle CloudSimple z portálu Azure portal. Vytvořte prostředek služby v každé oblasti, kde máte v úmyslu používat službu.

Další informace o [CloudSimple služby](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple uzlu

Uzel CloudSimple je vyhrazený, holé počítače, hyperkonvergovaného výpočetní výkon a úložiště hostitele, do kterého je nasazený hypervisor VMware ESXi. Tento uzel je pak součástí VMware vSphere, vCenter, sítě vSAN a NSX platformy. CloudSimple síťových služeb a síťových služeb edge jsou také povoleny. Každý uzel slouží jako jednotka úložnou a výpočetní kapacitu můžete zřídit vytvořit [privátních cloudů CloudSimple](cloudsimple-private-cloud.md). Zřízení nebo rezerva uzly v oblasti, kde je k dispozici služba CloudSimple.


Další informace o [CloudSimple uzly](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privátního cloudu

Privátní cloud CloudSimple je izolované prostředí zásobníku VMware spravuje server vCenter ve vlastní doméně pro správu. Zásobníku VMware zahrnuje hostitele ESXi, vSphere, vCenter, sítě vSAN a NSX. Zásobníku se spustí na vyhrazené uzly (izolované a vyhrazené holého hardwaru) a je využívána uživatelům prostřednictvím nativních nástrojů VMware, které zahrnují vCenter a NSX správce. Vyhrazené uzly jsou nasazené v umístění Azure a spravuje Azure. Každý z privátních cloudů lze segmentované a zabezpečeného za použití síťové služby, jako jsou sítě VLAN a podsítě a bránu firewall tabulky. Připojení k místním prostředím a síť Azure jsou vytvářeny pomocí zabezpečené privátní sítě VPN a Azure ExpressRoute připojení.

Další informace o [privátního cloudu CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Síťové služby

Služba CloudSimple poskytuje sítě v jedné oblasti, ve kterém je nasazená CloudSimple služby. Síť je jeden adresní prostor TCP vrstvy 3 se směrováním ve výchozím nastavení povolená. Všechny privátní cloudy a podsítí v této oblasti vytvořit vzájemně komunikovat bez jakékoli dodatečné konfigurace. Vytváření skupin distribuované portů v systému vCenter pomocí sítě VLAN. Následující funkce sítě můžete použít ke konfiguraci a zabezpečení vašich prostředků úlohy ve vašem privátním cloudu:

* [Sítě VLAN a podsítě](cloudsimple-vlans-subnets.md)
* [Brána firewall tabulky](cloudsimple-firewall-tables.md)
* [VPN Gateway](cloudsimple-vpn-gateways.md)
* [Veřejná IP adresa](cloudsimple-public-ip-address.md)
* [Připojení k síti Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuálního počítače

Se službou CloudSimple můžete spravovat virtuální počítače VMware na webu Azure Portal. Jeden nebo více clusterů nebo fondy prostředků z vašeho prostředí vSphere lze mapovat na předplatné, na kterém je služba vytvořena.

Další informace:

* [CloudSimple virtuálních počítačů](cloudsimple-virtual-machines.md)
* [Mapování předplatného Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
