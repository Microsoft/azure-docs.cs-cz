---
title: Klíčové koncepty pro správu řešení Azure VMware pomocí CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje klíčové koncepty pro správu řešení Azure VMware pomocí CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869030"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Klíčové koncepty pro správu řešení Azure VMware pomocí CloudSimple

Správa řešení Azure VMware pomocí CloudSimple vyžaduje pochopení následujících konceptů:

* Služba CloudSimple, která se zobrazuje jako řešení Azure VMware od CloudSimple – služba
* Uzel CloudSimple, který se zobrazuje jako řešení Azure VMware od CloudSimple – uzel
* CloudSimple privátní cloud
* Síť služeb
* CloudSimple virtuální počítač, který se zobrazí jako Azure VMware Solutions od CloudSimple – virtuální počítač

## <a name="cloudsimple-service"></a>CloudSimple služba

Pomocí služby CloudSimple můžete vytvářet a spravovat všechny prostředky přidružené k řešení VMware pomocí CloudSimple z portálu Azure. Vytvořte prostředek služby ve všech oblastech, kde chcete službu používat.

Další informace o [službě CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple uzel

Uzel CloudSimple je vyhrazený, holý, hyperkonvergovaný výpočetní a úložný hostitel, do kterého je nasazen hypervisor VMware ESXi. Tento uzel je pak začleněn do platforem VMware vSphere, vCenter, vSAN a NSX. CloudSimple síťové služby a hraniční síťové služby jsou také povoleny. Každý uzel slouží jako jednotka výpočetní a úložné kapacity, kterou můžete zřídit k vytvoření [privátních cloudových cloudových cloudů](cloudsimple-private-cloud.md). Zřizujete nebo rezervujete uzly v oblasti, kde je služba CloudSimple k dispozici.

Další informace o [cloudových jednoduchých uzlech](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privátní cloud

Privátní cloud CloudSimple je izolované prostředí zásobníku VMware spravované serverem vCenter ve vlastní doméně pro správu. Zásobník VMware obsahuje hostitele ESXi, vSphere, vCenter, vSAN a NSX. Zásobník běží na vyhrazených uzlech (vyhrazený a izolovaný holý hardware) a je spotřebován uživateli prostřednictvím nativních nástrojů VMware, které zahrnují vCenter a NSX Manager. Vyhrazené uzly se nasazují v umístěních Azure a jsou spravované Azure. Každý soukromý cloud lze segmentovat a zabezpečit pomocí síťových služeb, jako jsou sítě VLAN a podsítě a tabulky brány firewall. Připojení k místnímu prostředí a síti Azure se vytvářejí pomocí zabezpečené, privátní VPN a připojení Azure ExpressRoute.

Další informace o [cloudovém privátním cloudu](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Síť služeb

Služba CloudSimple poskytuje síť podle oblasti, kde je vaše služba CloudSimple nasazena. Síť je jeden adresní prostor vrstvy TCP vrstvy 3, ve kterých je směrování povoleno. Všechny privátní cloudy a podsítě vytvořené v této oblasti vzájemně komunikují bez jakékoli další konfigurace. Skupiny distribuovaných portů v programu VCenter vytváříte pomocí programů VLAN. Ke konfiguraci a zabezpečení prostředků pracovního vytížení v privátním cloudu můžete použít následující síťové funkce:

* [Sítě VLAN a podsítě](cloudsimple-vlans-subnets.md)
* [Tabulky bran firewall](cloudsimple-firewall-tables.md)
* [Brány VPN](cloudsimple-vpn-gateways.md)
* [Veřejná IP adresa](cloudsimple-public-ip-address.md)
* [Síťová připojení Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuální počítač

Pomocí služby CloudSimple můžete spravovat virtuální počítače VMware z portálu Azure. Jeden nebo více clusterů nebo fondů prostředků z vašeho prostředí vSphere lze mapovat na předplatné, ve kterém je služba vytvořena.

Další informace:

* [CloudSimple virtuální počítače](cloudsimple-virtual-machines.md)
* [Mapování předplatného Azure](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
