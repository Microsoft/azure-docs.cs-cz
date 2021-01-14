---
title: Azure Monitor pro Azure cache pro Redis | Microsoft Docs
description: Tento článek popisuje funkci Azure Monitor for Azure Redis Cache, která poskytuje vlastníkům mezipaměti rychlé porozumění problémům s výkonem a využitím.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 35f575410caa7a8a8ffbda35bdfa876418db8945
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210371"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Prozkoumejte Azure Monitor pro Azure cache pro Redis

Pro všechny vaše mezipaměti Azure pro prostředky Redis poskytuje Azure Monitor pro Azure cache pro Redis jednotný a interaktivní pohled na:

- Celkový výkon
- Selhání
- Kapacita
- Provozní stav

Tento článek vám pomůže pochopit výhody tohoto nového monitorovacího prostředí. Také ukazuje, jak upravit a přizpůsobit prostředí podle jedinečných potřeb vaší organizace.

## <a name="introduction"></a>Úvod

Než začnete, měli byste porozumět tomu, jak Azure Monitor pro Azure cache pro Redis vizuálně prezentují informace.

Poskytuje:

- V **perspektivě škálované** mezipaměti Azure pro prostředky Redis v jednom umístění napříč všemi vašimi předplatnými. Obor lze selektivně omezit pouze na odběry a prostředky, které chcete vyhodnotit.

- **Podrobná analýza** konkrétního prostředku Azure cache pro Redis. Můžete diagnostikovat problémy a zobrazit podrobnou analýzu využití, selhání, kapacity a operací. Výběrem libovolné z těchto kategorií zobrazíte podrobné zobrazení relevantních informací.  

- **Přizpůsobení** tohoto prostředí, které je sestavené základem Azure monitor šablon sešitu. Díky tomu můžete měnit metriky, které se zobrazují, a upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům. Změny můžete uložit ve vlastním sešitu a potom připnout grafy sešitů do řídicích panelů Azure.

Tato funkce nevyžaduje, abyste povolili nebo nenakonfigurovali cokoli. Ve výchozím nastavení se shromažďují informace o službě Azure cache pro Redis.

>[!NOTE]
>Pro přístup k této funkci se neúčtují žádné poplatky. Účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Zobrazení metrik využití a výkonu pro Azure cache pro Redis

