---
title: Klíčové pojmy pro správu řešení Azure VMware (AVS)
description: Popisuje klíčové koncepty pro správu řešení Azure VMware (AVS).
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025227"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Klíčové koncepty správy řešení Azure VMware (AVS)

Správa řešení Azure VMware (AVS) vyžaduje porozumění následujícím koncepcím:

* Služba AVS, která se zobrazuje jako Azure VMware Solutions (AVS) – služba
* Uzel služby AVS, který se zobrazuje jako řešení Azure VMware (AVS) – uzel
* Privátní cloud AVS
* Síťové služby
* Virtuální počítač se službou AVS, který se zobrazuje jako Azure VMware Solutions (AVS) – virtuální počítač

## <a name="avs-service"></a>Služba AVS

Pomocí služby AVS můžete z Azure Portal vytvářet a spravovat všechny prostředky přidružené k řešení VMware (AVS). Vytvořte prostředek služby v každé oblasti, ve které chcete službu používat.

Přečtěte si další informace o [službě AVS](cloudsimple-service.md).

## <a name="avs-node"></a>Uzel AVS

Uzel služby AVS je vyhrazený, holého a nenáročného hostitele úložiště, na který je nasazený VMware ESXi hypervisor. Tento uzel je pak začleněn do platforem VMware vSphere, vCenter, síti vSAN a NSX. Služba AVS síťové služby a hraniční síťové služby jsou také povoleny. Každý uzel slouží jako jednotka výpočetní kapacity a úložiště, kterou můžete zřídit pro vytváření [privátních cloudů služby AVS](cloudsimple-private-cloud.md). Uzly zřídíte nebo rezervujete v oblasti, kde je služba AVS k dispozici.

Přečtěte si další informace o [uzlech AVS](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Privátní cloud AVS

Privátní cloud služby AVS je izolované prostředí VMware Stack spravované serverem vCenter ve vlastní doméně pro správu. Zásobník VMware zahrnuje hostitele ESXi, vSphere, vCenter, síti vSAN a NSX. Zásobník běží na vyhrazených uzlech (vyhrazený a izolovaný holý hardware) a využívají ho uživatelé prostřednictvím nativních nástrojů VMware, které zahrnují vCenter a NSX Manager. Vyhrazené uzly se nasazují v umístěních Azure a spravují se pomocí Azure. Každý privátní cloud služby AVS je možné segmentovat a zabezpečit pomocí síťových služeb, jako jsou sítě VLAN a podsítě a tabulky brány firewall. Připojení k místnímu prostředí a síti Azure se vytvářejí pomocí zabezpečených, privátních sítí VPN a připojení Azure ExpressRoute.

Další informace o [privátním cloudu](cloudsimple-private-cloud.md)pro funkci AVS

## <a name="service-networking"></a>Síťové služby

Služba AVS zajišťuje síť pro oblast, ve které je nasazená vaše služba AVS. Ve výchozím nastavení je síť jedním adresním prostorem TCP vrstvy 3 s povoleným směrováním. Všechny privátní cloudy a podsítě pro funkci AVS vytvořené v této oblasti spolu vzájemně komunikují bez jakékoli další konfigurace. Distribuované skupiny portů můžete v vCenter vytvořit pomocí sítí VLAN. Pomocí následujících síťových funkcí můžete nakonfigurovat a zabezpečit prostředky úloh v privátním cloudu služby AVS:

* [SÍTĚ VLAN a podsítě](cloudsimple-vlans-subnets.md)
* [Tabulky brány firewall](cloudsimple-firewall-tables.md)
* [Brány VPN](cloudsimple-vpn-gateways.md)
* [Veřejná IP adresa](cloudsimple-public-ip-address.md)
* [Připojení k síti Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Virtuální počítač AVS

Pomocí služby AVS můžete spravovat virtuální počítače VMware z Azure Portal. Jeden nebo více clusterů nebo fondů prostředků z prostředí vSphere lze namapovat na předplatné, ve kterém je služba vytvořená.

Další informace:

* [Virtuální počítače pro funkci AVS](cloudsimple-virtual-machines.md)
* [Mapování předplatného Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
