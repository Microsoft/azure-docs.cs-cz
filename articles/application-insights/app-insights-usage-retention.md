---
title: Analýza uživatelů uchovávání informací pro webové aplikace pomocí Azure Application Insights | Dokumentace Microsoftu
description: Kolik uživatelů se vrátit do své aplikace?
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 85fd0131da6d67cb8001c4b3de25af0c4575a64d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721982"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analýza uživatelů uchovávání informací pro webové aplikace pomocí Application Insights

Funkce uchování v [Azure Application Insights](app-insights-overview.md) pomáhá analyzovat kolik uživatelů do vaší aplikace vracejí, a jak často provádět konkrétní úlohy nebo cílů. Například pokud spustíte her lokality, může porovnat počtu uživatelů, kteří vraťte se na web po ztrátě hry s použitím čísla, kteří se vrátí po soutěže winning. Tyto znalosti pomáhá vylepšovat uživatelské prostředí a strategii.

## <a name="get-started"></a>Začínáme

Pokud jste ještě nezobrazují žádná data v nástroji pro uchovávání informací na portálu služby Application Insights [zjistěte, jak začít pracovat s nástroji využití](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Nástroj udržení

![Nástroj Udržení](./media/app-insights-usage-retention/retention.png)

1. Panel nástrojů umožňuje uživatelům vytvářet nové sestavy uchovávání, otevřete existující sestavy uchovávání, uložit aktuální sestavu uchování nebo si uložte, protože vrátit zpět změny provedené v uložených sestav, aktualizovat data v sestavě, sdílet sestavu e-mailem nebo přímý odkaz a přístup k dokumentaci stránka. 
2. Ve výchozím nastavení zobrazuje uchování všem uživatelům, kteří neprovedli nic, pak se nevrátili a cokoli, nebyl během období. Můžete vybrat jinou kombinaci události zúžit zaměření na aktivit konkrétního uživatele.
3. Přidejte jeden nebo více filtrů na vlastnosti. Například se můžete zaměřit na uživatele v konkrétní zemi nebo oblast. Klikněte na tlačítko **aktualizace** po nastavení filtrů. 
4. Celkový graf uchování zobrazuje souhrn udržení uživatelů ve vybraném časovém období. 
5. Mřížky se zobrazí počet uživatelů, které uchovávají podle Tvůrce dotazů v #2. Každý řádek představuje kohorta uživatelů, který provedl jakékoli události v zobrazeném časovém období. Každá buňka v řádku ukazuje, kolik z tohoto kohorta vrátil nejméně jednou za na pozdější dobu. Někteří uživatelé mohou vrátit ve více než jednu tečku. 
6. Karty insights zobrazit horní pět zahájení události a horní pět vrácené události a poskytuje tak uživatelům lépe pochopit jejich uchování sestav. 

![Uchování myši při najetí myší](./media/app-insights-usage-retention/hover.png)

Uživatelé mohou najet myší buněk v nástroje uchovávání pro přístup k analytics tlačítko a nástroj tipy s vysvětlením, co znamená buňku. Tlačítko Analytics přejdou uživatelé k nástroj analýzy s předem naplněných dotazu generovat uživatelů z buňky. 

## <a name="use-business-events-to-track-retention"></a>Použít obchodní události ke sledování uchovávání informací

Nejužitečnější analýzy uchování získáte měření událostí, které představují důležité obchodní činnost. 

Například může být mnoho uživatelů otevřete stránku ve vaší aplikaci bez hru, která se zobrazí. Sledování právě zobrazení stránek by proto poskytnout odhad nepřesné vrátit kolik lidí si tuto hru zahrát po využívat dříve. Získat přehledné informace o vrácení přehrávačů, by měla vaše aplikace odeslat vlastní událost, když uživatel přehrává ve skutečnosti.  

Je dobrým zvykem naprogramujte vlastní události, které představují akce klíčových obchodních a použít pro analýzu uchovávání informací. Pokud chcete zaznamenat výsledek her, budete muset psát řádek kódu pro odesílání vlastních událostí do Application Insights. Pokud můžete zapsat do kódu webové stránky nebo v Node.JS, bude vypadat takto:

```JavaScript
    appinsights.trackEvent("won game");
```

Nebo v kódu serveru ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Další informace o vytváření vlastních událostí](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Další postup
- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Workbooks](app-insights-usage-workbooks.md)
    - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)


