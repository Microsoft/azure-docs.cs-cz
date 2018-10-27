---
title: Zobrazení v Azure Traffic Manageru přenosů | Dokumentace Microsoftu
description: Úvod do zobrazení provozu Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: d1800fea2212628e7647b5250efa33ebb97957f9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138068"
---
# <a name="traffic-manager-traffic-view"></a>Zobrazení přenosů Traffic Manageru

Traffic Manager poskytuje, kterým se směrováním na úrovni DNS tak, aby vaši koncoví uživatelé jsou směrované na založené na metodě směrování v dobrém stavu koncových bodů při vytváření profilu. Zobrazení přenosů poskytne přehled vaší uživatelské základny (na úrovni členitosti překladače DNS) a způsobu jejich provoz Traffic Manageru. Když povolíte Traffic View, tyto informace jsou zpracovávána poskytnout užitečné přehledy. 

Pomocí zobrazení přenosů, můžete:
- Zjistěte, kde se nachází (až místní úrovni překladače DNS) uživatelské základny.
- Zobrazit objem přenosů (zjištěnými jako dotazy DNS Azure Traffic Manager zpracovává) pocházející z těchto oblastí.
- Získejte přehled o novinkách reprezentativní latenci tito uživatelé.
- Podrobné informace o vzorcích konkrétních přenosů z každé z těchto uživatelských základen do oblastí Azure, kde jsou koncové body. 

Například můžete použít Traffic View pochopit, jaké oblasti mají velký počet přenosů, ale mají vyšší latencí. V dalším kroku můžete použít tyto informace k plánování vaše nároky na místo rozšiřující do nových oblastí Azure tak, aby tito uživatelé mají nižší latenci prostředí.

## <a name="how-traffic-view-works"></a>Jak funguje Traffic View

Zobrazení přenosů funguje tak, že Traffic Manageru, podívejte se na příchozí dotazy přijaté za posledních sedm dnů vůči profilu, který má tato funkce povolena. Zobrazení přenosů z příchozí informace dotazy extrahuje zdrojovou IP adresu Překladač DNS, který slouží jako reprezentace umístění uživatele. Tyto jsou potom seskupeny na do úrovně překladače DNS k vytvoření základních oblastech uživatele pomocí zeměpisné údaje udržované Traffic Managerem IP adresy. Traffic Manager poté hledá v oblasti Azure, ke kterým se dotaz, byla směrována a vytvoří mapování toku provozu pro uživatele z těchto oblastí.  
V dalším kroku, Traffic Manager koreluje oblasti základní mapování oblast Azure s tabulkami intelligence latence sítě, které udržuje pro různé koncové uživatele sítě pochopit průměrnou latenci uživatelé z těchto oblastí po Probíhá připojování k oblasti Azure. Všechny tyto výpočty se následně se spojí dohromady v na místní DNS překládání IP úrovni předtím, než se budou zobrazovat uživateli. Informace v různých způsobů, jak můžete využívat.

>[!NOTE]
>Latence je popsáno v zobrazení přenosů reprezentativní latence mezi koncový uživatel a oblastí Azure, ke kterým má připojené k a není latence vyhledávání DNS. Díky zobrazení přenosů vrátí nejlepší odhad úsilí latence mezi místního překladače DNS a oblasti Azure, které dotaz, byla směrována do, pokud není k dispozici dostatek dat a pak latence bude mít hodnotu null. 

## <a name="visual-overview"></a>Vizuální přehled

Když přejdete **Traffic View** části na stránce Traffic Manageru, se zobrazí zeměpisné mapě s překrytí insights Traffic View. Mapa obsahuje informace o uživatelské základně a koncové body pro váš profil Traffic Manageru.

### <a name="user-base-information"></a>Základní informace o uživateli

Pro tyto místní DNS jsou překladače pro umístění, které informace jsou k dispozici zobrazí se v objektu map. Barva přeložených označuje průměrnou dobu vyřízení zkušení koncoví uživatelé, kteří používají tento překladač služby DNS pro jejich dotazů Traffic Manageru.

Pokud najedete myší umístění Překladač DNS na mapě, zobrazí:
- IP adresa Překladač DNS
- Jaký objem přenosů dotazu DNS nějakého Traffic Managerem
- Koncové body, jaký provoz DNS byla směrována překladač jako čáry mezi koncový bod a překladač služby DNS 
- Průměrná latence z tohoto umístění ke koncovému bodu reprezentovaná jako barva čáry jejich propojení

### <a name="endpoint-information"></a>Informace o koncovém bodu

Oblasti Azure, ve kterých jsou umístěny koncové body se zobrazí jako modré tečky v objektu map. Pokud váš koncový bod je externí a nemá určitá oblast Azure k němu mapována, zobrazí se v horní části mapy. Klepněte na žádný koncový bod chcete zobrazit jiné umístění (podle Překladač DNS použít) z směrován provoz do tohoto koncového bodu. Připojení se zobrazí jako čáry mezi koncový bod a umístění Překladač DNS a jsou barvy podle reprezentativní latence mezi tohoto páru. Kromě toho můžete zobrazit název koncového bodu, oblast Azure, ve kterém běží a celkový objem požadavků, které byli přesměrováni do něj tento profil Traffic Manageru.


## <a name="tabular-listing-and-raw-data-download"></a>Tabulkovém výpisu a stáhnout nezpracovaná data

Zobrazí se zobrazení přenosů dat v tabelárním formátu na webu Azure portal. Existuje položka pro každou IP adresu Překladač DNS / spárovat koncový bod, který zobrazuje IP adresu Překladač DNS, název a zeměpisnou polohu oblasti Azure v rámci které koncový bod se nachází (Pokud je k dispozici), objem požadavků, které jsou přidružené k tento překladač služby DNS pro Tento koncový bod a reprezentativní zpoždění spojené s koncovými uživateli pomocí této služby DNS (Pokud je k dispozici). Zobrazení přenosů dat můžete také stáhnout jako soubor CSV, který slouží jako součást pracovního postupu analytics podle vašeho výběru.

## <a name="billing"></a>Fakturace

Při použití zobrazení přenosů se účtují, na základě počtu datových bodů použitých k vytvoření insights zobrazí. Typ bodu jenom data použít v současné době je dotazů přijatých pro váš profil Traffic Manageru. Podrobné informace o cenách najdete [Traffic Manageru stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Další postup

- Přečtěte si [jak funguje Traffic Manager](traffic-manager-overview.md)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md)

