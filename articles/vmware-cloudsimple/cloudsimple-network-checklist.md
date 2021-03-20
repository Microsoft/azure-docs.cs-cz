---
title: Řešení Azure VMware podle CloudSimple – kontrolní seznam sítě
description: Kontrolní seznam pro přidělení síťového CIDR v řešení Azure VMware podle CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025006"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Požadavky na síť pro řešení Azure VMware podle CloudSimple

Řešení Azure VMware od CloudSimple nabízí prostředí privátního cloudu VMware, které je přístupné pro uživatele a aplikace z místních prostředí, zařízení spravovaných v podniku a prostředků Azure. Připojení se doručuje prostřednictvím síťových služeb, jako jsou sítě VPN a připojení Azure ExpressRoute. Některé z těchto síťových služeb vyžadují, abyste zadali rozsahy síťových adres pro povolení služeb. 

Tabulky v tomto článku popisují sadu rozsahů adres a odpovídající služby, které používají zadané adresy. Některé z adres jsou povinné a některé jsou závislé na službách, které chcete nasadit. Tyto adresní prostory by se neměly překrývat s žádnými místními podsítěmi, Virtual Networkmi podsítěmi Azure nebo s plánovanými podsítěmi úloh CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Rozsahy síťových adres, které jsou vyžadovány pro vytvoření privátního cloudu

Během vytváření služby CloudSimple a privátního cloudu je nutné dodržovat zadané rozsahy směrování mezi doménami sítě (CIDR), a to následujícím způsobem.

| Název/používá se pro     | Description                                                                                                                            | Rozsah adres            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR brány      | Vyžaduje se pro hraniční služby (brány VPN).  Tento CIDR se vyžaduje při vytváření služby CloudSimple a musí být z prostoru RFC 1918. | za 28                      |
| vSphere/síti vSAN CIDR | Vyžadováno pro sítě správy VMware. Tento CIDR musí být zadaný při vytváření privátního cloudu.                                    | /24,/23 nebo/22 nebo/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Pro síťové připojení Azure k místní síti se vyžaduje rozsah síťových adres.

Připojení z [místní sítě k síti privátního cloudu prostřednictvím ExpressRoute](on-premises-connection.md) vytváří připojení Global REACH.  Připojení používá Border Gateway Protocol (BGP) k výměně tras mezi vaší místní sítí, vaší privátní cloudovou sítí a vašimi sítěmi Azure.

| Název/používá se pro             | Description                                                                                                                                                                             | Rozsah adres |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Partnerský vztah CIDR ExpressRoute | Vyžaduje se, když používáte ExpressRoute Global Reach pro místní připojení. Tento CIDR musí být k dispozici, když se žádost o připojení Global Reach provede prostřednictvím lístku podpory. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Site-to-site k místní síti

Připojení z [místní sítě k síti privátního cloudu pomocí sítě VPN typu Site-to-site](vpn-gateway.md) vyžaduje následující IP adresy, místní síť a identifikátory. 

| Adresa/rozsah adres | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partnerský vztah IP               | Veřejná IP adresa brány místní sítě VPN. Vyžaduje se k navázání připojení VPN typu Site-to-site mezi místním datacentrem a oblastí služby CloudSimple. Tato IP adresa se vyžaduje při vytváření brány VPN typu Site-to-site.                                         |
| Identifikátor partnera       | Identifikátor peer pro místní bránu VPN. To je obvykle stejné jako **IP adresa partnerského uzlu**.  Pokud je v místní bráně VPN zadaný jedinečný identifikátor, musí se zadat identifikátor.  Při vytváření brány VPN typu Site-to-site se vyžaduje ID partnera.   |
| Místní sítě   | Místní předpony, které vyžadují přístup k sítím CloudSimple v oblasti.  Zahrňte všechny předpony z místní sítě, které budou mít přístup k síti CloudSimple, včetně klientské sítě, ze které budou uživatelé přistupovat k síti.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Point-to-site

Připojení VPN typu Point-to-site umožňuje přístup k síti CloudSimple z klientského počítače.  [Chcete-li nastavit síť VPN typu Point-to-site](vpn-gateway.md), je nutné zadat následující rozsah síťových adres.

| Adresa/rozsah adres | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsíť klienta         | Adresy DHCP poskytuje podsíť klienta při připojení pomocí sítě VPN typu Point-to-site. Tato podsíť se vyžaduje při vytváření brány VPN typu Point-to-site na portálu CloudSimple.  Síť je rozdělená do dvou podsítí. jednu pro připojení UDP a druhou pro připojení TCP. |

## <a name="next-steps"></a>Další kroky

* [Nastavení místní brány firewall pro přístup k vašemu privátnímu cloudu](on-premises-firewall-configuration.md)
* [Rychlý Start – vytvoření služby CloudSimple](quickstart-create-cloudsimple-service.md)
* [Rychlý Start – konfigurace privátního cloudu](quickstart-create-private-cloud.md)
* Další informace o [síťových připojeních Azure](cloudsimple-azure-network-connection.md)
* Další informace o [branách VPN](cloudsimple-vpn-gateways.md)
