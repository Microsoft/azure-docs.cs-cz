---
title: Zobrazení přenosů v Azure Traffic Manager
description: V tomto úvodu se dozvíte, jak funguje zobrazení provozu Traffic Manageru.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: b20357413c62460aba55a2d354b90995a2aa4815
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183689"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Zobrazení přenosů

Traffic Manager poskytuje směrování na úrovni DNS, takže koncoví uživatelé budou přesměrováni na koncové body v závislosti na metodě směrování zadané při vytváření profilu. Zobrazení přenosů poskytuje Traffic Manager se zobrazením vašich uživatelských základů (na úrovni členitosti překladače DNS) a podle jejich vzoru přenosů. Když povolíte Zobrazení přenosů, zpracují se tyto informace, které vám poskytnou užitečné poznatky. 

Pomocí Zobrazení přenosů můžete:
- Pochopte, kde se nachází vaše uživatelská základna (až do úrovně místního překladače DNS).
- Zobrazení objemu provozu (zaznamenaného jako dotazy DNS zpracovávané službou Azure Traffic Manager) pocházející z těchto oblastí.
- Získejte přehled o tom, k čemu mají tito uživatelé zástupnou latenci.
- podrobně se do specifických vzorů provozu od každého z těchto uživatelů do oblastí Azure, kde máte koncové body. 

Můžete například použít Zobrazení přenosů k pochopení, které oblasti mají velký počet přenosů, ale trpí vyšší latencí. Dále můžete využít tyto informace k plánování rozšíření vašich nároků na nové oblasti Azure, aby tito uživatelé mohli mít nižší možnosti latence.

## <a name="how-traffic-view-works"></a>Jak Zobrazení přenosů funguje

Zobrazení přenosů funguje tak, že se Traffic Manager podívat na příchozí dotazy přijaté během posledních sedmi dnů na profil s povolenou funkcí. Z informací o příchozích dotazech Zobrazení přenosů extrahuje zdrojovou IP adresu překladače DNS, který se používá jako reprezentace umístění uživatelů. Ty se pak seskupují společně s podrobnostmi o úrovni překladače DNS k vytvoření oblastí s uživatelskými základem pomocí geografických informací o IP adresách, které spravuje Traffic Manager. Traffic Manager potom Prohlédněte oblasti Azure, ve kterých byl dotaz směrován, a vytvoří mapu toku provozu pro uživatele z těchto oblastí.  
V dalším kroku Traffic Manager koreluje základní oblast uživatele s mapováním oblastí Azure s tabulkami latence v síťové službě, kterou udržuje pro různé sítě koncových uživatelů, aby při připojování k oblastem Azure pochopila průměrnou latenci, kterou uživatelé z těchto oblastí zaznamenali. Všechny tyto výpočty se pak spojí na úrovni IP adresy pro místní Překladač DNS, než se zobrazí. Tyto informace můžete využívat různými způsoby.

Frekvence aktualizace dat zobrazení provozu závisí na několika interních proměnných služby. Data se ale většinou aktualizují každých 24 hodin.

>[!NOTE]
>Latence popsaná v Zobrazení přenosů je reprezentativní latencí mezi koncovým uživatelem a oblastmi Azure, ke kterým se připojili, a nejedná se o latenci vyhledávání DNS. Zobrazení přenosů se dokončí odhad latence mezi místním překladačem DNS a oblastí Azure, do které byl dotaz směrován, pokud není k dispozici dostatek dat, vrácená latence bude null. 

## <a name="visual-overview"></a>Vizuální přehled

Když přejdete na **zobrazení přenosů** část stránky Traffic Manager, zobrazí se geografická mapa s překrytím zobrazení přenosů Insights. Mapa poskytuje informace o uživatelské základu a koncových bodech pro váš profil Traffic Manager.

![Geografické zobrazení Traffic Manager Zobrazení přenosů][1]

### <a name="user-base-information"></a>Základní informace o uživateli

U místních překladačů DNS, pro které jsou dostupné informace o poloze, se zobrazí v mapě. Barva překladače DNS označuje průměrnou latenci, kterou zkušení koncoví uživatelé, kteří použili tento překladač DNS pro své Traffic Manager dotazy.

Pokud najedete myší na umístění překladače DNS na mapě, zobrazí se tyto informace:
- IP adresa překladače DNS
- objem provozu dotazů DNS, který Traffic Manager z něho uvidí
- koncové body, na které byl směrován provoz z překladače DNS jako čára mezi koncovým bodem a překladačem DNS 
- Průměrná latence z tohoto umístění na koncový bod reprezentovaná jako Barva čáry, která je propojuje

### <a name="endpoint-information"></a>Informace o koncovém bodu

Oblasti Azure, ve kterých se nachází koncové body, se zobrazují na mapě jako modré tečky. Pokud je váš koncový bod externí a nemá na něj namapovaný region Azure, zobrazí se v horní části mapy. Kliknutím na libovolný koncový bod zobrazíte různá umístění (na základě používaného překladače DNS), ze kterého byl provoz směrován do tohoto koncového bodu. Připojení se zobrazují jako čára mezi koncovým bodem a umístěním překladače DNS a jsou barevná na základě reprezentativnosti mezi touto dvojicí. Kromě toho můžete zobrazit název koncového bodu, oblast Azure, ve které se spouští, a celkový objem požadavků, na které byly směrovány tímto Traffic Manager profilem.


## <a name="tabular-listing-and-raw-data-download"></a>Stažení tabulkového zobrazení a nezpracovaná data

Data Zobrazení přenosů můžete zobrazit v tabulkovém formátu v Azure Portal. Existuje záznam pro každý pár IP/koncový bod překladače DNS, který zobrazuje IP adresu překladače DNS, název a zeměpisnou polohu oblasti Azure, ve které se nachází koncový bod (Pokud je k dispozici), objem požadavků přidružených k tomuto koncovému bodu DNS a zástupce pro koncové uživatele, který používá tuto službu DNS (tam, kde je k dispozici). Data Zobrazení přenosů můžete také stáhnout jako soubor CSV, který se dá použít jako součást pracovního postupu analýzy podle vašeho výběru.

## <a name="billing"></a>Fakturace

Při použití Zobrazení přenosů se fakturuje na základě počtu datových bodů použitých k vytvoření přehledů, které jsou k dispozici. V současné době je použit jediný typ datového bodu dotazy přijaté proti profilu Traffic Manager. Další podrobnosti o cenách najdete na [stránce s cenami Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Nejčastější dotazy

* [Co Zobrazení přenosů udělat?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Jak můžu využít Zobrazení přenosů?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Jak se Zobrazení přenosů liší od metrik Traffic Manager dostupných prostřednictvím služby Azure monitor?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Používá Zobrazení přenosů informace o podsíti klienta EDNS?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Kolik dní dat Zobrazení přenosů použít?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Jak Zobrazení přenosů zpracovávat externí koncové body?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Potřebuji povolit Zobrazení přenosů pro každý profil v předplatném?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Jak můžu Zobrazení přenosů vypnout?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Jak funguje Zobrazení přenosů fakturace?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Další kroky

- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png