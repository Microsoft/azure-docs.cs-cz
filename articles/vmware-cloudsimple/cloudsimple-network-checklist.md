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
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817472"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Požadavky na síť pro řešení Azure VMware podle CloudSimple

Řešení Azure VMware od CloudSimple nabízí prostředí privátního cloudu VMware, které je přístupné pro uživatele a aplikace z místních prostředí, ze spravovaných zařízení v podniku i z prostředků Azure. Připojení se doručuje pomocí síťových služeb, jako jsou připojení VPN a ExpressRoute.  Některé z Network Services vyžadují, abyste zadali rozsahy síťových adres pro povolení služeb.  Tabulky v tomto článku popisují sadu rozsahů adres a odpovídající služby, které používají zadané adresy.  Některé z adres jsou povinné a některé jsou závislé na službách, které chcete nasadit.  Tyto adresní prostory by se neměly překrývat s žádnými místními podsítěmi, Virtual Networkmi podsítěmi Azure nebo s plánovanými podsítěmi úloh CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Rozsahy síťových adres, které jsou vyžadovány pro vytvoření privátního cloudu

Při vytváření služby CloudSimple a privátního cloudu se vyžaduje následující rozsah směrování sítě.

| Název/používá se pro     | Popis                                                                                                                            | Rozsah adres            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR brány      | Vyžaduje se pro hraniční služby (brány VPN).  Tento CIDR se vyžaduje při vytváření služby CloudSimple a musí být z prostoru RFC 1918. | za 28                      |
| vSphere/síti vSAN CIDR | Vyžadováno pro sítě správy VMware. Tento CIDR musí být zadaný při vytváření privátního cloudu.                                    | /24,/23 nebo/22 nebo/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Pro síťové připojení Azure k místní síti se vyžaduje rozsah síťových adres.

Připojení z [místní sítě k síti privátního cloudu pomocí ExpressRoute](on-premises-connection.md) vytvoří připojení Global REACH.  Připojení vymění směrování tras přes protokol BGP mezi vaší místní sítí, sítí privátního cloudu a vašimi sítěmi Azure.

| Název/používá se pro             | Popis                                                                                                                                                                             | Rozsah adres |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Partnerský vztah CIDR ExpressRoute | Požadováno při použití Global Reach ExpressRoute se používá pro místní připojení. Tento CIDR je potřeba zadat, když se žádost o připojení Global Reach provede prostřednictvím lístku podpory. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Site-to-site k místní síti

Připojení z [místní sítě k síti privátního cloudu pomocí sítě VPN typu Site-to-site](vpn-gateway.md) vyžaduje následující IP adresy, místní síť a identifikátory. 

| Adresa/rozsah adres | Popis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partnerský vztah IP               | Veřejná IP adresa brány místní sítě VPN. Vyžaduje se k navázání připojení VPN typu Site-to-site mezi místním datacentrem a oblastí služby CloudSimple. Tato IP adresa se vyžaduje při vytváření brány VPN typu Site-to-site.                                         |
| Identifikátor partnera       | Identifikátor peer pro místní bránu VPN. To je obvykle stejné jako **IP adresa partnerského uzlu**.  Pokud je v místní bráně VPN zadaný jedinečný identifikátor, musí se zadat identifikátor.  Při vytváření VPN Gateway site-to-site se vyžaduje ID partnera.   |
| Místní sítě   | Místní předpony, které vyžadují přístup k sítím CloudSimple v oblasti.  Zahrňte všechny předpony z místní sítě, které budou přistupovat k síti CloudSimple, včetně klientské sítě, ze které budou uživatelé přistupovat.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Rozsah síťových adres vyžadovaný pro použití připojení VPN typu Point-to-site

Připojení VPN typu Point-to-site umožňuje přístup k síti CloudSimple z klientského počítače.  [Nastavení sítě VPN typu Point-to-site](vpn-gateway.md) vyžaduje, aby byl zadán následující rozsah síťových adres.

| Adresa/rozsah adres | Popis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsíť klienta         | Adresy DHCP se budou předávat z klientské podsítě, když se připojíte pomocí sítě VPN typu Point-to-site. Tato podsíť se vyžaduje při vytváření brány VPN typu Point-to-site na portálu CloudSimple.  Síť bude rozdělena do dvou podsítí a jedna bude použita pro připojení UDP a jiné pro připojení TCP. |

## <a name="next-steps"></a>Další kroky

* [Rychlý Start – vytvoření služby CloudSimple](quickstart-create-cloudsimple-service.md)
* [rychlý Start – vytvoření – privátní cloud](quickstart-create-private-cloud.md)
* Další informace o [síťových připojeních Azure](cloudsimple-azure-network-connection.md)
* Další informace o [branách VPN](cloudsimple-vpn-gateways.md)
