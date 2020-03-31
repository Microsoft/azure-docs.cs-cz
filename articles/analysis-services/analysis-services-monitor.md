---
title: Monitorování metrik serveru Služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak Analysis Services používá Azure Metrics Explorer, bezplatný nástroj na portálu, který vám pomůže sledovat výkon a stav vašich serverů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252802"
---
# <a name="monitor-server-metrics"></a>Monitorování metrik serveru

Analysis Services poskytuje metriky v Průzkumníku metrik Azure, bezplatný nástroj na portálu, který vám pomůže sledovat výkon a stav vašich serverů. Například monitorování využití paměti a procesoru, počet připojení klientů a spotřeba prostředků dotazu. Analysis Services používá stejné rozhraní monitorování jako většina ostatních služeb Azure. Další informace najdete [v tématu Začínáme s Průzkumníkem metrik Azure](../azure-monitor/platform/metrics-getting-started.md).

Chcete-li provádět podrobnější diagnostiku, sledovat výkon a identifikovat trendy napříč různými prostředky služeb ve skupině prostředků nebo předplatném, použijte [Azure Monitor](../azure-monitor/overview.md). Azure Monitor (služba) může mít za následek fakturovatelné služby.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Sledování metrik pro server Analysis Services

1. Na webu Azure Portal vyberte **Metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V **části Metrika**vyberte metriky, které chcete do grafu zahrnout. 

    ![Graf monitorování](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriky serveru

V této tabulce můžete určit, které metriky jsou nejvhodnější pro váš scénář monitorování. Ve stejném grafu lze zobrazit pouze metriky stejné jednotky.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|Délka fronty commandpoolu|Délka fronty úlohy fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů.|
|CurrentConnections|Připojení: Aktuální připojení|Počet|Průměr|Aktuální počet navázání připojení klientů.|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet vytvořených uživatelských relací.|
|mashup_engine_memory_metric|M Paměť motoru|Bajty|Průměr|Využití paměti procesy mashup motoru|
|mashup_engine_qpu_metric|M Motor QPU|Počet|Průměr|Využití QPU procesy mashup motoru|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná paměť výprask.|
|CleanerCurrentCena|Paměť: Čistší aktuální cena|Počet|Průměr|Aktuální cena paměti, $/bajt/čas, normalizována na 1000.|
|CleanerMemoryNeshrinkable|Paměť: Čistší paměť nesmrštná|Bajty|Průměr|Množství paměti v bajtů, které nejsou předmětem čištění čističem pozadí.|
|CleanerMemoryShrinkable|Paměť: Čistší paměť smrštitelná|Bajty|Průměr|Množství paměti v bajtů, které podléhají vymazání čističem pozadí.|
|MemoryLimitHard|Paměť: Limit paměti pevný|Bajty|Průměr|Pevný limit paměti, z konfiguračního souboru.|
|MemoryLimitHigh|Paměť: Maximální limit paměti|Bajty|Průměr|Vysoký limit paměti z konfiguračního souboru.|
|MemoryLimitLow|Paměť: Nedostatek limitu paměti|Bajty|Průměr|Nízký limit paměti z konfiguračního souboru.|
|PaměťLimitVertiPaq|Paměť: Limit paměti VertiPaq|Bajty|Průměr|Limit v paměti z konfiguračního souboru.|
|Využití paměti|Paměť: Využití paměti|Bajty|Průměr|Využití paměti procesu serveru, jak je použito při výpočtu čistší ceny paměti. Rovno čítač Process\PrivateBytes plus velikost dat mapovaných v paměti, ignoruje všechny paměti, která byla mapována nebo přidělena modulu analýzy v paměti (VertiPaq) nad limit paměti motoru.|
|private_bytes_metric|Soukromé bajty |Bajty|Průměr|Celkové množství paměti procesu modulu Analysis Services a mashup kontejneru procesy přiděleny, bez paměti sdílené s jinými procesy.|
|virtual_bytes_metric|Virtuální bajty |Bajty|Průměr|Aktuální velikost virtuálního adresového prostoru, který proces modulu Analysis Services a procesy kontejneru Mashup používají.|
|mashup_engine_private_bytes_metric|Soukromé bajty motoru M |Bajty|Průměr|Celkové množství paměti Mashup kontejneru procesy byly přiděleny, bez paměti sdílené s jinými procesy.|
|mashup_engine_virtual_bytes_metric|M Virtuální bajty motoru |Bajty|Průměr|Aktuální velikost procesů virtuálního adresového prostoru Mashup kontejneru používají.|
|Kvóta|Paměť: Kvóta|Bajty|Průměr|Aktuální kvóta paměti v bajtech. Kvóta paměti se také označuje jako udělení paměti nebo rezervace paměti.|
|Kvóta blokována|Paměť: Kvóta byla zablokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nejsou uvolněny jiné kvóty paměti.|
|VertiPaqNonpaged|Paměť: VertiPaq Nonpaged|Bajty|Průměr|Bajty paměti uzamčeny v pracovní sadě pro použití v modulu v paměti.|
|VertiPaqPaged|Paměť: VertiPaq Stránkované|Bajty|Průměr|Bajty stránkované paměti, které se používají pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty úlohy fondu pro zpracování|Počet|Průměr|Počet úloh bez vstupně-va ve frontě fondu vláken zpracování.|
|RowsConvertedPerSec|Zpracování: Řádky převedené za sekundu|CountPerSecond|Průměr|Rychlost řádků převedených během zpracování.|
|RowsReadPerSec|Zpracování: Řádky přečtené za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: Řádky napsané za sekundu|CountPerSecond|Průměr|Rychlost řádků zapsaných během zpracování.|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|
|Podprocesy QueryPoolBusy|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazu.|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Rychlost úspěšného dokončení připojení.|
|Podprocesy CommandPoolBusy|Vlákna: Podprocesy zaneprázdněné příkazy|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|
|Příkazy Pročinné a|Vlákna: Nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|
|LongParsingBusyVlákna|Vlákna: Dlouhá analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve fondu dlouhých analyzátorů.|
|LongParsingIdleThreads|Závity: Dlouhá analýza nečinných závitů|Počet|Průměr|Počet nečinných vláken ve fondu dlouhých analyzujících vláken.|
|Délka fronty|Vlákna: Délka fronty úloh dlouhé analýzy|Počet|Průměr|Počet úloh ve frontě fondu dlouhých analyzátorů vláken.|
|ProcessingPoolIOJobQueueLength|Vlákna: Délka fronty úloh vstupně-v.i.a. fondu|Počet|Průměr|Počet vstupně-v.i.,v.i.,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,|
|ProcessingPoolBusyIOJobThreads|Vlákna: Zpracování fondu zaneprázdněnvstupně-v.,V úlohy podprocesů|Počet|Průměr|Počet podprocesů spuštěných vstupně-v., které jsou ve fondu vláken zpracování vláken.|
|ProcessingPoolBusyNonIOThreads|Vlákna: Zpracování fondu zaneprázdněné vlákny, která nejsou vstupně-vstupně-va.|Počet|Průměr|Počet podprocesů, na kterých jsou ve fondu vláken zpracování vláken spuštěny úlohy bez vstupně-videa.|
|ProcessingPoolIdleIOJobThreads|Vlákna: Zpracování nečinných vstupně-vi úloh fondu|Počet|Průměr|Počet nečinných podprocesů pro vstupně-tovitých úloh ve fondu vláken zpracování.|
|ProcessingPoolIdleNonIOThreads|Vlákna: Zpracování nečinnosti fondu bez vstupně-v/V podprocesů|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazených pro úlohy bez vstupně-v..|
|QueryPoolIdleThreads|Vlákna: Nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných podprocesů pro vstupně-tovitých úloh ve fondu vláken zpracování.|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|
|ShortParsingBusyVlákna|Vlákna: Krátká analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve fondu krátkých analyzátorů.|
|ShortParsingIdleThreads|Závity: Krátká analýza nečinných závitů|Počet|Průměr|Počet nečinných vláken ve fondu krátkých analyzátorů.|
|ShortParsingJobQueueLength|Vlákna: Délka fronty úlohy krátké analýzy|Počet|Průměr|Počet úloh ve frontě fondu krátkých analyzátorů vláken.|
|Selhání totalconnections|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení.|
|TotalConnectionRequests|Celkový počet požadavků na připojení|Počet|Průměr|Celkový počet požadavků na připojení. |

## <a name="next-steps"></a>Další kroky
[Azure Monitor – přehled](../azure-monitor/overview.md)      
[Začínáme s Průzkumníkem metrik Azure](../azure-monitor/platform/metrics-getting-started.md)      
[Metriky v rozhraní REST monitoru Azure](/rest/api/monitor/metrics)
