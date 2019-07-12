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
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705175"
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
|qpu_metric|QPU|Count|Average|QPU. Rozsah 0 až 100 pro S1, 0 až 200 pro S2 a 0 až 400 pro S4|ServerResourceType|
|memory_metric|Memory (Paměť)|B|Average|Paměť. V rozsahu 0-25 GB pro S1, 0 – 50 GB pro S2 a 0 – 100 GB pro S4|ServerResourceType|
|TotalConnectionRequests|Celkem žádostí o spojení|Count|Average|Celkem žádostí o spojení. Jedná se o doručení.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná spojení za sekundu|CountPerSecond|Average|Míra úspěšně navázaných spojení.|ServerResourceType|
|TotalConnectionFailures|Celkem nezdařených spojení|Count|Average|Celkový počet neúspěšných pokusů o připojení.|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Average|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty fondu úloh příkazu|Count|Average|Počet úloh ve frontě fondu vláken příkazů.|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty fondu úloh zpracování|Count|Average|Počet úloh bez vstupně-ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: Aktuální počet připojení|Count|Average|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: Aktuální cena čisticího modulu|Count|Average|Aktuální cena paměti a $/ bajt/čas, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: Zmenšitelná paměť čisticího modulu|B|Average|Velikost paměti v bajtech, která čisticí procesem na pozadí se vyprázdňuje.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: Nezmenšitelná paměť|B|Average|Velikost paměti v bajtech, která není v souladu s čisticí vyprazdňování procesem na pozadí.|ServerResourceType|
|MemoryUsage|Paměť: Využití paměti|B|Average|Využití paměti procesu serveru, jak se používají při výpočtu cena čisticího modulu paměti. Rovnat čítači Process\PrivateBytes plus velikost dat mapovaných do paměti, ignoruje se jakákoli paměť, která byla mapována nebo přidělena analýzu v paměti modulu xVelocity (VertiPaq) Limit paměti modulu xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: Limit paměti – pevná|B|Average|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitHigh|Paměť: Limit paměti – vysoká|B|Average|Limit vysoké paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: Limit paměti – nízká|B|Average|Limit nízké paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: Memory Limit VertiPaq|B|Average|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: Kvóta|B|Average|Aktuální kvóta paměti, v bajtech. Kvóta paměti se taky říká rezervace paměti grant nebo paměti.|ServerResourceType|
|QuotaBlocked|Paměť: Kvóta – blokováno|Count|Average|Aktuální počet požadavků kvóty, které jsou blokovány, dokud jsou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq Nonpaged|B|Average|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq Paged|B|Average|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Average|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: Řádky převést za sekundu|CountPerSecond|Average|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: Počet zapsaných řádků za sekundu|CountPerSecond|Average|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vlákna: Příkaz zaneprázdněná vlákna fondu|Count|Average|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Vlákna: Příkaz nečinná vlákna fondu|Count|Average|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyThreads|Vlákna: Zaneprázdněná vlákna dlouhého parsování|Count|Average|Počet zaneprázdněných vláken ve fondu vláken dlouhého parsování.|ServerResourceType|
|LongParsingIdleThreads|Vlákna: Nečinná vlákna dlouhého parsování|Count|Average|Počet nečinných vláken ve fondu vláken dlouhého parsování.|ServerResourceType|
|LongParsingJobQueueLength|Vlákna: Dlouhého parsování délka fronty úloh|Count|Average|Počet úloh ve frontě fondu vláken dlouhého parsování.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vlákna: Zaneprázdněná vlákna úloh vstupně-výstupní operace fondu zpracování|Count|Average|Počet vláken ve fondu vláken zpracování spuštění vstupně-výstupních operací úloh.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vlákna: Zaneprázdněná vlákna jiných vstupně-fondu zpracování|Count|Average|Počet vláken, spouštění úloh bez vstupně-ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vlákna: Délka fronty úloh vstupně-výstupní operace v fondu zpracování|Count|Average|Počet vstupně-výstupních operací úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vlákna: Nečinná vlákna úloh vstupně-výstupní operace fondu zpracování|Count|Average|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vlákna: Nečinná vlákna jiných vstupně-fondu zpracování|Count|Average|Počet nečinných vláken ve fondu vláken zpracování vyhrazeném pro úlohy bez vstupně.|ServerResourceType|
|QueryPoolIdleThreads|Vlákna: Nečinná vlákna fondu dotazů|Count|Average|Počet nečinných vláken pro vstupně-výstupní úlohy do fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Count|Average|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|ShortParsingBusyThreads|Vlákna: Zaneprázdněná vlákna krátkého parsování|Count|Average|Počet zaneprázdněných vláken ve fondu vláken krátkého parsování.|ServerResourceType|
|ShortParsingIdleThreads|Vlákna: Nečinná vlákna krátkého parsování|Count|Average|Počet nečinných vláken ve fondu vláken krátkého parsování.|ServerResourceType|
|ShortParsingJobQueueLength|Vlákna: Krátkého parsování délka fronty úloh|Count|Average|Počet úloh ve frontě fondu vláken krátkého parsování.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Percent|Average|Průměrný thrashing paměti.|ServerResourceType|
|mashup_engine_qpu_metric|QPU modulu M|Count|Average|Využití QPU procesy modulu mashupu|ServerResourceType|
|mashup_engine_memory_metric|Paměť modulu M|B|Average|Využití paměti procesy modulu mashupu|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Požadavky brány celkem|Count|Celkem|Počet požadavků brány|Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány|Count|Celkem|Počet úspěšné požadavky brány|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované požadavky brány|Count|Celkem|Počet Neautorizované požadavky brány|Umístění, název hostitele|
|FailedRequests|Požadavky na brány po nezdařeném nasazení|Count|Celkem|Počet selhání v požadavky brány|Umístění, název hostitele|
|OtherRequests|Ostatní požadavky brány|Count|Celkem|Počet ostatní požadavky brány|Umístění, název hostitele|
|Doba trvání|Celková doba trvání požadavků na bránu|Milisekundy|Average|Celková doba trvání z požadavky brány v milisekundách|Umístění, název hostitele|
|Kapacita|Kapacita|Percent|Average|Metriky využití pro službu ApiManagement|Location|
|EventHubTotalEvents|Celkový počet EventHub události|Count|Celkem|Počet událostí odeslaných do centra událostí|Location|
|EventHubSuccessfulEvents|Úspěšné EventHub události|Count|Celkem|Počet úspěšných událostí centra událostí|Location|
|EventHubTotalFailedEvents|Události centra událostí se nezdařilo|Count|Celkem|Počet neúspěšných událostí centra událostí|Location|
|EventHubRejectedEvents|Události zamítnutí EventHub|Count|Celkem|Počet odmítnutých událostí centra událostí (nesprávné konfigurace nebo neoprávněný přístup)|Location|
|EventHubThrottledEvents|Omezené EventHub události|Count|Celkem|Počet omezené události centra událostí|Location|
|EventHubTimedoutEvents|Vypršel časový limit události centra událostí|Count|Celkem|Počet vypršel časový limit události centra událostí|Location|
|EventHubDroppedEvents|Události vynechané EventHub|Count|Celkem|Počet událostí přeskočeno z důvodu dosáhlo limitu pro velikost fronty|Location|
|EventHubTotalBytesSent|Velikost události centra událostí|B|Celkem|Celková velikost událostí centra událostí v bajtech|Location|
|Požadavky|Požadavky|Count|Celkem|Požadavky brány|Umístění, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Count|Celkem|Celkový počet úloh|Sady Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet aktualizací je spuštěné nasazení|Count|Celkem|Nasazení aktualizace softwaru celkový počet spuštění|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Celkový počet aktualizací spuštění počítače nasazení|Count|Celkem|Celkový počet softwarových aktualizace nasazení počítač běží v nasazení aktualizací softwaru spuštění|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Počet vyhrazených jader|Count|Celkem|Celkový počet vyhrazených jader v účtu batch|Žádné dimenze|
|TotalNodeCount|Počet vyhrazených uzlů|Count|Celkem|Celkový počet vyhrazených uzlů v účtu batch|Žádné dimenze|
|LowPriorityCoreCount|Počet jader LowPriority|Count|Celkem|Celkový počet jader s nízkou prioritou na účet batch|Žádné dimenze|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Count|Celkem|Celkový počet uzlů s nízkou prioritou na účtu batch|Žádné dimenze|
|CreatingNodeCount|Vytváří se počet uzlů|Count|Celkem|Počet uzlů, které vytváří|Žádné dimenze|
|StartingNodeCount|Počáteční počet uzlů|Count|Celkem|Počet uzlů spuštění|Žádné dimenze|
|WaitingForStartTaskNodeCount|Čekání na pro počet uzlů spuštění úkolu|Count|Celkem|Počet uzlů čekání na dokončení úlohy spustit|Žádné dimenze|
|StartTaskFailedNodeCount|Spuštění úlohy se nezdařilo počet uzlů|Count|Celkem|Počet uzlů, ve kterém se nezdařilo spustit úkol|Žádné dimenze|
|IdleNodeCount|Počet nečinných uzlů|Count|Celkem|Počet nečinných uzlů|Žádné dimenze|
|OfflineNodeCount|Offline počet uzlů|Count|Celkem|Počet uzlů v režimu offline|Žádné dimenze|
|RebootingNodeCount|Restartování počet uzlů|Count|Celkem|Počtu restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|Obnovování z Image počet uzlů|Count|Celkem|Počet uzlů, obnovování z Image|Žádné dimenze|
|RunningNodeCount|Spuštění počet uzlů|Count|Celkem|Počet spuštěných uzly|Žádné dimenze|
|LeavingPoolNodeCount|Opuštění počet uzlů fondu|Count|Celkem|Počet uzlů, které byste museli opustit fondu|Žádné dimenze|
|UnusableNodeCount|Nepoužitelné počet uzlů|Count|Celkem|Počet uzlů nepoužitelné|Žádné dimenze|
|PreemptedNodeCount|Ke zrušení přidělením počet uzlů|Count|Celkem|Počet uzlů přerušeno|Žádné dimenze|
|TaskStartEvent|Události spuštění úloh|Count|Celkem|Celkový počet úloh, které byly spuštěny|Žádné dimenze|
|TaskCompleteEvent|Události dokončení úkolu|Count|Celkem|Celkový počet úloh, které byly dokončeny|Žádné dimenze|
|TaskFailEvent|Události selhání úloh|Count|Celkem|Celkový počet úloh, které byly dokončeny v neúspěšném stavu|Žádné dimenze|
|PoolCreateEvent|Vytvoření fondu události|Count|Celkem|Celkový počet fondů, které byly vytvořeny|Žádné dimenze|
|PoolResizeStartEvent|Události zahájení změny velikosti fondu|Count|Celkem|Celkový počet změny velikosti fondu, které byly spuštěny|Žádné dimenze|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Count|Celkem|Celkový počet změny velikosti fondu, které byly dokončeny|Žádné dimenze|
|PoolDeleteStartEvent|Události zahájení odstranění fondu|Count|Celkem|Celkový počet odstranění fondu, které byly spuštěny|Žádné dimenze|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Count|Celkem|Celkový počet odstranění fondu, které byly dokončeny|Žádné dimenze|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Count|Celkem|Celkový počet úloh, které byly úspěšně odstraněny.|Žádné dimenze|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Count|Celkem|Celkový počet úloh, které bylo vyžádáno odstranit.|Žádné dimenze|
|JobDisableCompleteEvent|Události dokončení úlohy zakázat|Count|Celkem|Celkový počet úloh, které bylo úspěšně zakázáno.|Žádné dimenze|
|JobDisableStartEvent|Úlohy zakázat zahájení události|Count|Celkem|Celkový počet úloh, které bylo vyžádáno deaktivuje.|Žádné dimenze|
|JobStartEvent|Události spuštění úlohy|Count|Celkem|Celkový počet úloh, které byla úspěšně spuštěna.|Žádné dimenze|
|JobTerminateCompleteEvent|Úloha ukončení dokončení události|Count|Celkem|Celkový počet úloh, které se úspěšně ukončily.|Žádné dimenze|
|JobTerminateStartEvent|Úloha ukončení zahájení události|Count|Celkem|Celkový počet úloh, které bylo vyžádáno ukončení.|Žádné dimenze|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Připojení klienti|Count|Maximum||ShardId|
|totalcommandsprocessed|Celkový počet operací|Count|Celkem||ShardId|
|mezipaměti|Přístupy do mezipaměti|Count|Celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Count|Celkem||ShardId|
|getcommands|Získá|Count|Celkem||ShardId|
|setcommands|Sad|Count|Celkem||ShardId|
|operationsPerSecond|Operací za sekundu|Count|Maximum||ShardId|
|evictedkeys|Vyloučené klíče|Count|Celkem||ShardId|
|totalkeys|Celkový počet klíčů|Count|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Count|Celkem||ShardId|
|usedmemory|Použitá paměť|B|Maximum||ShardId|
|usedmemorypercentage|Procento využité paměti|Percent|Maximum||ShardId|
|usedmemoryRss|Využitá paměť RSS|B|Maximum||ShardId|
|serverLoad|Zatížení serveru|Percent|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Percent|Maximum||ShardId|
|cacheLatency|Mezipaměť latence mikrosekundy (Preview)|Count|Average||ShardId SampleType|
|chyby|Chyby|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Připojených klientů (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed0|Celkový počet operací (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|getcommands0|Získá (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|setcommands0|Nastaví (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|evictedkeys0|Vyloučené klíče (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|totalkeys0|Celkový počet klíčů (horizontálních oddílů 0)|Count|Maximum||Žádné dimenze|
|expiredkeys0|Prošlé klíče (horizontálních oddílů 0)|Count|Celkem||Žádné dimenze|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|usedmemoryRss0|Využitá paměť RSS (horizontálních oddílů 0)|B|Maximum||Žádné dimenze|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime0|Procesor (horizontálních oddílů 0)|Percent|Maximum||Žádné dimenze|
|connectedclients1|Připojených klientů (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed1|Celkový počet operací (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|getcommands1|Získá (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|setcommands1|Nastaví (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|evictedkeys1|Vyloučené klíče (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|totalkeys1|Celkový počet klíčů (horizontálních oddílů 1)|Count|Maximum||Žádné dimenze|
|expiredkeys1|Prošlé klíče (horizontálních oddílů 1)|Count|Celkem||Žádné dimenze|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|usedmemoryRss1|Využitá paměť RSS (horizontálních oddílů 1)|B|Maximum||Žádné dimenze|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime1|Procesor (horizontálních oddílů 1)|Percent|Maximum||Žádné dimenze|
|connectedclients2|Připojených klientů (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed2|Celkový počet operací (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|getcommands2|Získá (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|setcommands2|Nastaví (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|evictedkeys2|Vyloučené klíče (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|totalkeys2|Celkový počet klíčů (horizontálních oddílů 2)|Count|Maximum||Žádné dimenze|
|expiredkeys2|Prošlé klíče (horizontálních oddílů 2)|Count|Celkem||Žádné dimenze|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|usedmemoryRss2|Využitá paměť RSS (horizontálních oddílů 2)|B|Maximum||Žádné dimenze|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime2|Procesor (horizontálních oddílů 2)|Percent|Maximum||Žádné dimenze|
|connectedclients3|Připojených klientů (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed3|Celkový počet operací (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|getcommands3|Získá (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|setcommands3|Nastaví (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|evictedkeys3|Vyloučené klíče (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|totalkeys3|Celkový počet klíčů (horizontálních oddílů 3)|Count|Maximum||Žádné dimenze|
|expiredkeys3|Prošlé klíče (horizontálních oddílů 3)|Count|Celkem||Žádné dimenze|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|usedmemoryRss3|Využitá paměť RSS (horizontálních oddílů 3)|B|Maximum||Žádné dimenze|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime3|Procesor (horizontálních oddílů 3)|Percent|Maximum||Žádné dimenze|
|connectedclients4|Připojených klientů (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed4|Celkový počet operací (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|getcommands4|Získá (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|setcommands4|Nastaví (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|evictedkeys4|Vyloučené klíče (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|totalkeys4|Celkový počet klíčů (horizontálních oddílů 4)|Count|Maximum||Žádné dimenze|
|expiredkeys4|Prošlé klíče (horizontálních oddílů 4)|Count|Celkem||Žádné dimenze|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|usedmemoryRss4|Využitá paměť RSS (horizontálních oddílů 4)|B|Maximum||Žádné dimenze|
|serverLoad4|Server Load (Shard 4)|Percent|Maximum||Žádné dimenze|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime4|Procesor (horizontálních oddílů 4)|Percent|Maximum||Žádné dimenze|
|connectedclients5|Připojených klientů (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed5|Celkový počet operací (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|cachehits5|Přístupy do mezipaměti (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|getcommands5|Získá (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|setcommands5|Nastaví (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|operationsPerSecond5|Operací za sekundu (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|evictedkeys5|Vyloučené klíče (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|totalkeys5|Celkový počet klíčů (horizontální oddíl 5)|Count|Maximum||Žádné dimenze|
|expiredkeys5|Prošlé klíče (horizontální oddíl 5)|Count|Celkem||Žádné dimenze|
|usedmemory5|Využitá paměť (horizontální oddíl 5)|B|Maximum||Žádné dimenze|
|usedmemoryRss5|Využitá paměť RSS (horizontální oddíl 5)|B|Maximum||Žádné dimenze|
|serverLoad5|Zatížení serveru (horizontální oddíl 5)|Percent|Maximum||Žádné dimenze|
|cacheWrite5|Zápis do mezipaměti (horizontální oddíl 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead5|Čtení z mezipaměti (horizontální oddíl 5)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime5|Procesor (horizontální oddíl 5)|Percent|Maximum||Žádné dimenze|
|connectedclients6|Připojených klientů (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed6|Celkový počet operací (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|cachehits6|Přístupy do mezipaměti (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|getcommands6|Získá (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|setcommands6|Nastaví (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|operationsPerSecond6|Operací za sekundu (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|evictedkeys6|Vyloučené klíče (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|totalkeys6|Celkový počet klíčů (horizontální oddíl 6)|Count|Maximum||Žádné dimenze|
|expiredkeys6|Prošlé klíče (horizontální oddíl 6)|Count|Celkem||Žádné dimenze|
|usedmemory6|Využitá paměť (horizontální oddíl 6)|B|Maximum||Žádné dimenze|
|usedmemoryRss6|Využitá paměť RSS (horizontální oddíl 6)|B|Maximum||Žádné dimenze|
|serverLoad6|Zatížení serveru (horizontální oddíl 6)|Percent|Maximum||Žádné dimenze|
|cacheWrite6|Zápis do mezipaměti (horizontální oddíl 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead6|Čtení z mezipaměti (horizontální oddíl 6)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime6|Procesor (horizontální oddíl 6)|Percent|Maximum||Žádné dimenze|
|connectedclients7|Připojených klientů (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed7|Celkový počet operací (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|cachehits7|Přístupy do mezipaměti (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|getcommands7|Získá (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|setcommands7|Nastaví (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|operationsPerSecond7|Operací za sekundu (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|evictedkeys7|Vyloučené klíče (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|totalkeys7|Celkový počet klíčů (horizontální oddíl 7)|Count|Maximum||Žádné dimenze|
|expiredkeys7|Prošlé klíče (horizontální oddíl 7)|Count|Celkem||Žádné dimenze|
|usedmemory7|Využitá paměť (horizontální oddíl 7)|B|Maximum||Žádné dimenze|
|usedmemoryRss7|Využitá paměť RSS (horizontální oddíl 7)|B|Maximum||Žádné dimenze|
|serverLoad7|Server Load (Shard 7)|Percent|Maximum||Žádné dimenze|
|cacheWrite7|Zápis do mezipaměti (horizontální oddíl 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead7|Čtení z mezipaměti (horizontální oddíl 7)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime7|Procesor (horizontální oddíl 7)|Percent|Maximum||Žádné dimenze|
|connectedclients8|Připojených klientů (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed8|Celkový počet operací (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|cachehits8|Přístupy do mezipaměti (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|getcommands8|Získá (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|setcommands8|Nastaví (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|operationsPerSecond8|Operací za sekundu (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|evictedkeys8|Vyloučené klíče (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|totalkeys8|Celkový počet klíčů (horizontální oddíl 8)|Count|Maximum||Žádné dimenze|
|expiredkeys8|Prošlé klíče (horizontální oddíl 8)|Count|Celkem||Žádné dimenze|
|usedmemory8|Využitá paměť (horizontální oddíl 8)|B|Maximum||Žádné dimenze|
|usedmemoryRss8|Využitá paměť RSS (horizontální oddíl 8)|B|Maximum||Žádné dimenze|
|serverLoad8|Zatížení serveru (horizontální oddíl 8)|Percent|Maximum||Žádné dimenze|
|cacheWrite8|Zápis do mezipaměti (horizontální oddíl 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead8|Čtení z mezipaměti (horizontální oddíl 8)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime8|Procesor (horizontální oddíl 8)|Percent|Maximum||Žádné dimenze|
|connectedclients9|Připojených klientů (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|totalcommandsprocessed9|Celkový počet operací (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|cachehits9|Přístupy do mezipaměti (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|getcommands9|Získá (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|setcommands9|Nastaví (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|operationsPerSecond9|Operací za sekundu (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|evictedkeys9|Vyloučené klíče (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|totalkeys9|Celkový počet klíčů (horizontální oddíl 9)|Count|Maximum||Žádné dimenze|
|expiredkeys9|Prošlé klíče (horizontální oddíl 9)|Count|Celkem||Žádné dimenze|
|usedmemory9|Využitá paměť (horizontální oddíl 9)|B|Maximum||Žádné dimenze|
|usedmemoryRss9|Využitá paměť RSS (horizontální oddíl 9)|B|Maximum||Žádné dimenze|
|serverLoad9|Zatížení serveru (horizontální oddíl 9)|Percent|Maximum||Žádné dimenze|
|cacheWrite9|Zápis do mezipaměti (horizontální oddíl 9)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead9|Čtení z mezipaměti (horizontální oddíl 9)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime9|Procesor (horizontální oddíl 9)|Percent|Maximum||Žádné dimenze|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Average|Procento přidělených výpočetních jednotek, které jsou právě používány virtuálními počítači.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|Celkem|Počet bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz).|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|Celkem|Počet bajtů odchozích všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádné dimenze|
|Čtení disku bajtů/s|Čtení z disku|BytesPerSecond|Average|Průměrný počet bajtů přečtený z disku během období monitorování.|Žádné dimenze|
|Zápis disku bajtů/s|Zápis na disk|BytesPerSecond|Average|Průměrný počet bajtů zapsaný na disk během období monitorování.|Žádné dimenze|
|Čtení z disku operace/s|Čtení z disku operace/s|CountPerSecond|Average|Čtení z disku – IOPS.|Žádné dimenze|
|Operace zápisu disku/s|Operace zápisu disku/s|CountPerSecond|Average|Zápis na disk vstupně-výstupních operací.|Žádné dimenze|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Average|Procento přidělených výpočetních jednotek, které jsou právě používány virtuálními počítači.|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|B|Celkem|Počet bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|B|Celkem|Počet bajtů odchozích všech síťových rozhraní virtuálních počítačů (odchozí provoz).|RoleInstanceId|
|Čtení disku bajtů/s|Čtení z disku|BytesPerSecond|Average|Průměrný počet bajtů přečtený z disku během období monitorování.|RoleInstanceId|
|Zápis disku bajtů/s|Zápis na disk|BytesPerSecond|Average|Průměrný počet bajtů zapsaný na disk během období monitorování.|RoleInstanceId|
|Čtení z disku operace/s|Čtení z disku operace/s|CountPerSecond|Average|Čtení z disku – IOPS.|RoleInstanceId|
|Operace zápisu disku/s|Operace zápisu disku/s|CountPerSecond|Average|Zápis na disk vstupně-výstupních operací.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkový počet volání|Count|Celkem|Celkový počet volání.|ApiName OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Count|Celkem|Počet úspěšných volání.|ApiName OperationName, oblast|
|TotalErrors|Celkový počet chyb|Count|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName OperationName, oblast|
|BlockedCalls|Zablokovaná volání|Count|Celkem|Počet volání, míra došlo k překročení maximální kvótu.|ApiName OperationName, oblast|
|ServerErrors|Chyby serveru|Count|Celkem|Počet volání s vnitřní chybou služby (kód odpovědi HTTP 5xx).|ApiName OperationName, oblast|
|ClientErrors|Chyby klienta|Count|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName OperationName, oblast|
|DataIn|Data v|B|Celkem|Velikost příchozích dat v bajtech.|ApiName OperationName, oblast|
|DataOut|Výstupní data|B|Celkem|Velikost odchozích dat v bajtech.|ApiName OperationName, oblast|
|Latence|Latence|Milisekundy|Average|Latence v milisekundách.|ApiName OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Count|Celkem|Celkový počet znaků v příchozí textové žádosti.|ApiName OperationName, oblast|
|CharactersTrained|Znaky školení|Count|Celkem|Celkový počet znaků školení.|ApiName OperationName, oblast|
|SpeechSessionDuration|Rozpoznávání řeči doba trvání relace|Sekundy|Celkem|Celková doba trvání Řečové relace v sekundách.|ApiName OperationName, oblast|
|TotalTransactions|Transakce celkem|Count|Celkem|Celkový počet transakcí.|Žádné dimenze|
|TotalTokenCalls|Token volání celkem|Count|Celkem|Celkový počet volání tokenu.|ApiName OperationName, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síť v fakturovatelné|B|Celkem|Počet účtovaných bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Fakturovatelné mimo síť|B|Celkem|Počet účtovaných bajtů na všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Bajty čtení disku|Bajty čtení disku|B|Celkem|Počet bajtů přečtený z disku během období monitorování|Žádné dimenze|
|Bajty zápisu disku|Bajty zápisu disku|B|Celkem|Počet bajtů zapsaný na disk během období monitorování|Žádné dimenze|
|Čtení z disku operace/s|Čtení z disku operace/s|CountPerSecond|Average|Čtení z disku – IOPS|Žádné dimenze|
|Operace zápisu disku/s|Operace zápisu disku/s|CountPerSecond|Average|Zápis IOPS na disk|Žádné dimenze|
|Zbývající kredity CPU|Zbývající kredity CPU|Count|Average|Celkový počet kreditu, které můžete přechodem|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Count|Average|Celkový počet úvěry využívat virtuální počítač|Žádné dimenze|
|Na čtení disku bajtů/s|Přečtené bajty/s (zastaralé) z datového disku|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Za zápis disku bajtů/s|Data zápisu disku bajtů/sekundu (zastaralé)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Čtení operací za sekundu podle disku|Datový Disk operací čtení/s (zastaralé)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování|SlotId|
|Za operace zápisu disku/s|Datový Disk zápisu operace za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|HF podle disku|(Nepoužívané) hf datového disku|Count|Average|Hloubka fronty disku data (nebo délka fronty)|SlotId|
|V operačním systému podle disku přečtené bajty/s|Disk s operačním systémem přečtené bajty/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku zapsat bajty/s|Disk s operačním systémem zapsané bajty/s (zastaralé)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle operace čtení disku/s|Disk s operačním systémem operací čtení/s (zastaralé)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku zápisu operace/s|Disk s operačním systémem zápisu operace/s (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku hloubka fronty|Disk s operačním systémem hloubka fronty (zastaralé)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Přečtené bajty/s z datového disku|Datový Disk přečtené bajty/s (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Datový Disk zápis bajtů za sekundu|Datový Disk zápis bajtů za sekundu (Preview)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování|LOGICKÉ JEDNOTKY|
|Čtení operací za sekundu z datového disku|Datový Disk operací čtení/s (Preview)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Datový Disk zápisu operace za sekundu|Datový Disk zápisu operace za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|LOGICKÉ JEDNOTKY|
|Hloubka fronty datového disku|Hloubka fronty datového disku (Preview)|Count|Average|Hloubka fronty disku data (nebo délka fronty)|LOGICKÉ JEDNOTKY|
|Disk s operačním systémem přečtené bajty/s|Disk s operačním systémem přečtené bajty/s (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Disk s operačním systémem zapsané bajty/s|Disk s operačním systémem zapsané bajty/s (Preview)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Disk s operačním systémem operací čtení/s|Disk s operačním systémem operací čtení/s (Preview)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Disk s operačním systémem zápisu operace/s|Disk s operačním systémem zápisu operace/s (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku s operačním systémem (ve verzi Preview)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Příchozí toků|Příchozí toků (Preview)|Count|Average|Příchozí toky se číslo aktuální toků ve směru příchozí (provoz přicházející do virtuálního počítače)|Žádné dimenze|
|Odchozí toky|Odchozích toků (Preview)|Count|Average|Odchozí toky se číslo aktuální toků ve směru odchozí (přenosy z virtuálního počítače)|Žádné dimenze|
|Míra příchozích toků maximální vytváření|Příchozí míra maximální vytváření toků (Preview)|CountPerSecond|Average|Vytvoření maximální počet příchozích toků (provoz přicházející do virtuálního počítače)|Žádné dimenze|
|Kurz vytváření maximální odchozí toky|Míra maximální vytváření odchozích toků (Preview)|CountPerSecond|Average|Vytvoření maximální počet odchozích toků (přenosy z virtuálního počítače)|Žádné dimenze|
|Přístup do mezipaměti pro čtení úrovně Premium datového disku|Premium datový Disk ke čtení počet vstupů do mezipaměti (Preview)|Percent|Average|Přístup do mezipaměti pro čtení úrovně Premium datového disku|LOGICKÉ JEDNOTKY|
|Premium disku čtení neúspěšných přístupů do mezipaměti|Premium disku čtení neúspěšných přístupů do mezipaměti (Preview)|Percent|Average|Premium disku čtení neúspěšných přístupů do mezipaměti|LOGICKÉ JEDNOTKY|
|Stiskněte čtení z mezipaměti disku s operačním systémem Premium|Premium OS Disk Cache Read Hit (Preview)|Percent|Average|Stiskněte čtení z mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti (Preview)|Percent|Average|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti|Žádné dimenze|
|Síť celkem|Síť celkem|B|Celkem|Počet bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz)|Žádné dimenze|
|Síť celkem|Síť celkem|B|Celkem|Počet bajtů odchozích všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|VMName|
|Síťové vstupy|Síť v fakturovatelné|B|Celkem|Počet účtovaných bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz)|VMName|
|Síťové výstupy|Fakturovatelné mimo síť|B|Celkem|Počet účtovaných bajtů na všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|
|Bajty čtení disku|Bajty čtení disku|B|Celkem|Počet bajtů přečtený z disku během období monitorování|VMName|
|Bajty zápisu disku|Bajty zápisu disku|B|Celkem|Počet bajtů zapsaný na disk během období monitorování|VMName|
|Čtení z disku operace/s|Čtení z disku operace/s|CountPerSecond|Average|Čtení z disku – IOPS|VMName|
|Operace zápisu disku/s|Operace zápisu disku/s|CountPerSecond|Average|Zápis IOPS na disk|VMName|
|Zbývající kredity CPU|Zbývající kredity CPU|Count|Average|Celkový počet kreditu, které můžete přechodem|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Count|Average|Celkový počet úvěry využívat virtuální počítač|Žádné dimenze|
|Na čtení disku bajtů/s|Přečtené bajty/s (zastaralé) z datového disku|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Za zápis disku bajtů/s|Data zápisu disku bajtů/sekundu (zastaralé)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Čtení operací za sekundu podle disku|Datový Disk operací čtení/s (zastaralé)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování|SlotId|
|Za operace zápisu disku/s|Datový Disk zápisu operace za sekundu (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|HF podle disku|(Nepoužívané) hf datového disku|Count|Average|Hloubka fronty disku data (nebo délka fronty)|SlotId|
|V operačním systému podle disku přečtené bajty/s|Disk s operačním systémem přečtené bajty/s (zastaralé)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku zapsat bajty/s|Disk s operačním systémem zapsané bajty/s (zastaralé)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle operace čtení disku/s|Disk s operačním systémem operací čtení/s (zastaralé)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku zápisu operace/s|Disk s operačním systémem zápisu operace/s (zastaralé)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|V operačním systému podle disku hloubka fronty|Disk s operačním systémem hloubka fronty (zastaralé)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Přečtené bajty/s z datového disku|Datový Disk přečtené bajty/s (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|Logická jednotka, VMName|
|Datový Disk zápis bajtů za sekundu|Datový Disk zápis bajtů za sekundu (Preview)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování|Logická jednotka, VMName|
|Čtení operací za sekundu z datového disku|Datový Disk operací čtení/s (Preview)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování|Logická jednotka, VMName|
|Datový Disk zápisu operace za sekundu|Datový Disk zápisu operace za sekundu (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování|Logická jednotka, VMName|
|Hloubka fronty datového disku|Hloubka fronty datového disku (Preview)|Count|Average|Hloubka fronty disku data (nebo délka fronty)|Logická jednotka, VMName|
|Disk s operačním systémem přečtené bajty/s|Disk s operačním systémem přečtené bajty/s (Preview)|CountPerSecond|Average|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Disk s operačním systémem zapsané bajty/s|Disk s operačním systémem zapsané bajty/s (Preview)|CountPerSecond|Average|Bajty za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Disk s operačním systémem operací čtení/s|Disk s operačním systémem operací čtení/s (Preview)|CountPerSecond|Average|Vstupně-výstupních operací čtení z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Disk s operačním systémem zápisu operace/s|Disk s operačním systémem zápisu operace/s (Preview)|CountPerSecond|Average|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku s operačním systémem (ve verzi Preview)|Count|Average|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toků|Příchozí toků (Preview)|Count|Average|Příchozí toky se číslo aktuální toků ve směru příchozí (provoz přicházející do virtuálního počítače)|VMName|
|Odchozí toky|Odchozích toků (Preview)|Count|Average|Odchozí toky se číslo aktuální toků ve směru odchozí (přenosy z virtuálního počítače)|VMName|
|Míra příchozích toků maximální vytváření|Příchozí míra maximální vytváření toků (Preview)|CountPerSecond|Average|Vytvoření maximální počet příchozích toků (provoz přicházející do virtuálního počítače)|VMName|
|Kurz vytváření maximální odchozí toky|Míra maximální vytváření odchozích toků (Preview)|CountPerSecond|Average|Vytvoření maximální počet odchozích toků (přenosy z virtuálního počítače)|VMName|
|Přístup do mezipaměti pro čtení úrovně Premium datového disku|Premium datový Disk ke čtení počet vstupů do mezipaměti (Preview)|Percent|Average|Přístup do mezipaměti pro čtení úrovně Premium datového disku|Logická jednotka, VMName|
|Premium disku čtení neúspěšných přístupů do mezipaměti|Premium disku čtení neúspěšných přístupů do mezipaměti (Preview)|Percent|Average|Premium disku čtení neúspěšných přístupů do mezipaměti|Logická jednotka, VMName|
|Stiskněte čtení z mezipaměti disku s operačním systémem Premium|Premium OS Disk Cache Read Hit (Preview)|Percent|Average|Stiskněte čtení z mezipaměti disku s operačním systémem Premium|VMName|
|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti (Preview)|Percent|Average|Premium operačního systému disku čtení neúspěšnému přístupu do mezipaměti|VMName|
|Síť celkem|Síť celkem|B|Celkem|Počet bajtů přijatých na všech síťových rozhraní virtuálních počítačů (příchozí provoz)|VMName|
|Síť celkem|Síť celkem|B|Celkem|Počet bajtů odchozích všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití procesoru|Count|Average|Využití procesoru u všech jader v jednotkách millicore|containerName|
|MemoryUsage|Využití paměti|B|Average|Celkové využití paměti v bajtech.|containerName|
|NetworkBytesReceivedPerSecond|Bajtů přijatých za sekundu ze sítě|B|Average|Síť počet přijatých bajtů za sekundu.|Žádné dimenze|
|NetworkBytesTransmittedPerSecond|Síťové bajty za sekundu|B|Average|Síťové bajty za sekundu.|Žádné dimenze|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Počet celkový o přijetí změn|Count|Average|Počet obrázků si vyžádá celkem|Žádné dimenze|
|SuccessfulPullCount|Počet úspěšných o přijetí změn|Count|Average|Počet úspěšných image si|Žádné dimenze|
|TotalPushCount|Počet celkový počet nabízených oznámení|Count|Average|Počet obrázků nabízených oznámení v celkem|Žádné dimenze|
|SuccessfulPushCount|Počet úspěšných nabízených oznámení|Count|Average|Počet úspěšně image nabízených oznámení|Žádné dimenze|
|RunDuration|Doba trvání běhu|Milisekundy|Celkem|Doba trvání v milisekundách|Žádné dimenze|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru v spravovaný cluster|Count|Celkem|Celkový počet dostupných jader procesoru v spravovaný cluster|Žádné dimenze|
|kube_node_status_allocatable_memory_bytes|Celkové množství dostupné paměti v spravovaný cluster|B|Celkem|Celkové množství dostupné paměti v spravovaný cluster|Žádné dimenze|
|kube_pod_status_ready|Počet podů ve stavu Připraveno|Count|Celkem|Počet podů ve stavu Připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Count|Celkem|Stavy pro různé podmínky uzlu|Stav, stav, status2, uzlu|
|kube_pod_status_phase|Počet podů podle fáze|Count|Celkem|Počet podů podle fáze|fáze, obor názvů, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Volání Insights API zákazníka|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationSuccessfulLines|Mapování Import operace úspěšné řádky|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationFailedLines|Mapování operace importu se nezdařila řádky|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationTotalLines|Mapování Import operace celkový počet řádků|Count|Celkem||Žádné dimenze|
|DCIMappingImportOperationRuntimeInSeconds|Mapování modulu Runtime operace importu v řádu sekund|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundProfileExportSucceeded|Exportovat odchozí profilu proběhlo úspěšně|Count|Celkem||Žádné dimenze|
|DCIOutboundProfileExportFailed|Exportovat odchozí profilu se nezdařila|Count|Celkem||Žádné dimenze|
|DCIOutboundProfileExportDuration|Doba trvání exportu odchozí profilu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiExportSucceeded|Odchozí klíčového ukazatele výkonu Export byl úspěšný|Count|Celkem||Žádné dimenze|
|DCIOutboundKpiExportFailed|Exportovat odchozí klíčového ukazatele výkonu se nezdařilo|Count|Celkem||Žádné dimenze|
|DCIOutboundKpiExportDuration|Doba trvání odchozí Export klíčového ukazatele výkonu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiExportStarted|Spuštěn Export odchozí klíčového ukazatele výkonu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundProfileExportCount|Počet odchozích profilu exportu|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportFailed|Počáteční odchozí profilu exportu se nezdařila|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportSucceeded|Počáteční odchozí profilu exportu byla úspěšná|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportFailed|Odchozí počáteční klíčového ukazatele výkonu Export se nezdařil|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportSucceeded|Odchozí počáteční klíčového ukazatele výkonu Export byl úspěšný|Sekundy|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportDurationInSeconds|Doba trvání exportu odchozí počáteční profil v sekundách|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiFailed|Úloha Adla pro standardní klíčového ukazatele výkonu se nezdařilo v řádu sekund|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiTimeOut|Úloha Adla pro standardní klíčového ukazatele výkonu časový limit v sekundách|Sekundy|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiCompleted|Úloha Adla Standard klíčových ukazatelů výkonu v sekundách|Sekundy|Celkem||Žádné dimenze|
|ImportASAValuesFailed|Počet neúspěšných hodnoty import Azure Stream Analytics|Count|Celkem||Žádné dimenze|
|ImportASAValuesSucceeded|Počet úspěšně import ASA hodnoty|Count|Celkem||Žádné dimenze|
|DCIProfilesCount|Počet instancí profilu|Count|Poslední||Žádné dimenze|
|DCIInteractionsPerMonthCount|Interakcí za měsíc počet|Count|Poslední||Žádné dimenze|
|DCIKpisCount|Počet klíčových ukazatelů výkonu|Count|Poslední||Žádné dimenze|
|DCISegmentsCount|Počet segmentů|Count|Poslední||Žádné dimenze|
|DCIPredictiveMatchPoliciesCount|Prediktivní počet shod|Count|Poslední||Žádné dimenze|
|DCIPredictionsCount|Počet predikcí|Count|Poslední||Žádné dimenze|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost pro čtení (síť)|BytesPerSecond|Average|Propustnost čtení síťového rozhraní na zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Zápis propustnost (síť)|BytesPerSecond|Average|Zápis propustnost síťového rozhraní na zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování cloudu (sdílená složka)|BytesPerSecond|Average|Propustnost stažení do Azure ze sdílené složky období sestavy.|Sdílení|
|CloudUploadThroughputPerShare|Propustnost odesílání cloudu (sdílená složka)|BytesPerSecond|Average|Propustnost odesílání do Azure ze sdílené složky období sestavy.|Sdílení|
|BytesUploadedToCloudPerShare|Nahrání bajtů cloudu (sdílená složka)|B|Average|Celkový počet bajtů, který se nahraje do Azure ze sdílené složky období sestavy.|Sdílení|
|Celková kapacita|Celková kapacita|B|Average|Celková kapacita|Žádné dimenze|
|AvailableCapacity|Dostupná kapacita|B|Average|Dostupná kapacita v bajtech období sestavy.|Žádné dimenze|
|CloudUploadThroughput|Propustnost odesílání cloudu|BytesPerSecond|Average|Cloud propustnost odesílání do Azure během období vytváření sestav.|Žádné dimenze|
|CloudReadThroughput|Propustnost stahování cloudu|BytesPerSecond|Average|Cloud stahování propustnosti prostředí do Azure během období vytváření sestav.|Žádné dimenze|
|BytesUploadedToCloud|Nahrání bajtů cloudu (zařízení)|B|Average|Celkový počet bajtů, který se nahraje do Azure ze zařízení během období vytváření sestav.|Žádné dimenze|
|HyperVVirtualProcessorUtilization|Edge výpočty – procento CPU|Percent|Average|Procento využití procesoru|InstanceName|
|HyperVMemoryUtilization|Okraj výpočetních služeb – využití paměti|Percent|Average|Množství paměti RAM v použití|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Count|Celkem||pipelineName, activityName|
|SuccessfulRuns|Úspěšná spuštění|Count|Celkem||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Se nezdařilo metrika spuštění kanálu|Count|Celkem||FailureType, název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Count|Celkem||FailureType, název|
|ActivityFailedRuns|Metriky aktivity spuštění se nezdařilo|Count|Celkem||ActivityType, PipelineName, FailureType, název|
|ActivitySucceededRuns|Úspěšné běhy metriky aktivity|Count|Celkem||ActivityType, PipelineName, FailureType, název|
|TriggerFailedRuns|Se nezdařilo metrika spuštění aktivační události|Count|Celkem||Název, FailureType|
|TriggerSucceededRuns|Aktivační událost metriky spuštění bylo úspěšné|Count|Celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití Integration runtime – procesor|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime – paměť k dispozici|B|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Entity maximální povolený počet|Count|Maximum||Žádné dimenze|
|MaxAllowedFactorySizeInGbUnits|Maximální povolený továrny velikost (GB unit)|Count|Maximum||Žádné dimenze|
|ResourceCount|Počet celkový počet entit|Count|Maximum||Žádné dimenze|
|FactorySizeInGbUnits|Celkový počet factory velikost (GB unit)|Count|Maximum||Žádné dimenze|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Count|Celkem|Počet úspěšných úloh.|Žádné dimenze|
|JobEndedFailure|Neúspěšné úlohy|Count|Celkem|Počet nezdařených úloh.|Žádné dimenze|
|JobEndedCancelled|Zrušené úlohy|Count|Celkem|Počet zrušených úloh.|Žádné dimenze|
|JobAUEndedSuccess|Úspěšné čas AU|Sekundy|Celkem|Celkový čas AU u úspěšných úloh.|Žádné dimenze|
|JobAUEndedFailure|Čas selhání AU|Sekundy|Celkem|Celkový čas AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedCancelled|Zrušené čas AU|Sekundy|Celkem|Celkový čas AU pro zrušené úlohy.|Žádné dimenze|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Úložiště celkem|B|Maximum|Celkové množství dat uložených v účtu.|Žádné dimenze|
|DataWritten|Zapsaná data|B|Celkem|Celkové množství dat zapsaných do účtu.|Žádné dimenze|
|DataRead|Přečtená data|B|Celkem|Celkové množství dat číst z účtu.|Žádné dimenze|
|WriteRequests|Požadavky na zápis|Count|Celkem|Počet dat požadavky na zápis k účtu.|Žádné dimenze|
|ReadRequests|Žádosti o čtení|Count|Celkem|Počet dat, přečtěte si požadavky na účet.|Žádné dimenze|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Average|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Average|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Average|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Average|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Average|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Average|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Zpoždění replikace v řádu sekund|Count|Average|Zpoždění replikace v řádu sekund|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Average|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Average|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Average|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Average|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Average|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Average|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Zpoždění replikace v řádu sekund|Count|Average|Zpoždění replikace v řádu sekund|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Average|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|V/v úrovně procent|Percent|Average|V/v úrovně procent|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště.|B|Average|Limit úložiště.|Žádné dimenze|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Average|Procento úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Average|Využité úložiště protokolů serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Average|Limit úložiště protokolů serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|B|Average|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Sítě v rámci aktivních připojení|Žádné dimenze|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v řádu sekund|Žádné dimenze|
|pg_replica_log_delay_in_bytes|Maximální prodleva mezi repliky|B|Maximum|Zpoždění v bajtech nejvíce obložení repliky|Žádné dimenze|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|memory_percent|Paměť v procentech|Percent|Average|Paměť v procentech|Žádné dimenze|
|vstupně-výstupních operací|IOPS|Count|Average|V/v operací za sekundu|Žádné dimenze|
|storage_percent|Procento úložiště|Percent|Average|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|B|Average|Využité úložiště|Žádné dimenze|
|active_connections|Aktivní připojení|Count|Average|Aktivní připojení|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|B|Celkem|Odchozí síťový provoz mezi aktivních připojení|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|B|Celkem|Sítě v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslat zpráva telemetrie|Count|Celkem|Počet zpráv typu zařízení cloud telemetrie, které se pokusil odeslat do služby IoT hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Telemetrické zprávy odesílané|Count|Celkem|Počet zpráv typu zařízení cloud telemetrie úspěšně odeslán do služby IoT hub|Žádné dimenze|
|c2d.commands.egress.complete.success|Dokončení příkazů|Count|Celkem|Počet příkazů typu cloud zařízení zařízení byla úspěšně dokončena|Žádné dimenze|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Count|Celkem|Počet opuštěných zařízení příkazy typu cloud zařízení|Žádné dimenze|
|c2d.commands.egress.reject.success|Příkazy zamítnuto|Count|Celkem|Počet odmítnutých zařízení příkazy typu cloud zařízení|Žádné dimenze|
|devices.totalDevices|Celkový počet zařízení (zastaralé)|Count|Celkem|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojené zařízení (zastaralé) |Count|Celkem|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|d2c.telemetry.egress.success|Směrování: telemetrické zprávy doručí|Count|Celkem|Počet pokusů, které zprávy se úspěšně doručila na všechny koncové body pomocí služby IoT Hub směrování. Pokud zpráva je směrována do několika koncových bodů, tato hodnota zvýší o jedna pro každé úspěšné dodání. Pokud zpráva se doručí na stejný koncový bod vícekrát, tato hodnota zvýší o jedna pro každé úspěšné dodání.|Žádné dimenze|
|d2c.telemetry.egress.dropped|Směrování: telemetrie počet ztracených zpráv |Count|Celkem|Počet pokusů, které služba IoT Hub směrování z důvodu dead koncové body byly zahodit zprávy. Tato hodnota nepočítá doručování zpráv do základní trasy dodaným vynechané zprávy nejsou existuje.|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Směrování: telemetrické zprávy osamocené |Count|Celkem|Počet pokusů, které zprávy byly osamocené přesměrováním služby IoT Hub, protože neodpovídají žádné pravidla směrování (včetně základní pravidlo). |Žádné dimenze|
|d2c.telemetry.egress.invalid|Směrování: telemetrické zprávy nekompatibilní|Count|Celkem|Počet pokusů směrování služby IoT Hub se nepodařilo doručit z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakovaných pokusů.|Žádné dimenze|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručeny pro použití náhradní lokality|Count|Celkem|Počet pokusů, které směrování služby IoT Hub doručení zprávy na koncový bod přidružený k použití náhradní lokality trasy.|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručeny do centra událostí|Count|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů centra událostí.|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Směrování: zpráva latence pro Centrum událostí|Milisekundy|Average|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat zprávu do koncového bodu centra událostí.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Směrování: doručení zpráv do fronty služby Service Bus|Count|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do fronty služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Směrování: zpráva latence pro frontu služby Service Bus|Milisekundy|Average|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do fronty koncového bodu služby Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Směrování: doručení zprávy do tématu služby Service Bus|Count|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy do tématu služby Service Bus koncových bodů.|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Směrování: zpráva latence pro téma služby Service Bus|Milisekundy|Average|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do tématu koncovým bodem Service Bus.|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručovat zprávy a události|Count|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zprávy na integrovaný koncový bod (zprávy/events). Tato metrika jenom začne pracovat, když je povoleno směrování (https://aka.ms/iotrouting) pro službu IoT hub.|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Směrování: zpráva latence pro zprávy a události|Milisekundy|Average|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy do integrovaného koncového bodu (zprávy/events). Tato metrika jenom začne pracovat, když je povoleno směrování (https://aka.ms/iotrouting) pro službu IoT hub.|Žádné dimenze|
|d2c.endpoints.egress.storage|Směrování: doručení zprávy do služby storage|Count|Celkem|Počet pokusů, které se úspěšně směrování služby IoT Hub doručení zpráv do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.latency.storage|Směrování: zpráva latence pro úložiště|Milisekundy|Average|Průměrná latence (milisekundy) mezi zprávy příchozího přenosu dat do služby IoT Hub a příchozího přenosu dat telemetrické zprávy na koncový bod úložiště.|Žádné dimenze|
|d2c.endpoints.egress.storage.bytes|Směrování: data doručí do úložiště|B|Celkem|Objem dat (v bajtech) směrování služby IoT Hub doručí do koncových bodů úložiště.|Žádné dimenze|
|d2c.endpoints.egress.storage.blobs|Směrování: objekty BLOB doručit do úložiště|Count|Celkem|Počet pokusů, které směrování služby IoT Hub doručí do koncových bodů úložiště objektů BLOB.|Žádné dimenze|
|EventGridDeliveries|Event Grid doručení (preview)|Count|Celkem|Počet událostí služby IoT Hub, které se publikují do služby Event Grid. Dimenze výsledku používejte po dobu úspěšné i neúspěšné požadavky. Dimenze typ EventType ukazuje typ události (https://aka.ms/ioteventgrid).|Výsledek, typ události|
|EventGridLatency|Průměrná latence (milisekundy) z při událostí služby Iot Hub se vygeneroval pro při publikování události do služby Event Grid. Toto číslo je průměr mezi všechny typy událostí. Pomocí dimenze typ EventType zobrazíte latence konkrétní typ události.|EventType|
|d2c.twin.read.success|Čtení dvojčat úspěšné ze zařízení|Count|Celkem|Počet všech úspěšných čtení dvojčat zařízení iniciované.|Žádné dimenze|
|d2c.twin.read.failure|Čtení dvojčat ze zařízení se nezdařila|Count|Celkem|Počet všech se nezdařilo čtení dvojčat zařízení inicioval.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi čtení dvojčat zařízení|B|Average|Průměrné, minimální a maximální ze všech úspěšné, zařízení iniciované dvojčete čtení.|Žádné dimenze|
|d2c.twin.update.success|Aktualizace dvojčat úspěšné ze zařízení|Count|Celkem|Počet všech úspěšných aktualizací dvojčete zařízení iniciované.|Žádné dimenze|
|d2c.twin.update.failure|Aktualizace dvojčat ze zařízení s chybami|Count|Celkem|Počet všech neúspěšných dvojčete zařízení iniciované aktualizací.|Žádné dimenze|
|d2c.twin.update.size|Velikost aktualizace dvojčete zařízení|B|Average|Průměrné a minimální a maximální velikost všech úspěšné, zařízení iniciované dvojčete aktualizace.|Žádné dimenze|
|c2d.methods.success|Volání úspěšné přímé metody|Count|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|c2d.methods.failure|Přímé metody vyvolání se nezdařilo|Count|Celkem|Počet všech se nezdařilo volání metody s přímým přístupem.|Žádné dimenze|
|c2d.methods.requestSize|Žádost o velikosti vyvolání přímé metody|B|Average|Průměrné, minimální a maximální počet všech úspěšných požadavků přímé metody.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odpovědi vyvolání přímé metody|B|Average|Průměrné, minimální a maximální všechny úspěšné přímé metody odpovědí.|Žádné dimenze|
|c2d.twin.read.success|Čtení dvojčat úspěšné z back-endu|Count|Celkem|Počet všech úspěšných čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.twin.read.failure|Čtení dvojčat se nezdařilo z back-endu|Count|Celkem|Počet všech se nezdařilo čtení dvojčat iniciované back-end.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi čtení dvojčat z back-endu|B|Average|Průměr a minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete čtení.|Žádné dimenze|
|c2d.twin.update.success|Aktualizace dvojčat úspěšné z back-endu|Count|Celkem|Počet všech úspěšné aktualizace dvojčete iniciované back-end.|Žádné dimenze|
|c2d.twin.update.failure|Aktualizace dvojčat se nezdařilo z back-endu|Count|Celkem|Počet všech neúspěšných iniciované back-end dvojčete aktualizací.|Žádné dimenze|
|c2d.twin.update.size|Velikost dvojčete aktualizací z back-endu|B|Average|Průměrné, minimální a maximální velikost všech úspěšné, iniciované back-end dvojčete aktualizace.|Žádné dimenze|
|twinQueries.success|Úspěšné dvojčete dotazy|Count|Celkem|Počet všech úspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné dvojčete dotazy|Count|Celkem|Počet všech neúspěšných dvojčete dotazů.|Žádné dimenze|
|twinQueries.resultSize|Velikost výsledku dotazy dvojčete|B|Average|Průměrné, minimální a maximální velikosti výsledek všech dotazů na dvojčata úspěšné.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úlohy aktualizace dvojčete|Count|Celkem|Počet všech po úspěšném vytvoření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úlohy aktualizace dvojčete|Count|Celkem|Počet všech neúspěšných vytváření úlohy aktualizace dvojčete.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh vyvolání – metoda|Count|Celkem|Počet všech po úspěšném vytvoření úlohy vyvolání přímé metody.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh vyvolání – metoda|Count|Celkem|Počet všech neúspěšných vytváření úloh vyvolání přímé metody.|Žádné dimenze|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Count|Celkem|Počet všech úspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.listJobs.failure|Neúspěšná volání do seznamu úloh|Count|Celkem|Počet všech neúspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.cancelJob.success|V případě úspěšné úlohy|Count|Celkem|Počet všech úspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.cancelJob.failure|Zrušení úlohy, která selhala|Count|Celkem|Počet všech neúspěšných volání zrušit úlohu.|Žádné dimenze|
|jobs.queryJobs.success|Úspěšné úlohy dotazů|Count|Celkem|Počet všech úspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.queryJobs.failure|Neúspěšné úlohy dotazy|Count|Celkem|Počet všech neúspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.completed|Dokončené úlohy|Count|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|jobs.failed|Neúspěšné úlohy|Count|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezení|Count|Celkem|Omezí počet chyb omezení kvůli propustnost zařízení|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet zpráv používá|Count|Average|Počet celkový počet zpráv, které se dnes používají. Jedná se o kumulativní hodnotu, která se resetuje na nulu v 00:00 UTC každý den.|Žádné dimenze|
|deviceDataUsage|Celkový počet zařízení využití dat|B|Celkem|Bajtů přenesených do a z jakékoli zařízení připojené k IOT hub|Žádné dimenze|
|totalDeviceCount|Celkový počet zařízení (preview)|Count|Average|Počet zařízení zaregistrovaný do služby IoT hub|Žádné dimenze|
|connectedDeviceCount|Připojené zařízení (preview)|Count|Average|Počet zařízení připojená ke službě IoT hub|Žádné dimenze|
|Konfigurace|Konfigurace metrik|Count|Celkem|Pro operace konfigurace metrik|Žádné dimenze|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Count|Celkem|Počet k pokusu o registraci zařízení|Stav ProvisioningServiceName IotHubName,|
|DeviceAssignments|Přiřazená zařízení|Count|Celkem|Počet zařízení přiřazená do služby IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Count|Celkem|Počet zařízení atestací pokus o|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AvailableStorage|Dostupné úložiště|B|Celkem|Celkový úložiště k dispozici hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|CassandraConnectionClosures|Uzávěry připojení Cassandra|Count|Celkem|Počet připojení Cassandra, které nebyly uzavřeny, a Ohlášeno za 1 minutu|Oblast, ClosureReason|
|CassandraRequestCharges|Poplatky za žádost Cassandra|Count|Celkem|Počet ru spotřebovaných pro Cassandra žádosti|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Požadavky Cassandra|Count|Count|Počet zpracovaných požadavků Cassandra|Oblast, DatabaseName, CollectionName, typem operace OperationType, ResourceType, kód chyby|
|DataUsage|Využití dat|B|Celkem|Celkové množství dat využití hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|DocumentCount|Počet dokumentů|Count|Celkem|Celkového počtu dokumentů hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|DocumentQuota|Kvóta dokumentu|B|Celkem|Kvóta celkový úložiště hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|IndexUsage|Použití indexu|B|Celkem|Celkový počet index využití hlášené v intervalech 5 minut|CollectionName, DatabaseName, oblast|
|MetadataRequests|Požadavky na metadata|Count|Count|Počet žádostí o metadata. Cosmos DB udržuje kolekci metadat systému pro každý účet, který umožňuje vytvoření výčtu kolekce, databázím atd., a jejich konfigurací, které jsou zdarma.|DatabaseName CollectionName, oblast, StatusCode, |
|MongoRequestCharge|Zátěž žádostí mongo|Count|Celkem|Spotřebované jednotky žádostí mongo|Název databáze, CollectionName, oblast, CommandName, kód chyby|
|MongoRequests|Požadavky mongo|Count|Count|Počet zpracovaných požadavků Mongo|Název databáze, CollectionName, oblast, CommandName, kód chyby|
|ProvisionedThroughput|Zřízená propustnost|Count|Maximum|Zřízená propustnost|DatabaseName CollectionName|
|ReplicationLatency|P99 Latence replikace|Milisekundy|Average|Latencí P99 replikace mezi zdrojovou a cílovou oblastí pro účet povolenou geografickou|SourceRegion TargetRegion|
|ServiceAvailability|Dostupnost služby|Percent|Average|Dostupnost účtu požadavky na jednu hodinu, den nebo měsíc členitosti|Žádné dimenze|
|TotalRequestUnits|Celkový požadavek jednotky|Count|Celkem|Požadavku že spotřebované jednotky|Název databáze, CollectionName, oblast, StatusCode, typ operace|
|TotalRequests|Požadavky celkem|Count|Count|Počet zpracovaných požadavků|Název databáze, CollectionName, oblast, StatusCode, typ operace|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí, které publikuje na toto téma|Žádné dimenze|
|PublishFailCount|Publikování událostí se nezdařilo|Count|Celkem|Celkový počet událostí, které se nepovedlo publikovat. k tomuto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárovaných událostí|Count|Celkem|Celkový počet událostí, které se neshodují žádné odběry událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Publikování latence při úspěchu|Count|Celkem|Publikování latence při úspěchu v milisekundách|Žádné dimenze|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Odpovídající události|Count|Celkem|Celkový počet událostí, které odpovídají tento odběr události|Žádné dimenze|
|DeliveryAttemptFailCount|Doručení události nezdařilo|Count|Celkem|Celkový počet událostí doručení pro tento odběr události se nezdařilo.|Chyba ErrorType.|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručení pro tento odběr události|Žádné dimenze|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíl|Milisekundy|Average|Doba trvání zpracování cíl v milisekundách|Žádné dimenze|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vynechaných událostí vzorů pro tento odběr události|DropReason|
|DeadLetteredCount|Dead Lettered události|Count|Celkem|Celkový počet událostí dead lettered vzorů pro tento odběr události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí, které publikuje na toto téma|Žádné dimenze|
|PublishFailCount|Události se nezdařilo|Count|Celkem|Celkový počet událostí, které se nepovedlo publikovat. k tomuto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárovaných událostí|Count|Celkem|Celkový počet událostí, které se neshodují žádné odběry událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Publikování latence při úspěchu|Count|Celkem|Publikování latence při úspěchu v milisekundách|Žádné dimenze|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|Celkem|Úspěšné požadavky pro Microsoft.EventHub|EntityName, |
|ServerErrors|Chyby serveru.|Count|Celkem|Chyby serveru pro Microsoft.EventHub|EntityName, |
|UserErrors|Chyby uživatele.|Count|Celkem|Chyby uživatele pro Microsoft.EventHub|EntityName, |
|QuotaExceededErrors|Chyby překročení kvóty.|Count|Celkem|Chyby překročení kvóty pro Microsoft.EventHub|EntityName, |
|ThrottledRequests|Omezené požadavky.|Count|Celkem|Omezené požadavky pro Microsoft.EventHub|EntityName, |
|IncomingRequests|Příchozí požadavky|Count|Celkem|Příchozí požadavky pro Microsoft.EventHub|EntityName|
|IncomingMessages|Příchozí zprávy|Count|Celkem|Příchozí zprávy pro Microsoft.EventHub|EntityName|
|OutgoingMessages|Odchozí zprávy|Count|Celkem|Odchozí zprávy pro Microsoft.EventHub|EntityName|
|IncomingBytes|Příchozí bajty.|B|Celkem|Příchozí bajty pro Microsoft.EventHub|EntityName|
|OutgoingBytes|Odchozí bajty.|B|Celkem|Odchozí bajty pro Microsoft.EventHub|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Celkový počet aktivních připojení pro Microsoft.EventHub|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení.|Count|Average|Otevřená připojení pro Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Ukončená připojení.|Count|Average|Ukončená připojení pro Microsoft.EventHub.|EntityName|
|CaptureBacklog|Zachytit Backlog.|Count|Celkem|Zachytit Backlog pro Microsoft.EventHub|EntityName|
|CapturedMessages|Zachycené zprávy.|Count|Celkem|Zachycené zprávy pro Microsoft.EventHub|EntityName|
|CapturedBytes|Zachycené bajty.|B|Celkem|Zachycené bajty pro Microsoft.EventHub|EntityName|
|Size|Size|B|Average|Velikost centra událostí v bajtech.|EntityName|
|INREQS|Příchozí žádosti (zastaralé)|Count|Celkem|Celkový počet příchozích požadavků na odeslání pro obor názvů (zastaralé)|Žádné dimenze|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|FAILREQ|Neúspěšné požadavky (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|SVRBSY|Chyby kvůli zaneprázdněnosti serveru (zastaralé)|Count|Celkem|Chyby kvůli zaneprázdněnosti úplné havárii serveru pro obor názvů (zastaralé)|Žádné dimenze|
|INTERR|Vnitřní chyby serveru (zastaralé)|Count|Celkem|Celkový počet vnitřních chyb serveru pro obor názvů (zastaralé)|Žádné dimenze|
|MISCERR|Další chyby (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku příchozí zprávy (zastaralé)|Žádné dimenze|
|EHINMSGS|Příchozí zprávy (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku odchozí zprávy (zastaralé)|Žádné dimenze|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|B|Celkem|Příchozí propustnost zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku Příchozí bajty (zastaralé)|Žádné dimenze|
|EHINBYTES|Příchozí bajty (zastaralé)|B|Celkem|Příchozí propustnost zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|B|Celkem|Odchozí propustnost zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Použijte místo toho metriku Odchozí bajty (zastaralé)|Žádné dimenze|
|EHOUTBYTES|Odchozí bajty (zastaralé)|B|Celkem|Odchozí propustnost zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHABL|Archivovat zprávy backlogu (zastaralé)|Count|Celkem|Archivované zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádné dimenze|
|EHAMSGS|Archivace zpráv (zastaralé)|Count|Celkem|Archivované zprávy v oboru názvů (zastaralé) centra událostí|Žádné dimenze|
|EHAMBS|Archivovat propustnost zpráv (zastaralé)|B|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádné dimenze|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|Celkem|Úspěšné požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ServerErrors|Chyby serveru. (Preview)|Count|Celkem|Chyby serveru pro Microsoft.EventHub (Preview)|Žádné dimenze|
|UserErrors|Chyby uživatele. (Preview)|Count|Celkem|Chyby uživatele pro Microsoft.EventHub (Preview)|Žádné dimenze|
|QuotaExceededErrors|Chyby překročení kvóty. (Preview)|Count|Celkem|Chyby překročení kvóty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ThrottledRequests|Omezené požadavky. (Preview)|Count|Celkem|Omezené požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingRequests|Příchozí žádosti (Preview)|Count|Celkem|Příchozí požadavky pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingMessages|Příchozí zprávy (Preview)|Count|Celkem|Příchozí zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|Celkem|Odchozí zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|IncomingBytes|Příchozí bajty. (Preview)|B|Celkem|Příchozí bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|OutgoingBytes|Odchozí bajty. (Preview)|B|Celkem|Odchozí bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ActiveConnections|ActiveConnections (Preview)|Count|Average|Celkový počet aktivních připojení pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení. (Preview)|Count|Average|Otevřená připojení pro Microsoft.EventHub. (Preview)|Žádné dimenze|
|ConnectionsClosed|Ukončená připojení. (Preview)|Count|Average|Ukončená připojení pro Microsoft.EventHub. (Preview)|Žádné dimenze|
|CaptureBacklog|Zachytit Backlog. (Preview)|Count|Celkem|Zachytit Backlog pro Microsoft.EventHub (Preview)|Žádné dimenze|
|CapturedMessages|Zachycené zprávy. (Preview)|Count|Celkem|Zachycené zprávy pro Microsoft.EventHub (Preview)|Žádné dimenze|
|CapturedBytes|Zachycené bajty. (Preview)|B|Celkem|Zachycené bajty pro Microsoft.EventHub (Preview)|Žádné dimenze|
|Procesor|Procesor (Preview)|Percent|Maximum|Využití procesoru pro Cluster centra událostí v procentech|Role|
|AvailableMemory|Dostupná paměť (Preview)|Count|Maximum|Paměť dostupná clusteru centra událostí v bajtech|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Požadavky brány|Count|Celkem|Počet požadavků brány|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Požadavky brány podle kategorií|Count|Celkem|Počet požadavků brány podle kategorií (1xx/2xx nebo 3xx/4xx a 5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Počet aktivních pracovních procesů|Count|Maximum|Počet aktivních pracovních procesů|Název_dns_clusteru MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zjištěná hodnota metriky|Count|Average|Hodnota vypočítaná automatickým Škálováním při spuštění|MetricTriggerSource|
|MetricThreshold|Mezní hodnota metriky|Count|Average|Nakonfigurované mezní hodnota automatického škálování při spuštění automatického škálování.|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Count|Average|Kapacita ohlášená automatickému škálování při jeho spuštění.|Žádné dimenze|
|ScaleActionsInitiated|Zahájené akce škálování|Count|Celkem|Směr operace škálování.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Average|Procento testů dostupnosti úspěšně dokončila.|availabilityResult/name, availabilityResult/umístění|
|availabilityResults/počet|Testy dostupnosti|Count|Count|Počet testů dostupnosti|availabilityResult/name, availabilityResult nebo umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba testu dostupnosti|Milisekundy|Average|Doba testu dostupnosti|availabilityResult/name, availabilityResult nebo umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Čas síťového připojení při zatížení stránky|Milisekundy|Average|Doba mezi uživatele požadavku a síťové připojení. Zahrnuje DNS vyhledávání a přenosové připojení.|Žádné dimenze|
|browserTimings/processingDuration|Čas klientského zpracování|Milisekundy|Average|Doba mezi přijetím posledního bajtu dokumentu, dokud je načten v modelu DOM. Asynchronní žádosti se možná ještě zpracovávají.|Žádné dimenze|
|browserTimings/receiveDuration|Čas přijetí odezvy|Milisekundy|Average|Doba mezi prvním a posledním bajtem nebo do odpojení.|Žádné dimenze|
|browserTimings/sendDuration|Čas odeslání žádosti|Milisekundy|Average|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádné dimenze|
|browserTimings/totalDuration|Doba načítání stránek prohlížečem|Milisekundy|Average|Doba od žádosti uživatele do modelu DOM, šablon stylů, skripty a bitové kopie se načítají.|Žádné dimenze|
|závislosti nebo count|Volání závislostí|Count|Count|Počet volání prováděných aplikací vůči externím prostředkům.|/ Typ závislosti, závislost nebo performanceBucket, závislost/úspěch, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|závislosti a doba trvání|Doba trvání závislosti|Milisekundy|Average|Doba trvání volání prováděných aplikací vůči externím prostředkům.|/ Typ závislosti, závislost nebo performanceBucket, závislost/úspěch, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|/ Neúspěšné závislosti|Selhání volání závislostí|Count|Count|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům.|/ Typ závislosti, závislost nebo performanceBucket, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|zobrazení stránky nebo count|Zobrazení stránek|Count|Count|Počet zobrazení stránek.|operace/synthetic|
|zobrazení stránky a doba trvání|Doba načtení zobrazení stránky|Milisekundy|Average|Doba načtení zobrazení stránky|operace/synthetic|
|performanceCounters/requestExecutionTime|Doba provádění požadavku HTTP|Milisekundy|Average|Doba provádění nejnovější žádosti.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Požadavky HTTP ve frontě aplikace|Count|Average|Délka fronty žádostí na aplikace.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Četnost požadavků HTTP|CountPerSecond|Average|Počet všech žádostí na aplikaci za sekundu z ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|frekvence výjimek|CountPerSecond|Average|Počet zpracovaných a nezpracovaných výjimek hlášených systému windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Frekvence v/v procesu|BytesPerSecond|Average|Celkový počet bajtů za sekundu číst a zapisovat do souborů, sítě a zařízení.|cloud/roleInstance|
|čítače výkonu nebo processCpuPercentage|Proces – procesor|Percent|Average|Procento uplynulého času, že všechna vlákna procesu používala procesor pro spouštění instrukcí. To se může lišit od 0 do 100. Tato Metrika vyjadřuje výkon samotného procesu w3wp.|cloud/roleInstance|
|čítače výkonu nebo processorCpuPercentage|Čas procesoru|Percent|Average|Procento času, které procesor stráví na nečinných vláknech.|cloud/roleInstance|
|čítače výkonu nebo memoryAvailableBytes|Dostupná paměť|B|Average|Fyzická paměť k dispozici pro přidělení procesu nebo pro použití systémem.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Privátní bajty procesu|B|Average|Paměť exkluzivně přiřazená k procesům monitorované aplikace.|cloud/roleInstance|
|požadavky a doba trvání|Doba odezvy serveru|Milisekundy|Average|Doba mezi přijetím žádosti HTTP a dokončením odesílání odpovědi.|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|požadavky nebo count|Požadavky na server|Count|Count|Dokončených žádostí Count HTTP.|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|/ Neúspěšné požadavky|Neúspěšné požadavky|Count|Count|Požadavky HTTP počet označen jako neúspěšný. Ve většině případů jde o žádosti s kódem odpovědi > = 400 a není rovno 401.|žádost/performanceBucket, požadavek/resultCode, operace nebo syntetický, cloudu/instance role, cloudu/roleName|
|požadavky a míra|Frekvence požadavků serveru|CountPerSecond|Average|Rychlost požadavků na server za sekundu|žádost/performanceBucket, požadavek/kód výsledku, operace nebo syntetický, cloud nebo instance role, žádost o/úspěch, cloudu/roleName|
|výjimky nebo count|Výjimky|Count|Count|Kombinovaný počet všech nezachycených výjimek.|cloud/roleName, cloud nebo instance role, klient/typu|
|výjimky a prohlížeče|Výjimky prohlížečů|Count|Count|Počet nezachycených výjimek vyvolaných v prohlížeči.|Žádné dimenze|
|výjimky/server|Serverové výjimky|Count|Count|Počet nezachycených výjimek vyvolaných v serverové aplikaci.|cloud/roleName, cloud nebo instance role|
|trasování nebo count|trasování|Count|Count|Počet dokumentů trasování|trasování/severityLevel, operace nebo syntetický, cloudu/roleName, cloud nebo instance role|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkové využití přístupů k rozhraní Api|Count|Count|Celkový počet přístupů k rozhraní api služby|ActivityType, ActivityName|
|ServiceApiLatency|Celková latence rozhraní Api služby|Milisekundy|Average|Požadavků na celkové latence služby rozhraní api|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Celkové využití Api výsledky|Count|Count|Celkový počet výsledků rozhraní api služby|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Využití mezipaměti|Percent|Average|Úroveň využití v rámci clusteru|Žádné|
|QueryDuration|Doba trvání dotazu|Milisekundy|Average|Dotazy doba trvání v sekundách|Stav dotazu|
|IngestionUtilization|Ingestování využití|Percent|Average|Poměr používané ingestování sloty v clusteru|Žádné|
|KeepAlive|Zachování|Count|Average|Kontrola správnosti označuje, že cluster odpoví na dotazy|Žádné|
|IngestionVolumeInMB|Ingestování svazek (v MB)|Count|Celkem|Celkový objem přijatých dat do clusteru (v MB)|Databáze|
|IngestionLatencyInSeconds|Latence příjmu dat (v sekundách)|Sekundy|Average|Ingestování čas ze zdroje (například je zpráva EventHub) do clusteru během několika sekund|Žádné|
|EventProcessedForEventHubs|Události byly zpracovány (pro službu Event Hubs)|Count|Celkem|Počet události byly zpracovány v clusteru při ingestování z centra událostí|Žádné|
|IngestionResult|Příjem výsledků|Count|Count|Počet operací příjmu|Stav|
|Procesor|Procesor|Percent|Average|Úroveň využití procesoru|Žádný|
| ContinuousExportNumOfRecordsExported | Počet záznamů, které exportovali v průběžný export. | Count | Celkem | Exportovat několik záznamů pro každé úložiště artefaktů napsané v průběhu operace exportu  | Žádný |
| ExportUtilization | Exportovat využití | Percent | Maximum | Exportovat využití | Žádné |
| ContinuousExportPendingCount | Průběžný Export počtu čekajících | Count | Maximum | Počet čekající průběžného exportu připraven ke spuštění úlohy | Žádné |
| ContinuousExportMaxLatenessMinutes | Průběžný Export Maximální zpoždění minut | Count | Maximum | Maximální čas v minutách všechny průběžné exporty, které jsou čekající na vyřízení a připraven ke spuštění | Žádný |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Použití|Použití|Count|Count|Volání počet rozhraní API|ApiCategory ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet běhů pracovního postupu spuštěn.|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet běhů pracovního postupu dokončené.|Žádné dimenze|
|RunsSucceeded|Úspěšné běhy|Count|Celkem|Počet běhů pracovního postupu bylo úspěšné.|Žádné dimenze|
|RunsFailed|Neúspěšné běhy|Count|Celkem|Počet běhů pracovního postupu se nezdařilo.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet běhů pracovního postupu zrušila.|Žádné dimenze|
|RunLatency|Latence běhu|Sekundy|Average|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšných běhů|Sekundy|Average|Latence úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Omezené události běhu|Count|Celkem|Počet akcí pracovního postupu nebo aktivační událost omezených událostí.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|Celkem|Procento pracovní postup spouští se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet akcí pracovního postupu spuštění.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu byla úspěšná.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce|Count|Celkem|Počet spuštěných akcí pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet spuštěných akcí pracovního postupu přeskočeno.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Average|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Average|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet akcí pracovního postupu omezených událostí...|Žádné dimenze|
|TriggersStarted|Spuštěné triggery |Count|Celkem|Počet spuštění triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Dokončené triggery |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Úspěšné triggery |Count|Celkem|Počet triggerů pracovního postupu byla úspěšná.|Žádné dimenze|
|TriggersFailed|Neúspěšné triggery |Count|Celkem|Počet triggerů pracovního postupu se nezdařilo.|Žádné dimenze|
|TriggersSkipped|Vynechané triggery|Count|Celkem|Počet triggerů pracovního postupu přeskočeno.|Žádné dimenze|
|TriggersFired|Vyvolané triggery |Count|Celkem|Událost je počet triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Average|Latence triggerů pracovního postupu dokončené.|Žádné dimenze|
|TriggerFireLatency|Latence při vyvolání triggeru |Sekundy|Average|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšného triggeru |Sekundy|Average|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet triggeru pracovního postupu omezených událostí.|Žádné dimenze|
|BillableActionExecutions|Fakturovatelné operace provedení akce|Count|Celkem|Počet fakturovaných pracovního postupu akce.|Žádné dimenze|
|BillableTriggerExecutions|Fakturovatelné operace triggerů|Count|Celkem|Počet fakturovaných pracovního postupu aktivační události.|Žádné dimenze|
|TotalBillableExecutions|Fakturovatelné operace provedení celkem|Count|Celkem|Počet fakturovaných pracovního postupu.|Žádné dimenze|
|BillingUsageNativeOperation|Fakturace použití pro nativní operace spuštění|Count|Celkem|Počet nativních operace fakturovaných provedení.|Žádné dimenze|
|BillingUsageStandardConnector|Fakturace použití pro spuštění standardních konektorů|Count|Celkem|Počet standardní konektor fakturovaných provedení.|Žádné dimenze|
|BillingUsageStorageConsumption|Fakturace za spouštění spotřebu úložiště využití|Count|Celkem|Počet úložiště spotřeby fakturovaných provedení.|Žádné dimenze|
|BillingUsageNativeOperation|Fakturace použití pro nativní operace spuštění|Count|Celkem|Počet nativních operace fakturovaných provedení.|Žádné dimenze|
|BillingUsageStandardConnector|Fakturace použití pro spuštění standardních konektorů|Count|Celkem|Počet standardní konektor fakturovaných provedení.|Žádné dimenze|
|BillingUsageStorageConsumption|Fakturace za spouštění spotřebu úložiště využití|Count|Celkem|Počet úložiště spotřeby fakturovaných provedení.|Žádné dimenze|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet běhů pracovního postupu spuštěn.|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet běhů pracovního postupu dokončené.|Žádné dimenze|
|RunsSucceeded|Úspěšné běhy|Count|Celkem|Počet běhů pracovního postupu bylo úspěšné.|Žádné dimenze|
|RunsFailed|Neúspěšné běhy|Count|Celkem|Počet běhů pracovního postupu se nezdařilo.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet běhů pracovního postupu zrušila.|Žádné dimenze|
|RunLatency|Latence běhu|Sekundy|Average|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšných běhů|Sekundy|Average|Latence úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Omezené události běhu|Count|Celkem|Počet akcí pracovního postupu nebo aktivační událost omezených událostí.|Žádné dimenze|
|RunStartThrottledEvents|Omezené události spuštění|Count|Celkem|Počet spuštění pracovního postupu omezených událostí.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Percent|Celkem|Procento pracovní postup spouští se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet akcí pracovního postupu spuštění.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu byla úspěšná.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet spuštěných akcí pracovního postupu přeskočeno.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Average|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Average|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet akcí pracovního postupu omezených událostí...|Žádné dimenze|
|TriggersStarted|Spuštěné triggery |Count|Celkem|Počet spuštění triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Dokončené triggery |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Úspěšné triggery |Count|Celkem|Počet triggerů pracovního postupu byla úspěšná.|Žádné dimenze|
|TriggersFailed|Neúspěšné triggery |Count|Celkem|Počet triggerů pracovního postupu se nezdařilo.|Žádné dimenze|
|TriggersSkipped|Vynechané triggery|Count|Celkem|Počet triggerů pracovního postupu přeskočeno.|Žádné dimenze|
|TriggersFired|Vyvolané triggery |Count|Celkem|Událost je počet triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Average|Latence triggerů pracovního postupu dokončené.|Žádné dimenze|
|TriggerFireLatency|Latence při vyvolání triggeru |Sekundy|Average|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšného triggeru |Sekundy|Average|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet triggeru pracovního postupu omezených událostí.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Percent|Average|Pracovní postup využití procesoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Pracovní postup využití paměti pro prostředí integrační služby|Percent|Average|Pracovní postup využití paměti pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektor pro prostředí integrační služby|Percent|Average|Konektor využití procesoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Konektor využití paměti pro prostředí integrační služby|Percent|Average|Konektor využití paměti pro prostředí integrační služby.|Žádné dimenze|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Dokončené běhy|Dokončené běhy|Count|Celkem|Počet běhů bylo dokončeno úspěšně pro tento pracovní prostor|Scénář|
|Začínáme spuštění|Začínáme spuštění|Count|Celkem|Číslo, které spustil pro tento pracovní prostor|Scénář|
|Neúspěšná spuštění|Neúspěšná spuštění|Count|Celkem|Počet běhů se nezdařilo pro tento pracovní prostor|Scénář|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Použití|Použití|Count|Count|Volání počet rozhraní API|ApiCategory ResponseCode ApiName, typ ResultType,|
|Dostupnost|Dostupnost|Procento|Average|Dostupnost rozhraní API|ApiCategory ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageOtherLatency|Průměrná další latence|ms/op|Average|Průměrná jiné (který není pro čtení nebo zápisu) latence v milisekundách na operaci|Žádné dimenze|
|AverageReadLatency|Průměrná latence čtení|ms/op|Average|Průměrná latence v milisekundách na operaci čtení|Žádné dimenze|
|AverageTotalLatency|Průměrná celková latence|ms/op|Average|Průměrná celková latence v milisekundách na operaci|Žádné dimenze|
|AverageWriteLatency|Zápis Průměrná latence|ms/op|Average|Zápis Průměrná latence v milisekundách na operaci|Žádné dimenze|
|FilesystemOtherOps|Systém souborů ostatní operace|OPS|Average|Číslo systému souborů ostatní operace (které není pro čtení nebo zápis)|Žádné dimenze|
|FilesystemReadOps|Operace čtení systému souborů|OPS|Average|Operace čtení počtu systému souborů|Žádné dimenze|
|FilesystemTotalOps|Celkový počet operací systému souborů|OPS|Average|Součet všech operací systému souborů|Žádné dimenze|
|FilesystemWriteOps|Operace zápisu systému souborů|OPS|Average|Počet operací zápisu systému souborů|Žádné dimenze|
|IoBytesPerOtherOps|Bajtů na vstupně-výstupních operací na ostatní operace|bajty/op|Average|Číslo z In/out bajtů na ostatní operace (který není pro čtení nebo zápis)|Žádné dimenze|
|IoBytesPerReadOps|Bajty vstupně-výstupních operací za operace čtení|bajty/op|Average|Počet bajtů za operace čtení In/out|Žádné dimenze|
|IoBytesPerTotalOps|Bajty vstupně-výstupních operací za op ve všech operacích|bajty/op|Average|Součet všech příchozí/odchozí provoz bajtů|Žádné dimenze|
|IoBytesPerWriteOps|Bajty vstupně-výstupních operací za operace zápisu|bajty/op|Average|Číslo In/out bajtů za operace zápisu|Žádné dimenze|
|OtherIops|Další vstupně-výstupních operací|operací za sekundu|Average|Druhý vstup a výstup operace za sekundu|Žádné dimenze|
|OtherThroughput|Další propustnost|MB/s|Average|Ostatní (který není pro čtení nebo zápisu) propustnost v MB za sekundu|Žádné dimenze|
|ReadIops|Vstupně-výstupních operací čtení|operací za sekundu|Average|Přečtěte si v vstupně-výstupní operace za sekundu|Žádné dimenze|
|ReadThroughput|Propustnost pro čtení|MB/s|Average|Propustnost čtení v MB za sekundu|Žádné dimenze|
|TotalIops|Celkový počet iops|operací za sekundu|Average|Součet všech vstup a výstup operací za sekundu|Žádné dimenze|
|TotalThroughput|Celková propustnost|MB/s|Average|Součet všech propustnost v MB za sekundu|Žádné dimenze|
|VolumeAllocatedSize|Přidělená velikost svazku|bajtů|Average|Přidělená velikost svazku (nikoli skutečné používá bajtů)|Žádné dimenze|
|VolumeLogicalSize|Logická velikost svazku|bajtů|Average|Logická velikost svazku (využité bajty)|Žádné dimenze|
|VolumeSnapshotSize|Velikost snímku svazku|bajtů|Average|Velikost všech snímků ve svazku|Žádné dimenze|
|WriteIops|Zápis iops|operací za sekundu|Average|Zápis vstup a výstup operací za sekundu|Žádné dimenze|
|WriteThroughput|Zápis propustnost|MB/s|Average|Zápis propustnost v MB za sekundu|Žádné dimenze|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Velikost fondu přidělené svazku|bajtů|Average|Přidělená velikost fondu (nikoli skutečné používá bajtů)|Žádné dimenze|
|VolumePoolAllocatedUsed|Svazek fondu přidělené použít|bajtů|Average|Použitá velikost přidělené fondu|Žádné dimenze|
|VolumePoolTotalLogicalSize|Logická velikost svazku fondu celkem|bajtů|Average|Součet logické velikosti všech svazků, které patří do fondu|Žádné dimenze|
|VolumePoolTotalSnapshotSize|Velikost fondu celkový snímku svazku|bajtů|Average|Součet všech snímků ve fondu|Žádné dimenze|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Count|Celkem|Počet bajtů odeslaných síťové rozhraní|Žádné dimenze|
|BytesReceivedRate|Přijaté bajty|Count|Celkem|Počet bajtů přijatých síťové rozhraní|Žádné dimenze|
|PacketsSentRate|Pakety odeslané|Count|Celkem|Počet paketů síťové rozhraní odeslané|Žádné dimenze|
|PacketsReceivedRate|Obdržených paketů|Count|Celkem|Počet paketů síťové rozhraní přijaté za sekundu|Žádné dimenze|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Cesta dostupnost dat|Count|Average|Průměrná dostupnost cesty dat nástroje pro vyrovnávání zatížení na dobu trvání|FrontendIPAddress, FrontendPort|
|DipAvailability|Sondy stavu|Count|Average|Průměrná nástroje pro vyrovnávání zatížení sondy stavu na dobu trvání|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet bajtů přenesených v časovém období|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet paketů přenášet v časovém období|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Počet SYN|Count|Celkem|Celkový počet paketů SYN přenášet v časovém období|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Počet připojení SNAT|Count|Celkem|Celkový počet nových připojení SNAT vytvořených v časovém období|Vlastnost ConnectionState FrontendIPAddress BackendIPAddress,|
|AllocatedSnatPorts|Přidělené SNAT porty (Preview)|Count|Celkem|Celkový počet portů SNAT přidělené v časovém období|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Používané porty SNAT (Preview)|Count|Celkem|Celkový počet SNAT porty používané v časovém období|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Množství dotazů|Count|Celkem|Počet dotazů, které jsou zpracovány pro zónu DNS|Žádné dimenze|
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
|VipAvailability|Cesta dostupnost dat|Count|Average|Průměrná dostupnost IP adresy na dobu trvání|Port|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet bajtů přenesených v časovém období|Portů, směr|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet paketů přenášet v časovém období|Portů, směr|
|SynCount|Počet SYN|Count|Celkem|Celkový počet paketů SYN přenášet v časovém období|Portů, směr|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet uplatnění pravidel aplikace|Count|Celkem|Počet pokusů, které byly uplatnění pravidel aplikace|Protokol o stavu, důvodu|
|NetworkRuleHit|Počet průchodů pravidel sítě|Count|Celkem|Počet pokusů, které byly uplatnění pravidel sítě|Protokol o stavu, důvodu|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Celkem|Počet bajtů za sekundu, které má služby Application Gateway|Žádné dimenze|
|UnhealthyHostCount|Není v pořádku. počet hostitelů|Count|Average|Počet hostitelů back-end není v pořádku|BackendSettingsPool|
|HealthyHostCount|V pořádku. počet hostitelů|Count|Average|Počet hostitelů v dobrém stavu back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Count|Celkem|Počet úspěšných požadavků, které má služba Application Gateway obsluhuje|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Count|Celkem|Počet neúspěšných žádostí, které má služba Application Gateway obsluhuje|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Count|Celkem|Stav odpovědi HTTP vrácené Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální počet připojení|Count|Celkem|Počet aktuální připojení ke službě Application Gateway|Žádné dimenze|
|CapacityUnits|Aktuální jednotky kapacity|Count|Average|Spotřebované jednotky kapacity|Žádné dimenze|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Brána S2S šířky pásma|BytesPerSecond|Average|Průměrná šířky pásma site-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SBandwidth|P2S šířku pásma|BytesPerSecond|Average|Průměrná šířky pásma point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Počet připojení P2S|Count|Maximum|Počet připojení point-to-site brány|Protocol|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Average|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName RemoteIP|
|TunnelEgressBytes|Odchozí bajty tunelového propojení|B|Celkem|Odchozí bajty tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressBytes|Příchozí bajty tunelového propojení|B|Celkem|Příchozí bajty tunelového propojení|ConnectionName RemoteIP|
|TunnelEgressPackets|Odchozí pakety tunelového propojení|Count|Celkem|Počet odchozích paketů tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressPackets|Příchozí pakety tunelového propojení|Count|Celkem|Počet příchozích paketů tunelového propojení|ConnectionName RemoteIP|
|TunnelEgressPacketDropTSMismatch|Zahození paketu neodpovídá odchozího přenosu dat TS tunelového propojení|Count|Celkem|Počet odchozích paketů přetažení z neshoda selektor provozu tunelového propojení|ConnectionName RemoteIP|
|TunnelIngressPacketDropTSMismatch|Zahození paketu neodpovídá příchozího přenosu dat TS tunelového propojení|Count|Celkem|Počet příchozích paketů přetažení z neshoda selektor provozu tunelového propojení|ConnectionName RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Služba BITS ingressing Azure za sekundu|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Služba BITS egressing Azure za sekundu|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Služba BITS ingressing Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Služba BITS egressing Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Služba BITS ingressing Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Služba BITS egressing Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle koncový bod vrátil|Count|Celkem|Počet pokusů, které v daném časovém rámci byl vrácen koncových bodů Traffic Manageru|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu pomocí koncového bodu|Count|Maximum|1, pokud koncového bodu sondu stav "povoleno", jinak 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Testů paměti se nezdařilo|Percent|Average|% testy monitorování připojení se nezdařilo|Žádné dimenze|
|AverageRoundtripMs|Střední Doba odezvy (ms)|Milisekundy|Average|Průměrná síťové operace round-trip doba (ms) pro připojení k monitorování mezi zdrojem a cílem odeslané testy paměti|Žádné dimenze|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Count|Celkem|Počet požadavků klientů obsluhuje proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Velikost požadavku|B|Celkem|Počet bajtů odeslaných na požadavky klientů na proxy server HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Velikost odpovědi|B|Celkem|Počet bajtů odeslaných jako odpovědi ze serveru proxy HTTP/S pro klienty|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Count|Celkem|Počet požadavků odesílat back-endů proxy HTTP/S|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Odezva back-endu požadavku|Milisekundy|Average|Čas počítají na základě když byla vyslána žádost proxy HTTP/S back-endu dokud proxy HTTP/S poslední bajt odpovědi přijaté z back-endu|Back-endu|
|TotalLatency|Celková latence|Milisekundy|Average|Čas počítají na základě když žádost klienta byla přijata službou proxy HTTP/S dokud klient potvrzení poslední bajt odpovědi z proxy serveru HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stav back-endu|Percent|Average|Procento úspěšných stavů testy z proxy serveru HTTP/S na back-EndY|Back-endu, Bezproblémových|
|WebApplicationFirewallRequestCount|Počet požadavků brány Firewall webových aplikací|Count|Celkem|Počet žádostí klienta zpracovaných rozhraním firewallu webových aplikací|PolicyName, Název_pravidla, akce|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registration.all|Registrace – operace|Count|Celkem|Počet všech úspěšných operací s registrací (vytvoření, aktualizace, dotazy a odstranění). |Žádné dimenze|
|registration.create|Registrace – vytvořit operace|Count|Celkem|Počet všech úspěšných vytvoření registrací.|Žádné dimenze|
|registration.update|Registrace – aktualizovat operace|Count|Celkem|Počet všech úspěšných aktualizací registrací.|Žádné dimenze|
|registration.get|Registrace – přečíst operace|Count|Celkem|Počet všech úspěšných dotazů registrací.|Žádné dimenze|
|registration.delete|Registrace – odstranit operace|Count|Celkem|Počet všech úspěšných odstranění registrací.|Žádné dimenze|
|příchozí|Příchozí zprávy|Count|Celkem|Počet všech úspěšných odeslání volání rozhraní API. |Žádné dimenze|
|incoming.scheduled|Odeslaná plánovaná nabízená oznámení|Count|Celkem|Plánovaná nabízená oznámení bylo zrušeno|Žádné dimenze|
|incoming.scheduled.cancel|Plánovaná nabízená oznámení bylo zrušeno|Count|Celkem|Plánovaná nabízená oznámení bylo zrušeno|Žádné dimenze|
|Scheduled.Pending|Čekající plánovaná oznámení|Count|Celkem|Čekající plánovaná oznámení|Žádné dimenze|
|installation.all|Operace správy instalace|Count|Celkem|Operace správy instalace|Žádné dimenze|
|installation.get|Operace získání instalace|Count|Celkem|Operace získání instalace|Žádné dimenze|
|installation.upsert|Vytvořit nebo aktualizovat operace instalace|Count|Celkem|Vytvořit nebo aktualizovat operace instalace|Žádné dimenze|
|installation.patch|Opravit operace instalace|Count|Celkem|Opravit operace instalace|Žádné dimenze|
|installation.delete|Operace odstranění instalace|Count|Celkem|Operace odstranění instalace|Žádné dimenze|
|outgoing.allpns.success|Úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení.|Žádné dimenze|
|outgoing.allpns.invalidpayload|Chyby datové části|Count|Celkem|Počet nabídek, které se nezdařila, protože systém oznámení platformy vrátil chybu chybné datové části.|Žádné dimenze|
|outgoing.allpns.pnserror|Chyby externího systému oznámení|Count|Celkem|Počet nabídek, které se nezdařila, protože došlo k problému v komunikaci s systému oznámení platformy (nezahrnuje problémy s ověřováním).|Žádné dimenze|
|outgoing.allpns.channelerror|Chyby kanálů|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože kanál nebyl platný není přidružená k správnou aplikaci omezené nebo vypršela jeho platnost.|Žádné dimenze|
|outgoing.allpns.badorexpiredchannel|Chyby kanálů chybný nebo jeho platnost vypršela|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože kanál, token nebo ID v registraci vypršel nebo není platný.|Žádné dimenze|
|outgoing.wns.Success|Wns – úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení.|Žádné dimenze|
|outgoing.wns.invalidcredentials|Wns – chyby autorizace (Neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje nebo přihlašovací údaje jsou zablokované. (Windows Live nerozpozná přihlašovací údaje).|Žádné dimenze|
|outgoing.wns.badchannel|Chyba špatného kanálu služby nabízených oznámení Windows|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci nerozpoznal (stav služby nabízených oznámení Windows: 404 Nenalezeno).|Žádné dimenze|
|outgoing.wns.expiredchannel|Wns – kanál – chyba vypršení platnosti|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože vypršela platnost parametr ChannelURI (stav služby nabízených oznámení Windows: 410 Gone).|Žádné dimenze|
|outgoing.wns.throttled|Wns – omezená oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože služby nabízených oznámení Windows omezuje tuto aplikaci (stav služby nabízených oznámení Windows: 406 Nepřijatelný).|Žádné dimenze|
|outgoing.wns.tokenproviderunreachable|Wns – chyby autorizace (nedostupné)|Count|Celkem|Windows Live není dostupný.|Žádné dimenze|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Count|Celkem|Token se poskytl službě nabízených oznámení Windows není platný (stav služby nabízených oznámení Windows: 401 Neautorizováno).|Žádné dimenze|
|outgoing.wns.wrongtoken|Wns – chyby autorizace (chybný Token)|Count|Celkem|Token se poskytl službě nabízených oznámení Windows je platný, ale pro jinou aplikaci (stav služby nabízených oznámení Windows: 403 Zakázáno). To může nastat, pokud je parametr ChannelURI v registraci přidružený k jiné aplikaci. Zkontrolujte, že klientská aplikace souvisí s stejnou aplikaci, jehož přihlašovací údaje jsou v centru oznámení.|Žádné dimenze|
|outgoing.wns.invalidnotificationformat|Wns – neplatný formát oznámení|Count|Celkem|Formát oznámení není platný (stav služby nabízených oznámení Windows: 400). Všimněte si, že služby nabízených oznámení Windows není odmítnout všechny neplatné datové části.|Žádné dimenze|
|outgoing.wns.invalidnotificationsize|Wns – Chyba neplatné velikosti oznámení|Count|Celkem|Datová část oznámení je moc velká (stav služby nabízených oznámení Windows: 413).|Žádné dimenze|
|outgoing.wns.channelthrottled|Wns – omezený kanál|Count|Celkem|Oznámení byla zahozena, protože se omezuje parametr ChannelURI v registraci (hlavička odpovědi služby nabízených oznámení Windows: X-WNS-NotificationStatus:channelThrottled).|Žádné dimenze|
|outgoing.wns.channeldisconnected|Wns – odpojený kanál|Count|Celkem|Oznámení byla zahozena, protože se omezuje parametr ChannelURI v registraci (hlavička odpovědi služby nabízených oznámení Windows: X-WNS-DeviceConnectionStatus: odpojení).|Žádné dimenze|
|outgoing.wns.dropped|Wns – vynechaná oznámení|Count|Celkem|Oznámení byla zahozena, protože se omezuje parametr ChannelURI v registraci (X-WNS-NotificationStatus: dropped, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|outgoing.wns.pnserror|Wns – chyby|Count|Celkem|Oznámení se nedoručilo kvůli chybám při komunikaci se službou WNS.|Žádné dimenze|
|outgoing.wns.authenticationerror|Wns – chyby ověřování|Count|Celkem|Oznámení se nedoručilo kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje, nebo chybnému tokenu.|Žádné dimenze|
|outgoing.apns.success|APNS – úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení.|Žádné dimenze|
|outgoing.apns.invalidcredentials|Chyby autorizace APNS|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje nebo přihlašovací údaje jsou zablokované.|Žádné dimenze|
|outgoing.apns.badchannel|APNS Bad Channel Error|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože token je neplatný (kód stavu APNS: 8).|Žádné dimenze|
|outgoing.apns.expiredchannel|APNS kanál – chyba vypršení platnosti|Count|Celkem|Počet tokenů, které byly zneplatněny kanál zpětné vazby APNS.|Žádné dimenze|
|outgoing.apns.invalidnotificationsize|APNS Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože datová část byla moc velká (kód stavu APNS: 7).|Žádné dimenze|
|outgoing.apns.pnserror|APNS – chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádné dimenze|
|outgoing.gcm.success|Gcm – úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení.|Žádné dimenze|
|outgoing.gcm.invalidcredentials|Gcm – chyby autorizace (Neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje nebo přihlašovací údaje jsou zablokované.|Žádné dimenze|
|outgoing.gcm.badchannel|Chyba špatného kanálu GCM|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože registrationId v registraci nerozpoznal (výsledek GCM: Neplatná registrace).|Žádné dimenze|
|outgoing.gcm.expiredchannel|Gcm – Chyba kanálu vypršení časového limitu|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože byl vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádné dimenze|
|outgoing.gcm.throttled|Gcm – omezená oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože služba GCM omezila tuto aplikaci (kód stavu GCM: 501 – 599 nebo výsledek: není k dispozici).|Žádné dimenze|
|outgoing.gcm.invalidnotificationformat|Gcm – neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože datová část nemá správný formát (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádné dimenze|
|outgoing.gcm.invalidnotificationsize|Gcm – Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože datová část byla moc velká (výsledek GCM: MessageTooBig).|Žádné dimenze|
|outgoing.gcm.wrongchannel|Chyba nesprávného kanálu GCM|Count|Celkem|Počet nabídek, které se nezdařila, protože registrationId v registraci není přidružené k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádné dimenze|
|outgoing.gcm.pnserror|Gcm – chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM.|Žádné dimenze|
|outgoing.gcm.authenticationerror|Gcm – chyby ověřování|Count|Celkem|Počet nabídek, které se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje přihlašovací údaje jsou blokovány, nebo v aplikaci není správně nakonfigurované SenderId (výsledek GCM: MismatchedSenderId).|Žádné dimenze|
|outgoing.mpns.success|Mpns – úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení.|Žádné dimenze|
|outgoing.mpns.invalidcredentials|Mpns – neplatné přihlašovací údaje|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje nebo přihlašovací údaje jsou zablokované.|Žádné dimenze|
|outgoing.mpns.badchannel|Chyba mpns – špatný kanál|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci nerozpoznal (stav MPNS: 404 Nenalezeno).|Žádné dimenze|
|outgoing.mpns.throttled|Mpns – omezená oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 Nepřijatelný).|Žádné dimenze|
|outgoing.mpns.invalidnotificationformat|Mpns – neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože datová část oznámení byla moc velká.|Žádné dimenze|
|outgoing.mpns.channeldisconnected|Mpns – odpojený kanál|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože parametr ChannelURI v registraci byl odpojen. (stav MPNS: 412 Nenalezeno).|Žádné dimenze|
|outgoing.mpns.dropped|Mpns – vynechaná oznámení|Count|Celkem|Počet nabídek, které byly zahozeny MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo Potlačené).|Žádné dimenze|
|outgoing.mpns.pnserror|Mpns – chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS.|Žádné dimenze|
|outgoing.mpns.authenticationerror|Mpns – chyby ověřování|Count|Celkem|Počet nabídek, které nebyly se nezdařila, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje nebo přihlašovací údaje jsou zablokované.|Žádné dimenze|
|notificationhub.pushes|Všechna odchozí oznámení|Count|Celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|incoming.all.requests|Všechny příchozí požadavky|Count|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádné dimenze|
|Incoming.all.failedrequests|Všechny neúspěšné příchozí požadavky|Count|Celkem|Celkový počet neúspěšných příchozích požadavků pro Centrum oznámení|Žádné dimenze|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_ % volných uzlů Inode|% Volných uzlů Inode|Count|Average|Average_ % volných uzlů Inode|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|% Volného místa|Count|Average|Average_ % volného místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % použitých uzlů Inode|% Použitých uzlů Inode|Count|Average|Average_ % použitých uzlů Inode|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % využitého místa|% Využitého místa|Count|Average|Average_ % využitého místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené bajty/s|Bajty čtení z disku/s|Count|Average|Average_Disk přečtené bajty/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené strany/s|Čtení disku/s|Count|Average|Average_Disk přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Přenosy disku/s|Count|Average|Average_Disk přenosy/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané bajty/s|Bajty zapisování na disk/s|Count|Average|Average_Disk zapsané bajty/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané strany/s|Zápis disku/s|Count|Average|Average_Disk zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free v megabajtech|Volné megabajty|Count|Average|Average_Free v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Logical bajtů disku/s|Bajtů logického disku/s|Count|Average|Average_Logical bajtů disku/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Dostupná paměť v % Average_|Dostupná paměť v %|Count|Average|Dostupná paměť v % Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Count|Average|Average_ % dostupného odkládacího prostoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % využité paměti|% Využité paměti|Count|Average|Average_ % využité paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Využitý prostor záměny v Average_ %|Využitý prostor záměny v %|Count|Average|Využitý prostor záměny v Average_ %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available paměť v MB|Dostupná paměť v MB|Count|Average|Average_Available paměť v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available prostor záměny v MB|Dostupný prostor záměny v MB|Count|Average|Average_Available prostor záměny v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page přečtené strany/s|Čtení stránek/s|Count|Average|Average_Page přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page zapsané strany/s|Zápisy stránek/s|Count|Average|Average_Page zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pages za sekundu|Stránky/s|Count|Average|Average_Pages za sekundu|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used prostor záměny v MB|Použitý odkládací prostor v MB místa|Count|Average|Average_Used prostor záměny v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used paměť v MB|Použitá paměť v MB|Count|Average|Average_Used paměť v MB|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Odeslané bajty Average_Total|Celkový počet bajtů přenesených|Count|Average|Odeslané bajty Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Count|Average|Přijaté bajty Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total bajtů|Celkový počet bajtů|Count|Average|Average_Total bajtů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total pakety odesílané informace|Celkový počet paketů odesílané informace|Count|Average|Average_Total pakety odesílané informace|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total obdržených paketů|Celkový počet přijatých paketů|Count|Average|Average_Total obdržených paketů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby příjmu Average_Total|Chyby celkem příjmu|Count|Average|Chyby příjmu Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby odeslání Average_Total|Celkový počet odesílání chyb|Count|Average|Chyby odeslání Average_Total|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Count|Average|Average_Total kolizí|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Average|Average_Avg. Doba disku/čtení|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední Doba disku/přenos|Count|Average|Average_Avg. Doba disku/přenos|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Average|Average_Avg. Doby disku/zápis|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Physical bajtů disku/s|Bajtů fyzického disku/s|Count|Average|Average_Physical bajtů disku/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct privilegovaného času|Procento privilegovaného času|Count|Average|Average_Pct privilegovaného času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct uživatelského času|Procento uživatelského času|Count|Average|Average_Pct uživatelského času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|KB paměti Average_Used|Využité paměti kB|Count|Average|KB paměti Average_Used|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Virtual sdílené paměti|Sdílené virtuální paměti|Count|Average|Average_Virtual sdílené paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času DPC Average_|Čas DPC v %|Count|Average|% Času DPC Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ čas nečinnosti v %|Čas nečinnosti v %|Count|Average|Average_ čas nečinnosti v %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času přerušení Average_|Čas přerušení v %|Count|Average|% Času přerušení Average_|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Doba čekání na vstupně-výstupních operací Average_ %|Doba čekání % vstupně-výstupních operací|Count|Average|Doba čekání na vstupně-výstupních operací Average_ %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ dobrý čas v %|Dobrý čas v %|Count|Average|Average_ dobrý čas v %|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % privilegovaného času|% Privilegovaného času|Count|Average|Average_ % privilegovaného času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|% Času procesoru|Count|Average|Average_ % času procesoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % uživatelského času|Uživatelský čas v %|Count|Average|Average_ % uživatelského času|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free fyzické paměti|Volná fyzická paměť|Count|Average|Average_Free fyzické paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free místo ve stránkovacích souborech|Volné místo ve stránkovacích souborech|Count|Average|Average_Free místo ve stránkovacích souborech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free virtuální paměti|Volná virtuální paměť|Count|Average|Average_Free virtuální paměti|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Processes|Procesy|Count|Average|Average_Processes|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Size uložená v stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Count|Average|Average_Size uložená v stránkovacích souborech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Uptime|Doba provozu|Count|Average|Average_Uptime|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Users|Uživatelé|Count|Average|Average_Users|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední Doba disku/čtení|Count|Average|Average_Avg. Doba disku/čtení|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doby disku/zápis|Střední Doby disku/zápis|Count|Average|Average_Avg. Doby disku/zápis|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Count|Average|Délka fronty disku Average_Current|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtené strany/s|Čtení disku/s|Count|Average|Average_Disk přečtené strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Přenosy disku/s|Count|Average|Average_Disk přenosy/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zapsané strany/s|Zápis disku/s|Count|Average|Average_Disk zapsané strany/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free v megabajtech|Volné megabajty|Count|Average|Average_Free v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|% Volného místa|Count|Average|Average_ % volného místa|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available paměť v megabajtech|Počet MB k dispozici|Count|Average|Average_Available paměť v megabajtech|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % potvrzených bajtů|% Využití potvrzených bajtů|Count|Average|Average_ % potvrzených bajtů|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Přijaté Average_Bytes/s|Přijaté bajty/s|Count|Average|Přijaté Average_Bytes/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Odeslané Average_Bytes/s|Odeslané bajty/s|Count|Average|Odeslané Average_Bytes/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Count|Average|Average_Bytes celkem/s|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|% Času procesoru|Count|Average|Average_ % času procesoru|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Count|Average|Délka fronty Average_Processor|Počítače, název_objektu, InstanceName, Cesta_k_čítači, SourceSystem|
|Prezenční signál|Prezenční signál|Count|Celkem|Prezenční signál|Počítače, OSType, verze, SourceComputerId|
|Aktualizace|Aktualizace|Count|Average|Aktualizace|Počítače, produktů, klasifikace, UpdateState volitelné, schválené|
|Událost|Událost|Count|Average|Událost|Zdroj protokolu událostí, počítače, EventCategory, EventLevel, EventLevelName, ID události|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekundy|Average|Doba trvání dotazu jazyka DAX v posledního intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Count|Average|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|Vysoké využití QPU|Count|Celkem|Vysoké využití QPU za poslední minutu, 1 pro využití vysoké QPU, jinak 0|Žádné dimenze|
|memory_metric|Memory (Paměť)|B|Average|Paměť. Rozsah 0 – 3 GB pro A1, 0 – 5 GB pro A2, A3 0 až 10 GB, 0-25 GB pro A4, 0 – 50 GB pro A5 a 0 – 100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Percent|Average|Průměrný thrashing paměti.|Žádné dimenze|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections – úspěch|ListenerConnections – úspěch|Count|Celkem|Úspěšná připojení ListenerConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Celkem|ClientError v ListenerConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Celkem|ServerError na ListenerConnections pro Microsoft.Relay|EntityName|
|SenderConnections – úspěch|SenderConnections – úspěch|Count|Celkem|Úspěšná připojení SenderConnections pro Microsoft.Relay|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Celkem|ClientError v SenderConnections pro Microsoft.Relay|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Celkem|ServerError na SenderConnections pro Microsoft.Relay|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Celkem|ListenerConnections celkem pro Microsoft.Relay|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Celkem|Celkem žádostí SenderConnections pro Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Celkem|ActiveConnections celkem pro Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Celkem|ActiveListeners celkem pro Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Celkem|BytesTransferred celkem pro Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Celkem|ListenerDisconnects celkem pro Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Celkem|SenderDisconnects celkem pro Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence vyhledávání|Sekundy|Average|Hledání Průměrná latence pro službu search|Žádné dimenze|
|SearchQueriesPerSecond|Vyhledávací dotazy za sekundu|CountPerSecond|Average|Vyhledávací dotazy za sekundu pro vyhledávací službu|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Percent|Average|Procento vyhledávacích dotazů, které byly omezené služby search|Žádné dimenze|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů (Preview)|EntityName|
|ServerErrors|Chyby serveru. (Preview)|Count|Celkem|Chyby serveru pro Microsoft.ServiceBus (Preview)|EntityName|
|UserErrors|Chyby uživatele. (Preview)|Count|Celkem|Chyby uživatele pro Microsoft.ServiceBus (Preview)|EntityName|
|ThrottledRequests|Omezené požadavky. (Preview)|Count|Celkem|Omezené požadavky pro Microsoft.ServiceBus (Preview)|EntityName|
|IncomingRequests|Příchozí žádosti (Preview)|Count|Celkem|Příchozí požadavky pro Microsoft.ServiceBus (Preview)|EntityName|
|IncomingMessages|Příchozí zprávy (Preview)|Count|Celkem|Příchozí zprávy pro Microsoft.ServiceBus (Preview)|EntityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Count|Celkem|Odchozí zprávy pro Microsoft.ServiceBus (Preview)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Count|Celkem|Celkový počet aktivních připojení pro Microsoft.ServiceBus (Preview)|Žádné dimenze|
|Size|Velikost (Preview)|B|Average|Velikost fronty nebo tématu v bajtech. (Preview)|EntityName|
|Zprávy|Počet zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet zpráv ve frontě nebo tématu. (Preview)|EntityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|EntityName|
|DeadletteredMessages|Počet dead lettered zpráv ve frontě nebo tématu. (Preview)|Count|Average|Počet dead lettered zpráv ve frontě nebo tématu. (Preview)|EntityName|
|ScheduledMessages|Počet naplánovaných zprávy ve frontě nebo tématu. (Preview)|Count|Average|Počet naplánovaných zprávy ve frontě nebo tématu. (Preview)|EntityName|
|CPUXNS|Využití CPU na obor názvů|Percent|Maximum|Metrika využití pro procesoru v oboru názvů služby Service bus úrovně premium|Žádné dimenze|
|WSXNS|Využití paměti na obor názvů|Percent|Maximum|Metrika využití paměti obor názvů premium služby Service bus|Žádné dimenze|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Procesor přidělené do tohoto kontejneru v jednotkách millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|B|Average|Paměť přidělená pro tento kontejner v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Skutečné využití procesoru v jednotkách millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|B|Average|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Average|Využití procesoru pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Average|Využití procesoru pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Stav aplikace Service Fabric mřížky|ApplicationName, stav|
|ServiceStatus|ServiceStatus|Count|Average|Stav služby aplikace Service Fabric mřížky|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Stav služby repliky aplikace Service Fabric mřížky|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Stav kontejneru v aplikaci Service Fabric mřížky|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Average|Restartovat počet kontejneru v aplikaci Service Fabric mřížky|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Count|Maximum|Počet připojení uživatelů.|Koncový bod|
|MessageCount|Počet zpráv|Count|Celkem|Celkový počet zpráv.|Žádné dimenze|
|InboundTraffic|Příchozí provoz|B|Celkem|Příchozí provoz služby|Žádné dimenze|
|OutboundTraffic|Odchozí provoz|B|Celkem|Odchozí provoz služby|Žádné dimenze|
|UserErrors|Chyby uživatele|Percent|Maximum|Procento chyb uživatele|Žádné dimenze|
|SystemErrors|Chyby systému|Percent|Maximum|Procento chyb systému|Žádné dimenze|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Percent|Average|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procento v/v protokolu|Percent|Average|Procento v/v protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Average|Procento DTU|Žádné dimenze|
|úložiště|Využité dat|B|Maximum|Celkovou velikost databáze|Žádné dimenze|
|connection_successful|Úspěšná připojení|Count|Celkem|Úspěšná připojení|Žádné dimenze|
|connection_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádné dimenze|
|blocked_by_firewall|Blokovaná bránou Firewall|Count|Celkem|Blokovaná bránou Firewall|Žádné dimenze|
|Zablokování|Zablokování|Count|Celkem|Zablokování|Žádné dimenze|
|storage_percent|Procento využité dat|Percent|Maximum|Procento velikosti databáze|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Percent|Average|Procento úložiště OLTP v paměti|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Percent|Average|Procento pracovních procesů|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Average|Procento relací|Žádné dimenze|
|dtu_limit|Omezení jednotek DTU|Count|Average|Omezení jednotek DTU|Žádné dimenze|
|dtu_used|DTU použít|Count|Average|DTU použít|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Average|Limit procesoru|Žádné dimenze|
|cpu_used|Využití procesoru|Count|Average|Využití procesoru|Žádné dimenze|
|dwu_limit|Limit jednotky|Count|Maximum|Limit jednotky|Žádné dimenze|
|dwu_consumption_percent|Procento DWU|Percent|Maximum|Procento DWU|Žádné dimenze|
|dwu_used|Použít DWU|Count|Maximum|Použít DWU|Žádné dimenze|
|dw_cpu_percent|Úrovni uzlu DW procento využití procesoru|Percent|Average|Úrovni uzlu DW procento využití procesoru|DwLogicalNodeId|
|dw_physical_data_read_percent|Procento datových v/v úrovně uzlu DW|Percent|Average|Procento datových v/v úrovně uzlu DW|DwLogicalNodeId|
    |cache_hit_percent|Procenta přístupů do mezipaměti|Percent|Maximum|Procenta přístupů do mezipaměti|Žádné dimenze|
|cache_used_percent|Procento použitá mezipaměť|Percent|Maximum|Procento použitá mezipaměť|Žádné dimenze|
|local_tempdb_usage_percent|Procento místní databáze tempdb|Percent|Average|Procento místní databáze tempdb|Žádné dimenze|
|app_cpu_billed|Aplikace účtují procesoru|Count|Celkem|Aplikace účtují procesoru|Žádné dimenze|
|app_cpu_percent|Procento využití procesoru aplikace|Percent|Average|Procento využití procesoru aplikace|Žádné dimenze|
|app_memory_percent|Procento využité paměti aplikace|Percent|Average|Procento využité paměti aplikace|Žádné dimenze|
|allocated_data_storage|Přidělené místo na data|B|Average|Přidělené místo na data|Žádné dimenze|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Average|Procento CPU|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Percent|Average|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procento v/v protokolu|Percent|Average|Procento v/v protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Percent|Average|Procento DTU|Žádné dimenze|
|storage_percent|Procento využité dat||Percent|Average|Procento úložiště|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Percent|Average|Procento pracovních procesů|Žádné dimenze|
|sessions_percent|Procento relací|Percent|Average|Procento relací|Žádné dimenze|
|eDTU_limit|omezení eDTU|Count|Average|omezení eDTU|Žádné dimenze|
|storage_limit|Maximální velikost dat|B|Average|Limit úložiště.|Žádné dimenze|
|eDTU_used|použít eDTU|Count|Average|použít eDTU|Žádné dimenze|
|storage_used|Využité dat|B|Average|Využité úložiště|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Percent|Average|Procento úložiště OLTP v paměti|Žádné dimenze|
|cpu_limit|Limit procesoru|Count|Average|Limit procesoru|Žádné dimenze|
|cpu_used|Využití procesoru|Count|Average|Využití procesoru|Žádné dimenze|
|allocated_data_storage|Přidělené místo na data|B|Average|Přidělené místo na data|Žádné dimenze|
|allocated_data_storage_percent|Procento přidělené místo na data|Percent|Maximum|Procento přidělené místo na data|Žádné dimenze|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Count|Average|Počet virtuálních jader|Žádné dimenze|
|avg_cpu_percent|Průměrné procento CPU|Percent|Average|Průměrné procento CPU|Žádné dimenze|
|reserved_storage_mb|Vyhrazený úložný prostor|Count|Average|Vyhrazený úložný prostor|Žádné dimenze|
|storage_space_used_mb|Prostor úložiště využitý|Count|Average|Prostor úložiště využitý|Žádné dimenze|
|io_requests|Počet žádostí v/v úrovně|Count|Average|Počet žádostí v/v úrovně|Žádné dimenze|
|io_bytes_read|Přečtené bajty vstupně-výstupních operací|B|Average|Přečtené bajty vstupně-výstupních operací|Žádné dimenze|
|io_bytes_written|Zapsané bajty vstupně-výstupních operací|B|Average|Zapsané bajty vstupně-výstupních operací|Žádné dimenze|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Použitá kapacita|B|Průměr|Kapacita využitá účtem|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita služby Blob|B|Average|Velikost úložiště využitá službou Blob Service účtu úložiště v bajtech|BlobType, Tier|
|BlobCount|Počet objektů blob|Počet|Celkem|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType|       |BlobCount|Počet objektů blob|Count|Average|Počet objektů blob ve službě Blob Service účtu úložiště|BlobType, Tier|
|ContainerCount|Počet kontejnerů služby Blob|Počet|Průměr|Počet kontejnerů ve službě Blob Service účtu úložiště|Žádné dimenze|
|IndexCapacity|Index kapacity|B|Average|Velikost úložiště využitá službou ADLS Gen2 (hierarchické) Index v bajtech.|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FileCapacity|Kapacita služby File|B|Average|Velikost úložiště využitá službou File účtu úložiště v bajtech.|Žádné dimenze|
|FileCount|Počet souborů|Count|Average|Počet souborů ve službě File účtu úložiště.|Žádné dimenze|
|FileShareCount|Počet sdílení souborů|Count|Average|Počet sdílených složek ve službě File účtu úložiště.|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a požadavky, které došlo k chybě. Hodnota ResponseType dimenzi používejte pro počet různých typů odpovědi.|Hodnota ResponseType, GeoType ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
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
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
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
|Příchozí přenos dat|Příchozí přenos dat|B|Celkem|Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType ApiName, ověřování|
|Výchozí přenos|Výchozí přenos|B|Celkem|Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje síťovou latenci určenou v AverageE2ELatency.|GeoType ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že hodnota TotalBillableRequests vydělí počtem příslušných požadavků, včetně těch, ke které došlo k neočekávané chybě. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API.|GeoType ApiName, ověřování|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Výsledek relace synchronizace|Count|Average|Metriky, protokoly a hodnotu 1 každý času koncový bod serveru byl úspěšně dokončí relaci synchronizace s koncový bod cloudu.|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizovat soubory|Count|Celkem|Počet souborů synchronizované|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory nesynchronizuje|Count|Celkem|Počet souborů, které se nepovedlo synchronizovat|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bajty synchronizované|B|Celkem|Celková velikost souborů byl přenesen v případě relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Count|Maximum|Metriky, protokoly a hodnotu 1 každý času registrovaný server byl úspěšně záznamy prezenčního signálu se koncový bod cloudu.|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odvolání vrstvení cloudu|B|Celkem|Celková velikost dat připomenout serverem|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|% využití SU|Percent|Maximum|% využití SU|LogicalName, PartitionId|
|Situací|Vstupní události|Count|Celkem|Vstupní události|LogicalName, PartitionId|
|InputEventBytes|Bajty vstupních událostí|B|Celkem|Bajty vstupních událostí|LogicalName, PartitionId|
|LateInputEvents|Pozdní vstupní události|Count|Celkem|Pozdní vstupní události|LogicalName, PartitionId|
|Výstupní události|Výstupní události|Count|Celkem|Výstupní události|LogicalName, PartitionId|
|ConversionErrors|Chyby převodu dat|Count|Celkem|Chyby převodu dat|LogicalName, PartitionId|
|Chyby|Chyby za běhu|Count|Celkem|Chyby za běhu|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Count|Celkem|Události mimo pořadí|LogicalName, PartitionId|
|AMLCalloutRequests|Požadavky na funkce|Count|Celkem|Požadavky na funkce|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Nezdařené požadavky na funkce|Count|Celkem|Nezdařené požadavky na funkce|LogicalName, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Count|Celkem|Události funkcí|LogicalName, PartitionId|
|DeserializationError|Chyby deserializace vstupu|Count|Celkem|Chyby deserializace vstupu|LogicalName, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Count|Celkem|Události předčasného vstupu|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Vstupní události v backlogu|Count|Maximum|Vstupní události v backlogu|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Count|Celkem|Přijaté vstupní zdroje|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí přenos dat přijatých zpráv|Count|Celkem|Počet zpráv, které číst ze všech centra událostí nebo službu IoT hub zdroje událostí|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijatých neplatné zprávy|Count|Celkem|Počet zpráv neplatné čtení z všechna centra událostí nebo službu IoT hub zdroje událostí|Žádné dimenze|
|IngressReceivedBytes|Příchozí přenos dat přijatých bajtů|B|Celkem|Počet bajtů načtených ze všech zdrojů událostí|Žádné dimenze|
|IngressStoredBytes|Příchozí přenos dat uložené bajtů|B|Celkem|Celková velikost událostí úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressStoredEvents|Příchozí přenos dat uložených událostí|Count|Celkem|Počet událostí plochá úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Příchozí přenos dat přijatých zpráv časový interval|Sekundy|Maximum|Rozdíl mezi časem, který je zpráva zařazených do fronty ve zdroji událostí a čas, kdy se zpracovávají v příchozího přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počet přijatých zpráv příchozího přenosu dat|Count|Average|Rozdíl mezi pořadové číslo poslední zprávy ve frontě událostí zdroje oddílu a pořadovým číslem zprávy jsou zpracovávány v příchozího přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností horké úložiště|Count|Maximum|Maximální počet vlastností používá prostředí povolenou pro skladovou Položku S1, S2 a maximální počet vlastností záložním Store povolenou pro skladovou Položku průběžné platby|Žádné dimenze|
|WarmStorageUsedProperties|Horké úložiště použít vlastnosti |Count|Maximum|Počet vlastnosti používané prostředím pro skladovou Položku S1, S2 a počet vlastnosti používané ve Store záložním pro skladovou Položku průběžné platby|Žádné dimenze|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí přenos dat přijatých zpráv|Count|Celkem|Počet zpráv, které číst ze zdroje události|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijatých neplatné zprávy|Count|Celkem|Počet zpráv neplatná číst ze zdroje události|Žádné dimenze|
|IngressReceivedBytes|Příchozí přenos dat přijatých bajtů|B|Celkem|Počet bajtů načtených ze zdroje události|Žádné dimenze|
|IngressStoredBytes|Příchozí přenos dat uložené bajtů|B|Celkem|Celková velikost událostí úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressStoredEvents|Příchozí přenos dat uložených událostí|Count|Celkem|Počet událostí plochá úspěšně zpracovaná a k dispozici pro dotaz|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Příchozí přenos dat přijatých zpráv časový interval|Sekundy|Maximum|Rozdíl mezi časem, který je zpráva zařazených do fronty ve zdroji událostí a čas, kdy se zpracovávají v příchozího přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počet přijatých zpráv příchozího přenosu dat|Count|Average|Rozdíl mezi pořadové číslo poslední zprávy ve frontě událostí zdroje oddílu a pořadovým číslem zprávy jsou zpracovávány v příchozího přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností horké úložiště|Count|Maximum|Maximální počet vlastností používá prostředí povolenou pro skladovou Položku S1, S2 a maximální počet vlastností záložním Store povolenou pro skladovou Položku průběžné platby|Žádné dimenze|
|WarmStorageUsedProperties|Horké úložiště použít vlastnosti |Count|Maximum|Počet vlastnosti používané prostředím pro skladovou Položku S1, S2 a počet vlastnosti používané ve Store záložním pro skladovou Položku průběžné platby|Žádné dimenze|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Čtení disku bajtů/s|BytesPerSecond|Average|Průměrná propustnost z důvodu operace čtení za období vzorku.|Žádné dimenze|
|DiskWriteBytesPerSecond|Zápis disku bajtů/s|BytesPerSecond|Average|Průměrná propustnost kvůli operací zápisu po dobu vzorku.|Žádné dimenze|
|Bajty čtení disku|Bajty čtení disku|B|Celkem|Celková propustnost disku z důvodu operace čtení za období vzorku.|Žádné dimenze|
|Bajty zápisu disku|Bajty zápisu disku|B|Celkem|Celková propustnost disku z důvodu operací zápisu po dobu vzorku.|Žádné dimenze|
|DiskReadOperations|Operace čtení disku|Count|Celkem|Počet vstupně-výstupní operace čtení v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|DiskWriteOperations|Operací zápisu na disk|Count|Celkem|Počet vstupně-výstupní operace zápisu v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|Čtení z disku operace/s|Čtení z disku operace/s|CountPerSecond|Average|Průměrný počet vstupně-výstupní operace čtení v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|Operace zápisu disku/s|Operace zápisu disku/s|CountPerSecond|Average|Průměrný počet vstupně-výstupní operace zápisu v předchozím období vzorku. Všimněte si, že tyto operace může být proměnné velikosti.|Žádné dimenze|
|DiskReadLatency|Latence čtení disku|Milisekundy|Average|Celková latence čtení. Latence pro čtení součet zařízení a jádra.|Žádné dimenze|
|DiskWriteLatency|Latence zápisu u disku|Milisekundy|Average|Latence zápisu celkový počet. Latence zápisu součet zařízení a jádra.|Žádné dimenze|
|NetworkInBytesPerSecond|Síť v bajty/s|BytesPerSecond|Average|Propustnost sítě průměrné pro přijaté přenosy.|Žádné dimenze|
|NetworkOutBytesPerSecond|Síť si bajty/s|BytesPerSecond|Average|Propustnost sítě průměrné pro přenášená provoz.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|B|Celkem|Propustnost sítě celkový počet přijatých provozu.|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|B|Celkem|Propustnost sítě celkový počet přenesených provozu.|Žádné dimenze|
|MemoryUsed|Využité paměti|B|Average|Množství paměti počítače, který se používá ve virtuálním počítači.|Žádné dimenze|
|MemoryGranted|Paměť udělena|B|Average|Velikost paměti, která byla udělena k virtuálnímu počítači podle hostitele. Paměť nebylo uděleno k hostiteli, až ji je dotčená jednou uděleného může paměti výměnu nebo ballooned okamžitě, pokud je nutné VMkernel paměť.|Žádné dimenze|
|MemoryActive|Aktivní v paměti|B|Average|Množství paměti používané virtuálním Počítačem v posledních malé časové okno. Toto je "true" počet velikost paměti virtuálního počítače má aktuálně potřebu. Další, nevyužité paměti může automaticky nebo ballooned bez jakéhokoli dopadu na výkon hosta.|Žádné dimenze|
|Procento CPU|Procento CPU|Procento|Average|Využití procesoru. Tato hodnota je nahlásí se 100 % představující všechny jader procesoru v systému. Jako příklad je virtuální počítač 2-způsob, jak pomocí 50 % čtyři jádra systému úplně pomocí dvě jádra.|Žádné dimenze|
|PercentageCpuReady|Procento procesoru připravené|Milisekundy|Celkem|Ready time spadá čas věnovat časový limit na CPU(s) k dispozici v posledních interval aktualizace.|Žádné dimenze|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|
|DiskQueueLength|Délka fronty disku|Count|Average|Délka fronty disku|instance|
|HttpQueueLength|Délka fronty http|Count|Average|Délka fronty http|instance|
|BytesReceived|Data v|B|Celkem|Data v|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (s výjimkou funkce)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|instance|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Data v|B|Celkem|Data v|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|Http 401|Count|Celkem|Http 401|instance|
|Http403|Http 403|Count|Celkem|Http 403|instance|
|Http404|Http 404|Count|Celkem|Http 404|instance|
|Http406|Http 406|Count|Celkem|Http 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|AppConnections|Připojení|Count|Average|Připojení|instance|
|Obslužné rutiny|Počet popisovačů|Count|Average|Počet popisovačů|instance|
|Vlákna|Počet vláken|Count|Average|Počet vláken|instance|
|PrivateBytes|Nesdílené bajty|B|Average|Nesdílené bajty|instance|
|IoReadBytesPerSecond|Přečtené bajty vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Přečtené bajty vstupně-výstupních operací za sekundu|instance|
|IoWriteBytesPerSecond|Vstupně-výstupních operací zapsané bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|Vstupně-výstupních operací ostatní bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|Operace čtení vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Operace čtení vstupně-výstupních operací za sekundu|instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|Vstupně-výstupních operací ostatní operace za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet domén aplikace byla uvolněna|Count|Average|Celkový počet domén aplikace byla uvolněna|instance|
|Gen0Collections|0\. generace uvolňování pamětí|Count|Celkem|0\. generace uvolňování pamětí|instance|
|Gen1Collections|1\. generace uvolňování pamětí|Count|Celkem|1\. generace uvolňování pamětí|instance|
|Gen2Collections|Uvolnění paměti generace 2|Count|Celkem|Uvolnění paměti generace 2|instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Data v|B|Celkem|Data v|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|MB / MS|Celkem|[Jednotky provádění funkcí](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|instance|
|PrivateBytes|Nesdílené bajty|B|Average|Nesdílené bajty|instance|
|IoReadBytesPerSecond|Přečtené bajty vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Přečtené bajty vstupně-výstupních operací za sekundu|instance|
|IoWriteBytesPerSecond|Vstupně-výstupních operací zapsané bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|Vstupně-výstupních operací ostatní bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|Operace čtení vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Operace čtení vstupně-výstupních operací za sekundu|instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|Vstupně-výstupních operací ostatní operace za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet domén aplikace byla uvolněna|Count|Average|Celkový počet domén aplikace byla uvolněna|instance|
|Gen0Collections|0\. generace uvolňování pamětí|Count|Celkem|0\. generace uvolňování pamětí|instance|
|Gen1Collections|1\. generace uvolňování pamětí|Count|Celkem|1\. generace uvolňování pamětí|instance|
|Gen2Collections|Uvolnění paměti generace 2|Count|Celkem|Uvolnění paměti generace 2|instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|instance|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Data v|B|Celkem|Data v|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|Http 401|Count|Celkem|Http 401|instance|
|Http403|Http 403|Count|Celkem|Http 403|instance|
|Http404|Http 404|Count|Celkem|Http 404|instance|
|Http406|Http 406|Count|Celkem|Http 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|instance|
|MemoryWorkingSet|Pracovní sada paměti|B|Average|Pracovní sada paměti|instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|B|Average|Průměrná pracovní sada paměti|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|Count|Celkem|Jednotky provádění funkcí|instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|instance|
|AppConnections|Připojení|Count|Average|Připojení|instance|
|Obslužné rutiny|Počet popisovačů|Count|Average|Počet popisovačů|instance|
|Vlákna|Počet vláken|Count|Average|Počet vláken|instance|
|PrivateBytes|Nesdílené bajty|B|Average|Nesdílené bajty|instance|
|IoReadBytesPerSecond|Přečtené bajty vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Přečtené bajty vstupně-výstupních operací za sekundu|instance|
|IoWriteBytesPerSecond|Vstupně-výstupních operací zapsané bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací zapsané bajty za sekundu|instance|
|IoOtherBytesPerSecond|Vstupně-výstupních operací ostatní bajty za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní bajty za sekundu|instance|
|IoReadOperationsPerSecond|Operace čtení vstupně-výstupních operací za sekundu|BytesPerSecond|Celkem|Operace čtení vstupně-výstupních operací za sekundu|instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|instance|
|IoOtherOperationsPerSecond|Vstupně-výstupních operací ostatní operace za sekundu|BytesPerSecond|Celkem|Vstupně-výstupních operací ostatní operace za sekundu|instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikace|Count|Average|Požadavky ve frontě aplikace|instance|
|CurrentAssemblies|Aktuální sestavení|Count|Average|Aktuální sestavení|instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Average|Celkový počet domén aplikace|instance|
|TotalAppDomainsUnloaded|Celkový počet domén aplikace byla uvolněna|Count|Average|Celkový počet domén aplikace byla uvolněna|instance|
|Gen0Collections|0\. generace uvolňování pamětí|Count|Celkem|0\. generace uvolňování pamětí|instance|
|Gen1Collections|1\. generace uvolňování pamětí|Count|Celkem|1\. generace uvolňování pamětí|instance|
|Gen2Collections|Uvolnění paměti generace 2|Count|Celkem|Uvolnění paměti generace 2|instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Count|Celkem|Požadavky|instance|
|BytesReceived|Data v|B|Celkem|Data v|instance|
|BytesSent|Výstupní data|B|Celkem|Výstupní data|instance|
|Http101|HTTP 101|Count|Celkem|HTTP 101|instance|
|Http2xx|Http 2xx|Count|Celkem|Http 2xx|instance|
|Http3xx|Http 3xx|Count|Celkem|Http 3xx|instance|
|Http401|Http 401|Count|Celkem|Http 401|instance|
|Http403|Http 403|Count|Celkem|Http 403|instance|
|Http404|Http 404|Count|Celkem|Http 404|instance|
|Http406|Http 406|Count|Celkem|Http 406|instance|
|Http4xx|Http 4xx|Count|Celkem|Http 4xx|instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Average|Průměrná doba odezvy|instance|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|
|DiskQueueLength|Délka fronty disku|Count|Average|Délka fronty disku|instance|
|HttpQueueLength|Délka fronty http|Count|Average|Délka fronty http|instance|
|ActiveRequests|Aktivní požadavky|Count|Celkem|Aktivní požadavky|instance|
|TotalFrontEnds|Celkový počet front-Endů|Count|Average|Celkový počet front-Endů|Žádné dimenze|
|SmallAppServicePlanInstances|Pracovní procesy plánu krátkodobého používání aplikační služby|Count|Average|Pracovní procesy plánu krátkodobého používání aplikační služby|Žádné dimenze|
|MediumAppServicePlanInstances|Pracovní procesy plánu střednědobého používání aplikační služby|Count|Average|Pracovní procesy plánu střednědobého používání aplikační služby|Žádné dimenze|
|LargeAppServicePlanInstances|Pracovní procesy plánu používání aplikační služby|Count|Average|Pracovní procesy plánu používání aplikační služby|Žádné dimenze|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Hodnoty WorkersTotal|Celkový počet pracovních procesů|Count|Average|Celkový počet pracovních procesů|Žádné dimenze|
|WorkersAvailable|Dostupné pracovní procesy|Count|Average|Dostupné pracovní procesy|Žádné dimenze|
|Workersused za|Využité pracovní procesy|Count|Average|Využité pracovní procesy|Žádné dimenze|
|CpuPercentage|Procento procesoru|Percent|Average|Procento procesoru|instance|
|MemoryPercentage|Procento paměti|Percent|Average|Procento paměti|instance|

## <a name="next-steps"></a>Další postup
* [Přečtěte si informace o metriky ve službě Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Export metrik úložiště, Centrum událostí a Log Analytics](diagnostic-logs-overview.md)
