---
title: Informace o Azure ExpressRoute Direct
description: Tato stránka poskytuje přehled ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083605"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

Přímé ExpressRoute poskytuje možnost připojení přímo do globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. ExpressRoute Direct poskytuje připojení Dual 100 GB/s nebo 10 GB/s, které podporuje škálování aktivní/aktivní.

Klíčové funkce, které poskytuje ExpressRoute Direct patří, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzické izolace pro obory, které upraví a vyžadují vyhrazená a izolované připojení jako: bankovnictví, Government a maloobchodního prodeje
* Podrobné řízení distribuce okruhů podle organizační jednotky

## <a name="onboard-to-expressroute-direct"></a>Připojit k ExpressRoute Direct

Než začnete používat ExpressRoute Direct, musíte nejdřív zaregistrovat své předplatné. Při registraci odeslat e-mailu <ExpressRouteDirect@microsoft.com> svým ID předplatného, včetně následující podrobnosti:

* Scénáře, které pokud chcete provést s **přímo ExpressRoute**
* Naleznete v tématu předvolby umístění - [partnery a umístění partnerského vztahu](expressroute-locations-providers.md) úplný seznam všech umístění
* Časová osa implementace
* Další otázky

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute přes poskytovatele služeb a přímé ExpressRoute

| **ExpressRoute přes poskytovatele služeb** | **Přímé ExpressRoute** | 
| --- | --- |
| Využívá poskytovatelů služeb a umožňuje rychlé zprovoznění a možností připojení do stávající infrastruktury | Vyžaduje infrastrukturu s 100 GB/s/10 GB/s a úplnou správu všech vrstev.
| Se integruje s stovky poskytovatelů, včetně Ethernet a MPLS | Přímé/Dedicated kapacitu pro regulované odvětví a masivní ingestování |
| Skladová jednotka okruhů z 50 MB/s 10 GB/s | Zákazník může vybrat kombinaci následujících SKU okruhů v 100 GB/s ExpressRoute Direct: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> Zákazník může vybrat kombinaci následujících SKU okruhu na 10 GB/s ExpressRoute Direct:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta s více obchodními jednotkami a několika pracovními prostředími

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute přímo

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Microsoft Azure a Office 365.

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a může přistupovat k libovolné oblasti v geopolitické oblasti, ve výchozím nastavení a přístup všech globálních oblastí okruhu premium.  

Funkce ve většině případů je stejná jako okruhů, které využívají poskytovatele služeb ExpressRoute pro provoz. Pro podporu dalších členitosti a nové funkce prostřednictvím ExpressRoute přímo, jsou některé klíčové funkce, které existují v s přímým přístupem okruhy ExpressRoute.

## <a name="circuit-skus"></a>SKU okruhu

Přímé ExpressRoute podporuje scénáře ingestování velké objemy dat do služby Azure storage a další velké objemy dat služby. Okruhy ExpressRoute na 100 GB/s ExpressRoute Direct teď také podporují SKU **40 GB/** s a **100 GB/** s. Páry fyzických portů jsou jenom **100 nebo 10 GB/** s a můžou mít několik virtuálních okruhů. Velikosti okruhů:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Předplacená šířka pásma**: 200 GB/s | **Přihlášená šířka pásma**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 GB/s</li><li>100 GB/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Technické požadavky

* Rozhraní MSEE (Enterprise Edge router) společnosti Microsoft:
    * Dual 10 nebo 100 portů gigabitového Ethernetu v páru směrovačů
    * Připojení k LR vláknu s jedním režimem
    * IPv4 a IPv6
    * IP jednotka MTU 1500 bajtů

* Připojení vrstvy 2 nebo směrovač na úrovni 2/vrstvy 3:
    * Musí podporovat 1 802.1 Q (Dot1Q) nebo dvě značky 802.1 Q (QinQ) – zapouzdření značky.
    * EtherType = 0x8100
    * Je nutné přidat vnější značku VLAN (STAG) založenou na ID sítě VLAN, které je určeno pro Microsoft, *pouze v QinQ* .
    * Musí podporovat víc relací protokolu BGP (VLAN) na port a zařízení.
    * Připojení IPv4 a IPv6. *Pro protokol IPv6 se nevytvoří žádné další dílčí rozhraní. Adresa IPv6 bude přidána do stávajícího podřízeného rozhraní*. 
    * Volitelné: podpora [detekce obousměrného předávání (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , která je nakonfigurovaná ve výchozím nastavení u všech privátních partnerských vztahů v okruhech ExpressRoute.

## <a name="vlan-tagging"></a>Označování příznaky VLAN

Přímé ExpressRoute podporuje QinQ a Dot1Q označování příznaky VLAN.

* **Označování příznaky VLAN QinQ** umožňuje izolované domény směrování na základě okruhu ExpressRoute. Azure dynamicky přiděluje značku S při vytvoření okruhu a nedá se změnit. Každý partnerský vztah v okruhu (privátní a Microsoft) budou využívat jedinečné C – značka jako síť VLAN. C – značka nemusí být jedinečný v rámci okruhy na portech přímo ExpressRoute.

* **Označování příznaky VLAN Dot1Q** umožňuje jeden označené sítě VLAN na jednotlivé dvojice portů přímo ExpressRoute. Značku jazyka C použít u partnerského vztahu musí být jedinečný ve všech okruhů a partnerské vztahy na portu dvojici přímo ExpressRoute.

## <a name="workflow"></a>Pracovní postup

[pracovní postup ![](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Smlouva SLA

Přímé ExpressRoute poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivní/aktivní redundantní připojení globální síti Microsoftu. Infrastruktura ExpressRoute je redundantní a je redundantní a rozmanitým připojení globální síti Microsoftu a odpovídajícím způsobem se škáluje s požadavky zákazníků. 

## <a name="next-steps"></a>Další kroky

[Konfigurace ExpressRoute přímo](expressroute-howto-erdirect.md)
