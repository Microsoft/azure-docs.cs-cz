---
title: O Azure ExpressRoute Direct
description: Tato stránka poskytuje přehled expressroute direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083605"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

ExpressRoute Direct vám dává možnost připojit se přímo do globální sítě microsoftu v partnerských lokalitách strategicky rozmístěných po celém světě. ExpressRoute Direct poskytuje dvě 100 Gb/s nebo 10 Gb/s připojení, které podporuje aktivní/aktivní připojení ve velkém měřítku.

Mezi klíčové funkce, které ExpressRoute Direct poskytuje, patří mimo jiné:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzická izolace pro průmyslová odvětví, která jsou regulována a vyžadují specializované a izolované připojení, jako jsou bankovnictví, vláda a maloobchod
* Podrobné řízení distribuce okruhů podle organizační jednotky

## <a name="onboard-to-expressroute-direct"></a>Na palubě na ExpressRoute Direct

Před použitím ExpressRoute Direct musíte nejprve zaregistrovat své předplatné. Chcete-li se zaregistrovat, odešlete e-mail <ExpressRouteDirect@microsoft.com> s ID předplatného, včetně následujících podrobností:

* Scénáře, kterých chcete dosáhnout pomocí **služby ExpressRoute Direct**
* Předvolby lokalit – úplný seznam všech lokalit naleznete v části [Partneři a umístění partnerského vztahu](expressroute-locations-providers.md)
* Časová osa implementace
* Jakékoli další otázky

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute pomocí poskytovatele služeb a ExpressRoute Direct

| **ExpressRoute pomocí poskytovatele služeb** | **ExpressRoute Direct** | 
| --- | --- |
| Využívá poskytovatele služeb k rychlému zapisování a připojení do stávající infrastruktury. | Vyžaduje infrastrukturu 100 Gb/10 Gb/s a plnou správu všech vrstev
| Integruje se stovkami poskytovatelů, včetně Ethernet a MPLS | Přímá/vyhrazená kapacita pro regulovaná odvětví a masivní požívání dat |
| Obvody SKU od 50 Mb/s do 10 Gb/s | Zákazník si může vybrat kombinaci následujících okruhových skutových položek na 100 Gb/s ExpressRoute Direct: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Zákazník si může vybrat kombinaci následujících okruhových skutových položek na 10 Gb/s ExpressRoute Direct:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optimalizováno pro jednoho klienta | Optimalizováno pro jednoho tenanta s více organizačními jednotkami a více pracovními prostředími

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute Direct

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako je Microsoft Azure a Office 365.

Každé umístění partnerského vztahu má přístup ke globální síti microsoftu a ve výchozím nastavení má přístup k libovolné oblasti v geopolitické zóně a může přistupovat ke všem globálním oblastem s prémiovým okruhem.  

Funkce ve většině scénářů je ekvivalentní obvody, které využívají poskytovatele služeb ExpressRoute k provozu. Pro podporu další rozlišovací schopnosti a nové funkce nabízené pomocí ExpressRoute Direct, existují určité klíčové funkce, které existují na ExpressRoute Přímé okruhy.

## <a name="circuit-skus"></a>Obvod SKUs

ExpressRoute Direct podporuje scénáře masivního ingestování dat do úložiště Azure a dalších služeb velkých objemů dat. Obvody ExpressRoute na 100 Gb/s ExpressRoute Direct nyní také podporují **40 Gb/s** a **100 Gb/s** okruhových SKU. Dvojice fyzických portů jsou pouze **100 nebo 10 Gb/s** a mohou mít více virtuálních okruhů. Velikosti obvodů:

| **100 Gb/s ExpressRoute Direct** | **10 Gb/s ExpressRoute Direct** | 
| --- | --- |
| **Odebíaná šířka pásma:** 200 Gb/s | **Odebíaná šířka pásma:** 20 Gb/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Technické požadavky

* Rozhraní směrovače MSEE (Microsoft Enterprise Edge) :
    * Dva 10 nebo 100 gigabitové ethernetové porty pouze u dvojice směrovačů
    * Jednorežimové připojení LR Fiber
    * IPv4 a IPv6
    * IP MTU 1500 bajtů

* Připojení vrstvy přepínače/směrovače 2/vrstvy 3:
    * Musí podporovat 1 značku 1 802.1Q (Dot1Q) nebo dvě značky 802.1Q (QinQ) zapouzdření značky
    * Ethertype = 0x8100
    * Musí přidat vnější značku VLAN (STAG) na základě ID Sítě VLAN určené společností Microsoft - *platí pouze pro QinQ*
    * Musí podporovat více relací Protokolu BGP (VLAN) na port a zařízení.
    * Připojení IPv4 a IPv6. *Pro IPv6 nebude vytvořeno žádné další dílčí rozhraní. Adresa IPv6 bude přidána do existujícího dílčího rozhraní*. 
    * Volitelné: Podpora [obousměrné detekce předávání (BFD),](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) která je ve výchozím nastavení nakonfigurována ve všech soukromých partnerských partnerech na obvodech ExpressRoute

## <a name="vlan-tagging"></a>Označování v síti VLAN

ExpressRoute Direct podporuje označování QinQ i Dot1Q VLAN.

* **QinQ VLAN Tagging** umožňuje izolované směrovací domény na základě okruhu ExpressRoute. Azure dynamicky přiděluje s-tag při vytváření okruhu a nelze změnit. Každý partnerský vztah v okruhu (Private a Microsoft) bude využívat jedinečný c-tag jako vsítil. C-Tag nemusí být jedinečný napříč okruhy na portech ExpressRoute Direct.

* **Dot1Q VLAN Tagging** umožňuje jednu označenou síť VLAN na základě dvojice portů ExpressRoute Direct. C-Tag používaný v partnerském vztahu musí být jedinečný ve všech obvodech a partnerských linkách na dvojici portů ExpressRoute Direct.

## <a name="workflow"></a>Pracovní postup

[![pracovní postup](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct poskytuje stejnou síť SLA na podnikové úrovni s aktivními nebo aktivními redundantními připojeními do globální sítě společnosti Microsoft. Infrastruktura ExpressRoute je redundantní a připojení do microsoft global network je redundantní a rozmanité a škáluje podle požadavků zákazníků. 

## <a name="next-steps"></a>Další kroky

[Konfigurace ExpressRoute Direct](expressroute-howto-erdirect.md)
