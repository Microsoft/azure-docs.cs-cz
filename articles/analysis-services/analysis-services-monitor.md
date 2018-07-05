---
title: Monitorování metrik serveru Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o monitorování metrik serveru služby Analysis Services na webu Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 608323c467e0106af816c3432dec24090a9a9599
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442929"
---
# <a name="monitor-server-metrics"></a>Monitorování metrik serveru

Analysis Services poskytuje metriky, které vám pomohou při monitorování výkonu a stavu vašich serverů. Například monitorujte paměť a využití procesoru, počtu připojení klientů a spotřeba prostředků dotazu. Analysis Services používá stejné monitorování architektury jako většina ostatních služeb Azure. Další informace najdete v tématu [metriky ve službě Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

K provedení další podrobnější diagnostiku, sledovat výkon a identifikovat trendy v několika prostředcích služby ve skupině prostředků nebo předplatného, použijte [Azure Monitor](https://azure.microsoft.com/services/monitor/). Azure Monitor (služba), nemusí se vám začne fakturovat služba.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Monitorování metrik pro server služby Analysis Services

1. Na webu Azure portal, vyberte **metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V **dostupné metriky**, vyberte metriky, které chcete zahrnout do grafu. 

    ![Monitorování grafu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>
## <a name="server-metrics"></a>Metrik serveru
Tato tabulka slouží k určení, které metriky jsou nejvhodnější pro váš scénář monitorování. Jenom metriky se stejnými jednotkami mohou být zobrazeny ve stejném grafu.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Délka fronty fondu úloh příkazu|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů.|
|CurrentConnections|Připojení: Aktuální počet připojení|Počet|Průměr|Aktuální počet navázaných připojení klientů.|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet navázaných uživatelských relací.|
|mashup_engine_memory_metric|Paměť modulu M|B|Průměr|Využití paměti procesy modulu mashupu|
|mashup_engine_qpu_metric|QPU modulu M|Počet|Průměr|Využití QPU procesy modulu mashupu|
|memory_metric|Memory (Paměť)|B|Průměr|Paměť. V rozsahu 0-25 GB pro S1, 0 – 50 GB pro S2 a 0 – 100 GB pro S4|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrný thrashing paměti.|
|CleanerCurrentPrice|Paměť: Aktuální cena čisticího modulu|Počet|Průměr|Aktuální cena paměti a $/ bajt/čas, normalizovaná na 1000.|
|CleanerMemoryNonshrinkable|Paměť: Nezmenšitelná paměť|B|Průměr|Velikost paměti v bajtech, která není v souladu s čisticí vyprazdňování procesem na pozadí.|
|CleanerMemoryShrinkable|Paměť: Zmenšitelná paměť čisticího modulu|B|Průměr|Velikost paměti v bajtech, která čisticí procesem na pozadí se vyprázdňuje.|
|MemoryLimitHard|Paměť: Limit paměti – pevná|B|Průměr|Limit pevné paměti, z konfiguračního souboru.|
|Hodnota MemoryLimitHigh|Paměť: Limit paměti – vysoká|B|Průměr|Limit vysoké paměti, z konfiguračního souboru.|
|MemoryLimitLow|Paměť: Limit paměti – nízká|B|Průměr|Limit nízké paměti, z konfiguračního souboru.|
|MemoryLimitVertiPaq|Paměť: Limit paměti – VertiPaq|B|Průměr|Limit v paměti, z konfiguračního souboru.|
|Parametru MemoryUsage|Paměť: Využití paměti|B|Průměr|Využití paměti procesu serveru, jak se používají při výpočtu cena čisticího modulu paměti. Rovnat čítači Process\PrivateBytes plus velikost dat mapovaných do paměti, ignoruje se jakákoli paměť, která byla mapována nebo přidělena modulem analýzu v paměti (VertiPaq) překračující Limit paměti modulu.|
|Kvóta|Paměť: kvóta|B|Průměr|Aktuální kvóta paměti, v bajtech. Kvóta paměti se taky říká rezervace paměti grant nebo paměti.|
|QuotaBlocked|Paměť: Kvóta – blokováno|Počet|Průměr|Aktuální počet požadavků kvóty, které jsou blokovány, dokud jsou uvolněny jiné kvóty paměti.|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|B|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|
|VertiPaqPaged|Paměť: VertiPaq stránkované|B|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty fondu úloh zpracování|Počet|Průměr|Počet úloh bez vstupně-ve frontě fondu vláken zpracování.|
|RowsConvertedPerSec|Zpracování: Řádky převést za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: Počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0 až 100 pro S1, 0 až 200 pro S2 a 0 až 400 pro S4|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|
|SuccessfullConnectionsPerSec|Úspěšná spojení za sekundu|CountPerSecond|Průměr|Míra úspěšně navázaných spojení.|
|CommandPoolBusyThreads|Vlákna: Zaneprázdněná vlákna fondu příkazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|
|CommandPoolIdleThreads|Vlákna: Nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|
|LongParsingBusyThreads|Vlákna: Zaneprázdněná vlákna dlouhého parsování|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého parsování.|
|LongParsingIdleThreads|Vlákna: Nečinná vlákna dlouhého parsování|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouhého parsování.|
|LongParsingJobQueueLength|Vlákna: Dlouhého parsování délka fronty úloh|Počet|Průměr|Počet úloh ve frontě fondu vláken dlouhého parsování.|
|ProcessingPoolIOJobQueueLength|Vláken: Vstupně-výstupních operací délka fronty úloh fondu zpracování|Počet|Průměr|Počet vstupně-výstupních operací úloh ve frontě fondu vláken zpracování.|
|ProcessingPoolBusyIOJobThreads|Vlákna: Zaneprázdněná vlákna úloh vstupně-výstupní operace fondu zpracování|Počet|Průměr|Počet vláken ve fondu vláken zpracování spuštění vstupně-výstupních operací úloh.|
|ProcessingPoolBusyNonIOThreads|Vlákna: Zaneprázdněná vlákna jiných vstupně-fondu zpracování|Počet|Průměr|Počet vláken, spouštění úloh bez vstupně-ve fondu vláken zpracování.|
|ProcessingPoolIdleIOJobThreads|Vlákna: Nečinná vlákna úloh vstupně-výstupní operace fondu zpracování|Počet|Průměr|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|
|ProcessingPoolIdleNonIOThreads|Vlákna: Nečinná vlákna jiných vstupně-fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeném pro úlohy bez vstupně.|
|QueryPoolIdleThreads|Vlákna: Nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|
|QueryPoolJobQueueLength|Vlákna: Dotazování délka fronty fondu úloh|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|
|ShortParsingBusyThreads|Vlákna: Zaneprázdněná vlákna krátkého parsování|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken krátkého parsování.|
|ShortParsingIdleThreads|Vlákna: Nečinná vlákna krátkého parsování|Počet|Průměr|Počet nečinných vláken ve fondu vláken krátkého parsování.|
|ShortParsingJobQueueLength|Vlákna: Krátkého parsování délka fronty úloh|Počet|Průměr|Počet úloh ve frontě fondu vláken krátkého parsování.|
|TotalConnectionFailures|Celkem nezdařených spojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení.|
|TotalConnectionRequests|Celkem žádostí o spojení|Počet|Průměr|Celkem žádostí o spojení. |

## <a name="next-steps"></a>Další postup
[Monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metriky ve službě Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metriky ve službě Azure Monitor, rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)