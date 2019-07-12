---
title: Informace o ExpressRoute přímo – Azure | Dokumentace Microsoftu
description: Tato stránka obsahuje přehled služby ExpressRoute přímo
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807484"
---
# <a name="about-expressroute-direct"></a>O službě ExpressRoute Direct

Přímé ExpressRoute poskytuje možnost připojení přímo do globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. Přímé ExpressRoute poskytuje duální 100 GB/s nebo 10 GB/s připojením, která podporuje připojení k aktivní/aktivní ve velkém měřítku.

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
| Využívá poskytovatelů služeb a umožňuje rychlé zprovoznění a možností připojení do stávající infrastruktury | Vyžaduje 100 GB/s a 10 GB/s infrastrukturou a úplnou správu všech vrstev
| Se integruje s stovky poskytovatelů, včetně Ethernet a MPLS | Přímé/Dedicated kapacitu pro regulované odvětví a masivní ingestování |
| Skladová jednotka okruhů z 50 MB/s 10 GB/s | Zákazník si může přímo vybrat kombinaci následujících okruh skladové položky na 100 GB/s ExpressRoute: <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gbps</li><li>100 Gbps</li></ul> Zákazník může vybrat kombinaci následující okruhu skladové položky na ExpressRoute 10 GB/s přímým přístupem:<ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>
| Optimalizováno pro jednoho tenanta | Optimalizováno pro jednoho tenanta/Cloud Service providers nebo více organizačních jednotek

## <a name="expressroute-direct-circuits"></a>Okruhy ExpressRoute přímo

Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365.  

Každé umístění partnerského vztahu má přístup ke globální síti Microsoftu a může přistupovat k libovolné oblasti v geopolitické oblasti, ve výchozím nastavení a přístup všech globálních oblastí okruhu premium.  

Funkce ve většině případů je stejná jako okruhů, které využívají poskytovatele služeb ExpressRoute pro provoz. Pro podporu dalších členitosti a nové funkce prostřednictvím ExpressRoute přímo, jsou některé klíčové funkce, které existují v s přímým přístupem okruhy ExpressRoute.

## <a name="circuit-skus"></a>SKU okruhu

Přímé ExpressRoute podporuje scénáře ingestování velké objemy dat do služby Azure storage a další velké objemy dat služby. Okruhy ExpressRoute na 100 GB/s ExpressRoute přímé teď také podporují **40 GB/s** a **100 GB/s** okruh skladové položky. Páry fyzického portu jsou **100 nebo 10 GB/s** pouze a může mít několik virtuálních okruhů. Okruh velikosti:

| **Přímé ExpressRoute 100 GB/s** | **Přímé ExpressRoute 10 GB/s** | 
| --- | --- |
| **Předplacené šířky pásma**: 200 Gbps | **Předplacené šířky pásma**: 20 Gbps |
| <ul><li>5 Gb/s</li><li>10 Gb/s</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gb/s</li><li>2 Gb/s</li><li>5 Gb/s</li><li>10 Gb/s</li></ul>

## <a name="technical-requirements"></a>Technické požadavky

* Rozhraní směrovač (MSEE) Microsoft Enterprise Edge:
    * Duální porty s adaptéry Gigabit Ethernet 10 nebo 100 pouze mezi směrovači pár
    * Připojení s jednotným režimu Vlákének LR
    * Protokoly IPv4 a IPv6
    * IP MTU 1500 bajtů

* Připojení vrstvy 2 nebo vrstvy 3 přepínač nebo směrovač:
    * Musí podporovat značky 1 802.1Q (Dot1Q) nebo dvě značky 802.1Q (QinQ) označit zapouzdření
    * Kód Ethertype = 0x8100
    * Musíte přidat vnější značky sítě VLAN (STAG) na základě ID sítě VLAN určené Microsoft - *vztahuje pouze na QinQ*
    * Musí podporovat několik relací protokolu BGP (VLAN) na portu a zařízení
    * Připojení k protokolu IPv4 a IPv6. *Pro protokol IPv6 se nevytvoří žádné další dílčí rozhraní. IPv6 adres se přidají do existující dílčí rozhraní*. 
    * Volitelné: [Obousměrné předávání detekce (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) podpory, který je nakonfigurován ve výchozím nastavení všechny privátní partnerské vztahy okruhy ExpressRoute

## <a name="vlan-tagging"></a>Označování příznaky VLAN

Přímé ExpressRoute podporuje QinQ a Dot1Q označování příznaky VLAN.

* **Označování příznaky VLAN QinQ** umožňuje izolované domény směrování na základě okruhu ExpressRoute. Azure dynamicky přiděluje značku S při vytvoření okruhu a nedá se změnit. Každý partnerský vztah v okruhu (privátní a Microsoft) budou využívat jedinečné C – značka jako síť VLAN. C – značka nemusí být jedinečný v rámci okruhy na portech přímo ExpressRoute.

* **Označování příznaky VLAN Dot1Q** umožňuje jeden označené sítě VLAN na jednotlivé dvojice portů přímo ExpressRoute. Značku jazyka C použít u partnerského vztahu musí být jedinečný ve všech okruhů a partnerské vztahy na portu dvojici přímo ExpressRoute.

## <a name="workflow"></a>Pracovní postup

[![workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

Přímé ExpressRoute poskytuje stejnou smlouvu SLA na podnikové úrovni s aktivní/aktivní redundantní připojení globální síti Microsoftu. Infrastruktura ExpressRoute je redundantní a je redundantní a rozmanitým připojení globální síti Microsoftu a odpovídajícím způsobem se škáluje s požadavky zákazníků. 

## <a name="next-steps"></a>Další kroky

[Konfigurace ExpressRoute přímo](expressroute-howto-erdirect.md)
