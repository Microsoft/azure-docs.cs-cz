---
title: Informace o Azure ExpressRoute přímo | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled služby ExpressRoute Direct (Preview)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: c33bec76fe17336221c873778c2993d75fec81e8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962219"
---
# <a name="about-expressroute-direct-preview"></a>Informace o ExpressRoute Direct (Preview)

Přímé ExpressRoute poskytuje zákazníkům možnost připojení přímo do globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. Přímé ExpressRoute poskytuje připojení duální 100Gbps, která podporuje připojení k aktivní/aktivní ve velkém měřítku. 

Klíčové funkce, které poskytuje přímé ExpressRoute patří, ale nejsou omezené na:

* Masivní Ingestování do službami jako Storage a Cosmos DB 
* Fyzické izolace pro obory, které jsou reguluje a vyžaduje vyhrazený a izolovanou připojení jako: bankovnictví, Government a maloobchodního prodeje 
* Přesná kontrola nad okruh distribuce podle organizační jednotky

> [!IMPORTANT]
> Přímé ExpressRoute je aktuálně ve verzi Preview.
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Registrace verze Preview

Před využitím ExpressRoute přímo, musí nejdřív zaregistrovat předplatné ve verzi preview. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu <ExpressRouteDirect@microsoft.com>. Přímé ExpressRoute je funkce na podnikové úrovni. Uveďte další podrobnosti:

* Scénáře, které pokud chcete provést s **přímo ExpressRoute**
* Naleznete v tématu předvolby umístění - [partnery a umístění partnerského vztahu](expressroute-locations-providers.md) úplný seznam všech umístění
* Časová osa pro implementaci
* Nějaké dotazy, které máte týkajících se služeb

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute přes poskytovatele služeb a přímé ExpressRoute

| **ExpressRoute přes poskytovatele služeb** | **Přímé ExpressRoute** | 
| --- | --- | 
| Využívá poskytovatele služeb a umožňuje rychlé zprovoznění a možností připojení do stávající infrastruktury | Vyžaduje infrastrukturu 100Gbps a úplnou správu všech vrstev
| Se integruje s stovky poskytovatelů, včetně Ethernet a MPLS | Přímé/Dedicated kapacitu pro regulované odvětví a masivní ingestování | 
| SKU okruhů z 10 50 MB/s | Skladová jednotka okruhů z 1 GB/s 100Gbps
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta/Cloud Service providers nebo více organizačních jednotek

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute přímo

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.  

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a může přistupovat k libovolné oblasti v geopolitické oblasti, ve výchozím nastavení a přístup všech globálních oblastí okruhu premium.  

Funkce ve většině případů je stejná jako okruhů, které využívají poskytovatele služeb ExpressRoute pro provoz. Pro podporu dalších členitosti a nové funkce prostřednictvím ExpressRoute přímo, jsou některé klíčové funkce, které existují v s přímým přístupem okruhy ExpressRoute.

## <a name="circuit-skus"></a>SKU okruhu

Přímé ExpressRoute podporuje scénáře ingestování velké objemy dat do služby Azure storage a další velké objemy dat služby. ExpressRoute okruhy na přímé ExpressRoute teď také podporu **40G** a **100G** okruh skladové položky. 

## <a name="vlan-tagging"></a>Označování příznaky VLAN

Přímé ExpressRoute podporuje QinQ a Dot1Q označování příznaky VLAN.

* **Označování příznaky VLAN QinQ** umožňuje izolované domény směrování na základě okruhu ExpressRoute. Azure dynamicky přiděluje značku S při vytvoření okruhu a nedá se změnit. Každý partnerský vztah v okruhu (privátní a Microsoft) budou využívat jedinečné C – značka jako síť VLAN. C – značka nemusí být jedinečný v rámci okruhy na portech přímo ExpressRoute. 

* **Označování příznaky VLAN Dot1Q** umožňuje jeden označené sítě VLAN na jednotlivé dvojice portů přímo ExpressRoute. Značku jazyka C použít u partnerského vztahu musí být jedinečný ve všech okruhů a partnerské vztahy na portu dvojici přímo ExpressRoute.

## <a name="workflow"></a>Pracovní postup

![pracovní postup](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>SLA

Přímé ExpressRoute poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivní/aktivní redundantní připojení globální síti Microsoftu. Infrastruktura ExpressRoute je redundantní a je redundantní a rozmanitým připojení globální síti Microsoftu a odpovídajícím způsobem se škáluje s požadavky zákazníků. Během období preview nebude žádná smlouva SLA a by měly být považovány za pouze pro neprodukční úlohy.

## <a name="next-steps"></a>Další postup

[Konfigurace ExpressRoute přímo](expressroute-howto-erdirect.md)