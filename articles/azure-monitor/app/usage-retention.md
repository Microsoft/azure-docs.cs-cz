---
title: Analýza uchovávání informací uživatelů pro webové aplikace pomocí Azure Application Insights | Dokumenty Microsoftu
description: Kolik uživatelů se vrátí do vaší aplikace?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37ae97d5fbc62e507f726c452999a7f6e7c989c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670963"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analýza uchovávání informací uživatelů pro webové aplikace s application insights

Funkce uchovávání informací v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) vám pomůže analyzovat, kolik uživatelů se vrátí do vaší aplikace a jak často provádějí určité úkoly nebo dosahují cílů. Pokud například provozujete herní web, můžete porovnat počet uživatelů, kteří se na web vrátí po prohrané hře, s počtem uživatelů, kteří se vrátí po výhře. Tyto znalosti vám mohou pomoci zlepšit uživatelskou zkušenost i obchodní strategii.

## <a name="get-started"></a>Začínáme

Pokud data v nástroji pro uchovávání informací na portálu Application Insights ještě nevidíte, [přečtěte si, jak začít s nástroji pro využití](usage-overview.md).

## <a name="the-retention-tool"></a>Nástroj retence

![Nástroj Udržení](./media/usage-retention/retention.png)

1. Panel nástrojů umožňuje uživatelům vytvářet nové zprávy o uchovávání informací, otevírat stávající zprávy o uchovávání informací, ukládat aktuální zprávy o uchovávání informací nebo je ukládat jako, vracet změny provedené v uložených sestavách, aktualizovat data v sestavě, sdílet sestavu prostřednictvím e-mailu nebo přímého odkazu a získat přístup k dokumentaci. Stránka. 
2. Ve výchozím nastavení uchovávání zobrazuje všechny uživatele, kteří něco udělali, a pak se vrátili a udělali něco jiného za určité období. Můžete vybrat různé kombinace událostí zúžit zaměření na konkrétní aktivity uživatele.
3. Přidejte jeden nebo více filtrů na vlastnosti. Můžete se například zaměřit na uživatele v určité zemi nebo oblasti. Po nastavení filtrů klepněte na **tlačítko Aktualizovat.** 
4. Celkový retenční graf zobrazuje souhrn uchovávání uživatelů ve vybraném časovém období. 
5. Mřížka zobrazuje počet uživatelů uchovávaných podle tvůrce dotazů v #2. Každý řádek představuje kohortu uživatelů, kteří provedli jakoukoli událost v zobrazeném časovém období. Každá buňka v řádku ukazuje, kolik z této kohorty vrátil alespoň jednou v pozdějším období. Někteří uživatelé se mohou vrátit ve více než jednom období. 
6. Karty přehledů zobrazují prvních pět iniciačních událostí a prvních pět vrácených událostí, aby uživatelé lépe porozuměli své zprávě o uchovávání informací. 

![Retenční mouse hover](./media/usage-retention/hover.png)

Uživatelé mohou najet přes buňky na retenční nástroj pro přístup k analytické tlačítko a tipy nástrojů vysvětluje, co buňka znamená. Tlačítko Analytics přenese uživatele do nástroje Analytics s předem vyplněným dotazem, který generuje uživatele z buňky. 

## <a name="use-business-events-to-track-retention"></a>Použití obchodních událostí ke sledování uchovávání informací

Chcete-li získat nejužitečnější analýzu uchovávání informací, změřte události, které představují významné obchodní aktivity. 

Mnoho uživatelů může například otevřít stránku ve vaší aplikaci, aniž by hráli hru, kterou zobrazuje. Sledování pouze zobrazení stránky by proto poskytlo nepřesný odhad toho, kolik lidí se vrátí hrát hru poté, co si ji dříve užívali. Chcete-li získat jasný obraz o vracejících se hráčích, měla by aplikace odeslat vlastní událost, když se uživatel skutečně přehrává.  

Je vhodné kódovat vlastní události, které představují klíčové obchodní akce, a použít je pro analýzu uchovávání informací. Chcete-li zachytit výsledek hry, musíte napsat řádek kódu pro odeslání vlastní události do Application Insights. Pokud jej napíšete do kódu webové stránky nebo do souboru Node.JS, vypadá to takto:

```JavaScript
    appinsights.trackEvent("won game");
```

Nebo v ASP.NET kód serveru:

```csharp
   telemetry.TrackEvent("won game");
```

[Další informace o psaní vlastních událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Další kroky
- Chcete-li povolit možnosti využití, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud už vlastní události nebo zobrazení stránek odesíláte, prozkoumejte nástroje využití a zjistěte, jak uživatelé vaši službu používají.
    - [Uživatelé, relace, události](usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Toky uživatele](usage-flows.md)
    - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
    - [Přidání kontextu uživatele](usage-send-user-context.md)


