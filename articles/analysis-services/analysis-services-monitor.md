---
title: Monitorování Azure Analysis Services metriky serveru | Microsoft Docs
description: Naučte se monitorovat metriky Analysis Services serveru v Azure Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5431dd74629b9ed76a6a072d8ada286ce71a7633
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596102"
---
# <a name="monitor-server-metrics"></a>Monitorování metrik serveru

Analysis Services poskytuje metriky v Azure Průzkumník metrik, bezplatný nástroj na portálu, který vám umožní monitorovat výkon a stav serverů. Například monitorujte paměť a využití procesoru, počet připojení klientů a spotřebu prostředků dotazů. Analysis Services používá stejné rozhraní monitorování jako většina ostatních služeb Azure. Další informace najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md).

Chcete-li provádět podrobnější diagnostiku, sledovat výkon a identifikovat trendy v rámci několika prostředků služby v rámci skupiny prostředků nebo předplatného, použijte [Azure monitor](../azure-monitor/overview.md). Azure Monitor (služba) může mít za následek Fakturovatelné služby.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Monitorování metrik pro Analysis Services Server

1. V Azure Portal vyberte **metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V části **dostupné metriky**vyberte metriky, které chcete zahrnout do grafu. 

    ![Monitorovat graf](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Metriky serveru

Pomocí této tabulky můžete určit, které metriky jsou pro váš scénář monitorování nejvhodnější. Ve stejném grafu se dají zobrazit jenom metriky stejné jednotky.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Count|Average|Počet úloh ve frontě fondu vláken příkazů|
|CurrentConnections|Připojení: Aktuální připojení|Count|Average|Aktuální počet navázaných připojení klientů.|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Average|Aktuální počet navázaných uživatelských relací.|
|mashup_engine_memory_metric|Paměť motoru M|B|Average|Využití paměti procesy modulu hybridní webové aplikace|
|mashup_engine_qpu_metric|QPU modulu M|Count|Average|QPU využití hybridních procesů v hybridních modulech|
|memory_metric|Memory (Paměť)|B|Average|Rezident. Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|
|memory_thrashing_metric|Thrashing paměti|Percent|Average|Průměrná velikost thrashing paměti|
|CleanerCurrentPrice|Paměť: Aktuální cena čisticího modulu|Count|Average|Aktuální cena paměti $ USD, normalizovaná na 1000.|
|CleanerMemoryNonshrinkable|Paměť: Nezmenšovaná paměť čisticího modulu|B|Average|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|
|CleanerMemoryShrinkable|Paměť: Velikost čisticí paměti|B|Average|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|
|MemoryLimitHard|Paměť: Limit paměti – pevný|B|Average|Limit pevné paměti, z konfiguračního souboru.|
|Hodnota memorylimithigh|Paměť: Limit paměti – vysoká|B|Average|Horní limit paměti, z konfiguračního souboru.|
|MemoryLimitLow|Paměť: Limit paměti – nízká|B|Average|Omezení nedostatku paměti, z konfiguračního souboru.|
|MemoryLimitVertiPaq|Paměť: Limit paměti VertiPaq|B|Average|Limit v paměti, z konfiguračního souboru.|
|MemoryUsage|Paměť: Využití paměti|B|Average|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Je rovno čítači Process\PrivateBytes a velikosti paměti, která je namapovaná na paměť, která byla namapována nebo přidělena pomocí analytického stroje v paměti (VertiPaq), nad rámec limitu paměti stroje.|
|Kvóta|Paměť: Kvóta|B|Average|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|
|QuotaBlocked|Paměť: Kvóta blokována|Count|Average|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|
|VertiPaqNonpaged|Paměť: VertiPaq – nestránkované|B|Average|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|
|VertiPaqPaged|Paměť: VertiPaq na straně|B|Average|Počet bajtů stránkované paměti používaných pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Count|Average|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|RowsConvertedPerSec|Zpracování: Počet převedených řádků za sekundu|CountPerSecond|Average|Rychlost převodu řádků během zpracování.|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Average|Rychlost čtení řádků ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: Počet zapsaných řádků za sekundu|CountPerSecond|Average|Rychlost zápisu řádků během zpracování.|
|qpu_metric|QPU|Count|Average|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dotazů.|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Average|Frekvence úspěšných dokončení připojení.|
|CommandPoolBusyThreads|Vláken Zaneprázdněná vlákna fondu příkazů|Count|Average|Počet zaneprázdněných vláken ve fondu vláken příkazů.|
|CommandPoolIdleThreads|Vláken Nečinné podprocesy fondu příkazů|Count|Average|Počet nečinných vláken ve fondu vláken příkazů.|
|LongParsingBusyThreads|Vláken Dlouhá vlákna s dlouhou analýzou|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingIdleThreads|Vláken Dlouhá nečinná vlákna analýzy|Count|Average|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|
|LongParsingJobQueueLength|Vláken Délka fronty úloh dlouhého analýzy|Count|Average|Počet úloh ve frontě fondu vláken dlouhého analýzy.|
|ProcessingPoolIOJobQueueLength|Vláken Délka fronty úloh v/v fondu zpracování|Count|Average|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|
|ProcessingPoolBusyIOJobThreads|Vláken Zaneprázdněná vlákna úloh v/v fondu zpracování|Count|Average|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|
|ProcessingPoolBusyNonIOThreads|Vláken Vlákna, která nejsou v/v fondu zpracování, jsou zaneprázdněná.|Count|Average|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|
|ProcessingPoolIdleIOJobThreads|Vláken Zpracovávají se vstupně-výstupní vlákna nečinných fondů.|Count|Average|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|ProcessingPoolIdleNonIOThreads|Vláken Zpracování nečinných vstupně-výstupních vláken fondu|Count|Average|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|
|QueryPoolIdleThreads|Vláken Nečinné vlákna fondu dotazů|Count|Average|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|
|QueryPoolJobQueueLength|Vláken Délka fronty úloh fondu dotazů|Count|Average|Počet úloh ve frontě fondu vláken dotazů.|
|ShortParsingBusyThreads|Vláken Krátká vlákna s zaneprázdněnou analýzou|Count|Average|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingIdleThreads|Vláken Krátká nečinná vlákna analýzy|Count|Average|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|
|ShortParsingJobQueueLength|Vláken Délka fronty úlohy krátké analýzy|Count|Average|Počet úloh ve frontě krátkého analýzy fondu vláken.|
|TotalConnectionFailures|Celkový počet selhání připojení|Count|Average|Celkový počet neúspěšných pokusů o připojení|
|TotalConnectionRequests|Požadavky na připojení celkem|Count|Average|Celkový počet požadavků na připojení |

## <a name="next-steps"></a>Další postup
[Přehled Azure Monitor](../azure-monitor/overview.md)      
[Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md)      
[Metriky v Azure Monitor REST API](/rest/api/monitor/metrics)
