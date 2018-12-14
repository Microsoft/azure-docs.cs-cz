---
title: Vizualizace dat ze služby Azure Monitor | Dokumentace Microsoftu
description: Poskytuje přehled dostupných metod k vizualizaci dat uložených ve službě Azure Monitor, včetně dat z úložiště metriky a Log Analytics.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: a2fd26d110e7bf1ce7ac365b83659e5d33a037df
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383540"
---
# <a name="visualizing-data-from-azure-monitor"></a>Vizualizace dat ze služby Azure Monitor
Tento článek obsahuje přehled dostupných metod k vizualizaci dat uložených ve službě Azure Monitor. Jedná se o [metriky v úložišti Azure metriky](../azure-monitor/platform/data-collection.md#metrics) a [dat protokolů v Log Analytics](../azure-monitor/platform/data-collection.md#logs). 

Vizualizace, jako jsou tabulky a grafy vám může pomoct analyzovat data monitorování k procházení podrobností podle problémů a identifikaci vzorů. V závislosti na nástroj, který používáte může mít také možnost sdílení vizualizací s ostatními uživateli uvnitř i mimo vaši organizaci.

## <a name="azure-dashboards"></a>Řídicích panelů Azure
[Řídicích panelů Azure](../azure-portal/azure-portal-dashboards.md) jsou primární mnoha technologie pro Azure. Jsou zvláště užitečné při poskytování najednou přes infrastrukturu Azure a služby, která umožňuje rychle identifikovat důležité problémy.

![Řídicí panel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Výhody
- Těsnou integraci s Azure. Vizualizace můžete připnout na řídicí panely z více stránek Azure včetně Průzkumníka metrik, Log Analytics a Application Insights.
- Podporuje metrik a protokolů.
- Kombinovat data z víc zdrojů, včetně výstup z [Průzkumníka metrik](../azure-monitor/platform/metrics-charts.md), [dotazy Log Analytics](../azure-monitor/log-query/log-query-overview.md), a [mapuje](../application-insights/app-insights-app-map.md) a [dostupnosti]()ve službě Application Insights.
- Možnost pro osobní nebo sdílené řídicí panely. Díky integraci s Azure [ověřování na základě role (RBAC)](../role-based-access-control/overview.md).
- Automatická aktualizace. Aktualizace metrik závisí na časový rozsah s minimálně pět minut. Aktualizovat protokoly na jednu minutu.
- Podle řídicí panely metriky s časovým razítkem a vlastní parametry.
- Možnosti flexibilní rozložení.
- Režim zobrazení na celé obrazovce.


### <a name="limitations"></a>Omezení
- Omezené možnosti kontroly nad vizualizací Log Analytics bez podpory pro data tabulky. Celkový počet datových řad je omezené na 10 s další datové řady seskupené pod nadpisem _jiných_ kontejneru.
- Žádné vlastní parametry podporu pro grafy Log Analytics.
- Log Analytics grafy jsou omezené na posledních 30 dní.
- Log Analytics grafy je možné připnout jenom na sdílené řídicí panely.
- Žádná interakce s daty řídicího panelu.
- Omezené kontextové procházení.

## <a name="azure-monitor-views"></a>Azure Monitor zobrazení
[Zobrazení ve službě Azure Monitor](../azure-monitor/platform/view-designer.md) vám umožňují vytvářet vlastní vizualizace s objemu dat uložených ve službě Log Analytics. Jsou používány [řešení monitorování](../azure-monitor/insights/solutions.md) data můžete prezentovat tak jejich shromažďování.

![Zobrazení](media/visualizations/view.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace pro data služby Log Analytics.
- Export a import zobrazení přenést do jiné skupiny prostředků a předplatných.
- Integruje se do protokolu analytická správy modelu s pracovními prostory a monitorování řešení.
- [Filtry](../azure-monitor/platform/view-designer-filters.md) pro vlastní parametry.
- Interaktivní, podporuje víceúrovňové procházení v (zobrazení, která přechází do jiného zobrazení)

### <a name="limitations"></a>Omezení
- Podporuje protokoly ne však metriky.
- K dispozici žádná osobní zobrazení. K dispozici všem uživatelům přístup k pracovnímu prostoru.
- Žádná automatická aktualizace.
- Omezené možnosti rozložení.
- Bez podpory pro dotazování napříč pracovních prostorů Log Analytics a Application Insights aplikace.
- Dotazy mají omezenou velikost odpovědi 8MB a dotaz čas spuštění 110 sekund.



## <a name="application-insights-workbooks"></a>Sešity Application Insights
[Sešity](../application-insights/app-insights-usage-workbooks.md) jsou interaktivní dokumenty, které poskytují podrobné přehledy o vašich dat, prozkoumání a spolupráci v týmu. Konkrétní příklady, kde jsou užitečné sešity jsou řešení potíží s pokyny a incident postmortem.

![Sešit](media/visualizations/workbook.png)

### <a name="advantages"></a>Výhody
- Podporuje metrik a protokolů.
- Podporuje parametry umožňující interaktivní sestavy, ve kterém výběr elementu v tabulce se dynamicky aktualizovat přidružené grafů a vizualizací.
- Dokumenty toku.
- Možnost pro osobní nebo sdílené sešity.
- Jednoduchý a spolupráci přívětivá prostředí pro vytváření.
- Šablony podporují Galerie veřejné šablony založené na Githubu.

### <a name="limitations"></a>Omezení
- Žádná automatická aktualizace.
- Žádné kompaktní rozložení, jako jsou řídicí panely, které sešity méně užitečné jako podokně ze skla. Další možnosti pro zajištění hlubších poznatků určené.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je zvláště užitečná pro vytváření napjaté řídicí panely a sestavy, jakož i sestavy analýza dlouhodobé trendy klíčových ukazatelů výkonu. Je možné [importovat výsledky dotazu Log Analytics](../azure-monitor/platform/powerbi.md) do datové sady Power BI, takže můžete využít jeho funkce, jako je kombinování dat z různých zdrojů a sdílení sestav na webu a mobilních zařízeních.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace.
- Extenzivní interaktivita, včetně přiblížení a křížové filtrování.
- Dají se snadno sdílet v rámci vaší organizace.
- Integrace s ostatními daty z více zdrojů dat.
- Lepší výkon s výsledky z mezipaměti v datové krychli.


### <a name="limitations"></a>Omezení
- Podporuje protokoly ne však metriky.
- Bez integrace s Azure. Nelze spravovat řídicí panely a modelů prostřednictvím Azure Resource Manageru.
- Výsledky dotazu musí být importován do modelu Power BI ke konfiguraci. Omezení velikosti výsledek a aktualizace.
- Omezená data aktualizovat osm časy za den.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) je otevřená platforma, která vyniká v operační řídicí panely. Je to užitečné zejména pro zjištění a izolace a třídění provozní incidenty. Můžete přidat [modulu plug-in zdroje dat monitorování Azure Grafana](../monitoring-and-diagnostics/monitor-send-to-grafana.md) ke svému předplatnému Azure její vizualizace dat metriky Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace.
- Bohatý ekosystém datových zdrojů.
- Přiblížení data včetně interaktivitu.
- Podporuje parametry.

### <a name="limitations"></a>Omezení
- Podporuje metriky, ale nezahrnuje protokoly.
- Bez integrace s Azure. Nelze spravovat řídicí panely a modelů prostřednictvím Azure Resource Manageru.
- Náklady na podporu cloudu Grafana další infrastrukturu Grafana nebo další poplatky.


## <a name="build-your-own-custom-application"></a>Sestavení vlastní aplikace
Prostřednictvím svých rozhraní API z libovolného klienta REST, které vám umožní vytvářet své vlastní weby a aplikace můžou k datům ve metriky a Azure Log Analytics.

### <a name="advantages"></a>Výhody
- Naprostou flexibilitu uživatelského rozhraní, vizualizaci, interakce a funkce.
- Kombinujte metriky a protokolování dat s jinými zdroji dat.

### <a name="disadvantages"></a>Nevýhody
- Významné technického úsilí, které jsou povinné.


## <a name="next-steps"></a>Další postup
- Další informace o [data shromážděná službou Azure Monitor](../azure-monitor/platform/data-collection.md).
- Další informace o [řídicích panelů Azure](../azure-portal/azure-portal-dashboards.md).
- Další informace o [zobrazení ve službě Azure Monitor](../azure-monitor/platform/view-designer.md).
- Další informace o [sešity ve službě Application Insights](../application-insights/app-insights-usage-workbooks.md).
- Další informace o [importovat do Power BI data protokolu](../azure-monitor/platform/powerbi.md).
- Další informace o [modulu plug-in zdroje dat monitorování Azure Grafana](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
