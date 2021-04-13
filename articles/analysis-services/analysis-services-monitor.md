---
title: Monitorování Azure Analysis Services metriky serveru | Microsoft Docs
description: Přečtěte si, jak Analysis Services použít Azure Průzkumník metrik, bezplatný nástroj na portálu, který vám umožní monitorovat výkon a stav serverů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3991eb421f42ec6645e3321d3a624e226fd12c67
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314248"
---
# <a name="monitor-server-metrics"></a>Monitorování metrik serveru

Analysis Services poskytuje metriky v Azure Průzkumník metrik, bezplatný nástroj na portálu, který vám umožní monitorovat výkon a stav serverů. Například monitorujte paměť a využití procesoru, počet připojení klientů a spotřebu prostředků dotazů. Analysis Services používá stejné rozhraní monitorování jako většina ostatních služeb Azure. Další informace najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/essentials/metrics-getting-started.md).

Chcete-li provádět podrobnější diagnostiku, sledovat výkon a identifikovat trendy v rámci několika prostředků služby v rámci skupiny prostředků nebo předplatného, použijte [Azure monitor](../azure-monitor/overview.md). Azure Monitor (služba) může mít za následek Fakturovatelné služby.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Monitorování metrik pro Analysis Services Server

1. V Azure Portal vyberte **metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V části **metrika** vyberte metriky, které chcete zahrnout do grafu. 

    ![Monitorovat graf](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriky serveru

Pomocí této tabulky můžete určit, které metriky jsou pro váš scénář monitorování nejvhodnější. Ve stejném grafu se dají zobrazit jenom metriky stejné jednotky.

|Metric|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů|
|CurrentConnections|Připojení: aktuální připojení|Počet|Průměr|Aktuální počet navázaných připojení klientů.|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet navázaných uživatelských relací.|
|mashup_engine_memory_metric|Paměť motoru M|Bajty|Průměr|Využití paměti procesy modulu hybridní webové aplikace|
|mashup_engine_qpu_metric|QPU modulu M|Počet|Průměr|QPU využití hybridních procesů v hybridních modulech|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|
|CleanerCurrentPrice|Paměť: aktuální cena čisticího modulu|Počet|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|
|CleanerMemoryNonshrinkable|Paměť: nezmenšovaná paměť čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|
|CleanerMemoryShrinkable|Paměť: velikost čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|
|MemoryLimitHard|Paměť: limit paměti – pevný|Bajty|Průměr|Limit pevné paměti, z konfiguračního souboru.|
|Hodnota memorylimithigh|Paměť: limit paměti – vysoká|Bajty|Průměr|Horní limit paměti, z konfiguračního souboru.|
|MemoryLimitLow|Paměť: limit paměti – nízká|Bajty|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|
|MemoryLimitVertiPaq|Paměť: limit paměti VertiPaq|Bajty|Průměr|Limit v paměti, z konfiguračního souboru.|
|MemoryUsage|Paměť: využití paměti|Bajty|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Je rovno čítači Process\PrivateBytes a velikosti paměti, která je namapovaná na paměť, která byla namapována nebo přidělena pomocí analytického stroje v paměti (VertiPaq), nad rámec limitu paměti stroje.|
|private_bytes_metric|Soukromé bajty |Bajty|Průměr|Celková velikost paměti, kterou proces Analysis Services modulu a procesu kontejneru hybridních webových aplikací přidělil, a ne včetně paměti sdílené s jinými procesy.|
|virtual_bytes_metric|Virtuální bajty |Bajty|Průměr|Aktuální velikost virtuálního adresního prostoru, který používá procesy procesu Analysis Services Engine a hybridní aplikace.|
|mashup_engine_private_bytes_metric|Počet privátních bajtů modulu M |Bajty|Průměr|Celkový objem paměťových procesů kontejneru hybridní webové aplikace, včetně paměti, která je sdílena s jinými procesy.|
|mashup_engine_virtual_bytes_metric|Virtuální bajty motoru M |Bajty|Průměr|Aktuální velikost procesů kontejneru hybridního adresního prostoru virtuálního adresáře.|
|Kvóta|Paměť: kvóta|Bajty|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|
|QuotaBlocked|Paměť: kvóta blokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|Bajty|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|
|VertiPaqPaged|Paměť: VertiPaq stránkované|Bajty|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|RowsConvertedPerSec|Zpracování: počet převedených řádků za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|
|CommandPoolBusyThreads|Vlákna: zaneprázdněná vlákna fondu příkazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|
|CommandPoolIdleThreads|Vlákna: nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|
|LongParsingBusyThreads|Vlákna: zaneprázdněná vlákna s dlouhou analýzou|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingIdleThreads|Vlákna: nečinná vlákna při dlouhé analýze|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingJobQueueLength|Vlákna: délka fronty úloh dlouhého rozboru|Počet|Průměr|Počet úloh ve frontě fondu vláken dlouhého analýzy.|
|ProcessingPoolIOJobQueueLength|Vlákna: délka fronty úloh v/v fondu zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|ProcessingPoolBusyIOJobThreads|Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|
|ProcessingPoolBusyNonIOThreads|Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|
|ProcessingPoolIdleIOJobThreads|Vlákna: vlákna nečinných úloh v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|ProcessingPoolIdleNonIOThreads|Vlákna: nečinná vlákna v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|
|QueryPoolIdleThreads|Vlákna: nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|
|ShortParsingBusyThreads|Vlákna: krátkodobá analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingIdleThreads|Vlákna: nečinná vlákna krátké analýzy|Počet|Průměr|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingJobQueueLength|Vlákna: délka fronty úlohy krátké analýzy|Počet|Průměr|Počet úloh ve frontě krátkého analýzy fondu vláken.|
|TotalConnectionFailures|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení|
|TotalConnectionRequests|Požadavky na připojení celkem|Počet|Průměr|Celkový počet požadavků na připojení |

## <a name="next-steps"></a>Další kroky
[Přehled Azure Monitor](../azure-monitor/overview.md)      
[Začínáme s Azure Průzkumník metrik](../azure-monitor/essentials/metrics-getting-started.md)      
[Metriky v Azure Monitor REST API](/rest/api/monitor/metrics)
