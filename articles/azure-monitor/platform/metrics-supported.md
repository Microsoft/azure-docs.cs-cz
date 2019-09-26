---
title: Azure Monitor podporované metriky podle typu prostředku
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: a8cffe83ec0f2cdfd2e71accfa55966e5dedcd89
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259139"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s Azure Monitor

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Níže je uvedený úplný seznam všech metrik, které jsou aktuálně k dispozici s kanálem metriky Azure Monitor. Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API. Tento seznam obsahuje jenom metriky, které jsou k dispozici, pomocí kanálu konsolidace Azure Monitor metriky. Pokud chcete zadat dotaz na tyto metriky a získat k nim přístup, použijte [rozhraní API verze 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) .

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika příchozích zpráv v centru událostí se dá prozkoumat a na úrovni fronty se bude považovat za graf. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|memory_metric|Memory (Paměť)|B|Average|Rezident. Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|TotalConnectionRequests|Požadavky na připojení celkem|Count|Average|Celkový počet požadavků na připojení Jedná se o příjem.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Average|Frekvence úspěšných dokončení připojení.|ServerResourceType|
|TotalConnectionFailures|Celkový počet selhání připojení|Count|Average|Celkový počet neúspěšných pokusů o připojení|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Average|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Count|Average|Počet úloh ve frontě fondu vláken příkazů|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Count|Average|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: Aktuální připojení|Count|Average|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: Aktuální cena čisticího modulu|Count|Average|Aktuální cena paměti $ USD, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: Velikost čisticí paměti|B|Average|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: Nezmenšovaná paměť čisticího modulu|B|Average|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|ServerResourceType|
|MemoryUsage|Paměť: Využití paměti|B|Average|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Rovná se čítači Process\PrivateBytes a velikostí dat mapovaných do paměti, přičemž se ignoruje jakákoli paměť, která byla namapována nebo přidělena xVelocitym stroji pro analýzu paměti (VertiPaq), nad rámec limitu paměti stroje xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: Limit paměti – pevný|B|Average|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|Hodnota memorylimithigh|Paměť: Limit paměti – vysoká|B|Average|Horní limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: Limit paměti – nízká|B|Average|Omezení nedostatku paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: Limit paměti VertiPaq|B|Average|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: Kvóta|B|Average|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|ServerResourceType|
|QuotaBlocked|Paměť: Kvóta blokována|Count|Average|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq – nestránkované|B|Average|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq na straně|B|Average|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracováván Počet přečtených řádků za sekundu|CountPerSecond|Average|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracováván Počet převedených řádků za sekundu|CountPerSecond|Average|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracováván Počet zapsaných řádků za sekundu|CountPerSecond|Average|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vláken Zaneprázdněná vlákna fondu příkazů|Count|Average|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Vláken Nečinné podprocesy fondu příkazů|Count|Average|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyThreads|Vláken Dlouhá vlákna s dlouhou analýzou|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingIdleThreads|Vláken Dlouhá nečinná vlákna analýzy|Count|Average|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingJobQueueLength|Vláken Délka fronty úloh dlouhého analýzy|Count|Average|Počet úloh ve frontě fondu vláken dlouhého analýzy.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vláken Zaneprázdněná vlákna úloh v/v fondu zpracování|Count|Average|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vláken Vlákna, která nejsou v/v fondu zpracování, jsou zaneprázdněná.|Count|Average|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vláken Délka fronty úloh v/v fondu zpracování|Count|Average|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vláken Zpracovávají se vstupně-výstupní vlákna nečinných fondů.|Count|Average|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vláken Zpracování nečinných vstupně-výstupních vláken fondu|Count|Average|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|ServerResourceType|
|QueryPoolIdleThreads|Vláken Nečinné vlákna fondu dotazů|Count|Average|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vláken Délka fronty úloh fondu dotazů|Count|Average|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|ShortParsingBusyThreads|Vláken Krátká vlákna s zaneprázdněnou analýzou|Count|Average|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingIdleThreads|Vláken Krátká nečinná vlákna analýzy|Count|Average|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingJobQueueLength|Vláken Délka fronty úlohy krátké analýzy|Count|Average|Počet úloh ve frontě krátkého analýzy fondu vláken.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Percent|Average|Průměrná velikost thrashing paměti|ServerResourceType|
|mashup_engine_qpu_metric|QPU modulu M|Count|Average|QPU využití hybridních procesů v hybridních modulech|ServerResourceType|
|mashup_engine_memory_metric|Paměť motoru M|B|Average|Využití paměti procesy modulu hybridní webové aplikace|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Count|Celkem|Celkový počet požadavků brány v daném období. Může být rozdělené podle různých dimenzí, které vám pomohou diagnostikovat problémy. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Požadavky brány celkem|Count|Celkem|Celkový počet požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou `Requests` metriku. |Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány|Count|Celkem|Celkový počet úspěšných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou `Requests` metriku.|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované požadavky brány|Count|Celkem| Celkový počet neautorizovaných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou `Requests` metriku.|Umístění, název hostitele|
|FailedRequests|Neúspěšné požadavky brány|Count|Celkem|Celkový počet neúspěšných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou `Requests` metriku.|Umístění, název hostitele|
|OtherRequests|Další požadavky brány|Count|Celkem|Celkový počet požadavků brány v daném období, které nespadají do kategorií úspěšných, neautorizovaných nebo neúspěšných. Tato metrika je zastaralá, doporučujeme použít novou `Requests` metriku. |Umístění, název hostitele|
|Trvání|Celková doba trvání žádostí o bránu|Milisekundy|Average|Doba mezi tím, kdy API Management obdrží požadavek od klienta a vrátí odpověď klientovi.|Umístění, název hostitele|
|Kapacita|Kapacita|Percent|Average|Indikátor zatížení instance API Management pro účely informování o tom, zda je třeba škálovat instanci tak, aby vyhovovala větší zátěži.|Location|
|EventHubTotalEvents|Celkový počet událostí EventHub|Count|Celkem|Celkový počet událostí odeslaných do centra EventHub z API Management v daném období.|Location|
|EventHubSuccessfulEvents|Úspěšné události EventHub|Count|Celkem|Celkový počet úspěšných událostí EventHub v daném období.|Location|
|EventHubTotalFailedEvents|Neúspěšné události EventHub|Count|Celkem|Celkový počet neúspěšných událostí EventHub v daném období.|Location|
|EventHubRejectedEvents|Odmítnuté události EventHub|Count|Celkem|Celkový počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neoprávněný) v daném období.|Location|
|EventHubThrottledEvents|Omezené události EventHub|Count|Celkem|Celkový počet událostí v daném období, které mají omezené události EventHub.|Location|
|EventHubTimedoutEvents|Vypršel časový limit událostí EventHub.|Count|Celkem|Celkový počet událostí EventHub, jejichž časový limit vypršel v daném období.|Location|
|EventHubDroppedEvents|Vyřazené události EventHub|Count|Celkem|Celkový počet vynechaných událostí z důvodu dosažení limitu velikosti fronty v daném období.|Location|
|EventHubTotalBytesSent|Velikost událostí EventHub|B|Celkem|Celková velikost událostí EventHub v bajtech v daném období.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Count|Celkem|Celkový počet úloh|Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet spuštění nasazení aktualizací|Count|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Celkový počet spuštěných počítačů nasazení aktualizace|Count|Celkem|Celkový počet spuštěných počítačů nasazení aktualizace softwaru v běhu nasazení aktualizace softwaru|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Vyhrazený počet jader|Count|Celkem|Celkový počet vyhrazených jader v účtu Batch|Žádné dimenze|
|TotalNodeCount|Počet vyhrazených uzlů|Count|Celkem|Celkový počet vyhrazených uzlů v účtu Batch|Žádné dimenze|
|LowPriorityCoreCount|Počet jader LowPriority|Count|Celkem|Celkový počet jader s nízkou prioritou v účtu Batch|Žádné dimenze|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Count|Celkem|Celkový počet uzlů s nízkou prioritou v účtu Batch|Žádné dimenze|
|CreatingNodeCount|Vytváření počtu uzlů|Count|Celkem|Počet vytvořených uzlů|Žádné dimenze|
|StartingNodeCount|Počáteční počet uzlů|Count|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|WaitingForStartTaskNodeCount|Čekání na počet uzlů spouštěcí úlohy|Count|Celkem|Počet uzlů, které čekají na dokončení počátečního úkolu|Žádné dimenze|
|StartTaskFailedNodeCount|Počet neúspěšných spuštění úlohy – počet uzlů|Count|Celkem|Počet uzlů, ve kterých se spouštěcí úkol nezdařil|Žádné dimenze|
|IdleNodeCount|Počet nečinných uzlů|Count|Celkem|Počet nečinných uzlů|Žádné dimenze|
|OfflineNodeCount|Počet uzlů v režimu offline|Count|Celkem|Počet offline uzlů|Žádné dimenze|
|RebootingNodeCount|Restartování počtu uzlů|Count|Celkem|Počet restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|Počet uzlů obnovování imagí|Count|Celkem|Počet uzlů obnovování imagí|Žádné dimenze|
|RunningNodeCount|Počet spuštěných uzlů|Count|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|LeavingPoolNodeCount|Počet ponechávání uzlů fondu|Count|Celkem|Počet uzlů opouštících fond|Žádné dimenze|
|UnusableNodeCount|Počet nepoužitelných uzlů|Count|Celkem|Počet nepoužitelných uzlů|Žádné dimenze|
|PreemptedNodeCount|Počet zrušených uzlů|Count|Celkem|Počet zrušených uzlů|Žádné dimenze|
|TaskStartEvent|Události zahájení úlohy|Count|Celkem|Celkový počet úloh, které byly spuštěny|Žádné dimenze|
|TaskCompleteEvent|Události dokončení úlohy|Count|Celkem|Celkový počet úloh, které byly dokončeny|Žádné dimenze|
|TaskFailEvent|Události neúspěšných úloh|Count|Celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|Žádné dimenze|
|PoolCreateEvent|Vytváření fondů – události|Count|Celkem|Celkový počet vytvořených fondů|Žádné dimenze|
|PoolResizeStartEvent|Události spuštění změny velikosti fondu|Count|Celkem|Celkový počet změněných počtu fondů, které byly spuštěny|Žádné dimenze|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Count|Celkem|Celkový počet změněných velikostí fondu, které byly dokončeny|Žádné dimenze|
|PoolDeleteStartEvent|Události spuštění odstranění fondu|Count|Celkem|Celkový počet odstranění fondů, které byly spuštěny|Žádné dimenze|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Count|Celkem|Celkový počet odstranění fondů, které byly dokončeny|Žádné dimenze|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Count|Celkem|Celkový počet úspěšně odstraněných úloh.|Žádné dimenze|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Count|Celkem|Celkový počet úloh, které byly vyžádány k odstranění.|Žádné dimenze|
|JobDisableCompleteEvent|Úloha zakázat kompletní události|Count|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|Žádné dimenze|
|JobDisableStartEvent|Úloha zakázat počáteční události|Count|Celkem|Celkový počet úloh, které byly vyžádány k zakázání.|Žádné dimenze|
|JobStartEvent|Události spuštění úlohy|Count|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|Žádné dimenze|
|JobTerminateCompleteEvent|Ukončit události dokončení úlohy|Count|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|Žádné dimenze|
|JobTerminateStartEvent|Události spuštění ukončení úlohy|Count|Celkem|Celkový počet úloh, které byly vyžádány k ukončení.|Žádné dimenze|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Počet připojených klientů|Count|Maximum||ShardId|
|totalcommandsprocessed|Celkem operací|Count|Celkem||ShardId|
|cachehits|Přístupy do mezipaměti|Count|Celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Count|Celkem||ShardId|
|GetCommands|Získá|Count|Celkem||ShardId|
|setcommands|Sady|Count|Celkem||ShardId|
|operationsPerSecond|Operace za sekundu|Count|Maximum||ShardId|
|evictedkeys|Vyřazení klíčů|Count|Celkem||ShardId|
|totalkeys|Celkem klíčů|Count|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Count|Celkem||ShardId|
|usedmemory|Využitá paměť|B|Maximum||ShardId|
|usedmemorypercentage|Procento využité paměti|Percent|Maximum||ShardId|
|usedmemoryRss|RSS využité paměti|B|Maximum||ShardId|
|serverLoad|Zatížení serveru|Percent|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Percent|Maximum||ShardId|
|cacheLatency|Mikrosekundy latence mezipaměti (Preview)|Count|Average||ShardId, SampleType|
|chyby|Chyby|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Připojení klienti (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed0|Celkem operací (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|getcommands0|Načtení (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|setcommands0|Sady (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|evictedkeys0|Vyřazené klíče (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|totalkeys0|Celkem klíčů (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|expiredkeys0|Klíče vypršení platnosti (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|usedmemoryRss0|RSS využité paměti (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime0|PROCESOR (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|connectedclients1|Připojení klienti (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed1|Celkem operací (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|getcommands1|Načtení (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|setcommands1|Sady (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|evictedkeys1|Vyřazené klíče (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|totalkeys1|Celkem klíčů (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|expiredkeys1|Klíče vypršení platnosti (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|usedmemoryRss1|RSS využité paměti (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime1|PROCESOR (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|connectedclients2|Připojení klienti (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed2|Celkem operací (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|getcommands2|Get (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|setcommands2|Sady (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|evictedkeys2|Vyřazené klíče (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|totalkeys2|Celkem klíčů (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|expiredkeys2|Klíče vypršení platnosti (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|usedmemoryRss2|RSS využité paměti (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime2|PROCESOR (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|connectedclients3|Připojení klienti (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed3|Celkem operací (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|getcommands3|Get (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|setcommands3|Sady (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|evictedkeys3|Vyřazené klíče (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|totalkeys3|Celkem klíčů (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|expiredkeys3|Klíče vypršení platnosti (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|usedmemoryRss3|RSS využité paměti (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime3|PROCESOR (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|connectedclients4|Připojení klienti (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed4|Celkem operací (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|getcommands4|Get (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|setcommands4|Sady (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|evictedkeys4|Vyřazené klíče (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|totalkeys4|Celkem klíčů (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|expiredkeys4|Klíče vypršení platnosti (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|usedmemoryRss4|RSS využité paměti (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|serverLoad4|Zatížení serveru (horizontálních oddílů 4)|Percent|Maximum||Žádné dimenze|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime4|PROCESOR (horizontálních oddílů 4)|Percent|Maximum||Žádné dimenze|
|connectedclients5|Připojení klienti (horizontálních oddílů 5)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed5|Celkem operací (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|cachehits5|Přístupy do mezipaměti (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|getcommands5|Získá (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|setcommands5|Sady (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|operationsPerSecond5|Operací za sekundu (horizontálních oddílů 5)|Count|Maximum||Žádné dimenze|
|evictedkeys5|Vyřazení klíčů (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|totalkeys5|Celkem klíčů (horizontálních oddílů 5)|Count|Maximum||Žádné dimenze|
|expiredkeys5|Klíče vypršení platnosti (horizontálních oddílů 5)|Count|Celkem||Žádné dimenze|
|usedmemory5|Využitá paměť (horizontálních oddílů 5)|B|Maximum||Žádné dimenze|
|usedmemoryRss5|RSS využité paměti (horizontálních oddílů 5)|B|Maximum||Žádné dimenze|
|serverLoad5|Zatížení serveru (horizontálních oddílů 5)|Percent|Maximum||Žádné dimenze|
|cacheWrite5|Zápis do mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead5|Čtení z mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime5|PROCESOR (horizontálních oddílů 5)|Percent|Maximum||Žádné dimenze|
|connectedclients6|Připojení klienti (horizontálních oddílů 6)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed6|Celkem operací (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|cachehits6|Přístupy do mezipaměti (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|getcommands6|Načtení (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|setcommands6|Sady (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|operationsPerSecond6|Operací za sekundu (horizontálních oddílů 6)|Count|Maximum||Žádné dimenze|
|evictedkeys6|Vyřazené klíče (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|totalkeys6|Celkem klíčů (horizontálních oddílů 6)|Count|Maximum||Žádné dimenze|
|expiredkeys6|Klíče vypršení platnosti (horizontálních oddílů 6)|Count|Celkem||Žádné dimenze|
|usedmemory6|Využitá paměť (horizontálních oddílů 6)|B|Maximum||Žádné dimenze|
|usedmemoryRss6|RSS využité paměti (horizontálních oddílů 6)|B|Maximum||Žádné dimenze|
|serverLoad6|Zatížení serveru (horizontálních oddílů 6)|Percent|Maximum||Žádné dimenze|
|cacheWrite6|Zápis do mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead6|Čtení z mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime6|PROCESOR (horizontálních oddílů 6)|Percent|Maximum||Žádné dimenze|
|connectedclients7|Připojení klienti (horizontálních oddílů 7)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed7|Celkem operací (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|cachehits7|Přístupy do mezipaměti (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|getcommands7|Načtení (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|setcommands7|Sady (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|operationsPerSecond7|Operací za sekundu (horizontálních oddílů 7)|Count|Maximum||Žádné dimenze|
|evictedkeys7|Vyřazení klíčů (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|totalkeys7|Celkem klíčů (horizontálních oddílů 7)|Count|Maximum||Žádné dimenze|
|expiredkeys7|Klíče vypršení platnosti (horizontálních oddílů 7)|Count|Celkem||Žádné dimenze|
|usedmemory7|Využitá paměť (horizontálních oddílů 7)|B|Maximum||Žádné dimenze|
|usedmemoryRss7|RSS využité paměti (horizontálních oddílů 7)|B|Maximum||Žádné dimenze|
|serverLoad7|Zatížení serveru (horizontálních oddílů 7)|Percent|Maximum||Žádné dimenze|
|cacheWrite7|Zápis do mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead7|Čtení z mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime7|PROCESOR (horizontálních oddílů 7)|Percent|Maximum||Žádné dimenze|
|connectedclients8|Připojení klienti (horizontálních oddílů 8)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed8|Celkem operací (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|cachehits8|Přístupy do mezipaměti (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|getcommands8|Získá (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|setcommands8|Sady (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|operationsPerSecond8|Operací za sekundu (horizontálních oddílů 8)|Count|Maximum||Žádné dimenze|
|evictedkeys8|Vyřazené klíče (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|totalkeys8|Celkem klíčů (horizontálních oddílů 8)|Count|Maximum||Žádné dimenze|
|expiredkeys8|Klíče vypršení platnosti (horizontálních oddílů 8)|Count|Celkem||Žádné dimenze|
|usedmemory8|Využitá paměť (horizontálních oddílů 8)|B|Maximum||Žádné dimenze|
|usedmemoryRss8|RSS využité paměti (horizontálních oddílů 8)|B|Maximum||Žádné dimenze|
|serverLoad8|Zatížení serveru (horizontálních oddílů 8)|Percent|Maximum||Žádné dimenze|
|cacheWrite8|Zápis do mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead8|Čtení z mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime8|PROCESOR (horizontálních oddílů 8)|Percent|Maximum||Žádné dimenze|
|connectedclients9|Připojení klienti (horizontálních oddílů 9)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed9|Celkem operací (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|cachehits9|Přístupy do mezipaměti (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|getcommands9|Načtení (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|setcommands9|Sady (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|operationsPerSecond9|Operací za sekundu (horizontálních oddílů 9)|Count|Maximum||Žádné dimenze|
|evictedkeys9|Vyřazené klíče (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|totalkeys9|Celkem klíčů (horizontálních oddílů 9)|Count|Maximum||Žádné dimenze|
|expiredkeys9|Klíče vypršení platnosti (horizontálních oddílů 9)|Count|Celkem||Žádné dimenze|
|usedmemory9|Využitá paměť (horizontálních oddílů 9)|B|Maximum||Žádné dimenze|
|usedmemoryRss9|RSS využité paměti (horizontálních oddílů 9)|B|Maximum||Žádné dimenze|
|serverLoad9|Zatížení serveru (horizontálních oddílů 9)|Percent|Maximum||Žádné dimenze|
|cacheWrite9|Zápis do mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead9|Čtení z mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime9|PROCESOR (horizontálních oddílů 9)|Percent|Maximum||Žádné dimenze|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Average|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádné dimenze|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Average|Průměrný počet bajtů přečtených z disku během období monitorování|Žádné dimenze|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Average|Průměrný počet bajtů zapsaných na disk během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Average|Čtení z disku – IOPS|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Average|Zápis na disk – IOPS|Žádné dimenze|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Average|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|B|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|B|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|RoleInstanceId|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Average|Průměrný počet bajtů přečtených z disku během období monitorování|RoleInstanceId|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Average|Průměrný počet bajtů zapsaných na disk během období monitorování|RoleInstanceId|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Average|Čtení z disku – IOPS|RoleInstanceId|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Average|Zápis na disk – IOPS|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkový počet volání|Count|Celkem|Celkový počet volání.|ApiName, OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Count|Celkem|Počet úspěšných volání.|ApiName, OperationName, oblast|
|TotalErrors|Celkový počet chyb|Count|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName, OperationName, oblast|
|BlockedCalls|Blokovaná volání|Count|Celkem|Počet volání, která překročily limit nebo kvótu.|ApiName, OperationName, oblast|
|ServerErrors|Chyby serveru|Count|Celkem|Počet volání s interní chybou služby (kód odpovědi HTTP 5xx).|ApiName, OperationName, oblast|
|ClientErrors|Chyby klientů|Count|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName, OperationName, oblast|
|DataIn|Vstupní data|B|Celkem|Velikost příchozích dat v bajtech|ApiName, OperationName, oblast|
|Data|Výstupní data|B|Celkem|Velikost odchozích dat v bajtech|ApiName, OperationName, oblast|
|Latence|Latence|Milisekund|Average|Latence v milisekundách|ApiName, OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Count|Celkem|Celkový počet znaků v příchozím textovém požadavku.|ApiName, OperationName, oblast|
|CharactersTrained|Vyškolené znaky|Count|Celkem|Celkový počet vyškolených znaků|ApiName, OperationName, oblast|
|SpeechSessionDuration|Doba trvání relace řeči|Sekundy|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName, OperationName, oblast|
|TotalTransactions|Celkový počet transakcí|Count|Celkem|Celkový počet transakcí|Žádné dimenze|
|TotalTokenCalls|Celkový počet volání tokenu|Count|Celkem|Celkový počet volání tokenů|ApiName, OperationName, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síť v fakturovatelným|B|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Fakturovatelná síť|B|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|B|Celkem|Bajty přečtené z disku během období monitorování|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|B|Celkem|Bajty zapsané na disk během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Average|Čtení z disku – IOPS|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Average|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Count|Average|Celkový počet kreditů dostupných pro shlukování|Žádné dimenze|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Count|Average|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádné dimenze|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|HF podle disku|Datový disk hloubka fronty (zastaralé)|Count|Average|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|HF v operačním systému podle disku|Disk s operačním systémem hloubka fronty (zastaralé)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LOGICKÉ JEDNOTKY|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Count|Average|Hloubka fronty datových disků (nebo délka fronty)|LOGICKÉ JEDNOTKY|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Příchozí toky|Příchozí toky (Preview)|Count|Average|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádné dimenze|
|Odchozí toky|Odchozí toky (Preview)|Count|Average|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření příchozích toků|Míra vytváření maximálního počtu příchozích toků (Preview)|CountPerSecond|Average|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádné dimenze|
|Maximální rychlost vytváření odchozích toků|Frekvence maximálního vytváření odchozích toků (Preview)|CountPerSecond|Average|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádné dimenze|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Percent|Average|Počet přístupů do mezipaměti pro datový disk úrovně Premium|LOGICKÉ JEDNOTKY|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Percent|Average|Neúspěšné čtení mezipaměti datových disků Premium|LOGICKÉ JEDNOTKY|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Percent|Average|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Percent|Average|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Celkový příchozí síťový provoz|Celkový příchozí síťový provoz|B|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Celkový odchozí síťový provoz|Celkový odchozí síťový provoz|B|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|VMName|
|Síťové vstupy|Síť v fakturovatelným|B|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Síťové výstupy|Fakturovatelná síť|B|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|
|Čtení z disku – bajty|Čtení z disku – bajty|B|Celkem|Bajty přečtené z disku během období monitorování|VMName|
|Zápis na disk – bajty|Zápis na disk – bajty|B|Celkem|Bajty zapsané na disk během období monitorování|VMName|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Average|Čtení z disku – IOPS|VMName|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Average|Zápis na disk – IOPS|VMName|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Count|Average|Celkový počet kreditů dostupných pro shlukování|Žádné dimenze|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Count|Average|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádné dimenze|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|HF podle disku|Datový disk hloubka fronty (zastaralé)|Count|Average|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|HF v operačním systému podle disku|Disk s operačním systémem hloubka fronty (zastaralé)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LUN, VMName|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LUN, VMName|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování|LUN, VMName|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|LUN, VMName|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Count|Average|Hloubka fronty datových disků (nebo délka fronty)|LUN, VMName|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky (Preview)|Count|Average|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|VMName|
|Odchozí toky|Odchozí toky (Preview)|Count|Average|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|VMName|
|Maximální rychlost vytváření příchozích toků|Míra vytváření maximálního počtu příchozích toků (Preview)|CountPerSecond|Average|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|VMName|
|Maximální rychlost vytváření odchozích toků|Frekvence maximálního vytváření odchozích toků (Preview)|CountPerSecond|Average|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|VMName|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Percent|Average|Počet přístupů do mezipaměti pro datový disk úrovně Premium|LUN, VMName|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Percent|Average|Neúspěšné čtení mezipaměti datových disků Premium|LUN, VMName|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Percent|Average|Počet přístupů do mezipaměti disku s operačním systémem Premium|VMName|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Percent|Average|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|VMName|
|Celkový příchozí síťový provoz|Celkový příchozí síťový provoz|B|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Celkový odchozí síťový provoz|Celkový odchozí síťový provoz|B|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití procesoru|Count|Average|Využití CPU na všech jádrech v millicores.|containerName|
|MemoryUsage|Využití paměti|B|Average|Celkové využití paměti v bajtech|containerName|
|NetworkBytesReceivedPerSecond|Bajty přijaté přes síť za sekundu|B|Average|Počet přijatých bajtů sítě za sekundu.|Žádné dimenze|
|NetworkBytesTransmittedPerSecond|Bajty přenesené přes síť za sekundu|B|Average|Počet bajtů přenášených sítí za sekundu.|Žádné dimenze|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Celkový počet vyžádané replikace|Count|Average|Celkový počet načtených dat imagí|Žádné dimenze|
|SuccessfulPullCount|Počet úspěšných vyžádané replikace|Count|Average|Počet úspěšných stažení imagí|Žádné dimenze|
|TotalPushCount|Celkový počet nabízených oznámení|Count|Average|Celkový počet nabízených oznámení imagí|Žádné dimenze|
|SuccessfulPushCount|Počet úspěšných vložení|Count|Average|Počet úspěšných vložení imagí|Žádné dimenze|
|RunDuration|Doba trvání běhu|Milisekundy|Celkem|Doba běhu v milisekundách|Žádné dimenze|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Count|Celkem|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Žádné dimenze|
|kube_node_status_allocatable_memory_bytes|Celková velikost dostupné paměti ve spravovaném clusteru|B|Celkem|Celková velikost dostupné paměti ve spravovaném clusteru|Žádné dimenze|
|kube_pod_status_ready|Počet lusků ve stavu připraveno|Count|Celkem|Počet lusků ve stavu připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Count|Celkem|Stavy pro různé podmínky uzlu|podmínka, stav, status2, uzel|
|kube_pod_status_phase|Počet lusků podle fáze|Count|Celkem|Počet lusků podle fáze|fáze, obor názvů pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights volání rozhraní API|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationSuccessfulLines|Mapování úspěšných řádků operace importu|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationFailedLines|Chybné řádky operace importu mapování|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationTotalLines|Mapování řádků celkových operací importu|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationRuntimeInSeconds|Mapování modulu runtime operace importu za sekundu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundProfileExportSucceeded|Odchozí export profilu byl úspěšný.|Count|Celkem||Žádné dimenze|
|DCIOutboundProfileExportFailed|Export odchozího profilu se nezdařil.|Count|Celkem||Žádné dimenze|
|DCIOutboundProfileExportDuration|Doba trvání exportu odchozího profilu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiExportSucceeded|Odchozí ukazatel KPI se úspěšně exportoval.|Count|Celkem||Žádné dimenze|
|DCIOutboundKpiExportFailed|Export odchozího klíčového ukazatele se nezdařil.|Count|Celkem||Žádné dimenze|
|DCIOutboundKpiExportDuration|Odchozí doba exportu klíčového ukazatele výkonu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiExportStarted|Začalo export odchozího klíčového ukazatele výkonu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiRecordCount|Počet záznamů odchozího ukazatele KPI|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundProfileExportCount|Počet exportů odchozího profilu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportFailed|Odchozí export počátečního profilu se nezdařil.|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportSucceeded|Odchozí export počátečního profilu byl úspěšný.|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportFailed|Nepovedlo se exportovat výstupní počáteční klíčový ukazatel výkonu.|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportSucceeded|Odchozí počáteční klíč KPI se úspěšně exportoval.|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportDurationInSeconds|Doba neodchozího exportu počátečního profilu v sekundách|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiFailed|Úloha adla pro standardní klíčový ukazatel výkonu selhala během sekund.|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiTimeOut|Časový limit adla úlohy pro standardní klíčový ukazatel výkonu v sekundách|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiCompleted|Úloha adla pro standardní klíčový ukazatel výkonu se dokončila během sekund.|Sekundy|Celkem||Žádné dimenze|
|ImportASAValuesFailed|Počet neúspěšných importování hodnot ASA|Count|Celkem||Žádné dimenze|
|ImportASAValuesSucceeded|Počet úspěšně importovaných hodnot ASA|Count|Celkem||Žádné dimenze|
|DCIProfilesCount|Počet instancí profilu|Count|Poslední||Žádné dimenze|
|DCIInteractionsPerMonthCount|Počet interakcí za měsíc|Count|Poslední||Žádné dimenze|
|DCIKpisCount|Počet klíčových ukazatelů výkonu|Count|Poslední||Žádné dimenze|
|DCISegmentsCount|Počet segmentů|Count|Poslední||Žádné dimenze|
|DCIPredictiveMatchPoliciesCount|Počet prediktivních shod|Count|Poslední||Žádné dimenze|
|DCIPredictionsCount|Počet předpovědí|Count|Poslední||Žádné dimenze|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost čtení (síť)|BytesPerSecond|Average|Propustnost čtení síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Propustnost zápisu (síť)|BytesPerSecond|Average|Propustnost zápisu síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování do cloudu (sdílení)|BytesPerSecond|Average|Propustnost stahování do Azure ze sdílené složky během období generování sestav.|Sdílení|
|CloudUploadThroughputPerShare|Propustnost nahrávání do cloudu (sdílení)|BytesPerSecond|Average|Odeslání propustnosti do Azure ze sdílené složky během období generování sestav.|Sdílení|
|BytesUploadedToCloudPerShare|Odeslané bajty v cloudu (sdílená složka)|B|Average|Celkový počet bajtů odeslaných do Azure ze sdílené složky během období generování sestav.|Sdílení|
|Úložiště|Celková kapacita|B|Average|Celková kapacita|Žádné dimenze|
|Availablecapacity;)|Dostupná kapacita|B|Average|Dostupná kapacita v bajtech během období generování sestav.|Žádné dimenze|
|CloudUploadThroughput|Propustnost nahrávání cloudu|BytesPerSecond|Average|Propustnost nahrávání do cloudu do Azure během období generování sestav.|Žádné dimenze|
|CloudReadThroughput|Propustnost cloudu při stahování|BytesPerSecond|Average|Propustnost stahování cloudu do Azure během období generování sestav.|Žádné dimenze|
|BytesUploadedToCloud|Odeslané bajty v cloudu (zařízení)|B|Average|Celkový počet bajtů odeslaných do Azure ze zařízení během období generování sestav.|Žádné dimenze|
|HyperVVirtualProcessorUtilization|Výpočetní prostředí Edge – procento využití procesoru|Percent|Average|Využití procesoru v procentech|InstanceName|
|HyperVMemoryUtilization|Výpočet využití paměti na hraničních zařízeních|Percent|Average|Množství využité paměti RAM|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Count|Celkem||profilace, Activity|
|SuccessfulRuns|Úspěšná spuštění|Count|Celkem||profilace, Activity|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Neúspěšná metrika spuštění kanálu|Count|Celkem||FailureType, název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Count|Celkem||FailureType, název|
|ActivityFailedRuns|Neúspěšná aktivita spustí metriky|Count|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivitySucceededRuns|Úspěšná aktivita spustí metriky|Count|Celkem||ActivityType, název kanálu, FailureType, název|
|TriggerFailedRuns|Neúspěšná aktivační událost spustí metriky|Count|Celkem||Název, FailureType|
|TriggerSucceededRuns|Úspěšná aktivační událost spustí metriky|Count|Celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití procesoru prostředí Integration runtime|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Dostupná paměť modulu runtime integrace|B|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximální počet povolených entit|Count|Maximum||Žádné dimenze|
|MaxAllowedFactorySizeInGbUnits|Maximální povolená velikost továrny (jednotka GB)|Count|Maximum||Žádné dimenze|
|ResourceCount|Celkový počet entit|Count|Maximum||Žádné dimenze|
|FactorySizeInGbUnits|Celková velikost továrny (jednotka GB)|Count|Maximum||Žádné dimenze|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Count|Celkem|Počet úspěšných úloh|Žádné dimenze|
|JobEndedFailure|Neúspěšné úlohy|Count|Celkem|Počet neúspěšných úloh|Žádné dimenze|
|JobEndedCancelled|Zrušené úlohy|Count|Celkem|Počet zrušených úloh|Žádné dimenze|
|JobAUEndedSuccess|Úspěšná doba AU|Sekundy|Celkem|Celková doba AU pro úspěšné úlohy.|Žádné dimenze|
|JobAUEndedFailure|Neúspěšná doba aktualizace AU|Sekundy|Celkem|Celková doba AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedCancelled|Zrušená čas AU|Sekundy|Celkem|Celková doba AU pro zrušené úlohy.|Žádné dimenze|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Celková velikost úložiště|B|Maximum|Celková velikost dat uložených v účtu.|Žádné dimenze|
|Napsáno|Zapsaná data|B|Celkem|Celkové množství dat zapsaných na účet.|Žádné dimenze|
|DataRead|Přečtená data|B|Celkem|Celkový objem dat načtených z účtu.|Žádné dimenze|
|WriteRequests|Požadavky na zápis|Count|Celkem|Počet požadavků na zápis dat na účet.|Žádné dimenze|
|ReadRequests|Žádosti o čtení|Count|Celkem|Počet požadavků na čtení dat pro účet.|Žádné dimenze|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Percent|Average|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Percent|Average|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Percent|Average|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|B|Average|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Percent|Average|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|B|Average|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|B|Average|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Prodleva replikace v sekundách|Count|Average|Prodleva replikace v sekundách|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Percent|Average|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Percent|Average|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Percent|Average|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|B|Average|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Percent|Average|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|B|Average|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|B|Average|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Prodleva replikace v sekundách|Count|Average|Prodleva replikace v sekundách|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Percent|Average|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Percent|Average|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Percent|Average|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|B|Average|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Percent|Average|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|B|Average|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|B|Average|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v sekundách|Žádné dimenze|
|pg_replica_log_delay_in_bytes|Maximální prodleva napříč replikami|B|Maximum|Prodleva z největšího zpoždění repliky v bajtech|Žádné dimenze|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Percent|Average|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Percent|Average|Procentuální hodnota paměti|Žádné dimenze|
|IOPS|IOPS|Count|Average|Vstupně-výstupní operace za sekundu|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Počet pokusů o odeslání zprávy telemetrie|Count|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Odeslané zprávy telemetrie|Count|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné dimenze|
|c2d.commands.egress.complete.success|Dokončené příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení úspěšně dokončených zařízením|Žádné dimenze|
|c2d.commands.egress.abandon.success|Zrušené příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení, které zařízení opustilo|Žádné dimenze|
|c2d.commands.egress.reject.success|Odmítnuté příkazy|Count|Celkem|Počet příkazů typu cloud-zařízení odmítnutých zařízením|Žádné dimenze|
|devices.totalDevices|Celkem zařízení (zastaralé)|Count|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojená zařízení (zastaralé) |Count|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|d2c.telemetry.egress.success|Směrování: doručené zprávy telemetrie|Count|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné dimenze|
|d2c.telemetry.egress.dropped|Směrování: vyřazené zprávy telemetrie |Count|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Směrování: osamocené zprávy telemetrie |Count|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné dimenze|
|d2c.telemetry.egress.invalid|Směrování: nekompatibilní zprávy telemetrie|Count|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné dimenze|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručené do záložního režimu|Count|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručené do centra událostí|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekundy|Average|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručené zprávám/událostem|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události). Tato metrika začne pracovat pouze v případě, že https://aka.ms/iotrouting) je povoleno směrování (pro službu IoT Hub.|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Směrování: latence zpráv pro zprávy/události|Milisekundy|Average|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události) Tato metrika začne pracovat pouze v případě, že https://aka.ms/iotrouting) je povoleno směrování (pro službu IoT Hub.|Žádné dimenze|
|d2c.endpoints.egress.storage|Směrování: zprávy doručené do úložiště|Count|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. latence. Storage|Směrování: latence zpráv pro úložiště|Milisekundy|Average|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné dimenze|
|d2c.endpoints.egress.storage.bytes|Směrování: data Doručená do úložiště|B|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné dimenze|
|d2c.endpoints.egress.storage.blobs|Směrování: objekty blob doručené do úložiště|Count|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné dimenze|
|EventGridDeliveries|Event Grid doručení (Preview)|Count|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události (https://aka.ms/ioteventgrid).|Výsledek, EventType|
|EventGridLatency|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ události|
|d2c.twin.read.success|Úspěšné čtení ze zařízení|Count|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|d2c.twin.read.failure|Neúspěšná čtení ze zařízení|Count|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi u dvojitých čtení ze zařízení|B|Average|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|d2c.twin.update.success|Úspěšné nedokončené změny ze zařízení|Count|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|d2c.twin.update.failure|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Count|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné dimenze|
|d2c.twin.update.size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|B|Average|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|c2d.methods.success|Úspěšná volání přímé metody|Count|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Count|Celkem|Počet všech neúspěšných volání metody Direct|Žádné dimenze|
|c2d.methods.requestSize|Velikost žádosti o vyvolání přímé metody|B|Average|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odezvy volání přímých metod|B|Average|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné dimenze|
|c2d.twin.read.success|Úspěšné zdvojené čtení z back-endu|Count|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné dimenze|
|c2d.twin.read.failure|Neúspěšné čtení z back-endu ze zadních vláken|Count|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi zdvojeného čtení z back-endu|B|Average|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné dimenze|
|c2d.twin.update.success|Úspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné dimenze|
|c2d.twin.update.failure|Neúspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné dimenze|
|c2d.twin.update.size|Velikost dvojitě aktualizovaných aktualizací z back-endu|B|Average|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné dimenze|
|twinQueries.success|Úspěšné zdvojené dotazy|Count|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné zdvojené dotazy|Count|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné dimenze|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|B|Average|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytváření zdvojených úloh aktualizace|Count|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Count|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytváření úloh vyvolání metod|Count|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Nepovedlo se vytvořit úlohy vyvolání metody|Count|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné dimenze|
|jobs.listJobs.success|Úspěšná volání na seznam úloh|Count|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné dimenze|
|jobs.listJobs.failure|Neúspěšná volání pro výpis úloh|Count|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné dimenze|
|jobs.cancelJob.success|Úspěšná zrušení úlohy|Count|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné dimenze|
|jobs.cancelJob.failure|Neúspěšná zrušení úloh|Count|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné dimenze|
|jobs.queryJobs.success|Úspěšné dotazy na úlohy|Count|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné dimenze|
|jobs.queryJobs.failure|Neúspěšné dotazy na úlohy|Count|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné dimenze|
|dokončené úlohy|Dokončené úlohy|Count|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|úlohy. nezdařilo se|Neúspěšné úlohy|Count|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezování|Count|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Count|Average|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádné dimenze|
|deviceDataUsage|Celkové využití dat zařízení|B|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|totalDeviceCount|Celkem zařízení (Preview)|Count|Average|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|connectedDeviceCount|Připojená zařízení (Preview)|Count|Average|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|Konfiguračních|Metriky konfigurace|Count|Celkem|Metriky pro operace konfigurace|Žádné dimenze|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Count|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Přiřazená zařízení|Count|Celkem|Počet zařízení přiřazených ke centru IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Count|Celkem|Počet pokusů o ověření identity zařízení|ProvisioningServiceName, status, protokol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AvailableStorage|Dostupné úložiště|B|Celkem|Celkové dostupné úložiště hlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Ukončení připojení Cassandra|Count|Celkem|Počet uzavřených připojení Cassandra, která se hlásí s členitou úrovní 1 minuty|Oblast, ClosureReason|
|CassandraRequestCharges|Poplatky za žádosti Cassandra|Count|Celkem|Ru spotřebované pro vytvořené požadavky Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType|
|CassandraRequests|Žádosti Cassandra|Count|Count|Počet provedených požadavků Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType, ErrorCode|
|Využití datausage|Využití dat|B|Celkem|Celkové využití dat nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|DocumentCount|Počet dokumentů|Count|Celkem|Celkový počet dokumentů hlášených v rozmezí 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Kvóta dokumentu|B|Celkem|Celková kvóta úložiště vykazovaná s členitosti 5 minut|CollectionName, DatabaseName, region|
|IndexUsage|Využití indexu|B|Celkem|Celkové využití indexu nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Žádosti o metadata|Count|Count|Počet požadavků na metadata Cosmos DB zachovává shromažďování systémových metadat pro každý účet, který vám umožní vytvořit výčet kolekcí, databází atd. a jejich konfigurací bez poplatků.|DatabaseName, CollectionName, region, StatusCode |
|MongoRequestCharge|Poplatek za požadavek Mongo|Count|Celkem|Spotřebované jednotky žádosti Mongo|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequests|Žádosti Mongo|Count|Count|Počet provedených požadavků Mongo|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|ProvisionedThroughput|Zřízená propustnost|Count|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|ReplicationLatency|Latence replikace p99|Milisekund|Average|Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet|SourceRegion, TargetRegion|
|ServiceAvailability|Dostupnost služby|Percent|Average|Dostupnost žádostí o účet v časovém rozmezí jedné hodiny, dne nebo měsíce|Žádné dimenze|
|TotalRequestUnits|Celkový počet jednotek žádostí|Count|Celkem|Spotřebované jednotky žádosti|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType|
|TotalRequests|Požadavky celkem|Count|Count|Počet provedených požadavků|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishFailCount|Publikovat neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Count|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Count|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Odpovídající události|Count|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné dimenze|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné dimenze|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekundy|Average|Doba zpracování cíle v milisekundách|Žádné dimenze|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Count|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishFailCount|Neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Count|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Count|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|Celkem|Úspěšné požadavky pro Microsoft. EventHub|EntityName |
|ServerErrors|Chyby serveru.|Count|Celkem|Chyby serveru pro Microsoft. EventHub|EntityName |
|UserErrors|Chyby uživatele.|Count|Celkem|Chyby uživatele pro Microsoft. EventHub|EntityName |
|QuotaExceededErrors|Chyby překročení kvóty|Count|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|EntityName |
|ThrottledRequests|Omezené požadavky.|Count|Celkem|Omezené požadavky pro Microsoft. EventHub|EntityName |
|IncomingRequests|Příchozí požadavky|Count|Celkem|Příchozí požadavky pro Microsoft. EventHub|EntityName|
|IncomingMessages|Příchozí zprávy|Count|Celkem|Příchozí zprávy pro Microsoft. EventHub|EntityName|
|OutgoingMessages|Odchozí zprávy|Count|Celkem|Odchozí zprávy pro Microsoft. EventHub|EntityName|
|IncomingBytes|Příchozí bajty.|B|Celkem|Příchozí bajty pro Microsoft. EventHub|EntityName|
|OutgoingBytes|Odchozí bajty|B|Celkem|Odchozí bajty pro Microsoft. EventHub|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení.|Count|Average|Otevřená připojení pro Microsoft. EventHub|EntityName|
|ConnectionsClosed|Uzavřená připojení.|Count|Average|Otevřená připojení pro Microsoft. EventHub|EntityName|
|CaptureBacklog|Zachyťte nevyřízené položky.|Count|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|EntityName|
|CapturedMessages|Zachycené zprávy.|Count|Celkem|Zachycené zprávy pro Microsoft. EventHub|EntityName|
|CapturedBytes|Zachycené bajty.|B|Celkem|Zachycené bajty pro Microsoft. EventHub|EntityName|
|Size|Size|B|Average|Velikost centra EventHub v bajtech|EntityName|
|INREQS|Příchozí požadavky (zastaralé)|Count|Celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů (zastaralé)|Žádné dimenze|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|FAILREQ|Neúspěšné žádosti (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|SVRBSY|Chyby zaneprázdněnosti serveru (zastaralé)|Count|Celkem|Celkový počet chyb zaneprázdněných serverem pro obor názvů (zastaralé)|Žádné dimenze|
|MEZI SEBOU|Interní chyby serveru (zastaralé)|Count|Celkem|Celkový počet interních chyb serveru pro obor názvů (zastaralé)|Žádné dimenze|
|MISCERR|Další chyby (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích zpráv (zastaralé).|Žádné dimenze|
|EHINMSGS|Příchozí zprávy (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích zpráv (zastaralé).|Žádné dimenze|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|B|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích bajtů (zastaralé).|Žádné dimenze|
|EHINBYTES|Příchozí bajty (zastaralé)|B|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|B|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích bajtů (zastaralé).|Žádné dimenze|
|EHOUTBYTES|Odchozí bajty (zastaralé)|B|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHABL|Archivovat nevyřízené zprávy (zastaralé)|Count|Celkem|Archivní zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádné dimenze|
|EHAMSGS|Archivní zprávy (zastaralé)|Count|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)|Žádné dimenze|
|EHAMBS|Propustnost zpráv archivu (zastaralé)|B|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádné dimenze|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clustery

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|Celkem|Úspěšné požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ServerErrors|Chyby serveru. (Preview)|Count|Celkem|Chyby serveru pro Microsoft. EventHub (Preview)|Žádné dimenze|
|UserErrors|Chyby uživatele. (Preview)|Count|Celkem|Chyby uživatele pro Microsoft. EventHub (Preview)|Žádné dimenze|
|QuotaExceededErrors|Chyby překročení kvóty (Preview)|Count|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub. (Preview)|Žádné dimenze|
|ThrottledRequests|Omezené požadavky. (Preview)|Count|Celkem|Omezené požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingRequests|Příchozí žádosti (Preview)|Count|Celkem|Příchozí požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingMessages|Příchozí zprávy (Preview)|Count|Celkem|Příchozí zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|Celkem|Odchozí zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingBytes|Příchozí bajty. (Preview)|B|Celkem|Příchozí bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|OutgoingBytes|Odchozí bajty (Preview)|B|Celkem|Odchozí bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ActiveConnections|ActiveConnections (Preview)|Count|Average|Celkový počet aktivních připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení. (Preview)|Count|Average|Otevřená připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ConnectionsClosed|Uzavřená připojení. (Preview)|Count|Average|Otevřená připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|CaptureBacklog|Zachyťte nevyřízené položky. (Preview)|Count|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub. (Preview)|Žádné dimenze|
|CapturedMessages|Zachycené zprávy. (Preview)|Count|Celkem|Zachycené zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|CapturedBytes|Zachycené bajty. (Preview)|B|Celkem|Zachycené bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|Procesor|PROCESOR (Preview)|Percent|Maximum|Využití CPU pro cluster centra událostí jako procento|Role|
|AvailableMemory|Dostupná paměť (Preview)|Count|Maximum|Dostupná paměť pro cluster centra událostí v bajtech|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Žádosti o bránu|Count|Celkem|Počet žádostí o bránu|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Zařadit požadavky na bránu|Count|Celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Počet aktivních pracovníků|Count|Maximum|Počet aktivních pracovníků|ClusterDnsName, metric|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaznamenaná hodnota metriky|Count|Average|Hodnota vypočítaná AutoScale při spuštění|MetricTriggerSource|
|MetricThreshold|Prahová hodnota metriky|Count|Average|Nakonfigurované prahové hodnoty automatického škálování, když se spustilo automatické škálování.|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Count|Average|Kapacita nahlášená pro automatické škálování při jejím spuštění.|Žádné dimenze|
|ScaleActionsInitiated|Zahájené akce škálování|Count|Celkem|Směr operace škálování.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

(Public Preview)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Average|Procento úspěšně dokončených testů dostupnosti|availabilityResult/název, availabilityResult/umístění|
|availabilityResults/Count|Testy dostupnosti|Count|Count|Počet testů dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba trvání testu dostupnosti|Milisekund|Average|Doba trvání testu dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Čas síťového připojení při stahování stránky|Milisekund|Average|Doba mezi požadavkem uživatele a připojením k síti. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádné dimenze|
|browserTimings/processingDuration|Čas klientského zpracování|Milisekund|Average|Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky.|Žádné dimenze|
|browserTimings/receiveDuration|Čas přijetí odezvy|Milisekund|Average|Čas mezi prvním a posledním bajtů nebo až do odpojení|Žádné dimenze|
|browserTimings/sendDuration|Čas odeslání žádosti|Milisekund|Average|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádné dimenze|
|browserTimings/totalDuration|Čas načítání stránky prohlížeče|Milisekund|Average|Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí.|Žádné dimenze|
|závislosti/počet|Volání závislostí|Count|Count|Počet volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěch, provoz/syntetická, Cloud/roleInstance, Cloud/roleName|
|závislosti/doba trvání|Doba trvání závislosti|Milisekund|Average|Doba trvání volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěch, provoz/syntetická, Cloud/roleInstance, Cloud/roleName|
|závislosti/selhání|Chyby při volání závislostí|Count|Count|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|závislost/typ, Dependency/performanceBucket, provoz/syntetické, Cloud/roleInstance, Cloud/roleName|
|pageViews/Count|Zobrazení stránek|Count|Count|Počet zobrazení stránek|operace/syntetické|
|pageViews/doba trvání|Doba načtení zobrazení stránky|Milisekund|Average|Doba načtení zobrazení stránky|operace/syntetické|
|Čítače výkonu/requestExecutionTime|Doba provádění požadavku HTTP|Milisekund|Average|Čas provedení posledního požadavku.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|Požadavky HTTP ve frontě aplikací|Count|Average|Délka fronty požadavků aplikace|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Četnost požadavků HTTP|CountPerSecond|Average|Míra všech požadavků na aplikaci za sekundu z ASP.NET.|Cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Frekvence výjimek|CountPerSecond|Average|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Frekvence V/V procesu|BytesPerSecond|Average|Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení.|Cloud/roleInstance|
|Čítače výkonu/processCpuPercentage|Proces – procesor|Percent|Average|Procentuální hodnota uplynulého času, který všechny podprocesy procesu používají k provádění instrukcí. Může se lišit od 0 do 100. Tato metrika indikuje výkon samotného procesu W3wp.|Cloud/roleInstance|
|Čítače výkonu/processorCpuPercentage|Čas procesoru|Percent|Average|Procento času, které procesor stráví v nečinných vláknech|Cloud/roleInstance|
|Čítače výkonu/memoryAvailableBytes|Paměť k dispozici|B|Average|Fyzická paměť je okamžitě k dispozici pro přidělení procesu nebo pro použití systémem.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privátní bajty procesu|B|Average|Paměť exkluzivně přiřazená k procesům monitorovaných aplikací.|Cloud/roleInstance|
|žádosti/doba trvání|Doba odezvy serveru|Milisekund|Average|Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/počet|Požadavky serveru|Count|Count|Počet dokončených požadavků HTTP|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/selhání|Neúspěšné požadavky|Count|Count|Počet požadavků HTTP označených jako neúspěšné Ve většině případů se jedná o žádosti s kódem odpovědi > = 400 a nerovná se 401.|Request/performanceBucket, Request/resultCode, Operational/syntetické, Cloud/roleInstance, Cloud/roleName|
|žádosti/rychlost|Četnost požadavků serveru|CountPerSecond|Average|Frekvence požadavků serveru za sekundu|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|výjimky/počet|Výjimky|Count|Count|Kombinovaný počet všech nezachycených výjimek.|Cloud/roleName, Cloud/roleInstance, klient/typ|
|výjimky/prohlížeč|Výjimky prohlížečů|Count|Count|Počet nezachycených výjimek vyvolaných v prohlížeči|Žádné dimenze|
|výjimky/Server|Serverové výjimky|Count|Count|Počet nezachycených výjimek vyvolaných v serverové aplikaci|Cloud/roleName, Cloud/roleInstance|
|trasování/počet|Trasování|Count|Count|Počet dokumentů trasování|Trace/severityLevel, provozní/syntetické, Cloud/roleName, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkem přístupů k rozhraní API služby|Count|Count|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekundy|Average|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode|
|ServiceApiResult|Celkem výsledků rozhraní API služby|Count|Count|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Využití mezipaměti|Percent|Average|Úroveň využití v oboru clusteru|Žádné|
|QueryDuration|Doba trvání dotazu|Milisekundy|Average|Doba trvání dotazů v sekundách|Stav dotazu|
|IngestionUtilization|Využití příjmu|Percent|Average|Poměr využitých slotů pro přijímání na clusteru|Žádné|
|Udržení|Zachovat naživu|Count|Average|Správnosti check indikuje, že cluster reaguje na dotazy|Žádné|
|IngestionVolumeInMB|Objem příjmu (v MB)|Count|Celkem|Celkový objem zpracovaných dat do clusteru (v MB)|Databáze|
|IngestionLatencyInSeconds|Latence příjmu (v sekundách)|Sekundy|Average|Doba ingestování ze zdroje (např. zpráva je v centru EventHub) do clusteru v řádu sekund|Žádné|
|EventProcessedForEventHubs|Zpracované události (pro Event Hubs)|Count|Celkem|Počet událostí zpracovaných clusterem při přijímání z centra událostí|Žádné|
|IngestionResult|Výsledek ingestování|Count|Count|Počet operací ingestování|Stav|
|Procesor|Procesor|Percent|Average|Úroveň využití procesoru|Žádné|
| ContinuousExportNumOfRecordsExported | Počet záznamů exportovaných při průběžném exportu | Count | Celkem | Počet záznamů exportovaných pro každý artefakt úložiště zapsaný během operace exportu  | Žádné |
| ExportUtilization | Využití exportu | Percent | Maximum | Využití exportu | Žádné |
| ContinuousExportPendingCount | Počet nevyřízených položek průběžného exportu | Count | Maximum | Počet probíhajících úloh průběžného exportu připravených k provedení | Žádné |
| ContinuousExportMaxLatenessMinutes | Maximální počet minut zpoždění pro průběžný export | Count | Maximum | Maximální doba v minutách pro všechny průběžné exporty, které čekají a jsou připravené ke spuštění | Žádné |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/účty

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Count|Count|Počet volání rozhraní API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšná spuštění|Count|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšná spuštění|Count|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunLatency|Latence spuštění|Sekundy|Average|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Average|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Události omezeného spuštění|Count|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce|Count|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Average|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Average|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|TriggersStarted|Spuštěné aktivační události |Count|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Aktivační události dokončeny |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Aktivační události byly úspěšné |Count|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggersFailed|Neúspěšná triggery |Count|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Aktivační události přeskočeny|Count|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Aktivační události aktivovány |Count|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Average|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Average|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Average|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|
|BillableActionExecutions|Fakturovatelné provádění akcí|Count|Celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádné dimenze|
|BillableTriggerExecutions|Fakturovatelná spuštění triggerů|Count|Celkem|Počet fakturovaných provedení triggerů pracovního postupu|Žádné dimenze|
|TotalBillableExecutions|Fakturovatelná spuštění celkem|Count|Celkem|Počet fakturovaných provedení pracovního postupu|Žádné dimenze|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Count|Celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Count|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Count|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádné dimenze|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Count|Celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Count|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Count|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádné dimenze|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšná spuštění|Count|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšná spuštění|Count|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunLatency|Latence spuštění|Sekundy|Average|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Average|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Události omezeného spuštění|Count|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|RunStartThrottledEvents|Omezené události spuštění|Count|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce |Count|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Average|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Average|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|TriggersStarted|Spuštěné aktivační události |Count|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Aktivační události dokončeny |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Aktivační události byly úspěšné |Count|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggersFailed|Neúspěšná triggery |Count|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Aktivační události přeskočeny|Count|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Aktivační události aktivovány |Count|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Average|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Average|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Average|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Percent|Average|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti workflowu pro prostředí integrační služby|Percent|Average|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí integrační služby|Percent|Average|Využití procesoru konektoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí integrační služby|Percent|Average|Využití paměti konektoru pro prostředí integrační služby.|Žádné dimenze|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Dokončená spuštění|Dokončená spuštění|Count|Celkem|Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen.|Scénář|
|Spuštěná spuštění|Spuštěná spuštění|Count|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář|
|Neúspěšná spuštění|Neúspěšná spuštění|Count|Celkem|Počet spuštění se pro tento pracovní prostor nezdařil.|Scénář|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/Accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Count|Count|Počet volání rozhraní API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostupnost|Dostupnost|Procento|Average|Dostupnost rozhraní API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageOtherLatency|Průměrná jiná latence|MS/op|Average|Průměrná druhá latence (která není pro čtení nebo zápis) v milisekundách na operaci|Žádné dimenze|
|AverageReadLatency|Průměrná latence čtení|MS/op|Average|Průměrná latence čtení v milisekundách na operaci|Žádné dimenze|
|AverageTotalLatency|Průměrná celková latence|MS/op|Average|Průměrná celková latence v milisekundách na operaci|Žádné dimenze|
|AverageWriteLatency|Průměrná latence zápisu|MS/op|Average|Průměrná latence zápisu v milisekundách na operaci|Žádné dimenze|
|FilesystemOtherOps|Jiná operace systému souborů|OPS|Average|Počet dalších operací systému souborů (které nejsou čteny nebo zapsány)|Žádné dimenze|
|FilesystemReadOps|Operace čtení systému souborů|OPS|Average|Počet operací čtení systému souborů|Žádné dimenze|
|FilesystemTotalOps|Celková operace systému souborů|OPS|Average|Součet všech operací systému souborů|Žádné dimenze|
|FilesystemWriteOps|Operace zápisu do systému souborů|OPS|Average|Počet operací zápisu do systému souborů|Žádné dimenze|
|IoBytesPerOtherOps|Vstupně-výstupní bajty na jiné operace|bajty/op|Average|Počet vstupně-výstupních bajtů na jiné operace (které nejsou čtením nebo zápisem)|Žádné dimenze|
|IoBytesPerReadOps|Vstupně-výstupní operace na operaci čtení|bajty/op|Average|Počet vstupně-výstupních bajtů na operaci čtení|Žádné dimenze|
|IoBytesPerTotalOps|Počet vstupně-výstupních bajtů za sekundu ve všech operacích|bajty/op|Average|Součet všech operací v/v bajtech|Žádné dimenze|
|IoBytesPerWriteOps|Vstupně-výstupní operace na operaci zápisu|bajty/op|Average|Počet vstupně-výstupních bajtů na operaci zápisu|Žádné dimenze|
|OtherIops|Další IOPS|operace za sekundu|Average|Další vstupně-výstupní operace za sekundu|Žádné dimenze|
|OtherThroughput|Jiná propustnost|MB/s|Average|Jiná propustnost (která není pro čtení nebo zápis) v megabajtech za sekundu|Žádné dimenze|
|ReadIops|Čtení IOPS|operace za sekundu|Average|Načíst vstupně-výstupní operace za sekundu|Žádné dimenze|
|ReadThroughput|Propustnost čtení|MB/s|Average|Propustnost čtení v megabajtech za sekundu|Žádné dimenze|
|TotalIops|Celkový počet IOPS|operace za sekundu|Average|Součet všech vstupně-výstupních operací za sekundu|Žádné dimenze|
|TotalThroughput|Celková propustnost|MB/s|Average|Součet veškeré propustnosti v megabajtech za sekundu|Žádné dimenze|
|VolumeAllocatedSize|Velikost přiděleného svazku|bajtů|Average|Přidělená velikost svazku (nejedná se o skutečně využité bajty)|Žádné dimenze|
|VolumeLogicalSize|Logická velikost svazku|bajtů|Average|Logická velikost svazku (použité bajty)|Žádné dimenze|
|VolumeSnapshotSize|Velikost snímku svazku|bajtů|Average|Velikost všech snímků ve svazku|Žádné dimenze|
|WriteIops|Zápis IOPS|operace za sekundu|Average|Zapsat vstupně-výstupní operace za sekundu|Žádné dimenze|
|WriteThroughput|Propustnost zápisu|MB/s|Average|Propustnost zápisu v megabajtech za sekundu|Žádné dimenze|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Přidělená velikost fondu svazků|bajtů|Average|Přidělená velikost fondu (ne skutečné využité bajty)|Žádné dimenze|
|VolumePoolAllocatedUsed|Využití přiděleného fondu svazků|bajtů|Average|Přidělená velikost fondu, která se používá|Žádné dimenze|
|VolumePoolTotalLogicalSize|Celková logická velikost fondu svazků|bajtů|Average|Součet logické velikosti všech svazků patřících do fondu|Žádné dimenze|
|VolumePoolTotalSnapshotSize|Celková velikost snímku fondu svazků|bajtů|Average|Součet všech snímků ve fondu|Žádné dimenze|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Count|Celkem|Počet bajtů, které síťové rozhraní odeslalo|Žádné dimenze|
|BytesReceivedRate|Přijaté bajty|Count|Celkem|Počet bajtů, které síťové rozhraní přijalo|Žádné dimenze|
|PacketsSentRate|Odeslané pakety|Count|Celkem|Počet paketů, které síťové rozhraní odeslalo|Žádné dimenze|
|PacketsReceivedRate|Přijaté pakety|Count|Celkem|Počet paketů, které síťové rozhraní přijalo|Žádné dimenze|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Dostupnost cesty k datům|Count|Average|Průměrná dostupnost cesty k datům Load Balancer za dobu trvání|FrontendIPAddress, FrontendPort|
|DipAvailability|Stav sondy stavu|Count|Average|Průměrný stav testu stavu Load Balancer za dobu trvání|Typprotokolu, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet odeslaných bajtů v časovém období|FrontendIPAddress, FrontendPort, směr|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet odeslaných paketů v časovém období|FrontendIPAddress, FrontendPort, směr|
|SYNCount|Počet SYN|Count|Celkem|Celkový počet odeslaných paketů SYN v časovém období|FrontendIPAddress, FrontendPort, směr|
|SnatConnectionCount|Počet připojení SNAT|Count|Celkem|Celkový počet nových připojení SNAT vytvořených během časového období|FrontendIPAddress, BackendIPAddress, vlastnost ConnectionState|
|AllocatedSnatPorts|Přidělené porty SNAT (Preview)|Count|Celkem|Celkový počet portů SNAT přidělených v rámci časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu|
|UsedSnatPorts|Využité porty SNAT (Preview)|Count|Celkem|Celkový počet portů SNAT používaných během časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Count|Celkem|Počet dotazů poskytovaných pro zónu DNS|Žádné dimenze|
|RecordSetCount|Počet sad záznamů|Count|Maximum|Počet sad záznamů v zóně DNS|Žádné dimenze|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Percent|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádné dimenze|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS příchozích paketů|CountPerSecond|Maximum|DDoS příchozích paketů|Žádné dimenze|
|PacketsDroppedDDoS|Vynechané příchozí pakety DDoS|CountPerSecond|Maximum|Vynechané příchozí pakety DDoS|Žádné dimenze|
|PacketsForwardedDDoS|DDoS předaných příchozích paketů|CountPerSecond|Maximum|DDoS předaných příchozích paketů|Žádné dimenze|
|TCPPacketsInDDoS|DDoS příchozí pakety TCP|CountPerSecond|Maximum|DDoS příchozí pakety TCP|Žádné dimenze|
|TCPPacketsDroppedDDoS|Zrušené příchozí pakety protokolu TCP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety protokolu TCP DDoS|Žádné dimenze|
|TCPPacketsForwardedDDoS|DDoS předaných paketů příchozího protokolu TCP|CountPerSecond|Maximum|DDoS předaných paketů příchozího protokolu TCP|Žádné dimenze|
|UDPPacketsInDDoS|Příchozí pakety UDP DDoS|CountPerSecond|Maximum|Příchozí pakety UDP DDoS|Žádné dimenze|
|UDPPacketsDroppedDDoS|Zrušené příchozí pakety UDP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety UDP DDoS|Žádné dimenze|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předané DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předané DDoS|Žádné dimenze|
|BytesInDDoS|Příchozí bajty DDoS|BytesPerSecond|Maximum|Příchozí bajty DDoS|Žádné dimenze|
|BytesDroppedDDoS|Příchozí bajty vynechané DDoS|BytesPerSecond|Maximum|Příchozí bajty vynechané DDoS|Žádné dimenze|
|BytesForwardedDDoS|Příchozí bajty předané DDoS|BytesPerSecond|Maximum|Příchozí bajty předané DDoS|Žádné dimenze|
|TCPBytesInDDoS|Příchozí bajty DDoS TCP|BytesPerSecond|Maximum|Příchozí bajty DDoS TCP|Žádné dimenze|
|TCPBytesDroppedDDoS|Příchozí bajty protokolu TCP vyhozené DDoS|BytesPerSecond|Maximum|Příchozí bajty protokolu TCP vyhozené DDoS|Žádné dimenze|
|TCPBytesForwardedDDoS|Příchozí DDoS předaných bajtů protokolu TCP|BytesPerSecond|Maximum|Příchozí DDoS předaných bajtů protokolu TCP|Žádné dimenze|
|UDPBytesInDDoS|Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP DDoS|Žádné dimenze|
|UDPBytesDroppedDDoS|Zrušené Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Zrušené Příchozí bajty UDP DDoS|Žádné dimenze|
|UDPBytesForwardedDDoS|Příchozí DDoS předávaných bajtů UDP|BytesPerSecond|Maximum|Příchozí DDoS předávaných bajtů UDP|Žádné dimenze|
|IfUnderDDoSAttack|V části útok DDoS nebo ne|Count|Maximum|V části útok DDoS nebo ne|Žádné dimenze|
|DDoSTriggerTCPPackets|Příchozí pakety TCP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci zmírnění DDoS|Žádné dimenze|
|DDoSTriggerUDPPackets|Příchozí pakety UDP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci zmírnění DDoS|Žádné dimenze|
|DDoSTriggerSYNPackets|Příchozí pakety SYN pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci zmírnění DDoS|Žádné dimenze|
|VipAvailability|Dostupnost cesty k datům|Count|Average|Průměrná dostupnost IP adresy za dobu trvání|Port|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet odeslaných bajtů v časovém období|Port, směr|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet odeslaných paketů v časovém období|Port, směr|
|SynCount|Počet SYN|Count|Celkem|Celkový počet odeslaných paketů SYN v časovém období|Port, směr|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet volání pravidel aplikace|Count|Celkem|Počet přístupů k pravidlům aplikace|Stav, důvod, protokol|
|NetworkRuleHit|Počet volání síťových pravidel|Count|Celkem|Počet přístupů k síťovým pravidlům|Stav, důvod, protokol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Celkem|Počet bajtů za sekundu, které Application Gateway zasloužily|Žádné dimenze|
|UnhealthyHostCount|Počet hostitelů není v pořádku|Count|Average|Počet nezdravých hostitelů back-endu|BackendSettingsPool|
|healthyHostCount|Počet hostitelů v pořádku|Count|Average|Počet nefunkčních hostitelů back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Count|Celkem|Počet úspěšných požadavků, které Application Gateway obsluhovány|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Count|Celkem|Počet neúspěšných žádostí, které Application Gateway obsluhovány|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Count|Celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální připojení|Count|Celkem|Počet aktuálních připojení vytvořených pomocí Application Gateway|Žádné dimenze|
|CapacityUnits|Aktuální jednotky kapacity|Count|Average|Spotřebované jednotky kapacity|Žádné dimenze|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Šířka pásma S2S brány|BytesPerSecond|Average|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádné dimenze|
|P2SBandwidth|Šířka pásma P2S brány|BytesPerSecond|Average|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Počet připojení P2S|Count|Maximum|Počet připojení typu Point-to-site brány|Protocol|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Average|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Výstupní bajty tunelu|B|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Vstupní bajty tunelového propojení|B|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Odchozí tunelové pakety|Count|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunelové pakety pro příchozí připojení|Count|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Odpojení odchozího paketu o neshodě tunelového propojení TS|Count|Celkem|Počet odchozích odkládacích paketů z výběrů neshody tunelového propojení|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Přetažení paketů neshody TS s tunelem pro příchozí přenosy|Count|Celkem|Počet odkládacích paketů ze seznamu neshody pro výběr odchozího přenosu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Počet příchozích přenosů Azure za sekundu v bitech|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS – výstup Azure za sekundu|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Počet příchozích přenosů Azure za sekundu v bitech|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS – výstup Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Počet příchozích přenosů Azure za sekundu v bitech|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|BITS – výstup Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle vráceného koncového bodu|Count|Celkem|Počet vrácených Traffic Managerho koncového bodu v daném časovém rámci|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu podle koncového bodu|Count|Maximum|1, pokud je stav testu koncového bodu zapnuto, 0 jinak.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% PROBE selhalo|Percent|Average|% sond monitorování připojení selhalo.|Žádné dimenze|
|AverageRoundtripMs|Střední Doba odezvy (MS)|Milisekund|Average|Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem|Žádné dimenze|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Count|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|requestSize|Velikost požadavku|B|Celkem|Počet bajtů odeslaných jako požadavek od klientů na proxy server HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|responseSize|Velikost odpovědi|B|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Count|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S do back-endu|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latence žádosti back-endu|Milisekund|Average|Čas vypočítaný z doby, kdy byl požadavek odeslán proxy HTTP/S do back-endu do back-endu, dokud proxy protokolu HTTP/S nedostalo poslední bajt odpovědi z back-endu|Back-end|
|TotalLatency|Celková latence|Milisekund|Average|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stavu back-endu|Percent|Average|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endu|Back-end, problémových|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Count|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registrace. vše|Operace registrace|Count|Celkem|Počet všech úspěšných operací registrace (vytváření dotazů a odstraňování aktualizací). |Žádné dimenze|
|registration.create|Operace vytvoření registrace|Count|Celkem|Počet všech úspěšných vytvoření registrace.|Žádné dimenze|
|registration.update|Operace aktualizace registrace|Count|Celkem|Počet všech úspěšných aktualizací registrace.|Žádné dimenze|
|registration.get|Registrace – operace čtení|Count|Celkem|Počet všech úspěšných dotazů na registraci.|Žádné dimenze|
|registration.delete|Registrace – operace odstranění|Count|Celkem|Počet všech úspěšných odstranění registrací.|Žádné dimenze|
|přijíman|Příchozí zprávy|Count|Celkem|Počet všech úspěšných volání rozhraní API pro odeslání. |Žádné dimenze|
|incoming.scheduled|Odeslaná naplánovaná nabízená oznámení|Count|Celkem|Plánovaná nabízená oznámení zrušena|Žádné dimenze|
|incoming.scheduled.cancel|Plánovaná nabízená oznámení zrušena|Count|Celkem|Plánovaná nabízená oznámení zrušena|Žádné dimenze|
|naplánováno. čeká na|Nedokončená plánovaná oznámení|Count|Celkem|Nedokončená plánovaná oznámení|Žádné dimenze|
|instalace. vše|Operace správy instalace|Count|Celkem|Operace správy instalace|Žádné dimenze|
|installation.get|Získat operace instalace|Count|Celkem|Získat operace instalace|Žádné dimenze|
|installation.upsert|Operace vytvoření nebo aktualizace instalace|Count|Celkem|Operace vytvoření nebo aktualizace instalace|Žádné dimenze|
|instalace. patch|Operace instalace opravy|Count|Celkem|Operace instalace opravy|Žádné dimenze|
|installation.delete|Odstranit operace instalace|Count|Celkem|Odstranit operace instalace|Žádné dimenze|
|odchozí. allpns. úspěch|Úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. allpns. invalidpayload|Chyby datové části|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS vrátil chybnou datovou část.|Žádné dimenze|
|odchozí. allpns. pnserror|Chyby systému externích oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože došlo k potížím při komunikaci s PNS (nezahrnuje problémy s ověřováním).|Žádné dimenze|
|outgoing.allpns.channelerror|Chyby kanálu|Count|Celkem|Počet nabízených oznámení, která selhala, protože kanál byl neplatný, ale není přidružený ke správné aplikaci nebo vypršela její platnost.|Žádné dimenze|
|outgoing.allpns.badorexpiredchannel|Chybné nebo prošlé chyby kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršela platnost kanálu/tokenu nebo registrationId, nebo je neplatná.|Žádné dimenze|
|odchozí. WNS. úspěch|WNS úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. WNS. invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované. (Windows Live nerozpoznává přihlašovací údaje.)|Žádné dimenze|
|outgoing.wns.badchannel|Chyba WNS špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav WNS: 404 nebyl nalezen).|Žádné dimenze|
|outgoing.wns.expiredchannel|Chyba kanálu vypršení platnosti WNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože platnost parametr channeluri vypršela (stav WNS: 410 zmizelo).|Žádné dimenze|
|odchozí. WNS. omezení|WNS omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože WNS omezuje tuto aplikaci (stav WNS: 406 není přijatelné).|Žádné dimenze|
|outgoing.wns.tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Count|Celkem|Systém Windows Live není dostupný.|Žádné dimenze|
|odchozí. WNS. invalidtoken|WNS – chyby autorizace (neplatný token)|Count|Celkem|Token poskytnutý pro WNS není platný (stav WNS: 401 Neautorizováno).|Žádné dimenze|
|outgoing.wns.wrongtoken|WNS chyby autorizace (špatný token)|Count|Celkem|Token poskytnutý pro WNS je platný, ale pro jinou aplikaci (stav WNS: 403 zakázáno). K tomu může dojít, pokud je parametr channeluri v registraci přidružen k jiné aplikaci. Ověřte, že je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádné dimenze|
|odchozí. WNS. invalidnotificationformat|WNS neplatný formát oznámení|Count|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že WNS neumožňuje odmítat všechny neplatné datové části.|Žádné dimenze|
|odchozí. WNS. invalidnotificationsize|WNSá Chyba neplatné velikosti oznámení|Count|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádné dimenze|
|outgoing.wns.channelthrottled|WNS kanál – omezení|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-NotificationStatus: channelThrottled).|Žádné dimenze|
|odchozí. WNS. channeldisconnected|WNS kanál odpojen|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|outgoing.wns.dropped|WNS Vyřazená oznámení|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (X-WNS-NotificationStatus: vyřazeno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|odchozí. WNS. pnserror|WNS chyby|Count|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s WNS.|Žádné dimenze|
|outgoing.wns.authenticationerror|WNS chyby ověřování|Count|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje nebo chybný token.|Žádné dimenze|
|outgoing.apns.success|Úspěšná oznámení APNS|Count|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.apns.invalidcredentials|Chyby autorizace APNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|outgoing.apns.badchannel|Chyba služby APN Bad Channel|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (kód stavu binárního protokolu APNS: 8. Stavový kód protokolu HTTP služby APN: 400 s "BadDeviceToken").|Žádné dimenze|
|outgoing.apns.expiredchannel|Chyba kanálu vypršení platnosti služby APNS|Count|Celkem|Počet tokenů, u kterých se zrušila platnost kanálu zpětné vazby APNS.|Žádné dimenze|
|odchozí. APNs. invalidnotificationsize|APN – Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (Stavový kód binárního protokolu APNS: 7).|Žádné dimenze|
|outgoing.apns.pnserror|Chyby služby APN|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádné dimenze|
|outgoing.gcm.success|GCM úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. GCM. invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|outgoing.gcm.badchannel|Chyba GCM špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci nebyl rozpoznán (výsledek GCM: Neplatná registrace.|Žádné dimenze|
|outgoing.gcm.expiredchannel|Chyba kanálu vypršení platnosti GCM|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádné dimenze|
|odchozí. GCM. omezení|GCM omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože aplikace GCM omezila tuto aplikaci (Stavový kód GCM: 501-599 nebo výsledek: nedostupný)|Žádné dimenze|
|odchozí. GCM. invalidnotificationformat|GCM neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část nebyla správně naformátována (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádné dimenze|
|odchozí. GCM. invalidnotificationsize|GCMá Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádné dimenze|
|outgoing.gcm.wrongchannel|GCM chybná chyba kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružená k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádné dimenze|
|outgoing.gcm.pnserror|GCM chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádné dimenze|
|outgoing.gcm.authenticationerror|GCM chyby ověřování|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje. přihlašovací údaje jsou blokované nebo SenderId není v aplikaci správně nakonfigurovaný (výsledek GCM: MismatchedSenderId).|Žádné dimenze|
|outgoing.mpns.success|MPNS úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.mpns.invalidcredentials|Neplatné přihlašovací údaje MPNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|outgoing.mpns.badchannel|Chyba MPNS špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav MPNS: 404 nebyl nalezen).|Žádné dimenze|
|outgoing.mpns.throttled|MPNS omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 není přijatelné).|Žádné dimenze|
|odchozí. MPNS. invalidnotificationformat|MPNS neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část oznámení byla příliš velká.|Žádné dimenze|
|outgoing.mpns.channeldisconnected|MPNS kanál odpojen|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci byl odpojen (stav MPNS: 412 nebyl nalezen).|Žádné dimenze|
|outgoing.mpns.dropped|MPNS Vyřazená oznámení|Count|Celkem|Počet nabídek, které byly vyřazeny pomocí MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo potlačeno).|Žádné dimenze|
|outgoing.mpns.pnserror|MPNS chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádné dimenze|
|outgoing.mpns.authenticationerror|MPNS chyby ověřování|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|notificationhub. push|Všechna odchozí oznámení|Count|Celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|incoming.all.requests|Všechny příchozí žádosti|Count|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádné dimenze|
|příchozí. All. failedrequests|Všechny příchozí neúspěšné požadavky|Count|Celkem|Celkový počet příchozích neúspěšných žádostí pro Centrum oznámení|Žádné dimenze|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_% Free uzlů inode|% Bezplatného uzlů inode|Count|Average|Average_% Free uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Count|Average|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využití uzlů inode|% Použitého uzlů inode|Count|Average|Average_% využití uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ využité místo|% Využitého místa|Count|Average|Average_ využité místo|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přečtených bajtů za sekundu|Bajty čtení z disku/s|Count|Average|Average_Disk přečtených bajtů za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení disku/s|Count|Average|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosy za sekundu|Přenosy disku/s|Count|Average|Average_Disk přenosy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapsané bajty/s|Bajty zapisování na disk/s|Count|Average|Average_Disk zapsané bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zápisy za sekundu|Zápis disku/s|Count|Average|Average_Disk zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Count|Average|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical disk bajty/s|Bajtů logického disku/s|Count|Average|Average_Logical disk bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ dostupná paměť v%|% Dostupné paměti|Count|Average|Average_ dostupná paměť v%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Count|Average|Average_% dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ využité paměti|% Využité paměti|Count|Average|Average_ využité paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využitého místa odkládacího souboru|% Využitého místa odkládacího souboru|Count|Average|Average_% využitého místa odkládacího souboru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Available v MB|Dostupná paměť v MB|Count|Average|Paměť Average_Available v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB – swap|Dostupný počet MB swap|Count|Average|Average_Available MB – swap|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page čtení za sekundu|Čtení stránek/s|Count|Average|Average_Page čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zápisy za sekundu|Zápisy stránek/s|Count|Average|Average_Page zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stránky/s|Count|Average|Average_Pages/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used počet MB odkládacího prostoru|Využité místo odkládacího souboru v MB|Count|Average|Average_Used počet MB odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Used MB paměti|Využitá paměť v MB|Count|Average|Paměť Average_Used MB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přenesené bajty Average_Total|Celkový počet bajtů přenesených|Count|Average|Přenesené bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Count|Average|Přijaté bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Bajty celkem|Count|Average|Average_Total bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané pakety Average_Total|Celkový počet odeslaných paketů|Count|Average|Odeslané pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté pakety Average_Total|Celkový počet přijatých paketů|Count|Average|Přijaté pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total chyby příjmu|Celkový počet chyb příjmu|Count|Average|Average_Total chyby příjmu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Chyby Average_Total TX|Chyby odesílání celkem|Count|Average|Chyby Average_Total TX|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Count|Average|Average_Total kolizí|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Average|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední Doba disku/přenos|Count|Average|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Average|Average_Avg. Doby disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical disk bajty/s|Bajty fyzického disku/s|Count|Average|Average_Physical disk bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegovaného času|Privilegovaný čas protokolu PCT|Count|Average|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_Pct|Doba uživatele v protokolu PCT|Count|Average|Čas uživatele Average_Pct|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used kB paměti|Využitá paměť v kilobajtech|Count|Average|Average_Used kB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Sdílená paměť Average_Virtual|Virtuální sdílená paměť|Count|Average|Sdílená paměť Average_Virtual|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času DPC|% Času DPC|Count|Average|Average_% času DPC|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času nečinnosti|% Času nečinnosti|Count|Average|Average_% času nečinnosti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času přerušení|% Času přerušení|Count|Average|Average_% času přerušení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času čekání na v/v|% Času čekání na v/v|Count|Average|Average_% času čekání na v/v|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dobrý čas|% Dobrý čas|Count|Average|Average_% dobrý čas|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegovaného času|% Privilegovaného času|Count|Average|Average_% privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|% Času procesoru|Count|Average|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% uživatelského času|% Uživatelského času|Count|Average|Average_% uživatelského času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fyzická paměť|Volná fyzická paměť|Count|Average|Average_Free fyzická paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free místo ve stránkovacích souborech|Volné místo ve stránkovacích souborech|Count|Average|Average_Free místo ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Virtuální paměť Average_Free|Volná virtuální paměť|Count|Average|Virtuální paměť Average_Free|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Count|Average|Average_Processes|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size uložené ve stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Count|Average|Average_Size uložené ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Doba provozu|Count|Average|Average_Uptime|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Uživatelé|Count|Average|Average_Users|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Average|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Average|Average_Avg. Doby disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Count|Average|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení disku/s|Count|Average|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosy za sekundu|Přenosy disku/s|Count|Average|Average_Disk přenosy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zápisy za sekundu|Zápis disku/s|Count|Average|Average_Disk zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Count|Average|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Count|Average|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Počet MB k dispozici|Count|Average|Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% používané svěřené bajty|% Používaných potvrzených bajtů|Count|Average|Average_% používané svěřené bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes přijaté za sekundu|Přijaté bajty/s|Count|Average|Average_Bytes přijaté za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes odeslané za sekundu|Odeslané bajty/s|Count|Average|Average_Bytes odeslané za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Count|Average|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|% Času procesoru|Count|Average|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Count|Average|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Prezenční signál|Prezenční signál|Count|Celkem|Prezenční signál|Počítač, OSType, verze, SourceComputerId|
|Aktualizace|Aktualizace|Count|Average|Aktualizace|Počítač, produkt, klasifikace, UpdateState, volitelné, schválené|
|Událost|Událost|Count|Average|Událost|Zdroj, protokol událostí, počítač, EventCategory, EventLevel, EventLevelName, ID události|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekundy|Average|Doba trvání dotazu DAX v posledním intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vláken Délka fronty úloh fondu dotazů|Count|Average|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|QPU vysoké využití|Count|Celkem|QPU vysoké využití za poslední minutu, 1 pro vysoké využití QPU, jinak 0|Žádné dimenze|
|memory_metric|Memory (Paměť)|B|Average|Rezident. Rozsah 0-3 GB pro a1, 0-5 GB pro a2, 0-10 GB pro a3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Percent|Average|Průměrná velikost thrashing paměti|Žádné dimenze|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections – úspěch|ListenerConnections – úspěch|Count|Celkem|Úspěšně se ListenerConnections pro Microsoft. Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Celkem|ClientError v ListenerConnections pro Microsoft. Relay|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Celkem|ServerError v ListenerConnections pro Microsoft. Relay|EntityName|
|SenderConnections – úspěch|SenderConnections – úspěch|Count|Celkem|Úspěšně se SenderConnections pro Microsoft. Relay.|EntityName|
|SenderConnections – ClientError|SenderConnections – ClientError|Count|Celkem|ClientError v SenderConnections pro Microsoft. Relay|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Celkem|ServerError v SenderConnections pro Microsoft. Relay|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Celkem|Total ListenerConnections for Microsoft. Relay|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Celkem|Požadavky SenderConnections (celkem) pro Microsoft. Relay|EntityName|
|ActiveConnections|ActiveConnections|Count|Celkem|Total ActiveConnections for Microsoft. Relay|EntityName|
|ActiveListeners|ActiveListeners|Count|Celkem|Total ActiveListeners for Microsoft. Relay|EntityName|
|BytesTransferred|BytesTransferred|Count|Celkem|Total BytesTransferred for Microsoft. Relay|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Celkem|Total ListenerDisconnects for Microsoft. Relay|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Celkem|Total SenderDisconnects for Microsoft. Relay|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence hledání|Sekundy|Average|Průměrná latence hledání pro vyhledávací službu|Žádné dimenze|
|SearchQueriesPerSecond|Vyhledávací dotazy za sekundu|CountPerSecond|Average|Hledání dotazů za sekundu pro vyhledávací službu|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Percent|Average|Procento vyhledávacích dotazů, které byly pro vyhledávací službu omezené|Žádné dimenze|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů (Preview)|EntityName|
|ServerErrors|Chyby serveru. (Preview)|Count|Celkem|Chyby serveru pro Microsoft. ServiceBus (Preview)|EntityName|
|UserErrors|Chyby uživatele. (Preview)|Count|Celkem|Chyby uživatele pro Microsoft. ServiceBus (Preview)|EntityName|
|ThrottledRequests|Omezené požadavky. (Preview)|Count|Celkem|Omezené požadavky pro Microsoft. ServiceBus (Preview)|EntityName|
|IncomingRequests|Příchozí žádosti (Preview)|Count|Celkem|Příchozí požadavky pro Microsoft. ServiceBus (Preview)|EntityName|
|IncomingMessages|Příchozí zprávy (Preview)|Count|Celkem|Příchozí zprávy pro Microsoft. ServiceBus (Preview)|EntityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|Celkem|Odchozí zprávy pro Microsoft. ServiceBus (Preview)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Count|Celkem|Celkový počet aktivních připojení pro Microsoft. ServiceBus (Preview)|Žádné dimenze|
|Size|Velikost (Preview)|B|Average|Velikost fronty nebo tématu v bajtech (Preview)|EntityName|
|Zprávy|Počet zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet zpráv ve frontě nebo tématu. (Preview)|EntityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|EntityName|
|DeadletteredMessages|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|Count|Average|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|EntityName|
|ScheduledMessages|Počet naplánovaných zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet naplánovaných zpráv ve frontě nebo tématu. (Preview)|EntityName|
|CPUXNS|Využití CPU na obor názvů|Percent|Maximum|Metrika využití procesoru oboru názvů služby Service Bus úrovně Premium|Žádné dimenze|
|WSXNS|Využití velikosti paměti na obor názvů|Percent|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium|Žádné dimenze|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Procesor přidělený tomuto kontejneru v millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|B|Average|Paměť přidělená tomuto kontejneru v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Skutečné využití CPU v millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|B|Average|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Stav aplikace Service Fabric mřížka|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Average|Stav služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Stav repliky služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Stav kontejneru v aplikaci Service Fabric mřížka|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Count|Average|Restartování počtu kontejnerů v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Count|Maximum|Množství připojení uživatele.|Koncový bod|
|MessageCount|Počet zpráv|Count|Celkem|Celková velikost zpráv|Žádné dimenze|
|InboundTraffic|Příchozí provoz|B|Celkem|Příchozí provoz služby|Žádné dimenze|
|OutboundTraffic|Odchozí provoz|B|Celkem|Odchozí provoz služby|Žádné dimenze|
|UserErrors|Chyby uživatele|Percent|Maximum|Procento uživatelských chyb|Žádné dimenze|
|SystemErrors|Systémové chyby|Percent|Maximum|Procento systémových chyb|Žádné dimenze|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Percent|Average|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento V/V dat|Percent|Average|Procento V/V dat|Žádné dimenze|
|log_write_percent|Procento V/V protokolu|Percent|Average|Procentní hodnota protokolu v/v. Neplatí pro datové sklady.|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Average|Procento DTU Platí pro databáze založené na DTU.|Žádné dimenze|
|úložiště|Využitý datový prostor|B|Maximum|Celková velikost databáze Neplatí pro datové sklady.|Žádné dimenze|
|connection_successful|Úspěšná připojení|Count|Celkem|Úspěšná připojení|Žádné dimenze|
|connection_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|blocked_by_firewall|Blokováno bránou firewall|Count|Celkem|Blokováno bránou firewall|Žádné dimenze|
|Ukončení|Zablokování|Count|Celkem|Zablokování. Neplatí pro datové sklady.|Žádné dimenze|
|storage_percent|Procento využitého datového prostoru|Percent|Maximum|Procento velikosti databáze. Neplatí pro datové sklady nebo databáze na úrovni dat.|Žádné dimenze|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Percent|Average|Procentuální hodnota úložiště OLTP v paměti Neplatí pro datové sklady.|Žádné dimenze|
|workers_percent|Procento prac. procesů|Percent|Average|Procento pracovních procesů Neplatí pro datové sklady.|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Average|Procento relací Neplatí pro datové sklady.|Žádné dimenze|
|dtu_limit|Limit DTU|Count|Average|Limit DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|dtu_used|Využité DTU|Count|Average|Používá se DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Average|Limit procesoru. Platí pro databáze založené na vCore.|Žádné dimenze|
|cpu_used|Využitý procesor|Count|Average|Využitý procesor. Platí pro databáze založené na vCore.|Žádné dimenze|
|dwu_limit|DWU limit|Count|Maximum|DWU limit. Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_consumption_percent|Procento DWU|Percent|Maximum|Procento DWU Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_used|DWU použito|Count|Maximum|DWU použito. Platí jenom pro datové sklady.|Žádné dimenze|
|dw_cpu_percent|Procento CPU na úrovni uzlu DW|Percent|Average|Procento CPU na úrovni uzlu DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Procento vstupně-výstupních operací dat na úrovni uzlu DW|Percent|Average|Procento vstupně-výstupních operací dat na úrovni uzlu DW|DwLogicalNodeId|
|cache_hit_percent|Procento přístupů do mezipaměti|Percent|Maximum|Procento přístupů do mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|cache_used_percent|Procento využité mezipaměti|Percent|Maximum|Procento využité mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|local_tempdb_usage_percent|Místní procento databáze tempdb|Percent|Average|Místní procento databáze tempdb. Platí jenom pro datové sklady.|Žádné dimenze|
|app_cpu_billed|Aplikace fakturovaná podle procesoru|Count|Celkem|CPU aplikace se fakturuje. Platí pro databáze bez serveru.|Žádné dimenze|
|app_cpu_percent|Procento využití procesoru aplikací|Percent|Average|Procento využití procesoru aplikací Platí pro databáze bez serveru.|Žádné dimenze|
|app_memory_percent|Procento využité paměti aplikace|Percent|Average|Procento využité paměti aplikace Platí pro databáze bez serveru.|Žádné dimenze|
|allocated_data_storage|Přidělený datový prostor|B|Average|Přidělené datové místo Neplatí pro datové sklady.|Žádné dimenze|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Percent|Average|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento V/V dat|Percent|Average|Procento V/V dat|Žádné dimenze|
|log_write_percent|Procento V/V protokolu|Percent|Average|Procento V/V protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Average|Procento DTU Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_percent|Procento využitého datového prostoru||Percent|Average|Procento úložiště|Žádné dimenze|
|workers_percent|Procento prac. procesů|Percent|Average|Procento prac. procesů|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Average|Procento relací|Žádné dimenze|
|eDTU_limit|limit eDTU|Count|Average|limit eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_limit|Maximální velikost dat|B|Average|Omezení úložiště|Žádné dimenze|
|eDTU_used|využité eDTU|Count|Average|používá se eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_used|Využitý datový prostor|B|Average|Využité úložiště|Žádné dimenze|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Percent|Average|Procentuální hodnota úložiště OLTP v paměti|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Average|Limit procesoru. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|cpu_used|Využitý procesor|Count|Average|Využitý procesor. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|allocated_data_storage|Přidělený datový prostor|B|Average|Přidělený datový prostor|Žádné dimenze|
|allocated_data_storage_percent|Procentuální hodnota přiděleného datového prostoru|Percent|Maximum|Procentuální hodnota přiděleného datového prostoru|Žádné dimenze|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Count|Average|Počet virtuálních jader|Žádné dimenze|
|avg_cpu_percent|Průměrné procento procesoru|Percent|Average|Průměrné procento procesoru|Žádné dimenze|
|reserved_storage_mb|Rezervované místo v úložišti|Count|Average|Rezervované místo v úložišti|Žádné dimenze|
|storage_space_used_mb|Využité místo úložiště|Count|Average|Využité místo úložiště|Žádné dimenze|
|io_requests|Počet požadavků v/v|Count|Average|Počet požadavků v/v|Žádné dimenze|
|io_bytes_read|Přečtené vstupně-výstupní bajty|B|Average|Přečtené vstupně-výstupní bajty|Žádné dimenze|
|io_bytes_written|Zapsané vstupně-výstupní bajty|B|Average|Zapsané vstupně-výstupní bajty|Žádné dimenze|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Použitá kapacita|B|Průměr|Kapacita využitá účtem|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita služby Blob|B|Average|Velikost úložiště využitá službou Blob Service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů blob|Počet|Celkem|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType|       |BlobCount|Počet objektů blob|Count|Average|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů služby Blob|Počet|Průměr|Počet kontejnerů ve službě Blob Service účtu úložiště|Žádné dimenze|
|IndexCapacity|Kapacita indexu|B|Average|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita služby File|B|Average|Velikost úložiště využitá službou File účtu úložiště v bajtech|Žádné dimenze|
|FileCount|Počet souborů|Count|Average|Počet souborů v Souborové službě účtu úložiště.|Žádné dimenze|
|FileShareCount|Počet sdílených složek služby File|Count|Average|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita služby Queue|B|Průměr|Velikost úložiště využitá službou Queue účtu úložiště v bajtech|Žádné dimenze|
|QueueCount|Počet front|Počet|Průměr|Počet front ve službě Queue účtu úložiště|Žádné dimenze|
|QueueMessageCount|Počet zpráv fronty|Počet|Průměr|Přibližný počet zpráv fronty ve službě Queue účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita služby Table|B|Průměr|Velikost úložiště využitá službou Table Service účtu úložiště v bajtech|Žádné dimenze|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek ve službě Table Service účtu úložiště|Žádné dimenze|
|TableEntityCount|Počet entit tabulek|Počet|Průměr|Počet entit tabulek ve službě Table Service účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|Typ, ApiName, ověřování|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Výsledek relace synchronizace|Count|Average|Metrika, která protokoluje hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Count|Celkem|Počet synchronizovaných souborů|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Count|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synchronizované bajty|B|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Count|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když registrovaný server úspěšně zaznamená prezenční signál s koncovým bodem cloudu|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Stažení vrstvení cloudu|B|Celkem|Celková velikost dat vrácených serverem|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|% využití SU|Percent|Maximum|% využití SU|ID logického, PartitionId|
|InputEvents|Vstupní události|Count|Celkem|Vstupní události|ID logického, PartitionId|
|InputEventBytes|Bajty vstupních událostí|B|Celkem|Bajty vstupních událostí|ID logického, PartitionId|
|LateInputEvents|Pozdní vstupní události|Count|Celkem|Pozdní vstupní události|ID logického, PartitionId|
|OutputEvents|Výstupní události|Count|Celkem|Výstupní události|ID logického, PartitionId|
|ConversionErrors|Chyby převodu dat|Count|Celkem|Chyby převodu dat|ID logického, PartitionId|
|Chyby|Chyby za běhu|Count|Celkem|Chyby za běhu|ID logického, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Count|Celkem|Události mimo pořadí|ID logického, PartitionId|
|AMLCalloutRequests|Požadavky na funkce|Count|Celkem|Požadavky na funkce|ID logického, PartitionId|
|AMLCalloutFailedRequests|Nezdařené požadavky na funkce|Count|Celkem|Nezdařené požadavky na funkce|ID logického, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Count|Celkem|Události funkcí|ID logického, PartitionId|
|DeserializationError|Chyby deserializace vstupu|Count|Celkem|Chyby deserializace vstupu|ID logického, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Count|Celkem|Události předčasného vstupu|ID logického, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|ID logického, PartitionId|
|InputEventsSourcesBacklogged|Vstupní události v backlogu|Count|Maximum|Vstupní události v backlogu|ID logického, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Count|Celkem|Přijaté vstupní zdroje|ID logického, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Count|Celkem|Počet zpráv načtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Count|Celkem|Počet neplatných zpráv přečtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|B|Celkem|Počet přečtených bajtů ze všech zdrojů událostí|Žádné dimenze|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|B|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Count|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Count|Average|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávaných v příchozím přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Count|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Count|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Count|Celkem|Počet zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Count|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|B|Celkem|Počet přečtených bajtů ze zdroje události|Žádné dimenze|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|B|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Count|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Count|Average|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávaných v příchozím přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Count|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Count|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty čtení z disku/s|BytesPerSecond|Average|Průměrná propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|DiskWriteBytesPerSecond|Bajty zápisu na disk/s|BytesPerSecond|Average|Průměrná propustnost disku z důvodu operací zápisu v období vzorkování.|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|B|Celkem|Celková propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|B|Celkem|Celková propustnost disku v důsledku operací zápisu v období vzorkování.|Žádné dimenze|
|DiskReadOperations|Operace čtení z disku|Count|Celkem|Počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskWriteOperations|Operace zápisu na disk|Count|Celkem|Počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Average|Průměrný počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Average|Průměrný počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskReadLatency|Latence čtení disku|Milisekundy|Average|Celková latence čtení Součet latencí čtení zařízení a jádra.|Žádné dimenze|
|DiskWriteLatency|Latence zápisu na disk|Milisekundy|Average|Celková latence zápisu Součet latencí zápisu zařízení a jádra.|Žádné dimenze|
|NetworkInBytesPerSecond|Síť v bajtech/s|BytesPerSecond|Average|Průměrná propustnost sítě pro přijatý provoz.|Žádné dimenze|
|NetworkOutBytesPerSecond|Výstupní bajty sítě/s|BytesPerSecond|Average|Průměrná propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|Celkem|Celková propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|MemoryUsed|Využitá paměť|B|Average|Velikost paměti počítače, kterou virtuální počítač používá.|Žádné dimenze|
|MemoryGranted|Přidělená paměť|B|Average|Velikost paměti, která byla k virtuálnímu počítači udělena hostitelem. Hostiteli není pro hostitele udělené, dokud se nedotknete jednou, a pokud VMkernel potřebuje paměť, může se tato paměť vyměnit.|Žádné dimenze|
|MemoryActive|Paměť aktivní|B|Average|Velikost paměti, kterou virtuální počítač využíval v posledních malých oknech času. Toto je "pravdivý" počet paměti, které virtuální počítač v současnosti potřebuje. Dodatečná, nevyužitá paměť může být vyměněna nebo v bublině bez dopadu na výkon hosta.|Žádné dimenze|
|Procento CPU|Procento CPU|Procento|Average|Využití procesoru. Tato hodnota je hlášena s 100%, která představuje všechny jádra procesoru v systému. Příklad: oboustranný virtuální počítač, který používá 50% systému se čtyřmi jádry, plně používá dvě jádra.|Žádné dimenze|
|PercentageCpuReady|Procento připraveného procesoru|Milisekundy|Celkem|Čas připravenosti je doba, po kterou se bude čekat na dostupnost PROCESORů v minulém intervalu aktualizace.|Žádné dimenze|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|
|DiskQueueLength|Délka fronty disku|Count|Average|Délka fronty disku|instance|
|HttpQueueLength|Délka fronty HTTP|Count|Average|Délka fronty HTTP|instance|
|BytesReceived|Vstupní data|B|Celkem|Vstupní data|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/weby (kromě funkcí)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|instance|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Vstupní data|B|Celkem|Vstupní data|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|instance|
|Http406|HTTP 406|Count|Celkem|HTTP 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru HTTP|Count|Celkem|Chyby serveru HTTP|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|AppConnections|Připojení|Count|Average|Připojení|instance|
|Řeší|Počet popisovačů|Count|Average|Počet popisovačů|instance|
|Vlákna|Počet vláken|Count|Average|Počet vláken|instance|
|PrivateBytes|Privátní bajty|B|Average|Privátní bajty|instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|Celkem|V/V – přečtené bajty za sekundu|instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|Celkem|V/V – zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|Celkem|V/V – ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|Celkem|V/V – operace čtení za sekundu|instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|Celkem|V/V – operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|Celkem|V/V – ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Average|Celkový počet uvolněných domén aplikace|instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|Celkem|Uvolnění paměti 0. generace|instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|Celkem|Uvolnění paměti 1. generace|instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|Celkem|Uvolnění paměti 2. generace|instance|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/lokality (funkce)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Vstupní data|B|Celkem|Vstupní data|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http5xx|Chyby serveru HTTP|Count|Celkem|Chyby serveru HTTP|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|MB/milisekundy|Celkem|[Jednotky spuštění funkce](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|instance|
|PrivateBytes|Privátní bajty|B|Average|Privátní bajty|instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|Celkem|V/V – přečtené bajty za sekundu|instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|Celkem|V/V – zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|Celkem|V/V – ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|Celkem|V/V – operace čtení za sekundu|instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|Celkem|V/V – operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|Celkem|V/V – ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Average|Celkový počet uvolněných domén aplikace|instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|Celkem|Uvolnění paměti 0. generace|instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|Celkem|Uvolnění paměti 1. generace|instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|Celkem|Uvolnění paměti 2. generace|instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|instance|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Vstupní data|B|Celkem|Vstupní data|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|instance|
|Http406|HTTP 406|Count|Celkem|HTTP 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru HTTP|Count|Celkem|Chyby serveru HTTP|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|Count|Celkem|Jednotky provádění funkcí|instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|instance|
|AppConnections|Připojení|Count|Average|Připojení|instance|
|Řeší|Počet popisovačů|Count|Average|Počet popisovačů|instance|
|Vlákna|Počet vláken|Count|Average|Počet vláken|instance|
|PrivateBytes|Privátní bajty|B|Average|Privátní bajty|instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|Celkem|V/V – přečtené bajty za sekundu|instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|Celkem|V/V – zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|Celkem|V/V – ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|Celkem|V/V – operace čtení za sekundu|instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|Celkem|V/V – operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|Celkem|V/V – ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Average|Celkový počet uvolněných domén aplikace|instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|Celkem|Uvolnění paměti 0. generace|instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|Celkem|Uvolnění paměti 1. generace|instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|Celkem|Uvolnění paměti 2. generace|instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Vstupní data|B|Celkem|Vstupní data|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|instance|
|Http406|HTTP 406|Count|Celkem|HTTP 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru HTTP|Count|Celkem|Chyby serveru HTTP|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|
|DiskQueueLength|Délka fronty disku|Count|Average|Délka fronty disku|instance|
|HttpQueueLength|Délka fronty HTTP|Count|Average|Délka fronty HTTP|instance|
|ActiveRequests|Aktivní požadavky|Count|Celkem|Aktivní požadavky|instance|
|TotalFrontEnds|Celkový počet front-endů|Count|Average|Celkový počet front-endů|Žádné dimenze|
|SmallAppServicePlanInstances|Malé pracovní procesy plánu služby App Service|Count|Average|Malé pracovní procesy plánu služby App Service|Žádné dimenze|
|MediumAppServicePlanInstances|Střední pracovní procesy plánu služby App Service|Count|Average|Střední pracovní procesy plánu služby App Service|Žádné dimenze|
|LargeAppServicePlanInstances|Velké pracovní procesy plánu služby App Service|Count|Average|Velké pracovní procesy plánu služby App Service|Žádné dimenze|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WorkersTotal|Celkový počet pracovních procesů|Count|Average|Celkový počet pracovních procesů|Žádné dimenze|
|WorkersAvailable|Dostupné pracovní procesy|Count|Average|Dostupné pracovní procesy|Žádné dimenze|
|WorkersUsed|Využité pracovní procesy|Count|Average|Využité pracovní procesy|Žádné dimenze|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|

## <a name="next-steps"></a>Další kroky
* [Přečtěte si o metrikách v Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Exportovat metriky do úložiště, centra událostí nebo Log Analytics](resource-logs-overview.md)
