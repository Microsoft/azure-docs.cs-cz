---
title: Klíčové pojmy pro správu řešení Azure VMware podle CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje klíčové koncepty pro správu řešení Azure VMware podle CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8677bc7e4b9f4c2b24b5439aeefb307b9c544ed3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86525020"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Klíčové koncepty pro správu řešení Azure VMware podle CloudSimple

Správa řešení Azure VMware podle CloudSimple vyžaduje porozumění následujícím koncepcím:

* Služba CloudSimple, která se zobrazuje jako řešení Azure VMware podle CloudSimple-Service
* Uzel CloudSimple, který se zobrazuje jako řešení Azure VMware podle CloudSimple-Node
* Privátní cloud CloudSimple
* Síťové služby
* Virtuální počítač CloudSimple, který se zobrazuje jako řešení Azure VMware podle CloudSimple – virtuální počítač

## <a name="cloudsimple-service"></a>Služba CloudSimple

Pomocí služby CloudSimple můžete vytvořit a spravovat všechny prostředky, které jsou přidružené k řešení VMware pomocí CloudSimple z Azure Portal. Vytvořte prostředek služby v každé oblasti, ve které chcete službu používat.

Přečtěte si další informace o [službě CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Uzel CloudSimple

Uzel CloudSimple je vyhrazený, holé hostitel s více technologiemi a hostitele úložiště, do kterého je nasazený VMware ESXi hypervisor. Tento uzel je pak začleněn do platforem VMware vSphere, vCenter, síti vSAN a NSX. Jsou povolené i síťové služby CloudSimple a hraniční sítě. Každý uzel slouží jako jednotka výpočetní kapacity a úložiště, kterou můžete zřídit pro vytváření [privátních cloudů CloudSimple](cloudsimple-private-cloud.md). Uzly zřídíte nebo rezervujete v oblasti, kde je dostupná služba CloudSimple.

Přečtěte si další informace o [CloudSimple uzlech](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Privátní cloud CloudSimple

Privátní cloud CloudSimple je izolované prostředí VMware Stack spravované serverem vCenter ve vlastní doméně pro správu. Zásobník VMware zahrnuje hostitele ESXi, vSphere, vCenter, síti vSAN a NSX. Zásobník běží na vyhrazených uzlech (vyhrazený a izolovaný holý hardware) a využívají ho uživatelé prostřednictvím nativních nástrojů VMware, které zahrnují vCenter a NSX Manager. Vyhrazené uzly se nasazují v umístěních Azure a spravují se pomocí Azure. Každý privátní cloud je možné segmentovat a zabezpečit pomocí síťových služeb, jako jsou sítě VLAN a podsítě a tabulky brány firewall. Připojení k místnímu prostředí a síti Azure se vytvářejí pomocí zabezpečených, privátních sítí VPN a připojení Azure ExpressRoute.

Přečtěte si další informace o [privátním cloudu CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Síťové služby

Služba CloudSimple poskytuje síť pro každou oblast, ve které je nasazená vaše služba CloudSimple. Ve výchozím nastavení je síť jedním adresním prostorem TCP vrstvy 3 s povoleným směrováním. Všechny privátní cloudy a podsítě vytvořené v této oblasti spolu vzájemně komunikují bez jakékoli další konfigurace. Distribuované skupiny portů můžete v vCenter vytvořit pomocí sítí VLAN. Pomocí následujících síťových funkcí můžete nakonfigurovat a zabezpečit prostředky úloh ve vašem privátním cloudu:

* [Sítě VLAN a podsítě](cloudsimple-vlans-subnets.md)
* [Tabulky bran firewall](cloudsimple-firewall-tables.md)
* [VPN Gateway](cloudsimple-vpn-gateways.md)
* [Veřejná IP adresa](cloudsimple-public-ip-address.md)
* [Síťová připojení Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Virtuální počítač CloudSimple

Pomocí služby CloudSimple můžete spravovat virtuální počítače VMware z Azure Portal. Jeden nebo více clusterů nebo fondů prostředků z prostředí vSphere lze namapovat na předplatné, ve kterém je služba vytvořená.

Přečtěte si další informace:

* [Virtuální počítače s CloudSimple](cloudsimple-virtual-machines.md)
* [Mapování předplatného Azure](./azure-subscription-mapping.md)
