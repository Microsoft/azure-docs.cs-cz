---
title: Vizualizace dat z Azure Monitoru | Dokumenty společnosti Microsoft
description: Obsahuje souhrn dostupných metod pro vizualizaci dat metrik a protokolů uložených v Azure Monitoru.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 877616f6fd31bdfbe193bd8f03efb3f79317ad42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535365"
---
# <a name="visualizing-data-from-azure-monitor"></a>Vizualizace dat z Azure Monitoru
Tento článek obsahuje souhrn dostupných metod pro vizualizaci dat protokolu a metriky uložených v Azure Monitoru.

Vizualizace, jako jsou grafy a grafy, vám mohou pomoci analyzovat data monitorování, abyste mohli přejít k podrobnostem o problémech a identifikovat vzory. V závislosti na nástroji, který používáte, můžete mít také možnost sdílet vizualizace s ostatními uživateli ve vaší organizaci i mimo ni.

## <a name="workbooks"></a>Workbooks
[Sešity](../azure-monitor/app/usage-workbooks.md) jsou interaktivní dokumenty, které poskytují podrobné informace o vašich datech, vyšetřování a spolupráci uvnitř týmu. Konkrétní příklady, kde jsou užitečné sešity, jsou průvodce odstraňováním problémů a incident posmrtně.

![sešit](media/visualizations/workbook.png)

### <a name="advantages"></a>Výhody
- Podporuje metriky a protokoly.
- Podporuje parametry umožňující interaktivní sestavy, kde výběr prvku v tabulce dynamicky aktualizuje přidružené grafy a vizualizace.
- Tok podobný dokumentu.
- Možnost pro osobní nebo sdílené sešity
- Snadné, kolaborativní vytváření.
- Šablony podporují veřejnou galerii šablon založenou na GitHubu.

### <a name="limitations"></a>Omezení
- Žádná automatická aktualizace.
- Žádné husté rozložení jako řídicí panely, díky nimž jsou sešity méně užitečné jako jediné skleněné tabule. Určeno spíše pro hlubší vhled.


## <a name="azure-dashboards"></a>Řídicí panely Azure
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) jsou primární technologie řídicího panelu pro Azure. Jsou obzvláště užitečné při poskytování jednotného podokna skla nad infrastrukturou a službami Azure, které vám umožní rychle identifikovat důležité problémy.

![Řídicí panel](media/visualizations/dashboard.png)

### <a name="advantages"></a>Výhody
- Hluboká integrace do Azure. Vizualizace se dají připnout na řídicí panely z více stránek Azure, včetně Průzkumníka metrik, Analýzy protokolů a Přehledů aplikací.
- Podporuje metriky a protokoly.
- Kombinujte data z více zdrojů, včetně výstupu z [průzkumníka metrik](platform/metrics-charts.md), [protokolových dotazů](log-query/log-query-overview.md)a [map](app/app-map.md) a dostupnosti v Application Insights.
- Možnost pro osobní nebo sdílené řídicí panely. Integrované s ověřováním na základě rolí Azure [(RBAC)](../role-based-access-control/overview.md).
- Automatická aktualizace. Aktualizace metrik závisí na časovém rozsahu s minimálně pěti minutami. Protokoly aktualizovat každou hodinu, s možností ruční aktualizace na vyžádání kliknutím na ikonu "aktualizovat" na dané vizualizaci, nebo aktualizací celého řídicího panelu.
- Panely parametrizovaných metrik s časovým razítkem a vlastními parametry.
- Flexibilní možnosti rozložení.
- Režim celé obrazovky.


### <a name="limitations"></a>Omezení
- Omezená kontrola nad vizualizacemi protokolů bez podpory pro tabulky dat. Celkový počet datových řad je omezen na 10 s dalšími datovými řadami seskupenými pod _jiným_ kontejnerem.
- Žádné vlastní parametry podporují pro protokolgrafy.
- Protokolové grafy jsou omezeny na posledních 30 dní.
- Protokolové grafy lze připnout pouze ke sdíleným řídicím panelům.
- Žádná interaktivita s daty řídicího panelu.
- Omezené kontextové podrobné informace.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) je obzvláště užitečný pro vytváření řídicích panelů a sestav zaměřených na podnikání a také pro sestavy analyzující dlouhodobé trendy klíčových ukazatelů výkonu. Výsledky [dotazu protokolu](platform/powerbi.md) můžete importovat do datové sady Power BI, abyste mohli využívat jeho funkce, jako je kombinování dat z různých zdrojů a sdílení sestav na webu a mobilních zařízeních.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace.
- Rozsáhlá interaktivita včetně zoom-in a křížové filtrování.
- Snadné sdílení v celé organizaci.
- Integrace s dalšími daty z více zdrojů dat.
- Lepší výkon s výsledky uloženými v datové krychli.


