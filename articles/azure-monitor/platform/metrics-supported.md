---
title: Azure Monitor podporované metriky podle typu prostředku
description: Seznam metrik, které jsou dostupné pro jednotlivé typy prostředků pomocí Azure monitoru.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: f3907cbc46bd226ef8e90d2061f2d36b2afabf8a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957150"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky ve službě Azure Monitor

Platforma Azure Monitor poskytuje několik způsobů, jak pracovat s metriky, včetně grafů na portálu, přístup přes rozhraní REST API nebo dotazování je pomocí Powershellu nebo rozhraní příkazového řádku. Níže je úplný seznam všech metrik aktuálně k dispozici pro monitorování Azure metriky kanálu. Jiné metriky, může být k dispozici na portálu nebo pomocí starší verze rozhraní API. Tento seznam níže obsahuje pouze metriky, které jsou k dispozici prostřednictvím konsolidované kanálu metrik Azure monitoru. K vyhledání a přístup k těmto metrikám prosím použijte [2018-01-01 verze api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Průměr|QPU. Rozsah 0 až 100 pro S1, 0 až 200 pro S2 a 0 až 400 pro S4|ServerResourceType|
|memory_metric|Memory (Paměť)|B|Průměr|Paměť. V rozsahu 0-25 GB pro S1, 0 – 50 GB pro S2 a 0 – 100 GB pro S4|ServerResourceType|
|TotalConnectionRequests|Celkem žádostí o spojení|Count|Průměr|Celkem žádostí o spojení. Jedná se o doručení.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná spojení za sekundu|CountPerSecond|Průměr|Míra úspěšně navázaných spojení.|ServerResourceType|
|TotalConnectionFailures|Celkem nezdařených spojení|Count|Průměr|Celkový počet neúspěšných pokusů o připojení.|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Průměr|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty fondu úloh příkazu|Count|Průměr|Počet úloh ve frontě fondu vláken příkazů.|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty fondu úloh zpracování|Count|Průměr|Počet úloh bez vstupně-ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: Aktuální počet připojení|Count|Průměr|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: Aktuální cena čisticího modulu|Count|Průměr|Aktuální cena paměti a $/ bajt/čas, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: Zmenšitelná paměť čisticího modulu|B|Průměr|Velikost paměti v bajtech, která čisticí procesem na pozadí se vyprázdňuje.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: Nezmenšitelná paměť|B|Průměr|Velikost paměti v bajtech, která není v souladu s čisticí vyprazdňování procesem na pozadí.|ServerResourceType|
|MemoryUsage|Paměť: Využití paměti|B|Průměr|Využití paměti procesu serveru, jak se používají při výpočtu cena čisticího modulu paměti. Rovnat čítači Process\PrivateBytes plus velikost dat mapovaných do paměti, ignoruje se jakákoli paměť, která byla mapována nebo přidělena analýzu v paměti modulu xVelocity (VertiPaq) Limit paměti modulu xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: Limit paměti – pevná|B|Průměr|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitHigh|Paměť: Limit paměti – vysoká|B|Průměr|Limit vysoké paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: Limit paměti – nízká|B|Průměr|Limit nízké paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: Memory Limit VertiPaq|B|Průměr|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: Kvóta|B|Průměr|Aktuální kvóta paměti, v bajtech. Kvóta paměti se taky říká rezervace paměti grant nebo paměti.|ServerResourceType|
|QuotaBlocked|Paměť: Kvóta – blokováno|Count|Průměr|Aktuální počet požadavků kvóty, které jsou blokovány, dokud jsou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq Nonpaged|B|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq Paged|B|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: Řádky převést za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: Počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vlákna: Příkaz zaneprázdněná vlákna fondu|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Vlákna: Příkaz nečinná vlákna fondu|Count|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyThreads|Vlákna: Zaneprázdněná vlákna dlouhého parsování|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého parsování.|ServerResourceType|
|LongParsingIdleThreads|Vlákna: Nečinná vlákna dlouhého parsování|Count|Průměr|Počet nečinných vláken ve fondu vláken dlouhého parsování.|ServerResourceType|
|LongParsingJobQueueLength|Vlákna: Dlouhého parsování délka fronty úloh|Count|Průměr|Počet úloh ve frontě fondu vláken dlouhého parsování.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vlákna: Zaneprázdněná vlákna úloh vstupně-výstupní operace fondu zpracování|Count|Průměr|Počet vláken ve fondu vláken zpracování spuštění vstupně-výstupních operací úloh.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vlákna: Zaneprázdněná vlákna jiných vstupně-fondu zpracování|Count|Průměr|Počet vláken, spouštění úloh bez vstupně-ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vlákna: Délka fronty úloh vstupně-výstupní operace v fondu zpracování|Count|Průměr|Počet vstupně-výstupních operací úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vlákna: Nečinná vlákna úloh vstupně-výstupní operace fondu zpracování|Count|Průměr|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vlákna: Nečinná vlákna jiných vstupně-fondu zpracování|Count|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeném pro úlohy bez vstupně.|ServerResourceType|
|QueryPoolIdleThreads|Vlákna: Nečinná vlákna fondu dotazů|Count|Průměr|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Count|Průměr|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|ShortParsingBusyThreads|Vlákna: Zaneprázdněná vlákna krátkého parsování|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken krátkého parsování.|ServerResourceType|
|ShortParsingIdleThreads|Vlákna: Nečinná vlákna krátkého parsování|Count|Průměr|Počet nečinných vláken ve fondu vláken krátkého parsování.|ServerResourceType|
|ShortParsingJobQueueLength|Vlákna: Krátkého parsování délka fronty úloh|Count|Průměr|Počet úloh ve frontě fondu vláken krátkého parsování.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Percent|Průměr|Průměrný thrashing paměti.|ServerResourceType|
|mashup_engine_qpu_metric|QPU modulu M|Count|Průměr|Využití QPU procesy modulu mashupu|ServerResourceType|
|mashup_engine_memory_metric|Paměť modulu M|B|Průměr|Využití paměti procesy modulu mashupu|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Požadavky brány celkem|Count|celkem|Počet požadavků brány|Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány|Count|celkem|Počet úspěšné požadavky brány|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované požadavky brány|Count|celkem|Počet Neautorizované požadavky brány|Umístění, název hostitele|
|FailedRequests|Požadavky na brány po nezdařeném nasazení|Count|celkem|Počet selhání v požadavky brány|Umístění, název hostitele|
|OtherRequests|Ostatní požadavky brány|Count|celkem|Počet ostatní požadavky brány|Umístění, název hostitele|
|Trvání|Celková doba trvání požadavků na bránu|Milisekund|Průměr|Celková doba trvání z požadavky brány v milisekundách|Umístění, název hostitele|
|Kapacita|Kapacita|Percent|Průměr|Metriky využití pro službu ApiManagement|Location|
|EventHubTotalEvents|Celkový počet EventHub události|Count|celkem|Počet událostí odeslaných do centra událostí|Location|
|EventHubSuccessfulEvents|Úspěšné EventHub události|Count|celkem|Počet úspěšných událostí centra událostí|Location|
|EventHubTotalFailedEvents|Události centra událostí se nezdařilo|Count|celkem|Počet neúspěšných událostí centra událostí|Location|
|EventHubRejectedEvents|Události zamítnutí EventHub|Count|celkem|Počet odmítnutých událostí centra událostí (nesprávné konfigurace nebo neoprávněný přístup)|Location|
|EventHubThrottledEvents|Omezené EventHub události|Count|celkem|Počet omezené události centra událostí|Location|
|EventHubTimedoutEvents|Vypršel časový limit události centra událostí|Count|celkem|Počet vypršel časový limit události centra událostí|Location|
|EventHubDroppedEvents|Události vynechané EventHub|Count|celkem|Počet událostí přeskočeno z důvodu dosáhlo limitu pro velikost fronty|Location|
|EventHubTotalBytesSent|Velikost události centra událostí|B|celkem|Celková velikost událostí centra událostí v bajtech|Location|
|Požadavky|Požadavky|Count|celkem|Požadavky brány|Umístění, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Count|celkem|Celkový počet úloh|Sady Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet aktualizací je spuštěné nasazení|Count|celkem|Nasazení aktualizace softwaru celkový počet spuštění|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Celkový počet aktualizací spuštění počítače nasazení|Count|celkem|Celkový počet softwarových aktualizace nasazení počítač běží v nasazení aktualizací softwaru spuštění|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Počet vyhrazených jader|Count|celkem|Celkový počet vyhrazených jader v účtu batch|Žádné dimenze|
|TotalNodeCount|Počet vyhrazených uzlů|Count|celkem|Celkový počet vyhrazených uzlů v účtu batch|Žádné dimenze|
|LowPriorityCoreCount|Počet jader LowPriority|Count|celkem|Celkový počet jader s nízkou prioritou na účet batch|Žádné dimenze|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Count|celkem|Celkový počet uzlů s nízkou prioritou na účtu batch|Žádné dimenze|
|CreatingNodeCount|Vytváří se počet uzlů|Count|celkem|Počet uzlů, které vytváří|Žádné dimenze|
|StartingNodeCount|Počáteční počet uzlů|Count|celkem|Počet uzlů spuštění|Žádné dimenze|
|WaitingForStartTaskNodeCount|Čekání na pro počet uzlů spuštění úkolu|Count|celkem|Počet uzlů čekání na dokončení úlohy spustit|Žádné dimenze|
|StartTaskFailedNodeCount|Spuštění úlohy se nezdařilo počet uzlů|Count|celkem|Počet uzlů, ve kterém se nezdařilo spustit úkol|Žádné dimenze|
|IdleNodeCount|Počet nečinných uzlů|Count|celkem|Počet nečinných uzlů|Žádné dimenze|
|OfflineNodeCount|Offline počet uzlů|Count|celkem|Počet uzlů v režimu offline|Žádné dimenze|
|RebootingNodeCount|Restartování počet uzlů|Count|celkem|Počtu restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|Obnovování z Image počet uzlů|Count|celkem|Počet uzlů, obnovování z Image|Žádné dimenze|
|RunningNodeCount|Spuštění počet uzlů|Count|celkem|Počet spuštěných uzly|Žádné dimenze|
|LeavingPoolNodeCount|Opuštění počet uzlů fondu|Count|celkem|Počet uzlů, které byste museli opustit fondu|Žádné dimenze|
|UnusableNodeCount|Nepoužitelné počet uzlů|Count|celkem|Počet uzlů nepoužitelné|Žádné dimenze|
|PreemptedNodeCount|Ke zrušení přidělením počet uzlů|Count|celkem|Počet uzlů přerušeno|Žádné dimenze|
|TaskStartEvent|Události spuštění úloh|Count|celkem|Celkový počet úloh, které byly spuštěny|Žádné dimenze|
|TaskCompleteEvent|Události dokončení úkolu|Count|celkem|Celkový počet úloh, které byly dokončeny|Žádné dimenze|
|TaskFailEvent|Události selhání úloh|Count|celkem|Celkový počet úloh, které byly dokončeny v neúspěšném stavu|Žádné dimenze|
|PoolCreateEvent|Vytvoření fondu události|Count|celkem|Celkový počet fondů, které byly vytvořeny|Žádné dimenze|
|PoolResizeStartEvent|Události zahájení změny velikosti fondu|Count|celkem|Celkový počet změny velikosti fondu, které byly spuštěny|Žádné dimenze|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Count|celkem|Celkový počet změny velikosti fondu, které byly dokončeny|Žádné dimenze|
|PoolDeleteStartEvent|Události zahájení odstranění fondu|Count|celkem|Celkový počet odstranění fondu, které byly spuštěny|Žádné dimenze|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Count|celkem|Celkový počet odstranění fondu, které byly dokončeny|Žádné dimenze|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Count|celkem|Celkový počet úloh, které byly úspěšně odstraněny.|Žádné dimenze|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Count|celkem|Celkový počet úloh, které bylo vyžádáno odstranit.|Žádné dimenze|
|JobDisableCompleteEvent|Události dokončení úlohy zakázat|Count|celkem|Celkový počet úloh, které bylo úspěšně zakázáno.|Žádné dimenze|
|JobDisableStartEvent|Úlohy zakázat zahájení události|Count|celkem|Celkový počet úloh, které bylo vyžádáno deaktivuje.|Žádné dimenze|
|JobStartEvent|Události spuštění úlohy|Count|celkem|Celkový počet úloh, které byla úspěšně spuštěna.|Žádné dimenze|
|JobTerminateCompleteEvent|Úloha ukončení dokončení události|Count|celkem|Celkový počet úloh, které se úspěšně ukončily.|Žádné dimenze|
|JobTerminateStartEvent|Úloha ukončení zahájení události|Count|celkem|Celkový počet úloh, které bylo vyžádáno ukončení.|Žádné dimenze|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Počet připojených klientů|Count|Maximum||ShardId|
|totalcommandsprocessed|Operace celkem|Count|celkem||ShardId|
|mezipaměti|Přístupy do mezipaměti|Count|celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Count|celkem||ShardId|
|getcommands|Operace Get|Count|celkem||ShardId|
|setcommands|Sady|Count|celkem||ShardId|
|operationsPerSecond|Operací za sekundu|Count|Maximum||ShardId|
|evictedkeys|Vyloučené klíče|Count|celkem||ShardId|
|totalkeys|Celkový počet klíčů|Count|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Count|celkem||ShardId|
|usedmemory|Použitá paměť|B|Maximum||ShardId|
|usedmemorypercentage|Procento využité paměti|Percent|Maximum||ShardId|
|usedmemoryRss|Využitá paměť RSS|B|Maximum||ShardId|
|serverLoad|Zatížení serveru|Percent|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Percent|Maximum||ShardId|
|cacheLatency|Mezipaměť latence mikrosekundy (Preview)|Count|Průměr||ShardId SampleType|
|chyby|Chyby|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Připojených klientů (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed0|Celkový počet operací (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|getcommands0|Získá (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|setcommands0|Nastaví (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|evictedkeys0|Vyloučené klíče (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|totalkeys0|Celkový počet klíčů (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|expiredkeys0|Prošlé klíče (horizontálních oddílů 0)|Count|celkem||Žádné dimenze|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|usedmemoryRss0|Využitá paměť RSS (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime0|Procesor (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|connectedclients1|Připojených klientů (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed1|Celkový počet operací (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|getcommands1|Získá (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|setcommands1|Nastaví (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|evictedkeys1|Vyloučené klíče (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|totalkeys1|Celkový počet klíčů (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|expiredkeys1|Prošlé klíče (horizontálních oddílů 1)|Count|celkem||Žádné dimenze|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|usedmemoryRss1|Využitá paměť RSS (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime1|Procesor (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|connectedclients2|Připojených klientů (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed2|Celkový počet operací (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|getcommands2|Získá (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|setcommands2|Nastaví (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|evictedkeys2|Vyloučené klíče (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|totalkeys2|Celkový počet klíčů (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|expiredkeys2|Prošlé klíče (horizontálních oddílů 2)|Count|celkem||Žádné dimenze|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|usedmemoryRss2|Využitá paměť RSS (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime2|Procesor (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|connectedclients3|Připojených klientů (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed3|Celkový počet operací (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|getcommands3|Získá (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|setcommands3|Nastaví (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|evictedkeys3|Vyloučené klíče (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|totalkeys3|Celkový počet klíčů (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|expiredkeys3|Prošlé klíče (horizontálních oddílů 3)|Count|celkem||Žádné dimenze|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|usedmemoryRss3|Využitá paměť RSS (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime3|Procesor (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|connectedclients4|Připojených klientů (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed4|Celkový počet operací (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|getcommands4|Získá (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|setcommands4|Nastaví (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|evictedkeys4|Vyloučené klíče (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|totalkeys4|Celkový počet klíčů (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|expiredkeys4|Prošlé klíče (horizontálních oddílů 4)|Count|celkem||Žádné dimenze|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|usedmemoryRss4|Využitá paměť RSS (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|serverLoad4|Server Load (Shard 4)|Percent|Maximum||Žádné dimenze|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime4|Procesor (horizontálních oddílů 4)|Percent|Maximum||Žádné dimenze|
|connectedclients5|Připojených klientů (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed5|Celkový počet operací (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|cachehits5|Přístupy do mezipaměti (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|getcommands5|Získá (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|setcommands5|Nastaví (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|operationsPerSecond5|Operací za sekundu (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|evictedkeys5|Vyloučené klíče (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|totalkeys5|Celkový počet klíčů (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|expiredkeys5|Prošlé klíče (horizontální oddíl 5)|Count|celkem||Žádné dimenze|
|usedmemory5|Využitá paměť (horizontální oddíl 5)|B|Maximum||Žádné dimenze|
|usedmemoryRss5|Využitá paměť RSS (horizontální oddíl 5)|B|Maximum||Žádné dimenze|
|serverLoad5|Zatížení serveru (horizontální oddíl 5)|Percent|Maximum||Žádné dimenze|
|cacheWrite5|Zápis do mezipaměti (horizontální oddíl 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead5|Čtení z mezipaměti (horizontální oddíl 5)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime5|Procesor (horizontální oddíl 5)|Percent|Maximum||Žádné dimenze|
|connectedclients6|Připojených klientů (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed6|Celkový počet operací (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|cachehits6|Přístupy do mezipaměti (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|getcommands6|Získá (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|setcommands6|Nastaví (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|operationsPerSecond6|Operací za sekundu (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|evictedkeys6|Vyloučené klíče (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|totalkeys6|Celkový počet klíčů (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|expiredkeys6|Prošlé klíče (horizontální oddíl 6)|Count|celkem||Žádné dimenze|
|usedmemory6|Využitá paměť (horizontální oddíl 6)|B|Maximum||Žádné dimenze|
|usedmemoryRss6|Využitá paměť RSS (horizontální oddíl 6)|B|Maximum||Žádné dimenze|
|serverLoad6|Zatížení serveru (horizontální oddíl 6)|Percent|Maximum||Žádné dimenze|
|cacheWrite6|Zápis do mezipaměti (horizontální oddíl 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead6|Čtení z mezipaměti (horizontální oddíl 6)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime6|Procesor (horizontální oddíl 6)|Percent|Maximum||Žádné dimenze|
|connectedclients7|Připojených klientů (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed7|Celkový počet operací (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|cachehits7|Přístupy do mezipaměti (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|getcommands7|Získá (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|setcommands7|Nastaví (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|operationsPerSecond7|Operací za sekundu (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|evictedkeys7|Vyloučené klíče (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|totalkeys7|Celkový počet klíčů (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|expiredkeys7|Prošlé klíče (horizontální oddíl 7)|Count|celkem||Žádné dimenze|
|usedmemory7|Využitá paměť (horizontální oddíl 7)|B|Maximum||Žádné dimenze|
|usedmemoryRss7|Využitá paměť RSS (horizontální oddíl 7)|B|Maximum||Žádné dimenze|
|serverLoad7|Server Load (Shard 7)|Percent|Maximum||Žádné dimenze|
|cacheWrite7|Zápis do mezipaměti (horizontální oddíl 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead7|Čtení z mezipaměti (horizontální oddíl 7)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime7|Procesor (horizontální oddíl 7)|Percent|Maximum||Žádné dimenze|
|connectedclients8|Připojených klientů (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed8|Celkový počet operací (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|cachehits8|Přístupy do mezipaměti (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|getcommands8|Získá (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|setcommands8|Nastaví (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|operationsPerSecond8|Operací za sekundu (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|evictedkeys8|Vyloučené klíče (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|totalkeys8|Celkový počet klíčů (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|expiredkeys8|Prošlé klíče (horizontální oddíl 8)|Count|celkem||Žádné dimenze|
|usedmemory8|Využitá paměť (horizontální oddíl 8)|B|Maximum||Žádné dimenze|
|usedmemoryRss8|Využitá paměť RSS (horizontální oddíl 8)|B|Maximum||Žádné dimenze|
|serverLoad8|Zatížení serveru (horizontální oddíl 8)|Percent|Maximum||Žádné dimenze|
|cacheWrite8|Zápis do mezipaměti (horizontální oddíl 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead8|Čtení z mezipaměti (horizontální oddíl 8)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime8|Procesor (horizontální oddíl 8)|Percent|Maximum||Žádné dimenze|
|connectedclients9|Připojených klientů (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed9|Celkový počet operací (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|cachehits9|Přístupy do mezipaměti (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|getcommands9|Získá (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|setcommands9|Nastaví (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|operationsPerSecond9|Operací za sekundu (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|evictedkeys9|Vyloučené klíče (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|totalkeys9|Celkový počet klíčů (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|expiredkeys9|Prošlé klíče (horizontální oddíl 9)|Count|celkem||Žádné dimenze|
|usedmemory9|Využitá paměť (horizontální oddíl 9)|B|Maximum||Žádné dimenze|
|usedmemoryRss9|Využitá paměť RSS (horizontální oddíl 9)|B|Maximum||Žádné dimenze|
|serverLoad9|Zatížení serveru (horizontální oddíl 9)|Percent|Maximum||Žádné dimenze|
|cacheWrite9|Zápis do mezipaměti (horizontální oddíl 9)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead9|Čtení z mezipaměti (horizontální oddíl 9)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime9|Procesor (horizontální oddíl 9)|Percent|Maximum||Žádné dimenze|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtený z disku během období sledování|Žádné dimenze|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaný na disk během období sledování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|B|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|B|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|RoleInstanceId|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtený z disku během období sledování|RoleInstanceId|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaný na disk během období sledování|RoleInstanceId|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkem volání|Count|celkem|Celkový počet volání|ApiName OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Count|celkem|Počet úspěšných volání|ApiName OperationName, oblast|
|TotalErrors|Celkem chyb|Count|celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx)|ApiName OperationName, oblast|
|BlockedCalls|Blokovaná volání|Count|celkem|Počet volání nad limit vyplývající ze sazby nebo kvóty|ApiName OperationName, oblast|
|ServerErrors|Chyby serveru|Count|celkem|Počet volání s vnitřní chybou služby (kód odpovědi HTTP 5xx)|ApiName OperationName, oblast|
|ClientErrors|Chyby klientů|Count|celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx)|ApiName OperationName, oblast|
|DataIn|Vstupní data|B|celkem|Velikost příchozích dat v bajtech|ApiName OperationName, oblast|
|DataOut|Výstupní data|B|celkem|Velikost odchozích dat v bajtech|ApiName OperationName, oblast|
|Latence|Latence|Milisekundy|Průměr|Latence v milisekundách|ApiName OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Count|celkem|Celkový počet znaků v příchozí textové žádosti|ApiName OperationName, oblast|
|CharactersTrained|Znaky školení|Count|celkem|Celkový počet znaků školení.|ApiName OperationName, oblast|
|SpeechSessionDuration|Doba trvání řečové relace|Sekundy|celkem|Celková doba trvání řečové relace v sekundách|ApiName OperationName, oblast|
|TotalTransactions|Transakce celkem|Count|celkem|Celkový počet transakcí|Žádné dimenze|
|TotalTokenCalls|Celkem volání tokenů|Count|celkem|Celkový počet volání tokenů|ApiName OperationName, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Fakturovatelný příchozí síťový provoz|B|celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Fakturovatelný odchozí síťový provoz|B|celkem|Počet fakturovatelných bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|B|celkem|Počet bajtů přečtených z disku během monitorování|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|B|celkem|Počet bajtů zapsaných na disk během monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity CPU|Zbývající kredity CPU|Count|Průměr|Celkové množství kreditu, které se dá využít|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Count|Průměr|Celkové množství kreditu, které spotřeboval virtuální počítač|Žádné dimenze|
|Přečtené bajty podle disku za sekundu|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Zapsané bajty podle disku za sekundu|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení podle disku za sekundu|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování|SlotId|
|Operace zápisu podle disku za sekundu|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování|SlotId|
|HF podle disku|HF datového disku (zastaralé)|Count|Průměr|Hloubka fronty datového disku (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému podle disku za sekundu|Bajty přečtené z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Zapsané bajty v operačním systému podle disku za sekundu|Bajty zapsané na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení v operačním systému podle disku za sekundu|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému podle disku za sekundu|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|HF v operačním systému podle disku|HF disku s operačním systémem (zastaralé)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|Logická jednotka (LUN)|
|Bajty zapsané na datový disk za sekundu|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|Logická jednotka (LUN)|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování|Logická jednotka (LUN)|
|Operace zápisu na datový disk za sekundu|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování|Logická jednotka (LUN)|
|Hloubka fronty datového disku|Hloubka fronty datového disku (Preview)|Count|Průměr|Hloubka fronty datového disku (nebo délka fronty)|Logická jednotka (LUN)|
|Bajty přečtené z disku s operačním systémem za sekundu|Bajty přečtené z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zapsané na disk s operačním systémem za sekundu|Bajty zapsané na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku s operačním systémem (Preview)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Příchozí toky|Příchozí toky (Preview)|Count|Průměr|Příchozí toky jsou počet aktuálních toků v příchozím směru (provoz přicházející do virtuálního počítače).|Žádné dimenze|
|Odchozí toky|Odchozí toky (Preview)|Count|Průměr|Odchozí toky jsou počet aktuálních toků v odchozím směru (provoz odcházející z virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz do virtuálního počítače)|Žádné dimenze|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz z virtuálního počítače)|Žádné dimenze|
|Úspěšné čtení z mezipaměti datového disku úrovně Premium|Úspěšné čtení z mezipaměti datového disku úrovně Premium (Preview)|Percent|Průměr|Úspěšné čtení z mezipaměti datového disku úrovně Premium|Logická jednotka (LUN)|
|Neúspěšné čtení z mezipaměti datového disku úrovně Premium|Neúspěšné čtení z mezipaměti datového disku úrovně Premium (Preview)|Percent|Průměr|Neúspěšné čtení z mezipaměti datového disku úrovně Premium|Logická jednotka (LUN)|
|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium (Preview)|Percent|Průměr|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Žádné dimenze|
|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium (Preview)|Percent|Průměr|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Žádné dimenze|
|Celkový příchozí síťový provoz|Celkový příchozí síťový provoz|B|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Celkový odchozí síťový provoz|Celkový odchozí síťový provoz|B|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|VMName|
|Síťové vstupy|Fakturovatelný příchozí síťový provoz|B|celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Síťové výstupy|Fakturovatelný odchozí síťový provoz|B|celkem|Počet fakturovatelných bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|
|Čtení z disku – bajty|Čtení z disku – bajty|B|celkem|Počet bajtů přečtených z disku během monitorování|VMName|
|Zápis na disk – bajty|Zápis na disk – bajty|B|celkem|Počet bajtů zapsaných na disk během monitorování|VMName|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|VMName|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|VMName|
|Zbývající kredity CPU|Zbývající kredity CPU|Count|Průměr|Celkové množství kreditu, které se dá využít|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Count|Průměr|Celkové množství kreditu, které spotřeboval virtuální počítač|Žádné dimenze|
|Přečtené bajty podle disku za sekundu|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Zapsané bajty podle disku za sekundu|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení podle disku za sekundu|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování|SlotId|
|Operace zápisu podle disku za sekundu|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování|SlotId|
|HF podle disku|HF datového disku (zastaralé)|Count|Průměr|Hloubka fronty datového disku (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému podle disku za sekundu|Bajty přečtené z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Zapsané bajty v operačním systému podle disku za sekundu|Bajty zapsané na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení v operačním systému podle disku za sekundu|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému podle disku za sekundu|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|HF v operačním systému podle disku|HF disku s operačním systémem (zastaralé)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|Logická jednotka, VMName|
|Bajty zapsané na datový disk za sekundu|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|Logická jednotka, VMName|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování|Logická jednotka, VMName|
|Operace zápisu na datový disk za sekundu|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování|Logická jednotka, VMName|
|Hloubka fronty datového disku|Hloubka fronty datového disku (Preview)|Count|Průměr|Hloubka fronty datového disku (nebo délka fronty)|Logická jednotka, VMName|
|Bajty přečtené z disku s operačním systémem za sekundu|Bajty přečtené z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Bajty zapsané na disk s operačním systémem za sekundu|Bajty zapsané na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|IOPS čtení z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|IOPS zápisu z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku s operačním systémem (Preview)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky (Preview)|Count|Průměr|Příchozí toky jsou počet aktuálních toků v příchozím směru (provoz přicházející do virtuálního počítače).|VMName|
|Odchozí toky|Odchozí toky (Preview)|Count|Průměr|Odchozí toky jsou počet aktuálních toků v odchozím směru (provoz odcházející z virtuálního počítače).|VMName|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz do virtuálního počítače)|VMName|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz z virtuálního počítače)|VMName|
|Úspěšné čtení z mezipaměti datového disku úrovně Premium|Úspěšné čtení z mezipaměti datového disku úrovně Premium (Preview)|Percent|Průměr|Úspěšné čtení z mezipaměti datového disku úrovně Premium|Logická jednotka, VMName|
|Neúspěšné čtení z mezipaměti datového disku úrovně Premium|Neúspěšné čtení z mezipaměti datového disku úrovně Premium (Preview)|Percent|Průměr|Neúspěšné čtení z mezipaměti datového disku úrovně Premium|Logická jednotka, VMName|
|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium (Preview)|Percent|Průměr|Úspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|VMName|
|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium (Preview)|Percent|Průměr|Neúspěšné čtení z mezipaměti disku s operačním systémem úrovně Premium|VMName|
|Celkový příchozí síťový provoz|Celkový příchozí síťový provoz|B|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Celkový odchozí síťový provoz|Celkový odchozí síťový provoz|B|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití procesoru|Count|Průměr|Využití procesoru u všech jader v jednotkách millicore|containerName|
|MemoryUsage|Využití paměti|B|Průměr|Celkové využití paměti v bajtech|containerName|
|NetworkBytesReceivedPerSecond|Bajty přijaté přes síť za sekundu|B|Průměr|Bajty přijaté přes síť za sekundu|Žádné dimenze|
|NetworkBytesTransmittedPerSecond|Bajty přenesené přes síť za sekundu|B|Průměr|Bajty přenesené přes síť za sekundu|Žádné dimenze|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Počet celkový o přijetí změn|Count|Průměr|Počet obrázků si vyžádá celkem|Žádné dimenze|
|SuccessfulPullCount|Počet úspěšných o přijetí změn|Count|Průměr|Počet úspěšných image si|Žádné dimenze|
|TotalPushCount|Počet celkový počet nabízených oznámení|Count|Průměr|Počet obrázků nabízených oznámení v celkem|Žádné dimenze|
|SuccessfulPushCount|Počet úspěšných nabízených oznámení|Count|Průměr|Počet úspěšně image nabízených oznámení|Žádné dimenze|
|RunDuration|Doba trvání běhu|Milisekund|celkem|Doba trvání v milisekundách|Žádné dimenze|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru v spravovaný cluster|Count|celkem|Celkový počet dostupných jader procesoru v spravovaný cluster|Žádné dimenze|
|kube_node_status_allocatable_memory_bytes|Celkové množství dostupné paměti v spravovaný cluster|B|celkem|Celkové množství dostupné paměti v spravovaný cluster|Žádné dimenze|
|kube_pod_status_ready|Počet podů ve stavu Připraveno|Count|celkem|Počet podů ve stavu Připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Count|celkem|Stavy pro různé podmínky uzlu|Stav, stav, status2, uzlu|
|kube_pod_status_phase|Počet podů podle fáze|Count|celkem|Počet podů podle fáze|fáze, obor názvů, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Volání Insights API zákazníka|Count|celkem||Žádné dimenze|
|DCIMappingImportOperationSuccessfulLines|Mapování Import operace úspěšné řádky|Count|celkem||Žádné dimenze|
|DCIMappingImportOperationFailedLines|Mapování operace importu se nezdařila řádky|Count|celkem||Žádné dimenze|
|DCIMappingImportOperationTotalLines|Mapování Import operace celkový počet řádků|Count|celkem||Žádné dimenze|
|DCIMappingImportOperationRuntimeInSeconds|Mapování modulu Runtime operace importu v řádu sekund|Sekundy|celkem||Žádné dimenze|
|DCIOutboundProfileExportSucceeded|Exportovat odchozí profilu proběhlo úspěšně|Count|celkem||Žádné dimenze|
|DCIOutboundProfileExportFailed|Exportovat odchozí profilu se nezdařila|Count|celkem||Žádné dimenze|
|DCIOutboundProfileExportDuration|Doba trvání exportu odchozí profilu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiExportSucceeded|Odchozí klíčového ukazatele výkonu Export byl úspěšný|Count|celkem||Žádné dimenze|
|DCIOutboundKpiExportFailed|Exportovat odchozí klíčového ukazatele výkonu se nezdařilo|Count|celkem||Žádné dimenze|
|DCIOutboundKpiExportDuration|Doba trvání odchozí Export klíčového ukazatele výkonu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiExportStarted|Spuštěn Export odchozí klíčového ukazatele výkonu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Sekundy|celkem||Žádné dimenze|
|DCIOutboundProfileExportCount|Počet odchozích profilu exportu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportFailed|Počáteční odchozí profilu exportu se nezdařila|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportSucceeded|Počáteční odchozí profilu exportu byla úspěšná|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportFailed|Odchozí počáteční klíčového ukazatele výkonu Export se nezdařil|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportSucceeded|Odchozí počáteční klíčového ukazatele výkonu Export byl úspěšný|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportDurationInSeconds|Doba trvání exportu odchozí počáteční profil v sekundách|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiFailed|Úloha Adla pro standardní klíčového ukazatele výkonu se nezdařilo v řádu sekund|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiTimeOut|Úloha Adla pro standardní klíčového ukazatele výkonu časový limit v sekundách|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiCompleted|Úloha Adla Standard klíčových ukazatelů výkonu v sekundách|Sekundy|celkem||Žádné dimenze|
|ImportASAValuesFailed|Počet neúspěšných hodnoty import Azure Stream Analytics|Count|celkem||Žádné dimenze|
|ImportASAValuesSucceeded|Počet úspěšně import ASA hodnoty|Count|celkem||Žádné dimenze|
|DCIProfilesCount|Počet instancí profilu|Count|Poslední||Žádné dimenze|
|DCIInteractionsPerMonthCount|Interakcí za měsíc počet|Count|Poslední||Žádné dimenze|
|DCIKpisCount|Počet klíčových ukazatelů výkonu|Count|Poslední||Žádné dimenze|
|DCISegmentsCount|Počet segmentů|Count|Poslední||Žádné dimenze|
|DCIPredictiveMatchPoliciesCount|Prediktivní počet shod|Count|Poslední||Žádné dimenze|
|DCIPredictionsCount|Počet predikcí|Count|Poslední||Žádné dimenze|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost čtení (síť)|BytesPerSecond|Průměr|Propustnost čtení síťového rozhraní na zařízení v období vykazování pro všechny svazky v bráně|InstanceName|
|NICWriteThroughput|Propustnost zápisu (síť)|BytesPerSecond|Průměr|Propustnost zápisu síťového rozhraní na zařízení v období vykazování pro všechny svazky v bráně|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování cloudu (sdílená složka)|BytesPerSecond|Průměr|Propustnost při stahování ze sdílené složky do Azure během období vykazování|Sdílet|
|CloudUploadThroughputPerShare|Propustnost nahrávání cloudu (sdílená složka)|BytesPerSecond|Průměr|Propustnost při nahrávání ze sdílené složky do Azure během období vykazování|Sdílet|
|BytesUploadedToCloudPerShare|Nahrané cloudové bajty (sdílená složka)|B|Průměr|Celkový počet bajtů, který se nahrál do Azure ze sdílené složky během období vykazování|Sdílet|
|Celková kapacita|Celková kapacita|B|Průměr|Celková kapacita|Žádné dimenze|
|AvailableCapacity|Dostupná kapacita|B|Průměr|Dostupná kapacita v bajtech během období vykazování|Žádné dimenze|
|CloudUploadThroughput|Propustnost nahrávání cloudu|BytesPerSecond|Průměr|Propustnost cloudu při nahrávání do Azure během období vykazování|Žádné dimenze|
|CloudReadThroughput|Propustnost cloudu při stahování|BytesPerSecond|Průměr|Propustnost cloudu při stahování do Azure během období vykazování|Žádné dimenze|
|BytesUploadedToCloud|Nahrané cloudové bajty (zařízení)|B|Průměr|Celkový počet bajtů, který se nahrál do Azure ze zařízení během období vykazování|Žádné dimenze|
|HyperVVirtualProcessorUtilization|Výpočetní funkce Edge – procento využití procesoru|Percent|Průměr|Využití procesoru v procentech|InstanceName|
|HyperVMemoryUtilization|Výpočetní funkce Edge – využití paměti|Percent|Průměr|Množství využité paměti RAM|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Count|celkem||pipelineName, activityName|
|SuccessfulRuns|Úspěšná spuštění|Count|celkem||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Se nezdařilo metrika spuštění kanálu|Count|celkem||FailureType, název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Count|celkem||FailureType, název|
|ActivityFailedRuns|Metriky aktivity spuštění se nezdařilo|Count|celkem||ActivityType, PipelineName, FailureType, název|
|ActivitySucceededRuns|Úspěšné běhy metriky aktivity|Count|celkem||ActivityType, PipelineName, FailureType, název|
|TriggerFailedRuns|Se nezdařilo metrika spuštění aktivační události|Count|celkem||Název, FailureType|
|TriggerSucceededRuns|Aktivační událost metriky spuštění bylo úspěšné|Count|celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití Integration runtime – procesor|Percent|Průměr||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime – paměť k dispozici|B|Průměr||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Entity maximální povolený počet|Count|Maximum||Žádné dimenze|
|MaxAllowedFactorySizeInGbUnits|Maximální povolený továrny velikost (GB unit)|Count|Maximum||Žádné dimenze|
|ResourceCount|Počet celkový počet entit|Count|Maximum||Žádné dimenze|
|FactorySizeInGbUnits|Celkový počet factory velikost (GB unit)|Count|Maximum||Žádné dimenze|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Count|celkem|Počet úspěšných úloh.|Žádné dimenze|
|JobEndedFailure|Neúspěšné úlohy|Count|celkem|Počet nezdařených úloh.|Žádné dimenze|
|JobEndedCancelled|Zrušené úlohy|Count|celkem|Počet zrušených úloh.|Žádné dimenze|
|JobAUEndedSuccess|Úspěšné čas AU|Sekundy|celkem|Celkový čas AU u úspěšných úloh.|Žádné dimenze|
|JobAUEndedFailure|Čas selhání AU|Sekundy|celkem|Celkový čas AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedCancelled|Zrušené čas AU|Sekundy|celkem|Celkový čas AU pro zrušené úlohy.|Žádné dimenze|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Úložiště celkem|B|Maximum|Celkové množství dat uložených v účtu.|Žádné dimenze|
|DataWritten|Zapsaná data|B|celkem|Celkové množství dat zapsaných do účtu.|Žádné dimenze|
|Přečtená data|Přečtená data|B|celkem|Celkové množství dat číst z účtu.|Žádné dimenze|
|WriteRequests|Požadavky na zápis|Count|celkem|Počet dat požadavky na zápis k účtu.|Žádné dimenze|
|ReadRequests|Žádosti o čtení|Count|celkem|Počet dat, přečtěte si požadavky na účet.|Žádné dimenze|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Průměr|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Průměr|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Průměr|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Průměr|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Průměr|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Průměr|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Průměr|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Chyba připojení|Count|celkem|Chyba připojení|Žádné dimenze|
|seconds_behind_master|Zpoždění replikace v řádu sekund|Count|Průměr|Zpoždění replikace v řádu sekund|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Průměr|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Průměr|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Průměr|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Průměr|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Průměr|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Průměr|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Průměr|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Chyba připojení|Count|celkem|Chyba připojení|Žádné dimenze|
|seconds_behind_master|Zpoždění replikace v řádu sekund|Count|Průměr|Zpoždění replikace v řádu sekund|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Průměr|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Průměr|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Průměr|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Průměr|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Průměr|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Průměr|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Průměr|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Chyba připojení|Count|celkem|Chyba připojení|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|celkem|Sítě v rámci aktivních připojení|Žádné dimenze|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v řádu sekund|Žádné dimenze|
|pg_replica_log_delay_in_bytes|Maximální prodleva mezi repliky|B|Maximum|Zpoždění v bajtech nejvíce obložení repliky|Žádné dimenze|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Průměr|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Průměr|Paměť v procentech|Žádné dimenze|
|vstupně-výstupních operací|IOPS|Count|Průměr|V/v operací za sekundu|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Průměr|Využité úložiště|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslat zpráva telemetrie|Count|celkem|Počet zpráv typu zařízení cloud telemetrie, které se pokusil odeslat do služby IoT hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Telemetrické zprávy odesílané|Count|celkem|Počet zpráv typu zařízení cloud telemetrie úspěšně odeslán do služby IoT hub|Žádné dimenze|
|c2d.commands.egress.complete.success|Dokončení příkazů|Count|celkem|Počet příkazů typu cloud zařízení zařízení byla úspěšně dokončena|Žádné dimenze|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Count|celkem|Počet opuštěných zařízení příkazy typu cloud zařízení|Žádné dimenze|
|c2d.commands.egress.reject.success|Příkazy zamítnuto|Count|celkem|Počet odmítnutých zařízení příkazy typu cloud zařízení|Žádné dimenze|
|devices.totalDevices|Celkový počet zařízení (zastaralé)|Count|celkem|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojené zařízení (zastaralé) |Count|celkem|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|d2c.telemetry.egress.success|Směrování: telemetrické zprávy doručí|Count|celkem|Počet pokusů, které zprávy se úspěšně doručila na všechny koncové body pomocí služby IoT Hub směrování. Pokud zpráva je směrována do několika koncových bodů, tato hodnota zvýší o jedna pro každé úspěšné dodání. Pokud zpráva se doručí na stejný koncový bod vícekrát, tato hodnota zvýší o jedna pro každé úspěšné dodání.|Žádné dimenze|
|d2c.telemetry.egress.dropped|Směrování: telemetrie počet ztracených zpráv |Count|celkem|Počet pokusů, které služba IoT Hub směrování z důvodu dead koncové body byly zahodit zprávy. Tato hodnota nepočítá doručování zpráv do základní trasy dodaným vynechané zprávy nejsou existuje.|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Směrování: telemetrické zprávy osamocené |Count|celkem|Počet pokusů, které zprávy byly osamocené přesměrováním služby IoT Hub, protože neodpovídají žádné pravidla směrování (včetně základní pravidlo). |Žádné dimenze|
|d2c.telemetry.egress.invalid|Směrování: telemetrické zprávy nekompatibilní|Count|celkem|Počet pokusů směrování služby IoT Hub se nepodařilo doručit z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakovaných pokusů.|Žádné dimenze|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručeny pro použití náhradní lokality|Count|celkem|Počet pokusů, které směrování služby IoT Hub doručení zprávy na koncový bod přidružený k použití náhradní lokality trasy.|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručeny do centra událostí|Count|celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů centra událostí.|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Směrování: zpráva latence pro Centrum událostí|Milisekund|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat zprávu do koncového bodu centra událostí.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Směrování: doručení zpráv do fronty služby Service Bus|Count|celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do fronty služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Směrování: zpráva latence pro frontu služby Service Bus|Milisekund|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do fronty koncového bodu služby Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Směrování: doručení zprávy do tématu služby Service Bus|Count|celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy do tématu služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Směrování: zpráva latence pro téma služby Service Bus|Milisekund|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do tématu koncovým bodem Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručovat zprávy a události|Count|celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy na integrovaný koncový bod (zprávy/events).|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Směrování: zpráva latence pro zprávy a události|Milisekund|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do integrovaného koncového bodu (zprávy/events).|Žádné dimenze|
|d2c.endpoints.egress.storage|Směrování: doručení zprávy do služby storage|Count|celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.latency.storage|Směrování: zpráva latence pro úložiště|Milisekund|Průměr|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy na koncový bod úložiště.|Žádné dimenze|
|d2c.endpoints.egress.storage.bytes|Směrování: data doručí do úložiště|B|celkem|Objem dat (v bajtech) směrování služby IoT Hub doručí do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.egress.storage.blobs|Směrování: objekty BLOB doručit do úložiště|Count|celkem|Počet pokusů, které směrování služby IoT Hub doručí do koncových bodů úložiště objektů BLOB.|Žádné dimenze|
|EventGridDeliveries|Event Grid doručení (preview)|Count|celkem|Počet požadavků pro IoT Hub a vysílat události do služby Event Grid. Toto číslo zahrnuje úspěšné i neúspěšné požadavky. Dimenze výsledku používejte pro počet různých typů odpovědi. Chcete-li zobrazit where požadavky pocházejí z, použijte typ EventType dimenze.|Výsledek, typ události|
|EventGridLatency|Event Grid latence (preview)|Milisekund|Průměr|Průměrná latence (milisekundy) mezi událostí příchozího přenosu dat do služby IoT Hub a příjem příchozích dat událostí do služby Event Grid. Toto číslo je průměr mezi všechny typy událostí. Pomocí dimenze typ EventType zobrazíte latence konkrétní typ události.|Typ události|
|d2c.twin.read.success|Čtení dvojčat úspěšné ze zařízení|Count|celkem|Počet všech úspěšných čtení dvojčat zařízení iniciované.|Žádné dimenze|
|d2c.twin.read.failure|Čtení dvojčat ze zařízení se nezdařila|Count|celkem|Počet všech se nezdařilo čtení dvojčat zařízení inicioval.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi čtení dvojčat zařízení|B|Průměr|Průměrné, minimální a maximální ze všech úspěšné, zařízení iniciované dvojčete čtení.|Žádné dimenze|
|d2c.twin.update.success|Aktualizace dvojčat úspěšné ze zařízení|Count|celkem|Počet všech úspěšných aktualizací dvojčete zařízení iniciované.|Žádné dimenze|
|d2c.twin.update.failure|Aktualizace dvojčat ze zařízení s chybami|Count|celkem|Počet všech neúspěšných dvojčete zařízení iniciované aktualizací.|Žádné dimenze|
|d2c.twin.update.size|Velikost aktualizace dvojčete zařízení|B|Průměr|Průměrné a minimální a maximální velikost všech úspěšné, zařízení iniciované dvojčete aktualizace.|Žádné dimenze|
|c2d.methods.success|Volání úspěšné přímé metody|Count|celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|c2d.methods.failure|Přímé metody vyvolání se nezdařilo|Count|celkem|Počet všech se nezdařilo volání metody s přímým přístupem.|Žádné dimenze|
|c2d.methods.requestSize|Žádost o velikosti vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální počet všech úspěšných požadavků přímé metody.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odpovědi vyvolání přímé metody|B|Průměr|Průměrné, minimální a maximální všechny úspěšné přímé metody odpovědí.|Žádné dimenze|
|c2d.twin.read.success|Čtení dvojčat úspěšné z back-endu|Count|celkem|Počet všech úspěšných čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.twin.read.failure|Čtení dvojčat se nezdařilo z back-endu|Count|celkem|Počet všech se nezdařilo čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi čtení dvojčat z back-endu|B|Průměr|Průměr a minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete čtení.|Žádné dimenze|
|c2d.twin.update.success|Aktualizace dvojčat úspěšné z back-endu|Count|celkem|Počet všech úspěšné aktualizace dvojčete iniciované back-end.|Žádné dimenze|
|c2d.twin.update.failure|Aktualizace dvojčat se nezdařilo z back-endu|Count|celkem|Počet všech neúspěšných iniciované back-end dvojčete aktualizací.|Žádné dimenze|
|c2d.twin.update.size|Velikost dvojčete aktualizací z back-endu|B|Průměr|Průměrné, minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete aktualizace.|Žádné dimenze|
|twinQueries.success|Úspěšné dvojčete dotazy|Count|celkem|Počet všech úspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné dvojčete dotazy|Count|celkem|Počet všech neúspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.resultSize|Velikost výsledku dotazy dvojčete|B|Průměr|Průměrné, minimální a maximální velikosti výsledek všech dotazů na dvojčata úspěšné.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úlohy aktualizace dvojčete|Count|celkem|Počet všech po úspěšném vytvoření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úlohy aktualizace dvojčete|Count|celkem|Počet všech neúspěšných vytváření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh vyvolání – metoda|Count|celkem|Počet všech po úspěšném vytvoření úlohy vyvolání přímé metody.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh vyvolání – metoda|Count|celkem|Počet všech neúspěšných vytváření úloh vyvolání přímé metody.|Žádné dimenze|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Count|celkem|Počet všech úspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.listJobs.failure|Neúspěšná volání do seznamu úloh|Count|celkem|Počet všech neúspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.cancelJob.success|V případě úspěšné úlohy|Count|celkem|Počet všech úspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.cancelJob.failure|Zrušení úlohy, která selhala|Count|celkem|Počet všech neúspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.queryJobs.success|Úspěšné úlohy dotazů|Count|celkem|Počet všech úspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.queryJobs.failure|Neúspěšné úlohy dotazy|Count|celkem|Počet všech neúspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.completed|Dokončené úlohy|Count|celkem|Počet všech dokončených úloh.|Žádné dimenze|
|jobs.failed|Neúspěšné úlohy|Count|celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezení|Count|celkem|Omezí počet chyb omezení kvůli propustnost zařízení|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet zpráv používá|Count|Průměr|Počet celkový počet zpráv, které se dnes používají. Jedná se o kumulativní hodnotu, která se resetuje na nulu v 00:00 UTC každý den.|Žádné dimenze|
|deviceDataUsage|Celkový počet zařízení využití dat|B|celkem|Bajtů přenesených do a z jakékoli zařízení připojené k IOT hub|Žádné dimenze|
|totalDeviceCount|Celkový počet zařízení (preview)|Count|Průměr|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|connectedDeviceCount|Připojené zařízení (preview)|Count|Průměr|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|Konfigurace|Konfigurace metrik|Count|celkem|Pro operace konfigurace metrik|Žádné dimenze|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Count|celkem|Počet k pokusu o registraci zařízení|Stav ProvisioningServiceName IotHubName,|
|DeviceAssignments|Přiřazená zařízení|Count|celkem|Počet zařízení přiřazená do služby IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Count|celkem|Počet zařízení atestací pokus o|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AvailableStorage|Dostupné úložiště|B|celkem|Celkový úložiště k dispozici hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|CassandraConnectionClosures|Uzávěry připojení Cassandra|Count|celkem|Počet připojení Cassandra, které nebyly uzavřeny, a Ohlášeno za 1 minutu|Oblast, ClosureReason|
|CassandraRequestCharges|Poplatky za žádost Cassandra|Count|celkem|Počet ru spotřebovaných pro Cassandra žádosti|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Požadavky Cassandra|Count|Count|Počet zpracovaných požadavků Cassandra|Oblast, DatabaseName, CollectionName, typem operace OperationType, ResourceType, kód chyby|
|DataUsage|Využití dat|B|celkem|Celkové množství dat využití hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|DocumentCount|Počet dokumentů|Count|celkem|Celkového počtu dokumentů hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|DocumentQuota|Kvóta dokumentu|B|celkem|Kvóta celkový úložiště hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|IndexUsage|Použití indexu|B|celkem|Celkový počet index využití hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|MetadataRequests|Požadavky na metadata|Count|Count|Počet žádostí o metadata. Cosmos DB udržuje kolekci metadat systému pro každý účet, který umožňuje vytvoření výčtu kolekce, databázím atd., a jejich konfigurací, které jsou zdarma.|DatabaseName CollectionName, oblast, StatusCode, |
|MongoRequestCharge|Zátěž žádostí mongo|Count|celkem|Spotřebované jednotky žádostí mongo|Název databáze, CollectionName, oblast, CommandName, kód chyby|
|MongoRequests|Požadavky mongo|Count|Count|Počet zpracovaných požadavků Mongo|Název databáze, CollectionName, oblast, CommandName, kód chyby|
|ProvisionedThroughput|Zřízená propustnost|Count|Maximum|Zřízená propustnost|DatabaseName CollectionName|
|ReplicationLatency|P99 Latence replikace|Milisekundy|Průměr|Latencí P99 replikace mezi zdrojovou a cílovou oblastí pro účet povolenou geografickou|SourceRegion TargetRegion|
|ServiceAvailability|Dostupnost služby|Percent|Průměr|Dostupnost účtu požadavky na jednu hodinu, den nebo měsíc členitosti|Žádné dimenze|
|TotalRequestUnits|Celkový požadavek jednotky|Count|celkem|Požadavku že spotřebované jednotky|Název databáze, CollectionName, oblast, StatusCode, typ operace|
|TotalRequests|Požadavky celkem|Count|Count|Počet zpracovaných požadavků|Název databáze, CollectionName, oblast, StatusCode, typ operace|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|celkem|Celkový počet událostí, které publikuje na toto téma|Žádné dimenze|
|PublishFailCount|Publikování událostí se nezdařilo|Count|celkem|Celkový počet událostí, které se nepovedlo publikovat. k tomuto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárovaných událostí|Count|celkem|Celkový počet událostí, které se neshodují žádné odběry událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Publikování latence při úspěchu|Count|celkem|Publikování latence při úspěchu v milisekundách|Žádné dimenze|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Odpovídající události|Count|celkem|Celkový počet událostí, které odpovídají tento odběr události|Žádné dimenze|
|DeliveryAttemptFailCount|Doručení události nezdařilo|Count|celkem|Celkový počet událostí doručení pro tento odběr události se nezdařilo.|Chyba ErrorType.|
|DeliverySuccessCount|Doručené události|Count|celkem|Celkový počet událostí doručení pro tento odběr události|Žádné dimenze|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíl|Milisekund|Průměr|Doba trvání zpracování cíl v milisekundách|Žádné dimenze|
|DroppedEventCount|Vyřazené události|Count|celkem|Celkový počet vynechaných událostí vzorů pro tento odběr události|DropReason|
|DeadLetteredCount|Dead Lettered události|Count|celkem|Celkový počet událostí dead lettered vzorů pro tento odběr události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|celkem|Celkový počet událostí, které publikuje na toto téma|Žádné dimenze|
|PublishFailCount|Události se nezdařilo|Count|celkem|Celkový počet událostí, které se nepovedlo publikovat. k tomuto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárovaných událostí|Count|celkem|Celkový počet událostí, které se neshodují žádné odběry událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Publikování latence při úspěchu|Count|celkem|Publikování latence při úspěchu v milisekundách|Žádné dimenze|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|celkem|Úspěšné požadavky pro Microsoft.EventHub|EntityName, |
|ServerErrors|Chyby serveru|Count|celkem|Chyby serveru pro Microsoft.EventHub|EntityName, |
|UserErrors|Chyby uživatele|Count|celkem|Chyby uživatele pro Microsoft.EventHub|EntityName, |
|QuotaExceededErrors|Chyby překročení kvóty|Count|celkem|Chyby překročení kvóty pro Microsoft.EventHub|EntityName, |
|ThrottledRequests|Omezené požadavky|Count|celkem|Omezené požadavky pro Microsoft.EventHub|EntityName, |
|IncomingRequests|Příchozí požadavky|Count|celkem|Příchozí požadavky pro Microsoft.EventHub|EntityName|
|IncomingMessages|Příchozí zprávy|Count|celkem|Příchozí zprávy pro Microsoft.EventHub|EntityName|
|OutgoingMessages|Odchozí zprávy|Count|celkem|Odchozí zprávy pro Microsoft.EventHub|EntityName|
|IncomingBytes|Příchozí bajty|B|celkem|Příchozí bajty pro Microsoft.EventHub|EntityName|
|OutgoingBytes|Odchozí bajty|B|celkem|Odchozí bajty pro Microsoft.EventHub|EntityName|
|ActiveConnections|ActiveConnections|Count|Průměr|Celkový počet aktivních připojení pro Microsoft.EventHub|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení|Count|Průměr|Otevřená připojení pro Microsoft.EventHub|EntityName|
|ConnectionsClosed|Ukončená připojení|Count|Průměr|Ukončená připojení pro Microsoft.EventHub|EntityName|
|CaptureBacklog|Zachytit backlog|Count|celkem|Zachytit backlog pro Microsoft.EventHub|EntityName|
|CapturedMessages|Zachycené zprávy|Count|celkem|Zachycené zprávy pro Microsoft.EventHub|EntityName|
|CapturedBytes|Zachycené bajty|B|celkem|Zachycené bajty pro Microsoft.EventHub|EntityName|
|Velikost|Velikost|B|Průměr|Velikost centra událostí v bajtech|EntityName|
|INREQS|Příchozí žádosti (zastaralé)|Count|celkem|Celkový počet příchozích požadavků na odeslání pro obor názvů (zastaralé)|Žádné dimenze|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Count|celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|FAILREQ|Neúspěšné požadavky (zastaralé)|Count|celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|SVRBSY|Chyby kvůli zaneprázdněnosti serveru (zastaralé)|Count|celkem|Chyby kvůli zaneprázdněnosti úplné havárii serveru pro obor názvů (zastaralé)|Žádné dimenze|
|INTERR|Vnitřní chyby serveru (zastaralé)|Count|celkem|Celkový počet vnitřních chyb serveru pro obor názvů (zastaralé)|Žádné dimenze|
|MISCERR|Další chyby (zastaralé)|Count|celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Count|celkem|Celkový počet příchozích zpráv pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku příchozí zprávy (zastaralé)|Žádné dimenze|
|EHINMSGS|Příchozí zprávy (zastaralé)|Count|celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Count|celkem|Celkový počet odchozích zpráv pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku odchozí zprávy (zastaralé)|Žádné dimenze|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Count|celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|B|celkem|Příchozí propustnost zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku Příchozí bajty (zastaralé)|Žádné dimenze|
|EHINBYTES|Příchozí bajty (zastaralé)|B|celkem|Příchozí propustnost zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|B|celkem|Odchozí propustnost zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku Odchozí bajty (zastaralé)|Žádné dimenze|
|EHOUTBYTES|Odchozí bajty (zastaralé)|B|celkem|Odchozí propustnost zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHABL|Archivovat zprávy backlogu (zastaralé)|Count|celkem|Archivované zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádné dimenze|
|EHAMSGS|Archivace zpráv (zastaralé)|Count|celkem|Archivované zprávy v oboru názvů (zastaralé) centra událostí|Žádné dimenze|
|EHAMBS|Archivovat propustnost zpráv (zastaralé)|B|celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádné dimenze|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|celkem|Úspěšné požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ServerErrors|Chyby serveru (Preview)|Count|celkem|Chyby serveru pro Microsoft.EventHub (Preview)|Žádné dimenze|
|UserErrors|Chyby uživatele (Preview)|Count|celkem|Chyby uživatele pro Microsoft.EventHub (Preview)|Žádné dimenze|
|QuotaExceededErrors|Chyby překročení kvóty (Preview)|Count|celkem|Chyby překročení kvóty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ThrottledRequests|Omezené požadavky (Preview)|Count|celkem|Omezené požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingRequests|Příchozí žádosti (Preview)|Count|celkem|Příchozí požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingMessages|Příchozí zprávy (Preview)|Count|celkem|Příchozí zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|celkem|Odchozí zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingBytes|Příchozí bajty (Preview)|B|celkem|Příchozí bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|OutgoingBytes|Odchozí bajty (Preview)|B|celkem|Odchozí bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ActiveConnections|ActiveConnections (Preview)|Count|Průměr|Celkový počet aktivních připojení pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení (Preview)|Count|Průměr|Otevřená připojení pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ConnectionsClosed|Ukončená připojení (Preview)|Count|Průměr|Ukončená připojení pro Microsoft.EventHub (Preview)|Žádné dimenze|
|CaptureBacklog|Zachytit backlog (Preview)|Count|celkem|Zachytit backlog pro Microsoft.EventHub (Preview)|Žádné dimenze|
|CapturedMessages|Zachycené zprávy (Preview)|Count|celkem|Zachycené zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|CapturedBytes|Zachycené bajty (Preview)|B|celkem|Zachycené bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|Procesor|Procesor (Preview)|Percent|Maximum|Využití procesoru pro cluster centra událostí v procentech|Role|
|AvailableMemory|Dostupná paměť (Preview)|Count|Maximum|Paměť dostupná clusteru centra událostí v bajtech|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Požadavky brány|Count|celkem|Počet požadavků brány|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Požadavky brány podle kategorií|Count|celkem|Počet požadavků brány podle kategorií (1xx/2xx nebo 3xx/4xx a 5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Počet aktivních pracovních procesů|Count|Maximum|Počet aktivních pracovních procesů|Název_dns_clusteru MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zjištěná hodnota metriky|Count|Průměr|Hodnota vypočítaná automatickým škálováním při jeho spuštění|MetricTriggerSource|
|MetricThreshold|Mezní hodnota metriky|Count|Průměr|Konfigurovaná mezní hodnota automatického škálování, když se automatické škálování spustilo|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Count|Průměr|Kapacita ohlášená automatickému škálování při jeho spuštění|Žádné dimenze|
|ScaleActionsInitiated|Zahájené akce škálování|Count|celkem|Směr operace škálování|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityResult/name, availabilityResult/umístění|
|availabilityResults/počet|Testy dostupnosti|Count|Count|Počet testů dostupnosti|availabilityResult/name, availabilityResult nebo umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba trvání testu dostupnosti|Milisekundy|Průměr|Doba trvání testu dostupnosti|availabilityResult/name, availabilityResult nebo umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Čas síťového připojení při stahování stránky|Milisekundy|Průměr|Doba mezi uživatele požadavku a síťové připojení. Zahrnuje DNS vyhledávání a přenosové připojení.|Žádné dimenze|
|browserTimings/processingDuration|Čas klientského zpracování|Milisekundy|Průměr|Doba mezi přijetím posledního bajtu dokumentu, dokud je načten v modelu DOM. Asynchronní žádosti se možná ještě zpracovávají.|Žádné dimenze|
|browserTimings/receiveDuration|Čas přijetí odezvy|Milisekundy|Průměr|Doba mezi prvním a posledním bajtem nebo do odpojení|Žádné dimenze|
|browserTimings/sendDuration|Čas odeslání žádosti|Milisekundy|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu|Žádné dimenze|
|browserTimings/totalDuration|Čas načítání stránky prohlížeče|Milisekundy|Průměr|Doba od žádosti uživatele do načtení DOM, šablon stylů, skriptů a obrázků|Žádné dimenze|
|závislosti nebo count|Volání závislostí|Count|Count|Počet volání prováděných aplikací vůči externím prostředkům|/ Typ závislosti, závislost nebo performanceBucket, závislost/úspěch, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|závislosti a doba trvání|Doba trvání závislosti|Milisekundy|Průměr|Doba trvání volání prováděných aplikací vůči externím prostředkům|/ Typ závislosti, závislost nebo performanceBucket, závislost/úspěch, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|/ Neúspěšné závislosti|Chyby při volání závislostí|Count|Count|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|/ Typ závislosti, závislost nebo performanceBucket, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|zobrazení stránky nebo count|Zobrazení stránek|Count|Count|Počet zobrazení stránky|operace/synthetic|
|zobrazení stránky a doba trvání|Doba načtení zobrazení stránky|Milisekundy|Průměr|Doba načtení zobrazení stránky|operace/synthetic|
|performanceCounters/requestExecutionTime|Doba provádění požadavku HTTP|Milisekundy|Průměr|Doba provádění nejnovější žádosti|cloud/roleInstance|
|performanceCounters/requestsInQueue|Požadavky HTTP ve frontě aplikací|Count|Průměr|Délka fronty žádostí na aplikace|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Četnost požadavků HTTP|CountPerSecond|Průměr|Počet všech žádostí na aplikaci za sekundu z ASP.NET|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Frekvence výjimek|CountPerSecond|Průměr|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Frekvence V/V procesu|BytesPerSecond|Průměr|Celkem bajtů čtených ze souborů, sítě a zařízení nebo do nich zapisovaných za sekundu|cloud/roleInstance|
|čítače výkonu nebo processCpuPercentage|Proces – procesor|Percent|Průměr|Procento uplynulého času, že všechna vlákna procesu používala procesor pro spouštění instrukcí. To se může lišit od 0 do 100. Tato Metrika vyjadřuje výkon samotného procesu w3wp.|cloud/roleInstance|
|čítače výkonu nebo processorCpuPercentage|Čas procesoru|Percent|Průměr|Procento času, které procesor stráví na nečinných vláknech|cloud/roleInstance|
|čítače výkonu nebo memoryAvailableBytes|Dostupná paměť|B|Průměr|Okamžitě dostupná fyzická paměť pro přidělení procesu nebo pro systémové použití|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privátní bajty procesu|B|Průměr|Paměť exkluzivně přiřazená k procesům monitorované aplikace|cloud/roleInstance|
|požadavky a doba trvání|Doba odezvy serveru|Milisekundy|Průměr|Doba mezi přijetím žádosti HTTP a dokončením odesílání odpovědi|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|požadavky nebo count|Požadavky serveru|Count|Count|Počet dokončených žádostí HTTP|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|/ Neúspěšné požadavky|Neúspěšné požadavky|Count|Count|Požadavky HTTP počet označen jako neúspěšný. Ve většině případů jde o žádosti s kódem odpovědi > = 400 a není rovno 401.|žádost/performanceBucket, požadavek/resultCode, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|požadavky a míra|Frekvence požadavků serveru|CountPerSecond|Průměr|Rychlost požadavků na server za sekundu|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|výjimky nebo count|Výjimky|Count|Count|Kombinovaný počet všech nezachycených výjimek.|cloud/roleName, cloud nebo instance role, klient/typu|
|výjimky a prohlížeče|Výjimky prohlížečů|Count|Count|Počet nezachycených výjimek vyvolaných v prohlížeči|Žádné dimenze|
|výjimky/server|Serverové výjimky|Count|Count|Počet nezachycených výjimek vyvolaných v serverové aplikaci|cloud/roleName, cloud nebo instance role|
|trasování nebo count|Trasování|Count|Count|Počet dokumentů trasování|trasování/severityLevel, operace nebo syntetický, cloudu/roleName, cloud nebo instance role|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkem přístupů k rozhraní API služby|Count|Count|Celkový počet přístupů k rozhraní API služby|ActivityType, ActivityName|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Celkem výsledků rozhraní API služby|Count|Count|Celkový počet výsledků rozhraní API služby|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Využití mezipaměti|Percent|Průměr|Úroveň využití v rámci clusteru|Žádné dimenze|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Dotazy doba trvání v sekundách|QueryStatus|
|IngestionsLoadFactor|Ingestování využití|Percent|Průměr|Poměr používané ingestování sloty v clusteru|Žádné dimenze|
|IsEngineAnsweringQuery|Zachování|Count|Průměr|Kontrola správnosti označuje, že cluster odpoví na dotazy|Žádné dimenze|
|IngestCommandOriginalSizeInMb|Ingestování svazek (v MB)|Count|celkem|Celkový objem přijatých dat do clusteru (v MB)|Žádné dimenze|
|IngestedEventAgeSeconds|Latence příjmu dat (v sekundách)|Sekundy|Průměr|Ingestování čas ze zdroje (například je zpráva EventHub) do clusteru během několika sekund|Žádné dimenze|
|EventRecievedFromEventHub|Události byly zpracovány (pro službu Event Hubs)|Count|celkem|Počet události byly zpracovány v clusteru při ingestování z centra událostí|Žádné dimenze|
|IngestionResult|Příjem výsledků|Count|Count|Počet operací příjmu|IngestionResultDetails|
|EngineCPU|Procesor|Percent|Průměr|Úroveň využití procesoru|Žádné dimenze|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Count|Count|Volání počet rozhraní API|ApiCategory ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|celkem|Počet spuštěných běhů pracovního postupu.|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšné běhy|Count|celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšné běhy|Count|celkem|Počet neúspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|celkem|Počet běhů pracovního postupu zrušila.|Žádné dimenze|
|RunLatency|Latence běhu|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšných běhů|Sekundy|Průměr|Latence úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Omezené události běhu|Count|celkem|Počet akcí pracovního postupu nebo omezených událostí triggeru.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|celkem|Procento neúspěšných spuštění pracovních postupů|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce|Count|celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|celkem|Počet omezených událostí akcí pracovního postupu.|Žádné dimenze|
|TriggersStarted|Spuštěné triggery |Count|celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Dokončené triggery |Count|celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Úspěšné triggery |Count|celkem|Počet úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFailed|Neúspěšné triggery |Count|celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Vynechané triggery|Count|celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Vyvolané triggery |Count|celkem|Počet aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence při vyvolání triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšného triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|celkem|Počet omezených událostí triggeru pracovního postupu.|Žádné dimenze|
|BillableActionExecutions|Fakturovatelné operace provedení akce|Count|celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádné dimenze|
|BillableTriggerExecutions|Fakturovatelné operace provedení aktivačních událostí|Count|celkem|Počet fakturovaných provedení aktivačních událostí pracovního postupu|Žádné dimenze|
|TotalBillableExecutions|Fakturovatelné operace provedení celkem|Count|celkem|Počet fakturovaných provedení pracovního postupu|Žádné dimenze|
|BillingUsageNativeOperation|Fakturace za použití provedení nativních operací|Count|celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Fakturace za použití provedení standardních konektorů|Count|celkem|Počet fakturovaných provedení standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Fakturace za použití provedení využití úložiště|Count|celkem|Počet fakturovaných provedení využití úložiště|Žádné dimenze|
|BillingUsageNativeOperation|Fakturace za použití provedení nativních operací|Count|celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Fakturace za použití provedení standardních konektorů|Count|celkem|Počet fakturovaných provedení standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Fakturace za použití provedení využití úložiště|Count|celkem|Počet fakturovaných provedení využití úložiště|Žádné dimenze|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|celkem|Počet spuštěných běhů pracovního postupu.|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšné běhy|Count|celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšné běhy|Count|celkem|Počet neúspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|celkem|Počet běhů pracovního postupu zrušila.|Žádné dimenze|
|RunLatency|Latence běhu|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšných běhů|Sekundy|Průměr|Latence úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Omezené události běhu|Count|celkem|Počet akcí pracovního postupu nebo omezených událostí triggeru.|Žádné dimenze|
|RunStartThrottledEvents|Omezené události spuštění|Count|celkem|Počet omezených událostí spuštění pracovního postupu|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|celkem|Procento neúspěšných spuštění pracovních postupů|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce |Count|celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|celkem|Počet omezených událostí akcí pracovního postupu.|Žádné dimenze|
|TriggersStarted|Spuštěné triggery |Count|celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Dokončené triggery |Count|celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Úspěšné triggery |Count|celkem|Počet úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFailed|Neúspěšné triggery |Count|celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Vynechané triggery|Count|celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Vyvolané triggery |Count|celkem|Počet aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence při vyvolání triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšného triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|celkem|Počet omezených událostí triggeru pracovního postupu.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Percent|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti pracovního postupu pro prostředí integrační služby|Percent|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí integrační služby|Percent|Průměr|Využití procesoru konektoru pro prostředí integrační služby|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí integrační služby|Percent|Průměr|Využití paměti konektoru pro prostředí integrační služby|Žádné dimenze|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Dokončené běhy|Dokončené běhy|Count|celkem|Počet běhů bylo dokončeno úspěšně pro tento pracovní prostor|Scénář|
|Začínáme spuštění|Začínáme spuštění|Count|celkem|Číslo, které spustil pro tento pracovní prostor|Scénář|
|Neúspěšná spuštění|Neúspěšná spuštění|Count|celkem|Počet běhů se nezdařilo pro tento pracovní prostor|Scénář|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Count|Count|Volání počet rozhraní API|ApiCategory ResponseCode ApiName, typ ResultType,|
|Dostupnost|Dostupnost|Procento|Průměr|Dostupnost rozhraní API|ApiCategory ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageOtherLatency|Průměrná další latence|ms/op|Průměr|Průměrná jiné (který není pro čtení nebo zápisu) latence v milisekundách na operaci|Žádné dimenze|
|AverageReadLatency|Průměrná latence čtení|ms/op|Průměr|Průměrná latence v milisekundách na operaci čtení|Žádné dimenze|
|AverageTotalLatency|Průměrná celková latence|ms/op|Průměr|Průměrná celková latence v milisekundách na operaci|Žádné dimenze|
|AverageWriteLatency|Zápis Průměrná latence|ms/op|Průměr|Zápis Průměrná latence v milisekundách na operaci|Žádné dimenze|
|FilesystemOtherOps|Systém souborů ostatní operace|OPS|Průměr|Číslo systému souborů ostatní operace (které není pro čtení nebo zápis)|Žádné dimenze|
|FilesystemReadOps|Operace čtení systému souborů|OPS|Průměr|Operace čtení počtu systému souborů|Žádné dimenze|
|FilesystemTotalOps|Celkový počet operací systému souborů|OPS|Průměr|Součet všech operací systému souborů|Žádné dimenze|
|FilesystemWriteOps|Operace zápisu systému souborů|OPS|Průměr|Počet operací zápisu systému souborů|Žádné dimenze|
|IoBytesPerOtherOps|Bajtů na vstupně-výstupních operací na ostatní operace|bajty/op|Průměr|Číslo z In/out bajtů na ostatní operace (který není pro čtení nebo zápis)|Žádné dimenze|
|IoBytesPerReadOps|Bajty vstupně-výstupních operací za operace čtení|bajty/op|Průměr|Počet bajtů za operace čtení In/out|Žádné dimenze|
|IoBytesPerTotalOps|Bajty vstupně-výstupních operací za op ve všech operacích|bajty/op|Průměr|Součet všech příchozí/odchozí provoz bajtů|Žádné dimenze|
|IoBytesPerWriteOps|Bajty vstupně-výstupních operací za operace zápisu|bajty/op|Průměr|Číslo In/out bajtů za operace zápisu|Žádné dimenze|
|OtherIops|Další vstupně-výstupních operací|operací za sekundu|Průměr|Druhý vstup a výstup operace za sekundu|Žádné dimenze|
|OtherThroughput|Další propustnost|MB/s|Průměr|Ostatní (který není pro čtení nebo zápisu) propustnost v MB za sekundu|Žádné dimenze|
|ReadIops|Vstupně-výstupních operací čtení|operací za sekundu|Průměr|Přečtěte si v vstupně-výstupní operace za sekundu|Žádné dimenze|
|ReadThroughput|Propustnost pro čtení|MB/s|Průměr|Propustnost čtení v MB za sekundu|Žádné dimenze|
|TotalIops|Celkový počet iops|operací za sekundu|Průměr|Součet všech vstup a výstup operací za sekundu|Žádné dimenze|
|TotalThroughput|Celková propustnost|MB/s|Průměr|Součet všech propustnost v MB za sekundu|Žádné dimenze|
|VolumeAllocatedSize|Přidělená velikost svazku|bajtů|Průměr|Přidělená velikost svazku (nikoli skutečné používá bajtů)|Žádné dimenze|
|VolumeLogicalSize|Logická velikost svazku|bajtů|Průměr|Logická velikost svazku (využité bajty)|Žádné dimenze|
|VolumeSnapshotSize|Velikost snímku svazku|bajtů|Průměr|Velikost všech snímků ve svazku|Žádné dimenze|
|WriteIops|Zápis iops|operací za sekundu|Průměr|Zápis vstup a výstup operací za sekundu|Žádné dimenze|
|WriteThroughput|Zápis propustnost|MB/s|Průměr|Zápis propustnost v MB za sekundu|Žádné dimenze|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Velikost fondu přidělené svazku|bajtů|Průměr|Přidělená velikost fondu (nikoli skutečné používá bajtů)|Žádné dimenze|
|VolumePoolAllocatedUsed|Svazek fondu přidělené použít|bajtů|Průměr|Použitá velikost přidělené fondu|Žádné dimenze|
|VolumePoolTotalLogicalSize|Logická velikost svazku fondu celkem|bajtů|Průměr|Součet logické velikosti všech svazků, které patří do fondu|Žádné dimenze|
|VolumePoolTotalSnapshotSize|Velikost fondu celkový snímku svazku|bajtů|Průměr|Součet všech snímků ve fondu|Žádné dimenze|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Count|celkem|Počet bajtů odeslaných síťové rozhraní|Žádné dimenze|
|BytesReceivedRate|Přijaté bajty|Count|celkem|Počet bajtů přijatých síťové rozhraní|Žádné dimenze|
|PacketsSentRate|Pakety odeslané|Count|celkem|Počet paketů síťové rozhraní odeslané|Žádné dimenze|
|PacketsReceivedRate|Obdržených paketů|Count|celkem|Počet paketů síťové rozhraní přijaté za sekundu|Žádné dimenze|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Cesta dostupnost dat|Count|Průměr|Průměrná dostupnost cesty dat nástroje pro vyrovnávání zatížení na dobu trvání|FrontendIPAddress, FrontendPort|
|DipAvailability|Sondy stavu|Count|Průměr|Průměrná nástroje pro vyrovnávání zatížení sondy stavu na dobu trvání|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Count|celkem|Celkový počet bajtů přenesených v časovém období|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Počet paketů|Count|celkem|Celkový počet paketů přenášet v časovém období|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Počet SYN|Count|celkem|Celkový počet paketů SYN přenášet v časovém období|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Počet připojení SNAT|Count|celkem|Celkový počet nových připojení SNAT vytvořených v časovém období|Vlastnost ConnectionState FrontendIPAddress BackendIPAddress,|
|AllocatedSnatPorts|Přidělené SNAT porty (Preview)|Count|celkem|Celkový počet portů SNAT přidělené v časovém období|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Používané porty SNAT (Preview)|Count|celkem|Celkový počet SNAT porty používané v časovém období|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Množství dotazů|Count|celkem|Počet dotazů, které jsou zpracovány pro zónu DNS|Žádné dimenze|
|RecordSetCount|Record Set Count|Count|Maximum|Počet sad záznamů v zóně DNS|Žádné dimenze|
|RecordSetCapacityUtilization|Sada záznamů využití kapacity|Percent|Maximum|Procento kapacity sady záznamů využívaných zóny DNS|Žádné dimenze|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|Příchozí pakety před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety před útoky DDoS|Žádné dimenze|
|PacketsDroppedDDoS|Příchozí pakety se zahodí před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety se zahodí před útoky DDoS|Žádné dimenze|
|PacketsForwardedDDoS|Příchozí pakety předané před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety předané před útoky DDoS|Žádné dimenze|
|TCPPacketsInDDoS|Příchozí pakety TCP před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety TCP před útoky DDoS|Žádné dimenze|
|TCPPacketsDroppedDDoS|Příchozí pakety TCP zahodí před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety TCP zahodí před útoky DDoS|Žádné dimenze|
|TCPPacketsForwardedDDoS|Příchozí pakety TCP předávají před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety TCP předávají před útoky DDoS|Žádné dimenze|
|UDPPacketsInDDoS|Příchozí pakety UDP před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety UDP před útoky DDoS|Žádné dimenze|
|UDPPacketsDroppedDDoS|Příchozí pakety UDP zahodí před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety UDP zahodí před útoky DDoS|Žádné dimenze|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předávají před útoky DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předávají před útoky DDoS|Žádné dimenze|
|BytesInDDoS|Příchozí bajty před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty před útoky DDoS|Žádné dimenze|
|BytesDroppedDDoS|Příchozí bajty vyřazeny před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty vyřazeny před útoky DDoS|Žádné dimenze|
|BytesForwardedDDoS|Příchozí bajty předané před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty předané před útoky DDoS|Žádné dimenze|
|TCPBytesInDDoS|Příchozí bajty TCP před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty TCP před útoky DDoS|Žádné dimenze|
|TCPBytesDroppedDDoS|Příchozí bajty TCP vyřazeny před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty TCP vyřazeny před útoky DDoS|Žádné dimenze|
|TCPBytesForwardedDDoS|Příchozí bajty TCP předané před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty TCP předané před útoky DDoS|Žádné dimenze|
|UDPBytesInDDoS|Příchozí bajty UDP před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP před útoky DDoS|Žádné dimenze|
|UDPBytesDroppedDDoS|Příchozí bajty UDP vyřazeny před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP vyřazeny před útoky DDoS|Žádné dimenze|
|UDPBytesForwardedDDoS|Příchozí bajty UDP předané před útoky DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP předané před útoky DDoS|Žádné dimenze|
|IfUnderDDoSAttack|V části před útoky DDoS útoku nebo ne|Count|Maximum|V části před útoky DDoS útoku nebo ne|Žádné dimenze|
|DDoSTriggerTCPPackets|Příchozí pakety protokolu TCP pro aktivaci omezení rizik útoků DDoS|CountPerSecond|Maximum|Příchozí pakety protokolu TCP pro aktivaci omezení rizik útoků DDoS|Žádné dimenze|
|DDoSTriggerUDPPackets|Příchozí pakety UDP k aktivaci omezení rizik útoků DDoS|CountPerSecond|Maximum|Příchozí pakety UDP k aktivaci omezení rizik útoků DDoS|Žádné dimenze|
|DDoSTriggerSYNPackets|Příchozí pakety SYN aktivovat omezení rizik útoků DDoS|CountPerSecond|Maximum|Příchozí pakety SYN aktivovat omezení rizik útoků DDoS|Žádné dimenze|
|VipAvailability|Cesta dostupnost dat|Count|Průměr|Průměrná dostupnost IP adresy na dobu trvání|Port|
|ByteCount|Počet bajtů|Count|celkem|Celkový počet bajtů přenesených v časovém období|Portů, směr|
|PacketCount|Počet paketů|Count|celkem|Celkový počet paketů přenášet v časovém období|Portů, směr|
|SynCount|Počet SYN|Count|celkem|Celkový počet paketů SYN přenášet v časovém období|Portů, směr|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet uplatnění pravidel aplikace|Count|celkem|Počet pokusů, které byly uplatnění pravidel aplikace|Protokol o stavu, důvodu|
|NetworkRuleHit|Počet průchodů pravidel sítě|Count|celkem|Počet pokusů, které byly uplatnění pravidel sítě|Protokol o stavu, důvodu|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|celkem|Počet bajtů za sekundu, které má služby Application Gateway|Žádné dimenze|
|UnhealthyHostCount|Není v pořádku. počet hostitelů|Count|Průměr|Počet hostitelů back-end není v pořádku|BackendSettingsPool|
|HealthyHostCount|V pořádku. počet hostitelů|Count|Průměr|Počet hostitelů v dobrém stavu back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Count|celkem|Počet úspěšných požadavků, které má služba Application Gateway obsluhuje|BackendSettingsPool|
|FailedRequests|Neúspěšné žádosti|Count|celkem|Počet neúspěšných žádostí, které má služba Application Gateway obsluhuje|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Count|celkem|Stav odpovědi HTTP vrácené Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální počet připojení|Count|celkem|Počet aktuální připojení ke službě Application Gateway|Žádné dimenze|
|CapacityUnits|Aktuální jednotky kapacity|Count|Průměr|Spotřebované jednotky kapacity|Žádné dimenze|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Brána S2S šířky pásma|BytesPerSecond|Průměr|Průměrná šířky pásma site-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SBandwidth|P2S šířku pásma|BytesPerSecond|Průměr|Průměrná šířky pásma point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Počet připojení P2S|Count|Maximum|Počet připojení point-to-site brány|Protocol|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName RemoteIP|
|TunnelEgressBytes|Odchozí bajty tunelového propojení|B|celkem|Odchozí bajty tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressBytes|Příchozí bajty tunelového propojení|B|celkem|Příchozí bajty tunelového propojení|ConnectionName RemoteIP|
|TunnelEgressPackets|Odchozí pakety tunelového propojení|Count|celkem|Počet odchozích paketů tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressPackets|Příchozí pakety tunelového propojení|Count|celkem|Počet příchozích paketů tunelového propojení|ConnectionName RemoteIP|
|TunnelEgressPacketDropTSMismatch|Zahození paketu neodpovídá odchozího přenosu dat TS tunelového propojení|Count|celkem|Počet odchozích paketů přetažení z neshoda selektor provozu tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressPacketDropTSMismatch|Zahození paketu neodpovídá příchozího přenosu dat TS tunelového propojení|Count|celkem|Počet příchozích paketů přetažení z neshoda selektor provozu tunelového propojení|ConnectionName RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Služba BITS ingressing Azure za sekundu|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Služba BITS egressing Azure za sekundu|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Služba BITS ingressing Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Služba BITS egressing Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Služba BITS ingressing Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Služba BITS egressing Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle koncový bod vrátil|Count|celkem|Počet pokusů, které v daném časovém rámci byl vrácen koncových bodů Traffic Manageru|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu pomocí koncového bodu|Count|Maximum|1, pokud koncového bodu sondu stav "povoleno", jinak 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Testů paměti se nezdařilo|Percent|Průměr|% testy monitorování připojení se nezdařilo|Žádné dimenze|
|AverageRoundtripMs|Střední Doba odezvy (ms)|Milisekundy|Průměr|Průměrná síťové operace round-trip doba (ms) pro připojení k monitorování mezi zdrojem a cílem odeslané testy paměti|Žádné dimenze|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Count|celkem|Počet požadavků klientů obsluhuje proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Velikost požadavku|B|celkem|Počet bajtů odeslaných na požadavky klientů na proxy server HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Velikost odpovědi|B|celkem|Počet bajtů odeslaných jako odpovědi ze serveru proxy HTTP/S pro klienty|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Count|celkem|Počet požadavků odesílat back-endů proxy HTTP/S|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Odezva back-endu požadavku|Milisekundy|Průměr|Čas počítají na základě když byla vyslána žádost proxy HTTP/S back-endu dokud proxy HTTP/S poslední bajt odpovědi přijaté z back-endu|Back-end|
|TotalLatency|Celková latence|Milisekundy|Průměr|Čas počítají na základě když žádost klienta byla přijata službou proxy HTTP/S dokud klient potvrzení poslední bajt odpovědi z proxy serveru HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stav back-endu|Percent|Průměr|Procento úspěšných stavů testy z proxy serveru HTTP/S na back-EndY|Back-endu, Bezproblémových|
|WebApplicationFirewallRequestCount|Počet požadavků brány Firewall webových aplikací|Count|celkem|Počet žádostí klienta zpracovaných rozhraním firewallu webových aplikací|PolicyName, Název_pravidla, akce|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registration.all|Registrace – operace|Count|celkem|Počet všech úspěšných operací s registrací (vytvoření, aktualizace, dotazy a odstranění) |Žádné dimenze|
|registration.create|Registrace – vytvořit operace|Count|celkem|Počet všech úspěšných vytvoření registrací|Žádné dimenze|
|registration.update|Registrace – aktualizovat operace|Count|celkem|Počet všech úspěšných aktualizací registrací|Žádné dimenze|
|registration.get|Registrace – přečíst operace|Count|celkem|Počet všech úspěšných dotazů registrací|Žádné dimenze|
|registration.delete|Registrace – odstranit operace|Count|celkem|Počet všech úspěšných odstranění registrací|Žádné dimenze|
|příchozí|Příchozí zprávy|Count|celkem|Počet všech úspěšných volání rozhraní API, pomocí kterých se odesílají data |Žádné dimenze|
|incoming.scheduled|Odeslaná plánovaná nabízená oznámení|Count|celkem|Plánovaná nabízená oznámení bylo zrušeno|Žádné dimenze|
|incoming.scheduled.cancel|Plánovaná nabízená oznámení bylo zrušeno|Count|celkem|Plánovaná nabízená oznámení bylo zrušeno|Žádné dimenze|
|Scheduled.Pending|Čekající plánovaná oznámení|Count|celkem|Čekající plánovaná oznámení|Žádné dimenze|
|installation.all|Operace správy instalace|Count|celkem|Operace správy instalace|Žádné dimenze|
|installation.get|Operace získání instalace|Count|celkem|Operace získání instalace|Žádné dimenze|
|installation.upsert|Vytvořit nebo aktualizovat operace instalace|Count|celkem|Vytvořit nebo aktualizovat operace instalace|Žádné dimenze|
|installation.patch|Opravit operace instalace|Count|celkem|Opravit operace instalace|Žádné dimenze|
|installation.delete|Operace odstranění instalace|Count|celkem|Operace odstranění instalace|Žádné dimenze|
|outgoing.allpns.success|Úspěšná oznámení|Count|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.allpns.invalidpayload|Chyby datové části|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy vrátil chybu, která se týká chybné datové části|Žádné dimenze|
|outgoing.allpns.pnserror|Chyby externího systému oznámení|Count|celkem|Počet nabídek, které nebyly úspěšné, protože při komunikaci se systémem oznámení platformy došlo k problému (nezahrnuje problémy s ověřováním)|Žádné dimenze|
|outgoing.allpns.channelerror|Chyby kanálů|Count|celkem|Počet nabídek, které nebyly úspěšné, protože kanál nebyl platný nebo přidružený ke správné aplikaci, byl omezený, nebo jeho platnost vypršela|Žádné dimenze|
|outgoing.allpns.badorexpiredchannel|Chyby – chybný kanál nebo vypršení časového limitu kanálu|Count|celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršel nebo není platný kanál, token nebo ID registrace|Žádné dimenze|
|outgoing.wns.Success|WNS – úspěšná oznámení|Count|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.wns.invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované (Windows Live nerozpozná přihlašovací údaje).|Žádné dimenze|
|outgoing.wns.badchannel|WNS – chyba špatného kanálu|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci nerozpoznal (stav služby nabízených oznámení Windows: 404 Nenalezeno).|Žádné dimenze|
|outgoing.wns.expiredchannel|WNS – chyba vypršení časového limitu kanálu|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože vypršela platnost parametr ChannelURI (stav služby nabízených oznámení Windows: 410 Gone).|Žádné dimenze|
|outgoing.wns.throttled|WNS – omezená oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože služby nabízených oznámení Windows omezuje tuto aplikaci (stav služby nabízených oznámení Windows: 406 Nepřijatelný).|Žádné dimenze|
|outgoing.wns.tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Count|celkem|Služby Windows Live nejsou dostupné.|Žádné dimenze|
|outgoing.wns.invalidtoken|WNS – chyby autorizace (neplatný token)|Count|celkem|Token se poskytl službě nabízených oznámení Windows není platný (stav služby nabízených oznámení Windows: 401 Neautorizováno).|Žádné dimenze|
|outgoing.wns.wrongtoken|WNS – chyby autorizace (chybný token)|Count|celkem|Token se poskytl službě nabízených oznámení Windows je platný, ale pro jinou aplikaci (stav služby nabízených oznámení Windows: 403 Zakázáno). To může nastat, pokud je parametr ChannelURI v registraci přidružený k jiné aplikaci. Zkontrolujte, že klientská aplikace souvisí s stejnou aplikaci, jehož přihlašovací údaje jsou v centru oznámení.|Žádné dimenze|
|outgoing.wns.invalidnotificationformat|WNS – neplatný formát oznámení|Count|celkem|Formát oznámení není platný (stav služby nabízených oznámení Windows: 400). Všimněte si, že služby nabízených oznámení Windows není odmítnout všechny neplatné datové části.|Žádné dimenze|
|outgoing.wns.invalidnotificationsize|WNS – chyba neplatné velikosti oznámení|Count|celkem|Datová část oznámení je moc velká (stav služby nabízených oznámení Windows: 413).|Žádné dimenze|
|outgoing.wns.channelthrottled|WNS – omezený kanál|Count|celkem|Oznámení byla zahozena, protože se omezuje parametr ChannelURI v registraci (hlavička odpovědi služby nabízených oznámení Windows: X-WNS-NotificationStatus:channelThrottled).|Žádné dimenze|
|outgoing.wns.channeldisconnected|WNS – odpojený kanál|Count|celkem|Oznámení byla zahozena, protože se omezuje parametr ChannelURI v registraci (hlavička odpovědi služby nabízených oznámení Windows: X-WNS-DeviceConnectionStatus: odpojení).|Žádné dimenze|
|outgoing.wns.dropped|WNS – vynechaná oznámení|Count|celkem|Oznámení se ignoruje, protože v registraci se omezuje parametr ChannelURI (X-WNS-NotificationStatus: dropped, ale ne X-WNS-DeviceConnectionStatus: disconnected).|Žádné dimenze|
|outgoing.wns.pnserror|WNS – chyby|Count|celkem|Oznámení se nedoručilo kvůli chybám při komunikaci se Službou nabízených oznámení Windows.|Žádné dimenze|
|outgoing.wns.authenticationerror|WNS – chyby ověřování|Count|celkem|Oznámení se nedoručilo kvůli chybám při komunikaci s Windows Live, neplatným přihlašovacím údajům, nebo chybnému tokenu.|Žádné dimenze|
|outgoing.apns.success|APNS – úspěšná oznámení|Count|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.apns.invalidcredentials|Chyby autorizace APNs|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.apns.badchannel|Chyba APNS – špatný kanál|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože token je neplatný (kód stavu APNS: 8).|Žádné dimenze|
|outgoing.apns.expiredchannel|Chyba APNS – kanálu vypršel časový limit|Count|celkem|Počet tokenů, kterým kanál zpětné vazby APNs zrušil platnost|Žádné dimenze|
|outgoing.apns.invalidnotificationsize|APNS – chyba neplatné velikosti oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože datová část byla moc velká (kód stavu APNS: 7).|Žádné dimenze|
|outgoing.apns.pnserror|APNS – chyby|Count|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNs|Žádné dimenze|
|outgoing.gcm.success|GCM – úspěšná oznámení|Count|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.gcm.invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.gcm.badchannel|GCM – chyba špatného kanálu|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože registrationId v registraci nerozpoznal (výsledek GCM: Neplatná registrace).|Žádné dimenze|
|outgoing.gcm.expiredchannel|GCM – chyba vypršení časového limitu kanálu|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože byl vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádné dimenze|
|outgoing.gcm.throttled|GCM – omezená oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože služba GCM omezila tuto aplikaci (kód stavu GCM: 501 – 599 nebo výsledek: není k dispozici).|Žádné dimenze|
|outgoing.gcm.invalidnotificationformat|GCM – neplatný formát oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože datová část nemá správný formát (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádné dimenze|
|outgoing.gcm.invalidnotificationsize|GCM – chyba neplatné velikosti oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože datová část byla moc velká (výsledek GCM: MessageTooBig).|Žádné dimenze|
|outgoing.gcm.wrongchannel|GCM – chyba nesprávného kanálu|Count|celkem|Počet nabídek, které se nezdařila, protože registrationId v registraci není přidružené k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádné dimenze|
|outgoing.gcm.pnserror|GCM – chyby|Count|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádné dimenze|
|outgoing.gcm.authenticationerror|Chyby ověřování GCM|Count|celkem|Počet nabídek, které se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje přihlašovací údaje jsou blokovány, nebo v aplikaci není správně nakonfigurované SenderId (výsledek GCM: MismatchedSenderId).|Žádné dimenze|
|outgoing.mpns.success|MPNS – úspěšná oznámení|Count|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.mpns.invalidcredentials|MPNS – neplatné přihlašovací údaje|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.mpns.badchannel|MPNS – chyba špatného kanálu|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci nerozpoznal (stav MPNS: 404 Nenalezeno).|Žádné dimenze|
|outgoing.mpns.throttled|MPNS – omezená oznámení|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 Nepřijatelný).|Žádné dimenze|
|outgoing.mpns.invalidnotificationformat|MPNS – neplatný formát oznámení|Count|celkem|Počet nabídek, které nebyly úspěšné, protože datová část v oznámení byla moc velká|Žádné dimenze|
|outgoing.mpns.channeldisconnected|MPNS – odpojený kanál|Count|celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci byl odpojen. (stav MPNS: 412 Nenalezeno).|Žádné dimenze|
|outgoing.mpns.dropped|MPNS – vynechaná oznámení|Count|celkem|Počet nabídek, které byly zahozeny MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo Potlačené).|Žádné dimenze|
|outgoing.mpns.pnserror|MPNS – chyby|Count|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádné dimenze|
|outgoing.mpns.authenticationerror|MPNS – chyby ověřování|Count|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|notificationhub.pushes|Všechna odchozí oznámení|Count|celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|incoming.all.requests|Všechny příchozí požadavky|Count|celkem|Celkový počet příchozích požadavků pro centrum oznámení|Žádné dimenze|
|Incoming.all.failedrequests|Všechny neúspěšné příchozí požadavky|Count|celkem|Celkový počet neúspěšných příchozích požadavků pro centrum oznámení|Žádné dimenze|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_ % volných uzlů Inode|% Volných uzlů Inode|Count|Průměr|Average_ % volných uzlů Inode|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|% Volného místa|Count|Průměr|Average_ % volného místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % použitých uzlů Inode|% Použitých uzlů Inode|Count|Průměr|Average_ % použitých uzlů Inode|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % využitého místa|% Využitého místa|Count|Průměr|Average_ % využitého místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené bajty/s|Bajty čtení z disku/s|Count|Průměr|Average_Disk přečtené bajty/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené strany/s|Čtení disku/s|Count|Průměr|Average_Disk přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Přenosy disku/s|Count|Průměr|Average_Disk přenosy/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané bajty/s|Bajty zapisování na disk/s|Count|Průměr|Average_Disk zapsané bajty/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané strany/s|Zápis disku/s|Count|Průměr|Average_Disk zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free v megabajtech|Volné megabajty|Count|Průměr|Average_Free v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Logical bajtů disku/s|Bajtů logického disku/s|Count|Průměr|Average_Logical bajtů disku/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Dostupná paměť v % Average_|Dostupná paměť v %|Count|Průměr|Dostupná paměť v % Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Count|Průměr|Average_ % dostupného odkládacího prostoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % využité paměti|% Využité paměti|Count|Průměr|Average_ % využité paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Využitý prostor záměny v Average_ %|Využitý prostor záměny v %|Count|Průměr|Využitý prostor záměny v Average_ %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available paměť v MB|Dostupná paměť v MB|Count|Průměr|Average_Available paměť v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available prostor záměny v MB|Dostupný prostor záměny v MB|Count|Průměr|Average_Available prostor záměny v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page přečtené strany/s|Čtení stránek/s|Count|Průměr|Average_Page přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page zapsané strany/s|Zápisy stránek/s|Count|Průměr|Average_Page zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pages za sekundu|Stránky/s|Count|Průměr|Average_Pages za sekundu|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used prostor záměny v MB|Použitý odkládací prostor v MB místa|Count|Průměr|Average_Used prostor záměny v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used paměť v MB|Použitá paměť v MB|Count|Průměr|Average_Used paměť v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Odeslané bajty Average_Total|Celkový počet bajtů přenesených|Count|Průměr|Odeslané bajty Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Count|Průměr|Přijaté bajty Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total bajtů|Bajty celkem|Count|Průměr|Average_Total bajtů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total pakety odesílané informace|Celkový počet paketů odesílané informace|Count|Průměr|Average_Total pakety odesílané informace|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total obdržených paketů|Celkový počet přijatých paketů|Count|Průměr|Average_Total obdržených paketů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby příjmu Average_Total|Chyby celkem příjmu|Count|Průměr|Chyby příjmu Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby odeslání Average_Total|Celkový počet odesílání chyb|Count|Průměr|Chyby odeslání Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Count|Průměr|Average_Total kolizí|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Průměr|Average_Avg. Doba disku/čtení|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední Doba disku/přenos|Count|Průměr|Average_Avg. Doba disku/přenos|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Průměr|Average_Avg. Doby disku/zápis|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Physical bajtů disku/s|Bajtů fyzického disku/s|Count|Průměr|Average_Physical bajtů disku/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct privilegovaného času|Procento privilegovaného času|Count|Průměr|Average_Pct privilegovaného času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct uživatelského času|Procento uživatelského času|Count|Průměr|Average_Pct uživatelského času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|KB paměti Average_Used|Využité paměti kB|Count|Průměr|KB paměti Average_Used|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Virtual sdílené paměti|Sdílené virtuální paměti|Count|Průměr|Average_Virtual sdílené paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času DPC Average_|Čas DPC v %|Count|Průměr|% Času DPC Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ čas nečinnosti v %|Čas nečinnosti v %|Count|Průměr|Average_ čas nečinnosti v %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času přerušení Average_|Čas přerušení v %|Count|Průměr|% Času přerušení Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Doba čekání na vstupně-výstupních operací Average_ %|Doba čekání % vstupně-výstupních operací|Count|Průměr|Doba čekání na vstupně-výstupních operací Average_ %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ dobrý čas v %|Dobrý čas v %|Count|Průměr|Average_ dobrý čas v %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % privilegovaného času|% Privilegovaného času|Count|Průměr|Average_ % privilegovaného času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|% Času procesoru|Count|Průměr|Average_ % času procesoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % uživatelského času|Uživatelský čas v %|Count|Průměr|Average_ % uživatelského času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free fyzické paměti|Volná fyzická paměť|Count|Průměr|Average_Free fyzické paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free místo ve stránkovacích souborech|Volné místo ve stránkovacích souborech|Count|Průměr|Average_Free místo ve stránkovacích souborech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free virtuální paměti|Volná virtuální paměť|Count|Průměr|Average_Free virtuální paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Processes|Procesy|Count|Průměr|Average_Processes|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Size uložená v stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Count|Průměr|Average_Size uložená v stránkovacích souborech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Uptime|Doba provozu|Count|Průměr|Average_Uptime|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Users|Uživatelé|Count|Průměr|Average_Users|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Průměr|Average_Avg. Doba disku/čtení|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Průměr|Average_Avg. Doby disku/zápis|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Count|Průměr|Délka fronty disku Average_Current|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené strany/s|Čtení disku/s|Count|Průměr|Average_Disk přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Přenosy disku/s|Count|Průměr|Average_Disk přenosy/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané strany/s|Zápis disku/s|Count|Průměr|Average_Disk zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free v megabajtech|Volné megabajty|Count|Průměr|Average_Free v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|% Volného místa|Count|Průměr|Average_ % volného místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available paměť v megabajtech|Počet MB k dispozici|Count|Průměr|Average_Available paměť v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % potvrzených bajtů|% Využití potvrzených bajtů|Count|Průměr|Average_ % potvrzených bajtů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Přijaté Average_Bytes/s|Přijaté bajty/s|Count|Průměr|Přijaté Average_Bytes/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Odeslané Average_Bytes/s|Odeslané bajty/s|Count|Průměr|Odeslané Average_Bytes/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Count|Průměr|Average_Bytes celkem/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|% Času procesoru|Count|Průměr|Average_ % času procesoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Count|Průměr|Délka fronty Average_Processor|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Prezenční signál|Prezenční signál|Count|celkem|Prezenční signál|Počítače, OSType, verze, SourceComputerId|
|Aktualizace|Aktualizace|Count|Průměr|Aktualizace|Počítače, produktů, klasifikace, UpdateState volitelné, schválené|
|Událost|Událost|Count|Průměr|Událost|Zdroj protokolu událostí, počítače, EventCategory, EventLevel, EventLevelName, ID události|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazu jazyka DAX v posledního intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Count|Průměr|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|Vysoké využití QPU|Count|celkem|Vysoké využití QPU za poslední minutu, 1 pro využití vysoké QPU, jinak 0|Žádné dimenze|
|memory_metric|Memory (Paměť)|B|Průměr|Paměť. Rozsah 0 – 3 GB pro A1, 0 – 5 GB pro A2, A3 0 až 10 GB, 0-25 GB pro A4, 0 – 50 GB pro A5 a 0 – 100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Percent|Průměr|Průměrný thrashing paměti.|Žádné dimenze|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|celkem|Úspěšná připojení ListenerConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|celkem|ClientError v ListenerConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|celkem|ServerError na ListenerConnections pro Microsoft.Relay|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|celkem|Úspěšná připojení SenderConnections pro Microsoft.Relay|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|celkem|ClientError v SenderConnections pro Microsoft.Relay|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|celkem|ServerError na SenderConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|celkem|ListenerConnections celkem pro Microsoft.Relay|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|celkem|Celkem žádostí SenderConnections pro Microsoft.Relay|EntityName|
|ActiveConnections|ActiveConnections|Count|celkem|ActiveConnections celkem pro Microsoft.Relay|EntityName|
|ActiveListeners|ActiveListeners|Count|celkem|ActiveListeners celkem pro Microsoft.Relay|EntityName|
|BytesTransferred|BytesTransferred|Count|celkem|BytesTransferred celkem pro Microsoft.Relay|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|celkem|ListenerDisconnects celkem pro Microsoft.Relay|EntityName|
|SenderDisconnects|SenderDisconnects|Count|celkem|SenderDisconnects celkem pro Microsoft.Relay|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence vyhledávání|Sekundy|Průměr|Hledání Průměrná latence pro službu search|Žádné dimenze|
|SearchQueriesPerSecond|Vyhledávací dotazy za sekundu|CountPerSecond|Průměr|Vyhledávací dotazy za sekundu pro vyhledávací službu|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Percent|Průměr|Procento vyhledávacích dotazů, které byly omezené služby search|Žádné dimenze|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|celkem|Celkový počet úspěšných žádostí pro obor názvů (Preview)|EntityName|
|ServerErrors|Chyby serveru (Preview)|Count|celkem|Chyby serveru pro Microsoft.ServiceBus (Preview)|EntityName|
|UserErrors|Chyby uživatele (Preview)|Count|celkem|Chyby uživatele pro Microsoft.ServiceBus (Preview)|EntityName|
|ThrottledRequests|Omezené požadavky (Preview)|Count|celkem|Omezené požadavky pro Microsoft.ServiceBus (Preview)|EntityName|
|IncomingRequests|Příchozí žádosti (Preview)|Count|celkem|Příchozí požadavky pro Microsoft.ServiceBus (Preview)|EntityName|
|IncomingMessages|Příchozí zprávy (Preview)|Count|celkem|Příchozí zprávy pro Microsoft.ServiceBus (Preview)|EntityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|celkem|Odchozí zprávy pro Microsoft.ServiceBus (Preview)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Count|celkem|Celkový počet aktivních připojení pro Microsoft.ServiceBus (Preview)|Žádné dimenze|
|Velikost|Velikost (Preview)|B|Průměr|Velikost fronty nebo tématu v bajtech (Preview)|EntityName|
|Zprávy|Počet zpráv ve frontě nebo tématu (Preview)|Count|Průměr|Počet zpráv ve frontě nebo tématu (Preview)|EntityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu (Preview)|Count|Průměr|Počet aktivních zpráv ve frontě nebo tématu (Preview)|EntityName|
|DeadletteredMessages|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|Count|Průměr|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|EntityName|
|ScheduledMessages|Počet plánovaných zpráv ve frontě nebo tématu (Preview)|Count|Průměr|Počet plánovaných zpráv ve frontě nebo tématu (Preview)|EntityName|
|CPUXNS|Využití CPU na obor názvů|Percent|Maximum|Metrika využití procesoru v oboru názvů služby Service Bus na úrovni Premium |Žádné dimenze|
|WSXNS|Využití paměti na obor názvů|Percent|Maximum|Metrika využití paměti v oboru názvů služby Service Bus na úrovni Premium |Žádné dimenze|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Průměr|Procesor přidělené do tohoto kontejneru v jednotkách millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|B|Průměr|Paměť přidělená pro tento kontejner v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Průměr|Skutečné využití procesoru v jednotkách millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|B|Průměr|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Průměr|Využití procesoru pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Průměr|Využití procesoru pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Průměr|Stav aplikace Service Fabric mřížky|ApplicationName, stav|
|ServiceStatus|ServiceStatus|Count|Průměr|Stav služby aplikace Service Fabric mřížky|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Průměr|Stav služby repliky aplikace Service Fabric mřížky|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Průměr|Stav kontejneru v aplikaci Service Fabric mřížky|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Průměr|Restartovat počet kontejneru v aplikaci Service Fabric mřížky|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Count|Maximum|Počet připojení uživatelů.|Koncový bod|
|MessageCount|Počet zpráv|Count|celkem|Celkový počet zpráv.|Žádné dimenze|
|InboundTraffic|Příchozí provoz|B|celkem|Příchozí provoz služby|Žádné dimenze|
|OutboundTraffic|Odchozí provoz|B|celkem|Odchozí provoz služby|Žádné dimenze|
|UserErrors|Chyby uživatele|Percent|Maximum|Procento chyb uživatele|Žádné dimenze|
|SystemErrors|Chyby systému|Percent|Maximum|Procento chyb systému|Žádné dimenze|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Percent|Průměr|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento V/V dat|Percent|Průměr|Procento V/V dat|Žádné dimenze|
|log_write_percent|Procento V/V protokolu|Percent|Průměr|Procento V/V protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Průměr|Procento DTU|Žádné dimenze|
|úložiště|Využitý datový prostor|B|Maximum|Celkovou velikost databáze|Žádné dimenze|
|connection_successful|Úspěšná připojení|Count|celkem|Úspěšná připojení|Žádné dimenze|
|connection_failed|Chyba připojení|Count|celkem|Chyba připojení|Žádné dimenze|
|blocked_by_firewall|Blokovaná bránou Firewall|Count|celkem|Blokovaná bránou Firewall|Žádné dimenze|
|Zablokování|Zablokování|Count|celkem|Zablokování|Žádné dimenze|
|storage_percent|Procento využité dat|Percent|Maximum|Procento velikosti databáze|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Percent|Průměr|Procento úložiště OLTP v paměti|Žádné dimenze|
|workers_percent|Procento prac. procesů|Percent|Průměr|Procento prac. procesů|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Průměr|Procento relací|Žádné dimenze|
|dtu_limit|Omezení jednotek DTU|Count|Průměr|Omezení jednotek DTU|Žádné dimenze|
|dtu_used|DTU použít|Count|Průměr|DTU použít|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Průměr|Limit procesoru|Žádné dimenze|
|cpu_used|Využití procesoru|Count|Průměr|Využití procesoru|Žádné dimenze|
|dwu_limit|Limit jednotky|Count|Maximum|Limit jednotky|Žádné dimenze|
|dwu_consumption_percent|Procento DWU|Percent|Maximum|Procento DWU|Žádné dimenze|
|dwu_used|Použít DWU|Count|Maximum|Použít DWU|Žádné dimenze|
|dw_cpu_percent|Úrovni uzlu DW procento využití procesoru|Percent|Průměr|Úrovni uzlu DW procento využití procesoru|DwLogicalNodeId|
|dw_physical_data_read_percent|Procento datových v/v úrovně uzlu DW|Percent|Průměr|Procento datových v/v úrovně uzlu DW|DwLogicalNodeId|
    |cache_hit_percent|Procenta přístupů do mezipaměti|Percent|Maximum|Procenta přístupů do mezipaměti|Žádné dimenze|
|cache_used_percent|Procento použitá mezipaměť|Percent|Maximum|Procento použitá mezipaměť|Žádné dimenze|
|local_tempdb_usage_percent|Procento místní databáze tempdb|Percent|Průměr|Procento místní databáze tempdb|Žádné dimenze|
|app_cpu_billed|Aplikace fakturovaná podle procesoru|Count|celkem|Aplikace fakturovaná podle procesoru|Žádné dimenze|
|app_cpu_percent|Procento využití procesoru aplikace|Percent|Průměr|Procento využití procesoru aplikace|Žádné dimenze|
|app_memory_percent|Procento využité paměti aplikace|Percent|Průměr|Procento využité paměti aplikace|Žádné dimenze|
|allocated_data_storage|Přidělený datový prostor|B|Průměr|Přidělený datový prostor|Žádné dimenze|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Percent|Průměr|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento V/V dat|Percent|Průměr|Procento V/V dat|Žádné dimenze|
|log_write_percent|Procento V/V protokolu|Percent|Průměr|Procento V/V protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Průměr|Procento DTU|Žádné dimenze|
|storage_percent|Procento využité dat||Percent|Průměr|Procento úložiště|Žádné dimenze|
|workers_percent|Procento prac. procesů|Percent|Průměr|Procento prac. procesů|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Průměr|Procento relací|Žádné dimenze|
|eDTU_limit|omezení eDTU|Count|Průměr|omezení eDTU|Žádné dimenze|
|storage_limit|Maximální velikost dat|B|Průměr|Limit úložiště.|Žádné dimenze|
|eDTU_used|použít eDTU|Count|Průměr|použít eDTU|Žádné dimenze|
|storage_used|Využitý datový prostor|B|Průměr|Využité úložiště|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Percent|Průměr|Procento úložiště OLTP v paměti|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Průměr|Limit procesoru|Žádné dimenze|
|cpu_used|Využití procesoru|Count|Průměr|Využití procesoru|Žádné dimenze|
|allocated_data_storage|Přidělený datový prostor|B|Průměr|Přidělený datový prostor|Žádné dimenze|
|allocated_data_storage_percent|Procento přidělené místo na data|Percent|Maximum|Procento přidělené místo na data|Žádné dimenze|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Count|Průměr|Počet virtuálních jader|Žádné dimenze|
|avg_cpu_percent|Průměrné procento CPU|Percent|Průměr|Průměrné procento CPU|Žádné dimenze|
|reserved_storage_mb|Vyhrazený úložný prostor|Count|Průměr|Vyhrazený úložný prostor|Žádné dimenze|
|storage_space_used_mb|Prostor úložiště využitý|Count|Průměr|Prostor úložiště využitý|Žádné dimenze|
|io_requests|Počet žádostí v/v úrovně|Count|Průměr|Počet žádostí v/v úrovně|Žádné dimenze|
|io_bytes_read|Přečtené bajty vstupně-výstupních operací|B|Průměr|Přečtené bajty vstupně-výstupních operací|Žádné dimenze|
|io_bytes_written|Zapsané bajty vstupně-výstupních operací|B|Průměr|Zapsané bajty vstupně-výstupních operací|Žádné dimenze|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Použitá kapacita|B|Průměr|Kapacita využitá účtem|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita služby Blob|B|Průměr|Velikost úložiště využitá službou Blob Service účtu úložiště v bajtech|BlobType, Tier|
|BlobCount|Počet objektů blob|Počet|Celkem|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType|       |BlobCount|Počet objektů blob|Count|Průměr|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType, Tier|
|ContainerCount|Počet kontejnerů služby Blob|Počet|Průměr|Počet kontejnerů ve službě Blob Service účtu úložiště|Žádné dimenze|
|IndexCapacity|Kapacita indexu|B|Průměr|Množství úložiště v bajtech, které používá index ADLS Gen2 (Hierarchical)|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FileCapacity|Kapacita služby File|B|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Žádné dimenze|
|FileCount|Počet souborů|Count|Průměr|Počet souborů ve službě File účtu úložiště|Žádné dimenze|
|FileShareCount|Počet sdílených složek služby File|Count|Průměr|Počet sdílených složek ve službě File účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita služby Queue|B|Průměr|Velikost úložiště využitá službou Queue účtu úložiště v bajtech|Žádné dimenze|
|QueueCount|Počet front|Počet|Průměr|Počet front ve službě Queue účtu úložiště|Žádné dimenze|
|QueueMessageCount|Počet zpráv fronty|Počet|Průměr|Přibližný počet zpráv fronty ve službě Queue účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita služby Table|B|Průměr|Velikost úložiště využitá službou Table Service účtu úložiště v bajtech|Žádné dimenze|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek ve službě Table Service účtu úložiště|Žádné dimenze|
|TableEntityCount|Počet entit tabulek|Počet|Průměr|Počet entit tabulek ve službě Table Service účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Výsledek relace synchronizace|Count|Průměr|Metriky, protokoly a hodnotu 1 každý času koncový bod serveru byl úspěšně dokončí relaci synchronizace s koncový bod cloudu.|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Count|celkem|Počet souborů synchronizované|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Count|celkem|Počet souborů, které se nepovedlo synchronizovat|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bajty synchronizované|B|celkem|Celková velikost souborů byl přenesen v případě relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Count|Maximum|Metriky, protokoly a hodnotu 1 každý času registrovaný server byl úspěšně záznamy prezenčního signálu se koncový bod cloudu.|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odvolání vrstvení cloudu|B|celkem|Celková velikost dat připomenout serverem|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|% využití SU|Percent|Maximum|% využití SU|LogicalName, PartitionId|
|Situací|Vstupní události|Count|celkem|Vstupní události|LogicalName, PartitionId|
|InputEventBytes|Bajty vstupních událostí|B|celkem|Bajty vstupních událostí|LogicalName, PartitionId|
|LateInputEvents|Pozdní vstupní události|Count|celkem|Pozdní vstupní události|LogicalName, PartitionId|
|Výstupní události|Výstupní události|Count|celkem|Výstupní události|LogicalName, PartitionId|
|ConversionErrors|Chyby převodu dat|Count|celkem|Chyby převodu dat|LogicalName, PartitionId|
|Chyby|Chyby za běhu|Count|celkem|Chyby za běhu|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Count|celkem|Události mimo pořadí|LogicalName, PartitionId|
|AMLCalloutRequests|Požadavky na funkce|Count|celkem|Požadavky na funkce|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Nezdařené požadavky na funkce|Count|celkem|Nezdařené požadavky na funkce|LogicalName, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Count|celkem|Události funkcí|LogicalName, PartitionId|
|DeserializationError|Chyby deserializace vstupu|Count|celkem|Chyby deserializace vstupu|LogicalName, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Count|celkem|Události předčasného vstupu|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Vstupní události v backlogu|Count|Maximum|Vstupní události v backlogu|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Count|celkem|Přijaté vstupní zdroje|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí přenos dat přijatých zpráv|Count|celkem|Počet zpráv, které číst ze všech centra událostí nebo službu IoT hub zdroje událostí|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijatých neplatné zprávy|Count|celkem|Počet zpráv neplatné čtení z všechna centra událostí nebo službu IoT hub zdroje událostí|Žádné dimenze|
|IngressReceivedBytes|Příchozí přenos dat přijatých bajtů|B|celkem|Počet bajtů načtených ze všech zdrojů událostí|Žádné dimenze|
|IngressStoredBytes|Příchozí přenos dat uložené bajtů|B|celkem|Celková velikost událostí úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressStoredEvents|Příchozí přenos dat uložených událostí|Count|celkem|Počet událostí plochá úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Příchozí přenos dat přijatých zpráv časový interval|Sekundy|Maximum|Rozdíl mezi časem, který je zpráva zařazených do fronty ve zdroji událostí a čas, kdy se zpracovávají v příchozího přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počet přijatých zpráv příchozího přenosu dat|Count|Průměr|Rozdíl mezi pořadové číslo poslední zprávy ve frontě událostí zdroje oddílu a pořadovým číslem zprávy jsou zpracovávány v příchozího přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností horké úložiště|Count|Maximum|Maximální počet vlastností používá prostředí povolenou pro skladovou Položku S1, S2 a maximální počet vlastností záložním Store povolenou pro skladovou Položku průběžné platby|Žádné dimenze|
|WarmStorageUsedProperties|Horké úložiště použít vlastnosti |Count|Maximum|Počet vlastnosti používané prostředím pro skladovou Položku S1, S2 a počet vlastnosti používané ve Store záložním pro skladovou Položku průběžné platby|Žádné dimenze|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí přenos dat přijatých zpráv|Count|celkem|Počet zpráv, které číst ze zdroje události|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijatých neplatné zprávy|Count|celkem|Počet zpráv neplatná číst ze zdroje události|Žádné dimenze|
|IngressReceivedBytes|Příchozí přenos dat přijatých bajtů|B|celkem|Počet bajtů načtených ze zdroje události|Žádné dimenze|
|IngressStoredBytes|Příchozí přenos dat uložené bajtů|B|celkem|Celková velikost událostí úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressStoredEvents|Příchozí přenos dat uložených událostí|Count|celkem|Počet událostí plochá úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Příchozí přenos dat přijatých zpráv časový interval|Sekundy|Maximum|Rozdíl mezi časem, který je zpráva zařazených do fronty ve zdroji událostí a čas, kdy se zpracovávají v příchozího přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počet přijatých zpráv příchozího přenosu dat|Count|Průměr|Rozdíl mezi pořadové číslo poslední zprávy ve frontě událostí zdroje oddílu a pořadovým číslem zprávy jsou zpracovávány v příchozího přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností horké úložiště|Count|Maximum|Maximální počet vlastností používá prostředí povolenou pro skladovou Položku S1, S2 a maximální počet vlastností záložním Store povolenou pro skladovou Položku průběžné platby|Žádné dimenze|
|WarmStorageUsedProperties|Horké úložiště použít vlastnosti |Count|Maximum|Počet vlastnosti používané prostředím pro skladovou Položku S1, S2 a počet vlastnosti používané ve Store záložním pro skladovou Položku průběžné platby|Žádné dimenze|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty čtení z disku/s|BytesPerSecond|Průměr|Průměrná propustnost z důvodu operace čtení za období vzorku.|Žádné dimenze|
|DiskWriteBytesPerSecond|Bajty zápisu na disk/s|BytesPerSecond|Průměr|Průměrná propustnost kvůli operací zápisu po dobu vzorku.|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|B|celkem|Celková propustnost disku z důvodu operace čtení za období vzorku.|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|B|celkem|Celková propustnost disku z důvodu operací zápisu po dobu vzorku.|Žádné dimenze|
|DiskReadOperations|Operace čtení disku|Count|celkem|Počet vstupně-výstupní operace čtení v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|DiskWriteOperations|Operací zápisu na disk|Count|celkem|Počet vstupně-výstupní operace zápisu v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupní operace čtení v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupní operace zápisu v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|DiskReadLatency|Latence čtení disku|Milisekund|Průměr|Celková latence čtení. Latence pro čtení součet zařízení a jádra.|Žádné dimenze|
|DiskWriteLatency|Latence zápisu u disku|Milisekund|Průměr|Latence zápisu celkový počet. Latence zápisu součet zařízení a jádra.|Žádné dimenze|
|NetworkInBytesPerSecond|Síť v bajty/s|BytesPerSecond|Průměr|Propustnost sítě průměrné pro přijaté přenosy.|Žádné dimenze|
|NetworkOutBytesPerSecond|Síť si bajty/s|BytesPerSecond|Průměr|Propustnost sítě průměrné pro přenášená provoz.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|celkem|Propustnost sítě celkový počet přijatých provozu.|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|celkem|Propustnost sítě celkový počet přenesených provozu.|Žádné dimenze|
|MemoryUsed|Využité paměti|B|Průměr|Množství paměti počítače, který se používá ve virtuálním počítači.|Žádné dimenze|
|MemoryGranted|Paměť udělena|B|Průměr|Velikost paměti, která byla udělena k virtuálnímu počítači podle hostitele. Paměť nebylo uděleno k hostiteli, až ji je dotčená jednou uděleného může paměti výměnu nebo ballooned okamžitě, pokud je nutné VMkernel paměť.|Žádné dimenze|
|MemoryActive|Aktivní v paměti|B|Průměr|Množství paměti používané virtuálním Počítačem v posledních malé časové okno. Toto je "true" počet velikost paměti virtuálního počítače má aktuálně potřebu. Další, nevyužité paměti může automaticky nebo ballooned bez jakéhokoli dopadu na výkon hosta.|Žádné dimenze|
|Procento CPU|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je nahlásí se 100 % představující všechny jader procesoru v systému. Jako příklad je virtuální počítač 2-způsob, jak pomocí 50 % čtyři jádra systému úplně pomocí dvě jádra.|Žádné dimenze|
|PercentageCpuReady|Procento procesoru připravené|Milisekund|celkem|Ready time spadá čas věnovat časový limit na CPU(s) k dispozici v posledních interval aktualizace.|Žádné dimenze|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento CPU|Percent|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Percent|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Count|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty HTTP|Count|Průměr|Délka fronty HTTP|Instance|
|BytesReceived|Vstupní data|B|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|B|celkem|Výstupní data|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (s výjimkou funkce)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|celkem|Čas procesoru|Instance|
|Požadavky|Požadavky|Count|celkem|Požadavky|Instance|
|BytesReceived|Vstupní data|B|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|B|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Count|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Count|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Count|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Count|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Count|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Count|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Count|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|AppConnections|Připojení|Count|Průměr|Připojení|Instance|
|Popisovače|Počet popisovačů|Count|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Count|Průměr|Počet vláken|Instance|
|PrivateBytes|Privátní bajty|B|Průměr|Privátní bajty|Instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|celkem|V/V – přečtené bajty za sekundu|Instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|celkem|V/V – zapsané bajty za sekundu|Instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|celkem|V/V – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|celkem|V/V – operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|celkem|V/V – operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|celkem|V/V – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Průměr|Požadavky ve frontě aplikace|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|celkem|Uvolnění paměti 0. generace|Instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|celkem|Uvolnění paměti 1. generace|Instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|celkem|Uvolnění paměti 2. generace|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Vstupní data|B|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|B|celkem|Výstupní data|Instance|
|Http5xx|Chyby serveru HTTP|Count|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Průměr|Průměrná pracovní sada paměti|Instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|MB / MS|celkem|[Jednotky provádění funkcí](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|celkem|Počet spuštění funkce|Instance|
|PrivateBytes|Privátní bajty|B|Průměr|Privátní bajty|Instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|celkem|V/V – přečtené bajty za sekundu|Instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|celkem|V/V – zapsané bajty za sekundu|Instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|celkem|V/V – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|celkem|V/V – operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|celkem|V/V – operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|celkem|V/V – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Průměr|Požadavky ve frontě aplikace|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|celkem|Uvolnění paměti 0. generace|Instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|celkem|Uvolnění paměti 1. generace|Instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|celkem|Uvolnění paměti 2. generace|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|celkem|Čas procesoru|Instance|
|Požadavky|Požadavky|Count|celkem|Požadavky|Instance|
|BytesReceived|Vstupní data|B|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|B|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Count|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Count|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Count|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Count|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Count|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Count|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Count|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|Count|celkem|Jednotky provádění funkcí|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|celkem|Počet spuštění funkce|Instance|
|AppConnections|Připojení|Count|Průměr|Připojení|Instance|
|Popisovače|Počet popisovačů|Count|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Count|Průměr|Počet vláken|Instance|
|PrivateBytes|Privátní bajty|B|Průměr|Privátní bajty|Instance|
|IoReadBytesPerSecond|V/V – přečtené bajty za sekundu|BytesPerSecond|celkem|V/V – přečtené bajty za sekundu|Instance|
|IoWriteBytesPerSecond|V/V – zapsané bajty za sekundu|BytesPerSecond|celkem|V/V – zapsané bajty za sekundu|Instance|
|IoOtherBytesPerSecond|V/V – ostatní bajty za sekundu|BytesPerSecond|celkem|V/V – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|V/V – operace čtení za sekundu|BytesPerSecond|celkem|V/V – operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|V/V – operace zápisu za sekundu|BytesPerSecond|celkem|V/V – operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/V – ostatní operace za sekundu|BytesPerSecond|celkem|V/V – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Průměr|Požadavky ve frontě aplikace|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Uvolnění paměti 0. generace|Count|celkem|Uvolnění paměti 0. generace|Instance|
|Gen1Collections|Uvolnění paměti 1. generace|Count|celkem|Uvolnění paměti 1. generace|Instance|
|Gen2Collections|Uvolnění paměti 2. generace|Count|celkem|Uvolnění paměti 2. generace|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Count|celkem|Požadavky|Instance|
|BytesReceived|Vstupní data|B|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|B|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Count|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Count|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Count|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Count|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Count|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Count|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Count|celkem|Chyby serveru HTTP|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|CpuPercentage|Procento CPU|Percent|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Percent|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Count|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty HTTP|Count|Průměr|Délka fronty HTTP|Instance|
|ActiveRequests|Aktivní požadavky|Count|celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet front-endů|Count|Průměr|Celkový počet front-endů|Žádné dimenze|
|SmallAppServicePlanInstances|Malé pracovní procesy plánu služby App Service|Count|Průměr|Malé pracovní procesy plánu služby App Service|Žádné dimenze|
|MediumAppServicePlanInstances|Střední pracovní procesy plánu služby App Service|Count|Průměr|Střední pracovní procesy plánu služby App Service|Žádné dimenze|
|LargeAppServicePlanInstances|Velké pracovní procesy plánu služby App Service|Count|Průměr|Velké pracovní procesy plánu služby App Service|Žádné dimenze|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Hodnoty WorkersTotal|Celkový počet pracovních procesů|Count|Průměr|Celkový počet pracovních procesů|Žádné dimenze|
|WorkersAvailable|Dostupné pracovní procesy|Count|Průměr|Dostupné pracovní procesy|Žádné dimenze|
|Workersused za|Využité pracovní procesy|Count|Průměr|Využité pracovní procesy|Žádné dimenze|
|CpuPercentage|Procento CPU|Percent|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Percent|Průměr|Procento paměti|Instance|

## <a name="next-steps"></a>Další postup
* [Přečtěte si informace o metriky ve službě Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Export metrik úložiště, Centrum událostí a Log Analytics](diagnostic-logs-overview.md)
