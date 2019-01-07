---
title: Analytics – výkonné hledání a dotazovací nástroj založený na služby Azure Application Insights | Dokumentace Microsoftu
description: 'Přehled analýzy, nástroj výkonné diagnostické vyhledávání služby Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 52b18f3f6f1166650fb13694f3eed54c28bbc0a9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731021"
---
# <a name="analytics-in-application-insights"></a>Analýzy ve službě Application Insights
Analýza je výkonný nástroj hledání a dotaz z [Application Insights](../../application-insights/app-insights-overview.md). Analytics je webový nástroj, takže není zapotřebí žádné nastavení. Pokud jste již nakonfigurovali Application Insights pro některé z aplikací, můžete analyzovat data vaší aplikace tak, že otevřete Analytics z vaší aplikace [okno přehledu](../../azure-monitor/app/app-insights-dashboards.md).

![Otevřené stránce portal.azure.com otevřete prostředek Application Insights a klikněte na tlačítko Analytics.](./media/analytics/001.png)

Můžete také použít [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) což je bezplatná ukázkovém prostředí s velkým množstvím ukázková data.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Dotazování dat v Analytics
Typický dotaz začíná název tabulky, za nímž následuje řadu *operátory* oddělené `|`.
Například můžeme zjistit, kolik požadavků naši aplikaci přijatých z různých zemí, během posledních 3 hodiny:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Začínáme s názvem tabulky *požadavky* a podle potřeby přidejte potrubím elementy.  Nejprve definujeme filtr času ke kontrole pouze záznamy z poslední 3 hodiny.
Počítáme počet záznamů za země (, že se data nachází ve sloupci *client_CountryOrRegion*). Nakonec jsme vykreslovat výsledky v grafu.
<br>

![Výsledky dotazu](./media/analytics/030.png)

Jazyk má mnoho atraktivní funkce:

* [Filtr](/azure/kusto/query/whereoperator) telemetrie vaší aplikace nezpracovaná tak všechna pole, včetně vaší vlastní vlastnosti a metriky.
* [Připojte se k](/azure/kusto/query/joinoperator) více tabulek je možné korelovat požadavky s zobrazení stránek, volání závislostí, výjimek a trasování protokolu.
* Výkonný statistický [agregace](/azure/kusto/query/summarizeoperator).
* Okamžité a výkonnou vizualizací.
* [Rozhraní REST API](https://dev.applicationinsights.io/) , můžete použít ke spouštění dotazů prostřednictvím kódu programu, například z Powershellu.

[Úplné referenční informace k jazyku](https://go.microsoft.com/fwlink/?linkid=856079) podrobnosti každý příkaz podporovány a pravidelně aktualizuje.

## <a name="next-steps"></a>Další postup
* Začínáme s [portál Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Začínáme [zápis dotazů](https://go.microsoft.com/fwlink/?linkid=856078)
* Zkontrolujte [SQL-uživatelů tahák](https://aka.ms/sql-analytics) pro překlady nejběžnější idiomy.
* Vyzkoušejte si analýzy na naše [playground](https://analytics.applicationinsights.io/demo) Pokud vaše aplikace neodesílá data do Application Insights ještě.
* Podívejte [úvodní video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).