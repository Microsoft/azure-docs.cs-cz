---
title: Řešení Azure VMware (AVS) – kontrolní seznam sítě
description: Kontrolní seznam pro přidělení síťového CIDR v řešení Azure VMware pomocí služby AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025006"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Požadavky na síť pro řešení Azure VMware pomocí služby AVS

Řešení Azure VMware (AVS) nabízí prostředí privátního cloudu VMware, které je přístupné pro uživatele a aplikace z místních prostředí, zařízení spravovaných v podniku a prostředků Azure. Připojení se doručuje prostřednictvím síťových služeb, jako jsou sítě VPN a připojení Azure ExpressRoute. Některé z těchto síťových služeb vyžadují, abyste zadali rozsahy síťových adres pro povolení služeb. 

Tabulky v tomto článku popisují sadu rozsahů adres a odpovídající služby, které používají zadané adresy. Některé z adres jsou povinné a některé jsou závislé na službách, které chcete nasadit. Tyto adresní prostory by se neměly překrývat s žádnými místními podsítěmi, podsítěmi Azure Virtual Network ani plánované podsítě úloh služby AVS.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Rozsahy síťových adres, které jsou vyžadovány pro vytvoření privátního cloudu služby AVS

Během vytváření služby AVS a privátního cloudu služby AVS musíte dodržovat zadané rozsahy směrování mezi doménami sítě (CIDR), a to následujícím způsobem.

| Název/používá se pro     | Popis                                                                                                                            | Rozsah adres            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR brány      | Vyžaduje se pro hraniční služby (brány VPN). Tento CIDR se vyžaduje při vytváření služby AVS a musí se nacházet z prostoru RFC 1918. | /28                      |
| vSphere/síti vSAN CIDR | Vyžadováno pro sítě správy VMware. Tento CIDR se musí zadat během vytváření privátního cloudu pro funkci AVS.                                    | /24,/23 nebo/22 nebo/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Pro síťové připojení Azure k místní síti se vyžaduje rozsah síťových adres.

Připojení z [místní sítě k síti privátního cloudu služby AVS prostřednictvím ExpressRoute](on-premises-connection.md) vytváří připojení Global REACH. Připojení používá Border Gateway Protocol (BGP) k výměně tras mezi vaší místní sítí, vaší privátní cloudovou sítí služby AVS a vašimi sítěmi Azure.

| Název/používá se pro             | Popis                                                                                                                                                                             | Rozsah adres |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Partnerský vztah CIDR ExpressRoute | Vyžaduje se, když používáte ExpressRoute Global Reach pro místní připojení. Tento CIDR musí být k dispozici, když se žádost o připojení Global Reach provede prostřednictvím lístku podpory. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Site-to-site k místní síti

Připojení z [místní sítě k síti privátního cloudu služby AVS pomocí sítě VPN typu Site-to-site](vpn-gateway.md) vyžaduje následující IP adresy, místní síť a identifikátory. 

| Adresa/rozsah adres | Popis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partnerský vztah IP               | Veřejná IP adresa brány místní sítě VPN. Vyžaduje se k navázání připojení VPN typu Site-to-site mezi místním datacentrem a oblastí služby AVS. Tato IP adresa se vyžaduje při vytváření brány VPN typu Site-to-site.                                         |
| Identifikátor partnera       | Identifikátor peer pro místní bránu VPN. To je obvykle stejné jako **IP adresa partnerského uzlu**.  Pokud je v místní bráně VPN zadaný jedinečný identifikátor, musí se zadat identifikátor.  Při vytváření brány VPN typu Site-to-site se vyžaduje ID partnera.   |
| Místní sítě   | Místní předpony, které potřebují přístup k sítím se systémem AVS v oblasti.  Zahrňte všechny předpony z místní sítě, které budou přistupovat k síti služby AVS, včetně klientské sítě, ze které budou uživatelé přistupovat k síti.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Point-to-site

Připojení VPN typu Point-to-site umožňuje přístup k síti služby AVS z klientského počítače. [Chcete-li nastavit síť VPN typu Point-to-site](vpn-gateway.md), je nutné zadat následující rozsah síťových adres.

| Adresa/rozsah adres | Popis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsíť klienta         | Adresy DHCP poskytuje podsíť klienta při připojení pomocí sítě VPN typu Point-to-site. Tato podsíť se vyžaduje při vytváření brány VPN typu Point-to-site na portálu služby AVS. Síť je rozdělená do dvou podsítí, jednu pro připojení UDP a druhou pro připojení TCP. |

## <a name="next-steps"></a>Další kroky

* [Nastavení místní brány firewall pro přístup k privátnímu cloudu služby AVS](on-premises-firewall-configuration.md)
* [Rychlý Start – vytvoření služby AVS](quickstart-create-cloudsimple-service.md)
* [Rychlý Start – konfigurace privátního cloudu služby AVS](quickstart-create-private-cloud.md)
* Další informace o [síťových připojeních Azure](cloudsimple-azure-network-connection.md)
* Další informace o [branách VPN](cloudsimple-vpn-gateways.md)
