---
title: Monitorování Azure Analysis Services metriky serveru | Microsoft Docs
description: Přečtěte si, jak Analysis Services použít Azure Průzkumník metrik, bezplatný nástroj na portálu, který vám umožní monitorovat výkon a stav serverů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252802"
---
# <a name="monitor-server-metrics"></a>Monitorování metrik serveru

Analysis Services poskytuje metriky v Azure Průzkumník metrik, bezplatný nástroj na portálu, který vám umožní monitorovat výkon a stav serverů. Například monitorujte paměť a využití procesoru, počet připojení klientů a spotřebu prostředků dotazů. Analysis Services používá stejné rozhraní monitorování jako většina ostatních služeb Azure. Další informace najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md).

Chcete-li provádět podrobnější diagnostiku, sledovat výkon a identifikovat trendy v rámci několika prostředků služby v rámci skupiny prostředků nebo předplatného, použijte [Azure monitor](../azure-monitor/overview.md). Azure Monitor (služba) může mít za následek Fakturovatelné služby.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Monitorování metrik pro Analysis Services Server

1. V Azure Portal vyberte **metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V části **metrika**vyberte metriky, které chcete zahrnout do grafu. 

    ![Monitorovat graf](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriky serveru

Pomocí této tabulky můžete určit, které metriky jsou pro váš scénář monitorování nejvhodnější. Ve stejném grafu se dají zobrazit jenom metriky stejné jednotky.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Count|Průměr|Počet úloh ve frontě fondu vláken příkazů|
|CurrentConnections|Připojení: aktuální připojení|Count|Průměr|Aktuální počet navázaných připojení klientů.|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Průměr|Aktuální počet navázaných uživatelských relací.|
|mashup_engine_memory_metric|Paměť motoru M|B|Průměr|Využití paměti procesy modulu hybridní webové aplikace|
|mashup_engine_qpu_metric|QPU modulu M|Count|Průměr|QPU využití hybridních procesů v hybridních modulech|
|memory_metric|Memory (Paměť)|B|Průměr|Rezident. Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|
|CleanerCurrentPrice|Paměť: aktuální cena čisticího modulu|Count|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|
|CleanerMemoryNonshrinkable|Paměť: nezmenšovaná paměť čisticí paměti|B|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|
|CleanerMemoryShrinkable|Paměť: velikost čisticí paměti|B|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|
|MemoryLimitHard|Paměť: limit paměti – pevný|B|Průměr|Limit pevné paměti, z konfiguračního souboru.|
|Hodnota memorylimithigh|Paměť: limit paměti – vysoká|B|Průměr|Horní limit paměti, z konfiguračního souboru.|
|MemoryLimitLow|Paměť: limit paměti – nízká|B|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|
|MemoryLimitVertiPaq|Paměť: limit paměti VertiPaq|B|Průměr|Limit v paměti, z konfiguračního souboru.|
|MemoryUsage|Paměť: využití paměti|B|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Je rovno čítači Process\PrivateBytes a velikosti paměti, která je namapovaná na paměť, která byla namapována nebo přidělena pomocí analytického stroje v paměti (VertiPaq), nad rámec limitu paměti stroje.|
|private_bytes_metric|Soukromé bajty |B|Průměr|Celková velikost paměti, kterou proces Analysis Services modulu a procesu kontejneru hybridních webových aplikací přidělil, a ne včetně paměti sdílené s jinými procesy.|
|virtual_bytes_metric|Virtuální bajty |B|Průměr|Aktuální velikost virtuálního adresního prostoru, který používá procesy procesu Analysis Services Engine a hybridní aplikace.|
|mashup_engine_private_bytes_metric|Počet privátních bajtů modulu M |B|Průměr|Celkový objem paměťových procesů kontejneru hybridní webové aplikace, včetně paměti, která je sdílena s jinými procesy.|
|mashup_engine_virtual_bytes_metric|Virtuální bajty motoru M |B|Průměr|Aktuální velikost procesů kontejneru hybridního adresního prostoru virtuálního adresáře.|
|Kvóta|Paměť: kvóta|B|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|
|QuotaBlocked|Paměť: kvóta blokována|Count|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|B|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|
|VertiPaqPaged|Paměť: VertiPaq stránkované|B|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Count|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|RowsConvertedPerSec|Zpracování: počet převedených řádků za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|
|qpu_metric|QPU|Count|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|
|CommandPoolBusyThreads|Vlákna: zaneprázdněná vlákna fondu příkazů|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|
|CommandPoolIdleThreads|Vlákna: nečinná vlákna fondu příkazů|Count|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|
|LongParsingBusyThreads|Vlákna: zaneprázdněná vlákna s dlouhou analýzou|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingIdleThreads|Vlákna: nečinná vlákna při dlouhé analýze|Count|Průměr|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingJobQueueLength|Vlákna: délka fronty úloh dlouhého rozboru|Count|Průměr|Počet úloh ve frontě fondu vláken dlouhého analýzy.|
|ProcessingPoolIOJobQueueLength|Vlákna: délka fronty úloh v/v fondu zpracování|Count|Průměr|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|ProcessingPoolBusyIOJobThreads|Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování|Count|Průměr|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|
|ProcessingPoolBusyNonIOThreads|Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování|Count|Průměr|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|
|ProcessingPoolIdleIOJobThreads|Vlákna: vlákna nečinných úloh v/v fondu zpracování|Count|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|ProcessingPoolIdleNonIOThreads|Vlákna: nečinná vlákna v/v fondu zpracování|Count|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|
|QueryPoolIdleThreads|Vlákna: nečinná vlákna fondu dotazů|Count|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Count|Průměr|Počet úloh ve frontě fondu vláken dotazů.|
|ShortParsingBusyThreads|Vlákna: krátkodobá analýza zaneprázdněných vláken|Count|Průměr|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingIdleThreads|Vlákna: nečinná vlákna krátké analýzy|Count|Průměr|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingJobQueueLength|Vlákna: délka fronty úlohy krátké analýzy|Count|Průměr|Počet úloh ve frontě krátkého analýzy fondu vláken.|
|TotalConnectionFailures|Celkový počet selhání připojení|Count|Průměr|Celkový počet neúspěšných pokusů o připojení|
|TotalConnectionRequests|Požadavky na připojení celkem|Count|Průměr|Celkový počet požadavků na připojení |

## <a name="next-steps"></a>Další kroky
[Přehled Azure Monitor](../azure-monitor/overview.md)      
[Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md)      
[Metriky v Azure Monitor REST API](/rest/api/monitor/metrics)
