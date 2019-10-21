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
ms.openlocfilehash: 4e2cd3b66385d37815d0fc51a70ca31aba25db29
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528613"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s Azure Monitor

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Níže je uvedený úplný seznam všech metrik, které jsou aktuálně k dispozici s kanálem metriky Azure Monitor. Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API. Tento seznam obsahuje jenom metriky, které jsou k dispozici, pomocí kanálu konsolidace Azure Monitor metriky. Pokud chcete zadat dotaz na tyto metriky a získat k nim přístup, použijte [rozhraní API verze 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) .

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|memory_metric|Paměť|Psaný|Průměr|Rezident. Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|private_bytes_metric|Soukromé bajty |Psaný|Průměr|Celková velikost paměti, kterou proces Analysis Services modulu a procesu kontejneru hybridních webových aplikací přidělil, a ne včetně paměti sdílené s jinými procesy.|ServerResourceType|
|virtual_bytes_metric|Virtuální bajty |Psaný|Průměr|Aktuální velikost virtuálního adresního prostoru, který používá procesy procesu Analysis Services Engine a hybridní aplikace.|ServerResourceType|
|TotalConnectionRequests|Požadavky na připojení celkem|Počet|Průměr|Celkový počet požadavků na připojení Jedná se o příjem.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|ServerResourceType|
|TotalConnectionFailures|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: aktuální připojení|Počet|Průměr|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: aktuální cena čisticího modulu|Počet|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: velikost čisticí paměti|Psaný|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: nezmenšovaná paměť čisticí paměti|Psaný|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|ServerResourceType|
|MemoryUsage|Paměť: využití paměti|Psaný|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Rovná se čítači Process\PrivateBytes a velikostí dat mapovaných do paměti, přičemž se ignoruje jakákoli paměť, která byla namapována nebo přidělena xVelocitym stroji pro analýzu paměti (VertiPaq), nad rámec limitu paměti stroje xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: limit paměti – pevný|Psaný|Průměr|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|Hodnota memorylimithigh|Paměť: limit paměti – vysoká|Psaný|Průměr|Horní limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: limit paměti – nízká|Psaný|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: limit paměti VertiPaq|Psaný|Průměr|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: kvóta|Psaný|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|ServerResourceType|
|QuotaBlocked|Paměť: kvóta blokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|Psaný|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq stránkované|Psaný|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: počet převedených řádků za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vlákna: zaneprázdněná vlákna fondu příkazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Vlákna: nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyThreads|Vlákna: zaneprázdněná vlákna s dlouhou analýzou|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingIdleThreads|Vlákna: nečinná vlákna při dlouhé analýze|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingJobQueueLength|Vlákna: délka fronty úloh dlouhého rozboru|Počet|Průměr|Počet úloh ve frontě fondu vláken dlouhého analýzy.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vlákna: délka fronty úloh v/v fondu zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vlákna: vlákna nečinných úloh v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vlákna: nečinná vlákna v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|ServerResourceType|
|QueryPoolIdleThreads|Vlákna: nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|ShortParsingBusyThreads|Vlákna: krátkodobá analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingIdleThreads|Vlákna: nečinná vlákna krátké analýzy|Počet|Průměr|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingJobQueueLength|Vlákna: délka fronty úlohy krátké analýzy|Počet|Průměr|Počet úloh ve frontě krátkého analýzy fondu vláken.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|ServerResourceType|
|mashup_engine_qpu_metric|QPU modulu M|Počet|Průměr|QPU využití hybridních procesů v hybridních modulech|ServerResourceType|
|mashup_engine_memory_metric|Paměť motoru M|Psaný|Průměr|Využití paměti procesy modulu hybridní webové aplikace|ServerResourceType|
|mashup_engine_private_bytes_metric|Počet privátních bajtů modulu M |Psaný|Průměr|Celkový objem paměťových procesů kontejneru hybridní webové aplikace, včetně paměti, která je sdílena s jinými procesy.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuální bajty motoru M |Psaný|Průměr|Aktuální velikost procesů kontejneru hybridního adresního prostoru virtuálního adresáře.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Počet|Celkem|Celkový počet požadavků brány v daném období. Může být rozdělené podle různých dimenzí, které vám pomohou diagnostikovat problémy. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Požadavky brány celkem|Počet|Celkem|Celkový počet požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou metriku `Requests`. |Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány|Počet|Celkem|Celkový počet úspěšných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou metriku `Requests`.|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované požadavky brány|Počet|Celkem| Celkový počet neautorizovaných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou metriku `Requests`.|Umístění, název hostitele|
|FailedRequests|Neúspěšné požadavky brány|Počet|Celkem|Celkový počet neúspěšných požadavků brány v daném období. Tato metrika je zastaralá, doporučujeme použít novou metriku `Requests`.|Umístění, název hostitele|
|OtherRequests|Další požadavky brány|Počet|Celkem|Celkový počet požadavků brány v daném období, které nespadají do kategorií úspěšných, neautorizovaných nebo neúspěšných. Tato metrika je zastaralá, doporučujeme použít novou metriku `Requests`. |Umístění, název hostitele|
|Délka|Celková doba trvání žádostí o bránu|Milisekund|Průměr|Doba mezi tím, kdy API Management obdrží požadavek od klienta a vrátí odpověď klientovi.|Umístění, název hostitele|
|Kapacita|Kapacita|Procento|Průměr|Indikátor zatížení instance API Management pro účely informování o tom, zda je třeba škálovat instanci tak, aby vyhovovala větší zátěži.|Umístění|
|EventHubTotalEvents|Celkový počet událostí EventHub|Počet|Celkem|Celkový počet událostí odeslaných do centra EventHub z API Management v daném období.|Umístění|
|EventHubSuccessfulEvents|Úspěšné události EventHub|Počet|Celkem|Celkový počet úspěšných událostí EventHub v daném období.|Umístění|
|EventHubTotalFailedEvents|Neúspěšné události EventHub|Počet|Celkem|Celkový počet neúspěšných událostí EventHub v daném období.|Umístění|
|EventHubRejectedEvents|Odmítnuté události EventHub|Počet|Celkem|Celkový počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neoprávněný) v daném období.|Umístění|
|EventHubThrottledEvents|Omezené události EventHub|Počet|Celkem|Celkový počet událostí v daném období, které mají omezené události EventHub.|Umístění|
|EventHubTimedoutEvents|Vypršel časový limit událostí EventHub.|Počet|Celkem|Celkový počet událostí EventHub, jejichž časový limit vypršel v daném období.|Umístění|
|EventHubDroppedEvents|Vyřazené události EventHub|Počet|Celkem|Celkový počet vynechaných událostí z důvodu dosažení limitu velikosti fronty v daném období.|Umístění|
|EventHubTotalBytesSent|Velikost událostí EventHub|Psaný|Celkem|Celková velikost událostí EventHub v bajtech v daném období.|Umístění|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Počet|Celkem|Celkový počet úloh|Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet spuštění nasazení aktualizací|Počet|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName, stav|
|TotalUpdateDeploymentMachineRuns|Celkový počet spuštěných počítačů nasazení aktualizace|Počet|Celkem|Celkový počet spuštěných počítačů nasazení aktualizace softwaru v běhu nasazení aktualizace softwaru|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft. Batch/batchAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Vyhrazený počet jader|Počet|Celkem|Celkový počet vyhrazených jader v účtu Batch|Žádné dimenze|
|TotalNodeCount|Počet vyhrazených uzlů|Počet|Celkem|Celkový počet vyhrazených uzlů v účtu Batch|Žádné dimenze|
|LowPriorityCoreCount|Počet jader LowPriority|Počet|Celkem|Celkový počet jader s nízkou prioritou v účtu Batch|Žádné dimenze|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Počet|Celkem|Celkový počet uzlů s nízkou prioritou v účtu Batch|Žádné dimenze|
|CreatingNodeCount|Vytváření počtu uzlů|Počet|Celkem|Počet vytvořených uzlů|Žádné dimenze|
|StartingNodeCount|Počáteční počet uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|WaitingForStartTaskNodeCount|Čekání na počet uzlů spouštěcí úlohy|Počet|Celkem|Počet uzlů, které čekají na dokončení počátečního úkolu|Žádné dimenze|
|StartTaskFailedNodeCount|Počet neúspěšných spuštění úlohy – počet uzlů|Počet|Celkem|Počet uzlů, ve kterých se spouštěcí úkol nezdařil|Žádné dimenze|
|IdleNodeCount|Počet nečinných uzlů|Počet|Celkem|Počet nečinných uzlů|Žádné dimenze|
|OfflineNodeCount|Počet uzlů v režimu offline|Počet|Celkem|Počet offline uzlů|Žádné dimenze|
|RebootingNodeCount|Restartování počtu uzlů|Počet|Celkem|Počet restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|Počet uzlů obnovování imagí|Počet|Celkem|Počet uzlů obnovování imagí|Žádné dimenze|
|RunningNodeCount|Počet spuštěných uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|LeavingPoolNodeCount|Počet ponechávání uzlů fondu|Počet|Celkem|Počet uzlů opouštících fond|Žádné dimenze|
|UnusableNodeCount|Počet nepoužitelných uzlů|Počet|Celkem|Počet nepoužitelných uzlů|Žádné dimenze|
|PreemptedNodeCount|Počet zrušených uzlů|Počet|Celkem|Počet zrušených uzlů|Žádné dimenze|
|TaskStartEvent|Události zahájení úlohy|Počet|Celkem|Celkový počet úloh, které byly spuštěny|Žádné dimenze|
|TaskCompleteEvent|Události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly dokončeny|Žádné dimenze|
|TaskFailEvent|Události neúspěšných úloh|Počet|Celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|Žádné dimenze|
|PoolCreateEvent|Vytváření fondů – události|Počet|Celkem|Celkový počet vytvořených fondů|Žádné dimenze|
|PoolResizeStartEvent|Události spuštění změny velikosti fondu|Počet|Celkem|Celkový počet změněných počtu fondů, které byly spuštěny|Žádné dimenze|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Počet|Celkem|Celkový počet změněných velikostí fondu, které byly dokončeny|Žádné dimenze|
|PoolDeleteStartEvent|Události spuštění odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly spuštěny|Žádné dimenze|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly dokončeny|Žádné dimenze|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Počet|Celkem|Celkový počet úspěšně odstraněných úloh.|Žádné dimenze|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k odstranění.|Žádné dimenze|
|JobDisableCompleteEvent|Úloha zakázat kompletní události|Počet|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|Žádné dimenze|
|JobDisableStartEvent|Úloha zakázat počáteční události|Počet|Celkem|Celkový počet úloh, které byly vyžádány k zakázání.|Žádné dimenze|
|JobStartEvent|Události spuštění úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|Žádné dimenze|
|JobTerminateCompleteEvent|Ukončit události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|Žádné dimenze|
|JobTerminateStartEvent|Události spuštění ukončení úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k ukončení.|Žádné dimenze|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Připojení klienti|Počet|Maximum||ShardId|
|totalcommandsprocessed|Celkem operací|Počet|Celkem||ShardId|
|cachehits|Přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Počet|Celkem||ShardId|
|GetCommands|Získá|Počet|Celkem||ShardId|
|setcommands|Obnovení|Počet|Celkem||ShardId|
|operationsPerSecond|Operace za sekundu|Počet|Maximum||ShardId|
|evictedkeys|Vyřazení klíčů|Počet|Celkem||ShardId|
|totalkeys|Celkem klíčů|Počet|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Počet|Celkem||ShardId|
|usedmemory|Využitá paměť|Psaný|Maximum||ShardId|
|usedmemorypercentage|Procento využité paměti|Procento|Maximum||ShardId|
|usedmemoryRss|RSS využité paměti|Psaný|Maximum||ShardId|
|serverLoad|Zatížení serveru|Procento|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Procento|Maximum||ShardId|
|cacheLatency|Mikrosekundy latence mezipaměti (Preview)|Počet|Průměr||ShardId, SampleType|
|chyby|Chyby|Počet|Maximum||ShardId, ErrorType|
|connectedclients0|Připojení klienti (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed0|Celkem operací (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|getcommands0|Načtení (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|setcommands0|Sady (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|evictedkeys0|Vyřazené klíče (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|totalkeys0|Celkem klíčů (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|expiredkeys0|Klíče vypršení platnosti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss0|RSS využité paměti (horizontálních oddílů 0)|Psaný|Maximum||Žádné dimenze|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Procento|Maximum||Žádné dimenze|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime0|PROCESOR (horizontálních oddílů 0)|Procento|Maximum||Žádné dimenze|
|connectedclients1|Připojení klienti (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed1|Celkem operací (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|getcommands1|Načtení (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|setcommands1|Sady (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|evictedkeys1|Vyřazené klíče (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|totalkeys1|Celkem klíčů (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|expiredkeys1|Klíče vypršení platnosti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss1|RSS využité paměti (horizontálních oddílů 1)|Psaný|Maximum||Žádné dimenze|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Procento|Maximum||Žádné dimenze|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime1|PROCESOR (horizontálních oddílů 1)|Procento|Maximum||Žádné dimenze|
|connectedclients2|Připojení klienti (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed2|Celkem operací (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|getcommands2|Get (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|setcommands2|Sady (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|evictedkeys2|Vyřazené klíče (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|totalkeys2|Celkem klíčů (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|expiredkeys2|Klíče vypršení platnosti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss2|RSS využité paměti (horizontálních oddílů 2)|Psaný|Maximum||Žádné dimenze|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Procento|Maximum||Žádné dimenze|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime2|PROCESOR (horizontálních oddílů 2)|Procento|Maximum||Žádné dimenze|
|connectedclients3|Připojení klienti (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed3|Celkem operací (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|getcommands3|Get (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|setcommands3|Sady (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|evictedkeys3|Vyřazené klíče (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|totalkeys3|Celkem klíčů (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|expiredkeys3|Klíče vypršení platnosti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss3|RSS využité paměti (horizontálních oddílů 3)|Psaný|Maximum||Žádné dimenze|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Procento|Maximum||Žádné dimenze|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime3|PROCESOR (horizontálních oddílů 3)|Procento|Maximum||Žádné dimenze|
|connectedclients4|Připojení klienti (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed4|Celkem operací (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|getcommands4|Get (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|setcommands4|Sady (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|evictedkeys4|Vyřazené klíče (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|totalkeys4|Celkem klíčů (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|expiredkeys4|Klíče vypršení platnosti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss4|RSS využité paměti (horizontálních oddílů 4)|Psaný|Maximum||Žádné dimenze|
|serverLoad4|Zatížení serveru (horizontálních oddílů 4)|Procento|Maximum||Žádné dimenze|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime4|PROCESOR (horizontálních oddílů 4)|Procento|Maximum||Žádné dimenze|
|connectedclients5|Připojení klienti (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed5|Celkem operací (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|cachehits5|Přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|getcommands5|Získá (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|setcommands5|Sady (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond5|Operací za sekundu (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|evictedkeys5|Vyřazení klíčů (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|totalkeys5|Celkem klíčů (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|expiredkeys5|Klíče vypršení platnosti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|usedmemory5|Využitá paměť (horizontálních oddílů 5)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss5|RSS využité paměti (horizontálních oddílů 5)|Psaný|Maximum||Žádné dimenze|
|serverLoad5|Zatížení serveru (horizontálních oddílů 5)|Procento|Maximum||Žádné dimenze|
|cacheWrite5|Zápis do mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead5|Čtení z mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime5|PROCESOR (horizontálních oddílů 5)|Procento|Maximum||Žádné dimenze|
|connectedclients6|Připojení klienti (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed6|Celkem operací (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|cachehits6|Přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|getcommands6|Načtení (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|setcommands6|Sady (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond6|Operací za sekundu (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|evictedkeys6|Vyřazené klíče (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|totalkeys6|Celkem klíčů (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|expiredkeys6|Klíče vypršení platnosti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|usedmemory6|Využitá paměť (horizontálních oddílů 6)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss6|RSS využité paměti (horizontálních oddílů 6)|Psaný|Maximum||Žádné dimenze|
|serverLoad6|Zatížení serveru (horizontálních oddílů 6)|Procento|Maximum||Žádné dimenze|
|cacheWrite6|Zápis do mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead6|Čtení z mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime6|PROCESOR (horizontálních oddílů 6)|Procento|Maximum||Žádné dimenze|
|connectedclients7|Připojení klienti (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed7|Celkem operací (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|cachehits7|Přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|getcommands7|Načtení (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|setcommands7|Sady (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond7|Operací za sekundu (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|evictedkeys7|Vyřazení klíčů (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|totalkeys7|Celkem klíčů (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|expiredkeys7|Klíče vypršení platnosti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|usedmemory7|Využitá paměť (horizontálních oddílů 7)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss7|RSS využité paměti (horizontálních oddílů 7)|Psaný|Maximum||Žádné dimenze|
|serverLoad7|Zatížení serveru (horizontálních oddílů 7)|Procento|Maximum||Žádné dimenze|
|cacheWrite7|Zápis do mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead7|Čtení z mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime7|PROCESOR (horizontálních oddílů 7)|Procento|Maximum||Žádné dimenze|
|connectedclients8|Připojení klienti (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed8|Celkem operací (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|cachehits8|Přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|getcommands8|Získá (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|setcommands8|Sady (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond8|Operací za sekundu (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|evictedkeys8|Vyřazené klíče (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|totalkeys8|Celkem klíčů (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|expiredkeys8|Klíče vypršení platnosti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|usedmemory8|Využitá paměť (horizontálních oddílů 8)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss8|RSS využité paměti (horizontálních oddílů 8)|Psaný|Maximum||Žádné dimenze|
|serverLoad8|Zatížení serveru (horizontálních oddílů 8)|Procento|Maximum||Žádné dimenze|
|cacheWrite8|Zápis do mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead8|Čtení z mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime8|PROCESOR (horizontálních oddílů 8)|Procento|Maximum||Žádné dimenze|
|connectedclients9|Připojení klienti (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|totalcommandsprocessed9|Celkem operací (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|cachehits9|Přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|getcommands9|Načtení (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|setcommands9|Sady (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond9|Operací za sekundu (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|evictedkeys9|Vyřazené klíče (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|totalkeys9|Celkem klíčů (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|expiredkeys9|Klíče vypršení platnosti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|usedmemory9|Využitá paměť (horizontálních oddílů 9)|Psaný|Maximum||Žádné dimenze|
|usedmemoryRss9|RSS využité paměti (horizontálních oddílů 9)|Psaný|Maximum||Žádné dimenze|
|serverLoad9|Zatížení serveru (horizontálních oddílů 9)|Procento|Maximum||Žádné dimenze|
|cacheWrite9|Zápis do mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead9|Čtení z mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|percentProcessorTime9|PROCESOR (horizontálních oddílů 9)|Procento|Maximum||Žádné dimenze|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|Psaný|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|Psaný|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádné dimenze|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|Žádné dimenze|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/sloty/role

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|Psaný|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|Psaný|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|RoleInstanceId|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|RoleInstanceId|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|RoleInstanceId|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkový počet volání|Počet|Celkem|Celkový počet volání.|ApiName, OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Počet|Celkem|Počet úspěšných volání.|ApiName, OperationName, oblast|
|TotalErrors|Celkový počet chyb|Počet|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName, OperationName, oblast|
|BlockedCalls|Blokovaná volání|Počet|Celkem|Počet volání, která překročily limit nebo kvótu.|ApiName, OperationName, oblast|
|ServerErrors|Chyby serveru|Počet|Celkem|Počet volání s interní chybou služby (kód odpovědi HTTP 5xx).|ApiName, OperationName, oblast|
|ClientErrors|Chyby klienta|Počet|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName, OperationName, oblast|
|DataIn|Data v|Psaný|Celkem|Velikost příchozích dat v bajtech|ApiName, OperationName, oblast|
|Data|Výstupní data|Psaný|Celkem|Velikost odchozích dat v bajtech|ApiName, OperationName, oblast|
|Latence|Latence|Milisekund|Průměr|Latence v milisekundách|ApiName, OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Počet|Celkem|Celkový počet znaků v příchozím textovém požadavku.|ApiName, OperationName, oblast|
|CharactersTrained|Vyškolené znaky|Počet|Celkem|Celkový počet vyškolených znaků|ApiName, OperationName, oblast|
|SpeechSessionDuration|Doba trvání relace řeči|Sekund|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName, OperationName, oblast|
|TotalTransactions|Celkový počet transakcí|Počet|Celkem|Celkový počet transakcí|Žádné dimenze|
|TotalTokenCalls|Celkový počet volání tokenu|Počet|Celkem|Celkový počet volání tokenů|ApiName, OperationName, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádné dimenze|
|Síťové vstupy|Síť v fakturovatelným|Psaný|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Fakturovatelná síť|Psaný|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Bajty čtení z disku|Bajty čtení z disku|Psaný|Celkem|Bajty přečtené z disku během období monitorování|Žádné dimenze|
|Bajty zápisu na disk|Bajty zápisu na disk|Psaný|Celkem|Bajty zapsané na disk během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádné dimenze|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádné dimenze|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty (zastaralé)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty (zastaralé)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Hloubka fronty datových disků|Hloubka fronty datových disků (Preview)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Příchozí toky|Příchozí toky (Preview)|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádné dimenze|
|Odchozí toky|Odchozí toky (Preview)|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření příchozích toků|Míra vytváření maximálního počtu příchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádné dimenze|
|Maximální rychlost vytváření odchozích toků|Frekvence maximálního vytváření odchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádné dimenze|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Celková síť|Celková síť|Psaný|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Psaný|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|VMName|
|Síťové vstupy|Síť v fakturovatelným|Psaný|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Síťové výstupy|Fakturovatelná síť|Psaný|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|
|Bajty čtení z disku|Bajty čtení z disku|Psaný|Celkem|Bajty přečtené z disku během období monitorování|VMName|
|Bajty zápisu na disk|Bajty zápisu na disk|Psaný|Celkem|Bajty zapsané na disk během období monitorování|VMName|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|VMName|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|VMName|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádné dimenze|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádné dimenze|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu (zastaralé)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu (zastaralé)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty (zastaralé)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s (zastaralé)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu (zastaralé)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty (zastaralé)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LUN, VMName|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LUN, VMName|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|LUN, VMName|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|LUN, VMName|
|Hloubka fronty datových disků|Hloubka fronty datových disků (Preview)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|LUN, VMName|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky (Preview)|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|VMName|
|Odchozí toky|Odchozí toky (Preview)|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|VMName|
|Maximální rychlost vytváření příchozích toků|Míra vytváření maximálního počtu příchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|VMName|
|Maximální rychlost vytváření odchozích toků|Frekvence maximálního vytváření odchozích toků (Preview)|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|VMName|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|LUN, VMName|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|LUN, VMName|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|VMName|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|VMName|
|Celková síť|Celková síť|Psaný|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Psaný|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití CPU|Počet|Průměr|Využití CPU na všech jádrech v millicores.|ContainerName|
|MemoryUsage|Využití paměti|Psaný|Průměr|Celkové využití paměti v bajtech|ContainerName|
|NetworkBytesReceivedPerSecond|Počet přijatých bajtů sítě za sekundu|Psaný|Průměr|Počet přijatých bajtů sítě za sekundu.|Žádné dimenze|
|NetworkBytesTransmittedPerSecond|Bajty přenášené přes síť za sekundu|Psaný|Průměr|Počet bajtů přenášených sítí za sekundu.|Žádné dimenze|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Celkový počet vyžádané replikace|Počet|Průměr|Celkový počet načtených dat imagí|Žádné dimenze|
|SuccessfulPullCount|Počet úspěšných vyžádané replikace|Počet|Průměr|Počet úspěšných stažení imagí|Žádné dimenze|
|TotalPushCount|Celkový počet nabízených oznámení|Počet|Průměr|Celkový počet nabízených oznámení imagí|Žádné dimenze|
|SuccessfulPushCount|Počet úspěšných vložení|Počet|Průměr|Počet úspěšných vložení imagí|Žádné dimenze|
|RunDuration|Doba trvání běhu|Milisekund|Celkem|Doba běhu v milisekundách|Žádné dimenze|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Počet|Celkem|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Žádné dimenze|
|kube_node_status_allocatable_memory_bytes|Celková velikost dostupné paměti ve spravovaném clusteru|Psaný|Celkem|Celková velikost dostupné paměti ve spravovaném clusteru|Žádné dimenze|
|kube_pod_status_ready|Počet lusků ve stavu připraveno|Počet|Celkem|Počet lusků ve stavu připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Počet|Celkem|Stavy pro různé podmínky uzlu|podmínka, stav, status2, uzel|
|kube_pod_status_phase|Počet lusků podle fáze|Počet|Celkem|Počet lusků podle fáze|fáze, obor názvů pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. CustomerInsights/Centers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights volání rozhraní API|Počet|Celkem||Žádné dimenze|
|DCIMappingImportOperationSuccessfulLines|Mapování úspěšných řádků operace importu|Počet|Celkem||Žádné dimenze|
|DCIMappingImportOperationFailedLines|Chybné řádky operace importu mapování|Počet|Celkem||Žádné dimenze|
|DCIMappingImportOperationTotalLines|Mapování řádků celkových operací importu|Počet|Celkem||Žádné dimenze|
|DCIMappingImportOperationRuntimeInSeconds|Mapování modulu runtime operace importu za sekundu|Sekund|Celkem||Žádné dimenze|
|DCIOutboundProfileExportSucceeded|Odchozí export profilu byl úspěšný.|Počet|Celkem||Žádné dimenze|
|DCIOutboundProfileExportFailed|Export odchozího profilu se nezdařil.|Počet|Celkem||Žádné dimenze|
|DCIOutboundProfileExportDuration|Doba trvání exportu odchozího profilu|Sekund|Celkem||Žádné dimenze|
|DCIOutboundKpiExportSucceeded|Odchozí ukazatel KPI se úspěšně exportoval.|Počet|Celkem||Žádné dimenze|
|DCIOutboundKpiExportFailed|Export odchozího klíčového ukazatele se nezdařil.|Počet|Celkem||Žádné dimenze|
|DCIOutboundKpiExportDuration|Odchozí doba exportu klíčového ukazatele výkonu|Sekund|Celkem||Žádné dimenze|
|DCIOutboundKpiExportStarted|Začalo export odchozího klíčového ukazatele výkonu|Sekund|Celkem||Žádné dimenze|
|DCIOutboundKpiRecordCount|Počet záznamů odchozího ukazatele KPI|Sekund|Celkem||Žádné dimenze|
|DCIOutboundProfileExportCount|Počet exportů odchozího profilu|Sekund|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportFailed|Odchozí export počátečního profilu se nezdařil.|Sekund|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportSucceeded|Odchozí export počátečního profilu byl úspěšný.|Sekund|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportFailed|Nepovedlo se exportovat výstupní počáteční klíčový ukazatel výkonu.|Sekund|Celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportSucceeded|Odchozí počáteční klíč KPI se úspěšně exportoval.|Sekund|Celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportDurationInSeconds|Doba neodchozího exportu počátečního profilu v sekundách|Sekund|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiFailed|Úloha adla pro standardní klíčový ukazatel výkonu selhala během sekund.|Sekund|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiTimeOut|Časový limit adla úlohy pro standardní klíčový ukazatel výkonu v sekundách|Sekund|Celkem||Žádné dimenze|
|AdlaJobForStandardKpiCompleted|Úloha adla pro standardní klíčový ukazatel výkonu se dokončila během sekund.|Sekund|Celkem||Žádné dimenze|
|ImportASAValuesFailed|Počet neúspěšných importování hodnot ASA|Počet|Celkem||Žádné dimenze|
|ImportASAValuesSucceeded|Počet úspěšně importovaných hodnot ASA|Počet|Celkem||Žádné dimenze|
|DCIProfilesCount|Počet instancí profilu|Počet|Posledního||Žádné dimenze|
|DCIInteractionsPerMonthCount|Počet interakcí za měsíc|Počet|Posledního||Žádné dimenze|
|DCIKpisCount|Počet klíčových ukazatelů výkonu|Počet|Posledního||Žádné dimenze|
|DCISegmentsCount|Počet segmentů|Počet|Posledního||Žádné dimenze|
|DCIPredictiveMatchPoliciesCount|Počet prediktivních shod|Počet|Posledního||Žádné dimenze|
|DCIPredictionsCount|Počet předpovědí|Počet|Posledního||Žádné dimenze|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost čtení (síť)|BytesPerSecond|Průměr|Propustnost čtení síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Propustnost zápisu (síť)|BytesPerSecond|Průměr|Propustnost zápisu síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování do cloudu (sdílení)|BytesPerSecond|Průměr|Propustnost stahování do Azure ze sdílené složky během období generování sestav.|Sdílení|
|CloudUploadThroughputPerShare|Propustnost nahrávání do cloudu (sdílení)|BytesPerSecond|Průměr|Odeslání propustnosti do Azure ze sdílené složky během období generování sestav.|Sdílení|
|BytesUploadedToCloudPerShare|Odeslané bajty v cloudu (sdílená složka)|Psaný|Průměr|Celkový počet bajtů odeslaných do Azure ze sdílené složky během období generování sestav.|Sdílení|
|Úložiště|Celková kapacita|Psaný|Průměr|Celková kapacita|Žádné dimenze|
|Availablecapacity;)|Dostupná kapacita|Psaný|Průměr|Dostupná kapacita v bajtech během období generování sestav.|Žádné dimenze|
|CloudUploadThroughput|Propustnost nahrávání do cloudu|BytesPerSecond|Průměr|Propustnost nahrávání do cloudu do Azure během období generování sestav.|Žádné dimenze|
|CloudReadThroughput|Propustnost stahování v cloudu|BytesPerSecond|Průměr|Propustnost stahování cloudu do Azure během období generování sestav.|Žádné dimenze|
|BytesUploadedToCloud|Odeslané bajty v cloudu (zařízení)|Psaný|Průměr|Celkový počet bajtů odeslaných do Azure ze zařízení během období generování sestav.|Žádné dimenze|
|HyperVVirtualProcessorUtilization|Výpočetní prostředí Edge – procento využití procesoru|Procento|Průměr|Procento využití procesoru|InstanceName|
|HyperVMemoryUtilization|Výpočet využití paměti na hraničních zařízeních|Procento|Průměr|Velikost využité paměti RAM|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Počet|Celkem||profilace, Activity|
|SuccessfulRuns|Úspěšná spuštění|Počet|Celkem||profilace, Activity|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Neúspěšná metrika spuštění kanálu|Počet|Celkem||FailureType, název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Počet|Celkem||FailureType, název|
|ActivityFailedRuns|Neúspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivitySucceededRuns|Úspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|TriggerFailedRuns|Neúspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|
|TriggerSucceededRuns|Úspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití procesoru prostředí Integration runtime|Procento|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAvailableMemory|Dostupná paměť modulu runtime integrace|Psaný|Průměr||IntegrationRuntimeName, Node|
|MaxAllowedResourceCount|Maximální počet povolených entit|Počet|Maximum||Žádné dimenze|
|MaxAllowedFactorySizeInGbUnits|Maximální povolená velikost továrny (jednotka GB)|Počet|Maximum||Žádné dimenze|
|ResourceCount|Celkový počet entit|Počet|Maximum||Žádné dimenze|
|FactorySizeInGbUnits|Celková velikost továrny (jednotka GB)|Počet|Maximum||Žádné dimenze|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Počet|Celkem|Počet úspěšných úloh|Žádné dimenze|
|JobEndedFailure|Neúspěšné úlohy|Počet|Celkem|Počet neúspěšných úloh|Žádné dimenze|
|JobEndedCancelled|Zrušené úlohy|Počet|Celkem|Počet zrušených úloh|Žádné dimenze|
|JobAUEndedSuccess|Úspěšná doba AU|Sekund|Celkem|Celková doba AU pro úspěšné úlohy.|Žádné dimenze|
|JobAUEndedFailure|Neúspěšná doba aktualizace AU|Sekund|Celkem|Celková doba AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedCancelled|Zrušená čas AU|Sekund|Celkem|Celková doba AU pro zrušené úlohy.|Žádné dimenze|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Úložiště celkem|Psaný|Maximum|Celková velikost dat uložených v účtu.|Žádné dimenze|
|Napsáno|Zapsaná data|Psaný|Celkem|Celkové množství dat zapsaných na účet.|Žádné dimenze|
|Čtení z|Přečtená data|Psaný|Celkem|Celkový objem dat načtených z účtu.|Žádné dimenze|
|WriteRequests|Požadavky na zápis|Počet|Celkem|Počet požadavků na zápis dat na účet.|Žádné dimenze|
|ReadRequests|Žádosti o čtení|Počet|Celkem|Počet požadavků na čtení dat pro účet.|Žádné dimenze|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|Psaný|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|Psaný|Průměr|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Psaný|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Psaný|Průměr|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Průměr|Prodleva replikace v sekundách|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|Psaný|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|Psaný|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|Psaný|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|Psaný|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|Psaný|Průměr|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Psaný|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Psaný|Průměr|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Průměr|Prodleva replikace v sekundách|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|Psaný|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|Psaný|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|Psaný|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|Psaný|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Omezení úložiště|Psaný|Průměr|Omezení úložiště|Žádné dimenze|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Psaný|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Psaný|Průměr|Limit úložiště protokolu serveru|Žádné dimenze|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|backup_storage_used|Využité úložiště záloh|Psaný|Průměr|Využité úložiště záloh|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|Psaný|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|Psaný|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekund|Maximum|Prodleva repliky v sekundách|Žádné dimenze|
|pg_replica_log_delay_in_bytes|Maximální prodleva napříč replikami|Psaný|Maximum|Prodleva z největšího zpoždění repliky v bajtech|Žádné dimenze|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|IOPS|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|Žádné dimenze|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|Psaný|Průměr|Využité úložiště|Žádné dimenze|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|network_bytes_egress|Síťové výstupy|Psaný|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Síťové vstupy|Psaný|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|D2C. telemetrie. příchozí přenos dat allProtocol|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat. úspěch|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné dimenze|
|C2D. Commands. Complete. Complete. Success|Dokončené příkazy|Počet|Celkem|Počet příkazů typu cloud-zařízení úspěšně dokončených zařízením|Žádné dimenze|
|C2D. Commands. odchozí. Abandon. Success|Zrušené příkazy|Počet|Celkem|Počet příkazů typu cloud-zařízení, které zařízení opustilo|Žádné dimenze|
|C2D. Commands. odchozí. remítat. Success|Odmítnuté příkazy|Počet|Celkem|Počet příkazů typu cloud-zařízení odmítnutých zařízením|Žádné dimenze|
|Devices. totalDevices|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|Devices. connectedDevices. allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|D2C. telemetrie. odchozí. úspěch|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné dimenze|
|D2C. telemetrie. výstup. vyřazeno|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné dimenze|
|D2C. telemetrie. výstup. osamocený|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné dimenze|
|D2C. telemetrie. invýstup. neplatné|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné dimenze|
|D2C. telemetrie. odchozí. Fallback|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné dimenze|
|D2C. Endpoints. odchozí. eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné dimenze|
|D2C. Endpoints. latence. eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné dimenze|
|D2C. Endpoints. invýstups. builtIns. events|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události). Tato metrika začne pracovat jenom v případě, že je povolené směrování (https://aka.ms/iotrouting) pro službu IoT Hub.|Žádné dimenze|
|D2C. Endpoints. latence. builtIn. events|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události) Tato metrika začne pracovat jenom v případě, že je povolené směrování (https://aka.ms/iotrouting) pro službu IoT Hub.|Žádné dimenze|
|D2C. Endpoints. odchozí úložiště. Storage|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. latence. Storage|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné dimenze|
|D2C. Endpoints. invýstups. Storage. bytes|Směrování: data Doručená do úložiště|Psaný|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné dimenze|
|D2C. Endpoints. výstup. Storage. BLOBs|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné dimenze|
|EventGridDeliveries|Event Grid doručení (Preview)|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události (https://aka.ms/ioteventgrid).|Výsledek, EventType|
|EventGridLatency|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ|
|D2C. vláken. Read. Success|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. nevlákenný. Read. Failure|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné dimenze|
|D2C. nevlákenný. Read. Size|Velikost odpovědi u dvojitých čtení ze zařízení|Psaný|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. Update. Success|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. aktualizace. selhání|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné dimenze|
|D2C. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Psaný|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|C2D. Methods. Success|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádné dimenze|
|C2D. Methods. requestSize|Velikost žádosti o vyvolání přímé metody|Psaný|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné dimenze|
|C2D. Methods. responseSize|Velikost odezvy volání přímých metod|Psaný|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné dimenze|
|C2D. vláken. Read. Success|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné dimenze|
|C2D. nevlákenný. Read. Failure|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné dimenze|
|C2D. nevlákenný. Read. Size|Velikost odpovědi zdvojeného čtení z back-endu|Psaný|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné dimenze|
|C2D. nevlákenná. Update. Success|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné dimenze|
|C2D. nevlákenná. aktualizace. selhání|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné dimenze|
|C2D. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací z back-endu|Psaný|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné dimenze|
|twinQueries. Success|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné dimenze|
|twinQueries. selhání|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné dimenze|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|Psaný|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné dimenze|
|Jobs. createTwinUpdateJob. Success|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|Jobs. createTwinUpdateJob. selhání|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|Jobs. createDirectMethodJob. Success|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné dimenze|
|Jobs. createDirectMethodJob. selhání|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné dimenze|
|Jobs. listJobs. Success|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné dimenze|
|Jobs. listJobs. selhání|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné dimenze|
|Jobs. cancelJob. Success|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné dimenze|
|Jobs. cancelJob. selhání|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné dimenze|
|Jobs. queryJobs. Success|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné dimenze|
|Jobs. queryJobs. selhání|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné dimenze|
|dokončené úlohy|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|úlohy. nezdařilo se|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat sendThrottle|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet všech aktuálně využívaných zpráv Jedná se o kumulativní hodnotu, která se každý den resetuje na nulu v 00:00 UTC.|Žádné dimenze|
|deviceDataUsage|Celkové využití dat zařízení|Psaný|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|totalDeviceCount|Celkem zařízení (Preview)|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|connectedDeviceCount|Připojená zařízení (Preview)|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|konfiguračních|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádné dimenze|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Počet|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Přiřazená zařízení|Počet|Celkem|Počet zařízení přiřazených ke centru IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Počet|Celkem|Počet pokusů o ověření identity zařízení|ProvisioningServiceName, status, protokol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AvailableStorage|Dostupné úložiště|Psaný|Celkem|Celkové dostupné úložiště hlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Ukončení připojení Cassandra|Počet|Celkem|Počet uzavřených připojení Cassandra, která se hlásí s členitou úrovní 1 minuty|Oblast, ClosureReason|
|CassandraRequestCharges|Poplatky za žádosti Cassandra|Počet|Celkem|Ru spotřebované pro vytvořené požadavky Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType|
|CassandraRequests|Žádosti Cassandra|Počet|Počet|Počet provedených požadavků Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType, ErrorCode|
|Využití datausage|Využití dat|Psaný|Celkem|Celkové využití dat nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|DocumentCount|Počet dokumentů|Počet|Celkem|Celkový počet dokumentů hlášených v rozmezí 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Kvóta dokumentu|Psaný|Celkem|Celková kvóta úložiště vykazovaná s členitosti 5 minut|CollectionName, DatabaseName, region|
|IndexUsage|Využití indexu|Psaný|Celkem|Celkové využití indexu nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Žádosti o metadata|Počet|Počet|Počet požadavků na metadata Cosmos DB zachovává shromažďování systémových metadat pro každý účet, který vám umožní vytvořit výčet kolekcí, databází atd. a jejich konfigurací bez poplatků.|DatabaseName, CollectionName, region, StatusCode |
|MongoRequestCharge|Poplatek za požadavek Mongo|Počet|Celkem|Spotřebované jednotky žádosti Mongo|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequests|Žádosti Mongo|Počet|Počet|Počet provedených požadavků Mongo|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|ProvisionedThroughput|Zřízená propustnost|Počet|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|ReplicationLatency|Latence replikace p99|Milisekund|Průměr|Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet|SourceRegion, TargetRegion|
|ServiceAvailability|Dostupnost služby|Procento|Průměr|Dostupnost žádostí o účet v časovém rozmezí jedné hodiny, dne nebo měsíce|Žádné dimenze|
|TotalRequestUnits|Celkový počet jednotek žádostí|Počet|Celkem|Spotřebované jednotky žádosti|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType|
|TotalRequests|Požadavky celkem|Počet|Počet|Počet provedených požadavků|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Počet|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné dimenze|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné dimenze|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádné dimenze|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishFailCount|Neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Počet|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|

## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub|EntityName |
|ServerErrors|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. EventHub|EntityName |
|UserErrors|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub|EntityName |
|QuotaExceededErrors|Chyby překročení kvóty|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|EntityName |
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub|EntityName |
|IncomingRequests|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub|entityName|
|IncomingMessages|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub|entityName|
|OutgoingMessages|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub|entityName|
|IncomingBytes|Příchozí bajty.|Psaný|Celkem|Příchozí bajty pro Microsoft. EventHub|entityName|
|OutgoingBytes|Odchozí bajty|Psaný|Celkem|Odchozí bajty pro Microsoft. EventHub|entityName|
|ActiveConnections|ActiveConnections|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|entityName|
|ConnectionsClosed|Uzavřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|entityName|
|CaptureBacklog|Zachyťte nevyřízené položky.|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|entityName|
|CapturedMessages|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub|entityName|
|CapturedBytes|Zachycené bajty.|Psaný|Celkem|Zachycené bajty pro Microsoft. EventHub|entityName|
|Velikost|Velikost|Psaný|Průměr|Velikost centra EventHub v bajtech|entityName|
|INREQS|Příchozí požadavky (zastaralé)|Počet|Celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů (zastaralé)|Žádné dimenze|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|FAILREQ|Neúspěšné žádosti (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|SVRBSY|Chyby zaneprázdněnosti serveru (zastaralé)|Počet|Celkem|Celkový počet chyb zaneprázdněných serverem pro obor názvů (zastaralé)|Žádné dimenze|
|MEZI sebou|Interní chyby serveru (zastaralé)|Počet|Celkem|Celkový počet interních chyb serveru pro obor názvů (zastaralé)|Žádné dimenze|
|MISCERR|Další chyby (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádné dimenze|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích zpráv (zastaralé).|Žádné dimenze|
|EHINMSGS|Příchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích zpráv (zastaralé).|Žádné dimenze|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádné dimenze|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|Psaný|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích bajtů (zastaralé).|Žádné dimenze|
|EHINBYTES|Příchozí bajty (zastaralé)|Psaný|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|Psaný|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích bajtů (zastaralé).|Žádné dimenze|
|EHOUTBYTES|Odchozí bajty (zastaralé)|Psaný|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádné dimenze|
|EHABL|Archivovat nevyřízené zprávy (zastaralé)|Počet|Celkem|Archivní zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádné dimenze|
|EHAMSGS|Archivní zprávy (zastaralé)|Počet|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)|Žádné dimenze|
|EHAMBS|Propustnost zpráv archivu (zastaralé)|Psaný|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádné dimenze|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ServerErrors|Chyby serveru. (Preview)|Počet|Celkem|Chyby serveru pro Microsoft. EventHub (Preview)|Žádné dimenze|
|UserErrors|Chyby uživatele. (Preview)|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub (Preview)|Žádné dimenze|
|QuotaExceededErrors|Chyby překročení kvóty (Preview)|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub. (Preview)|Žádné dimenze|
|ThrottledRequests|Omezené požadavky. (Preview)|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingRequests|Příchozí žádosti (Preview)|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingMessages|Příchozí zprávy (Preview)|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|OutgoingMessages|Odchozí zprávy (Preview)|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|IncomingBytes|Příchozí bajty. (Preview)|Psaný|Celkem|Příchozí bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|OutgoingBytes|Odchozí bajty (Preview)|Psaný|Celkem|Odchozí bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ActiveConnections|ActiveConnections (Preview)|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení. (Preview)|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|ConnectionsClosed|Uzavřená připojení. (Preview)|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub (Preview)|Žádné dimenze|
|CaptureBacklog|Zachyťte nevyřízené položky. (Preview)|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub. (Preview)|Žádné dimenze|
|CapturedMessages|Zachycené zprávy. (Preview)|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub (Preview)|Žádné dimenze|
|CapturedBytes|Zachycené bajty. (Preview)|Psaný|Celkem|Zachycené bajty pro Microsoft. EventHub (Preview)|Žádné dimenze|
|Procesor|PROCESOR (Preview)|Procento|Maximum|Využití CPU pro cluster centra událostí jako procento|Role|
|AvailableMemory|Dostupná paměť (Preview)|Počet|Maximum|Dostupná paměť pro cluster centra událostí v bajtech|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Žádosti o bránu|Počet|Celkem|Počet žádostí o bránu|ClusterDnsName, stavu protokolu http|
|CategorizedGatewayRequests|Zařadit požadavky na bránu|Počet|Celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, stavu protokolu http|
|NumActiveWorkers|Počet aktivních pracovníků|Počet|Maximum|Počet aktivních pracovníků|ClusterDnsName, metric|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaznamenaná hodnota metriky|Počet|Průměr|Hodnota vypočítaná AutoScale při spuštění|MetricTriggerSource|
|MetricThreshold|Prahová hodnota metriky|Počet|Průměr|Nakonfigurované prahové hodnoty automatického škálování, když se spustilo automatické škálování.|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Počet|Průměr|Kapacita nahlášená pro automatické škálování při jejím spuštění.|Žádné dimenze|
|ScaleActionsInitiated|Zahájené akce škálování|Počet|Celkem|Směr operace škálování.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

(Public Preview)

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityResult/název, availabilityResult/umístění|
|availabilityResults/Count|Testy dostupnosti|Počet|Počet|Počet testů dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba trvání testu dostupnosti|Milisekund|Průměr|Doba trvání testu dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Doba připojení k síti – načtení stránky|Milisekund|Průměr|Doba mezi požadavkem uživatele a připojením k síti. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádné dimenze|
|browserTimings/processingDuration|Doba zpracování klienta|Milisekund|Průměr|Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky.|Žádné dimenze|
|browserTimings/receiveDuration|Doba přijetí odezvy|Milisekund|Průměr|Čas mezi prvním a posledním bajtů nebo až do odpojení|Žádné dimenze|
|browserTimings/sendDuration|Čas požadavku na odeslání|Milisekund|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádné dimenze|
|browserTimings/totalDuration|Doba načítání stránky v prohlížeči|Milisekund|Průměr|Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí.|Žádné dimenze|
|závislosti/počet|Volání závislostí|Počet|Počet|Počet volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěch, provoz/syntetická, Cloud/roleInstance, Cloud/roleName|
|závislosti/doba trvání|Doba trvání závislosti|Milisekund|Průměr|Doba trvání volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěch, provoz/syntetická, Cloud/roleInstance, Cloud/roleName|
|závislosti/selhání|Selhání volání závislostí|Počet|Počet|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|závislost/typ, Dependency/performanceBucket, provoz/syntetické, Cloud/roleInstance, Cloud/roleName|
|pageViews/Count|Zobrazení stránek|Počet|Počet|Počet zobrazení stránek|operace/syntetické|
|pageViews/doba trvání|Doba načítání zobrazení stránky|Milisekund|Průměr|Doba načítání zobrazení stránky|operace/syntetické|
|Čítače výkonu/requestExecutionTime|Doba provádění požadavku HTTP|Milisekund|Průměr|Čas provedení posledního požadavku.|Cloud/roleInstance|
|Čítače výkonu/requestsInQueue|Požadavky HTTP ve frontě aplikací|Počet|Průměr|Délka fronty požadavků aplikace|Cloud/roleInstance|
|Čítače výkonu/requestsPerSecond|Rychlost požadavku HTTP|CountPerSecond|Průměr|Míra všech požadavků na aplikaci za sekundu z ASP.NET.|Cloud/roleInstance|
|Čítače výkonu/exceptionsPerSecond|Míra výjimek|CountPerSecond|Průměr|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|Cloud/roleInstance|
|Čítače výkonu/processIOBytesPerSecond|Rychlost zpracování v/v|BytesPerSecond|Průměr|Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení.|Cloud/roleInstance|
|Čítače výkonu/processCpuPercentage|PROCESOR procesů|Procento|Průměr|Procentuální hodnota uplynulého času, který všechny podprocesy procesu používají k provádění instrukcí. Může se lišit od 0 do 100. Tato metrika indikuje výkon samotného procesu W3wp.|Cloud/roleInstance|
|Čítače výkonu/processorCpuPercentage|Čas procesoru|Procento|Průměr|Procento času, které procesor stráví v nečinných vláknech|Cloud/roleInstance|
|Čítače výkonu/memoryAvailableBytes|Dostupná paměť|Psaný|Průměr|Fyzická paměť je okamžitě k dispozici pro přidělení procesu nebo pro použití systémem.|Cloud/roleInstance|
|Čítače výkonu/processPrivateBytes|Nesdílené bajty procesu|Psaný|Průměr|Paměť exkluzivně přiřazená k procesům monitorovaných aplikací.|Cloud/roleInstance|
|žádosti/doba trvání|Doba odezvy serveru|Milisekund|Průměr|Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/počet|Žádosti serveru|Počet|Počet|Počet dokončených požadavků HTTP|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/selhání|Neúspěšné požadavky|Počet|Počet|Počet požadavků HTTP označených jako neúspěšné Ve většině případů se jedná o žádosti s kódem odpovědi > = 400 a nerovná se 401.|Request/performanceBucket, Request/resultCode, Operational/syntetické, Cloud/roleInstance, Cloud/roleName|
|žádosti/rychlost|Počet požadavků serveru|CountPerSecond|Průměr|Frekvence požadavků serveru za sekundu|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|výjimky/počet|Výjimky|Počet|Počet|Kombinovaný počet všech nezachycených výjimek.|Cloud/roleName, Cloud/roleInstance, klient/typ|
|výjimky/prohlížeč|Výjimky prohlížečů|Počet|Počet|Počet nezachycených výjimek vyvolaných v prohlížeči|Žádné dimenze|
|výjimky/Server|Výjimky serveru|Počet|Počet|Počet nezachycených výjimek vyvolaných v serverové aplikaci|Cloud/roleName, Cloud/roleInstance|
|trasování/počet|Trasování|Počet|Počet|Počet dokumentů trasování|Trace/severityLevel, provozní/syntetické, Cloud/roleName, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkový počet přístupů k rozhraní API služby|Počet|Počet|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode|
|ServiceApiResult|Celkový počet výsledků rozhraní API služby|Počet|Počet|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Využití mezipaměti|Procento|Průměr|Úroveň využití v oboru clusteru|Žádné|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazů v sekundách|Dotaz na stav|
|IngestionUtilization|Využití příjmu|Procento|Průměr|Poměr využitých slotů pro přijímání na clusteru|Žádné|
|Udržení|Zachovat naživu|Počet|Průměr|Správnosti check indikuje, že cluster reaguje na dotazy|Žádné|
|IngestionVolumeInMB|Objem příjmu (v MB)|Počet|Celkem|Celkový objem zpracovaných dat do clusteru (v MB)|Databáze|
|IngestionLatencyInSeconds|Latence příjmu (v sekundách)|Sekund|Průměr|Doba ingestování ze zdroje (např. zpráva je v centru EventHub) do clusteru v řádu sekund|Žádné|
|EventProcessedForEventHubs|Zpracované události (pro Event Hubs)|Počet|Celkem|Počet událostí zpracovaných clusterem při přijímání z centra událostí|Žádné|
|IngestionResult|Výsledek ingestování|Počet|Počet|Počet operací ingestování|Stav|
|Procesor|Procesor|Procento|Průměr|Úroveň využití procesoru|Žádné|
| ContinuousExportNumOfRecordsExported | Počet záznamů exportovaných při průběžném exportu | Počet | Celkem | Počet záznamů exportovaných pro každý artefakt úložiště zapsaný během operace exportu  | Žádné |
| ExportUtilization | Využití exportu | Procento | Maximum | Využití exportu | Žádné |
| ContinuousExportPendingCount | Počet nevyřízených položek průběžného exportu | Počet | Maximum | Počet probíhajících úloh průběžného exportu připravených k provedení | Žádné |
| ContinuousExportMaxLatenessMinutes | Maximální počet minut zpoždění pro průběžný export | Počet | Maximum | Maximální doba v minutách pro všechny průběžné exporty, které čekají a jsou připravené ke spuštění | Žádné |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Počet volání rozhraní API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunLatency|Latence spuštění|Sekund|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšného spuštění|Sekund|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce|Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekund|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekund|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|TriggersStarted|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggersFailed|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekund|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence požáru triggeru |Sekund|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekund|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|
|BillableActionExecutions|Fakturovatelné provádění akcí|Počet|Celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádné dimenze|
|BillableTriggerExecutions|Fakturovatelná spuštění triggerů|Počet|Celkem|Počet fakturovaných provedení triggerů pracovního postupu|Žádné dimenze|
|TotalBillableExecutions|Fakturovatelná spuštění celkem|Počet|Celkem|Počet fakturovaných provedení pracovního postupu|Žádné dimenze|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Počet|Celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Počet|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Počet|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádné dimenze|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Počet|Celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Počet|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Počet|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádné dimenze|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunLatency|Latence spuštění|Sekund|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšného spuštění|Sekund|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|RunStartThrottledEvents|Spustit omezené události|Počet|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce |Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekund|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekund|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|TriggersStarted|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggersFailed|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekund|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence požáru triggeru |Sekund|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekund|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Procento|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti workflowu pro prostředí integrační služby|Procento|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí integrační služby|Procento|Průměr|Využití procesoru konektoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí integrační služby|Procento|Průměr|Využití paměti konektoru pro prostředí integrační služby.|Žádné dimenze|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Dokončená spuštění|Dokončená spuštění|Počet|Celkem|Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen.|Scénář|
|Spuštěná spuštění|Spuštěná spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář|
|Neúspěšná spuštění|Neúspěšná spuštění|Počet|Celkem|Počet spuštění se pro tento pracovní prostor nezdařil.|Scénář|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/Accounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Počet volání rozhraní API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostupnost|Dostupnost|Procento|Průměr|Dostupnost rozhraní API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/svazky

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageOtherLatency|Průměrná jiná latence|MS/op|Průměr|Průměrná druhá latence (která není pro čtení nebo zápis) v milisekundách na operaci|Žádné dimenze|
|AverageReadLatency|Průměrná latence čtení|MS/op|Průměr|Průměrná latence čtení v milisekundách na operaci|Žádné dimenze|
|AverageTotalLatency|Průměrná celková latence|MS/op|Průměr|Průměrná celková latence v milisekundách na operaci|Žádné dimenze|
|AverageWriteLatency|Průměrná latence zápisu|MS/op|Průměr|Průměrná latence zápisu v milisekundách na operaci|Žádné dimenze|
|FilesystemOtherOps|Jiná operace systému souborů|OPS|Průměr|Počet dalších operací systému souborů (které nejsou čteny nebo zapsány)|Žádné dimenze|
|FilesystemReadOps|Operace čtení systému souborů|OPS|Průměr|Počet operací čtení systému souborů|Žádné dimenze|
|FilesystemTotalOps|Celková operace systému souborů|OPS|Průměr|Součet všech operací systému souborů|Žádné dimenze|
|FilesystemWriteOps|Operace zápisu do systému souborů|OPS|Průměr|Počet operací zápisu do systému souborů|Žádné dimenze|
|IoBytesPerOtherOps|Vstupně-výstupní bajty na jiné operace|bajty/op|Průměr|Počet vstupně-výstupních bajtů na jiné operace (které nejsou čtením nebo zápisem)|Žádné dimenze|
|IoBytesPerReadOps|Vstupně-výstupní operace na operaci čtení|bajty/op|Průměr|Počet vstupně-výstupních bajtů na operaci čtení|Žádné dimenze|
|IoBytesPerTotalOps|Počet vstupně-výstupních bajtů za sekundu ve všech operacích|bajty/op|Průměr|Součet všech operací v/v bajtech|Žádné dimenze|
|IoBytesPerWriteOps|Vstupně-výstupní operace na operaci zápisu|bajty/op|Průměr|Počet vstupně-výstupních bajtů na operaci zápisu|Žádné dimenze|
|OtherIops|Další IOPS|operace za sekundu|Průměr|Další vstupně-výstupní operace za sekundu|Žádné dimenze|
|OtherThroughput|Jiná propustnost|Procesor|Průměr|Jiná propustnost (která není pro čtení nebo zápis) v megabajtech za sekundu|Žádné dimenze|
|ReadIops|Čtení IOPS|operace za sekundu|Průměr|Načíst vstupně-výstupní operace za sekundu|Žádné dimenze|
|ReadThroughput|Propustnost čtení|Procesor|Průměr|Propustnost čtení v megabajtech za sekundu|Žádné dimenze|
|TotalIops|Celkový počet IOPS|operace za sekundu|Průměr|Součet všech vstupně-výstupních operací za sekundu|Žádné dimenze|
|TotalThroughput|Celková propustnost|Procesor|Průměr|Součet veškeré propustnosti v megabajtech za sekundu|Žádné dimenze|
|VolumeAllocatedSize|Velikost přiděleného svazku|Psaný|Průměr|Přidělená velikost svazku (nejedná se o skutečně využité bajty)|Žádné dimenze|
|VolumeLogicalSize|Logická velikost svazku|Psaný|Průměr|Logická velikost svazku (použité bajty)|Žádné dimenze|
|VolumeSnapshotSize|Velikost snímku svazku|Psaný|Průměr|Velikost všech snímků ve svazku|Žádné dimenze|
|WriteIops|Zápis IOPS|operace za sekundu|Průměr|Zapsat vstupně-výstupní operace za sekundu|Žádné dimenze|
|WriteThroughput|Propustnost zápisu|Procesor|Průměr|Propustnost zápisu v megabajtech za sekundu|Žádné dimenze|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Přidělená velikost fondu svazků|Psaný|Průměr|Přidělená velikost fondu (ne skutečné využité bajty)|Žádné dimenze|
|VolumePoolAllocatedUsed|Využití přiděleného fondu svazků|Psaný|Průměr|Přidělená velikost fondu, která se používá|Žádné dimenze|
|VolumePoolTotalLogicalSize|Celková logická velikost fondu svazků|Psaný|Průměr|Součet logické velikosti všech svazků patřících do fondu|Žádné dimenze|
|VolumePoolTotalSnapshotSize|Celková velikost snímku fondu svazků|Psaný|Průměr|Součet všech snímků ve fondu|Žádné dimenze|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Počet|Celkem|Počet bajtů, které síťové rozhraní odeslalo|Žádné dimenze|
|BytesReceivedRate|Přijaté bajty|Počet|Celkem|Počet bajtů, které síťové rozhraní přijalo|Žádné dimenze|
|PacketsSentRate|Odeslané pakety|Počet|Celkem|Počet paketů, které síťové rozhraní odeslalo|Žádné dimenze|
|PacketsReceivedRate|Přijaté pakety|Počet|Celkem|Počet paketů, které síťové rozhraní přijalo|Žádné dimenze|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost cesty k datům Load Balancer za dobu trvání|FrontendIPAddress, FrontendPort|
|DipAvailability|Stav sondy stavu|Počet|Průměr|Průměrný stav testu stavu Load Balancer za dobu trvání|Typprotokolu, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Počet|Celkem|Celkový počet odeslaných bajtů v časovém období|FrontendIPAddress, FrontendPort, směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|FrontendIPAddress, FrontendPort, směr|
|SYNCount|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|FrontendIPAddress, FrontendPort, směr|
|SnatConnectionCount|Počet připojení SNAT|Počet|Celkem|Celkový počet nových připojení SNAT vytvořených během časového období|FrontendIPAddress, BackendIPAddress, vlastnost ConnectionState|
|AllocatedSnatPorts|Přidělené porty SNAT (Preview)|Počet|Celkem|Celkový počet portů SNAT přidělených v rámci časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu|
|UsedSnatPorts|Využité porty SNAT (Preview)|Počet|Celkem|Celkový počet portů SNAT používaných během časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Počet|Celkem|Počet dotazů poskytovaných pro zónu DNS|Žádné dimenze|
|RecordSetCount|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně DNS|Žádné dimenze|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádné dimenze|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
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
|IfUnderDDoSAttack|V části útok DDoS nebo ne|Počet|Maximum|V části útok DDoS nebo ne|Žádné dimenze|
|DDoSTriggerTCPPackets|Příchozí pakety TCP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci zmírnění DDoS|Žádné dimenze|
|DDoSTriggerUDPPackets|Příchozí pakety UDP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci zmírnění DDoS|Žádné dimenze|
|DDoSTriggerSYNPackets|Příchozí pakety SYN pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci zmírnění DDoS|Žádné dimenze|
|VipAvailability|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost IP adresy za dobu trvání|Port|
|ByteCount|Počet bajtů|Počet|Celkem|Celkový počet odeslaných bajtů v časovém období|Port, směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|Port, směr|
|SynCount|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|Port, směr|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet volání pravidel aplikace|Počet|Celkem|Počet přístupů k pravidlům aplikace|Stav, důvod, protokol|
|NetworkRuleHit|Počet volání síťových pravidel|Počet|Celkem|Počet přístupů k síťovým pravidlům|Stav, důvod, protokol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Celkem|Počet bajtů za sekundu, které Application Gateway zasloužily|Žádné dimenze|
|UnhealthyHostCount|Počet hostitelů není v pořádku|Počet|Průměr|Počet nezdravých hostitelů back-endu|BackendSettingsPool|
|healthyHostCount|Počet hostitelů v pořádku|Počet|Průměr|Počet nefunkčních hostitelů back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Počet|Celkem|Počet úspěšných požadavků, které Application Gateway obsluhovány|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Počet|Celkem|Počet neúspěšných žádostí, které Application Gateway obsluhovány|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Počet|Celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální připojení|Počet|Celkem|Počet aktuálních připojení vytvořených pomocí Application Gateway|Žádné dimenze|
|CapacityUnits|Aktuální jednotky kapacity|Počet|Průměr|Spotřebované jednotky kapacity|Žádné dimenze|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Šířka pásma S2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádné dimenze|
|P2SBandwidth|Šířka pásma P2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Počet připojení P2S|Počet|Maximum|Počet připojení typu Point-to-site brány|Protocol (Protokol)|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Výstupní bajty tunelu|Psaný|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Vstupní bajty tunelového propojení|Psaný|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Odchozí tunelové pakety|Počet|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunelové pakety pro příchozí připojení|Počet|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Odpojení odchozího paketu o neshodě tunelového propojení TS|Počet|Celkem|Počet odchozích odkládacích paketů z výběrů neshody tunelového propojení|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Přetažení paketů neshody TS s tunelem pro příchozí přenosy|Počet|Celkem|Počet odkládacích paketů ze seznamu neshody pro výběr odchozího přenosu tunelu|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle vráceného koncového bodu|Počet|Celkem|Počet vrácených Traffic Managerho koncového bodu v daném časovém rámci|Koncový bod|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu podle koncového bodu|Počet|Maximum|1, pokud je stav testu koncového bodu zapnuto, 0 jinak.|Koncový bod|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% PROBE selhalo|Procento|Průměr|% sond monitorování připojení selhalo.|Žádné dimenze|
|AverageRoundtripMs|Průměrná doba odezvy (MS)|Milisekund|Průměr|Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem|Žádné dimenze|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|requestCount|Počet požadavků|Počet|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Velikost požadavku|Psaný|Celkem|Počet bajtů odeslaných jako požadavek od klientů na proxy server HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Velikost odpovědi|Psaný|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Počet|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S do back-endu|Stavu protokolu HTTP, HttpStatusGroup, back-end|
|BackendRequestLatency|Latence žádosti back-endu|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek odeslán proxy HTTP/S do back-endu do back-endu, dokud proxy protokolu HTTP/S nedostalo poslední bajt odpovědi z back-endu|Back-end|
|TotalLatency|Celková latence|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stavu back-endu|Procento|Průměr|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endu|Back-end, problémových|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/obory názvů/NotificationHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registrace. vše|Operace registrace|Počet|Celkem|Počet všech úspěšných operací registrace (vytváření dotazů a odstraňování aktualizací). |Žádné dimenze|
|registrace. Create|Operace vytvoření registrace|Počet|Celkem|Počet všech úspěšných vytvoření registrace.|Žádné dimenze|
|registrace. aktualizace|Operace aktualizace registrace|Počet|Celkem|Počet všech úspěšných aktualizací registrace.|Žádné dimenze|
|registrace. Get|Registrace – operace čtení|Počet|Celkem|Počet všech úspěšných dotazů na registraci.|Žádné dimenze|
|registrace. odstranění|Registrace – operace odstranění|Počet|Celkem|Počet všech úspěšných odstranění registrací.|Žádné dimenze|
|Přijíman|Příchozí zprávy|Počet|Celkem|Počet všech úspěšných volání rozhraní API pro odeslání. |Žádné dimenze|
|příchozí. plánováno|Odeslaná naplánovaná nabízená oznámení|Počet|Celkem|Plánovaná nabízená oznámení zrušena|Žádné dimenze|
|příchozí. plánováno. Cancel|Plánovaná nabízená oznámení zrušena|Počet|Celkem|Plánovaná nabízená oznámení zrušena|Žádné dimenze|
|naplánováno. čeká na|Nedokončená plánovaná oznámení|Počet|Celkem|Nedokončená plánovaná oznámení|Žádné dimenze|
|instalace. vše|Operace správy instalace|Počet|Celkem|Operace správy instalace|Žádné dimenze|
|instalace. Get|Získat operace instalace|Počet|Celkem|Získat operace instalace|Žádné dimenze|
|instalace. Upsert|Operace vytvoření nebo aktualizace instalace|Počet|Celkem|Operace vytvoření nebo aktualizace instalace|Žádné dimenze|
|instalace. patch|Operace instalace opravy|Počet|Celkem|Operace instalace opravy|Žádné dimenze|
|instalace. odstranit|Odstranit operace instalace|Počet|Celkem|Odstranit operace instalace|Žádné dimenze|
|odchozí. allpns. úspěch|Úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. allpns. invalidpayload|Chyby datové části|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS vrátil chybnou datovou část.|Žádné dimenze|
|odchozí. allpns. pnserror|Chyby systému externích oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože došlo k potížím při komunikaci s PNS (nezahrnuje problémy s ověřováním).|Žádné dimenze|
|odchozí. allpns. channelerror|Chyby kanálu|Počet|Celkem|Počet nabízených oznámení, která selhala, protože kanál byl neplatný, ale není přidružený ke správné aplikaci nebo vypršela její platnost.|Žádné dimenze|
|odchozí. allpns. badorexpiredchannel|Chybné nebo prošlé chyby kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršela platnost kanálu/tokenu nebo registrationId, nebo je neplatná.|Žádné dimenze|
|odchozí. WNS. úspěch|WNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. WNS. invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované. (Windows Live nerozpoznává přihlašovací údaje.)|Žádné dimenze|
|odchozí. WNS. badchannel|Chyba WNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav WNS: 404 nebyl nalezen).|Žádné dimenze|
|odchozí. WNS. expiredchannel|Chyba kanálu vypršení platnosti WNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože platnost parametr channeluri vypršela (stav WNS: 410 pryč).|Žádné dimenze|
|odchozí. WNS. omezení|WNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože WNS omezuje tuto aplikaci (stav WNS: 406 není přijatelné).|Žádné dimenze|
|odchozí. WNS. tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Počet|Celkem|Systém Windows Live není dostupný.|Žádné dimenze|
|odchozí. WNS. invalidtoken|WNS – chyby autorizace (neplatný token)|Počet|Celkem|Token poskytnutý pro WNS není platný (stav WNS: 401 Neautorizováno).|Žádné dimenze|
|odchozí. WNS. wrongtoken|WNS chyby autorizace (špatný token)|Počet|Celkem|Token poskytnutý pro WNS je platný, ale pro jinou aplikaci (stav WNS: 403 zakázaný). K tomu může dojít, pokud je parametr channeluri v registraci přidružen k jiné aplikaci. Ověřte, že je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádné dimenze|
|odchozí. WNS. invalidnotificationformat|WNS neplatný formát oznámení|Počet|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že WNS neumožňuje odmítat všechny neplatné datové části.|Žádné dimenze|
|odchozí. WNS. invalidnotificationsize|WNSá Chyba neplatné velikosti oznámení|Počet|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádné dimenze|
|odchozí. WNS. channelthrottled|WNS kanál – omezení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-NotificationStatus: channelThrottled).|Žádné dimenze|
|odchozí. WNS. channeldisconnected|WNS kanál odpojen|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|odchozí. WNS. vyřazeno|WNS Vyřazená oznámení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (X-WNS-NotificationStatus: vyřazeno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|odchozí. WNS. pnserror|WNS chyby|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s WNS.|Žádné dimenze|
|odchozí. WNS. authenticationerror|WNS chyby ověřování|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje nebo chybný token.|Žádné dimenze|
|odchozí. APNs. Success|Úspěšná oznámení APNS|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. APNs. invalidcredentials|Chyby autorizace APNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. APNs. badchannel|Chyba služby APN Bad Channel|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (Stavový kód binárního protokolu APNS: 8. Stavový kód protokolu HTTP služby APN: 400 s názvem "BadDeviceToken").|Žádné dimenze|
|odchozí. APNs. expiredchannel|Chyba kanálu vypršení platnosti služby APNS|Počet|Celkem|Počet tokenů, u kterých se zrušila platnost kanálu zpětné vazby APNS.|Žádné dimenze|
|odchozí. APNs. invalidnotificationsize|APN – Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (Stavový kód binárního protokolu APNS: 7).|Žádné dimenze|
|odchozí. APNs. pnserror|Chyby služby APN|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádné dimenze|
|odchozí. GCM. úspěch|GCM úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. GCM. invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. GCM. badchannel|Chyba GCM špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci nebyl rozpoznán (výsledek GCM: Neplatná registrace).|Žádné dimenze|
|odchozí. GCM. expiredchannel|Chyba kanálu vypršení platnosti GCM|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádné dimenze|
|odchozí. GCM. omezení|GCM omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože aplikace GCM omezila tuto aplikaci (kód stavu GCM: 501-599 nebo výsledek: nedostupný)|Žádné dimenze|
|odchozí. GCM. invalidnotificationformat|GCM neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část nebyla správně naformátována (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádné dimenze|
|odchozí. GCM. invalidnotificationsize|GCMá Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádné dimenze|
|odchozí. GCM. wrongchannel|GCM chybná chyba kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružená k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádné dimenze|
|odchozí. GCM. pnserror|GCM chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádné dimenze|
|odchozí. GCM. authenticationerror|GCM chyby ověřování|Počet|Celkem|Počet nabízených oznámení, která selhala, protože PNS nepřijaly zadané přihlašovací údaje, jsou přihlašovací údaje blokované nebo SenderId není v aplikaci správně nakonfigurovaný (výsledek GCM: MismatchedSenderId).|Žádné dimenze|
|odchozí. MPNS. úspěch|MPNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. MPNS. invalidcredentials|Neplatné přihlašovací údaje MPNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. MPNS. badchannel|Chyba MPNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav MPNS: 404 nebyl nalezen).|Žádné dimenze|
|odchozí. MPNS. omezení|MPNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 není přijatelné).|Žádné dimenze|
|odchozí. MPNS. invalidnotificationformat|MPNS neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část oznámení byla příliš velká.|Žádné dimenze|
|odchozí. MPNS. channeldisconnected|MPNS kanál odpojen|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci byl odpojen (stav MPNS: 412 nebyl nalezen).|Žádné dimenze|
|odchozí. MPNS. vyřazeno|MPNS Vyřazená oznámení|Počet|Celkem|Počet nabídek, které byly vyřazeny pomocí MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo potlačeno).|Žádné dimenze|
|odchozí. MPNS. pnserror|MPNS chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádné dimenze|
|odchozí. MPNS. authenticationerror|MPNS chyby ověřování|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|notificationhub. push|Všechna odchozí oznámení|Počet|Celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|příchozí. All. – žádosti|Všechny příchozí žádosti|Počet|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádné dimenze|
|příchozí. All. failedrequests|Všechny příchozí neúspěšné požadavky|Počet|Celkem|Celkový počet příchozích neúspěšných žádostí pro Centrum oznámení|Žádné dimenze|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_% Free uzlů inode|% Bezplatného uzlů inode|Počet|Průměr|Average_% Free uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Počet|Průměr|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využití uzlů inode|% Použitého uzlů inode|Počet|Průměr|Average_% využití uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ využité místo|% Využitého místa|Počet|Průměr|Average_ využité místo|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přečtených bajtů za sekundu|Bajty čtení z disku/s|Počet|Průměr|Average_Disk přečtených bajtů za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení z disku/s|Počet|Průměr|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosy za sekundu|Přenosy disku/s|Počet|Průměr|Average_Disk přenosy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapsané bajty/s|Bajty zápisu na disk/s|Počet|Průměr|Average_Disk zapsané bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zápisy za sekundu|Zápisy na disk/s|Počet|Průměr|Average_Disk zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Počet|Průměr|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical disk bajty/s|Bajty logického disku/s|Počet|Průměr|Average_Logical disk bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ dostupná paměť v%|% Dostupné paměti|Počet|Průměr|Average_ dostupná paměť v%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Počet|Průměr|Average_% dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ využité paměti|% Využité paměti|Počet|Průměr|Average_ využité paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využitého místa odkládacího souboru|% Využitého místa odkládacího souboru|Počet|Průměr|Average_% využitého místa odkládacího souboru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Available v MB|Dostupná paměť v MB|Počet|Průměr|Paměť Average_Available v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB – swap|Dostupný počet MB swap|Počet|Průměr|Average_Available MB – swap|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page čtení za sekundu|Čtení stránek/s|Počet|Průměr|Average_Page čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zápisy za sekundu|Zápisy stránek/s|Počet|Průměr|Average_Page zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stránky/s|Počet|Průměr|Average_Pages/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used počet MB odkládacího prostoru|Využité místo odkládacího souboru v MB|Počet|Průměr|Average_Used počet MB odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Used MB paměti|Využitá paměť v MB|Počet|Průměr|Paměť Average_Used MB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přenesené bajty Average_Total|Celkový počet odeslaných bajtů|Počet|Průměr|Přenesené bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Počet|Průměr|Přijaté bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Bajty celkem|Počet|Průměr|Average_Total bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané pakety Average_Total|Celkový počet odeslaných paketů|Počet|Průměr|Odeslané pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté pakety Average_Total|Celkový počet přijatých paketů|Počet|Průměr|Přijaté pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total chyby příjmu|Celkový počet chyb příjmu|Počet|Průměr|Average_Total chyby příjmu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Chyby Average_Total TX|Chyby odesílání celkem|Počet|Průměr|Chyby Average_Total TX|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Počet|Průměr|Average_Total kolizí|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední doba disku/čtení|Počet|Průměr|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední doba disku/přenos|Počet|Průměr|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/zápis|Střední doba disku/zápis|Počet|Průměr|Average_Avg. Doba disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical disk bajty/s|Bajty fyzického disku/s|Počet|Průměr|Average_Physical disk bajty/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegovaného času|Privilegovaný čas protokolu PCT|Počet|Průměr|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_Pct|Doba uživatele v protokolu PCT|Počet|Průměr|Čas uživatele Average_Pct|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used kB paměti|Využitá paměť v kilobajtech|Počet|Průměr|Average_Used kB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Sdílená paměť Average_Virtual|Virtuální sdílená paměť|Počet|Průměr|Sdílená paměť Average_Virtual|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času DPC|% Času DPC|Počet|Průměr|Average_% času DPC|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času nečinnosti|% Času nečinnosti|Počet|Průměr|Average_% času nečinnosti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času přerušení|% Času přerušení|Počet|Průměr|Average_% času přerušení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času čekání na v/v|% Času čekání na v/v|Počet|Průměr|Average_% času čekání na v/v|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dobrý čas|% Dobrý čas|Počet|Průměr|Average_% dobrý čas|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegovaného času|% Privilegovaného času|Počet|Průměr|Average_% privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|% Času procesoru|Počet|Průměr|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% uživatelského času|% Uživatelského času|Počet|Průměr|Average_% uživatelského času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fyzická paměť|Volná fyzická paměť|Počet|Průměr|Average_Free fyzická paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free místo ve stránkovacích souborech|Volné místo ve stránkovacích souborech|Počet|Průměr|Average_Free místo ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Virtuální paměť Average_Free|Volná virtuální paměť|Počet|Průměr|Virtuální paměť Average_Free|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Počet|Průměr|Average_Processes|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size uložené ve stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Počet|Průměr|Average_Size uložené ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Doba provozu|Počet|Průměr|Average_Uptime|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Uživatelé|Počet|Průměr|Average_Users|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/čtení|Střední doba disku/čtení|Počet|Průměr|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/zápis|Střední doba disku/zápis|Počet|Průměr|Average_Avg. Doba disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Počet|Průměr|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení z disku/s|Počet|Průměr|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosy za sekundu|Přenosy disku/s|Počet|Průměr|Average_Disk přenosy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zápisy za sekundu|Zápisy na disk/s|Počet|Průměr|Average_Disk zápisy za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Počet|Průměr|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Počet|Průměr|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostupné MB|Počet|Průměr|Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% používané svěřené bajty|% Používaných potvrzených bajtů|Počet|Průměr|Average_% používané svěřené bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes přijaté za sekundu|Přijaté bajty/s|Počet|Průměr|Average_Bytes přijaté za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes odeslané za sekundu|Odeslané bajty/s|Počet|Průměr|Average_Bytes odeslané za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Počet|Průměr|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|% Času procesoru|Počet|Průměr|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Počet|Průměr|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Signály|Signály|Počet|Celkem|Signály|Počítač, OSType, verze, SourceComputerId|
|Aktualizovat|Aktualizovat|Počet|Průměr|Aktualizovat|Počítač, produkt, klasifikace, UpdateState, volitelné, schválené|
|Událost|Událost|Počet|Průměr|Událost|Zdroj, protokol událostí, počítač, EventCategory, EventLevel, EventLevelName, ID události|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazu DAX v posledním intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|QPU vysoké využití|Počet|Celkem|QPU vysoké využití za poslední minutu, 1 pro vysoké využití QPU, jinak 0|Žádné dimenze|
|memory_metric|Paměť|Psaný|Průměr|Rezident. Rozsah 0-3 GB pro a1, 0-5 GB pro a2, 0-10 GB pro a3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|Žádné dimenze|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections – úspěch|ListenerConnections – úspěch|Počet|Celkem|Úspěšně se ListenerConnections pro Microsoft. Relay.|entityName|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Počet|Celkem|ClientError v ListenerConnections pro Microsoft. Relay|entityName|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Počet|Celkem|ServerError v ListenerConnections pro Microsoft. Relay|entityName|
|SenderConnections – úspěch|SenderConnections – úspěch|Počet|Celkem|Úspěšně se SenderConnections pro Microsoft. Relay.|entityName|
|SenderConnections – ClientError|SenderConnections – ClientError|Počet|Celkem|ClientError v SenderConnections pro Microsoft. Relay|entityName|
|SenderConnections – ServerError|SenderConnections – ServerError|Počet|Celkem|ServerError v SenderConnections pro Microsoft. Relay|entityName|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Počet|Celkem|Total ListenerConnections for Microsoft. Relay|entityName|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Počet|Celkem|Požadavky SenderConnections (celkem) pro Microsoft. Relay|entityName|
|ActiveConnections|ActiveConnections|Počet|Celkem|Total ActiveConnections for Microsoft. Relay|entityName|
|ActiveListeners|ActiveListeners|Počet|Celkem|Total ActiveListeners for Microsoft. Relay|entityName|
|BytesTransferred|BytesTransferred|Počet|Celkem|Total BytesTransferred for Microsoft. Relay|entityName|
|ListenerDisconnects|ListenerDisconnects|Počet|Celkem|Total ListenerDisconnects for Microsoft. Relay|entityName|
|SenderDisconnects|SenderDisconnects|Počet|Celkem|Total SenderDisconnects for Microsoft. Relay|entityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence hledání|Sekund|Průměr|Průměrná latence hledání pro vyhledávací službu|Žádné dimenze|
|SearchQueriesPerSecond|Hledání dotazů za sekundu|CountPerSecond|Průměr|Hledání dotazů za sekundu pro vyhledávací službu|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Procento|Průměr|Procento vyhledávacích dotazů, které byly pro vyhledávací službu omezené|Žádné dimenze|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů (Preview)|entityName|
|ServerErrors|Chyby serveru. (Preview)|Počet|Celkem|Chyby serveru pro Microsoft. ServiceBus (Preview)|entityName|
|UserErrors|Chyby uživatele. (Preview)|Počet|Celkem|Chyby uživatele pro Microsoft. ServiceBus (Preview)|entityName|
|ThrottledRequests|Omezené požadavky. (Preview)|Počet|Celkem|Omezené požadavky pro Microsoft. ServiceBus (Preview)|entityName|
|IncomingRequests|Příchozí žádosti (Preview)|Počet|Celkem|Příchozí požadavky pro Microsoft. ServiceBus (Preview)|entityName|
|IncomingMessages|Příchozí zprávy (Preview)|Počet|Celkem|Příchozí zprávy pro Microsoft. ServiceBus (Preview)|entityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Počet|Celkem|Odchozí zprávy pro Microsoft. ServiceBus (Preview)|entityName|
|ActiveConnections|ActiveConnections (Preview)|Počet|Celkem|Celkový počet aktivních připojení pro Microsoft. ServiceBus (Preview)|Žádné dimenze|
|Velikost|Velikost (Preview)|Psaný|Průměr|Velikost fronty nebo tématu v bajtech (Preview)|entityName|
|Zprávy|Počet zpráv ve frontě nebo tématu. (Preview)|Počet|Průměr|Počet zpráv ve frontě nebo tématu. (Preview)|entityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|Počet|Průměr|Počet aktivních zpráv ve frontě nebo tématu. (Preview)|entityName|
|DeadletteredMessages|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|Počet|Průměr|Počet nedoručených zpráv ve frontě nebo tématu (Preview)|entityName|
|ScheduledMessages|Počet naplánovaných zpráv ve frontě nebo tématu. (Preview)|Počet|Průměr|Počet naplánovaných zpráv ve frontě nebo tématu. (Preview)|entityName|
|CPUXNS|Využití CPU na obor názvů|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus úrovně Premium|Žádné dimenze|
|WSXNS|Využití velikosti paměti na obor názvů|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium|Žádné dimenze|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikace

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Počet|Průměr|Procesor přidělený tomuto kontejneru v millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Psaný|Průměr|Paměť přidělená tomuto kontejneru v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Počet|Průměr|Skutečné využití CPU v millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Psaný|Průměr|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Počet|Průměr|Stav aplikace Service Fabric mřížka|ApplicationName, status|
|ServiceStatus|ServiceStatus|Počet|Průměr|Stav služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Počet|Průměr|Stav repliky služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Počet|Průměr|Stav kontejneru v aplikaci Service Fabric mřížka|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Počet|Průměr|Restartování počtu kontejnerů v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Počet|Maximum|Množství připojení uživatele.|Koncový bod|
|MessageCount|Počet zpráv|Počet|Celkem|Celková velikost zpráv|Žádné dimenze|
|InboundTraffic|Příchozí provoz|Psaný|Celkem|Příchozí provoz služby|Žádné dimenze|
|OutboundTraffic|Odchozí provoz|Psaný|Celkem|Odchozí provoz služby|Žádné dimenze|
|UserErrors|Chyby uživatele|Procento|Maximum|Procento uživatelských chyb|Žádné dimenze|
|SystemErrors|Systémové chyby|Procento|Maximum|Procento systémových chyb|Žádné dimenze|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v. Neplatí pro datové sklady.|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro databáze založené na DTU.|Žádné dimenze|
|úložiště|Využité místo pro data|Psaný|Maximum|Celková velikost databáze Neplatí pro datové sklady.|Žádné dimenze|
|connection_successful|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádné dimenze|
|connection_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|blocked_by_firewall|Blokováno bránou firewall|Počet|Celkem|Blokováno bránou firewall|Žádné dimenze|
|Ukončení|Zablokování|Počet|Celkem|Zablokování. Neplatí pro datové sklady.|Žádné dimenze|
|storage_percent|Procento využitého datového prostoru|Procento|Maximum|Procento velikosti databáze. Neplatí pro datové sklady nebo databáze na úrovni dat.|Žádné dimenze|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti Neplatí pro datové sklady.|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů Neplatí pro datové sklady.|Žádné dimenze|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací Neplatí pro datové sklady.|Žádné dimenze|
|dtu_limit|Limit DTU|Počet|Průměr|Limit DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|dtu_used|Využité DTU|Počet|Průměr|Používá se DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro databáze založené na vCore.|Žádné dimenze|
|cpu_used|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro databáze založené na vCore.|Žádné dimenze|
|dwu_limit|DWU limit|Počet|Maximum|DWU limit. Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_consumption_percent|Procento DWU|Procento|Maximum|Procento DWU Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_used|DWU použito|Počet|Maximum|DWU použito. Platí jenom pro datové sklady.|Žádné dimenze|
|dw_cpu_percent|Procento CPU na úrovni uzlu DW|Procento|Průměr|Procento CPU na úrovni uzlu DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Procento vstupně-výstupních operací dat na úrovni uzlu DW|Procento|Průměr|Procento vstupně-výstupních operací dat na úrovni uzlu DW|DwLogicalNodeId|
|cache_hit_percent|Procento přístupů do mezipaměti|Procento|Maximum|Procento přístupů do mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|cache_used_percent|Procento využité mezipaměti|Procento|Maximum|Procento využité mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|local_tempdb_usage_percent|Místní procento databáze tempdb|Procento|Průměr|Místní procento databáze tempdb. Platí jenom pro datové sklady.|Žádné dimenze|
|app_cpu_billed|CPU aplikace se fakturuje.|Počet|Celkem|CPU aplikace se fakturuje. Platí pro databáze bez serveru.|Žádné dimenze|
|app_cpu_percent|Procento využití procesoru aplikací|Procento|Průměr|Procento využití procesoru aplikací Platí pro databáze bez serveru.|Žádné dimenze|
|app_memory_percent|Procento využité paměti aplikace|Procento|Průměr|Procento využité paměti aplikace Platí pro databáze bez serveru.|Žádné dimenze|
|allocated_data_storage|Přidělené datové místo|Psaný|Průměr|Přidělené datové místo Neplatí pro datové sklady.|Žádné dimenze|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/servery/elasticPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_percent|Procento využitého datového prostoru||Procento|Průměr|Procento úložiště|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|Žádné dimenze|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací|Žádné dimenze|
|eDTU_limit|limit eDTU|Počet|Průměr|limit eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_limit|Maximální velikost dat|Psaný|Průměr|Omezení úložiště|Žádné dimenze|
|eDTU_used|využité eDTU|Počet|Průměr|používá se eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|storage_used|Využité místo pro data|Psaný|Průměr|Využité úložiště|Žádné dimenze|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti|Žádné dimenze|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|cpu_used|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|allocated_data_storage|Přidělené datové místo|Psaný|Průměr|Přidělené datové místo|Žádné dimenze|
|allocated_data_storage_percent|Procentuální hodnota přiděleného datového prostoru|Procento|Maximum|Procentuální hodnota přiděleného datového prostoru|Žádné dimenze|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Počet|Průměr|Počet virtuálních jader|Žádné dimenze|
|avg_cpu_percent|Průměrné procento procesoru|Procento|Průměr|Průměrné procento procesoru|Žádné dimenze|
|reserved_storage_mb|Rezervované místo v úložišti|Počet|Průměr|Rezervované místo v úložišti|Žádné dimenze|
|storage_space_used_mb|Využité místo úložiště|Počet|Průměr|Využité místo úložiště|Žádné dimenze|
|io_requests|Počet požadavků v/v|Počet|Průměr|Počet požadavků v/v|Žádné dimenze|
|io_bytes_read|Přečtené vstupně-výstupní bajty|Psaný|Průměr|Přečtené vstupně-výstupní bajty|Žádné dimenze|
|io_bytes_written|Zapsané vstupně-výstupní bajty|Psaný|Průměr|Zapsané vstupně-výstupní bajty|Žádné dimenze|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Využitá kapacita|Psaný|Průměr|Kapacita využitého účtu|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Psaný|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Psaný|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Latence úspěch E2E|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita objektu BLOB|Psaný|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů BLOB|Počet|Celkem|Počet objektů BLOB v Blob service účtu úložiště|blobType|       |BlobCount|Počet objektů BLOB|Počet|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů objektů BLOB|Počet|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádné dimenze|
|IndexCapacity|Kapacita indexu|Psaný|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Psaný|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Psaný|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Latence úspěch E2E|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita souboru|Psaný|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Žádné dimenze|
|FileCount|Počet souborů|Počet|Průměr|Počet souborů v Souborové službě účtu úložiště.|Žádné dimenze|
|FileShareCount|Počet sdílených souborů|Počet|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Psaný|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Psaný|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Latence úspěch E2E|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Psaný|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádné dimenze|
|QueueCount|Počet front|Počet|Průměr|Počet front v Služba front účtu úložiště.|Žádné dimenze|
|QueueMessageCount|Počet zpráv ve frontě|Počet|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Psaný|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Psaný|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Latence úspěch E2E|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita tabulky|Psaný|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádné dimenze|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek v Table service účtu úložiště|Žádné dimenze|
|TableEntityCount|Počet entit tabulky|Počet|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Psaný|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Psaný|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Latence úspěch E2E|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Výsledek relace synchronizace|Počet|Průměr|Metrika, která protokoluje hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synchronizované bajty|Psaný|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Počet|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když registrovaný server úspěšně zaznamená prezenční signál s koncovým bodem cloudu|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Stažení vrstvení cloudu|Psaný|Celkem|Celková velikost dat vrácených serverem|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|Využití SU%|Procento|Maximum|Využití SU%|ID logického, PartitionId|
|InputEvents|Události vstupu|Počet|Celkem|Události vstupu|ID logického, PartitionId|
|InputEventBytes|Bajty vstupních událostí|Psaný|Celkem|Bajty vstupních událostí|ID logického, PartitionId|
|LateInputEvents|Zpožděné vstupní události|Počet|Celkem|Zpožděné vstupní události|ID logického, PartitionId|
|OutputEvents|Výstupní události|Počet|Celkem|Výstupní události|ID logického, PartitionId|
|ConversionErrors|Chyby převodu dat|Počet|Celkem|Chyby převodu dat|ID logického, PartitionId|
|Chyby|Běhové chyby|Počet|Celkem|Běhové chyby|ID logického, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Počet|Celkem|Události mimo pořadí|ID logického, PartitionId|
|AMLCalloutRequests|Žádosti o funkce|Počet|Celkem|Žádosti o funkce|ID logického, PartitionId|
|AMLCalloutFailedRequests|Neúspěšné žádosti o funkce|Počet|Celkem|Neúspěšné žádosti o funkce|ID logického, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Počet|Celkem|Události funkcí|ID logického, PartitionId|
|DeserializationError|Chyby při deserializaci vstupu|Počet|Celkem|Chyby při deserializaci vstupu|ID logického, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Počet|Celkem|Události předčasného vstupu|ID logického, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekund|Maximum|Zpoždění vodoznaku|ID logického, PartitionId|
|InputEventsSourcesBacklogged|Nevyřízené události vstupu|Počet|Maximum|Nevyřízené události vstupu|ID logického, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Počet|Celkem|Přijaté vstupní zdroje|ID logického, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv načtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Psaný|Celkem|Počet přečtených bajtů ze všech zdrojů událostí|Žádné dimenze|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Psaný|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekund|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávaných v příchozím přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Psaný|Celkem|Počet přečtených bajtů ze zdroje události|Žádné dimenze|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Psaný|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekund|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zprávy zpracovávaných v příchozím přenosu dat|Žádné dimenze|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty čtení z disku/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|DiskWriteBytesPerSecond|Bajty zápisu na disk/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací zápisu v období vzorkování.|Žádné dimenze|
|Bajty čtení z disku|Bajty čtení z disku|Psaný|Celkem|Celková propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|Bajty zápisu na disk|Bajty zápisu na disk|Psaný|Celkem|Celková propustnost disku v důsledku operací zápisu v období vzorkování.|Žádné dimenze|
|DiskReadOperations|Operace čtení z disku|Počet|Celkem|Počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskWriteOperations|Operace zápisu na disk|Počet|Celkem|Počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Průměrný počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskReadLatency|Latence čtení disku|Milisekund|Průměr|Celková latence čtení Součet latencí čtení zařízení a jádra.|Žádné dimenze|
|DiskWriteLatency|Latence zápisu na disk|Milisekund|Průměr|Celková latence zápisu Součet latencí zápisu zařízení a jádra.|Žádné dimenze|
|NetworkInBytesPerSecond|Síť v bajtech/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přijatý provoz.|Žádné dimenze|
|NetworkOutBytesPerSecond|Výstupní bajty sítě/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|Psaný|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|Psaný|Celkem|Celková propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|MemoryUsed|Využitá paměť|Psaný|Průměr|Velikost paměti počítače, kterou virtuální počítač používá.|Žádné dimenze|
|MemoryGranted|Přidělená paměť|Psaný|Průměr|Velikost paměti, která byla k virtuálnímu počítači udělena hostitelem. Hostiteli není pro hostitele udělené, dokud se nedotknete jednou, a pokud VMkernel potřebuje paměť, může se tato paměť vyměnit.|Žádné dimenze|
|MemoryActive|Paměť aktivní|Psaný|Průměr|Velikost paměti, kterou virtuální počítač využíval v posledních malých oknech času. Toto je "pravdivý" počet paměti, které virtuální počítač v současnosti potřebuje. Dodatečná, nevyužitá paměť může být vyměněna nebo v bublině bez dopadu na výkon hosta.|Žádné dimenze|
|Procento CPU|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je hlášena s 100%, která představuje všechny jádra procesoru v systému. Příklad: oboustranný virtuální počítač, který používá 50% systému se čtyřmi jádry, plně používá dvě jádra.|Žádné dimenze|
|PercentageCpuReady|Procento připraveného procesoru|Milisekund|Celkem|Čas připravenosti je doba, po kterou se bude čekat na dostupnost PROCESORů v minulém intervalu aktualizace.|Žádné dimenze|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Počet|Průměr|Délka fronty http|Instance|
|BytesReceived|Data v|Psaný|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Psaný|Celkem|Výstupní data|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/weby (kromě funkcí)

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekund|Celkem|Čas procesoru|Instance|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data v|Psaný|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Psaný|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Psaný|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Psaný|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekund|Průměr|Průměrná doba odezvy|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Řeší|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vláken|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Psaný|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Počet|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Počet|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Počet|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Počet|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Počet|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Počet|Celkem|Uvolňování paměti 2. generace|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/lokality (funkce)

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Data v|Psaný|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Psaný|Celkem|Výstupní data|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Psaný|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Psaný|Průměr|Průměrná pracovní sada paměti|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|MB/milisekundy|Celkem|[Jednotky spuštění funkce](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|PrivateBytes|Soukromé bajty|Psaný|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Počet|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Počet|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Počet|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Počet|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Počet|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Počet|Celkem|Uvolňování paměti 2. generace|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekund|Celkem|Čas procesoru|Instance|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data v|Psaný|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Psaný|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Psaný|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Psaný|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekund|Průměr|Průměrná doba odezvy|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|Počet|Celkem|Jednotky spuštění funkce|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Řeší|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vláken|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Psaný|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Počet|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Počet|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Počet|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Počet|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Počet|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Počet|Celkem|Uvolňování paměti 2. generace|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky|Požadavky|Počet|Celkem|Požadavky|Instance|
|BytesReceived|Data v|Psaný|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Psaný|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekund|Průměr|Průměrná doba odezvy|Instance|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Počet|Průměr|Délka fronty http|Instance|
|ActiveRequests|Aktivní požadavky|Počet|Celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet front-endy|Počet|Průměr|Celkový počet front-endy|Žádné dimenze|
|SmallAppServicePlanInstances|Plánování pracovníků malých App Service|Počet|Průměr|Plánování pracovníků malých App Service|Žádné dimenze|
|MediumAppServicePlanInstances|Střední App Service plánování pracovních procesů|Počet|Průměr|Střední App Service plánování pracovních procesů|Žádné dimenze|
|LargeAppServicePlanInstances|Zaměstnanci s velkými App Servicey plánu|Počet|Průměr|Zaměstnanci s velkými App Servicey plánu|Žádné dimenze|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WorkersTotal|Celkový počet pracovníků|Počet|Průměr|Celkový počet pracovníků|Žádné dimenze|
|WorkersAvailable|Zaměstnanci, kteří jsou k dispozici|Počet|Průměr|Zaměstnanci, kteří jsou k dispozici|Žádné dimenze|
|WorkersUsed|Využívané pracovní procesy|Počet|Průměr|Využívané pracovní procesy|Žádné dimenze|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|

## <a name="next-steps"></a>Další kroky
* [Přečtěte si o metrikách v Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Exportovat metriky do úložiště, centra událostí nebo Log Analytics](resource-logs-overview.md)
