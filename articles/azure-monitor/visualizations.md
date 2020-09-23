---
title: Vizualizace dat z Azure Monitor | Microsoft Docs
description: Poskytuje souhrn dostupných metod pro vizualizaci dat metrik a protokolů uložených v Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 4c9f74f5a9950a299b34e22c1e86dd91f8c82364
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983605"
---
# <a name="visualizing-data-from-azure-monitor"></a>Vizualizace dat z Azure Monitoru
Tento článek poskytuje souhrn dostupných metod pro vizualizaci dat protokolů a metrik uložených v Azure Monitor.

Vizualizace, jako jsou grafy a grafy, vám pomohou analyzovat data monitorování, aby bylo možné rozlišit problémy a identifikovat vzory. V závislosti na nástroji, který použijete, můžete také sdílet vizualizace s ostatními uživateli v rámci vaší organizace i mimo ni.

## <a name="workbooks"></a>Workbooks
[Sešity](./platform/workbooks-overview.md) jsou interaktivní dokumenty, které poskytují podrobné přehledy o vašich datech, vyšetřování a spolupráci v rámci týmu. Konkrétní příklady, kde jsou sešity užitečné, jsou průvodci odstraňováním potíží a Postmortem incidentů.

![Diagram znázorňuje snímky obrazovky několika stránek ze sešitu, včetně analýzy zobrazení stránky, využití a času stráveného na stránce.](media/visualizations/workbook.png)

### <a name="advantages"></a>Výhody
- Podporuje obě metriky a protokoly.
- Podporuje parametry umožňující interaktivní sestavy, ve kterých se při výběru prvku v tabulce dynamicky aktualizují přidružené grafy a vizualizace.
- Tok podobný tomuto dokumentu.
- Možnost pro osobní nebo sdílené sešity.
- Jednoduché a uživatelsky přívětivé prostředí pro vytváření.
- Šablony podporují galerii šablon založených na GitHubu.

### <a name="limitations"></a>Omezení
- Bez automatické aktualizace.
- Žádná zhuštěná rozložení, jako jsou řídicí panely, což vede k tomu, že sešity jsou méně užitečné jako samostatné podokno skla. Pro poskytování hlubších poznatků je zamýšleno více.


## <a name="azure-dashboards"></a>Řídicí panely Azure
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) jsou primární technologie pro řídicí panely pro Azure. Jsou zvláště užitečné v případě, že máte v infrastruktuře a službách Azure samostatné podokno, které vám umožní rychle identifikovat důležité problémy.

![Snímek obrazovky ukazuje příklad řídicího panelu Azure s přizpůsobitelnými informacemi.](media/visualizations/dashboard.png)

Tady je video s návodem k vytváření řídicích panelů.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Výhody
- Hluboká integrace do Azure Vizualizace je možné připnout na řídicí panely z více stránek Azure, včetně Průzkumník metrik, Log Analytics a Application Insights.
- Podporuje obě metriky a protokoly.
- Kombinovat data z různých zdrojů, včetně výstupu z [Průzkumníka metrik](platform/metrics-charts.md), [dotazů protokolu](log-query/log-query-overview.md)a [map](app/app-map.md) a dostupnosti v Application Insights.
- Možnost pro osobní nebo sdílené řídicí panely. Integrováno s [ověřováním na základě rolí Azure (RBAC)](../role-based-access-control/overview.md).
- Automatická aktualizace Aktualizace metrik závisí na časovém rozsahu, který je minimálně pět minut. Protokoly se aktualizují každou hodinu s možností ruční aktualizace na vyžádání kliknutím na ikonu aktualizovat v dané vizualizaci nebo aktualizací úplného řídicího panelu.
- Parametry a řídicí panely metriky s parametry s časovým razítkem a vlastními parametry.
- Flexibilní možnosti rozložení.
- Režim zobrazení na celé obrazovce.


### <a name="limitations"></a>Omezení
- Omezená kontrola nad vizualizacemi protokolu bez podpory tabulek dat. Celkový počet datových řad je omezený na 10 s další datovou řadou seskupenou v _jiném_ intervalu.
- Nepodporují se žádné vlastní parametry pro grafy protokolů.
- Grafy protokolů jsou omezené na posledních 30 dní.
- Grafy protokolů je možné připnout pouze na sdílené řídicí panely.
- Žádná interaktivita s daty řídicího panelu
- Omezené kontextové procházení


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je zvláště užitečné pro vytváření řídicích panelů a sestav orientovaných na firmy a také sestav, které analyzují dlouhodobé trendy klíčových ukazatelů výkonu. [Výsledky dotazu protokolu můžete importovat](platform/powerbi.md) do Power BI datové sady, abyste mohli využít jeho funkce, jako je například kombinování dat z různých zdrojů a sdílení sestav na webových a mobilních zařízeních.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Výhody
- Bohatá vizualizace.
- Rozsáhlá interaktivita, včetně přiblížení a křížového filtrování.
- Snadné sdílení v celé organizaci.
- Integrace s dalšími daty z více zdrojů dat.
- Lepší výkon s výsledky uloženými v mezipaměti v datové krychli.


