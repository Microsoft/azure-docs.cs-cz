---
title: Azure VMware Solution by CloudSimple – kontrolní seznam sítě
description: Kontrolní seznam pro přidělování síťových CIDR v řešení Azure VMware podle CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025006"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Síťové předpoklady pro řešení Azure VMware od CloudSimple

Řešení Azure VMware od CloudSimple nabízí privátní cloudové prostředí VMware, které je přístupné uživatelům a aplikacím z místních prostředí, podnikových zařízení a prostředků Azure. Připojení se dodává prostřednictvím síťových služeb, jako jsou sítě VIRTUÁLNÍ sítě a připojení Azure ExpressRoute. Některé z těchto síťových služeb vyžadují zadání rozsahů síťových adres pro povolení služeb. 

Tabulky v tomto článku popisují sadu rozsahů adres a odpovídající služby, které používají zadané adresy. Některé adresy jsou povinné a některé závisí na služby, které chcete nasadit. Tyto adresní prostory by se neměly překrývat s žádnou místní podsítí, podsítěmi Virtuální sítě Azure nebo plánovanými podsítěmi CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Rozsahy síťových adres potřebné pro vytvoření privátního cloudu

Během vytváření služby CloudSimple a privátního cloudu je nutné dodržovat zadané rozsahy směrování mezi doménami (CIDR) bez třídy (NETWORK), a to následujícím způsobem.

| Název/používá se pro     | Popis                                                                                                                            | Rozsah adres            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| BRÁNA CIDR      | Vyžadováno pro hraniční služby (brány VPN).  Tento CIDR je vyžadovánběhem vytváření služby CloudSimple a musí být z prostoru RFC 1918. | /28                      |
| vSphere/vSAN CIDR | Vyžadováno pro sítě pro správu společnosti VMware. Tento CIDR musí být zadán během vytváření privátního cloudu.                                    | /24 nebo /23 nebo /22 nebo /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Rozsah síťových adres požadovaný pro síťové připojení Azure k místní síti

Připojení z [místní sítě k privátní cloudové síti prostřednictvím ExpressRoute](on-premises-connection.md) vytvoří připojení Global Reach.  Připojení používá protokol BGP (Border Gateway Protocol) k výměně tras mezi místní sítí, privátní cloudovou sítí a sítěmi Azure.

| Název/používá se pro             | Popis                                                                                                                                                                             | Rozsah adres |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Cidr partnerského vztahu ExpressRoute | Povinné při použití expressroute global reach pro místní připojení. Tento CIDR musí být poskytnuta při požadavku na připojení global reach prostřednictvím lístku podpory. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Rozsah síťových adres potřebný pro použití připojení VPN mezi lokalitami k místní síti

Připojení z [místní sítě k privátní cloudové síti pomocí sítě VPN mezi lokalitami](vpn-gateway.md) vyžaduje následující adresy IP, místní síť a identifikátory. 

| Rozsah adres/adres | Popis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ip partnerské strany               | Místní veřejná IP adresa brány VPN. Vyžaduje se k navázání připojení VPN mezi místním datovým centrem a oblastí služby CloudSimple. Tato IP adresa je vyžadována při vytváření brány VPN mezi lokalitami.                                         |
| Identifikátor partnera       | Peer identifikátor místní brány VPN. To je obvykle stejné jako **peer IP**.  Pokud je v místní bráně VPN zadán jedinečný identifikátor, musí být určen identifikátor.  ID druhé strany je vyžadováno při vytváření brány VPN typu site-to-site.   |
| Místní sítě   | Místní předpony, které potřebují přístup k cloudovým sítím v oblasti.  Zahrnout všechny předpony z místní sítě, která bude přistupovat k síti CloudSimple, včetně klientské sítě, ze které budou uživatelé přistupovat k síti.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Rozsah síťových adres požadovaný pro použití připojení VPN mezi místy

Připojení VPN bodu k webu umožňuje přístup k síti CloudSimple z klientského počítače.  [Chcete-li nastavit síť VPN typu point-to-site](vpn-gateway.md), je nutné zadat následující rozsah síťových adres.

| Rozsah adres/adres | Popis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsíť klienta         | Adresy DHCP jsou poskytovány podsítí klienta při připojení pomocí sítě VPN bodu na pracoviště. Tato podsíť je vyžadována při vytváření brány VPN bodu na místo na portálu CloudSimple.  Síť je rozdělena do dvou podsítí; jeden pro připojení UDP a druhý pro připojení TCP. |

## <a name="next-steps"></a>Další kroky

* [Místní nastavení brány firewall pro přístup k privátnímu cloudu](on-premises-firewall-configuration.md)
* [Úvodní příručka – vytvoření služby CloudSimple](quickstart-create-cloudsimple-service.md)
* [Úvodní příručka – Konfigurace privátního cloudu](quickstart-create-private-cloud.md)
* Další informace o [síťových připojeních Azure](cloudsimple-azure-network-connection.md)
* Další informace o [privátní chodnících VPN](cloudsimple-vpn-gateways.md)
