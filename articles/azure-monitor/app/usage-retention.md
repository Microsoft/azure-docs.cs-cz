---
title: Analýza uchovávání informací uživatelů pro webové aplikace s využitím Azure Application Insights | Dokumentace Microsoftu
description: Kolik uživatelů se vrátí do vaší aplikace?
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 5f5f6235354adc565815ac2eab0a1c774267102d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899425"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analýza uchovávání informací uživatelů pro webové aplikace pomocí Application Insights

Funkce uchování v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) pomáhá analyzovat počet uživatelů, kteří se vrátí do vaší aplikace, a jak často provádějí konkrétní úkoly nebo dosahují cílů. Pokud například spustíte herní server, můžete porovnat počet uživatelů, kteří se vrátí k webu, po ztrátě hry s číslem vráceným po výhrě. Tato znalostní báze vám může pomoci vylepšit uživatelské prostředí i obchodní strategii.

## <a name="get-started"></a>Začít

Pokud se vám ještě nezobrazuje data na portálu Application Insights, [Přečtěte si, jak začít s nástroji pro používání](usage-overview.md).

## <a name="the-retention-tool"></a>Nástroj pro uchovávání

![Nástroj Udržení](./media/usage-retention/retention.png)

1. Panel nástrojů umožňuje uživatelům vytvářet nové sestavy pro uchovávání informací, otevírat existující sestavy uchovávání informací, ukládat aktuální sestavu o uchovávání dat nebo je ukládat jako, vracet změny provedené v uložených sestavách, aktualizovat data v sestavě, sdílet sestavu e-mailem nebo přímým odkazem a přistupovat k dokumentaci. Page. 
2. Ve výchozím nastavení se v rámci uchovávání zobrazují všichni uživatelé, kteří něco udělali zpátky a za určitou dobu udělali něco jiného. Můžete vybrat jinou kombinaci událostí a zúžit tak zaměření na konkrétní aktivity uživatelů.
3. Přidejte jeden nebo více filtrů do vlastností. Můžete se třeba zaměřit na uživatele v určité zemi nebo oblasti. Po nastavení filtrů klikněte na **aktualizovat** . 
4. Celkový graf uchování zobrazuje souhrn uchovávání uživatelů napříč vybraným časovým obdobím. 
5. V mřížce se zobrazuje počet uživatelů, kteří se uchovávají podle Tvůrce dotazů v #2. Každý řádek představuje kohorta uživatele, kteří provedli jakoukoli událost v zobrazeném časovém období. Každá buňka na řádku ukazuje, kolik z těchto kohorta bylo v pozdějším období vráceno alespoň jednou. Někteří uživatelé se mohou vracet ve více než jedné lhůtě. 
6. Karty s přehledy zobrazují pět událostí zahájení a prvních pět vrácených událostí, které uživatelům poskytují lepší představu o jejich sestavě o jejich uchování. 

![Najetí myší na uchování](./media/usage-retention/hover.png)

Uživatelé mohou umístit ukazatel myši nad buňky nástroje pro uchovávání a získat tak přístup k tlačítku Analytics a popisům tlačítek, které vysvětlují, co buňka znamená. Tlačítko Analýza převezme uživatele do nástroje Analytics Tool s předem vyplněným dotazem, který vygeneruje uživatele z buňky. 

## <a name="use-business-events-to-track-retention"></a>Použití obchodních událostí ke sledování uchovávání informací

Chcete-li získat nejužitečnější analýzu uchovávání informací, změřte události, které představují významné obchodní aktivity. 

Mnoho uživatelů může například v aplikaci otevřít stránku bez hraní hry, kterou zobrazuje. Sledování pouze zobrazení stránky by proto poskytovalo nepřesný odhad počtu osob, které se po jejich opětovném využití vrátí k přehrání hry. Pokud chcete získat jasný přehled o vracení přehrávačů, vaše aplikace by měla odeslat vlastní událost, když uživatel skutečně přehraje.  

Dobrým zvykem je zakódovat vlastní události, které reprezentují klíčové obchodní akce, a využít je k analýze uchovávání informací. Chcete-li zachytit výsledek hry, je nutné napsat řádek kódu k odeslání vlastní události do Application Insights. Pokud jej píšete v kódu webové stránky nebo v Node. JS, vypadá to jako:

```JavaScript
    appinsights.trackEvent("won game");
```

Nebo v kódu serveru ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Přečtěte si další informace o psaní vlastních událostí](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Další kroky
- Chcete-li povolit prostředí používání, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste už odeslali vlastní události nebo zobrazení stránky, prozkoumejte nástroje využití a zjistěte, jak uživatelé používají vaši službu.
    - [Uživatelé, relace, události](usage-segmentation.md)
    - [Trychtýře](usage-funnels.md)
    - [Toky uživatele](usage-flows.md)
    - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
    - [Přidat kontext uživatele](usage-send-user-context.md)