### <a name="limitations"></a>Omezení
- Podporuje protokoly, ale ne metriky.
- Žádná Integrace Azure Řídicí panely a modely nejde spravovat prostřednictvím Azure Resource Manager.
- Aby bylo možné konfigurovat výsledky dotazu, je nutné je importovat do modelu Power BI. Omezení velikosti a aktualizace výsledku
- Aktualizace dat s omezeným počtem osmi časů za den.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) je otevřená platforma, kterou aplikace Excel v provozních řídicích panelech. Je zvláště užitečné pro zjišťování a izolaci provozních incidentů a jejich třídění. Do předplatného Azure můžete přidat [modul plug-in Grafana Azure monitor dat](platform/grafana-plugin.md) , který bude vizualizovat vaše data metrik Azure.

![Snímek obrazovky ukazuje vizualizace Grafana.](media/visualizations/grafana.png)

### <a name="advantages"></a>Výhody
- Bohatá vizualizace.
- Bohatý ekosystém zdrojů dat.
- Interakce dat včetně přiblížení.
- Podporuje parametry.

### <a name="limitations"></a>Omezení
- Žádná Integrace Azure Řídicí panely a modely nejde spravovat prostřednictvím Azure Resource Manager.
- Náklady na podporu další infrastruktury Grafana nebo dalších nákladů pro Cloud Grafana


## <a name="build-your-own-custom-application"></a>Sestavení vlastní aplikace
K datům v protokolech a datech metrik v Azure Monitor prostřednictvím rozhraní API můžete přistupovat pomocí libovolného klienta REST, který umožňuje vytvářet vlastní weby a aplikace.

### <a name="advantages"></a>Výhody
- Kompletní flexibilita v uživatelském rozhraní, vizualizaci, interaktivitě a funkcích.
- Kombinovat metriky a data protokolů s ostatními zdroji dat.

### <a name="disadvantages"></a>Nevýhody
- Vyžaduje se významné technické úsilí.


## <a name="azure-monitor-views"></a>Zobrazení Azure Monitor

> [!IMPORTANT]
> Zobrazení se v procesu již nepoužívá. Pokyny k převádění zobrazení na sešity najdete v tématu [Průvodce přechodem Azure monitor návrháře zobrazení na sešity](platform/view-designer-conversion-overview.md) .

[Zobrazení v Azure monitor](platform/view-designer.md)  umožňují vytvářet vlastní vizualizace s daty protokolu. Používají se pro [monitorování řešení](insights/solutions.md) k prezentaci shromažďovaných dat.


![Snímek obrazovky zobrazuje dlaždici řešení monitorování kontejnerů a podrobné zobrazení Azure Monitor, které se otevře, když ho vyberete.](media/visualizations/view.png)

### <a name="advantages"></a>Výhody
- Bohatá vizualizace pro data protokolu.
- Export a import zobrazení pro přenos do jiných skupin prostředků a předplatných.
- Integruje se do modelu správy Azure Monitor s pracovními prostory a monitorovacími řešeními.
- [Filtry](platform/view-designer-filters.md) pro vlastní parametry.
- Interactive, podporuje přechod na více úrovní (zobrazení, které se podrobněji rozchází do jiného zobrazení)

### <a name="limitations"></a>Omezení
- Podporuje protokoly, ale ne metriky.
- Žádná osobní zobrazení. K dispozici všem uživatelům s přístupem k pracovnímu prostoru.
- Bez automatické aktualizace.
- Omezené možnosti rozložení.
- Žádná podpora pro dotazování napříč několika pracovními prostory nebo Application Insights aplikacemi.
- Dotazy jsou omezené na velikost odpovědi na 8MB a čas provedení dotazu 110 sekund.

## <a name="next-steps"></a>Další kroky
- Seznamte se s [daty shromažďovanými nástrojem Azure monitor](platform/data-platform.md).
- Přečtěte si o [řídicích panelech Azure](../azure-portal/azure-portal-dashboards.md).
- Přečtěte si o [zobrazeních v Azure monitor](platform/view-designer.md).
- Seznamte se se [sešity](./platform/workbooks-overview.md).
- Přečtěte si informace o [importu dat protokolu do Power BI](./platform/powerbi.md).
- Seznamte se s [modulem plug-in zdroje dat Grafana Azure monitor](./platform/grafana-plugin.md).

