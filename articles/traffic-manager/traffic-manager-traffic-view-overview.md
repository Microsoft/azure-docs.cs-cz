---
title: Zobrazení provozu ve Správci provozu Azure
description: V tomto úvodu se dozvíte, jak funguje zobrazení Traffic Manager Traffic.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938346"
---
# <a name="traffic-manager-traffic-view"></a>Zobrazení provozu traffic manageru

Traffic Manager poskytuje směrování na úrovni DNS tak, aby koncoví uživatelé byli přesměrováni na v pořádku koncové body na základě metody směrování zadané při vytváření profilu. Zobrazení provozu poskytuje traffic manageru zobrazení uživatelských základen (na úrovni rozlišovací schopnosti dns) a jejich vzor provozu. Pokud povolíte zobrazení provozu, tyto informace se zpracují, aby vám poskytly užitečné přehledy. 

Pomocí zobrazení provozu můžete:
- pochopit, kde jsou umístěny vaše uživatelské základny (až do místní rozlišovací schopnost DNS).
- zobrazení objemu provozu (pozorované jako dotazy DNS zpracovávané službou Azure Traffic Manager) pocházející z těchto oblastí.
- získat přehled o tom, co je reprezentativní latence zkušený těchto uživatelů.
- podrobné prohledit do konkrétní chod vzory z každé z těchto uživatelských základen do oblastí Azure, kde máte koncové body. 

Zobrazení provozu můžete například použít k pochopení, které oblasti mají velký počet přenosů, ale trpí vyšší latencí. Dále můžete tyto informace použít k plánování rozšíření stopy do nových oblastí Azure tak, aby tito uživatelé mohli mít nižší latence zkušenosti.

## <a name="how-traffic-view-works"></a>Jak zobrazení provozu funguje

Zobrazení provozu funguje tak, že traffic manager se podíval na příchozí dotazy přijaté v posledních sedmi dnech proti profilu, který má tuto funkci povolenou. Z informací o příchozích dotazech zobrazení provozu extrahuje zdrojovou IP adresu překladače DNS, který se používá jako reprezentace umístění uživatelů. Ty jsou pak seskupeny na úrovni překladače DNS, aby se vytvořily základní oblasti uživatelů pomocí geografických informací o ADRESÁCH IP spravovaných traffic managerem. Traffic Manager pak prozkoumá oblasti Azure, do kterých byl dotaz směrován, a vytvoří mapu toku provozu pro uživatele z těchto oblastí.  
V dalším kroku Traffic Manager koreluje základní oblast uživatele s mapováním oblasti Azure s tabulkami latence síťové inteligence, které udržuje pro různé sítě koncových uživatelů, aby pochopil průměrnou latenci, kterou uživatelé z těchto oblastí zažívají, když připojení k oblastem Azure. Všechny tyto výpočty jsou pak kombinovány na úrovni IP překladače DNS, než je vám předložen. Informace můžete využívat různými způsoby.

Četnost aktualizace dat zobrazení provozu závisí na více proměnných interní služby. Data jsou však obvykle aktualizována jednou za 24 hodin.

>[!NOTE]
>Latence popsaná v zobrazení provozu je reprezentativní latence mezi koncovým uživatelem a oblastmi Azure, ke kterým se připojili, a není latencí vyhledávání DNS. Zobrazení provozu poskytuje maximální úsilí odhad latence mezi místní překladač DNS a oblast Azure dotaz byl směrován do, pokud není k dispozici dostatek dat pak latence vrácena bude null. 

## <a name="visual-overview"></a>Vizuální přehled

Když přejdete do části **Zobrazení provozu** na stránce Traffic Manager, zobrazí se vám geografická mapa s překrytím přehledů zobrazení provozu. Mapa poskytuje informace o uživatelské základně a koncových bodech pro váš profil Traffic Manageru.

![Geografické zobrazení traffic manageru traffic view][1]

### <a name="user-base-information"></a>Informace o uživatelské základně

Pro místní překladače DNS, pro které jsou k dispozici informace o poloze, jsou zobrazeny v mapě. Barva překladače DNS označuje průměrnou latenci, kterou mají koncoví uživatelé, kteří tento překladač DNS používali pro své dotazy Traffic Manageru.

Pokud na mapě najedete na místo překladače DNS, zobrazí se:
- IP adresa překladače DNS
- objem provozu dotazů DNS, který z něj traffic manager zaznamenal
- koncové body, do kterých byl směrován provoz z překladače DNS jako čára mezi koncovým bodem a překladačem DNS 
- průměrná latence z tohoto umístění do koncového bodu, reprezentovaná jako barva čáry, která je spojuje

### <a name="endpoint-information"></a>Informace o koncovém bodu

Oblasti Azure, ve kterých jsou umístěny koncové body jsou zobrazeny jako modré tečky v mapě. Pokud je váš koncový bod externí a nemá na něj namapovanou oblast Azure, zobrazí se v horní části mapy. Kliknutím na libovolný koncový bod zobrazíte různá umístění (na základě použitého překladače DNS), odkud byl provoz směrován do tohoto koncového bodu. Připojení jsou zobrazeny jako čára mezi koncovým bodem a umístěním překladače DNS a jsou barevné podle reprezentativní latence mezi tímto párem. Kromě toho můžete zobrazit název koncového bodu, oblast Azure, ve kterém se spouští, a celkový objem požadavků, které na něj byly směrovány tímto profilem Traffic Manageru.


## <a name="tabular-listing-and-raw-data-download"></a>Tabulkový výpis a stahování nezpracovaných dat

Data zobrazení provozu můžete zobrazit v tabulkovém formátu na webu Azure Portal. Pro každý pár IP/koncového bodu překladače DNS je položka, která zobrazuje IP adresu překladače DNS, název a geografické umístění oblasti Azure, ve které je koncový bod umístěn (pokud je k dispozici), objem požadavků přidružených k tomuto překladači DNS k tomuto koncovému bodu a reprezentativní latence přidružená koncovým uživatelům používajícím tento DNS (pokud je k dispozici). Data zobrazení provozu můžete také stáhnout jako soubor CSV, který lze použít jako součást analytického pracovního postupu podle vašeho výběru.

## <a name="billing"></a>Fakturace

Při použití zobrazení provozu se vám budou účtovat na základě počtu datových bodů použitých k vytvoření prezentovaných přehledů. V současné době je jediným použitým typem datového bodu dotazy přijaté proti profilu traffic manageru. Další podrobnosti o cenách najdete na [stránce s cenami v Traffic Manageru](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Nejčastější dotazy

* [K čemu zobrazení provozu dělá?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Jak mohu využít zobrazení provozu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Jak se zobrazení návštěvnosti liší od metrik Traffic Manageru dostupných prostřednictvím azure monitoru?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Používá zobrazení provozu informace o podsíti klienta EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Kolik dní dat zobrazení provozu používá?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [Jak zobrazení provozu zpracovává externí koncové body?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [Musím povolit zobrazení provozu pro každý profil v mém předplatném?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Jak mohu vypnout traffic view?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Jak funguje fakturace zobrazení provozu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Další kroky

- [Zjistěte, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) podporovaných traffic managerem
- Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png