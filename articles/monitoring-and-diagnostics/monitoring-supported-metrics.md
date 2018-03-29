---
title: Azure metriky monitorování - podporované metriky na typ prostředku | Microsoft Docs
description: Seznam metriky, které jsou dostupné pro jednotlivé typy prostředků s Azure monitorování.
author: anirudhcavale
manager: ashwink
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: ancav
ms.openlocfilehash: fc7a4062af9e73383af46f0c36eb22710ce9b6e1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s monitorováním Azure
Monitorování Azure poskytuje několik způsobů, jak pracovat s metriky, včetně grafů, je na portálu, k nim přistupovat pomocí rozhraní REST API nebo je dotazování pomocí prostředí PowerShell nebo rozhraní příkazového řádku. Níže je úplný seznam všech metriky aktuálně k dispozici s Azure monitorování metriky kanálu.

> [!NOTE]
> Další metriky může být k dispozici v portálu nebo pomocí starší verze rozhraní API. Tento seznam obsahuje pouze metriky, které jsou k dispozici konsolidované monitorování Azure metriky zřetězením příkazů. Vyhledat a přístup k metriky s dimenzí použijte prosím [verze rozhraní api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 S1, 0 – 200 S2 a 0 – 400 pro S4|ServerResourceType|
|memory_metric|Paměť|bajtů|Průměr|Paměť. V rozsahu 0-25 GB pro S1, 0 – 50 GB pro S2 a 0 – 100 GB pro S4|ServerResourceType|
|TotalConnectionRequests|Žádosti o připojení (celkem)|Počet|Průměr|Požadavky na celkový počet připojení. Jedná se o doručení.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšné připojení za sekundu|CountPerSecond|Průměr|Počet dokončených úspěšné připojení.|ServerResourceType|
|TotalConnectionFailures|Chyby připojení (celkem)|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení.|ServerResourceType|
|CurrentUserSessions|Aktuální relace uživatele|Počet|Průměr|Aktuální počet uživatelských relací navázat.|ServerResourceType|
|QueryPoolBusyThreads|Dotaz z fondu podprocesů zaneprázdněn|Počet|Průměr|Počet vytížených vláken ve fondu vláken dotazu.|ServerResourceType|
|CommandPoolJobQueueLength|Příkaz délku fronty úloh|Počet|Průměr|Počet úloh ve frontě příkaz fondu vláken.|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet úloh jiný I/O ve frontě fondu zpracování vláken.|ServerResourceType|
|CurrentConnections|Připojení: Aktuální připojení|Počet|Průměr|Aktuální počet připojení klienta.|ServerResourceType|
|CleanerCurrentPrice|Paměť: Čisticí aktuální cena|Počet|Průměr|Aktuální cena paměti, $a bajtů/čas, normalizovány na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: Čisticí paměti vypočítat|bajtů|Průměr|Množství paměti v bajtech, podstoupí čisticí vyprazdňování pozadím.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: Čisticí nonshrinkable paměti|bajtů|Průměr|Množství paměti v bajtech, není v souladu čisticí vyprazdňování pozadím.|ServerResourceType|
|Parametru MemoryUsage|Paměti: Využití paměti|bajtů|Průměr|Využití paměti procesem serveru v rámci výpočet ceny čisticí paměti. Rovná se čítač Process\PrivateBytes plus velikost dat mapované paměti, ignoruje všechny paměti, které bylo namapované nebo přidělené modul xVelocity analýzy v paměti (VertiPaq) překračující modul xVelocity Limit paměti.|ServerResourceType|
|MemoryLimitHard|Paměti: Pevný Limit paměti|bajtů|Průměr|Omezení pevné paměti z konfiguračního souboru.|ServerResourceType|
|MemoryLimitHigh|Paměť: Omezení paměti vysoká|bajtů|Průměr|Limit velkého množství paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměti: Nízká Limit paměti|bajtů|Průměr|Limit nedostatek paměti z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměti: VertiPaq Limit paměti|bajtů|Průměr|Omezení v paměti z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: kvóty|bajtů|Průměr|Aktuální kvótu paměti, v bajtech. Kvótu paměti se také označuje jako rezervace paměti grant nebo paměti.|ServerResourceType|
|QuotaBlocked|Paměti: Blokované kvótu|Počet|Průměr|Aktuální počet požadavků kvóty, které jsou blokovaný, dokud jsou uvolněny kvóty další paměť.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkovaného fondu|bajtů|Průměr|Bajtů paměti uzamčena v pracovní sadě pro použití stroj v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq stránkovaného fondu|bajtů|Průměr|Bajty stránkovaného paměti používané pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Řádky čtení za sekundu|CountPerSecond|Průměr|Počet řádků přečíst ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: Řádky převést za sekundu|CountPerSecond|Průměr|Počet řádků převést během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: Řádků zapsaných za sekundu|CountPerSecond|Průměr|Počet řádků zapsaných během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vláken: Příkaz zaneprázdněn z fondu podprocesů|Počet|Průměr|Počet vytížených vláken ve fondu vláken příkaz.|ServerResourceType|
|CommandPoolIdleThreads|Vláken: Příkaz nečinných vláken fondu|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkaz.|ServerResourceType|
|LongParsingBusyThreads|Vláken: Analýza zaneprázdněn vláken dlouho|Počet|Průměr|Počet vytížených vláken ve fondu vláken dlouho analýzy.|ServerResourceType|
|LongParsingIdleThreads|Vláken: Analýza nečinných vláken dlouho|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouho analýzy.|ServerResourceType|
|LongParsingJobQueueLength|Vláken: Analýza dlouho délka fronty úloh|Počet|Průměr|Počet úloh ve frontě dlouho analýzy fondu vláken.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vláken: Fond zaneprázdněn vstupně-výstupní úlohy vláken zpracování|Počet|Průměr|Počet vláken, které jsou spuštěné úlohy vstupně-výstupních operací ve fondu zpracování vláken.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vláken: Zaneprázdněný jiný I/O vláken fondu zpracování|Počet|Průměr|Počet vláken, které jsou spuštěné úlohy bez I/O ve fondu zpracování vláken.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vláken: Fond délka fronty vstupně-výstupní úlohy zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve frontě fondu zpracování vláken.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vláken: Fond nečinnosti vstupně-výstupní úlohy vláken zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve fondu zpracování vláken nečinných vláken.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vláken: Nečinnosti vláken jiný I/O fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu zpracování vláken, který je vyhrazený pro jiný I/O úlohy.|ServerResourceType|
|QueryPoolIdleThreads|Vláken: Dotaz nečinných vláken fondu|Počet|Průměr|Počet vstupně-výstupních úloh ve fondu zpracování vláken nečinných vláken.|ServerResourceType|
|QueryPoolJobQueueLength|Vláken: Dotaz lengt fronty úloh fondu|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|ServerResourceType|
|ShortParsingBusyThreads|Vláken: Analýza zaneprázdněn vláken krátké|Počet|Průměr|Počet vytížených vláken v krátké analýzy fondu vláken.|ServerResourceType|
|ShortParsingIdleThreads|Vláken: Analýza nečinných vláken krátké|Počet|Průměr|Počet nečinných vláken v krátké analýzy fondu vláken.|ServerResourceType|
|ShortParsingJobQueueLength|Vláken: Analýza délka fronty úloh krátké|Počet|Průměr|Počet úloh ve frontě krátké analýzy fondu vláken.|ServerResourceType|
|memory_thrashing_metric|Zahlcení paměti|Procenta|Průměr|Průměrná paměti zahlcení.|ServerResourceType|
|mashup_engine_qpu_metric|Modul M QPU|Počet|Průměr|Využití QPU procesy modul hybridní webové aplikace|ServerResourceType|
|mashup_engine_memory_metric|M modul paměti|bajtů|Průměr|Využití paměti procesy modul hybridní webové aplikace|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Celkový počet brány požadavků|Počet|celkem|Počet požadavků na bránu|Location, Hostname|
|SuccessfulRequests|Požadavky pro úspěšné brány|Počet|celkem|Počet požadavků úspěšná brány|Location, Hostname|
|UnauthorizedRequests|Požadavky neoprávněným brány|Počet|celkem|Počet požadavků neoprávněným brány|Location, Hostname|
|FailedRequests|Požadavky na bránu|Počet|celkem|Počet selhání v žádostech o brány|Location, Hostname|
|OtherRequests|Ostatní požadavky brány|Počet|celkem|Počet požadavků na jiné brány|Location, Hostname|
|Doba trvání|Celková doba trvání brány požadavků|Milisekundy|Průměr|Celkové doby trvání z brány požadavků v milisekundách|Location, Hostname|
|Kapacita|Kapacita (Preview)|Procenta|Maximálně|Metrika využití služby ApiManagement|Místo|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Počet|celkem|Celkový počet úloh|RunbookName, stav|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Vyhrazené počet jader|Počet|celkem|Celkový počet vyhrazených jader na účtu batch|Žádné dimenze|
|TotalNodeCount|Počet vyhrazených uzlů|Počet|celkem|Celkový počet vyhrazených uzlů na účtu batch|Žádné dimenze|
|LowPriorityCoreCount|Počet jader LowPriority|Počet|celkem|Celkový počet nízkou prioritu jader na účtu batch|Žádné dimenze|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Počet|celkem|Celkový počet uzlů nízkou prioritu na účtu batch|Žádné dimenze|
|CreatingNodeCount|Vytváření počet uzlů|Počet|celkem|Počet uzlů, které vytváří|Žádné dimenze|
|StartingNodeCount|Počáteční počet uzlů|Počet|celkem|Počet uzlů spouštění|Žádné dimenze|
|WaitingForStartTaskNodeCount|Čeká se na pro počet uzlů spuštění úloh|Počet|celkem|Počet uzlů čekání na dokončení úlohy spustit|Žádné dimenze|
|StartTaskFailedNodeCount|Spuštění úlohy se nezdařilo počet uzlů|Počet|celkem|Počet uzlů, kde se nezdařilo spustit úlohu|Žádné dimenze|
|IdleNodeCount|Počet nečinných uzlů|Počet|celkem|Počet nečinných uzlů|Žádné dimenze|
|OfflineNodeCount|Offline počet uzlů|Počet|celkem|Počet offline uzlů|Žádné dimenze|
|RebootingNodeCount|Restartování počet uzlů|Počet|celkem|Počet restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|Obnovování počet uzlů|Počet|celkem|Počet uzlů obnovování|Žádné dimenze|
|RunningNodeCount|Spuštění počet uzlů|Počet|celkem|Počet spuštěných uzly|Žádné dimenze|
|LeavingPoolNodeCount|Opouštění počet uzlů fondu|Počet|celkem|Počet uzlů a fondu|Žádné dimenze|
|UnusableNodeCount|Nepoužitelná počet uzlů|Počet|celkem|Počet nepoužitelná uzlů|Žádné dimenze|
|PreemptedNodeCount|Zrušené počet uzlů|Počet|celkem|Počet uzlů zrušené|Žádné dimenze|
|TaskStartEvent|Úloha spuštění události|Počet|celkem|Celkový počet úloh, které byly zahájeny|Žádné dimenze|
|TaskCompleteEvent|Dokončení událostí úlohy|Počet|celkem|Celkový počet úloh, které byly dokončeny|Žádné dimenze|
|TaskFailEvent|Událostí selhání úlohy|Počet|celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|Žádné dimenze|
|PoolCreateEvent|Události vytváření fondu|Počet|celkem|Celkový počet fondy, které byly vytvořeny|Žádné dimenze|
|PoolResizeStartEvent|Události spuštění změny velikosti fondu|Počet|celkem|Celkový počet změní velikost fondu, které byly zahájeny|Žádné dimenze|
|PoolResizeCompleteEvent|Dokončení události změny velikosti fondu|Počet|celkem|Celkový počet změní velikost fondu, které byly dokončeny|Žádné dimenze|
|PoolDeleteStartEvent|Události spuštění odstranění fondu|Počet|celkem|Celkový počet odstranění fondu, které byly zahájeny|Žádné dimenze|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Počet|celkem|Celkový počet odstranění fondu, které byly dokončeny|Žádné dimenze|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Připojení klienti|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed|Operace celkem|Počet|celkem||Žádné dimenze|
|mezipaměti|Přístupy do mezipaměti|Počet|celkem||Žádné dimenze|
|cachemisses|Neúspěšné přístupy do mezipaměti|Počet|celkem||Žádné dimenze|
|getcommands|Klíče Get|Počet|celkem||Žádné dimenze|
|setcommands|Klíče Set|Počet|celkem||Žádné dimenze|
|operationsPerSecond|Operací za sekundu|Počet|celkem||Žádné dimenze|
|evictedkeys|Vyloučené klíče|Počet|celkem||Žádné dimenze|
|totalkeys|Celkový počet klíčů|Počet|Maximálně||Žádné dimenze|
|expiredkeys|Prošlé klíče|Počet|celkem||Žádné dimenze|
|usedmemory|Použitá paměť|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss|Použitá paměť RSS|bajtů|Maximálně||Žádné dimenze|
|serverLoad|Zatížení serveru|Procenta|Maximálně||Žádné dimenze|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime|CPU|Procenta|Maximálně||Žádné dimenze|
|connectedclients0|Připojených klientů (horizontálního oddílu 0)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed0|Celkový počet operací (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|cachehits0|Přístupů k mezipaměti (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|getcommands0|Získá (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|setcommands0|Nastaví (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|operationsPerSecond0|Operací za sekundu (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|evictedkeys0|Vyřazené klíče (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|totalkeys0|Celkový počet klíčů (horizontálního oddílu 0)|Počet|Maximálně||Žádné dimenze|
|expiredkeys0|Vypršela platnost klíče (horizontálního oddílu 0)|Počet|celkem||Žádné dimenze|
|usedmemory0|Použitá paměť (horizontálního oddílu 0)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss0|Použitá paměť RSS (horizontálního oddílu 0)|bajtů|Maximálně||Žádné dimenze|
|serverLoad0|Zatížení serveru (horizontálního oddílu 0)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite0|Mezipaměť zápisu (horizontálního oddílu 0)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead0|Mezipaměti pro čtení (horizontálního oddílu 0)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime0|Procesor (horizontálního oddílu 0)|Procenta|Maximálně||Žádné dimenze|
|connectedclients1|Připojených klientů (horizontálních 1)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed1|Celkový počet operací (horizontálních 1)|Počet|celkem||Žádné dimenze|
|cachehits1|Přístupů k mezipaměti (horizontálních 1)|Počet|celkem||Žádné dimenze|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních 1)|Počet|celkem||Žádné dimenze|
|getcommands1|Získá (horizontálních 1)|Počet|celkem||Žádné dimenze|
|setcommands1|Nastaví (horizontálních 1)|Počet|celkem||Žádné dimenze|
|operationsPerSecond1|Operací za sekundu (horizontálních 1)|Počet|celkem||Žádné dimenze|
|evictedkeys1|Vyřazené klíče (horizontálních 1)|Počet|celkem||Žádné dimenze|
|totalkeys1|Celkový počet klíčů (horizontálních 1)|Počet|Maximálně||Žádné dimenze|
|expiredkeys1|Vypršela platnost klíče (horizontálních 1)|Počet|celkem||Žádné dimenze|
|usedmemory1|Použitá paměť (horizontálních 1)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss1|Použitá paměť RSS (horizontálních 1)|bajtů|Maximálně||Žádné dimenze|
|serverLoad1|Zatížení serveru (horizontálních 1)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite1|Mezipaměť zápisu (horizontálních 1)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead1|Mezipaměti pro čtení (horizontálních 1)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime1|Procesor (horizontálních 1)|Procenta|Maximálně||Žádné dimenze|
|connectedclients2|Připojených klientů (horizontálních 2)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed2|Celkový počet operací (horizontálních 2)|Počet|celkem||Žádné dimenze|
|cachehits2|Přístupů k mezipaměti (horizontálních 2)|Počet|celkem||Žádné dimenze|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních 2)|Počet|celkem||Žádné dimenze|
|getcommands2|Získá (horizontálních 2)|Počet|celkem||Žádné dimenze|
|setcommands2|Nastaví (horizontálních 2)|Počet|celkem||Žádné dimenze|
|operationsPerSecond2|Operací za sekundu (horizontálních 2)|Počet|celkem||Žádné dimenze|
|evictedkeys2|Vyřazené klíče (horizontálních 2)|Počet|celkem||Žádné dimenze|
|totalkeys2|Celkový počet klíčů (horizontálních 2)|Počet|Maximálně||Žádné dimenze|
|expiredkeys2|Vypršela platnost klíče (horizontálních 2)|Počet|celkem||Žádné dimenze|
|usedmemory2|Použitá paměť (horizontálních 2)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss2|Použitá paměť RSS (horizontálních 2)|bajtů|Maximálně||Žádné dimenze|
|serverLoad2|Zatížení serveru (horizontálních 2)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite2|Mezipaměť zápisu (horizontálních 2)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead2|Mezipaměti pro čtení (horizontálních 2)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime2|Procesor (horizontálních 2)|Procenta|Maximálně||Žádné dimenze|
|connectedclients3|Připojených klientů (horizontálních 3)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed3|Celkový počet operací (horizontálních 3)|Počet|celkem||Žádné dimenze|
|cachehits3|Přístupů k mezipaměti (horizontálních 3)|Počet|celkem||Žádné dimenze|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních 3)|Počet|celkem||Žádné dimenze|
|getcommands3|Získá (horizontálních 3)|Počet|celkem||Žádné dimenze|
|setcommands3|Nastaví (horizontálních 3)|Počet|celkem||Žádné dimenze|
|operationsPerSecond3|Operací za sekundu (horizontálních 3)|Počet|celkem||Žádné dimenze|
|evictedkeys3|Vyřazené klíče (horizontálních 3)|Počet|celkem||Žádné dimenze|
|totalkeys3|Celkový počet klíčů (horizontálních 3)|Počet|Maximálně||Žádné dimenze|
|expiredkeys3|Vypršela platnost klíče (horizontálních 3)|Počet|celkem||Žádné dimenze|
|usedmemory3|Použitá paměť (horizontálních 3)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss3|Použitá paměť RSS (horizontálních 3)|bajtů|Maximálně||Žádné dimenze|
|serverLoad3|Zatížení serveru (horizontálních 3)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite3|Mezipaměť zápisu (horizontálních 3)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead3|Mezipaměti pro čtení (horizontálních 3)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime3|Procesor (horizontálních 3)|Procenta|Maximálně||Žádné dimenze|
|connectedclients4|Připojených klientů (horizontálních 4)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed4|Celkový počet operací (horizontálních 4)|Počet|celkem||Žádné dimenze|
|cachehits4|Přístupů k mezipaměti (horizontálních 4)|Počet|celkem||Žádné dimenze|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních 4)|Počet|celkem||Žádné dimenze|
|getcommands4|Získá (horizontálních 4)|Počet|celkem||Žádné dimenze|
|setcommands4|Nastaví (horizontálních 4)|Počet|celkem||Žádné dimenze|
|operationsPerSecond4|Operací za sekundu (horizontálních 4)|Počet|celkem||Žádné dimenze|
|evictedkeys4|Vyřazené klíče (horizontálních 4)|Počet|celkem||Žádné dimenze|
|totalkeys4|Celkový počet klíčů (horizontálních 4)|Počet|Maximálně||Žádné dimenze|
|expiredkeys4|Vypršela platnost klíče (horizontálních 4)|Počet|celkem||Žádné dimenze|
|usedmemory4|Použitá paměť (horizontálních 4)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss4|Použitá paměť RSS (horizontálních 4)|bajtů|Maximálně||Žádné dimenze|
|serverLoad4|Zatížení serveru (horizontálních 4)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite4|Mezipaměť zápisu (horizontálních 4)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead4|Mezipaměti pro čtení (horizontálních 4)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime4|CPU (Shard 4)|Procenta|Maximálně||Žádné dimenze|
|connectedclients5|Připojených klientů (horizontálních 5)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed5|Celkový počet operací (horizontálních 5)|Počet|celkem||Žádné dimenze|
|cachehits5|Přístupů k mezipaměti (horizontálních 5)|Počet|celkem||Žádné dimenze|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontálních 5)|Počet|celkem||Žádné dimenze|
|getcommands5|Získá (horizontálních 5)|Počet|celkem||Žádné dimenze|
|setcommands5|Nastaví (horizontálních 5)|Počet|celkem||Žádné dimenze|
|operationsPerSecond5|Operací za sekundu (horizontálních 5)|Počet|celkem||Žádné dimenze|
|evictedkeys5|Vyřazené klíče (horizontálních 5)|Počet|celkem||Žádné dimenze|
|totalkeys5|Celkový počet klíčů (horizontálních 5)|Počet|Maximálně||Žádné dimenze|
|expiredkeys5|Vypršela platnost klíče (horizontálních 5)|Počet|celkem||Žádné dimenze|
|usedmemory5|Použitá paměť (horizontálních 5)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss5|Použitá paměť RSS (horizontálních 5)|bajtů|Maximálně||Žádné dimenze|
|serverLoad5|Zatížení serveru (horizontálních 5)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite5|Mezipaměť zápisu (horizontálních 5)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead5|Mezipaměti pro čtení (horizontálních 5)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime5|CPU (Shard 5)|Procenta|Maximálně||Žádné dimenze|
|connectedclients6|Připojených klientů (horizontálních 6)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed6|Celkový počet operací (horizontálních 6)|Počet|celkem||Žádné dimenze|
|cachehits6|Přístupů k mezipaměti (horizontálních 6)|Počet|celkem||Žádné dimenze|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontálních 6)|Počet|celkem||Žádné dimenze|
|getcommands6|Získá (horizontálních 6)|Počet|celkem||Žádné dimenze|
|setcommands6|Nastaví (horizontálních 6)|Počet|celkem||Žádné dimenze|
|operationsPerSecond6|Operací za sekundu (horizontálních 6)|Počet|celkem||Žádné dimenze|
|evictedkeys6|Vyřazené klíče (horizontálních 6)|Počet|celkem||Žádné dimenze|
|totalkeys6|Celkový počet klíčů (horizontálních 6)|Počet|Maximálně||Žádné dimenze|
|expiredkeys6|Vypršela platnost klíče (horizontálních 6)|Počet|celkem||Žádné dimenze|
|usedmemory6|Použitá paměť (horizontálních 6)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss6|Použitá paměť RSS (horizontálních 6)|bajtů|Maximálně||Žádné dimenze|
|serverLoad6|Zatížení serveru (horizontálních 6)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite6|Mezipaměť zápisu (horizontálních 6)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead6|Mezipaměti pro čtení (horizontálních 6)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime6|CPU (Shard 6)|Procenta|Maximálně||Žádné dimenze|
|connectedclients7|Připojených klientů (horizontálních 7)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed7|Celkový počet operací (horizontálních 7)|Počet|celkem||Žádné dimenze|
|cachehits7|Přístupů k mezipaměti (horizontálních 7)|Počet|celkem||Žádné dimenze|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontálních 7)|Počet|celkem||Žádné dimenze|
|getcommands7|Získá (horizontálních 7)|Počet|celkem||Žádné dimenze|
|setcommands7|Nastaví (horizontálních 7)|Počet|celkem||Žádné dimenze|
|operationsPerSecond7|Operací za sekundu (horizontálních 7)|Počet|celkem||Žádné dimenze|
|evictedkeys7|Vyřazené klíče (horizontálních 7)|Počet|celkem||Žádné dimenze|
|totalkeys7|Celkový počet klíčů (horizontálních 7)|Počet|Maximálně||Žádné dimenze|
|expiredkeys7|Vypršela platnost klíče (horizontálních 7)|Počet|celkem||Žádné dimenze|
|usedmemory7|Použitá paměť (horizontálních 7)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss7|Použitá paměť RSS (horizontálních 7)|bajtů|Maximálně||Žádné dimenze|
|serverLoad7|Zatížení serveru (horizontálních 7)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite7|Mezipaměť zápisu (horizontálních 7)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead7|Mezipaměti pro čtení (horizontálních 7)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime7|Procesor (horizontálních 7)|Procenta|Maximálně||Žádné dimenze|
|connectedclients8|Připojených klientů (horizontálních 8)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed8|Celkový počet operací (horizontálních 8)|Počet|celkem||Žádné dimenze|
|cachehits8|Přístupů k mezipaměti (horizontálních 8)|Počet|celkem||Žádné dimenze|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontálních 8)|Počet|celkem||Žádné dimenze|
|getcommands8|Získá (horizontálních 8)|Počet|celkem||Žádné dimenze|
|setcommands8|Nastaví (horizontálních 8)|Počet|celkem||Žádné dimenze|
|operationsPerSecond8|Operací za sekundu (horizontálních 8)|Počet|celkem||Žádné dimenze|
|evictedkeys8|Vyřazené klíče (horizontálních 8)|Počet|celkem||Žádné dimenze|
|totalkeys8|Celkový počet klíčů (horizontálních 8)|Počet|Maximálně||Žádné dimenze|
|expiredkeys8|Vypršela platnost klíče (horizontálních 8)|Počet|celkem||Žádné dimenze|
|usedmemory8|Použitá paměť (horizontálních 8)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss8|Použitá paměť RSS (horizontálních 8)|bajtů|Maximálně||Žádné dimenze|
|serverLoad8|Zatížení serveru (horizontálních 8)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite8|Mezipaměť zápisu (horizontálních 8)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead8|Mezipaměti pro čtení (horizontálních 8)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime8|Procesor (horizontálních 8)|Procenta|Maximálně||Žádné dimenze|
|connectedclients9|Připojených klientů (horizontálních 9)|Počet|Maximálně||Žádné dimenze|
|totalcommandsprocessed9|Celkový počet operací (horizontálních 9)|Počet|celkem||Žádné dimenze|
|cachehits9|Přístupů k mezipaměti (horizontálních 9)|Počet|celkem||Žádné dimenze|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontálních 9)|Počet|celkem||Žádné dimenze|
|getcommands9|Získá (horizontálních 9)|Počet|celkem||Žádné dimenze|
|setcommands9|Nastaví (horizontálních 9)|Počet|celkem||Žádné dimenze|
|operationsPerSecond9|Operací za sekundu (horizontálních 9)|Počet|celkem||Žádné dimenze|
|evictedkeys9|Vyřazené klíče (horizontálních 9)|Počet|celkem||Žádné dimenze|
|totalkeys9|Celkový počet klíčů (horizontálních 9)|Počet|Maximálně||Žádné dimenze|
|expiredkeys9|Vypršela platnost klíče (horizontálních 9)|Počet|celkem||Žádné dimenze|
|usedmemory9|Použitá paměť (horizontálních 9)|bajtů|Maximálně||Žádné dimenze|
|usedmemoryRss9|Použitá paměť RSS (horizontálních 9)|bajtů|Maximálně||Žádné dimenze|
|serverLoad9|Zatížení serveru (horizontálních 9)|Procenta|Maximálně||Žádné dimenze|
|cacheWrite9|Mezipaměť zápisu (horizontálních 9)|BytesPerSecond|Maximálně||Žádné dimenze|
|cacheRead9|Mezipaměti pro čtení (horizontálních 9)|BytesPerSecond|Maximálně||Žádné dimenze|
|percentProcessorTime9|Procesor (horizontálních 9)|Procenta|Maximálně||Žádné dimenze|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procenta|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|bajtů|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|bajtů|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtený z disku během období monitorování|Žádné dimenze|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaný na disk během období monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procenta|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|bajtů|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|bajtů|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtený z disku během období monitorování|Žádné dimenze|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaný na disk během období monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkem volání|Počet|celkem|Celkový počet volání|Žádné dimenze|
|SuccessfulCalls|Úspěšná volání|Počet|celkem|Počet úspěšných volání|Žádné dimenze|
|TotalErrors|Celkem chyb|Počet|celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx)|Žádné dimenze|
|BlockedCalls|Blokovaná volání|Počet|celkem|Počet volání nad limit vyplývající ze sazby nebo kvóty|Žádné dimenze|
|ServerErrors|Chyby serveru|Počet|celkem|Počet volání s vnitřní chybou služby (kód odpovědi HTTP 5xx)|Žádné dimenze|
|ClientErrors|Chyby klientů|Počet|celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx)|Žádné dimenze|
|DataIn|Vstupní data|bajtů|celkem|Velikost příchozích dat v bajtech|Žádné dimenze|
|DataOut|Výstupní data|bajtů|celkem|Velikost odchozích dat v bajtech|Žádné dimenze|
|Latence|Latence|Počet milisekund|Průměr|Latence v milisekundách|Žádné dimenze|
|CharactersTranslated|Přeložené znaky|Počet|celkem|Celkový počet znaků v příchozí textové žádosti|Žádné dimenze|
|SpeechSessionDuration|Doba trvání řečové relace|Sekundy|celkem|Celková doba trvání řečové relace v sekundách|Žádné dimenze|
|TotalTransactions|Celkový počet transakcí|Počet|celkem|Celkový počet transakcí|Žádné dimenze|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procenta|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|bajtů|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|bajtů|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|bajtů|celkem|Celkový počet bajtů přečtený z disku během monitorování|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|bajtů|celkem|Celkový počet bajtů zapsaný na disk během monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity CPU|Zbývající kredity CPU|Počet|Průměr|Celkové množství kreditu, které se dá využít|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Počet|Průměr|Celkové množství kreditu, které spotřeboval virtuální počítač|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procenta|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|bajtů|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|bajtů|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|bajtů|celkem|Celkový počet bajtů přečtený z disku během monitorování|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|bajtů|celkem|Celkový počet bajtů zapsaný na disk během monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity CPU|Zbývající kredity CPU|Počet|Průměr|Celkové množství kreditu, které se dá využít|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Počet|Průměr|Celkové množství kreditu, které spotřeboval virtuální počítač|Žádné dimenze|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procenta|Průměr|Procento přidělených výpočetních jednotek, které virtuální počítače aktuálně používají|Žádné dimenze|
|Síťové vstupy|Síťové vstupy|bajtů|celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Síťové výstupy|bajtů|celkem|Počet bajtů odchozích ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Čtení z disku – bajty|Čtení z disku – bajty|bajtů|celkem|Celkový počet bajtů přečtený z disku během monitorování|Žádné dimenze|
|Zápis na disk – bajty|Zápis na disk – bajty|bajtů|celkem|Celkový počet bajtů zapsaný na disk během monitorování|Žádné dimenze|
|Čtení z disku – operace/s|Čtení z disku – operace/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Zápis na disk – operace/s|Zápis na disk – operace/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Zbývající kredity CPU|Zbývající kredity CPU|Počet|Průměr|Celkové množství kreditu, které se dá využít|Žádné dimenze|
|Spotřebované kredity CPU|Spotřebované kredity CPU|Počet|Průměr|Celkové množství kreditu, které spotřeboval virtuální počítač|Žádné dimenze|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití CPU|Počet|Průměr|Využití procesoru na všechny jader v millicores.|containerName|
|Parametru MemoryUsage|Využití paměti|bajtů|Průměr|Celkové využití paměti v bajtech.|containerName|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DCIApiCalls|Přehled rozhraní API volání zákazníka|Počet|celkem||Žádné dimenze|
|DCIMappingImportOperationSuccessfulLines|Mapování importu operace úspěšné řádky|Počet|celkem||Žádné dimenze|
|DCIMappingImportOperationFailedLines|Mapování importu se nezdařila řádky|Počet|celkem||Žádné dimenze|
|DCIMappingImportOperationTotalLines|Mapování importu operaci celkový počet řádků|Počet|celkem||Žádné dimenze|
|DCIMappingImportOperationRuntimeInSeconds|Mapování importu operaci za běhu v sekundách|Sekundy|celkem||Žádné dimenze|
|DCIOutboundProfileExportSucceeded|Odchozí profilu exportu byla úspěšná|Počet|celkem||Žádné dimenze|
|DCIOutboundProfileExportFailed|Odchozí profilu exportu se nezdařila|Počet|celkem||Žádné dimenze|
|DCIOutboundProfileExportDuration|Doba trvání odchozí profilu exportu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiExportSucceeded|Odchozí klíčového ukazatele výkonu Export byl úspěšný|Počet|celkem||Žádné dimenze|
|DCIOutboundKpiExportFailed|Odchozí klíčového ukazatele výkonu Export se nezdařil|Počet|celkem||Žádné dimenze|
|DCIOutboundKpiExportDuration|Doba trvání odchozí Export klíčového ukazatele výkonu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiExportStarted|Export odchozí klíčový ukazatel výkonu spustit|Sekundy|celkem||Žádné dimenze|
|DCIOutboundKpiRecordCount|Počet záznamů odchozí klíčového ukazatele výkonu|Sekundy|celkem||Žádné dimenze|
|DCIOutboundProfileExportCount|Počet Export odchozí profilů|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportFailed|Odchozí počáteční profilu exportu se nezdařila|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportSucceeded|Odchozí počáteční profilu exportu byla úspěšná|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportFailed|Odchozí počáteční klíčového ukazatele výkonu Export se nezdařil|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialKpiExportSucceeded|Odchozí počáteční klíčového ukazatele výkonu Export byl úspěšný|Sekundy|celkem||Žádné dimenze|
|DCIOutboundInitialProfileExportDurationInSeconds|Odchozí počáteční profilu exportu doby v sekundách|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiFailed|Úloha Adla pro standardní klíčový ukazatel výkonu se nezdařilo s|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiTimeOut|Úloha Adla pro standardní klíčový ukazatel výkonu časový limit v sekundách|Sekundy|celkem||Žádné dimenze|
|AdlaJobForStandardKpiCompleted|Úloha Adla pro standardní klíčový ukazatel výkonu dokončit v sekundách|Sekundy|celkem||Žádné dimenze|
|ImportASAValuesFailed|Hodnoty ASA importu se nezdařila, počet|Počet|celkem||Žádné dimenze|
|ImportASAValuesSucceeded|Import ASA hodnoty počtu bylo úspěšně dokončeno.|Počet|celkem||Žádné dimenze|
|DCIProfilesCount|Počet instancí profilu|Počet|Poslední||Žádné dimenze|
|DCIInteractionsPerMonthCount|Interakce na počet měsíc|Počet|Poslední||Žádné dimenze|
|DCIKpisCount|Počet klíčového ukazatele výkonu|Počet|Poslední||Žádné dimenze|
|DCISegmentsCount|Počet segmentů|Počet|Poslední||Žádné dimenze|
|DCIPredictiveMatchPoliciesCount|Prediktivní počet shod|Počet|Poslední||Žádné dimenze|
|DCIPredictionsCount|Počet předpovědi|Počet|Poslední||Žádné dimenze|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Počet|celkem||pipelineName, název aktivity activityName, windowEnd, windowStart |
|SuccessfulRuns|Úspěšná spuštění|Počet|celkem||pipelineName, název aktivity activityName, windowEnd, windowStart |

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metriky spustí kanálu se nezdařilo|Počet|celkem||FailureType, Name|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Počet|celkem||FailureType, Name|
|ActivityFailedRuns|Spustí metriky aktivity se nezdařilo|Počet|celkem||ActivityType, PipelineName, FailureType, název|
|ActivitySucceededRuns|Úspěšné metriky spuštění aktivity|Počet|celkem||ActivityType, PipelineName, FailureType, název|
|TriggerFailedRuns|Aktivační událost metriky spuštění se nezdařilo|Počet|celkem||Název, FailureType|
|TriggerSucceededRuns|Aktivační událost metriky spustí bylo úspěšné|Počet|celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití procesoru runtime integrace|Procenta|Průměr||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Dostupná paměť integrace modulu runtime|bajtů|Průměr||IntegrationRuntimeName, NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Počet|celkem|Počet neúspěšných úloh.|Žádné dimenze|
|JobEndedFailure|Neúspěšné úlohy|Počet|celkem|Počet nezdařených úloh.|Žádné dimenze|
|JobEndedCancelled|Zrušené úlohy|Počet|celkem|Počet zrušených úloh.|Žádné dimenze|
|JobAUEndedSuccess|Úspěšné AU čas|Sekundy|celkem|Celkový čas AU pro úspěšné úlohy.|Žádné dimenze|
|JobAUEndedFailure|Neúspěšné AU čas|Sekundy|celkem|Celkový čas AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedCancelled|Zrušené AU čas|Sekundy|celkem|Celkový čas AU pro zrušené úlohy.|Žádné dimenze|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Celkový počet úložiště|bajtů|Maximálně|Celkové množství dat uložených v účtu.|Žádné dimenze|
|DataWritten|Zapsaná data|bajtů|celkem|Celkové množství dat, zapisuje na účet.|Žádné dimenze|
|Přečtená data|Přečtená data|bajtů|celkem|Celkové množství dat číst z účtu.|Žádné dimenze|
|WriteRequests|Požadavků na zápis|Počet|celkem|Počet dat požadavků na zápis k účtu.|Žádné dimenze|
|ReadRequests|Požadavky pro čtení|Počet|celkem|Počet data načíst požadavky na účet.|Žádné dimenze|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procenta|Průměr|Procento využití procesoru|Žádné dimenze|
|compute_limit|Výpočetní jednotka limit|Počet|Průměr|Výpočetní jednotka limit|Žádné dimenze|
|compute_consumption_percent|Výpočetní jednotka procento|Procenta|Průměr|Výpočetní jednotka procento|Žádné dimenze|
|memory_percent|Paměť v procentech|Procenta|Průměr|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|Vstupně-výstupní operace v procentech|Procenta|Průměr|Vstupně-výstupní operace v procentech|Žádné dimenze|
|storage_percent|Procento úložiště|Procenta|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|bajtů|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště|bajtů|Průměr|Limit úložiště|Žádné dimenze|
|active_connections|Celkový počet aktivních připojení|Počet|Průměr|Celkový počet aktivních připojení|Žádné dimenze|
|connections_failed|Celkový počet selhání připojení|Počet|Průměr|Celkový počet selhání připojení|Žádné dimenze|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procenta|Průměr|Procento využití procesoru|Žádné dimenze|
|compute_limit|Výpočetní jednotka limit|Počet|Průměr|Výpočetní jednotka limit|Žádné dimenze|
|compute_consumption_percent|Výpočetní jednotka procento|Procenta|Průměr|Výpočetní jednotka procento|Žádné dimenze|
|memory_percent|Paměť v procentech|Procenta|Průměr|Paměť v procentech|Žádné dimenze|
|io_consumption_percent|Vstupně-výstupní operace v procentech|Procenta|Průměr|Vstupně-výstupní operace v procentech|Žádné dimenze|
|storage_percent|Procento úložiště|Procenta|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Využité úložiště|bajtů|Průměr|Využité úložiště|Žádné dimenze|
|storage_limit|Limit úložiště|bajtů|Průměr|Limit úložiště|Žádné dimenze|
|active_connections|Celkový počet aktivních připojení|Počet|Průměr|Celkový počet aktivních připojení|Žádné dimenze|
|connections_failed|Celkový počet selhání připojení|Počet|Průměr|Celkový počet selhání připojení|Žádné dimenze|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslání zprávy telemetrie|Počet|celkem|Počet zařízení cloud telemetrické zprávy se pokusil odeslat do služby IoT hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Telemetrické zprávy odeslané|Počet|celkem|Počet zařízení cloud telemetrické zprávy úspěšně odeslán do služby IoT hub|Žádné dimenze|
|c2d.commands.egress.complete.success|Příkazy byla dokončena|Počet|celkem|Počet příkazů typu cloud zařízení úspěšně dokončila, a zařízení|Žádné dimenze|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Počet|celkem|Počet příkazů typu cloud zařízení opuštěny v rámci zařízení|Žádné dimenze|
|c2d.commands.egress.reject.success|Příkazy odmítnut|Počet|celkem|Počet zařízení byl odmítnut příkazů typu cloud zařízení|Žádné dimenze|
|devices.totalDevices|Celkový počet zařízení|Počet|celkem|Počet zařízení zaregistrovat do služby IoT hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojená zařízení|Počet|celkem|Počet zařízení připojených ke službě IoT hub|Žádné dimenze|
|d2c.telemetry.egress.success|Telemetrické zprávy doručit|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncových bodů (celkem)|Žádné dimenze|
|d2c.telemetry.egress.dropped|Vyřazené zprávy|Počet|celkem|Počet zpráv vyřadit, protože koncový bod doručení byla neaktivní|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Osamocené zprávy|Počet|celkem|Počet zpráv, na které se neshodují žádné cesty, včetně záložní trasy|Žádné dimenze|
|d2c.telemetry.egress.invalid|Neplatné zprávy|Počet|celkem|Počet zpráv, které nejsou doručeny z důvodu nekompatibility s nástrojem koncový bod|Žádné dimenze|
|d2c.telemetry.egress.fallback|Zprávy odpovídající záložního stavu|Počet|celkem|Počet zpráv zapsaných do záložního koncového bodu|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Zpráv doručených do koncové body centra událostí|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncové body centra událostí|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Latence zprávy pro koncové body centra událostí|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do koncového bodu centra událostí, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Doručování zpráv do fronty Service Bus koncové body|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncových bodů frontou Service Bus|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Latence zprávy pro koncové body frontou Service Bus|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráv do fronty Service Bus koncový bod, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Doručování zpráv do tématu Service Bus koncové body|Počet|celkem|Počet zpráv byly úspěšně zapsána do tématu Service Bus koncové body|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Latence zprávy pro koncové body témata sběrnice|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do tématu Service Bus koncový bod, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Zpráv doručených do vestavěným koncovým bodem (zprávy nebo události)|Počet|celkem|Počet zpráv byla úspěšně zapsána do vestavěným koncovým bodem (zprávy nebo události)|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Zpráva latence pro předdefinovaný koncový bod (zprávy nebo události)|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráva do vestavěným koncovým bodem (zprávy událostí), v milisekundách |Žádné dimenze|
|d2c.endpoints.egress.storage|Zpráv doručených do koncové body úložiště|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncové body úložiště|Žádné dimenze|
|d2c.endpoints.latency.storage|Latence zprávy pro koncové body úložiště|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráva do koncový bod úložiště, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.storage.bytes|Data zapsaná do úložiště|bajtů|celkem|Množství dat v bajtech, zapisovat do úložiště koncové body|Žádné dimenze|
|d2c.endpoints.egress.storage.blobs|Zapisovat do úložiště objektů BLOB|Počet|celkem|Počet zapsána do koncové body úložiště objektů BLOB|Žádné dimenze|
|d2c.twin.read.success|Úspěšné twin čte ze zařízení|Počet|celkem|Počet čtení všechny úspěšné twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.read.failure|Čtení twin ze zařízení se nezdařila|Počet|celkem|Počet všech nepodařilo spouštěná zařízení twin čtení.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi twin čtení ze zařízení|bajtů|Průměr|Průměr, min a max všechny úspěšné, spouštěná zařízení twin čtení.|Žádné dimenze|
|d2c.twin.update.success|Aktualizace úspěšná twin ze zařízení|Počet|celkem|Počet aktualizací všechny úspěšné twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.update.failure|Aktualizace twin ze zařízení se nezdařila|Počet|celkem|Počet všech se nezdařila aktualizace twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.update.size|Velikost twin aktualizace ze zařízení|bajtů|Průměr|Průměr, minimální a maximální velikost všech úspěšné, spouštěná zařízení twin aktualizace.|Žádné dimenze|
|c2d.methods.success|Volání úspěšné přímá metoda|Počet|celkem|Počet všech úspěšných volání přímá metoda.|Žádné dimenze|
|c2d.Methods.failure|Přímá metoda volání se nezdařilo|Počet|celkem|Počet všech se nezdařila, volání metod direct.|Žádné dimenze|
|c2d.methods.requestSize|Žádost o velikosti volání přímá metoda|bajtů|Průměr|Průměr, min a max všechny úspěšné požadavky přímá metoda.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odpovědi volání přímá metoda|bajtů|Průměr|Průměr, min a max všechny úspěšné odpovědi přímá metoda.|Žádné dimenze|
|c2d.twin.read.success|Úspěšné twin čte z back-end|Počet|celkem|Počet všech úspěšně spustil back-end twin čtení.|Žádné dimenze|
|c2d.twin.read.failure|Neúspěšné twin čte z back-end|Počet|celkem|Počet všech se nezdařilo čtení twin iniciované back-end.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi twin čtení z back-end|bajtů|Průměr|Průměr, min a max všechny úspěšné, iniciované back-end twin čtení.|Žádné dimenze|
|c2d.twin.update.success|Aktualizace úspěšná twin z back-end|Počet|celkem|Počet aktualizací všechny úspěšné twin iniciované back-end.|Žádné dimenze|
|c2d.twin.update.failure|Aktualizace se nezdařila twin z back-end|Počet|celkem|Počet všech se nezdařila aktualizace twin iniciované back-end.|Žádné dimenze|
|c2d.twin.update.size|Velikost twin aktualizace z back-end|bajtů|Průměr|Průměr, minimální a maximální velikost všech úspěšné, iniciované back-end twin aktualizace.|Žádné dimenze|
|twinQueries.success|Úspěšné twin dotazy|Počet|celkem|Počet všech úspěšné twin dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné twin dotazy|Počet|celkem|Počet všechny neúspěšné twin dotazy.|Žádné dimenze|
|twinQueries.resultSize|Objem výsledků dotazů Twin|bajtů|Průměr|Průměr, minimální a maximální velikosti výsledek všechny úspěšné twin dotazy.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úloh aktualizace twin|Počet|celkem|Počet všech úspěšném vytvoření twin aktualizace úloh.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úloh aktualizace twin|Počet|celkem|Počet všech se nezdařilo vytvoření twin aktualizace úlohy.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh volání – metoda|Počet|celkem|Počet všech úspěšném vytvoření přímá metoda volání úloh.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh volání – metoda|Počet|celkem|Počet všech se nezdařilo vytvoření přímá metoda vyvolání úlohy.|Žádné dimenze|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Počet|celkem|Počet všech úspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.listJobs.failure|Volání do seznamu úloh se nezdařilo|Počet|celkem|Počet všech selhání volání do seznamu úloh.|Žádné dimenze|
|jobs.cancelJob.success|Zrušení úloh úspěšné|Počet|celkem|Počet všech úspěšných volání zrušení úlohy.|Žádné dimenze|
|jobs.cancelJob.failure|Informace o neúspěšné úloze zrušení|Počet|celkem|Počet všech volání se nezdařilo zrušit úlohu.|Žádné dimenze|
|jobs.queryJobs.success|Až se úloha úspěšně dotazy|Počet|celkem|Počet všech úspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.queryJobs.failure|Úloha se dotazuje se nezdařilo|Počet|celkem|Počet všech selhání volání do úlohy dotaz.|Žádné dimenze|
|jobs.completed|Dokončené úlohy|Počet|celkem|Počet všech dokončené úlohy.|Žádné dimenze|
|jobs.failed|Neúspěšné úlohy|Počet|celkem|Počet všechny neúspěšné úlohy.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet omezení chyb|Počet|celkem|Omezí počet omezení chyb z důvodu propustnost zařízení generovaný|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet zpráv, které používá|Počet|Průměr|Počet celkový počet zpráv v současné době používá. Toto je kumulativní hodnotu, která je nastaven na hodnotu nula v 00:00 UTC každý den.|Žádné dimenze|
|deviceDataUsage|Celkový počet devicedata využití|Počet|celkem|Bajtů přenesených do a z jakékoli zařízení připojené k platformě IotHub|Žádné dimenze|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Počet|celkem|Počet pokus o registraci zařízení|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Zařízení přiřazená|Počet|celkem|Počet zařízení přiřazená do služby IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Počet|celkem|Počet zařízení atestace podle pokus|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|míra požadovaný využití|Procenta|Průměr|míra požadovaný využití|Žádné dimenze|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|míra požadovaný využití|Procenta|Průměr|míra požadovaný využití|Žádné dimenze|
|deviceDataUsage|Celkový počet devicedata využití|Počet|celkem|Bajtů přenesených do a z jakékoli zařízení připojené k platformě IotHub|Žádné dimenze|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslání zprávy telemetrie|Počet|celkem|Počet zařízení cloud telemetrické zprávy se pokusil odeslat do služby IoT hub|Žádné dimenze|
|d2c.telemetry.ingress.success|Telemetrické zprávy odeslané|Počet|celkem|Počet zařízení cloud telemetrické zprávy úspěšně odeslán do služby IoT hub|Žádné dimenze|
|c2d.commands.egress.complete.success|Příkazy byla dokončena|Počet|celkem|Počet příkazů typu cloud zařízení úspěšně dokončila, a zařízení|Žádné dimenze|
|c2d.commands.egress.abandon.success|Příkazy opuštění|Počet|celkem|Počet příkazů typu cloud zařízení opuštěny v rámci zařízení|Žádné dimenze|
|c2d.commands.egress.reject.success|Příkazy odmítnut|Počet|celkem|Počet zařízení byl odmítnut příkazů typu cloud zařízení|Žádné dimenze|
|devices.totalDevices|Celkový počet zařízení|Počet|celkem|Počet zařízení zaregistrovat do služby IoT hub|Žádné dimenze|
|devices.connectedDevices.allProtocol|Připojená zařízení|Počet|celkem|Počet zařízení připojených ke službě IoT hub|Žádné dimenze|
|d2c.telemetry.egress.success|Telemetrické zprávy doručit|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncových bodů (celkem)|Žádné dimenze|
|d2c.telemetry.egress.dropped|Vyřazené zprávy|Počet|celkem|Počet zpráv vyřadit, protože koncový bod doručení byla neaktivní|Žádné dimenze|
|d2c.telemetry.egress.orphaned|Osamocené zprávy|Počet|celkem|Počet zpráv, na které se neshodují žádné cesty, včetně záložní trasy|Žádné dimenze|
|d2c.telemetry.egress.invalid|Neplatné zprávy|Počet|celkem|Počet zpráv, které nejsou doručeny z důvodu nekompatibility s nástrojem koncový bod|Žádné dimenze|
|d2c.telemetry.egress.fallback|Zprávy odpovídající záložního stavu|Počet|celkem|Počet zpráv zapsaných do záložního koncového bodu|Žádné dimenze|
|d2c.endpoints.egress.eventHubs|Zpráv doručených do koncové body centra událostí|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncové body centra událostí|Žádné dimenze|
|d2c.endpoints.latency.eventHubs|Latence zprávy pro koncové body centra událostí|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do koncového bodu centra událostí, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.serviceBusQueues|Doručování zpráv do fronty Service Bus koncové body|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncových bodů frontou Service Bus|Žádné dimenze|
|d2c.endpoints.latency.serviceBusQueues|Latence zprávy pro koncové body frontou Service Bus|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráv do fronty Service Bus koncový bod, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.serviceBusTopics|Doručování zpráv do tématu Service Bus koncové body|Počet|celkem|Počet zpráv byly úspěšně zapsána do tématu Service Bus koncové body|Žádné dimenze|
|d2c.endpoints.latency.serviceBusTopics|Latence zprávy pro koncové body témata sběrnice|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příchozí zprávy do tématu Service Bus koncový bod, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.builtIn.events|Zpráv doručených do vestavěným koncovým bodem (zprávy nebo události)|Počet|celkem|Počet zpráv byla úspěšně zapsána do vestavěným koncovým bodem (zprávy nebo události)|Žádné dimenze|
|d2c.endpoints.latency.builtIn.events|Zpráva latence pro předdefinovaný koncový bod (zprávy nebo události)|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráva do vestavěným koncovým bodem (zprávy událostí), v milisekundách |Žádné dimenze|
|d2c.endpoints.egress.storage|Zpráv doručených do koncové body úložiště|Počet|celkem|Počet zpráv byla úspěšně zapsána do koncové body úložiště|Žádné dimenze|
|d2c.endpoints.latency.storage|Latence zprávy pro koncové body úložiště|Milisekundy|Průměr|Průměrná latence mezi příchozí zprávy do služby IoT hub a příjem příchozích dat zpráva do koncový bod úložiště, v milisekundách|Žádné dimenze|
|d2c.endpoints.egress.storage.bytes|Data zapsaná do úložiště|bajtů|celkem|Množství dat v bajtech, zapisovat do úložiště koncové body|Žádné dimenze|
|d2c.endpoints.egress.storage.blobs|Zapisovat do úložiště objektů BLOB|Počet|celkem|Počet zapsána do koncové body úložiště objektů BLOB|Žádné dimenze|
|d2c.twin.read.success|Úspěšné twin čte ze zařízení|Počet|celkem|Počet čtení všechny úspěšné twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.read.failure|Čtení twin ze zařízení se nezdařila|Počet|celkem|Počet všech nepodařilo spouštěná zařízení twin čtení.|Žádné dimenze|
|d2c.twin.read.size|Velikost odpovědi twin čtení ze zařízení|bajtů|Průměr|Průměr, min a max všechny úspěšné, spouštěná zařízení twin čtení.|Žádné dimenze|
|d2c.twin.update.success|Aktualizace úspěšná twin ze zařízení|Počet|celkem|Počet aktualizací všechny úspěšné twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.update.failure|Aktualizace twin ze zařízení se nezdařila|Počet|celkem|Počet všech se nezdařila aktualizace twin spouštěná zařízení.|Žádné dimenze|
|d2c.twin.update.size|Velikost twin aktualizace ze zařízení|bajtů|Průměr|Průměr, minimální a maximální velikost všech úspěšné, spouštěná zařízení twin aktualizace.|Žádné dimenze|
|c2d.methods.success|Volání úspěšné přímá metoda|Počet|celkem|Počet všech úspěšných volání přímá metoda.|Žádné dimenze|
|c2d.Methods.failure|Přímá metoda volání se nezdařilo|Počet|celkem|Počet všech se nezdařila, volání metod direct.|Žádné dimenze|
|c2d.methods.requestSize|Žádost o velikosti volání přímá metoda|bajtů|Průměr|Průměr, min a max všechny úspěšné požadavky přímá metoda.|Žádné dimenze|
|c2d.methods.responseSize|Velikost odpovědi volání přímá metoda|bajtů|Průměr|Průměr, min a max všechny úspěšné odpovědi přímá metoda.|Žádné dimenze|
|c2d.twin.read.success|Úspěšné twin čte z back-end|Počet|celkem|Počet všech úspěšně spustil back-end twin čtení.|Žádné dimenze|
|c2d.twin.read.failure|Neúspěšné twin čte z back-end|Počet|celkem|Počet všech se nezdařilo čtení twin iniciované back-end.|Žádné dimenze|
|c2d.twin.read.size|Velikost odpovědi twin čtení z back-end|bajtů|Průměr|Průměr, min a max všechny úspěšné, iniciované back-end twin čtení.|Žádné dimenze|
|c2d.twin.update.success|Aktualizace úspěšná twin z back-end|Počet|celkem|Počet aktualizací všechny úspěšné twin iniciované back-end.|Žádné dimenze|
|c2d.twin.update.failure|Aktualizace se nezdařila twin z back-end|Počet|celkem|Počet všech se nezdařila aktualizace twin iniciované back-end.|Žádné dimenze|
|c2d.twin.update.size|Velikost twin aktualizace z back-end|bajtů|Průměr|Průměr, minimální a maximální velikost všech úspěšné, iniciované back-end twin aktualizace.|Žádné dimenze|
|twinQueries.success|Úspěšné twin dotazy|Počet|celkem|Počet všech úspěšné twin dotazů.|Žádné dimenze|
|twinQueries.failure|Neúspěšné twin dotazy|Počet|celkem|Počet všechny neúspěšné twin dotazy.|Žádné dimenze|
|twinQueries.resultSize|Objem výsledků dotazů Twin|bajtů|Průměr|Průměr, minimální a maximální velikosti výsledek všechny úspěšné twin dotazy.|Žádné dimenze|
|jobs.createTwinUpdateJob.success|Úspěšné vytvoření úloh aktualizace twin|Počet|celkem|Počet všech úspěšném vytvoření twin aktualizace úloh.|Žádné dimenze|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úloh aktualizace twin|Počet|celkem|Počet všech se nezdařilo vytvoření twin aktualizace úlohy.|Žádné dimenze|
|jobs.createDirectMethodJob.success|Úspěšné vytvoření úloh volání – metoda|Počet|celkem|Počet všech úspěšném vytvoření přímá metoda volání úloh.|Žádné dimenze|
|jobs.createDirectMethodJob.failure|Neúspěšné vytvoření úloh volání – metoda|Počet|celkem|Počet všech se nezdařilo vytvoření přímá metoda vyvolání úlohy.|Žádné dimenze|
|jobs.listJobs.success|Úspěšné volání do seznamu úloh|Počet|celkem|Počet všech úspěšných volání do seznamu úloh.|Žádné dimenze|
|jobs.listJobs.failure|Volání do seznamu úloh se nezdařilo|Počet|celkem|Počet všech selhání volání do seznamu úloh.|Žádné dimenze|
|jobs.cancelJob.success|Zrušení úloh úspěšné|Počet|celkem|Počet všech úspěšných volání zrušení úlohy.|Žádné dimenze|
|jobs.cancelJob.failure|Informace o neúspěšné úloze zrušení|Počet|celkem|Počet všech volání se nezdařilo zrušit úlohu.|Žádné dimenze|
|jobs.queryJobs.success|Až se úloha úspěšně dotazy|Počet|celkem|Počet všech úspěšných volání do úlohy dotaz.|Žádné dimenze|
|jobs.queryJobs.failure|Úloha se dotazuje se nezdařilo|Počet|celkem|Počet všech selhání volání do úlohy dotaz.|Žádné dimenze|
|jobs.completed|Dokončené úlohy|Počet|celkem|Počet všech dokončené úlohy.|Žádné dimenze|
|jobs.failed|Neúspěšné úlohy|Počet|celkem|Počet všechny neúspěšné úlohy.|Žádné dimenze|
|d2c.telemetry.ingress.sendThrottle|Počet omezení chyb|Počet|celkem|Omezí počet omezení chyb z důvodu propustnost zařízení generovaný|Žádné dimenze|
|dailyMessageQuotaUsed|Celkový počet zpráv, které používá|Počet|Průměr|Počet celkový počet zpráv v současné době používá. Toto je kumulativní hodnotu, která je nastaven na hodnotu nula v 00:00 UTC každý den.|Žádné dimenze|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MetadataRequests|Požadavků na metadata|Počet|Počet|Počet požadavků na metadata. Cosmos DB udržuje kolekci metadat systému pro každý účet, který umožňuje vytvořit výčet kolekce, databáze atd., a jejich konfiguraci, které jsou zdarma.|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Mongo požadavek zdarma|Počet|celkem|Jednotek žádosti mongo použití|GlobalDatabaseAccountName, DatabaseName, název_kolekce, oblast, CommandName, kód chyby|
|MongoRequests|Mongo požadavky|Počet|Počet|Počet zpracovaných požadavků Mongo|GlobalDatabaseAccountName, DatabaseName, název_kolekce, oblast, CommandName, kód chyby|
|TotalRequestUnits|Celkový požadavek jednotky|Počet|celkem|Požadavku že využívat jednotky|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|Požadavky celkem|Počet|Počet|Počet zpracovaných požadavků|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Počet|celkem|Úspěšné požadavky pro Microsoft.EventHub (Preview)|entityName|
|ServerErrors|Chyby serveru (Preview)|Počet|celkem|Chyby serveru pro Microsoft.EventHub (Preview)|entityName|
|UserErrors|Chyby uživatele (Preview)|Počet|celkem|Chyby uživatele pro Microsoft.EventHub (Preview)|entityName|
|QuotaExceededErrors|Chyby překročení kvóty (Preview)|Počet|celkem|Chyby překročení kvóty pro Microsoft.EventHub (Preview)|entityName|
|ThrottledRequests|Omezené požadavky (Preview)|Počet|celkem|Omezené požadavky pro Microsoft.EventHub (Preview)|entityName|
|IncomingRequests|Příchozí žádosti (Preview)|Počet|celkem|Příchozí požadavky pro Microsoft.EventHub (Preview)|entityName|
|IncomingMessages|Příchozí zprávy (Preview)|Počet|celkem|Příchozí zprávy pro Microsoft.EventHub (Preview)|entityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Počet|celkem|Odchozí zprávy pro Microsoft.EventHub (Preview)|entityName|
|IncomingBytes|Příchozí bajty (Preview)|bajtů|celkem|Příchozí bajty pro Microsoft.EventHub (Preview)|entityName|
|OutgoingBytes|Odchozí bajty (Preview)|bajtů|celkem|Odchozí bajty pro Microsoft.EventHub (Preview)|entityName|
|ActiveConnections|ActiveConnections (Preview)|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft.EventHub (Preview)|Žádné dimenze|
|ConnectionsOpened|Otevřená připojení (Preview)|Počet|Průměr|Otevřená připojení pro Microsoft.EventHub (Preview)|entityName|
|ConnectionsClosed|Ukončená připojení (Preview)|Počet|Průměr|Ukončená připojení pro Microsoft.EventHub (Preview)|entityName|
|CaptureBacklog|Zachytit backlog (Preview)|Počet|celkem|Zachytit backlog pro Microsoft.EventHub (Preview)|entityName|
|CapturedMessages|Zachycené zprávy (Preview)|Počet|celkem|Zachycené zprávy pro Microsoft.EventHub (Preview)|entityName|
|CapturedBytes|Zachycené bajty (Preview)|bajtů|celkem|Zachycené bajty pro Microsoft.EventHub (Preview)|entityName|
|Velikost|Velikost (Preview)|bajtů|Průměr|Velikost EventHub v bajtech. (Preview)|entityName|
|INREQS|Příchozí požadavky|Počet|celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů|Žádné dimenze|
|SUCCREQ|Úspěšné požadavky|Počet|celkem|Celkový počet úspěšných žádostí pro obor názvů|Žádné dimenze|
|FAILREQ|Neúspěšné požadavky|Počet|celkem|Celkový počet neúspěšných žádostí pro obor názvů|Žádné dimenze|
|SVRBSY|Chyby kvůli zaneprázdněnosti serveru|Počet|celkem|Celkový počet chyb kvůli zaneprázdnění serveru pro obor názvů|Žádné dimenze|
|INTERR|Vnitřní chyby serveru|Počet|celkem|Celkový počet vnitřních chyb serveru pro obor názvů|Žádné dimenze|
|MISCERR|Jiné chyby|Počet|celkem|Celkový počet neúspěšných žádostí pro obor názvů|Žádné dimenze|
|INMSGS|Příchozí zprávy (zastaralé)|Počet|celkem|Celkový počet příchozích zpráv pro obor názvů. Tato metrika je zastaralý. Místo toho použijte metrika příchozí zprávy|Žádné dimenze|
|EHINMSGS|Příchozí zprávy|Počet|celkem|Celkový počet příchozích zpráv pro obor názvů|Žádné dimenze|
|OUTMSGS|Odchozí zprávy (zastaralé)|Počet|celkem|Celkový počet odchozích zpráv pro obor názvů. Tato metrika je zastaralý. Místo toho použijte metrika odchozích zpráv|Žádné dimenze|
|EHOUTMSGS|Odchozí zprávy|Počet|celkem|Celkový počet odchozích zpráv pro obor názvů|Žádné dimenze|
|EHINMBS|Příchozí bajty (zastaralé)|bajtů|celkem|Události rozbočovače příchozí zprávy propustnost pro obor názvů. Tato metrika je zastaralý. Místo toho použijte metrika Příchozí bajty|Žádné dimenze|
|EHINBYTES|Příchozí bajty|bajtů|celkem|Propustnost příchozích zpráv centra událostí pro obor názvů|Žádné dimenze|
|EHOUTMBS|Odchozí bajty (zastaralé)|bajtů|celkem|Události rozbočovače odchozí zprávy propustnost pro obor názvů. Tato metrika je zastaralý. Místo toho použijte metrika Odchozí bajty|Žádné dimenze|
|EHOUTBYTES|Odchozí bajty|bajtů|celkem|Propustnost odchozích zpráv centra událostí pro obor názvů|Žádné dimenze|
|EHABL|Archivovat zprávy backlogu|Počet|celkem|Archivované zprávy centra událostí v backlogu pro obor názvů|Žádné dimenze|
|EHAMSGS|Archivovat zprávy|Počet|celkem|Archivované zprávy centra událostí v oboru názvů|Žádné dimenze|
|EHAMBS|Archivovat propustnost zpráv|bajtů|celkem|Propustnost archivovaných zpráv Centra událostí v oboru názvů|Žádné dimenze|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Požadavky na brány|Počet|celkem|Počet požadavků na bránu|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Požadavků zařazených do kategorií brány|Počet|celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zjištěná hodnota metriky|Počet|Průměr|Hodnota vypočítaná automatickým škálováním při jeho spuštění|MetricTriggerSource|
|MetricThreshold|Mezní hodnota metriky|Počet|Průměr|Konfigurovaná mezní hodnota automatického škálování, když se automatické škálování spustilo|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Počet|Průměr|Kapacita ohlášená automatickému škálování při jeho spuštění|Žádné dimenze|
|ScaleActionsInitiated|Zahájené akce škálování|Počet|celkem|Směr operace škálování|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkem přístupů k rozhraní API služby|Počet|Počet|Celkový počet přístupů k rozhraní API služby|ActivityType, ActivityName|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekundy|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, název aktivity ActivityName, StatusCode|
|ServiceApiResult|Celkem výsledků rozhraní API služby|Počet|Počet|Celkový počet výsledků rozhraní API služby|ActivityType, název aktivity ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Latence|Latence|Milisekundy|Průměr|Doba volání API|OperationName, výsledek|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Počet|celkem|Počet spuštěných běhů pracovního postupu.|Žádné dimenze|
|RunsCompleted|Dokončené běhy|Počet|celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsSucceeded|Úspěšné běhy|Počet|celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Neúspěšné běhy|Počet|celkem|Počet neúspěšných běhů pracovního postupu.|Žádné dimenze|
|RunsCancelled|Zrušené běhy|Počet|celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunLatency|Latence běhu|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Latence úspěšných běhů|Sekundy|Průměr|Latence úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Omezené události běhu|Počet|celkem|Počet akcí pracovního postupu nebo omezených událostí triggeru.|Žádné dimenze|
|RunFailurePercentage|Procento selhání spuštění|Procenta|celkem|Procento neúspěšných spuštění pracovních postupů|Žádné dimenze|
|ActionsStarted|Spuštěné akce |Počet|celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Dokončené akce |Počet|celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Úspěšné akce |Počet|celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Neúspěšné akce|Počet|celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Vynechané akce |Počet|celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Omezené události akcí|Počet|celkem|Počet omezených událostí akcí pracovního postupu.|Žádné dimenze|
|TriggersStarted|Spuštěné triggery |Počet|celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Dokončené triggery |Počet|celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Úspěšné triggery |Počet|celkem|Počet úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFailed|Neúspěšné triggery |Počet|celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Vynechané triggery|Počet|celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Vyvolané triggery |Počet|celkem|Počet aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggerFireLatency|Latence při vyvolání triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerSuccessLatency|Latence úspěšného triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Omezené události triggeru|Počet|celkem|Počet omezených událostí triggeru pracovního postupu.|Žádné dimenze|
|BillableActionExecutions|Fakturovatelné operace provedení akce|Počet|celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádné dimenze|
|BillableTriggerExecutions|Fakturovatelné operace provedení aktivačních událostí|Počet|celkem|Počet fakturovaných provedení aktivačních událostí pracovního postupu|Žádné dimenze|
|TotalBillableExecutions|Fakturovatelné operace provedení celkem|Počet|celkem|Počet fakturovaných provedení pracovního postupu|Žádné dimenze|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Dostupnost virtuálních IP adres|Počet|Průměr|Dostupnost virtuální IP adresy koncových bodů, na základě výsledků testu|Virtuální IP adresa, VipPort|
|DipAvailability|Dostupnost vyhrazené IP adresy|Počet|Průměr|Dostupnost vyhrazené IP adresy koncových bodů, na základě výsledků testu|Typprotokolu, DipPort, virtuální IP adresa, VipPort, DipAddress|
|ByteCount|Počet bajtů|Počet|celkem|Celkový počet bajtů přenesených v časovém období|Virtuální IP adresa, VipPort, směr|
|PacketCount|Počet paketů|Počet|celkem|Celkový počet paketů odeslaných v časovém období|Virtuální IP adresa, VipPort, směr|
|SYNCount|Počet SYN|Počet|celkem|Celkový počet paketů SYN přenášených v časovém období|Virtuální IP adresa, VipPort, směr|
|SnatConnectionCount|Počet připojení překládat pomocí SNAT|Počet|celkem|Celkový počet nových připojení překládat pomocí SNAT vytvořených v časovém období|Virtuální IP adresa, DipAddress, ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Dotaz svazku|Počet|celkem|Počet dotazů poskytovaného pro zóny DNS|Žádné dimenze|
|RecordSetCount|Počet sady záznamů|Počet|Maximálně|Počet sad záznamů v zóně DNS|Žádné dimenze|
|RecordSetCapacityUtilization|Sady záznamů využití kapacity|Procenta|Maximálně|Procento kapacity sady záznamů využívaných zóny DNS|Žádné dimenze|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|Příchozí pakety DDoS|CountPerSecond|Maximálně|Příchozí pakety DDoS|Žádné dimenze|
|PacketsDroppedDDoS|Příchozí pakety vyřadit DDoS|CountPerSecond|Maximálně|Příchozí pakety vyřadit DDoS|Žádné dimenze|
|PacketsForwardedDDoS|Příchozí pakety předávané DDoS|CountPerSecond|Maximálně|Příchozí pakety předávané DDoS|Žádné dimenze|
|TCPPacketsInDDoS|Příchozí pakety TCP DDoS|CountPerSecond|Maximálně|Příchozí pakety TCP DDoS|Žádné dimenze|
|TCPPacketsDroppedDDoS|Příchozí pakety TCP vyřadit DDoS|CountPerSecond|Maximálně|Příchozí pakety TCP vyřadit DDoS|Žádné dimenze|
|TCPPacketsForwardedDDoS|Příchozí pakety TCP předávají DDoS|CountPerSecond|Maximálně|Příchozí pakety TCP předávají DDoS|Žádné dimenze|
|UDPPacketsInDDoS|Příchozí pakety UDP DDoS|CountPerSecond|Maximálně|Příchozí pakety UDP DDoS|Žádné dimenze|
|UDPPacketsDroppedDDoS|Příchozí pakety UDP vyřadit DDoS|CountPerSecond|Maximálně|Příchozí pakety UDP vyřadit DDoS|Žádné dimenze|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předávají DDoS|CountPerSecond|Maximálně|Příchozí pakety UDP předávají DDoS|Žádné dimenze|
|BytesInDDoS|Příchozí bajty DDoS|BytesPerSecond|Maximálně|Příchozí bajty DDoS|Žádné dimenze|
|BytesDroppedDDoS|Příchozí bajty vyřadit DDoS|BytesPerSecond|Maximálně|Příchozí bajty vyřadit DDoS|Žádné dimenze|
|BytesForwardedDDoS|Příchozí bajty předávaných DDoS|BytesPerSecond|Maximálně|Příchozí bajty předávaných DDoS|Žádné dimenze|
|TCPBytesInDDoS|Příchozí bajty TCP DDoS|BytesPerSecond|Maximálně|Příchozí bajty TCP DDoS|Žádné dimenze|
|TCPBytesDroppedDDoS|Příchozí bajty TCP vyřadit DDoS|BytesPerSecond|Maximálně|Příchozí bajty TCP vyřadit DDoS|Žádné dimenze|
|TCPBytesForwardedDDoS|Příchozí bajty TCP předávaných DDoS|BytesPerSecond|Maximálně|Příchozí bajty TCP předávaných DDoS|Žádné dimenze|
|UDPBytesInDDoS|Příchozí bajty UDP DDoS|BytesPerSecond|Maximálně|Příchozí bajty UDP DDoS|Žádné dimenze|
|UDPBytesDroppedDDoS|Příchozí bajty UDP vyřadit DDoS|BytesPerSecond|Maximálně|Příchozí bajty UDP vyřadit DDoS|Žádné dimenze|
|UDPBytesForwardedDDoS|Příchozí bajty UDP předávaných DDoS|BytesPerSecond|Maximálně|Příchozí bajty UDP předávaných DDoS|Žádné dimenze|
|IfUnderDDoSAttack|V části DDoS útokům nebo ne|Počet|Maximálně|V části DDoS útokům nebo ne|Žádné dimenze|
|DDoSTriggerTCPPackets|Příchozí pakety TCP k aktivaci DDoS zmírnění|CountPerSecond|Maximálně|Příchozí pakety TCP k aktivaci DDoS zmírnění|Žádné dimenze|
|DDoSTriggerUDPPackets|Příchozí pakety UDP pro aktivaci DDoS zmírnění|CountPerSecond|Maximálně|Příchozí pakety UDP pro aktivaci DDoS zmírnění|Žádné dimenze|
|DDoSTriggerSYNPackets|Příchozí pakety SYN k aktivaci DDoS zmírnění|CountPerSecond|Maximálně|Příchozí pakety SYN k aktivaci DDoS zmírnění|Žádné dimenze|
|VipAvailability|Dostupnost|Počet|Průměr|Průměrná dostupnost IPAddress v časovém období|Port|
|ByteCount|Počet bajtů|Počet|celkem|Celkový počet bajtů přenesených v časovém období|Port, směr|
|PacketCount|Počet paketů|Počet|celkem|Celkový počet paketů odeslaných v časovém období|Port, směr|
|SynCount|Počet SYN|Počet|celkem|Celkový počet paketů SYN přenášených v časovém období|Port, směr|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|celkem|Počet bajtů za sekundu, které se má zpracovat aplikační bránu|Žádné dimenze|
|UnhealthyHostCount|Chybný počet hostitele|Počet|Průměr|Počet back-end není v pořádku hostitelích|BackendSettingsPool|
|HealthyHostCount|V pořádku počet hostitele|Počet|Průměr|Počet hostitelů v pořádku back-end|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Počet|celkem|Počet úspěšných požadavků, které má spouští Application Gateway|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Počet|celkem|Počet neúspěšných požadavků, které se má obsluhovat Application Gateway|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Počet|celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální počet připojení|Počet|celkem|Počet aktuální připojení vytvořených pomocí Application Gateway|Žádné dimenze|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Tunelové propojení šířky pásma|BytesPerSecond|Průměr|Průměrná šířky pásma tunelu v bajtech za sekundu|ConnectionName, Vzdálená adresa IP|
|TunnelEgressBytes|Tunelové propojení Odchozí bajty|bajtů|celkem|Odchozí bajty tunelového propojení|ConnectionName, Vzdálená adresa IP|
|TunnelIngressBytes|Příchozí bajty tunelového propojení|bajtů|celkem|Příchozí bajty tunelového propojení|ConnectionName, Vzdálená adresa IP|
|TunnelEgressPackets|Odchozí pakety tunelového propojení|Počet|celkem|Počet odchozích paketů tunelového propojení|ConnectionName, Vzdálená adresa IP|
|TunnelIngressPackets|Příchozí pakety tunelového propojení|Počet|celkem|Příchozí paket počet přes tunelové propojení|ConnectionName, Vzdálená adresa IP|
|TunnelEgressPacketDropTSMismatch|Tunelové propojení odchozí TS neshoda paketu rozevírací|Počet|celkem|Odchozí počet vyřazení z provozu selektor neshoda tunelového propojení|ConnectionName, Vzdálená adresa IP|
|TunnelIngressPacketDropTSMismatch|Tunelové propojení příchozího TS neshoda paketu rozevírací|Počet|celkem|Příchozí počet vyřazení z provozu selektor neshoda tunelového propojení|ConnectionName, Vzdálená adresa IP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Služba BITS ingressing Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Služba BITS egressing Azure za sekundu|Žádné dimenze|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy pomocí vrátil koncový bod|Počet|celkem|Kolikrát koncový bod Traffic Manager vrátil v zadaném časovém období|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncový bod koncovým bodem|Počet|Maximálně|1, pokud koncového bodu testu stav "povoleno", jinak hodnota 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sondy se nezdařilo|Procenta|Průměr|% monitorování sondy připojení se nezdařilo.|Žádné dimenze|
|AverageRoundtripMs|Střední Doba odezvy (ms)|Počet milisekund|Průměr|Operace round-trip čas průměrná sítě (ms) pro připojení k monitorování sondy posílaná mezi zdrojovým a cílovým|Žádné dimenze|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Registration.all|Registrace – operace|Počet|celkem|Počet všech úspěšných operací s registrací (vytvoření, aktualizace, dotazy a odstranění) |Žádné dimenze|
|registration.create|Registrace – vytvořit operace|Počet|celkem|Počet všech úspěšných vytvoření registrací|Žádné dimenze|
|registration.update|Registrace – aktualizovat operace|Počet|celkem|Počet všech úspěšných aktualizací registrací|Žádné dimenze|
|Registration.Get|Registrace – přečíst operace|Počet|celkem|Počet všech úspěšných dotazů registrací|Žádné dimenze|
|registration.delete|Registrace – odstranit operace|Počet|celkem|Počet všech úspěšných odstranění registrací|Žádné dimenze|
|příchozí|Příchozí zprávy|Počet|celkem|Počet všech úspěšných volání rozhraní API, pomocí kterých se odesílají data |Žádné dimenze|
|incoming.scheduled|Odeslaná plánovaná nabízená oznámení|Počet|celkem|Zrušená plánovaná nabízená oznámení|Žádné dimenze|
|incoming.scheduled.cancel|Zrušená plánovaná nabízená oznámení|Počet|celkem|Zrušená plánovaná nabízená oznámení|Žádné dimenze|
|scheduled.pending|Čekající plánovaná oznámení|Počet|celkem|Čekající plánovaná oznámení|Žádné dimenze|
|Installation.all|Operace správy instalace|Počet|celkem|Operace správy instalace|Žádné dimenze|
|Installation.Get|Operace získání instalace|Počet|celkem|Operace získání instalace|Žádné dimenze|
|Installation.upsert|Vytvořit nebo aktualizovat operace instalace|Počet|celkem|Vytvořit nebo aktualizovat operace instalace|Žádné dimenze|
|Installation.patch|Opravit operace instalace|Počet|celkem|Opravit operace instalace|Žádné dimenze|
|installation.delete|Operace odstranění instalace|Počet|celkem|Operace odstranění instalace|Žádné dimenze|
|outgoing.allpns.success|Úspěšná oznámení|Počet|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.allpns.invalidpayload|Chyby datové části|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy vrátil chybu, která se týká chybné datové části|Žádné dimenze|
|outgoing.allpns.pnserror|Chyby externího systému oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože při komunikaci se systémem oznámení platformy došlo k problému (nezahrnuje problémy s ověřováním)|Žádné dimenze|
|outgoing.allpns.channelerror|Chyby kanálů|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože kanál nebyl platný nebo přidružený ke správné aplikaci, byl omezený, nebo jeho platnost vypršela|Žádné dimenze|
|outgoing.allpns.badorexpiredchannel|Chyby – chybný kanál nebo vypršení časového limitu kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršel nebo není platný kanál, token nebo ID registrace|Žádné dimenze|
|outgoing.wns.success|WNS – úspěšná oznámení|Počet|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.wns.invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované (Windows Live nerozpoznal přihlašovací údaje).|Žádné dimenze|
|outgoing.wns.badchannel|WNS – chyba špatného kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože se v registraci nerozpoznal parametr ChannelURI (stav Služby nabízených oznámení Windows: 404 Nenalezeno)|Žádné dimenze|
|outgoing.wns.expiredchannel|WNS – chyba vypršení časového limitu kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost parametru ChannelURI (stav Služby nabízených oznámení Windows: 410 Trvale není k dispozici)|Žádné dimenze|
|outgoing.wns.throttled|WNS – omezená oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože Služba nabízených oznámení Windows omezuje tuto aplikaci (stav Služby nabízených oznámení Windows: 406 Nepřijatelný požadavek)|Žádné dimenze|
|outgoing.wns.tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Počet|celkem|Služby Windows Live nejsou dostupné.|Žádné dimenze|
|outgoing.wns.invalidtoken|WNS – chyby autorizace (neplatný token)|Počet|celkem|Token, který se poskytl Službě nabízených oznámení Windows, není platný (stav Služby nabízených oznámení Windows: 401 Neautorizováno).|Žádné dimenze|
|outgoing.wns.wrongtoken|WNS – chyby autorizace (chybný token)|Počet|celkem|Zadaný pro WNS token je platný, ale pro jinou aplikaci (WNS stav: 403 Zakázáno). To může nastat, když ChannelURI v registraci souvisí s jinou aplikací. Zkontrolujte, jestli klientská aplikace je přidružená ke stejné aplikaci, jehož pověření se v centru oznámení.|Žádné dimenze|
|outgoing.wns.invalidnotificationformat|WNS – neplatný formát oznámení|Počet|celkem|Formát oznámení není platný (stav WNS: 400). Všimněte si, že WNS není odmítnout všechny neplatné datové části.|Žádné dimenze|
|outgoing.wns.invalidnotificationsize|WNS – chyba neplatné velikosti oznámení|Počet|celkem|Datová část oznámení je moc velká (stav Služby nabízených oznámení Windows: 413).|Žádné dimenze|
|outgoing.wns.channelthrottled|WNS – omezený kanál|Počet|celkem|Oznámení se ignoruje, protože v registraci se omezuje parametr ChannelURI (hlavička odpovědi Služby nabízených oznámení Windows: X-WNS-NotificationStatus:channelThrottled).|Žádné dimenze|
|outgoing.wns.channeldisconnected|WNS – odpojený kanál|Počet|celkem|Oznámení se ignoruje, protože v registraci se omezuje parametr ChannelURI (hlavička odpovědi Služby nabízených oznámení Windows: X-WNS-DeviceConnectionStatus: disconnected).|Žádné dimenze|
|outgoing.wns.dropped|WNS – vynechaná oznámení|Počet|celkem|Oznámení se ignoruje, protože v registraci se omezuje parametr ChannelURI (X-WNS-NotificationStatus: dropped, ale ne X-WNS-DeviceConnectionStatus: disconnected).|Žádné dimenze|
|outgoing.wns.pnserror|WNS – chyby|Počet|celkem|Oznámení se nedoručilo kvůli chybám při komunikaci se Službou nabízených oznámení Windows.|Žádné dimenze|
|outgoing.wns.authenticationerror|WNS – chyby ověřování|Počet|celkem|Oznámení se nedoručilo kvůli chybám při komunikaci s Windows Live, neplatným přihlašovacím údajům, nebo chybnému tokenu.|Žádné dimenze|
|outgoing.apns.success|APNS – úspěšná oznámení|Počet|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.apns.invalidcredentials|Chyby autorizace APNs|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.apns.badchannel|Chyba APNS – špatný kanál|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (kód stavu APNs: 8)|Žádné dimenze|
|outgoing.apns.expiredchannel|Chyba APNS – kanálu vypršel časový limit|Počet|celkem|Počet tokenů, kterým kanál zpětné vazby APNs zrušil platnost|Žádné dimenze|
|outgoing.apns.invalidnotificationsize|APNS – chyba neplatné velikosti oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla moc velká (kód stavu APNs: 7)|Žádné dimenze|
|outgoing.apns.pnserror|APNS – chyby|Počet|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNs|Žádné dimenze|
|outgoing.gcm.success|GCM – úspěšná oznámení|Počet|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.gcm.invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.gcm.badchannel|GCM – chyba špatného kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože se v registraci nerozpoznal parametr registrationId (výsledek GCM: Invalid Registration)|Žádné dimenze|
|outgoing.gcm.expiredchannel|GCM – chyba vypršení časového limitu kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered)|Žádné dimenze|
|outgoing.gcm.throttled|GCM – omezená oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože služba GCM omezila tuto aplikaci (kód stavu GCM: 501–599 nebo výsledek: Unavailable)|Žádné dimenze|
|outgoing.gcm.invalidnotificationformat|GCM – neplatný formát oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože datová část neměla správný formát (výsledek GCM: InvalidDataKey nebo InvalidTtl)|Žádné dimenze|
|outgoing.gcm.invalidnotificationsize|GCM – chyba neplatné velikosti oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla moc velká (výsledek GCM: MessageTooBig)|Žádné dimenze|
|outgoing.gcm.wrongchannel|GCM – chyba nesprávného kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružené k aktuální aplikaci (výsledek GCM: InvalidPackageName)|Žádné dimenze|
|outgoing.gcm.pnserror|GCM – chyby|Počet|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádné dimenze|
|outgoing.gcm.authenticationerror|Chyby ověřování GCM|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované, nebo v aplikaci není správně nakonfigurované SenderId (výsledek GCM: MismatchedSenderId)|Žádné dimenze|
|outgoing.mpns.success|MPNS – úspěšná oznámení|Počet|celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|outgoing.mpns.invalidcredentials|MPNS – neplatné přihlašovací údaje|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|outgoing.mpns.badchannel|MPNS – chyba špatného kanálu|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože se v registraci nerozpoznal parametr ChannelURI (stav MPNS: 404 Nenalezeno)|Žádné dimenze|
|outgoing.mpns.throttled|MPNS – omezená oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 Nepřijatelný požadavek)|Žádné dimenze|
|outgoing.mpns.invalidnotificationformat|MPNS – neplatný formát oznámení|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože datová část v oznámení byla moc velká|Žádné dimenze|
|outgoing.mpns.channeldisconnected|MPNS – odpojený kanál|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože parametr ChannelURI v registraci byl odpojený (stav MPNS: 412 Nenalezeno)|Žádné dimenze|
|outgoing.mpns.dropped|MPNS – vynechaná oznámení|Počet|celkem|Počet nabídek, které služba MPNS ignorovala (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo Suppressed)|Žádné dimenze|
|outgoing.mpns.pnserror|MPNS – chyby|Počet|celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádné dimenze|
|outgoing.mpns.authenticationerror|MPNS – chyby ověřování|Počet|celkem|Počet nabídek, které nebyly úspěšné, protože systém oznámení platformy nepřijal poskytnuté přihlašovací údaje, nebo jsou přihlašovací údaje blokované|Žádné dimenze|
|notificationhub.pushes|Všechna odchozí oznámení|Počet|celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|incoming.all.requests|Všechny příchozí požadavky|Počet|celkem|Celkový počet příchozích požadavků pro centrum oznámení|Žádné dimenze|
|incoming.all.failedrequests|Všechny neúspěšné příchozí požadavky|Počet|celkem|Celkový počet neúspěšných příchozích požadavků pro centrum oznámení|Žádné dimenze|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekundy|Průměr|Dotaz DAX doba trvání posledního intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vláken: Délka fronty úloh fondu dotazu|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|Žádné dimenze|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(Verze public Preview)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_ % volných uzlů Inode|Average_ % volných uzlů Inode|Počet|Průměr|Average_ % volných uzlů Inode|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|Average_ % volného místa|Počet|Průměr|Average_ % volného místa|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % použitých uzlů Inode|Average_ % použitých uzlů Inode|Počet|Průměr|Average_ % použitých uzlů Inode|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Využitý prostor v Average_ %|Využitý prostor v Average_ %|Počet|Průměr|Využitý prostor v Average_ %|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přečtených bajtů/s|Average_Disk přečtených bajtů/s|Počet|Průměr|Average_Disk přečtených bajtů/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk čtení/s|Average_Disk čtení/s|Počet|Průměr|Average_Disk čtení/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Average_Disk přenosy/s|Počet|Průměr|Average_Disk přenosy/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Zápis Average_Disk bajty/s|Zápis Average_Disk bajty/s|Počet|Průměr|Zápis Average_Disk bajty/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zápisů za sekundu|Average_Disk zápisů za sekundu|Počet|Průměr|Average_Disk zápisů za sekundu|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free MB|Average_Free MB|Počet|Průměr|Average_Free MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Logical bajtů disku/s|Average_Logical bajtů disku/s|Počet|Průměr|Average_Logical bajtů disku/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Dostupná paměť v % Average_|Dostupná paměť v % Average_|Počet|Průměr|Dostupná paměť v % Average_|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % dostupného odkládacího prostoru|Average_ % dostupného odkládacího prostoru|Počet|Průměr|Average_ % dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % použité paměti|Average_ % použité paměti|Počet|Průměr|Average_ % použité paměti|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Využitý prostor záměny v Average_ %|Využitý prostor záměny v Average_ %|Počet|Průměr|Využitý prostor záměny v Average_ %|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Paměť v megabajtech Average_Available|Paměť v megabajtech Average_Available|Počet|Průměr|Paměť v megabajtech Average_Available|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available prostor záměny v MB|Average_Available prostor záměny v MB|Počet|Průměr|Average_Available prostor záměny v MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page čtení/s|Average_Page čtení/s|Počet|Průměr|Average_Page čtení/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Page zápisů za sekundu|Average_Page zápisů za sekundu|Počet|Průměr|Average_Page zápisů za sekundu|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pages za sekundu|Average_Pages za sekundu|Počet|Průměr|Average_Pages za sekundu|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used prostor záměny v MB|Average_Used prostor záměny v MB|Počet|Průměr|Average_Used prostor záměny v MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Used paměť v MB|Average_Used paměť v MB|Počet|Průměr|Average_Used paměť v MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total bajtů přenesených|Average_Total bajtů přenesených|Počet|Průměr|Average_Total bajtů přenesených|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Počet přijatých bajtů Average_Total|Počet přijatých bajtů Average_Total|Počet|Průměr|Počet přijatých bajtů Average_Total|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total bajtů|Average_Total bajtů|Počet|Průměr|Average_Total bajtů|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total pakety odesílané informace|Average_Total pakety odesílané informace|Počet|Průměr|Average_Total pakety odesílané informace|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Obdržených paketů Average_Total|Obdržených paketů Average_Total|Počet|Průměr|Obdržených paketů Average_Total|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby příjmu Average_Total|Chyby příjmu Average_Total|Počet|Průměr|Chyby příjmu Average_Total|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Chyby odeslání Average_Total|Chyby odeslání Average_Total|Počet|Průměr|Chyby odeslání Average_Total|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Total kolizí|Average_Total kolizí|Počet|Průměr|Average_Total kolizí|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Average_Avg. Doba disku/čtení|Počet|Průměr|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/přenos|Average_Avg. Doba disku/přenos|Počet|Průměr|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/zápis|Average_Avg. Doba disku/zápis|Počet|Průměr|Average_Avg. Doba disku/zápis|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Physical bajtů disku/s|Average_Physical bajtů disku/s|Počet|Průměr|Average_Physical bajtů disku/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct privilegovaného času|Average_Pct privilegovaného času|Počet|Průměr|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Pct uživatelského času|Average_Pct uživatelského času|Počet|Průměr|Average_Pct uživatelského času|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Paměť Average_Used kB|Paměť Average_Used kB|Počet|Průměr|Paměť Average_Used kB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Virtual sdílené paměti|Average_Virtual sdílené paměti|Počet|Průměr|Average_Virtual sdílené paměti|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času DPC Average_|% Času DPC Average_|Počet|Průměr|% Času DPC Average_|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času nečinnosti|Average_ % času nečinnosti|Počet|Průměr|Average_ % času nečinnosti|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|% Času přerušení Average_|% Času přerušení Average_|Počet|Průměr|% Času přerušení Average_|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Doba čekání vstupů % Average_|Doba čekání vstupů % Average_|Počet|Průměr|Doba čekání vstupů % Average_|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ dobrý čas v %|Average_ dobrý čas v %|Počet|Průměr|Average_ dobrý čas v %|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % privilegovaného času|Average_ % privilegovaného času|Počet|Průměr|Average_ % privilegovaného času|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|Average_ % času procesoru|Počet|Průměr|Average_ % času procesoru|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % uživatelského času|Average_ % uživatelského času|Počet|Průměr|Average_ % uživatelského času|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free fyzické paměti|Average_Free fyzické paměti|Počet|Průměr|Average_Free fyzické paměti|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free místo ve stránkovacích souborech|Average_Free místo ve stránkovacích souborech|Počet|Průměr|Average_Free místo ve stránkovacích souborech|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free virtuální paměti|Average_Free virtuální paměti|Počet|Průměr|Average_Free virtuální paměti|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Processes|Average_Processes|Počet|Průměr|Average_Processes|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Size uložená v stránkovacích souborech|Average_Size uložená v stránkovacích souborech|Počet|Průměr|Average_Size uložená v stránkovacích souborech|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Uptime|Average_Uptime|Počet|Průměr|Average_Uptime|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Users|Average_Users|Počet|Průměr|Average_Users|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/čtení|Average_Avg. Doba disku/čtení|Počet|Průměr|Average_Avg. Doba disku/čtení|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Avg. Doba disku/zápis|Average_Avg. Doba disku/zápis|Počet|Průměr|Average_Avg. Doba disku/zápis|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty disku Average_Current|Délka fronty disku Average_Current|Počet|Průměr|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk čtení/s|Average_Disk čtení/s|Počet|Průměr|Average_Disk čtení/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk přenosy/s|Average_Disk přenosy/s|Počet|Průměr|Average_Disk přenosy/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Disk zápisů za sekundu|Average_Disk zápisů za sekundu|Počet|Průměr|Average_Disk zápisů za sekundu|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Free MB|Average_Free MB|Počet|Průměr|Average_Free MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % volného místa|Average_ % volného místa|Počet|Průměr|Average_ % volného místa|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Available MB|Average_Available MB|Počet|Průměr|Average_Available MB|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % potvrzených bajtů v použití|Average_ % potvrzených bajtů v použití|Počet|Průměr|Average_ % potvrzených bajtů v použití|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Bytes Received/sec|Average_Bytes Received/sec|Počet|Průměr|Average_Bytes Received/sec|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Bytes odeslaných za sekundu|Average_Bytes odeslaných za sekundu|Počet|Průměr|Average_Bytes odeslaných za sekundu|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_Bytes celkem/s|Average_Bytes celkem/s|Počet|Průměr|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Average_ % času procesoru|Average_ % času procesoru|Počet|Průměr|Average_ % času procesoru|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Délka fronty Average_Processor|Délka fronty Average_Processor|Počet|Průměr|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, Cesta_k_čítači, SourceSystem|
|Prezenční signál|Prezenční signál|Počet|Průměr|Prezenční signál|Počítač, OSType, verze, SourceComputerId|
|Aktualizovat|Aktualizovat|Počet|Průměr|Aktualizovat|Počítač, produktů, klasifikace, UpdateState, volitelné, schválené|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Počet|celkem|Úspěšná připojení ListenerConnections pro Microsoft.Relay|entityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Počet|celkem|ClientError v ListenerConnections pro Microsoft.Relay|entityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Počet|celkem|ServerError na ListenerConnections pro Microsoft.Relay|entityName|
|SenderConnections-Success|SenderConnections-Success|Počet|celkem|Úspěšná připojení SenderConnections pro Microsoft.Relay|entityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Počet|celkem|ClientError v SenderConnections pro Microsoft.Relay|entityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Počet|celkem|ServerError na SenderConnections pro Microsoft.Relay|entityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Počet|celkem|ListenerConnections celkem pro Microsoft.Relay|entityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Počet|celkem|Celkem žádostí SenderConnections pro Microsoft.Relay|entityName|
|ActiveConnections|ActiveConnections|Počet|celkem|ActiveConnections celkem pro Microsoft.Relay|entityName|
|ActiveListeners|ActiveListeners|Počet|celkem|ActiveListeners celkem pro Microsoft.Relay|entityName|
|BytesTransferred|BytesTransferred|Počet|celkem|BytesTransferred celkem pro Microsoft.Relay|entityName|
|ListenerDisconnects|ListenerDisconnects|Počet|celkem|ListenerDisconnects celkem pro Microsoft.Relay|entityName|
|SenderDisconnects|SenderDisconnects|Počet|celkem|SenderDisconnects celkem pro Microsoft.Relay|entityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence vyhledávání|Sekundy|Průměr|Hledání Průměrná latence pro službu vyhledávání.|Žádné dimenze|
|SearchQueriesPerSecond|Vyhledávací dotazy za sekundu|CountPerSecond|Průměr|Vyhledávací dotazy za sekundu pro službu vyhledávání.|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Procenta|Průměr|Procento vyhledávací dotazy, které byly omezené pro službu vyhledávání.|Žádné dimenze|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky (Preview)|Počet|celkem|Celkový počet úspěšných požadavků pro obor názvů (Preview)|EntityName, |
|ServerErrors|Chyby serveru (Preview)|Počet|celkem|Chyby serveru pro Microsoft.ServiceBus (Preview)|EntityName, |
|UserErrors|Chyby uživatele (Preview)|Počet|celkem|Chyby uživatele pro Microsoft.ServiceBus (Preview)|EntityName, |
|ThrottledRequests|Omezené požadavky (Preview)|Počet|celkem|Omezené požadavky pro Microsoft.ServiceBus (Preview)|EntityName, |
|IncomingRequests|Příchozí žádosti (Preview)|Počet|celkem|Příchozí požadavky pro Microsoft.ServiceBus (Preview)|entityName|
|IncomingMessages|Příchozí zprávy (Preview)|Počet|celkem|Příchozí zprávy pro Microsoft.ServiceBus (Preview)|entityName|
|OutgoingMessages|Odchozí zprávy (Preview)|Počet|celkem|Odchozí zprávy pro Microsoft.ServiceBus (Preview)|entityName|
|ActiveConnections|ActiveConnections (Preview)|Počet|celkem|Celkový počet aktivních připojení pro Microsoft.ServiceBus (Preview)|Žádné dimenze|
|Velikost|Velikost (Preview)|bajtů|Průměr|Velikost fronta nebo téma v bajtech. (Preview)|entityName|
|Zprávy|Počet zpráv ve frontě nebo téma. (Preview)|Počet|Průměr|Počet zpráv ve frontě nebo téma. (Preview)|entityName|
|ActiveMessages|Počet active zprávy ve frontě nebo téma. (Preview)|Počet|Průměr|Počet active zprávy ve frontě nebo téma. (Preview)|entityName|
|CPUXNS|Využití CPU na obor názvů|Procenta|Maximálně|Metrika využití procesoru v oboru názvů služby Service Bus na úrovni Premium |Žádné dimenze|
|WSXNS|Využití paměti na obor názvů|Procenta|Maximálně|Metrika využití paměti v oboru názvů služby Service Bus na úrovni Premium |Žádné dimenze|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Procenta|Průměr|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Procenta|Průměr|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procento vstupně-výstupní operace protokolu|Procenta|Průměr|Procento vstupně-výstupní operace protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Procenta|Průměr|Procento DTU|Žádné dimenze|
|úložiště|Velikost celkový databáze|bajtů|Maximálně|Velikost celkový databáze|Žádné dimenze|
|connection_successful|Úspěšné připojení|Počet|celkem|Úspěšné připojení|Žádné dimenze|
|connection_failed|Neúspěšné připojení|Počet|celkem|Neúspěšné připojení|Žádné dimenze|
|blocked_by_firewall|Blokováno bránou Firewall|Počet|celkem|Blokováno bránou Firewall|Žádné dimenze|
|zablokování|Zablokování|Počet|celkem|Zablokování|Žádné dimenze|
|storage_percent|Procento velikosti databáze|Procenta|Maximálně|Procento velikosti databáze|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Procenta|Průměr|Procento úložiště OLTP v paměti|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Procenta|Průměr|Procento pracovních procesů|Žádné dimenze|
|sessions_percent|Procento relací|Procenta|Průměr|Procento relací|Žádné dimenze|
|dtu_limit|Omezení jednotek DTU|Počet|Průměr|Omezení jednotek DTU|Žádné dimenze|
|dtu_used|Použít DTU|Počet|Průměr|Použít DTU|Žádné dimenze|
|dwu_limit|DWU limit|Počet|Maximálně|DWU limit|Žádné dimenze|
|dwu_consumption_percent|Procento DWU|Procenta|Maximálně|Procento DWU|Žádné dimenze|
|dwu_used|DWU použít|Počet|Maximálně|DWU použít|Žádné dimenze|
|dw_cpu_percent|Úroveň uzlu DW procento využití procesoru|Procenta|Průměr|Úroveň uzlu DW procento využití procesoru|DwLogicalNodeId|
|dw_physical_data_read_percent|Procento úroveň vstupně-výstupní operace dat datového skladu uzlu|Procenta|Průměr|Procento úroveň vstupně-výstupní operace dat datového skladu uzlu|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procentuální využití procesoru|Procenta|Průměr|Procentuální využití procesoru|Žádné dimenze|
|physical_data_read_percent|Procento datových V/V|Procenta|Průměr|Procento datových V/V|Žádné dimenze|
|log_write_percent|Procento vstupně-výstupní operace protokolu|Procenta|Průměr|Procento vstupně-výstupní operace protokolu|Žádné dimenze|
|dtu_consumption_percent|Procento DTU|Procenta|Průměr|Procento DTU|Žádné dimenze|
|storage_percent|Procento úložiště|Procenta|Průměr|Procento úložiště|Žádné dimenze|
|workers_percent|Procento pracovních procesů|Procenta|Průměr|Procento pracovních procesů|Žádné dimenze|
|sessions_percent|Procento relací|Procenta|Průměr|Procento relací|Žádné dimenze|
|eDTU_limit|omezení eDTU|Počet|Průměr|omezení eDTU|Žádné dimenze|
|storage_limit|Limit úložiště|bajtů|Průměr|Limit úložiště|Žádné dimenze|
|eDTU_used|eDTU použít|Počet|Průměr|eDTU použít|Žádné dimenze|
|storage_used|Využité úložiště|bajtů|Průměr|Využité úložiště|Žádné dimenze|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Procenta|Průměr|Procento úložiště OLTP v paměti|Žádné dimenze|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procento DTU|Procenta|Průměr|Procento DTU|ElasticPoolResourceId|
|storage_used|Využité úložiště|bajtů|Průměr|Využité úložiště|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Použitá kapacita|bajtů|Průměr|Kapacita využitá účtem|Žádné dimenze|
|Transakce|Transakce|Počet|celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje úspěšné i neúspěšné požadavky, jakož i požadavků, které vytváří chyby. Hodnota ResponseType dimenzi používejte pro počet jiný typ odpovědi.|ResponseType, GeoType, ApiName|
|Příchozí přenos|Příchozí přenos|bajtů|celkem|Množství dat příjem příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName|
|Výchozí přenos|Výchozí přenos|bajtů|celkem|Množství odchozí data v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence úložiště Azure používá ke zpracování v případě úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě, zadaný v AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšné požadavky na služby úložiště nebo zadaná operace rozhraní API, v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName|
|Dostupnost|Dostupnost|Procenta|Průměr|Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota TotalBillableRequests dělení podle počtu příslušné požadavky, včetně těch, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita služby Blob|bajtů|celkem|Velikost úložiště využitá službou Blob účtu úložiště v bajtech|BlobType|
|BlobCount|Počet objektů blob|Počet|celkem|Počet objektů blob ve službě Blob účtu úložiště|BlobType|
|ContainerCount|Počet kontejnerů služby Blob|Počet|Průměr|Počet kontejnerů ve službě Blob účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje úspěšné i neúspěšné požadavky, jakož i požadavků, které vytváří chyby. Hodnota ResponseType dimenzi používejte pro počet jiný typ odpovědi.|ResponseType, GeoType, ApiName|
|Příchozí přenos|Příchozí přenos|bajtů|celkem|Množství dat příjem příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName|
|Výchozí přenos|Výchozí přenos|bajtů|celkem|Množství odchozí data v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence úložiště Azure používá ke zpracování v případě úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě, zadaný v AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšné požadavky na služby úložiště nebo zadaná operace rozhraní API, v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName|
|Dostupnost|Dostupnost|Procenta|Průměr|Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota TotalBillableRequests dělení podle počtu příslušné požadavky, včetně těch, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita služby Table|bajtů|Průměr|Velikost úložiště využitá službou Table účtu úložiště v bajtech|Žádné dimenze|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek ve službě Table účtu úložiště|Žádné dimenze|
|TableEntityCount|Počet entit tabulek|Počet|Průměr|Počet entit tabulek ve službě Table účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje úspěšné i neúspěšné požadavky, jakož i požadavků, které vytváří chyby. Hodnota ResponseType dimenzi používejte pro počet jiný typ odpovědi.|ResponseType, GeoType, ApiName|
|Příchozí přenos|Příchozí přenos|bajtů|celkem|Množství dat příjem příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName|
|Výchozí přenos|Výchozí přenos|bajtů|celkem|Množství odchozí data v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence úložiště Azure používá ke zpracování v případě úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě, zadaný v AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšné požadavky na služby úložiště nebo zadaná operace rozhraní API, v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName|
|Dostupnost|Dostupnost|Procenta|Průměr|Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota TotalBillableRequests dělení podle počtu příslušné požadavky, včetně těch, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita služby Queue|bajtů|Průměr|Velikost úložiště využitá službou Queue účtu úložiště v bajtech|Žádné dimenze|
|QueueCount|Počet front|Počet|Průměr|Počet front ve službě Queue účtu úložiště|Žádné dimenze|
|QueueMessageCount|Počet zpráv fronty|Počet|Průměr|Přibližný počet zpráv fronty ve službě Queue účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje úspěšné i neúspěšné požadavky, jakož i požadavků, které vytváří chyby. Hodnota ResponseType dimenzi používejte pro počet jiný typ odpovědi.|ResponseType, GeoType, ApiName|
|Příchozí přenos|Příchozí přenos|bajtů|celkem|Množství dat příjem příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName|
|Výchozí přenos|Výchozí přenos|bajtů|celkem|Množství odchozí data v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence úložiště Azure používá ke zpracování v případě úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě, zadaný v AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšné požadavky na služby úložiště nebo zadaná operace rozhraní API, v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName|
|Dostupnost|Dostupnost|Procenta|Průměr|Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota TotalBillableRequests dělení podle počtu příslušné požadavky, včetně těch, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FileCapacity|Kapacita služby File|bajtů|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Žádné dimenze|
|FileCount|Počet souborů|Počet|Průměr|Počet souborů ve službě File účtu úložiště|Žádné dimenze|
|FileShareCount|Počet sdílených složek služby File|Počet|Průměr|Počet sdílených složek ve službě File účtu úložiště|Žádné dimenze|
|Transakce|Transakce|Počet|celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje úspěšné i neúspěšné požadavky, jakož i požadavků, které vytváří chyby. Hodnota ResponseType dimenzi používejte pro počet jiný typ odpovědi.|ResponseType, GeoType, ApiName|
|Příchozí přenos|Příchozí přenos|bajtů|celkem|Množství dat příjem příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|GeoType, ApiName|
|Výchozí přenos|Výchozí přenos|bajtů|celkem|Množství odchozí data v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|GeoType, ApiName|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekundy|Průměr|Průměrná latence úložiště Azure používá ke zpracování v případě úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě, zadaný v AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekundy|Průměr|Průměrná latence začátku do konce úspěšné požadavky na služby úložiště nebo zadaná operace rozhraní API, v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|GeoType, ApiName|
|Dostupnost|Dostupnost|Procenta|Průměr|Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota TotalBillableRequests dělení podle počtu příslušné požadavky, včetně těch, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|% využití SU|Procenta|Maximálně|% využití SU|Žádné dimenze|
|InputEvents|Vstupní události|Počet|celkem|Vstupní události|Žádné dimenze|
|InputEventBytes|Bajty vstupních událostí|bajtů|celkem|Bajty vstupních událostí|Žádné dimenze|
|LateInputEvents|Pozdní vstupní události|Počet|celkem|Pozdní vstupní události|Žádné dimenze|
|OutputEvents|Výstupní události|Počet|celkem|Výstupní události|Žádné dimenze|
|ConversionErrors|Chyby převodu dat|Počet|celkem|Chyby převodu dat|Žádné dimenze|
|Chyby|Chyby za běhu|Počet|celkem|Chyby za běhu|Žádné dimenze|
|DroppedOrAdjustedEvents|Události mimo pořadí|Počet|celkem|Události mimo pořadí|Žádné dimenze|
|AMLCalloutRequests|Požadavky na funkce|Počet|celkem|Požadavky na funkce|Žádné dimenze|
|AMLCalloutFailedRequests|Nezdařené požadavky na funkce|Počet|celkem|Nezdařené požadavky na funkce|Žádné dimenze|
|AMLCalloutInputEvents|Události funkcí|Počet|celkem|Události funkcí|Žádné dimenze|
|DeserializationError|Chyby deserializace vstupu|Počet|celkem|Chyby deserializace vstupu|Žádné dimenze|
|EarlyInputEvents|Události, jejichž čas aplikace je dřívější než jejich času příchodu.|Počet|celkem|Události, jejichž čas aplikace je dřívější než jejich času příchodu.|Žádné dimenze|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento CPU|Procenta|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Procenta|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty HTTP|Počet|Průměr|Délka fronty HTTP|Instance|
|BytesReceived|Vstupní data|bajtů|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|bajtů|celkem|Výstupní data|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (s výjimkou funkce)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|celkem|Žádosti|Instance|
|BytesReceived|Vstupní data|bajtů|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|bajtů|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Počet|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Počet|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Počet|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Počet|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Počet|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Počet|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|bajtů|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|bajtů|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Popisovače|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funkce)

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Vstupní data|bajtů|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|bajtů|celkem|Výstupní data|Instance|
|Http5xx|Chyby serveru HTTP|Počet|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|bajtů|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|bajtů|Průměr|Průměrná pracovní sada paměti|Instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|Počet|celkem|Jednotky provádění funkcí|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|celkem|Počet spuštění funkce|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|celkem|Žádosti|Instance|
|BytesReceived|Vstupní data|bajtů|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|bajtů|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Počet|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Počet|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Počet|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Počet|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Počet|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Počet|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|celkem|Chyby serveru HTTP|Instance|
|MemoryWorkingSet|Pracovní sada paměti|bajtů|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|bajtů|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|FunctionExecutionUnits|Jednotky provádění funkcí|Počet|celkem|Jednotky provádění funkcí|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|celkem|Počet spuštění funkce|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Popisovače|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Žádosti|Žádosti|Počet|celkem|Žádosti|Instance|
|BytesReceived|Vstupní data|bajtů|celkem|Vstupní data|Instance|
|BytesSent|Výstupní data|bajtů|celkem|Výstupní data|Instance|
|Http101|HTTP 101|Počet|celkem|HTTP 101|Instance|
|Http2xx|HTTP 2xx|Počet|celkem|HTTP 2xx|Instance|
|Http3xx|HTTP 3xx|Počet|celkem|HTTP 3xx|Instance|
|Http401|HTTP 401|Počet|celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|celkem|HTTP 404|Instance|
|Http406|HTTP 406|Počet|celkem|HTTP 406|Instance|
|Http4xx|HTTP 4xx|Počet|celkem|HTTP 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|celkem|Chyby serveru HTTP|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|CpuPercentage|Procento CPU|Procenta|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Procenta|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty HTTP|Počet|Průměr|Délka fronty HTTP|Instance|
|ActiveRequests|Aktivní požadavky|Počet|celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet front-endů|Počet|Průměr|Celkový počet front-endů|Žádné dimenze|
|SmallAppServicePlanInstances|Malé pracovní procesy plánu služby App Service|Počet|Průměr|Malé pracovní procesy plánu služby App Service|Žádné dimenze|
|MediumAppServicePlanInstances|Střední pracovní procesy plánu služby App Service|Počet|Průměr|Střední pracovní procesy plánu služby App Service|Žádné dimenze|
|LargeAppServicePlanInstances|Velké pracovní procesy plánu služby App Service|Počet|Průměr|Velké pracovní procesy plánu služby App Service|Žádné dimenze|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Hodnoty WorkersTotal|Celkový počet pracovních procesů|Počet|Průměr|Celkový počet pracovních procesů|Žádné dimenze|
|WorkersAvailable|Dostupné pracovní procesy|Počet|Průměr|Dostupné pracovní procesy|Žádné dimenze|
|Workersused za|Využité pracovní procesy|Počet|Průměr|Využité pracovní procesy|Žádné dimenze|
|CpuPercentage|Procento CPU|Procenta|Průměr|Procento CPU|Instance|
|MemoryPercentage|Procento paměti|Procenta|Průměr|Procento paměti|Instance|

## <a name="next-steps"></a>Další postup
* [Přečtěte si informace o metriky v Azure monitorování](monitoring-overview-metrics.md)
* [Vytvářet upozornění na metriky](insights-receive-alert-notifications.md)
* [Export metriky pro úložiště, centra událostí nebo analýzy protokolů](monitoring-overview-of-diagnostic-logs.md)
