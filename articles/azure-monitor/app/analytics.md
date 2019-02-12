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
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: c9392d910098e8a2dfadc4842dfcfe185f01fafc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004515"
---
# <a name="analytics-in-application-insights"></a>Analýzy ve službě Application Insights
Analýza je výkonný nástroj hledání a dotaz z [Application Insights](app-insights-overview.md). Analytics je webový nástroj, takže není zapotřebí žádné nastavení.
Pokud jste již nakonfigurovali Application Insights pro některé z aplikací, můžete analyzovat data vaší aplikace tak, že otevřete Analytics z vaší aplikace [okno přehledu](app-insights-dashboards.md).

![Otevřené stránce portal.azure.com otevřete prostředek Application Insights a klikněte na tlačítko Analytics.](./media/analytics/001.png)

Můžete také použít [Analytics playground](https://go.microsoft.com/fwlink/?linkid=859557) což je bezplatná ukázkovém prostředí s velkým množstvím ukázková data.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Vztah k protokoly Azure monitoru
Application Insights analytics vychází [Průzkumník dat Azure](/azure/data-explorer) jako protokoly Azure monitoru a také používá [Průzkumník dat dotazovací jazyk](/azure/kusto/query). Používá stejný [portál log analytics](../log-query/get-started-portal.md) jako protokoly Azure monitoru, i když jeho data se ukládají v samostatném oddílu.

Můžete přímo k datům nelze přistoupit v pracovním prostoru Log Analytics z Application Insights analytics ani můžete jste přímý přístup k aplikaci data ze služby log analytics. Aby bylo možné dotazovat obě sady dat společně, zápisu [dotazu v log analytics](../log-query/log-query-overview.md) a použití [App() použijte výraz](../log-query/app-expression.md) pro přístup k datům aplikace.


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
* [Připojte se k](/azure/kusto/query/joinoperator) více tabulek - korelaci požadavků s zobrazení stránek, volání závislostí, výjimek a trasování protokolu.
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