### <a name="limitations"></a>Omezení
- Podporuje protokoly, ale ne metriky.
- Žádná integrace Azure. Pomocí Azure Resource Manageru nelze spravovat řídicí panely a modely.
- Výsledky dotazu je třeba importovat do modelu Power BI, aby bylo možné je nakonfigurovat. Omezení velikosti výsledku a aktualizace.
- Omezená aktualizace dat osmkrát denně.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) je otevřená platforma, která vyniká v provozních řídicích panelech. To je zvláště užitečné pro detekci a izolaci a třídění provozních incidentů. Do předplatného Azure můžete přidat plugin pro [zdroje dat Grafana Azure Monitor,](platform/grafana-plugin.md) aby si ho mohl představit.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace.
- Bohatý ekosystém zdrojů dat.
- Interaktivita dat včetně přiblížení.
- Podporuje parametry.

### <a name="limitations"></a>Omezení
- Žádná integrace Azure. Pomocí Azure Resource Manageru nelze spravovat řídicí panely a modely.
- Náklady na podporu dodatečné infrastruktury Grafana nebo dodatečné náklady pro Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Vytvořte si vlastní aplikaci
K datům v protokolu a metrikách v Azure Monitoru můžete přistupovat prostřednictvím jejich rozhraní API pomocí libovolného klienta REST, který vám umožní vytvářet vlastní weby a aplikace.

### <a name="advantages"></a>Výhody
- Úplná flexibilita v oblasti ui, vizualizace, interaktivity a funkcí.
- Zkombinujte metriky a data protokolu s jinými zdroji dat.

### <a name="disadvantages"></a>Nevýhody
- Je zapotřebí značného technického úsilí.


## <a name="azure-monitor-views"></a>Zobrazení monitoru Azure

> [!IMPORTANT]
> Zobrazení jsou v procesu zastaralosti. Pokyny k převodu zobrazení na sešity najdete v [tématu Návrhář zobrazení Azure Monitor u sešitů.](platform/view-designer-conversion-overview.md)

[Zobrazení ve službě Azure Monitor](platform/view-designer.md) umožňují vytvářet vlastní vizualizace s daty protokolu. Používají se [pomocí monitorovacích řešení](insights/solutions.md) k prezentaci údajů, které shromažďují.


![Zobrazení](media/visualizations/view.png)

### <a name="advantages"></a>Výhody
- Bohaté vizualizace pro data protokolu.
- Exportujte a importujte zobrazení a přeneste je do jiných skupin prostředků a předplatných.
- Integruje se do modelu správy Azure Monitoru s pracovními prostory a monitorovacími řešeními.
- [Filtry](platform/view-designer-filters.md) pro vlastní parametry.
- Interaktivní, podporuje víceúrovňové vrtáky (zobrazení, které provrtává do jiného zobrazení)

### <a name="limitations"></a>Omezení
- Podporuje protokoly, ale ne metriky.
- Žádné osobní názory. K dispozici všem uživatelům s přístupem k pracovnímu prostoru.
- Žádná automatická aktualizace.
- Omezené možnosti rozložení.
- Žádná podpora pro dotazování na více pracovních prostorů nebo aplikací Application Insights.
- Dotazy jsou omezeny ve velikosti odpovědi na 8 MB a čas spuštění dotazu 110 sekund.

## <a name="next-steps"></a>Další kroky
- Další informace o [datech shromážděných službou Azure Monitor](platform/data-platform.md).
- Další informace o [řídicích panelech Azure](../azure-portal/azure-portal-dashboards.md).
- Další informace o [zobrazeních v Azure Monitoru](platform/view-designer.md).
- Další informace o [sešitech](../azure-monitor/app/usage-workbooks.md).
- Přečtěte si o [importu dat protokolu do Power BI](../azure-monitor/platform/powerbi.md).
- Další informace o [modulu plug-in zdroje dat Grafana Azure Monitor](../azure-monitor/platform/grafana-plugin.md).

