---
title: Azure Monitor pro Azure cache pro Redis (Preview) | Microsoft Docs
description: Tento článek popisuje funkci Azure Monitor for Azure Redis Cache, která poskytuje vlastníkům mezipaměti rychlé porozumění problémům s výkonem a využitím.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: a6a8499a13e5c14869f9c9063528cea4ee82f419
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650427"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Prozkoumejte Azure Monitor pro Azure cache pro Redis (Preview)

Pro všechny vaše mezipaměti Azure pro prostředky Redis poskytuje Azure Monitor pro Azure cache pro Redis (Preview) jednotný a interaktivní pohled na:

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

- **Přizpůsobení** tohoto prostředí je tvořené základem Azure monitor šablonami sešitu. Prostředí umožňuje změnit, jaké metriky se zobrazí, a upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům. Změny můžete uložit ve vlastním sešitu a potom připnout grafy sešitů do řídicích panelů Azure.

Tato funkce nevyžaduje, abyste povolili nebo nenakonfigurovali cokoli. Ve výchozím nastavení se shromažďují informace o službě Azure cache pro Redis.

>[!NOTE]
>Pro přístup k této funkci se neúčtují žádné poplatky. Účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Zobrazení metrik využití a výkonu pro Azure cache pro Redis

Pokud chcete zobrazit využití a výkon účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyhledejte **monitorování**a vyberte **monitorování**.

    ![Vyhledávací pole se slovem "monitor" a výsledkem hledání služeb, které zobrazuje "monitor" se symbolem rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

1. Vyberte **Azure cache pro Redis (Preview)**. Pokud tato možnost není k dispozici, vyberte **Další**  >  **mezipaměť Azure pro Redis**.

### <a name="overview"></a>Přehled

V **přehledu**se v tabulce zobrazuje interaktivní mezipaměť Azure pro metriky Redis. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

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
- **Příkazy Set**

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

Úplný seznam definic metrik, které tvoří tyto sešity, najdete v [článku o dostupných metrikách a intervalech generování sestav](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Připnout, exportovat a rozbalit

Pokud chcete na [řídicím panelu Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)připnout libovolný oddíl metriky, vyberte symbol připínáček v pravém horním rohu.

![Oddíl metriky se zvýrazněným symbolem připínáčku](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu aplikace Excel, vyberte symbol šipky dolů nalevo od symbolu připínáčku.

![Zvýrazněný symbol exportu a sešitu](./media/cosmosdb-insights-overview/export.png)

Chcete-li rozbalit nebo sbalit všechna zobrazení v sešitu, vyberte symbol rozbalení vlevo od symbolu exportu.

![Zvýrazněný symbol rozbalovacího sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Přizpůsobení Azure Monitor pro Azure cache pro Redis (Preview)

Vzhledem k tomu, že se toto prostředí sestavuje základem Azure monitor šablon sešitu, můžete vybrat **přizpůsobení**  >  **Úpravy**  >  **Uložit** a uložit kopii změněné verze do vlastního sešitu.

![Panel příkazů s zvýrazněným přizpůsobením](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají v rámci skupiny prostředků v části **Mé sestavy** nebo ve **sdílených sestavách** . **Mé sestavy** je k dispozici pouze pro vás. **Sdílené sestavy** jsou k dispozici všem uživatelům, kteří mají přístup ke skupině prostředků.

Až uložíte vlastní sešit, otevřete ho tak, že přejdete do galerie sešitů.

![Panel příkazů se zvýrazněnou galerií](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [výstrahy metrik](../platform/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications.md) , abyste nastavili automatizované výstrahy, které pomáhají při zjišťování problémů.

* Přečtěte si o scénářích, které sešit podporuje, jak vytvářet a přizpůsobovat sestavy, a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../app/usage-workbooks.md).
