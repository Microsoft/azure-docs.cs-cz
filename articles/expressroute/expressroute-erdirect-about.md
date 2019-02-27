---
title: Informace o ExpressRoute přímo – Azure | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled služby ExpressRoute přímo
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: fb9dc5116ba23d57c7f2fe543e734759e8bbcc7b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881596"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

Přímé ExpressRoute poskytuje možnost připojení přímo do globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. Přímé ExpressRoute poskytuje připojení duální 100 GB/s, která podporuje připojení k aktivní/aktivní ve velkém měřítku.

Klíčové funkce, které poskytuje ExpressRoute Direct patří, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzické izolace pro obory, které upraví a vyžadují izolované a vyhrazené připojení jako: Bankovnictví, Government a maloobchodního prodeje
* Podrobné řízení distribuce okruhů podle organizační jednotky

## <a name="onboard-to-expressroute-direct"></a>Připojení k ExpressRoute přímo

Před použitím ExpressRoute přímo, musí nejdřív zaregistrovat vaše předplatné. Při registraci odeslat e-mailu <ExpressRouteDirect@microsoft.com> svým ID předplatného, včetně následující podrobnosti:

* Scénáře, které pokud chcete provést s **přímo ExpressRoute**
* Naleznete v tématu předvolby umístění - [partnery a umístění partnerského vztahu](expressroute-locations-providers.md) úplný seznam všech umístění
* Časová osa pro implementaci
* Další otázky

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute přes poskytovatele služeb a přímé ExpressRoute

| **ExpressRoute přes poskytovatele služeb** | **Přímé ExpressRoute** | 
| --- | --- |
| Využívá poskytovatelů služeb a umožňuje rychlé zprovoznění a možností připojení do stávající infrastruktury | Vyžaduje infrastrukturu 100 GB/s a úplnou správu všech vrstev
| Se integruje s stovky poskytovatelů, včetně Ethernet a MPLS | Přímé/Dedicated kapacitu pro regulované odvětví a masivní ingestování |
| Skladová jednotka okruhů z 50 MB/s 10 GB/s | Zákazník si může vybrat kombinaci okruh následující SKU: 5 GB/s, 10 GB/s, 40 GB/s, 100 GB/s – omezený na celkem 200 GB/s
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta/Cloud Service providers nebo více organizačních jednotek

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute přímo

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.  

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a může přistupovat k libovolné oblasti v geopolitické oblasti, ve výchozím nastavení a přístup všech globálních oblastí okruhu premium.  

Funkce ve většině případů je stejná jako okruhů, které využívají poskytovatele služeb ExpressRoute pro provoz. Pro podporu dalších členitosti a nové funkce prostřednictvím ExpressRoute přímo, jsou některé klíčové funkce, které existují v s přímým přístupem okruhy ExpressRoute.

## <a name="circuit-skus"></a>SKU okruhu

Přímé ExpressRoute podporuje scénáře ingestování velké objemy dat do služby Azure storage a další velké objemy dat služby. ExpressRoute okruhy na přímé ExpressRoute teď také podporu **40 GB/s** a **100 GB/s** okruh skladové položky. Páry fyzického portu jsou **100 GB/s** pouze a může mít několik virtuálních okruhů s šířek pásma 5 GB/s, 10 GB/s, 40 GB/s, 100 GB/s – až k 200 GB/s v libovolné kombinaci. 

## <a name="vlan-tagging"></a>Označování příznaky VLAN

Přímé ExpressRoute podporuje QinQ a Dot1Q označování příznaky VLAN.

* **Označování příznaky VLAN QinQ** umožňuje izolované domény směrování na základě okruhu ExpressRoute. Azure dynamicky přiděluje značku S při vytvoření okruhu a nedá se změnit. Každý partnerský vztah v okruhu (privátní a Microsoft) budou využívat jedinečné C – značka jako síť VLAN. C – značka nemusí být jedinečný v rámci okruhy na portech přímo ExpressRoute.

* **Označování příznaky VLAN Dot1Q** umožňuje jeden označené sítě VLAN na jednotlivé dvojice portů přímo ExpressRoute. Značku jazyka C použít u partnerského vztahu musí být jedinečný ve všech okruhů a partnerské vztahy na portu dvojici přímo ExpressRoute.

## <a name="workflow"></a>Pracovní postup

[![workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

Přímé ExpressRoute poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivní/aktivní redundantní připojení globální síti Microsoftu. Infrastruktura ExpressRoute je redundantní a je redundantní a rozmanitým připojení globální síti Microsoftu a odpovídajícím způsobem se škáluje s požadavky zákazníků. 

## <a name="next-steps"></a>Další postup

[Konfigurace ExpressRoute přímo](expressroute-howto-erdirect.md)