Pokud chcete zobrazit využití a výkon účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Vyhledejte **monitorování** a vyberte **monitorování**.

    ![Vyhledávací pole se slovem "monitor" a výsledkem hledání služeb, které zobrazuje "monitor" se symbolem rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

1. Vyberte **mezipaměť Azure pro Redis**. Pokud tato možnost není k dispozici, vyberte **Další**  >  **mezipaměť Azure pro Redis**.

### <a name="overview"></a>Přehled

V **přehledu** se v tabulce zobrazuje interaktivní mezipaměť Azure pro metriky Redis. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

- **Předplatná**: seznam obsahuje jenom odběry, které mají prostředek Azure cache for Redis.  

- **Mezipaměť Azure pro Redis**: můžete vybrat vše, podmnožinu nebo jednu mezipaměť Azure pro prostředek Redis.

- **Časový rozsah**: ve výchozím nastavení tabulka zobrazuje poslední čtyři hodiny informace na základě odpovídajících výběrů.

V rozevíracích seznamech je dlaždice počítadla. V této dlaždici se zobrazuje celkový počet prostředků Azure cache pro Redis ve vybraných předplatných. Podmíněné kódy barev nebo Heat mapy pro sloupce sešitu sestavy metrik transakcí. Hluboká barva představuje nejvyšší hodnotu. Světlejší barvy označují nižší hodnoty.

Výběrem šipky rozevíracího seznamu vedle jedné z položek Azure cache for Redis se odhalí rozpis metrik výkonu na úrovni jednotlivých prostředků.

![Snímek obrazovky s přehledem](./media/redis-cache-insights-overview/overview.png)

Když vyberete položku mezipaměť Azure pro název prostředku Redis zvýrazněnou modrou, zobrazí se výchozí tabulka **přehledu** pro příslušný účet. Zobrazuje tyto sloupce:

- **Využitá paměť**
- **Využitá paměť v procentech**
- **Zatížení serveru**
- **Časová osa zatížení serveru**
- **Procesor**
- **Připojení klienti**
- **Neúspěšné přístupy do mezipaměti**
- **Chyby (max.)**

### <a name="operations"></a>Operace

Když vyberete **operace** v horní části stránky, otevře se tabulka **operace** v šabloně sešitu. Zobrazuje tyto sloupce:

- **Celkový počet operací**
- **Časová osa celkového počtu operací**
- **Operace za sekundu**
- **Příkazy Get**
- **Sady**

![Snímek obrazovky s provozním prostředím](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Využití

Když vyberete možnost **použití** v horní části stránky, otevře se tabulka **využití** šablony sešitu. Zobrazuje tyto sloupce:

- **Čtení z mezipaměti**
- **Časová osa čtení z mezipaměti**
- **Zápis do mezipaměti**
- **Přístupy do mezipaměti**
- **Neúspěšné přístupy do mezipaměti**

![Snímek obrazovky se zkušenostmi s používáním](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>Selhání

Když v horní části stránky vyberete **selhání** , otevře se tabulka **selhání** šablony sešitu. Zobrazuje tyto sloupce:

- **Celkový počet chyb**
- **Převzetí služeb při selhání/chyby**
- **UnresponsiveClient/chyby**
- **RDB/chyby**
- **AOF/chyby**
- **Export/chyby**
- **Dataztráta/chyby**
- **Import/chyby**

![Snímek obrazovky s chybami s rozpisem podle typu požadavku HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definice metriky

Úplný seznam definic metrik, které tvoří tyto sešity, najdete v [článku o dostupných metrikách a intervalech generování sestav](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Zobrazení z mezipaměti Azure pro prostředek Redis

Přístup k Azure Monitor pro Azure cache pro Redis přímo z jednotlivého prostředku:

1. V Azure Portal vyberte mezipaměť Azure pro Redis.

2. V seznamu vyberte jednotlivou mezipaměť Azure pro prostředek Redis. V části monitorování vyberte přehledy.

    ![Snímek obrazovky s možnostmi "přehledy", které jsou zvýrazněny v červeném poli](./media/redis-cache-insights-overview/insights.png)

Tato zobrazení jsou přístupná taky tak, že v sešitu na úrovni Azure Monitor vyberete název prostředku mezipaměti Azure pro prostředek Redis.

### <a name="resource-level-overview"></a>Přehled na úrovni prostředků

V sešitě s **přehledem** pro Azure Redis Cache se zobrazuje několik metrik výkonu, které vám umožní přístup k:

- Interaktivní grafy výkonu zobrazující nejdůležitější podrobnosti týkající se Azure cache pro Redis výkon.

- Metriky a stavové dlaždice zvýrazňují horizontálních oddílů výkon, celkový počet připojených klientů a celkovou latenci.

![Snímek obrazovky s přehledem řídicího panelu, který zobrazuje informace o výkonu procesoru, využité paměti, připojených klientech, chybách, klíčích s prošlými klíči](./media/redis-cache-insights-overview/resource-overview.png)

Výběr kterékoli z ostatních karet pro **výkon** nebo **operace** otevře příslušné sešity.

### <a name="resource-level-performance"></a>Výkon na úrovni prostředku

![Snímek obrazovky s grafy výkonu prostředků](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>Operace na úrovni prostředku

![Snímek obrazovky s grafy operací prostředků](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>Připnout, exportovat a rozbalit

Pokud chcete na [řídicím panelu Azure](../../azure-portal/azure-portal-dashboards.md)připnout libovolný oddíl metriky, vyberte symbol připínáček v pravém horním rohu.

![Oddíl metriky se zvýrazněným symbolem připínáčku](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu aplikace Excel, vyberte symbol šipky dolů nalevo od symbolu připínáčku.

![Zvýrazněný symbol exportu a sešitu](./media/cosmosdb-insights-overview/export.png)

Chcete-li rozbalit nebo sbalit všechna zobrazení v sešitu, vyberte symbol rozbalení vlevo od symbolu exportu.

![Zvýrazněný symbol rozbalovacího sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Přizpůsobení Azure Monitor pro Azure cache pro Redis

Vzhledem k tomu, že se toto prostředí sestavuje základem Azure monitor šablon sešitu, můžete vybrat **přizpůsobení**  >  **Úpravy**  >  **Uložit** a uložit kopii změněné verze do vlastního sešitu.

![Panel příkazů s zvýrazněným přizpůsobením](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají v rámci skupiny prostředků v části **Mé sestavy** nebo ve **sdílených sestavách** . **Mé sestavy** je k dispozici pouze pro vás. **Sdílené sestavy** jsou k dispozici všem uživatelům, kteří mají přístup ke skupině prostředků.

Až uložíte vlastní sešit, otevřete ho tak, že přejdete do galerie sešitů.

![Panel příkazů se zvýrazněnou galerií](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokyny k řešení potíží najdete v článku věnovaném [řešení potíží](troubleshoot-workbooks.md)s vyhrazenými sešity na základě sešitu.

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [výstrahy metrik](../platform/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md) , abyste nastavili automatizované výstrahy, které pomáhají při zjišťování problémů.

* Přečtěte si o scénářích, které sešit podporuje, jak vytvářet a přizpůsobovat sestavy, a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../platform/workbooks-overview.md).
