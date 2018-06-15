---
title: Přenosy dat zobrazení v Azure Traffic Manageru | Microsoft Docs
description: Úvod do zobrazení provozu Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 7ce51017fdee92e5589c06b398c9650930d5436d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179833"
---
# <a name="traffic-manager-traffic-view"></a>Zobrazení provozu Traffic Manageru

Traffic Manager poskytuje že s DNS úrovni směrování, aby vaši koncoví uživatelé jsou směrované na v pořádku koncové body založené na metodě směrování zadali při vytváření profilu. Zobrazení provozu Traffic Manager k dispozici zobrazení vaší uživatelské základny (na úrovni členitosti Překladač DNS) a jejich provoz vzor. Když povolíte provoz zobrazení, tyto informace zpracování poskytnout prakticky využitelné informace. 

Pomocí zobrazení provoz, můžete:
- pochopení, kde se nachází (až místní DNS překladač úrovně rozlišením) uživatelské základny.
- Zobrazit objem provozu (zjištěnými jako dotazy DNS zpracovává Azure Traffic Managerem) pocházející z těchto oblastí.
- získáte přehled o co je reprezentativní latence, které tyto uživatele.
- Podrobné informace do vzory konkrétní přenosy ze všech těchto uživatelské základny k oblastem Azure, kde máte koncové body. 

Například můžete zobrazit provoz zjistit, které oblasti máte velký počet přenosů dat, ale trpí vyšší latence. Dále můžete tyto informace k plánování rozšíření vaší nároky na nové oblastí Azure tak, aby tito uživatelé může mít nižší latenci prostředí.

## <a name="how-traffic-view-works"></a>Jak funguje provoz zobrazení

Zobrazení provoz funguje tak, že Traffic Manager. podívejte se na příchozí dotazů přijatých za posledních sedmi dnech proti profil, který má povolení této funkce. Příchozí informace dotazy extrahuje zobrazení provozu zdrojovou IP Překladač DNS, který slouží jako reprezentace umístění uživatele. Tyto jsou pak seskupeny dohromady na úrovni rozlišením Překladač DNS vytvořit základní oblasti uživatele pomocí zeměpisné údaje udržované Traffic Managerem IP adres. Správce provozu se poté hledat v oblastech Azure, ke kterým dotaz byl směrován a vytvoří mapu tok provozu pro uživatele z těchto oblastí.  
V dalším kroku Traffic Manager korelaci oblasti základní pro mapování oblast Azure s tabulkami intelligence latence sítě, které udržuje pro koncového uživatele různé sítě zjistit, které uživatelé z těchto oblastí Průměrná latence při připojování k oblastem Azure. Tyto výpočty se pak zkombinují v úrovni místní DNS překladač IP předtím, než se zobrazí na vás. Můžete využívat informace různými způsoby.

>[!NOTE]
>Latence popsané v zobrazení provoz je reprezentativní latenci mezi koncové uživatele a oblastech Azure, ke kterým byl připojený k a není latence vyhledávání DNS. Díky zobrazení provoz vrátil nejlepší odhad úsilí latenci mezi místní překladač DNS a oblast Azure, které dotaz byl směrován, pokud je k dispozici dostatek dat pak latence, bude mít hodnotu null. 

## <a name="visual-overview"></a>Přehled Visual

Když přejdete **provoz zobrazení** části na stránce Traffic Manager se zobrazí s zeměpisné mapě s překrytí Statistika provozu zobrazení. Mapy poskytuje informace o základní uživatele a koncové body pro váš profil Traffic Manageru.

### <a name="user-base-information"></a>Základní informace o uživateli

Pro tyto místní překladače služby DNS pro umístění, které informace jsou k dispozici jsou uvedené v mapě. Barva Překladač DNS označuje Průměrná latence zkušeného koncoví uživatelé, kteří používají tento překladač služby DNS pro jejich dotazy Traffic Manager.

Pokud je ukazatel myši nad umístění Překladač DNS v mapě, se zobrazí:
- Překladač DNS na IP adresu
- objem provozu dotazu DNS z jeho pohledu Traffic Managerem
- koncových bodů, do které provoz z DNS byla překladač směrovaných, jako řádek mezi koncový bod a překladač služby DNS 
- Průměrná latence z tohoto umístění ke koncovému bodu, vyjádřené barvu čáry jejich připojením

### <a name="endpoint-information"></a>Informace o koncovém

Oblastí Azure, ve kterých jsou umístěny koncové body se zobrazí jako modré tečky v mapě. Pokud váš koncový bod je externí a nemá k němu mapována oblasti Azure, se zobrazí v horní části mapy. Klikněte na libovolný koncový bod zobrazíte různých umístěních (podle Překladač DNS používá) z kde byl přenášená do tohoto koncového bodu. Připojení se zobrazují jako řádek mezi koncový bod a umístění Překladač DNS a podle reprezentativní latenci mezi tuto dvojici, se zobrazí. Kromě toho můžete zobrazit název koncového bodu, oblasti Azure, ve kterém je spuštěná a celkový objem požadavků, které byly směrované na ni tímto profilem Traffic Manager.


## <a name="tabular-listing-and-raw-data-download"></a>Tabulkový výčet a stahování nezpracovaná data

Data zobrazení provozu můžete zobrazit v tabulkovém formátu na portálu Azure. Existuje položka pro každou IP adresu Překladač DNS / spárujte koncový bod, který ukazuje na IP adresu Překladač DNS, názvu a zeměpisné umístění oblast Azure z které koncový bod se nachází (Pokud je k dispozici), objem požadavků, které jsou přidružené k této překladač služby DNS k tohoto koncového bodu a reprezentativní zpoždění spojené s koncovým uživatelům pomocí této služby DNS (je-li k dispozici). Data zobrazení provozu můžete také stáhnout jako soubor CSV, který slouží jako součást pracovního postupu analytics podle svého výběru.

## <a name="billing"></a>Fakturace

Pokud používáte zobrazení provoz, vám fakturují, na základě počtu datových bodů použít k vytvoření statistiky uvedené. V současné době je bod pouze datový typ používaný dotazů přijatých pro svůj profil Traffic Manageru. Další podrobnosti o cenách najdete na webu [Traffic Manager stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Další postup

- Další informace [fungování Traffic Manager](traffic-manager-overview.md)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

