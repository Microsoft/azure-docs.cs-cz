---
title: Informace o Azure ExpressRoute přímo | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled služby ExpressRoute Direct (Preview)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: ce4bc88f6b599bb3f5f760b2ba83c81f2b6acc5f
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405174"
---
# <a name="about-expressroute-direct-preview"></a>Informace o ExpressRoute Direct (Preview)

ExpressRoute Direct poskytuje zákazníkům možnost připojit se přímo ke globální síti Microsoftu v místech partnerských uzlů strategicky rozmístěných po celém světě. Přímé ExpressRoute poskytuje duální 100 GB/s připojením, která podporuje připojení k aktivní/aktivní ve velkém měřítku. 

Klíčové funkce, které poskytuje ExpressRoute Direct patří, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB 
* Fyzická izolace pro odvětví, která jsou regulovaná a vyžadují vyhrazené a izolované připojení, například: bankovnictví, státní správa a maloobchodní prodej 
* Podrobné řízení distribuce okruhů podle organizační jednotky

> [!IMPORTANT]
> Přímé ExpressRoute je aktuálně ve verzi Preview.
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Registrace verze Preview

Před použitím ExpressRoute přímo, musí nejdřív zaregistrovat předplatné ve verzi preview. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu <ExpressRouteDirect@microsoft.com>. Přímé ExpressRoute je funkce na podnikové úrovni. Uveďte další podrobnosti:

* Scénáře, které pokud chcete provést s **přímo ExpressRoute**
* Naleznete v tématu předvolby umístění - [partnery a umístění partnerského vztahu](expressroute-locations-providers.md) úplný seznam všech umístění
* Časová osa pro implementaci
* Další otázky

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute přes poskytovatele služeb a přímé ExpressRoute

| **ExpressRoute přes poskytovatele služeb** | **Přímé ExpressRoute** | 
| --- | --- | 
| Využívá poskytovatele služeb a umožňuje rychlé zprovoznění a možností připojení do stávající infrastruktury | Vyžaduje 100 GB/s infrastrukturou a úplnou správu všech vrstev
| Se integruje s stovky poskytovatelů, včetně Ethernet a MPLS | Přímé/Dedicated kapacitu pro regulované odvětví a masivní ingestování | 
| SKU okruhů z 50 MB/s až 10 GB/s | Skladová jednotka okruhů z 5 GB/s 100 GB/s (5 GB/s, 10 GB/s, 40 GB/s, 100 GB/s)
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta/Cloud Service providers nebo více organizačních jednotek

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute přímo

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.  

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a může přistupovat k libovolné oblasti v geopolitické oblasti, ve výchozím nastavení a přístup všech globálních oblastí okruhu premium.  

Funkce ve většině případů je stejná jako okruhů, které využívají poskytovatele služeb ExpressRoute pro provoz. Pro podporu dalších členitosti a nové funkce prostřednictvím ExpressRoute přímo, jsou některé klíčové funkce, které existují v s přímým přístupem okruhy ExpressRoute.

## <a name="circuit-skus"></a>SKU okruhu

Přímé ExpressRoute podporuje scénáře ingestování velké objemy dat do služby Azure storage a další velké objemy dat služby. ExpressRoute okruhy na přímé ExpressRoute teď také podporu **40 GB/s** a **100 GB/s** okruh skladové položky. 

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
