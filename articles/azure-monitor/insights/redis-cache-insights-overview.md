---
title: Azure Monitor pro Azure cache pro Redis (Preview) | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro funkci Redis Cache, která poskytuje vlastníky Azure cache pro vlastníky Redis s rychlým porozuměním problémům s výkonem a využitím.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210971"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Prozkoumejte Azure Monitor pro Azure cache pro Redis (Preview)

Azure Monitor pro Azure cache pro Redis (Preview) poskytuje přehled o celkovém výkonu, selháních, kapacitě a provozním stavu všech vašich Azure cache pro prostředky Redis v jednotném interaktivním prostředí. Tento článek vám pomůže pochopit výhody tohoto nového prostředí pro monitorování a jak můžete upravit a přizpůsobit prostředí tak, aby vyhovovalo jedinečným potřebám vaší organizace.

## <a name="introduction"></a>Úvod

Předtím, než se začnete do prostředí, byste měli pochopit, jak prezentuje a vizualizují informace.

Poskytuje:

* **V perspektivě škálované** mezipaměti Azure pro prostředky Redis napříč všemi vašimi předplatnými v jednom místě s možností selektivně přidružit jenom k předplatným a prostředkům, které vás zajímají.

* Procházejte **analýzu** konkrétního prostředku Azure cache pro Redis, která vám pomůžou diagnostikovat problémy nebo provádět podrobnou analýzu pomocí kategorií, selhání, kapacity a operací. Výběrem některé z těchto možností získáte Podrobný pohled na relevantní.  

* **Přizpůsobitelné** – toto prostředí je postavené na šablonách sešitu Azure monitor, což vám umožní změnit, jaké metriky se zobrazí, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a pak je Uložit do vlastního sešitu. Grafy v sešitech je pak možné připnout na řídicí panely Azure.  

Tato funkce nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tato služba Azure cache pro Redis se ve výchozím nastavení shromáždí.

>[!NOTE]
>Pro přístup k této funkci se neplatí žádné poplatky a účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Zobrazení metrik využití a výkonu pro Azure cache pro Redis

Pokud chcete zobrazit využití a výkon účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **monitorování** a vyberte **monitor**.

    ![Vyhledávací pole se slovem "monitor" a rozevírací seznam, který říká službám "monitor" s obrázkem stylu rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

3. Vyberte **Azure cache pro Redis (Preview)**.

Pokud tato možnost není k dispozici, klikněte na **Další** a vyberte **Azure cache pro Redis**.

### <a name="overview"></a>Přehled

V **přehledu**se v tabulce zobrazuje interaktivní mezipaměť Azure pro metriky Redis. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* V seznamu **předplatných** jsou uvedena pouze předplatná, která mají prostředek Azure cache pro Redis.  

* **Mezipaměť Azure pro Redis** – můžete vybrat vše, podmnožinu nebo jednu mezipaměť Azure pro prostředek Redis.

* **Časový rozsah** – ve výchozím nastavení zobrazí poslední 4 hodiny informací na základě odpovídajících výběrů.

Dlaždice počítadla v rozevíracích seznamech shrnuje celkový počet Azure cache pro prostředky Redis ve vybraných předplatných. Pro sloupce v sešitu jsou podmíněné barevné kódování nebo Heat mapy, které vykazují metriky transakcí. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách.

Výběrem šipky rozevíracího seznamu vedle jedné z položek Azure cache for Redis se odhalí rozpis metrik výkonu na úrovni jednotlivých prostředků:

![Snímek obrazovky s přehledem](./media/redis-cache-insights-overview/overview.png)

Výběrem možnosti pro název prostředku Azure cache for Redis zvýrazněné modře přejdete k výchozímu **přehledu** pro příslušný účet. Zobrazuje `Used Memory` , `Used Memory Percentage` ,, `Server Load` `Server Load Timeline` , `CPU` , `Connected Clients` , `Cache Misses` , `Errors (Max)` .

### <a name="operations"></a>Operace

V horní části stránky vyberte **operace** a otevře se část **operace** v šabloně sešitu. Zobrazuje `Total Operations` , `Total Operations Timeline` ,, `Operations Per Second` `Gets` , `Sets` .

![Snímek obrazovky s přehledem](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Využití

V horní části stránky vyberte **využití** a otevře se část **použití** šablony sešitu. Zobrazuje `Cache Read` , `Cache Read Timeline` ,, `CacheWrite` `CacheHits` , `Cache Misses` .

![Snímek obrazovky s přehledem](./media/redis-cache-insights-overview/usage.png)

V horní části stránky vyberte **selhání** a otevře se část **selhání** šablony sešitu. Zobrazuje `Total Errors` , `Failover/Errors` ,, `UnresponsiveClient/Errors` `RDB/Errors` , `AOF/Errors` , `Export/Errors` , `Dataloss/Errors` , `Import/Errors` .

![Snímek obrazovky s chybami s rozpisem podle typu požadavku HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definice metriky

Úplný seznam definic metrik, které tvoří tyto sešity, najdete v [článku dostupné metriky a intervaly vytváření sestav](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Připnout, exportovat a rozbalit

Libovolný oddíl metriky můžete připnout na [řídicí panel Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Připnout oddíl metriky na příklad řídicího panelu](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu aplikace Excel, vyberte ikonu šipky dolů nalevo od ikony připínáčku.

![Ikona exportovat sešit](./media/cosmosdb-insights-overview/export.png)

Chcete-li rozbalit nebo sbalit všechna rozevírací zobrazení v sešitu, vyberte ikonu rozbalení vlevo od ikony exportu:

![Ikona rozbalení sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Přizpůsobení Azure Monitor pro Azure cache pro Redis (Preview)

Vzhledem k tomu, že toto prostředí je postavené na šablonách sešitu Azure monitor, máte možnost **přizpůsobit**  >  **Úpravy** a **Uložit** kopii změněné verze do vlastního sešitu. 

![Přizpůsobit pruh](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají v rámci skupiny prostředků, a to buď v části **Mé sestavy** , která je pro vás nebo ve **sdílených sestavách** přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Spustit galerii sešitů z panelu příkazů](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [Upozornění metrik](../platform/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications.md) , abyste nastavili automatizované upozorňování na pomoc při zjišťování problémů.

* Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../app/usage-workbooks.md).
