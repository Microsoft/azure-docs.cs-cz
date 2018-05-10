---
title: Analýza uživatelů uchovávání informací pro webové aplikace pomocí služby Azure Application Insights | Microsoft docs
description: Počet uživatelů, kteří se vrátit do vaší aplikace?
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: bafe9579cc783dbc5c851e626e3b9bea0c559520
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analýza uživatelů uchovávání informací pro webové aplikace pomocí Application Insights

Funkci uchování v [Azure Application Insights](app-insights-overview.md) pomáhá analyzovat počet uživatelů, kteří se vraťte do vaší aplikace a jak často provádět konkrétní úlohy nebo dosáhnout cíle. Například pokud spustíte herní lokality, může porovnejte počet uživatelů, kteří vrátit do lokality po ztrátě hry s číslem, kteří se vrátí po hodnocený. Tyto znalosti vám pomůžou líp uživatelské prostředí a obchodní strategie.

## <a name="get-started"></a>Začínáme

Pokud nevidíte ještě dat v nástroji pro uchovávání informací na portálu služby Application Insights [zjistěte, jak začít pracovat s nástroji využití](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Nástroj pro uchování

![Nástroj Udržení](./media/app-insights-usage-retention/retention.png)

1. Panel nástrojů umožňuje uživatelům vytváření nových sestav uchování, otevřete existující sestavy uchování, uloží aktuální sestavu uchování nebo uložit, protože vrátit zpět změny provedené v uložených sestav, aktualizovat data v sestavě, sdílené složky sestav e-mailem nebo přímý odkaz a přístup k na stránce dokumentace. 
2. Ve výchozím nastavení zobrazuje uchování všichni uživatelé, kteří neprovedli nic, pak se vrátila zpět a neprovedli nic jiného v období. Můžete vybrat různé kombinace události zúžit zaměření na konkrétního uživatele aktivity.
3. Přidejte jeden nebo více filtry vlastností. Například se můžete soustředit na uživatele v konkrétní země nebo oblasti. Klikněte na tlačítko **aktualizace** po nastavení filtrů. 
4. Celkové uchování graf zobrazuje souhrn uživatele uchování mezi zvolené časové období. 
5. Mřížky zobrazuje počet uživatelů, které uchovávají podle Tvůrce dotazů v #. 2. Každý řádek představuje kohorty uživatele, který provedl všechny události v zobrazeném časovém období. Každý buňky v řádku zobrazuje, kolik této kohorty vrátil nejméně jednou za na pozdější dobu. Někteří uživatelé se můžou vrátit ve více než jedno období. 
6. Statistika karty Zobrazit nejvyšší pět inicializace události a horní pět vrácený události uživatelům lépe porozumět jejich uchovávání dat sestavy. 

![Při přechodu myší uchování](./media/app-insights-usage-retention/hover.png)

Uživatelé mohou pozastavte ukazatel myši nad buněk na nástroj uchování pro přístup k analytics nástroj a tlačítko tipy vysvětlující, co znamená buňky. Tlačítko Analytics trvá uživatelům nástroj pro analýzu s předem vyplněná dotazu generovat uživatelé z buňky. 

## <a name="use-business-events-to-track-retention"></a>Použít obchodní události ke sledování uchování

Nejužitečnější analysis uchování získáte měření události, které představují důležité obchodní aktivity. 

Například může být řada uživatelů otevřete stránku ve vaší aplikaci bez hru, který se zobrazí. Sledování právě zobrazení stránky by proto zadejte nesprávné odhad kolik lidí vrátit ve hře po jeho využívat dříve. Získat přehledné informace o vrácení přehrávače, vaše aplikace by měli poslat vlastní události, když uživatel ve skutečnosti hraje.  

Je dobrým zvykem kód vlastní události, které představují klíče obchodní akce a použít pro uchování analýzy. Když Pokud chcete zachytit herní výsledek, budete muset napsat řádek kódu pro odeslání vlastní události Application insights. Pokud píšete ho v kódu webové stránky nebo v Node.JS, vypadá takto:

```JavaScript
    appinsights.trackEvent("won game");
```

Nebo v serverovém kódu ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Další informace o vytváření vlastních událostí](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Další postup
- Pokud chcete povolit použití možnosti, zahájit odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [stránky zobrazení](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již odeslat vlastní události nebo zobrazení stránky, prozkoumejte využití nástroje se dozvíte, jak uživatelé používat služby.
    - [Uživatelé, relace, události](app-insights-usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Workbooks](app-insights-usage-workbooks.md)
    - [Přidat uživatelský kontext](app-insights-usage-send-user-context.md)


