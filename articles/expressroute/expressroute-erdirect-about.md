---
title: Informace o Azure ExpressRoute Direct
description: Seznamte se s klíčovými funkcemi Azure ExpressRoute Direct a informací, které jsou potřeba k tomu, aby ExpressRoute přímé, jako jsou dostupné SKU a technické požadavky.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: duau
ms.openlocfilehash: 0cc07f9647c4f7836438adbe2b4cf9fa24a1c890
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566394"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

ExpressRoute Direct nabízí možnost připojit se přímo k globální síti Microsoftu při partnerských umístěních, která jsou strategicky distribuována po celém světě. ExpressRoute Direct poskytuje připojení Dual 100 GB/s nebo 10 GB/s, které podporuje škálování aktivní/aktivní.

K dispozici jsou klíčové funkce, které ExpressRoute Direct poskytuje, ale nejsou omezené na:

* Masivní příjem dat do služeb jako Storage a Cosmos DB
* Fyzická izolace pro obory, které jsou regulováné a vyžadují vyhrazené a izolované připojení, jako je například bankovnictví, státní správa a maloobchodní prodej.
* Podrobné řízení distribuce okruhů podle organizační jednotky

## <a name="onboard-to-expressroute-direct"></a>Připojit k ExpressRoute Direct

Než začnete používat ExpressRoute Direct, musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, pošlete E-mail na adresu <ExpressRouteDirect@microsoft.com> s vaším ID předplatného, včetně následujících podrobností:

* Scénáře, které se chystáte provést s **přímým přístupem ExpressRoute**
* Předvolby umístění – v části [partneři a umístění partnerských vztahů](expressroute-locations-providers.md) najdete úplný seznam všech umístění.
* Časová osa implementace
* Jakékoli další dotazy

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute pomocí poskytovatele služeb a ExpressRoute Direct

| **ExpressRoute pomocí poskytovatele služeb** | **ExpressRoute Direct** | 
| --- | --- |
| Využívá poskytovatele služeb k umožnění rychlé registrace a připojení do stávající infrastruktury. | Vyžaduje infrastrukturu s 100 GB/s/10 GB/s a úplnou správu všech vrstev.
| Se integruje se stovkami poskytovatelů, včetně ethernetových a MPLS | Přímá a vyhrazená kapacita pro regulované odvětví a obrovské přijímání dat |
| SKU okruhů od 50 do 10 GB/s | Zákazník může vybrat kombinaci následujících SKU okruhů v 100 GB/s ExpressRoute Direct: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> Zákazník může vybrat kombinaci následujících SKU okruhu na 10 GB/s ExpressRoute Direct:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta s více obchodními jednotkami a několika pracovními prostředími

## <a name="expressroute-direct-circuits"></a>Přímé okruhy ExpressRoute

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako jsou Microsoft Azure a Microsoft 365.

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a má ve výchozím nastavení přístup k libovolné oblasti v geopolitické zóně a má přístup ke všem globálním oblastem okruhu Premium.  

Funkce ve většině scénářů je rovnocenná okruhům, které využívají poskytovatele služby ExpressRoute k provozu. Aby bylo možné podporovat další členitost a nové funkce, které jsou nabízeny pomocí ExpressRoute Direct, existují některé klíčové funkce, které existují na ExpressRoute přímých Okruhech.

## <a name="circuit-skus"></a>SKU okruhů

ExpressRoute Direct podporuje rozsáhlé scénáře přijímání dat do služby Azure Storage a dalších služeb pro velké objemy dat. Okruhy ExpressRoute na 100 GB/s ExpressRoute Direct teď také podporují SKU **40 GB/** s a **100 GB/** s. Páry fyzických portů jsou jenom **100 nebo 10 GB/** s a můžou mít několik virtuálních okruhů. Velikosti okruhů:

| **100 GB/s ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Předplacená šířka pásma**: 200 GB/s | **Přihlášená šířka pásma**: 20 GB/s |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gb/s</li><li>100 Gb/s</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

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

## <a name="vlan-tagging"></a>Označování značek VLAN

ExpressRoute Direct podporuje označování značek VLAN QinQ a Dot1Q.

* **Označování QINQ VLAN** umožňuje doménám izolované směrování na jednotlivých okruhech ExpressRoute. Azure při vytváření okruhů dynamicky přiděluje značku S-a nedá se změnit. Každý partnerský vztah na okruhu (Private a Microsoft) bude používat jedinečnou značku C jako síť VLAN. Označení C-není nutné, aby bylo v okruhech na ExpressRoute přímých portech jedinečné.

* **Označení sítě VLAN Dot1Q** umožňuje jednu OZNAČENOU síť VLAN pro každou dvojici přímých portů ExpressRoute. Označení jazyka C používaného pro partnerský vztah musí být jedinečné ve všech okruhech a partnerských vztahů na páru portů ExpressRoute Direct.

## <a name="workflow"></a>Pracovní postup

[![pracovního postupu](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivními nebo aktivními redundantními připojeními do globální sítě Microsoft. Infrastruktura ExpressRoute je redundantní a připojení k globální síti Microsoft je redundantní a různé a škáluje se podle požadavků zákazníků. 

## <a name="next-steps"></a>Další kroky

[Konfigurace ExpressRoute Direct](expressroute-howto-erdirect.md)
