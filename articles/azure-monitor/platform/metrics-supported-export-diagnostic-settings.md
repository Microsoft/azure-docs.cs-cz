---
title: Azure Monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: dcf5276393400be864e738d89bc5713f5aac242b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963474"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky

Azure Monitor poskytuje ve výchozím nastavení [metriky platforem](data-platform-metrics.md) bez konfigurace. Nabízí několik způsobů, jak pracovat s metrikami platforem, včetně jejich grafů na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Úplný seznam metrik platforem, které jsou aktuálně k dispozici v konsolidovaném kanálu metriky Azure Monitor, najdete v tématu [metriky – podporované](metrics-supported.md) . Pro dotazování a přístup k těmto metrikám použijte [verzi api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

Metriky platformy můžete exportovat z kanálu Azure monitor do jiných umístění jedním ze dvou způsobů.
1. Použití [nastavení diagnostiky](diagnostic-settings.md) k odeslání do Log Analytics, Event Hubs nebo Azure Storage.
2. Použít [REST API metriky](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Vzhledem k tomu, že v Azure Monitor back-endu složitými rozhraními, nejsou všechny metriky exportovatelné pomocí nastavení diagnostiky. Následující tabulka uvádí, které lze a nelze exportovat pomocí nastavení diagnostiky.

Tabulka obsahuje následující sloupce. 
- Exportovatelné přes nastavení diagnostiky? 
- ResourceType 
- Metrika 
- MetricDisplayName
- Jednotka 
- AggregationType


> [!NOTE]
> Následující tabulka může mít v dolní části vodorovný posuvník. Pokud se domníváte, že jste neztratili nějaké informace, zkontrolujte, že posuvník je zcela vlevo.  


Exportovatelné přes nastavení diagnostiky? | ResourceType | Metrika | MetricDisplayName | Jednotka | AggregationType
|----|-----|------|----|----|-----|
Ano | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Paměť: aktuální cena čisticího modulu | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Paměť: nezmenšovaná paměť čisticí paměti | B | Průměr
Ano | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Paměť: velikost čisticí paměti | B | Průměr
Ano | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Vlákna: zaneprázdněná vlákna fondu příkazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Vlákna: nečinná vlákna fondu příkazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Délka fronty úloh fondu příkazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | CurrentConnections | Připojení: aktuální připojení | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | CurrentUserSessions | Aktuální uživatelské relace | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Vlákna: zaneprázdněná vlákna s dlouhou analýzou | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Vlákna: nečinná vlákna při dlouhé analýze | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Vlákna: délka fronty úloh dlouhého rozboru | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | Paměť motoru M | B | Průměr
Ano | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | Počet privátních bajtů modulu M | B | Průměr
Ano | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | QPU modulu M | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | Virtuální bajty motoru M | B | Průměr
Ano | Microsoft.AnalysisServices/servers | memory_metric | Paměť | B | Průměr
Ano | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Thrashing paměti | Procento | Průměr
Ano | Microsoft.AnalysisServices/servers | MemoryLimitHard | Paměť: limit paměti – pevný | B | Průměr
Ano | Microsoft.AnalysisServices/servers | Hodnota memorylimithigh | Paměť: limit paměti – vysoká | B | Průměr
Ano | Microsoft.AnalysisServices/servers | MemoryLimitLow | Paměť: limit paměti – nízká | B | Průměr
Ano | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Paměť: limit paměti VertiPaq | B | Průměr
Ano | Microsoft.AnalysisServices/servers | MemoryUsage | Paměť: využití paměti | B | Průměr
Ano | Microsoft.AnalysisServices/servers | private_bytes_metric | Soukromé bajty | B | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Vlákna: vlákna nečinných úloh v/v fondu zpracování | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Vlákna: nečinná vlákna v/v fondu zpracování | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Vlákna: délka fronty úloh v/v fondu zpracování | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Délka fronty úloh zpracování fondu | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Zaneprázdněná vlákna fondu dotazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Vlákna: nečinná vlákna fondu dotazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Vlákna: délka fronty úloh fondu dotazů | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | Kvóta | Paměť: kvóta | B | Průměr
Ano | Microsoft.AnalysisServices/servers | QuotaBlocked | Paměť: kvóta blokována | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Zpracování: počet převedených řádků za sekundu | CountPerSecond | Průměr
Ano | Microsoft.AnalysisServices/servers | RowsReadPerSec | Zpracování: Počet přečtených řádků za sekundu | CountPerSecond | Průměr
Ano | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Zpracování: počet zapsaných řádků za sekundu | CountPerSecond | Průměr
Ano | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Vlákna: krátkodobá analýza zaneprázdněných vláken | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Vlákna: nečinná vlákna krátké analýzy | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Vlákna: délka fronty úlohy krátké analýzy | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Úspěšná připojení za sekundu | CountPerSecond | Průměr
Ano | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Celkový počet selhání připojení | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Požadavky na připojení celkem | Počet | Průměr
Ano | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Paměť: VertiPaq nestránkované | B | Průměr
Ano | Microsoft.AnalysisServices/servers | VertiPaqPaged | Paměť: VertiPaq stránkované | B | Průměr
Ano | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Virtuální bajty | B | Průměr
Ano | Microsoft.ApiManagement/service | BackendDuration | Doba trvání požadavků back-endu | Milisekundy | Průměr
Ano | Microsoft.ApiManagement/service | Kapacita | Kapacita | Procento | Průměr
Ano | Microsoft.ApiManagement/service | Délka | Celková doba trvání žádostí o bránu | Milisekundy | Průměr
Ano | Microsoft.ApiManagement/service | EventHubDroppedEvents | Vyřazené události EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubRejectedEvents | Odmítnuté události EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Úspěšné události EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubThrottledEvents | Omezené události EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Vypršel časový limit událostí EventHub. | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubTotalBytesSent | Velikost událostí EventHub | B | Celkem
Ano | Microsoft.ApiManagement/service | EventHubTotalEvents | Celkový počet událostí EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Neúspěšné události EventHub | Počet | Celkem
Ano | Microsoft.ApiManagement/service | FailedRequests | Neúspěšné požadavky brány (zastaralé) | Počet | Celkem
Ano | Microsoft.ApiManagement/service | OtherRequests | Jiné požadavky na bránu (zastaralé) | Počet | Celkem
Ano | Microsoft.ApiManagement/service | Požadavky | Požadavky | Počet | Celkem
Ano | Microsoft.ApiManagement/service | SuccessfulRequests | Úspěšné požadavky brány (zastaralé) | Počet | Celkem
Ano | Microsoft.ApiManagement/service | TotalRequests | Požadavky brány celkem (zastaralé) | Počet | Celkem
Ano | Microsoft.ApiManagement/service | UnauthorizedRequests | Neautorizované žádosti o bránu (zastaralé) | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | AppCpuUsagePercentage | Procento využití procesoru aplikací | Procento | Průměr
Ano | Microsoft. AppPlatform/pružina | AppMemoryCommitted | Přiřazená paměť aplikace | B | Průměr
Ano | Microsoft. AppPlatform/pružina | AppMemoryMax | Maximální velikost paměti aplikace | B | Maximum
Ano | Microsoft. AppPlatform/pružina | AppMemoryUsed | Využitá paměť aplikace | B | Průměr
Ano | Microsoft. AppPlatform/pružina | GCPauseTotalCount | Počet pozastavení GC | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | GCPauseTotalTime | Celkový čas pozastavení GC | Milisekundy | Celkem
Ano | Microsoft. AppPlatform/pružina | MaxOldGenMemoryPoolBytes | Maximální velikost dostupných starých dat generace | B | Průměr
Ano | Microsoft. AppPlatform/pružina | OldGenMemoryPoolBytes | Stará velikost dat generace | B | Průměr
Ano | Microsoft. AppPlatform/pružina | OldGenPromotedBytes | Zvýšit úroveň na starou velikost dat generace | B | Maximum
Ano | Microsoft. AppPlatform/pružina | SystemCpuUsagePercentage | Procento využití procesoru v systému | Procento | Průměr
Ano | Microsoft. AppPlatform/pružina | TomcatErrorCount | Globální chyba Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatReceivedBytes | Celkový počet přijatých bajtů Tomcat | B | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatRequestMaxTime | Maximální čas požadavku Tomcat | Milisekundy | Maximum
Ano | Microsoft. AppPlatform/pružina | TomcatRequestTotalCount | Celkový počet žádostí Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatRequestTotalTime | Celkový počet požadavků Tomcat | Milisekundy | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatResponseAvgTime | Průměrná doba požadavku Tomcat | Milisekundy | Průměr
Ano | Microsoft. AppPlatform/pružina | TomcatSentBytes | Celkový počet odeslaných bajtů Tomcat | B | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatSessionActiveCurrentCount | Počet otevřených relací Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatSessionActiveMaxCount | Maximální počet aktivních relací Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatSessionAliveMaxTime | Maximální doba běhu relace Tomcat | Milisekundy | Maximum
Ano | Microsoft. AppPlatform/pružina | TomcatSessionCreatedCount | Počet vytvořených relací Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatSessionExpiredCount | Počet vypršení relace Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | TomcatSessionRejectedCount | Počet odmítnutých relací Tomcat | Počet | Celkem
Ano | Microsoft. AppPlatform/pružina | YoungGenPromotedBytes | Zvýšení úrovně na velikost dat malé generace | B | Maximum
Ano | Microsoft. Automation/automationAccounts | TotalJob | Celkový počet úloh | Počet | Celkem
Ano | Microsoft. Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Celkový počet spuštěných počítačů nasazení aktualizace | Počet | Celkem
Ano | Microsoft. Automation/automationAccounts | TotalUpdateDeploymentRuns | Celkový počet spuštění nasazení aktualizací | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | CoreCount | Vyhrazený počet jader | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | CreatingNodeCount | Vytváření počtu uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | IdleNodeCount | Počet nečinných uzlů | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Události dokončení odstranění úlohy | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Události spuštění odstranění úlohy | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Úloha zakázat kompletní události | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Úloha zakázat počáteční události | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobStartEvent | Události spuštění úlohy | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Ukončit události dokončení úlohy | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Události spuštění ukončení úlohy | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Počet ponechávání uzlů fondu | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | Počet jader LowPriority | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | OfflineNodeCount | Počet uzlů v režimu offline | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | PoolCreateEvent | Vytváření fondů – události | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Události dokončení odstranění fondu | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Události spuštění odstranění fondu | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Události dokončení změny velikosti fondu | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Události spuštění změny velikosti fondu | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Počet zrušených uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | RebootingNodeCount | Restartování počtu uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Počet uzlů obnovování imagí | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | RunningNodeCount | Počet spuštěných uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | StartingNodeCount | Počáteční počet uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Počet neúspěšných spuštění úlohy – počet uzlů | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Události dokončení úlohy | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | TaskFailEvent | Události neúspěšných úloh | Počet | Celkem
Ano | Microsoft.Batch/batchAccounts | TaskStartEvent | Události zahájení úlohy | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Počet uzlů s nízkou prioritou | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | TotalNodeCount | Počet vyhrazených uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | UnusableNodeCount | Počet nepoužitelných uzlů | Počet | Celkem
Ne | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Čekání na počet uzlů spouštěcí úlohy | Počet | Celkem
Ano | Microsoft. BatchAI/pracovní prostory | Aktivní jádra | Aktivní jádra | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Aktivní uzly | Aktivní uzly | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Jádra nečinných | Jádra nečinných | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Nečinné uzly | Nečinné uzly | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Dokončená úloha | Dokončená úloha | Počet | Celkem
Ano | Microsoft. BatchAI/pracovní prostory | Úloha odeslána | Úloha odeslána | Počet | Celkem
Ano | Microsoft. BatchAI/pracovní prostory | Vynechávání jader | Vynechávání jader | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Ukončení uzlů | Ukončení uzlů | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Zrušené jádra | Zrušené jádra | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Přepnuté uzly | Přepnuté uzly | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Procento využití kvóty | Procento využití kvóty | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Celkový počet jader | Celkový počet jader | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Celkem uzlů | Celkem uzlů | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Nepoužitelné jádra | Nepoužitelné jádra | Počet | Průměr
Ano | Microsoft. BatchAI/pracovní prostory | Nepoužité uzly | Nepoužité uzly | Počet | Průměr
Ano | Microsoft. blockchain/blockchainMembers | ConnectionAccepted | Přijatá připojení | Počet | Celkem
Ano | Microsoft. blockchain/blockchainMembers | ConnectionActive | Aktivní připojení | Počet | Průměr
Ano | Microsoft. blockchain/blockchainMembers | ConnectionHandled | Zpracovaná připojení | Počet | Celkem
Ano | Microsoft. blockchain/blockchainMembers | CpuUsagePercentageInDouble | Procento využití procesoru | Procento | Maximum
Ano | Microsoft. blockchain/blockchainMembers | IOReadBytes | Bajty čtení v/v | B | Celkem
Ano | Microsoft. blockchain/blockchainMembers | IOWriteBytes | Bajty zápisu v/v | B | Celkem
Ano | Microsoft. blockchain/blockchainMembers | Limitu | Omezení paměti | B | Průměr
Ano | Microsoft. blockchain/blockchainMembers | MemoryUsage | Využití paměti | B | Průměr
Ano | Microsoft. blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Procento využití paměti | Procento | Průměr
Ano | Microsoft. blockchain/blockchainMembers | PendingTransactions | Nedokončené transakce | Počet | Průměr
Ano | Microsoft. blockchain/blockchainMembers | ProcessedBlocks | Zpracované bloky | Počet | Celkem
Ano | Microsoft. blockchain/blockchainMembers | ProcessedTransactions | Zpracované transakce | Počet | Celkem
Ano | Microsoft. blockchain/blockchainMembers | QueuedTransactions | Transakce ve frontě | Počet | Průměr
Ano | Microsoft. blockchain/blockchainMembers | RequestHandled | Zpracované žádosti | Počet | Celkem
Ano | Microsoft. blockchain/blockchainMembers | StorageUsage | Využití úložiště | B | Průměr
Ano | Microsoft.Cache/redis | cachehits | Přístupy do mezipaměti | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits0 | Přístupy do mezipaměti (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits1 | Přístupy do mezipaměti (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits2 | Přístupy do mezipaměti (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits3 | Přístupy do mezipaměti (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits4 | Přístupy do mezipaměti (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits5 | Přístupy do mezipaměti (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits6 | Přístupy do mezipaměti (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits7 | Přístupy do mezipaměti (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits8 | Přístupy do mezipaměti (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachehits9 | Přístupy do mezipaměti (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | cacheLatency | Mikrosekundy latence mezipaměti (Preview) | Počet | Průměr
Ano | Microsoft.Cache/redis | cachemisses | Neúspěšné přístupy do mezipaměti | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses0 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses1 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses2 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses3 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses4 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses5 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses6 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses7 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses8 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | cachemisses9 | Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | cacheRead | Čtení z mezipaměti | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead0 | Čtení z mezipaměti (horizontálních oddílů 0) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead1 | Čtení z mezipaměti (horizontálních oddílů 1) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead2 | Čtení z mezipaměti (horizontálních oddílů 2) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead3 | Čtení z mezipaměti (horizontálních oddílů 3) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead4 | Čtení z mezipaměti (horizontálních oddílů 4) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead5 | Čtení z mezipaměti (horizontálních oddílů 5) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead6 | Čtení z mezipaměti (horizontálních oddílů 6) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead7 | Čtení z mezipaměti (horizontálních oddílů 7) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead8 | Čtení z mezipaměti (horizontálních oddílů 8) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheRead9 | Čtení z mezipaměti (horizontálních oddílů 9) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite | Zápis do mezipaměti | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite0 | Zápis do mezipaměti (horizontálních oddílů 0) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite1 | Zápis do mezipaměti (horizontálních oddílů 1) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite2 | Zápis do mezipaměti (horizontálních oddílů 2) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite3 | Zápis do mezipaměti (horizontálních oddílů 3) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite4 | Zápis do mezipaměti (horizontálních oddílů 4) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite5 | Zápis do mezipaměti (horizontálních oddílů 5) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite6 | Zápis do mezipaměti (horizontálních oddílů 6) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite7 | Zápis do mezipaměti (horizontálních oddílů 7) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite8 | Zápis do mezipaměti (horizontálních oddílů 8) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | cacheWrite9 | Zápis do mezipaměti (horizontálních oddílů 9) | BytesPerSecond | Maximum
Ano | Microsoft.Cache/redis | connectedclients | Připojení klienti | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients0 | Připojení klienti (horizontálních oddílů 0) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients1 | Připojení klienti (horizontálních oddílů 1) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients2 | Připojení klienti (horizontálních oddílů 2) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients3 | Připojení klienti (horizontálních oddílů 3) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients4 | Připojení klienti (horizontálních oddílů 4) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients5 | Připojení klienti (horizontálních oddílů 5) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients6 | Připojení klienti (horizontálních oddílů 6) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients7 | Připojení klienti (horizontálních oddílů 7) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients8 | Připojení klienti (horizontálních oddílů 8) | Počet | Maximum
Ano | Microsoft.Cache/redis | connectedclients9 | Připojení klienti (horizontálních oddílů 9) | Počet | Maximum
Ano | Microsoft.Cache/redis | chyby | chyby | Počet | Maximum
Ano | Microsoft.Cache/redis | evictedkeys | Vyřazení klíčů | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys0 | Vyřazené klíče (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys1 | Vyřazené klíče (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys2 | Vyřazené klíče (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys3 | Vyřazené klíče (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys4 | Vyřazené klíče (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys5 | Vyřazení klíčů (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys6 | Vyřazené klíče (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys7 | Vyřazení klíčů (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys8 | Vyřazené klíče (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | evictedkeys9 | Vyřazené klíče (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys | Prošlé klíče | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys0 | Klíče vypršení platnosti (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys1 | Klíče vypršení platnosti (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys2 | Klíče vypršení platnosti (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys3 | Klíče vypršení platnosti (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys4 | Klíče vypršení platnosti (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys5 | Klíče vypršení platnosti (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys6 | Klíče vypršení platnosti (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys7 | Klíče vypršení platnosti (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys8 | Klíče vypršení platnosti (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | expiredkeys9 | Klíče vypršení platnosti (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | GetCommands | Získá | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands0 | Načtení (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands1 | Načtení (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands2 | Get (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands3 | Get (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands4 | Get (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands5 | Získá (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands6 | Načtení (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands7 | Načtení (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands8 | Získá (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | getcommands9 | Načtení (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | operationsPerSecond | Operace za sekundu | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond0 | Operací za sekundu (horizontálních oddílů 0) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond1 | Operací za sekundu (horizontálních oddílů 1) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond2 | Operací za sekundu (horizontálních oddílů 2) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond3 | Operací za sekundu (horizontálních oddílů 3) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond4 | Operací za sekundu (horizontálních oddílů 4) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond5 | Operací za sekundu (horizontálních oddílů 5) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond6 | Operací za sekundu (horizontálních oddílů 6) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond7 | Operací za sekundu (horizontálních oddílů 7) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond8 | Operací za sekundu (horizontálních oddílů 8) | Počet | Maximum
Ano | Microsoft.Cache/redis | operationsPerSecond9 | Operací za sekundu (horizontálních oddílů 9) | Počet | Maximum
Ano | Microsoft.Cache/redis | PercentProcessorTime | Procesor | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime0 | PROCESOR (horizontálních oddílů 0) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime1 | PROCESOR (horizontálních oddílů 1) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime2 | PROCESOR (horizontálních oddílů 2) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime3 | PROCESOR (horizontálních oddílů 3) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime4 | PROCESOR (horizontálních oddílů 4) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime5 | PROCESOR (horizontálních oddílů 5) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime6 | PROCESOR (horizontálních oddílů 6) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime7 | PROCESOR (horizontálních oddílů 7) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime8 | PROCESOR (horizontálních oddílů 8) | Procento | Maximum
Ano | Microsoft.Cache/redis | percentProcessorTime9 | PROCESOR (horizontálních oddílů 9) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad | Zatížení serveru | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad0 | Zatížení serveru (horizontálních oddílů 0) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad1 | Zatížení serveru (horizontálních oddílů 1) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad2 | Zatížení serveru (horizontálních oddílů 2) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad3 | Zatížení serveru (horizontálních oddílů 3) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad4 | Zatížení serveru (horizontálních oddílů 4) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad5 | Zatížení serveru (horizontálních oddílů 5) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad6 | Zatížení serveru (horizontálních oddílů 6) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad7 | Zatížení serveru (horizontálních oddílů 7) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad8 | Zatížení serveru (horizontálních oddílů 8) | Procento | Maximum
Ano | Microsoft.Cache/redis | serverLoad9 | Zatížení serveru (horizontálních oddílů 9) | Procento | Maximum
Ano | Microsoft.Cache/redis | setcommands | Obnovení | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands0 | Sady (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands1 | Sady (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands2 | Sady (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands3 | Sady (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands4 | Sady (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands5 | Sady (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands6 | Sady (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands7 | Sady (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands8 | Sady (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | setcommands9 | Sady (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed | Celkem operací | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed0 | Celkem operací (horizontálních oddílů 0) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed1 | Celkem operací (horizontálních oddílů 1) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed2 | Celkem operací (horizontálních oddílů 2) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed3 | Celkem operací (horizontálních oddílů 3) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed4 | Celkem operací (horizontálních oddílů 4) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed5 | Celkem operací (horizontálních oddílů 5) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed6 | Celkem operací (horizontálních oddílů 6) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed7 | Celkem operací (horizontálních oddílů 7) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed8 | Celkem operací (horizontálních oddílů 8) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalcommandsprocessed9 | Celkem operací (horizontálních oddílů 9) | Počet | Celkem
Ano | Microsoft.Cache/redis | totalkeys | Celkem klíčů | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys0 | Celkem klíčů (horizontálních oddílů 0) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys1 | Celkem klíčů (horizontálních oddílů 1) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys2 | Celkem klíčů (horizontálních oddílů 2) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys3 | Celkem klíčů (horizontálních oddílů 3) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys4 | Celkem klíčů (horizontálních oddílů 4) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys5 | Celkem klíčů (horizontálních oddílů 5) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys6 | Celkem klíčů (horizontálních oddílů 6) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys7 | Celkem klíčů (horizontálních oddílů 7) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys8 | Celkem klíčů (horizontálních oddílů 8) | Počet | Maximum
Ano | Microsoft.Cache/redis | totalkeys9 | Celkem klíčů (horizontálních oddílů 9) | Počet | Maximum
Ano | Microsoft.Cache/redis | usedmemory | Využitá paměť | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory0 | Využitá paměť (horizontálních oddílů 0) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory1 | Využitá paměť (horizontálních oddílů 1) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory2 | Využitá paměť (horizontálních oddílů 2) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory3 | Využitá paměť (horizontálních oddílů 3) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory4 | Využitá paměť (horizontálních oddílů 4) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory5 | Využitá paměť (horizontálních oddílů 5) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory6 | Využitá paměť (horizontálních oddílů 6) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory7 | Využitá paměť (horizontálních oddílů 7) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory8 | Využitá paměť (horizontálních oddílů 8) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemory9 | Využitá paměť (horizontálních oddílů 9) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemorypercentage | Procento využité paměti | Procento | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss | RSS využité paměti | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss0 | RSS využité paměti (horizontálních oddílů 0) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss1 | RSS využité paměti (horizontálních oddílů 1) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss2 | RSS využité paměti (horizontálních oddílů 2) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss3 | RSS využité paměti (horizontálních oddílů 3) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss4 | RSS využité paměti (horizontálních oddílů 4) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss5 | RSS využité paměti (horizontálních oddílů 5) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss6 | RSS využité paměti (horizontálních oddílů 6) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss7 | RSS využité paměti (horizontálních oddílů 7) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss8 | RSS využité paměti (horizontálních oddílů 8) | B | Maximum
Ano | Microsoft.Cache/redis | usedmemoryRss9 | RSS využité paměti (horizontálních oddílů 9) | B | Maximum
Ne | Microsoft.ClassicCompute/domainNames/slots/roles | Bajty čtení z disku/s | Čtení z disku | BytesPerSecond | Průměr
Ano | Microsoft.ClassicCompute/domainNames/slots/roles | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ne | Microsoft.ClassicCompute/domainNames/slots/roles | Bajty zápisu na disk/s | Zápis na disk | BytesPerSecond | Průměr
Ano | Microsoft.ClassicCompute/domainNames/slots/roles | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft.ClassicCompute/domainNames/slots/roles | Síťové vstupy | Síťové vstupy | B | Celkem
Ano | Microsoft.ClassicCompute/domainNames/slots/roles | Síťové výstupy | Síťové výstupy | B | Celkem
Ano | Microsoft.ClassicCompute/domainNames/slots/roles | Procento CPU | Procento CPU | Procento | Průměr
Ne | Microsoft. ClassicCompute/virtualMachines | Bajty čtení z disku/s | Čtení z disku | BytesPerSecond | Průměr
Ano | Microsoft. ClassicCompute/virtualMachines | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ne | Microsoft. ClassicCompute/virtualMachines | Bajty zápisu na disk/s | Zápis na disk | BytesPerSecond | Průměr
Ano | Microsoft. ClassicCompute/virtualMachines | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft. ClassicCompute/virtualMachines | Síťové vstupy | Síťové vstupy | B | Celkem
Ano | Microsoft. ClassicCompute/virtualMachines | Síťové výstupy | Síťové výstupy | B | Celkem
Ano | Microsoft. ClassicCompute/virtualMachines | Procento CPU | Procento CPU | Procento | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts | Transakce | Transakce | Počet | Celkem
Ne | Microsoft. ClassicStorage/storageAccounts | UsedCapacity | Použitá kapacita | B | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | Dostupnost | Dostupnost | Procento | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCapacity | Kapacita služby Blob | B | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/blobServices | BlobCount | Počet objektů blob | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | ContainerCount | Počet kontejnerů služby Blob | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ne | Microsoft. ClassicStorage/storageAccounts/blobServices | IndexCapacity | Kapacita indexu | B | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/blobServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/služby | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/služby | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ne | Microsoft. ClassicStorage/storageAccounts/služby | Kapacita zařízení | Kapacita souboru | B | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/služby | FileCount | Počet souborů | Počet | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/služby | FileShareCount | Počet sdílených souborů | Počet | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/služby | FileShareQuota | Velikost kvóty sdílení souborů | B | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/služby | FileShareSnapshotCount | Počet snímků sdílené složky | Počet | Průměr
Ne | Microsoft. ClassicStorage/storageAccounts/služby | FileShareSnapshotSize | Velikost snímku sdílené složky | B | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/služby | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/služby | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/služby | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/služby | Transakce | Transakce | Počet | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCapacity | Kapacita služby Queue | B | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueCount | Počet front | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | QueueMessageCount | Počet zpráv fronty | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/queueServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCapacity | Kapacita služby Table | B | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | TableCount | Počet tabulek | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | TableEntityCount | Počet entit tabulek | Počet | Průměr
Ano | Microsoft. ClassicStorage/storageAccounts/tableServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | BlockedCalls | Blokovaná volání | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | CharactersTrained | Vyškolené znaky | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | CharactersTranslated | Přeložené znaky | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | ClientErrors | Chyby klienta | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | DataIn | Data v | B | Celkem
Ano | Microsoft.CognitiveServices/accounts | Data | Výstupní data | B | Celkem
Ano | Microsoft.CognitiveServices/accounts | Latence | Latence | Milisekund | Průměr
Ano | Microsoft.CognitiveServices/accounts | ServerErrors | Chyby serveru | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Doba trvání relace řeči | Sekund | Celkem
Ano | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Úspěšná volání | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | TotalCalls | Celkový počet volání | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | TotalErrors | Celkový počet chyb | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Celkový počet volání tokenu | Počet | Celkem
Ano | Microsoft.CognitiveServices/accounts | TotalTransactions | Celkový počet transakcí | Počet | Celkem
Ano | Microsoft.Compute/virtualMachines | Spotřebované kredity procesoru | Spotřebované kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Zbývající kredity procesoru | Zbývající kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Hloubka fronty datových disků | Hloubka fronty datových disků (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty přečtené z datového disku za sekundu | Bajty přečtené z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace čtení z datového disku za sekundu | Operace čtení z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty zapsané na datový disk/s | Bajty zapsané na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace zápisu na datový disk/s | Operace zápisu na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty čtení z disku | Bajty čtení z disku | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty zápisu na disk | Bajty zápisu na disk | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Příchozí toky | Příchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Maximální rychlost vytváření příchozích toků | Míra vytváření maximálního počtu příchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Síťové vstupy | Síť v fakturovatelný (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Celková síť | Celková síť | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Síťové výstupy | Fakturovatelná odchozí síť (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Celkový počet síťových výstupů | Celkový počet síťových výstupů | B | Celkem
Ano | Microsoft.Compute/virtualMachines | Hloubka fronty disku s operačním systémem | Hloubka fronty disku operačního systému (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty přečtené z disku s operačním systémem/s | Počet přečtených bajtů disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace čtení z disku s operačním systémem za sekundu | Operace čtení z disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty zápisu na disk s operačním systémem/s | Bajty zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace zápisu na disk s operačním systémem za sekundu | Operace zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operační systém na disk hloubka fronty | Disk s operačním systémem hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Přečtené bajty v operačním systému na disk/s | Bajty přečtené z disku s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace čtení z operačního systému na disk/s | Operace čtení z disku s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty zapsané v operačním systému na disk/s | Bajty zápisu na disk s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace zápisu v operačním systému na disk/s | Operace zápisu na disk s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Odchozí toky | Odchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Maximální rychlost vytváření odchozích toků | Frekvence maximálního vytváření odchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Hloubka fronty na disk | Datový disk hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachines | Přečtené bajty podle disku/s | Bajty přečtené z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace čtení na disk/s | Operace čtení z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Bajty zápisu na disk/s | Bajty zapsané na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Operace zápisu na disk/s | Operace zápisu na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachines | Procento CPU | Procento CPU | Procento | Průměr
Ano | Microsoft.Compute/virtualMachines | Počet přístupů do mezipaměti pro datový disk úrovně Premium | Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachines | Neúspěšné čtení mezipaměti datových disků Premium | Neúspěšné čtení mezipaměti datových disků Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachines | Počet přístupů do mezipaměti disku s operačním systémem Premium | Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachines | Neúspěšné čtení mezipaměti disku s operačním systémem Premium | Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Spotřebované kredity procesoru | Spotřebované kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Zbývající kredity procesoru | Zbývající kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Hloubka fronty datových disků | Hloubka fronty datových disků (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty přečtené z datového disku za sekundu | Bajty přečtené z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace čtení z datového disku za sekundu | Operace čtení z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty zapsané na datový disk/s | Bajty zapsané na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace zápisu na datový disk/s | Operace zápisu na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty čtení z disku | Bajty čtení z disku | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty zápisu na disk | Bajty zápisu na disk | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Příchozí toky | Příchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Maximální rychlost vytváření příchozích toků | Míra vytváření maximálního počtu příchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Síťové vstupy | Síť v fakturovatelný (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Celková síť | Celková síť | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Síťové výstupy | Fakturovatelná odchozí síť (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Celkový počet síťových výstupů | Celkový počet síťových výstupů | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets | Hloubka fronty disku s operačním systémem | Hloubka fronty disku operačního systému (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty přečtené z disku s operačním systémem/s | Počet přečtených bajtů disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace čtení z disku s operačním systémem za sekundu | Operace čtení z disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty zápisu na disk s operačním systémem/s | Bajty zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace zápisu na disk s operačním systémem za sekundu | Operace zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operační systém na disk hloubka fronty | Disk s operačním systémem hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Přečtené bajty v operačním systému na disk/s | Bajty přečtené z disku s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace čtení z operačního systému na disk/s | Operace čtení z disku s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty zapsané v operačním systému na disk/s | Bajty zápisu na disk s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace zápisu v operačním systému na disk/s | Operace zápisu na disk s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Odchozí toky | Odchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Maximální rychlost vytváření odchozích toků | Frekvence maximálního vytváření odchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Hloubka fronty na disk | Datový disk hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Přečtené bajty podle disku/s | Bajty přečtené z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace čtení na disk/s | Operace čtení z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Bajty zápisu na disk/s | Bajty zapsané na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Operace zápisu na disk/s | Operace zápisu na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Procento CPU | Procento CPU | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Počet přístupů do mezipaměti pro datový disk úrovně Premium | Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Neúspěšné čtení mezipaměti datových disků Premium | Neúspěšné čtení mezipaměti datových disků Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Počet přístupů do mezipaměti disku s operačním systémem Premium | Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets | Neúspěšné čtení mezipaměti disku s operačním systémem Premium | Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Spotřebované kredity procesoru | Spotřebované kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Zbývající kredity procesoru | Zbývající kredity procesoru | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Hloubka fronty datových disků | Hloubka fronty datových disků (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty přečtené z datového disku za sekundu | Bajty přečtené z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace čtení z datového disku za sekundu | Operace čtení z datového disku za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapsané na datový disk/s | Bajty zapsané na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace zápisu na datový disk/s | Operace zápisu na datový disk za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty čtení z disku | Bajty čtení z disku | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zápisu na disk | Bajty zápisu na disk | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Příchozí toky | Příchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maximální rychlost vytváření příchozích toků | Míra vytváření maximálního počtu příchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Síťové vstupy | Síť v fakturovatelný (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Celková síť | Celková síť | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Síťové výstupy | Fakturovatelná odchozí síť (zastaralé) | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Celkový počet síťových výstupů | Celkový počet síťových výstupů | B | Celkem
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Hloubka fronty disku s operačním systémem | Hloubka fronty disku operačního systému (Preview) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty přečtené z disku s operačním systémem/s | Počet přečtených bajtů disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace čtení z disku s operačním systémem za sekundu | Operace čtení z disku s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zápisu na disk s operačním systémem/s | Bajty zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace zápisu na disk s operačním systémem za sekundu | Operace zápisu na disk s operačním systémem za sekundu (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operační systém na disk hloubka fronty | Disk s operačním systémem hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Přečtené bajty v operačním systému na disk/s | Bajty přečtené z disku s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace čtení z operačního systému na disk/s | Operace čtení z disku s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zapsané v operačním systému na disk/s | Bajty zápisu na disk s operačním systémem/s (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace zápisu v operačním systému na disk/s | Operace zápisu na disk s operačním systémem za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Odchozí toky | Odchozí toky | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maximální rychlost vytváření odchozích toků | Frekvence maximálního vytváření odchozích toků (Preview) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Hloubka fronty na disk | Datový disk hloubka fronty (zastaralé) | Počet | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Přečtené bajty podle disku/s | Bajty přečtené z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace čtení na disk/s | Operace čtení z datového disku za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bajty zápisu na disk/s | Bajty zapsané na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operace zápisu na disk/s | Operace zápisu na datový disk za sekundu (zastaralé) | CountPerSecond | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Procento CPU | Procento CPU | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Počet přístupů do mezipaměti pro datový disk úrovně Premium | Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Neúspěšné čtení mezipaměti datových disků Premium | Neúspěšné čtení mezipaměti datových disků Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Počet přístupů do mezipaměti disku s operačním systémem Premium | Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Neúspěšné čtení mezipaměti disku s operačním systémem Premium | Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview) | Procento | Průměr
Ano | Microsoft.ContainerInstance/containerGroups | CpuUsage | Využití CPU | Počet | Průměr
Ano | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Využití paměti | B | Průměr
Ano | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Počet přijatých bajtů sítě za sekundu | B | Průměr
Ano | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Bajty přenášené přes síť za sekundu | B | Průměr
Ano | Microsoft.ContainerRegistry/registries | RunDuration | Doba trvání běhu | Milisekundy | Celkem
Ano | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | Počet úspěšných vyžádané replikace | Počet | Průměr
Ano | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | Počet úspěšných vložení | Počet | Průměr
Ano | Microsoft.ContainerRegistry/registries | TotalPullCount | Celkový počet vyžádané replikace | Počet | Průměr
Ano | Microsoft.ContainerRegistry/registries | TotalPushCount | Celkový počet nabízených oznámení | Počet | Průměr
Ne | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Celkový počet dostupných jader procesoru ve spravovaném clusteru | Počet | Průměr
Ne | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Celková velikost dostupné paměti ve spravovaném clusteru | B | Průměr
Ne | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Stavy pro různé podmínky uzlu | Počet | Průměr
Ne | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Počet lusků podle fáze | Počet | Průměr
Ne | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Počet lusků ve stavu připraveno | Počet | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | Availablecapacity;) | Dostupná kapacita | B | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Odeslané bajty v cloudu (zařízení) | B | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Odeslané bajty v cloudu (sdílená složka) | B | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Propustnost stahování v cloudu | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Propustnost stahování do cloudu (sdílení) | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Propustnost nahrávání do cloudu | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Propustnost nahrávání do cloudu (sdílení) | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Výpočet využití paměti na hraničních zařízeních | Procento | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Výpočetní prostředí Edge – procento využití procesoru | Procento | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Propustnost čtení (síť) | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Propustnost zápisu (síť) | BytesPerSecond | Průměr
Ano | Microsoft.DataBoxEdge/dataBoxEdgeDevices | Úložiště | Celková kapacita | B | Průměr
Ano | Microsoft. DataFactory/DataFactory | FailedRuns | Neúspěšná spuštění | Počet | Celkem
Ano | Microsoft. DataFactory/DataFactory | SuccessfulRuns | Úspěšná spuštění | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | ActivityCancelledRuns | Zrušené metriky spuštění aktivit | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | ActivityFailedRuns | Neúspěšná aktivita spustí metriky | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | ActivitySucceededRuns | Úspěšná aktivita spustí metriky | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | FactorySizeInGbUnits | Celková velikost továrny (jednotka GB) | Počet | Maximum
Ano | Microsoft. DataFactory/továrny | IntegrationRuntimeAvailableMemory | Dostupná paměť modulu runtime integrace | B | Průměr
Ano | Microsoft. DataFactory/továrny | IntegrationRuntimeAverageTaskPickupDelay | Doba trvání fronty prostředí Integration runtime | Sekund | Průměr
Ano | Microsoft. DataFactory/továrny | IntegrationRuntimeCpuPercentage | Využití procesoru prostředí Integration runtime | Procento | Průměr
Ano | Microsoft. DataFactory/továrny | IntegrationRuntimeQueueLength | Délka fronty prostředí Integration runtime | Počet | Průměr
Ano | Microsoft. DataFactory/továrny | MaxAllowedFactorySizeInGbUnits | Maximální povolená velikost továrny (jednotka GB) | Počet | Maximum
Ano | Microsoft. DataFactory/továrny | MaxAllowedResourceCount | Maximální počet povolených entit | Počet | Maximum
Ano | Microsoft. DataFactory/továrny | PipelineCancelledRuns | Zrušené metriky spuštění kanálu | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | PipelineFailedRuns | Neúspěšná metrika spuštění kanálu | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | PipelineSucceededRuns | Úspěšné metriky spuštění kanálu | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | ResourceCount | Celkový počet entit | Počet | Maximum
Ano | Microsoft. DataFactory/továrny | TriggerCancelledRuns | Zrušené aktivační události spustí metriky | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | TriggerFailedRuns | Neúspěšná aktivační událost spustí metriky | Počet | Celkem
Ano | Microsoft. DataFactory/továrny | TriggerSucceededRuns | Úspěšná aktivační událost spustí metriky | Počet | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Čas AU se zrušil. | Sekund | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Neúspěšná doba aktualizace AU | Sekund | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Úspěšná doba AU | Sekund | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Zrušené úlohy | Počet | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Neúspěšné úlohy | Počet | Celkem
Ano | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Úspěšné úlohy | Počet | Celkem
Ano | Microsoft.DataLakeStore/accounts | DataRead | Přečtená data | B | Celkem
Ano | Microsoft.DataLakeStore/accounts | Napsáno | Zapsaná data | B | Celkem
Ano | Microsoft.DataLakeStore/accounts | ReadRequests | Žádosti o čtení | Počet | Celkem
Ano | Microsoft.DataLakeStore/accounts | TotalStorage | Úložiště celkem | B | Maximum
Ano | Microsoft.DataLakeStore/accounts | WriteRequests | Požadavky na zápis | Počet | Celkem
Ano | Microsoft.DBforMariaDB/servers | active_connections | Aktivní připojení | Počet | Průměr
Ano | Microsoft.DBforMariaDB/servers | backup_storage_used | Využité úložiště záloh | B | Průměr
Ano | Microsoft.DBforMariaDB/servers | connections_failed | Neúspěšná připojení | Počet | Celkem
Ano | Microsoft.DBforMariaDB/servers | cpu_percent | Procento využití procesoru | Procento | Průměr
Ano | Microsoft.DBforMariaDB/servers | io_consumption_percent | V/v procenta | Procento | Průměr
Ano | Microsoft.DBforMariaDB/servers | memory_percent | Procentuální hodnota paměti | Procento | Průměr
Ano | Microsoft.DBforMariaDB/servers | network_bytes_egress | Síťové výstupy | B | Celkem
Ano | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Síťové vstupy | B | Celkem
Ano | Microsoft.DBforMariaDB/servers | seconds_behind_master | Prodleva replikace v sekundách | Počet | Maximum
Ano | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Limit úložiště protokolu serveru | B | Průměr
Ano | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Procentuální hodnota úložiště protokolu serveru | Procento | Průměr
Ano | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Využité úložiště protokolu serveru | B | Průměr
Ano | Microsoft.DBforMariaDB/servers | storage_limit | Omezení úložiště | B | Maximum
Ano | Microsoft.DBforMariaDB/servers | storage_percent | Procento úložiště | Procento | Průměr
Ano | Microsoft.DBforMariaDB/servers | storage_used | Využité úložiště | B | Průměr
Ano | Microsoft.DBforMySQL/servers | active_connections | Aktivní připojení | Počet | Průměr
Ano | Microsoft.DBforMySQL/servers | backup_storage_used | Využité úložiště záloh | B | Průměr
Ano | Microsoft.DBforMySQL/servers | connections_failed | Neúspěšná připojení | Počet | Celkem
Ano | Microsoft.DBforMySQL/servers | cpu_percent | Procento využití procesoru | Procento | Průměr
Ano | Microsoft.DBforMySQL/servers | io_consumption_percent | V/v procenta | Procento | Průměr
Ano | Microsoft.DBforMySQL/servers | memory_percent | Procentuální hodnota paměti | Procento | Průměr
Ano | Microsoft.DBforMySQL/servers | network_bytes_egress | Síťové výstupy | B | Celkem
Ano | Microsoft.DBforMySQL/servers | network_bytes_ingress | Síťové vstupy | B | Celkem
Ano | Microsoft.DBforMySQL/servers | seconds_behind_master | Prodleva replikace v sekundách | Počet | Maximum
Ano | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Limit úložiště protokolu serveru | B | Maximum
Ano | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Procentuální hodnota úložiště protokolu serveru | Procento | Průměr
Ano | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Využité úložiště protokolu serveru | B | Průměr
Ano | Microsoft.DBforMySQL/servers | storage_limit | Omezení úložiště | B | Maximum
Ano | Microsoft.DBforMySQL/servers | storage_percent | Procento úložiště | Procento | Průměr
Ano | Microsoft.DBforMySQL/servers | storage_used | Využité úložiště | B | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | active_connections | Aktivní připojení | Počet | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Využité úložiště záloh | B | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | connections_failed | Neúspěšná připojení | Počet | Celkem
Ano | Microsoft.DBforPostgreSQL/servers | cpu_percent | Procento využití procesoru | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | V/v procenta | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | memory_percent | Procentuální hodnota paměti | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Síťové výstupy | B | Celkem
Ano | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Síťové vstupy | B | Celkem
Ano | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Maximální prodleva napříč replikami | B | Maximum
Ano | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Prodleva repliky | Sekund | Maximum
Ano | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Limit úložiště protokolu serveru | B | Maximum
Ano | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Procentuální hodnota úložiště protokolu serveru | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Využité úložiště protokolu serveru | B | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | storage_limit | Omezení úložiště | B | Maximum
Ano | Microsoft.DBforPostgreSQL/servers | storage_percent | Procento úložiště | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/servers | storage_used | Využité úložiště | B | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Aktivní připojení | Počet | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | Procento využití procesoru | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | IOPS | IOPS | Počet | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Procentuální hodnota paměti | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Síťové výstupy | B | Celkem
Ano | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Síťové vstupy | B | Celkem
Ano | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Procento úložiště | Procento | Průměr
Ano | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Využité úložiště | B | Průměr
Ano | Microsoft. zařízení/účet | digitaltwins. telemetrie. Nodes | Zástupný text telemetrie uzlu digitální vlákna | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | Zrušené zprávy C2D | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | Doručení zpráv C2D bylo dokončeno. | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Odmítnuté zprávy C2D | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | C2D. Methods. Failure | Neúspěšná volání přímé metody | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.methods.requestSize | Velikost žádosti o vyvolání přímé metody | B | Průměr
Ano | Microsoft.Devices/IotHubs | c2d.methods.responseSize | Velikost odezvy volání přímých metod | B | Průměr
Ano | Microsoft.Devices/IotHubs | c2d.methods.success | Úspěšná volání přímé metody | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Neúspěšné čtení z back-endu ze zadních vláken | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.twin.read.size | Velikost odpovědi zdvojeného čtení z back-endu | B | Průměr
Ano | Microsoft.Devices/IotHubs | c2d.twin.read.success | Úspěšné zdvojené čtení z back-endu | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Neúspěšné zdvojené aktualizace z back-endu | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | c2d.twin.update.size | Velikost dvojitě aktualizovaných aktualizací z back-endu | B | Průměr
Ano | Microsoft.Devices/IotHubs | c2d.twin.update.success | Úspěšné zdvojené aktualizace z back-endu | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D zprávy prošly (Preview) | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | konfiguračních | Metriky konfigurace | Počet | Celkem
Ne | Microsoft.Devices/IotHubs | connectedDeviceCount | Připojená zařízení (Preview) | Počet | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Směrování: zprávy doručené zprávám/událostem | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Směrování: zprávy doručené do centra událostí | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Směrování: zprávy doručené do fronty Service Bus | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Směrování: zprávy doručené do Service Bus tématu | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Směrování: zprávy doručené do úložiště | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Směrování: objekty blob doručené do úložiště | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Směrování: data Doručená do úložiště | B | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Směrování: latence zpráv pro zprávy/události | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Směrování: latence zprávy pro centrum událostí | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Směrování: latence zprávy pro Service Bus frontu | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Směrování: latence zprávy pro Service Bus téma | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | D2C. Endpoints. latence. Storage | Směrování: latence zpráv pro úložiště | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Směrování: vyřazené zprávy telemetrie  | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Směrování: zprávy doručené do záložního režimu | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Směrování: nekompatibilní zprávy telemetrie | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Směrování: osamocené zprávy telemetrie  | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Směrování: doručené zprávy telemetrie | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Počet pokusů o odeslání zprávy telemetrie | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Počet chyb omezování | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Odeslané zprávy telemetrie | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Neúspěšná čtení ze zařízení | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.twin.read.size | Velikost odpovědi u dvojitých čtení ze zařízení | B | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.twin.read.success | Úspěšné čtení ze zařízení | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Neúspěšné aktualizace ze zařízení se zdvojenými chybami | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | d2c.twin.update.size | Velikost dvojitě aktualizovaných aktualizací ze zařízení | B | Průměr
Ano | Microsoft.Devices/IotHubs | d2c.twin.update.success | Úspěšné nedokončené změny ze zařízení | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Celkový počet použitých zpráv | Počet | Průměr
Ano | Microsoft.Devices/IotHubs | deviceDataUsage | Celkové využití dat zařízení | B | Celkem
Ano | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Celkové využití dat zařízení (Preview) | B | Celkem
Ano | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Připojená zařízení (zastaralé)  | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | devices.totalDevices | Celkem zařízení (zastaralé) | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | EventGridDeliveries | Event Grid doručení (Preview) | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | EventGridLatency | Latence Event Grid (Preview) | Milisekundy | Průměr
Ano | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Neúspěšná zrušení úloh | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Úspěšná zrušení úlohy | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | dokončené úlohy | Dokončené úlohy | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Nepovedlo se vytvořit úlohy vyvolání metody | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Úspěšné vytváření úloh vyvolání metod | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Nepovedlo se vytvořit úlohy s dvojitou aktualizací | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Úspěšné vytváření zdvojených úloh aktualizace | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | úlohy. nezdařilo se | Neúspěšné úlohy | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Neúspěšná volání pro výpis úloh | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.listJobs.success | Úspěšná volání na seznam úloh | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Neúspěšné dotazy na úlohy | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Úspěšné dotazy na úlohy | Počet | Celkem
Ne | Microsoft.Devices/IotHubs | totalDeviceCount | Celkem zařízení (Preview) | Počet | Průměr
Ano | Microsoft.Devices/IotHubs | twinQueries.failure | Neúspěšné zdvojené dotazy | Počet | Celkem
Ano | Microsoft.Devices/IotHubs | twinQueries.resultSize | Velikost výsledku nevlákenných dotazů | B | Průměr
Ano | Microsoft.Devices/IotHubs | twinQueries.success | Úspěšné zdvojené dotazy | Počet | Celkem
Ano | Microsoft.Devices/provisioningServices | AttestationAttempts | Pokusy o ověření identity | Počet | Celkem
Ano | Microsoft.Devices/provisioningServices | DeviceAssignments | Přiřazená zařízení | Počet | Celkem
Ano | Microsoft.Devices/provisioningServices | RegistrationAttempts | Pokusy o registraci | Počet | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | AvailableStorage | Dostupné úložiště | B | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | CassandraConnectionClosures | Ukončení připojení Cassandra | Počet | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | CassandraRequestCharges | Poplatky za žádosti Cassandra | Počet | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | CassandraRequests | Žádosti Cassandra | Počet | Počet
Ne | Microsoft. DocumentDB/databaseAccounts | Využití datausage | Využití dat | B | Celkem
Ano | Microsoft. DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Počet | Počet
Ne | Microsoft. DocumentDB/databaseAccounts | DocumentCount | Počet dokumentů | Počet | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | DocumentQuota | Kvóta dokumentu | B | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | IndexUsage | Využití indexu | B | Celkem
Ne | Microsoft. DocumentDB/databaseAccounts | MetadataRequests | Žádosti o metadata | Počet | Počet
Ano | Microsoft. DocumentDB/databaseAccounts | MongoRequestCharge | Poplatek za požadavek Mongo | Počet | Celkem
Ano | Microsoft. DocumentDB/databaseAccounts | MongoRequests | Žádosti Mongo | Počet | Počet
Ne | Microsoft. DocumentDB/databaseAccounts | MongoRequestsCount | Počet požadavků Mongo | CountPerSecond | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | MongoRequestsDelete | Frekvence žádosti o odstranění Mongo | CountPerSecond | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | MongoRequestsInsert | Frekvence požadavků na vložení Mongo | CountPerSecond | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | MongoRequestsQuery | Frekvence požadavků na dotaz Mongo | CountPerSecond | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | MongoRequestsUpdate | Frekvence požadavků na aktualizace Mongo | CountPerSecond | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | ProvisionedThroughput | Zřízená propustnost | Počet | Maximum
Ano | Microsoft. DocumentDB/databaseAccounts | ReplicationLatency | Latence replikace p99 | Milisekund | Průměr
Ne | Microsoft. DocumentDB/databaseAccounts | ServiceAvailability | Dostupnost služby | Procento | Průměr
Ano | Microsoft. DocumentDB/databaseAccounts | TotalRequests | Požadavky celkem | Počet | Počet
Ano | Microsoft. DocumentDB/databaseAccounts | TotalRequestUnits | Celkový počet jednotek žádostí | Počet | Celkem
Ne | Microsoft. EnterpriseKnowledgeGraph/Services | FailureCount | Počet selhání | Počet | Počet
Ne | Microsoft. EnterpriseKnowledgeGraph/Services | SuccessCount | Počet úspěchů | Počet | Počet
Ne | Microsoft. EnterpriseKnowledgeGraph/Services | SuccessLatency | Latence úspěchu | Milisekund | Průměr
Ne | Microsoft. EnterpriseKnowledgeGraph/Services | TransactionCount | Počet transakcí | Počet | Počet
Ano | Microsoft. EventGrid/domény | DeadLetteredCount | Nedoručené události s písmeny | Počet | Celkem
Ne | Microsoft. EventGrid/domény | DeliveryAttemptFailCount | Doručení – neúspěšné události | Počet | Celkem
Ano | Microsoft. EventGrid/domény | DeliverySuccessCount | Doručené události | Počet | Celkem
Ne | Microsoft. EventGrid/domény | DestinationProcessingDurationInMs | Doba trvání zpracování cíle | Milisekundy | Průměr
Ano | Microsoft. EventGrid/domény | DroppedEventCount | Vyřazené události | Počet | Celkem
Ano | Microsoft. EventGrid/domény | MatchedEventCount | Spárované události | Počet | Celkem
Ano | Microsoft. EventGrid/domény | PublishFailCount | Publikovat neúspěšné události | Počet | Celkem
Ano | Microsoft. EventGrid/domény | PublishSuccessCount | Publikované události | Počet | Celkem
Ano | Microsoft. EventGrid/domény | PublishSuccessLatencyInMs | Latence úspěšného publikování | Počet | Celkem
Ano | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Nedoručené události s písmeny | Počet | Celkem
Ne | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Doručení – neúspěšné události | Počet | Celkem
Ano | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Doručené události | Počet | Celkem
Ne | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Doba trvání zpracování cíle | Milisekundy | Průměr
Ano | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Vyřazené události | Počet | Celkem
Ano | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Spárované události | Počet | Celkem
Ano | Microsoft.EventGrid/extensionTopics | PublishFailCount | Publikovat neúspěšné události | Počet | Celkem
Ano | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Publikované události | Počet | Celkem
Ano | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | Latence úspěšného publikování | Počet | Celkem
Ano | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Nespárované události | Počet | Celkem
Ano | Microsoft.EventGrid/topics | PublishFailCount | Publikovat neúspěšné události | Počet | Celkem
Ano | Microsoft.EventGrid/topics | PublishSuccessCount | Publikované události | Počet | Celkem
Ano | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | Latence úspěšného publikování | Počet | Celkem
Ano | Microsoft.EventGrid/topics | UnmatchedEventCount | Nespárované události | Počet | Celkem
Ne | Microsoft. EventHub/clustery | ActiveConnections | ActiveConnections | Počet | Průměr
Ne | Microsoft. EventHub/clustery | AvailableMemory | Paměť k dispozici | Procento | Maximum
Ne | Microsoft. EventHub/clustery | CaptureBacklog | Zachyťte nevyřízené položky. | Počet | Celkem
Ne | Microsoft. EventHub/clustery | CapturedBytes | Zachycené bajty. | B | Celkem
Ne | Microsoft. EventHub/clustery | CapturedMessages | Zachycené zprávy. | Počet | Celkem
Ne | Microsoft. EventHub/clustery | ConnectionsClosed | Uzavřená připojení. | Počet | Průměr
Ne | Microsoft. EventHub/clustery | ConnectionsOpened | Otevřená připojení. | Počet | Průměr
Ne | Microsoft. EventHub/clustery | Procesor | Procesor | Procento | Maximum
Ano | Microsoft. EventHub/clustery | IncomingBytes | Příchozí bajty. | B | Celkem
Ano | Microsoft. EventHub/clustery | IncomingMessages | Příchozí zprávy | Počet | Celkem
Ano | Microsoft. EventHub/clustery | IncomingRequests | Příchozí žádosti | Počet | Celkem
Ano | Microsoft. EventHub/clustery | OutgoingBytes | Odchozí bajty | B | Celkem
Ano | Microsoft. EventHub/clustery | OutgoingMessages | Odchozí zprávy | Počet | Celkem
Ne | Microsoft. EventHub/clustery | QuotaExceededErrors | Chyby překročení kvóty | Počet | Celkem
Ne | Microsoft. EventHub/clustery | ServerErrors | Chyby serveru. | Počet | Celkem
Ne | Microsoft. EventHub/clustery | SuccessfulRequests | Úspěšné požadavky | Počet | Celkem
Ne | Microsoft. EventHub/clustery | ThrottledRequests | Omezené požadavky. | Počet | Celkem
Ne | Microsoft. EventHub/clustery | UserErrors | Chyby uživatele. | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Počet | Průměr
Ne | Microsoft.EventHub/namespaces | CaptureBacklog | Zachyťte nevyřízené položky. | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | CapturedBytes | Zachycené bajty. | B | Celkem
Ne | Microsoft.EventHub/namespaces | CapturedMessages | Zachycené zprávy. | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | ConnectionsClosed | Uzavřená připojení. | Počet | Průměr
Ne | Microsoft.EventHub/namespaces | ConnectionsOpened | Otevřená připojení. | Počet | Průměr
Ano | Microsoft.EventHub/namespaces | EHABL | Archivovat nevyřízené zprávy (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | EHAMBS | Propustnost zpráv archivu (zastaralé) | B | Celkem
Ano | Microsoft.EventHub/namespaces | EHAMSGS | Archivní zprávy (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | EHINBYTES | Příchozí bajty (zastaralé) | B | Celkem
Ano | Microsoft.EventHub/namespaces | EHINMBS | Příchozí bajty (zastaralé) (zastaralé) | B | Celkem
Ano | Microsoft.EventHub/namespaces | EHINMSGS | Příchozí zprávy (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | EHOUTBYTES | Odchozí bajty (zastaralé) | B | Celkem
Ano | Microsoft.EventHub/namespaces | EHOUTMBS | Odchozí bajty (zastaralé) (zastaralé) | B | Celkem
Ano | Microsoft.EventHub/namespaces | EHOUTMSGS | Odchozí zprávy (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | FAILREQ | Neúspěšné žádosti (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | IncomingBytes | Příchozí bajty. | B | Celkem
Ano | Microsoft.EventHub/namespaces | IncomingMessages | Příchozí zprávy | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | IncomingRequests | Příchozí žádosti | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | INMSGS | Příchozí zprávy (zastaralé) (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | INREQS | Příchozí požadavky (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | MEZI sebou | Interní chyby serveru (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | MISCERR | Další chyby (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | OutgoingBytes | Odchozí bajty | B | Celkem
Ano | Microsoft.EventHub/namespaces | OutgoingMessages | Odchozí zprávy | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | OUTMSGS | Odchozí zprávy (zastaralé) (zastaralé) | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | QuotaExceededErrors | Chyby překročení kvóty | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | ServerErrors | Chyby serveru. | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | Velikost | Velikost | B | Průměr
Ne | Microsoft.EventHub/namespaces | SuccessfulRequests | Úspěšné požadavky | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | SUCCREQ | Úspěšné požadavky (zastaralé) | Počet | Celkem
Ano | Microsoft.EventHub/namespaces | SVRBSY | Chyby zaneprázdněnosti serveru (zastaralé) | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | ThrottledRequests | Omezené požadavky. | Počet | Celkem
Ne | Microsoft.EventHub/namespaces | UserErrors | Chyby uživatele. | Počet | Celkem
Ano | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Zařadit požadavky na bránu | Počet | Celkem
Ano | Microsoft.HDInsight/clusters | GatewayRequests | Žádosti o bránu | Počet | Celkem
Ano | Microsoft.HDInsight/clusters | NumActiveWorkers | Počet aktivních pracovníků | Počet | Maximum
Ano | Microsoft.HDInsight/clusters | ScalingRequests | Požadavky na škálování | Počet | Maximum
Ano | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Prahová hodnota metriky | Počet | Průměr
Ano | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Zjištěná kapacita | Počet | Průměr
Ano | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Zaznamenaná hodnota metriky | Počet | Průměr
Ano | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Zahájené akce škálování | Počet | Celkem
Ano | Microsoft. Insights/Components | availabilityResults/availabilityPercentage | Dostupnost | Procento | Průměr
Ne | Microsoft. Insights/Components | availabilityResults/Count | Testy dostupnosti | Počet | Počet
Ano | Microsoft. Insights/Components | availabilityResults/doba trvání | Doba trvání testu dostupnosti | Milisekund | Průměr
Ano | Microsoft. Insights/Components | browserTimings/networkDuration | Doba připojení k síti – načtení stránky | Milisekund | Průměr
Ano | Microsoft. Insights/Components | browserTimings/processingDuration | Doba zpracování klienta | Milisekund | Průměr
Ano | Microsoft. Insights/Components | browserTimings/receiveDuration | Doba přijetí odezvy | Milisekund | Průměr
Ano | Microsoft. Insights/Components | browserTimings/sendDuration | Čas požadavku na odeslání | Milisekund | Průměr
Ano | Microsoft. Insights/Components | browserTimings/totalDuration | Doba načítání stránky v prohlížeči | Milisekund | Průměr
Ne | Microsoft. Insights/Components | závislosti/počet | Volání závislostí | Počet | Počet
Ano | Microsoft. Insights/Components | závislosti/doba trvání | Doba trvání závislosti | Milisekund | Průměr
Ne | Microsoft. Insights/Components | závislosti/selhání | Selhání volání závislostí | Počet | Počet
Ne | Microsoft. Insights/Components | výjimky/prohlížeč | Výjimky prohlížečů | Počet | Počet
Ano | Microsoft. Insights/Components | výjimky/počet | Výjimky | Počet | Počet
Ne | Microsoft. Insights/Components | výjimky/Server | Výjimky serveru | Počet | Počet
Ano | Microsoft. Insights/Components | pageViews/Count | Zobrazení stránek | Počet | Počet
Ano | Microsoft. Insights/Components | pageViews/doba trvání | Doba načítání zobrazení stránky | Milisekund | Průměr
Ano | Microsoft. Insights/Components | performanceCounters/exceptionsPerSecond | Míra výjimek | CountPerSecond | Průměr
Ano | Microsoft. Insights/Components | Čítače výkonu/memoryAvailableBytes | Dostupná paměť | B | Průměr
Ano | Microsoft. Insights/Components | Čítače výkonu/processCpuPercentage | PROCESOR procesů | Procento | Průměr
Ano | Microsoft. Insights/Components | performanceCounters/processIOBytesPerSecond | Rychlost zpracování v/v | BytesPerSecond | Průměr
Ano | Microsoft. Insights/Components | Čítače výkonu/processorCpuPercentage | Čas procesoru | Procento | Průměr
Ano | Microsoft. Insights/Components | performanceCounters/processPrivateBytes | Nesdílené bajty procesu | B | Průměr
Ano | Microsoft. Insights/Components | Čítače výkonu/requestExecutionTime | Doba provádění požadavku HTTP | Milisekund | Průměr
Ano | Microsoft. Insights/Components | performanceCounters/requestsInQueue | Požadavky HTTP ve frontě aplikací | Počet | Průměr
Ano | Microsoft. Insights/Components | performanceCounters/requestsPerSecond | Rychlost požadavku HTTP | CountPerSecond | Průměr
Ne | Microsoft. Insights/Components | požadavky/počet | Žádosti serveru | Počet | Počet
Ano | Microsoft. Insights/Components | žádosti/doba trvání | Doba odezvy serveru | Milisekund | Průměr
Ne | Microsoft. Insights/Components | požadavky/selhání | Neúspěšné požadavky | Počet | Počet
Ne | Microsoft. Insights/Components | žádosti/rychlost | Počet požadavků serveru | CountPerSecond | Průměr
Ano | Microsoft. Insights/Components | trasování/počet | Trasování | Počet | Počet
Ano | Microsoft.KeyVault/vaults | ServiceApiHit | Celkový počet přístupů k rozhraní API služby | Počet | Počet
Ano | Microsoft.KeyVault/vaults | ServiceApiLatency | Celková latence rozhraní API služby | Milisekundy | Průměr
Ano | Microsoft.KeyVault/vaults | ServiceApiResult | Celkový počet výsledků rozhraní API služby | Počet | Počet
Ano | Microsoft.Kusto/Clusters | CacheUtilization | Využití mezipaměti | Procento | Průměr
Ano | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | Maximální počet minut zpoždění pro průběžný export | Počet | Maximum
Ano | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | Průběžný export – počet exportovaných záznamů | Počet | Celkem
Ano | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | Počet nevyřízených položek průběžného exportu | Počet | Maximum
Ano | Microsoft.Kusto/Clusters | ContinuousExportResult | Výsledek průběžného exportu | Počet | Počet
Ano | Microsoft.Kusto/Clusters | Procesor | Procesor | Procento | Průměr
Ano | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | Zpracované události (pro Event/centra IoT) | Počet | Celkem
Ano | Microsoft.Kusto/Clusters | ExportUtilization | Využití exportu | Procento | Maximum
Ano | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | Latence příjmu (v sekundách) | Sekund | Průměr
Ano | Microsoft.Kusto/Clusters | IngestionResult | Výsledek ingestování | Počet | Počet
Ano | Microsoft.Kusto/Clusters | IngestionUtilization | Využití příjmu | Procento | Průměr
Ano | Microsoft.Kusto/Clusters | IngestionVolumeInMB | Objem příjmu (v MB) | Počet | Celkem
Ano | Microsoft.Kusto/Clusters | Udržení | Zachovat naživu | Počet | Průměr
Ano | Microsoft.Kusto/Clusters | QueryDuration | Doba trvání dotazu | Milisekundy | Průměr
Ano | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | Rychlost přijímání požadavků pro streamování | Počet | RateRequestsPerSecond
Ano | Microsoft.Kusto/Clusters | StreamingIngestDataRate | Přenosová rychlost ingestování datových proudů | Počet | Průměr
Ano | Microsoft.Kusto/Clusters | StreamingIngestDuration | Doba ingestování streamování | Milisekundy | Průměr
Ano | Microsoft.Kusto/Clusters | StreamingIngestResults | Výsledek ingestování streamování | Počet | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionLatency | Latence akcí  | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted | Dokončené akce  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionsFailed | Neúspěšné akce  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped | Vynechané akce  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionsStarted | Spuštěné akce  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded | Úspěšné akce  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionSuccessLatency | Latence úspěšných akcí  | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents | Omezené události akcí | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Využití paměti konektoru pro prostředí integrační služby | Procento | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | Využití procesoru konektoru pro prostředí integrační služby | Procento | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | Využití paměti workflowu pro prostředí integrační služby | Procento | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | Využití procesoru pracovního postupu pro prostředí integrační služby | Procento | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage | Procento selhání spuštění | Procento | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunLatency | Latence spuštění | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | RunsCancelled | Zrušené běhy | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunsCompleted | Dokončené běhy | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunsFailed | Neúspěšná spuštění | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunsStarted | Spuštěné běhy | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded | Úspěšná spuštění | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents | Spustit omezené události | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | RunSuccessLatency | Latence úspěšného spuštění | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents | Události omezeného spuštění | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggerFireLatency | Latence požáru triggeru  | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggerLatency | Latence triggeru  | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted | Aktivační události dokončeny  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersFailed | Neúspěšná triggery  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersFired | Aktivační události aktivovány  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped | Aktivační události přeskočeny | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersStarted | Spuštěné aktivační události  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded | Aktivační události byly úspěšné  | Počet | Celkem
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggerSuccessLatency | Latence úspěšnosti triggeru  | Sekund | Průměr
Ano | Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents | Omezené události triggeru | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionLatency | Latence akcí  | Sekund | Průměr
Ano | Microsoft.Logic/workflows | ActionsCompleted | Dokončené akce  | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionsFailed | Neúspěšné akce  | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionsSkipped | Vynechané akce  | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionsStarted | Spuštěné akce  | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionsSucceeded | Úspěšné akce  | Počet | Celkem
Ano | Microsoft.Logic/workflows | ActionSuccessLatency | Latence úspěšných akcí  | Sekund | Průměr
Ano | Microsoft.Logic/workflows | ActionThrottledEvents | Omezené události akcí | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillableActionExecutions | Fakturovatelné provádění akcí | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillableTriggerExecutions | Fakturovatelná spuštění triggerů | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageNativeOperation | Využití fakturace pro provádění nativních operací | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageNativeOperation | Využití fakturace pro provádění nativních operací | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageStandardConnector | Využití fakturace pro spuštění standardních konektorů | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageStandardConnector | Využití fakturace pro spuštění standardních konektorů | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Využití fakturace pro provádění spotřeby úložiště | Počet | Celkem
Ano | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Využití fakturace pro provádění spotřeby úložiště | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunFailurePercentage | Procento selhání spuštění | Procento | Celkem
Ano | Microsoft.Logic/workflows | RunLatency | Latence spuštění | Sekund | Průměr
Ano | Microsoft.Logic/workflows | RunsCancelled | Zrušené běhy | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunsCompleted | Dokončené běhy | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunsFailed | Neúspěšná spuštění | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunsStarted | Spuštěné běhy | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunsSucceeded | Úspěšná spuštění | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunStartThrottledEvents | Spustit omezené události | Počet | Celkem
Ano | Microsoft.Logic/workflows | RunSuccessLatency | Latence úspěšného spuštění | Sekund | Průměr
Ano | Microsoft.Logic/workflows | RunThrottledEvents | Události omezeného spuštění | Počet | Celkem
Ano | Microsoft.Logic/workflows | TotalBillableExecutions | Fakturovatelná spuštění celkem | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggerFireLatency | Latence požáru triggeru  | Sekund | Průměr
Ano | Microsoft.Logic/workflows | TriggerLatency | Latence triggeru  | Sekund | Průměr
Ano | Microsoft.Logic/workflows | TriggersCompleted | Aktivační události dokončeny  | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggersFailed | Neúspěšná triggery  | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggersFired | Aktivační události aktivovány  | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggersSkipped | Aktivační události přeskočeny | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggersStarted | Spuštěné aktivační události  | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggersSucceeded | Aktivační události byly úspěšné  | Počet | Celkem
Ano | Microsoft.Logic/workflows | TriggerSuccessLatency | Latence úspěšnosti triggeru  | Sekund | Průměr
Ano | Microsoft.Logic/workflows | TriggerThrottledEvents | Omezené události triggeru | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Aktivní jádra | Aktivní jádra | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Aktivní uzly | Aktivní uzly | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Dokončená spuštění | Dokončená spuštění | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Neúspěšná spuštění | Neúspěšná spuštění | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Jádra nečinných | Jádra nečinných | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Nečinné uzly | Nečinné uzly | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Vynechávání jader | Vynechávání jader | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Ukončení uzlů | Ukončení uzlů | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Nasazení modelu se nezdařilo | Nasazení modelu se nezdařilo | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Nasazení modelu spuštěn | Nasazení modelu spuštěn | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Nasazení modelu bylo úspěšné. | Nasazení modelu bylo úspěšné. | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Nepovedlo se zaregistrovat model | Nepovedlo se zaregistrovat model | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Registr modelu byl úspěšný. | Registr modelu byl úspěšný. | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Zrušené jádra | Zrušené jádra | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Přepnuté uzly | Přepnuté uzly | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Procento využití kvóty | Procento využití kvóty | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Spuštěná spuštění | Spuštěná spuštění | Počet | Celkem
Ano | Microsoft.MachineLearningServices/workspaces | Celkový počet jader | Celkový počet jader | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Celkem uzlů | Celkem uzlů | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Nepoužitelné jádra | Nepoužitelné jádra | Počet | Průměr
Ano | Microsoft.MachineLearningServices/workspaces | Nepoužité uzly | Nepoužité uzly | Počet | Průměr
Ano | Microsoft. Maps/Accounts | Dostupnost | Dostupnost | Procento | Průměr
Ne | Microsoft. Maps/Accounts | Využití | Využití | Počet | Počet
Ano | Microsoft. Media/MediaServices | AssetCount | Počet assetů | Počet | Průměr
Ano | Microsoft. Media/MediaServices | AssetQuota | Kvóta prostředků | Počet | Průměr
Ano | Microsoft. Media/MediaServices | AssetQuotaUsedPercentage | Procento využité kvóty prostředků | Procento | Průměr
Ano | Microsoft. Media/MediaServices | ContentKeyPolicyCount | Počet zásad klíče obsahu | Počet | Průměr
Ano | Microsoft. Media/MediaServices | ContentKeyPolicyQuota | Kvóta zásad pro klíč obsahu | Počet | Průměr
Ano | Microsoft. Media/MediaServices | ContentKeyPolicyQuotaUsedPercentage | Procento využité kvóty zásad klíčů obsahu | Procento | Průměr
Ano | Microsoft. Media/MediaServices | StreamingPolicyCount | Počet zásad streamování | Počet | Průměr
Ano | Microsoft. Media/MediaServices | StreamingPolicyQuota | Kvóta zásad streamování | Počet | Průměr
Ano | Microsoft. Media/MediaServices | StreamingPolicyQuotaUsedPercentage | Procento využité kvóty zásad streamování | Procento | Průměr
Ano | Microsoft. Media/MediaServices/starají | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft. Media/MediaServices/starají | Požadavky | Požadavky | Počet | Celkem
Ano | Microsoft. Media/MediaServices/starají | SuccessE2ELatency | Koncová latence úspěch | Milisekundy | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | Počet pozastavení GC | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | Celkový čas pozastavení GC | Milisekundy | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | Maximální velikost dostupných starých dat generace | B | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | Stará velikost dat generace | B | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Zvýšit úroveň na starou velikost dat generace | B | Maximum
Ano | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Procento využití procesoru služby | Procento | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Přiřazená paměť služby | B | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Maximální velikost paměti služby | B | Maximum
Ano | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Využitá paměť služby | B | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | Procento využití procesoru v systému | Procento | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Globální chyba Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Celkový počet přijatých bajtů Tomcat | B | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Maximální čas požadavku Tomcat | Milisekundy | Maximum
Ano | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Celkový počet žádostí Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Celkový počet požadavků Tomcat | Milisekundy | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Průměrná doba požadavku Tomcat | Milisekundy | Průměr
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Celkový počet odeslaných bajtů Tomcat | B | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Počet otevřených relací Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Maximální počet aktivních relací Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Maximální doba běhu relace Tomcat | Milisekundy | Maximum
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Počet vytvořených relací Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | Počet vypršení relace Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | Počet odmítnutých relací Tomcat | Počet | Celkem
Ano | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Zvýšení úrovně na velikost dat malé generace | B | Maximum
Ano | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Využití přiděleného fondu svazků | B | Průměr
Ano | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Celková logická velikost fondu svazků | B | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | AverageReadLatency | Průměrná latence čtení | Milisekund | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | AverageWriteLatency | Průměrná latence zápisu | Milisekund | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | ReadIops | Čtení IOPS | CountPerSecond | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | VolumeLogicalSize | Logická velikost svazku | B | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | VolumeSnapshotSize | Velikost snímku svazku | B | Průměr
Ano | Microsoft. NetApp/netAppAccounts/capacityPools/svazky | WriteIops | Zápis IOPS | CountPerSecond | Průměr
Ne | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Čas Application Gateway celkem | Milisekund | Průměr
Ne | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Počet požadavků za minutu na hostitele v pořádku | Počet | Průměr
Ne | Microsoft.Network/applicationGateways | BackendConnectTime | Čas připojení back-endu | Milisekund | Průměr
Ne | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Doba odezvy prvního bajtu back-endu | Milisekund | Průměr
Ne | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Doba odezvy posledního bajtu back-endu | Milisekund | Průměr
Ano | Microsoft.Network/applicationGateways | BackendResponseStatus | Stav odpovědi back-endu | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | BlockedCount | Distribuce pravidla Blokované požadavky brány firewall webových aplikací | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | BlockedReqCount | Počet blokovaných požadavků firewallu webových aplikací | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | BytesReceived | Přijaté bajty | B | Celkem
Ano | Microsoft.Network/applicationGateways | BytesSent | Odeslané bajty | B | Celkem
Ne | Microsoft.Network/applicationGateways | CapacityUnits | Aktuální jednotky kapacity | Počet | Průměr
Ne | Microsoft.Network/applicationGateways | ClientRtt | Čas odezvy klienta | Milisekund | Průměr
Ne | Microsoft.Network/applicationGateways | ComputeUnits | Aktuální výpočetní jednotky | Počet | Průměr
Ano | Microsoft.Network/applicationGateways | CurrentConnections | Aktuální připojení | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | FailedRequests | Neúspěšné požadavky | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | HealthyHostCount | Počet hostitelů v pořádku | Počet | Průměr
Ano | Microsoft.Network/applicationGateways | MatchedCount | Distribuce všech pravidel v firewallu webových aplikací | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | ResponseStatus | Stav odpovědi | Počet | Celkem
Ne | Microsoft.Network/applicationGateways | Propustnost | Propustnost | BytesPerSecond | Průměr
Ano | Microsoft.Network/applicationGateways | TlsProtocol | Protokol TLS klienta | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | TotalRequests | Požadavky celkem | Počet | Celkem
Ano | Microsoft.Network/applicationGateways | UnhealthyHostCount | Počet hostitelů není v pořádku | Počet | Průměr
Ano | Microsoft. Network/azurefirewalls | ApplicationRuleHit | Počet volání pravidel aplikace | Počet | Celkem
Ano | Microsoft. Network/azurefirewalls | Zpracováno na zpracování | Zpracovaná data | B | Celkem
Ano | Microsoft. Network/azurefirewalls | FirewallHealth | Stav brány firewall | Procento | Průměr
Ano | Microsoft. Network/azurefirewalls | NetworkRuleHit | Počet volání síťových pravidel | Počet | Celkem
Ano | Microsoft. Network/azurefirewalls | SNATPortUtilization | Využití portu SNAT | Procento | Průměr
Ano | Microsoft. Network/připojení | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/připojení | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Průměr
Ano | Microsoft.Network/dnszones | QueryVolume | Svazek dotazu | Počet | Celkem
Ne | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Využití kapacity sady záznamů | Procento | Maximum
Ano | Microsoft.Network/dnszones | RecordSetCount | Počet sad záznamů | Počet | Maximum
Ano | Microsoft.Network/expressRouteCircuits | ArpAvailability | Dostupnost protokolu ARP | Procento | Průměr
Ano | Microsoft.Network/expressRouteCircuits | BgpAvailability | Dostupnost protokolu BGP | Procento | Průměr
Ne | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Průměr
Ne | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Průměr
Ne | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Průměr
Ne | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Průměr
Ne | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Průměr
Ne | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/expressRouteCircuits/peering | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/expressRouteCircuits/peering | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Průměr
Ne | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Průměr
Ne | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/expressRoutePorts | AdminState | AdminState | Počet | Průměr
Ano | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Počet | Průměr
Ano | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Průměr
Ano | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Počet | Průměr
Ano | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Počet | Průměr
Ano | Microsoft. Network/frontdoors | BackendHealthPercentage | Procento stavu back-endu | Procento | Průměr
Ano | Microsoft. Network/frontdoors | BackendRequestCount | Počet požadavků back-endu | Počet | Celkem
Ano | Microsoft. Network/frontdoors | BackendRequestLatency | Latence žádosti back-endu | Milisekund | Průměr
Ano | Microsoft. Network/frontdoors | BillableResponseSize | Fakturovatelná velikost odpovědi | B | Celkem
Ano | Microsoft. Network/frontdoors | RequestCount | Počet požadavků | Počet | Celkem
Ano | Microsoft. Network/frontdoors | RequestSize | Velikost požadavku | B | Celkem
Ano | Microsoft. Network/frontdoors | ResponseSize | Velikost odpovědi | B | Celkem
Ano | Microsoft. Network/frontdoors | TotalLatency | Celková latence | Milisekund | Průměr
Ano | Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount | Počet požadavků firewallu webových aplikací | Počet | Celkem
Ne | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Přidělené porty SNAT (Preview) | Počet | Celkem
Ano | Microsoft.Network/loadBalancers | ByteCount | Počet bajtů | Počet | Celkem
Ano | Microsoft.Network/loadBalancers | DipAvailability | Stav sondy stavu | Počet | Průměr
Ano | Microsoft.Network/loadBalancers | PacketCount | Počet paketů | Počet | Celkem
Ano | Microsoft.Network/loadBalancers | SnatConnectionCount | Počet připojení SNAT | Počet | Celkem
Ano | Microsoft.Network/loadBalancers | SYNCount | Počet SYN | Počet | Celkem
Ne | Microsoft.Network/loadBalancers | UsedSnatPorts | Využité porty SNAT (Preview) | Počet | Celkem
Ano | Microsoft.Network/loadBalancers | VipAvailability | Dostupnost cesty k datům | Počet | Průměr
Ano | Microsoft.Network/networkInterfaces | BytesReceivedRate | Přijaté bajty | B | Celkem
Ano | Microsoft.Network/networkInterfaces | BytesSentRate | Odeslané bajty | B | Celkem
Ano | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Přijaté pakety | Počet | Celkem
Ano | Microsoft.Network/networkInterfaces | PacketsSentRate | Odeslané pakety | Počet | Celkem
Ano | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Průměrná doba odezvy (MS) | Milisekund | Průměr
Ano | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Počet neúspěšných kontrol v procentech (Preview) | Procento | Průměr
Ano | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | % PROBE selhalo | Procento | Průměr
Ano | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Doba odezvy (MS) (Preview) | Milisekund | Průměr
Ano | Microsoft.Network/publicIPAddresses | ByteCount | Počet bajtů | Počet | Celkem
Ano | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Příchozí bajty vynechané DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Příchozí bajty předané DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | BytesInDDoS | Příchozí bajty DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Příchozí pakety SYN pro aktivaci zmírnění DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Příchozí pakety TCP pro aktivaci zmírnění DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Příchozí pakety UDP pro aktivaci zmírnění DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | V části útok DDoS nebo ne | Počet | Maximum
Ano | Microsoft.Network/publicIPAddresses | PacketCount | Počet paketů | Počet | Celkem
Ano | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Vynechané příchozí pakety DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | DDoS předaných příchozích paketů | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | PacketsInDDoS | DDoS příchozích paketů | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | SynCount | Počet SYN | Počet | Celkem
Ano | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | Příchozí bajty protokolu TCP vyhozené DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | Příchozí DDoS předaných bajtů protokolu TCP | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Příchozí bajty DDoS TCP | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | Zrušené příchozí pakety protokolu TCP DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | DDoS předaných paketů příchozího protokolu TCP | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | DDoS příchozí pakety TCP | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | Zrušené Příchozí bajty UDP DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | Příchozí DDoS předávaných bajtů UDP | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | Příchozí bajty UDP DDoS | BytesPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | Zrušené příchozí pakety UDP DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | Příchozí pakety UDP předané DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | Příchozí pakety UDP DDoS | CountPerSecond | Maximum
Ano | Microsoft.Network/publicIPAddresses | VipAvailability | Dostupnost cesty k datům | Počet | Průměr
Ano | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Stav koncového bodu podle koncového bodu | Počet | Maximum
Ano | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Dotazy podle vráceného koncového bodu | Počet | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Šířka pásma S2S brány | BytesPerSecond | Průměr
Ano | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Šířka pásma P2S brány | BytesPerSecond | Průměr
Ano | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | Počet připojení P2S | Počet | Maximum
Ano | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Šířka pásma tunelu | BytesPerSecond | Průměr
Ano | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Výstupní bajty tunelu | B | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Odpojení odchozího paketu o neshodě tunelového propojení TS | Počet | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Odchozí tunelové pakety | Počet | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Vstupní bajty tunelového propojení | B | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Přetažení paketů neshody TS s tunelem pro příchozí přenosy | Počet | Celkem
Ano | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Tunelové pakety pro příchozí připojení | Počet | Celkem
Ano | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Čas odezvy na virtuální počítač pomocí příkazů pro odeslání | Milisekund | Průměr
Ano | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Nepovedlo se odeslat příkazy do virtuálního počítače | Procento | Průměr
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | přijíman | Příchozí zprávy | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | příchozí. All. failedrequests | Všechny příchozí neúspěšné požadavky | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | Všechny příchozí žádosti | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | Odeslaná naplánovaná nabízená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | Zrušená plánovaná nabízená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalace. vše | Operace správy instalace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Odstranit operace instalace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | Získat operace instalace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalace. patch | Operace instalace opravy | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | Operace vytvoření nebo aktualizace instalace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub. push | Všechna odchozí oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Chybné nebo prošlé chyby kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | Chyby kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. allpns. invalidpayload | Chyby datové části | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. allpns. pnserror | Chyby systému externích oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. allpns. úspěch | Úspěšná oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Chyba služby APN Bad Channel | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Chyba kanálu vypršení platnosti služby APNS | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | Chyby autorizace APNS | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. APNs. invalidnotificationsize | APN – Chyba neplatné velikosti oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | Chyby služby APN | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Úspěšná oznámení APNS | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | GCM chyby ověřování | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | Chyba GCM špatného kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | Chyba kanálu vypršení platnosti GCM | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. GCM. invalidcredentials | GCM – chyby autorizace (neplatné přihlašovací údaje) | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. GCM. invalidnotificationformat | GCM neplatný formát oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. GCM. invalidnotificationsize | GCMá Chyba neplatné velikosti oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | GCM chyby | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | GCM úspěšná oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. GCM. omezení | GCM omezená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | GCM chybná chyba kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | MPNS chyby ověřování | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | Chyba MPNS špatného kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | MPNS kanál odpojen | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | MPNS Vyřazená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | Neplatné přihlašovací údaje MPNS | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. MPNS. invalidnotificationformat | MPNS neplatný formát oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | MPNS chyby | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | MPNS úspěšná oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | MPNS omezená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | WNS chyby ověřování | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | Chyba WNS špatného kanálu | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. channeldisconnected | WNS kanál odpojen | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | WNS kanál – omezení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | WNS Vyřazená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | Chyba kanálu vypršení platnosti WNS | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. invalidcredentials | WNS – chyby autorizace (neplatné přihlašovací údaje) | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. invalidnotificationformat | WNS neplatný formát oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. invalidnotificationsize | WNSá Chyba neplatné velikosti oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. invalidtoken | WNS – chyby autorizace (neplatný token) | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. pnserror | WNS chyby | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. úspěch | WNS úspěšná oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | odchozí. WNS. omezení | WNS omezená oznámení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS – chyby autorizace (nedostupné) | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS chyby autorizace (špatný token) | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registrace. vše | Operace registrace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | Operace vytvoření registrace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | Registrace – operace odstranění | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | Registrace – operace čtení | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | Operace aktualizace registrace | Počet | Celkem
Ano | Microsoft.NotificationHubs/Namespaces/NotificationHubs | naplánováno. čeká na | Nedokončená plánovaná oznámení | Počet | Celkem
Ano | Microsoft.OperationalInsights/workspaces | Average_ k dispozici paměti | % Dostupné paměti | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% dostupného odkládacího prostoru | % Dostupného odkládacího prostoru | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% používaných potvrzených bajtů | % Používaných potvrzených bajtů | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času DPC | % Času DPC | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_ uzlů inode% Free | % Bezplatného uzlů inode | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% volného místa | % Volného místa | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% volného místa | % Volného místa | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času nečinnosti | % Času nečinnosti | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času přerušení | % Času přerušení | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času čekání na v/v | % Času čekání na v/v | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% dobrý čas | % Dobrý čas | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% privilegovaného času | % Privilegovaného času | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času procesoru | % Času procesoru | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% času procesoru | % Času procesoru | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% využití uzlů inode | % Použitého uzlů inode | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Využitá paměť Average_% | % Využité paměti | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Využité místo Average_% | % Využitého místa | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_% využitého místa odkládacího souboru | % Využitého místa odkládacího souboru | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Čas uživatele Average_% | % Uživatelského času | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Available MB | Počet MB k dispozici | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Paměť Average_Available v MB | Dostupná paměť v MB | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Přeměna Average_Available MB | Dostupný počet MB swap | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Avg. Doba disku/čtení | Střední doba disku/čtení | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Avg. Doba disku/čtení | Střední doba disku/čtení | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Avg. Doba disku/přenos | Střední doba disku/přenos | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Avg. Doby disku/zápis | Střední doba disku/zápis | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Avg. Doby disku/zápis | Střední doba disku/zápis | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Přijaté Average_Bytes za sekundu | Přijaté bajty/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Odeslané Average_Bytes za sekundu | Odeslané bajty/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Bytes celkem/s | Bajty celkem/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Délka fronty disku Average_Current | Aktuální délka fronty disku | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk přečtených bajtů/s | Bajty čtení z disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk čtení za sekundu | Čtení disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk čtení za sekundu | Čtení disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk přenosů za sekundu | Přenosy disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk přenosů za sekundu | Přenosy disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Disk bajty zápisu za sekundu | Bajty zapisování na disk/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Zápisy Average_Disk za sekundu | Zápis disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Zápisy Average_Disk za sekundu | Zápis disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Free megabajtů | Volné megabajty | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Free megabajtů | Volné megabajty | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Free fyzická paměť | Volná fyzická paměť | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Free mezera v stránkovacích souborech | Volné místo ve stránkovacích souborech | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Free virtuální paměti | Volná virtuální paměť | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Počet bajtů Average_Logical disku za sekundu | Bajtů logického disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Page čtení za sekundu | Čtení stránek/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Zápisy Average_Page za sekundu | Zápisy stránek/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Pages/s | Stránky/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Pct privilegovaného času | Privilegovaný čas protokolu PCT | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Čas uživatele Average_Pct | Doba uživatele v protokolu PCT | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Počet bajtů Average_Physical disku za sekundu | Bajty fyzického disku/s | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Processes | Procesy | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Délka fronty Average_Processor | Délka fronty procesoru | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Size uložený ve stránkovacích souborech | Velikost uložená ve stránkovacích souborech | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Total bajty | Bajty celkem | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Přijaté bajty Average_Total | Celkový počet přijatých bajtů | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Přenesené bajty Average_Total | Celkový počet bajtů přenesených | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Total kolizí | Celkový počet kolizí | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Přijaté pakety Average_Total | Celkový počet přijatých paketů | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Odeslané Average_Total pakety | Celkový počet odeslaných paketů | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Total chyby příjmu | Celkový počet chyb příjmu | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Chyby při odesílání Average_Total | Chyby odesílání celkem | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Uptime | Doba provozu | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Used MB odkládacího prostoru | Využité místo odkládacího souboru v MB | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Used kB paměti | Využitá paměť v kilobajtech | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Used paměť v MB | Využitá paměť v MB | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Users | Uživatelé | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Average_Virtual sdílená paměť | Virtuální sdílená paměť | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Událost | Událost | Počet | Průměr
Ano | Microsoft.OperationalInsights/workspaces | Signály | Signály | Počet | Celkem
Ano | Microsoft.OperationalInsights/workspaces | Aktualizovat | Aktualizovat | Počet | Průměr
Ano | Microsoft.PowerBIDedicated/capacities | memory_metric | Paměť | B | Průměr
Ano | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Thrashing paměti (datové sady) | Procento | Průměr
Ano | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU vysoké využití | Počet | Celkem
Ano | Microsoft.PowerBIDedicated/capacities | QueryDuration | Doba trvání dotazu (datové sady) | Milisekundy | Průměr
Ano | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Délka fronty úloh fondu dotazů (datové sady) | Počet | Průměr
Ne | Microsoft. Relay/obory názvů | ActiveConnections | ActiveConnections | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ActiveListeners | ActiveListeners | Počet | Celkem
Ano | Microsoft. Relay/obory názvů | BytesTransferred | BytesTransferred | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ListenerConnections-ClientError | ListenerConnections-ClientError | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ListenerConnections-ServerError | ListenerConnections-ServerError | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ListenerConnections – úspěch | ListenerConnections – úspěch | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | ListenerDisconnects | ListenerDisconnects | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | SenderConnections – ClientError | SenderConnections – ClientError | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | SenderConnections-ServerError | SenderConnections-ServerError | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | SenderConnections – úspěch | SenderConnections – úspěch | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Počet | Celkem
Ne | Microsoft. Relay/obory názvů | SenderDisconnects | SenderDisconnects | Počet | Celkem
Ano | Microsoft.Search/searchServices | SearchLatency | Latence hledání | Sekund | Průměr
Ano | Microsoft.Search/searchServices | SearchQueriesPerSecond | Hledání dotazů za sekundu | CountPerSecond | Průměr
Ano | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Procento omezených vyhledávacích dotazů | Procento | Průměr
Ne | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | ActiveMessages | Počet aktivních zpráv ve frontě nebo tématu. | Počet | Průměr
Ne | Microsoft.ServiceBus/namespaces | ConnectionsClosed | Uzavřená připojení. | Počet | Průměr
Ne | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Otevřená připojení. | Počet | Průměr
Ne | Microsoft.ServiceBus/namespaces | CPUXNS | PROCESOR (zastaralé) | Procento | Maximum
Ne | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Počet nedoručených zpráv ve frontě nebo tématu | Počet | Průměr
Ano | Microsoft.ServiceBus/namespaces | IncomingMessages | Příchozí zprávy | Počet | Celkem
Ano | Microsoft.ServiceBus/namespaces | IncomingRequests | Příchozí žádosti | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | Zprávy | Počet zpráv ve frontě nebo tématu. | Počet | Průměr
Ne | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | Procesor | Procento | Maximum
Ne | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Využití paměti | Procento | Maximum
Ano | Microsoft.ServiceBus/namespaces | OutgoingMessages | Odchozí zprávy | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | ScheduledMessages | Počet naplánovaných zpráv ve frontě nebo tématu. | Počet | Průměr
Ne | Microsoft.ServiceBus/namespaces | ServerErrors | Chyby serveru. | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | Velikost | Velikost | B | Průměr
Ne | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Úspěšné požadavky | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | ThrottledRequests | Omezené požadavky. | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | UserErrors | Chyby uživatele. | Počet | Celkem
Ne | Microsoft.ServiceBus/namespaces | WSXNS | Využití paměti (zastaralé) | Procento | Maximum
Ne | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | B | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | B | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Procento | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Procento | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Počet | Průměr
Ne | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | Počet | Průměr
Ano | Microsoft. SignalRService/Signaler | ConnectionCount | Počet připojení | Počet | Maximum
Ano | Microsoft. SignalRService/Signaler | InboundTraffic | Příchozí provoz | B | Celkem
Ano | Microsoft. SignalRService/Signaler | MessageCount | Počet zpráv | Počet | Celkem
Ano | Microsoft. SignalRService/Signaler | OutboundTraffic | Odchozí provoz | B | Celkem
Ano | Microsoft. SignalRService/Signaler | SystemErrors | Systémové chyby | Procento | Maximum
Ano | Microsoft. SignalRService/Signaler | UserErrors | Chyby uživatele | Procento | Maximum
Ano | Microsoft.Sql/managedInstances | avg_cpu_percent | Průměrné procento procesoru | Procento | Průměr
Ano | Microsoft.Sql/managedInstances | io_bytes_read | Přečtené vstupně-výstupní bajty | B | Průměr
Ano | Microsoft.Sql/managedInstances | io_bytes_written | Zapsané vstupně-výstupní bajty | B | Průměr
Ano | Microsoft.Sql/managedInstances | io_requests | Počet požadavků v/v | Počet | Průměr
Ano | Microsoft.Sql/managedInstances | reserved_storage_mb | Rezervované místo v úložišti | Počet | Průměr
Ano | Microsoft.Sql/managedInstances | storage_space_used_mb | Využité místo úložiště | Počet | Průměr
Ano | Microsoft.Sql/managedInstances | virtual_core_count | Počet virtuálních jader | Počet | Průměr
Ne | Microsoft.Sql/servers | database_dtu_consumption_percent | Procento DTU | Procento | Průměr
Ne | Microsoft.Sql/servers | database_storage_used | Využité místo pro data | B | Průměr
Ano | Microsoft.Sql/servers | dtu_consumption_percent | Procento DTU | Procento | Průměr
Ano | Microsoft.Sql/servers | dtu_used | Využité DTU | Počet | Průměr
Ano | Microsoft.Sql/servers | storage_used | Využité místo pro data | B | Průměr
Ano | Microsoft.Sql/servers/databases | allocated_data_storage | Přidělené datové místo | B | Průměr
Ano | Microsoft.Sql/servers/databases | app_cpu_billed | CPU aplikace se fakturuje. | Počet | Celkem
Ano | Microsoft.Sql/servers/databases | app_cpu_percent | Procento využití procesoru aplikací | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | app_memory_percent | Procento paměti aplikace | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | blocked_by_firewall | Blokováno bránou firewall | Počet | Celkem
Ano | Microsoft.Sql/servers/databases | cache_hit_percent | Procento přístupů do mezipaměti | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | cache_used_percent | Procento využité mezipaměti | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | connection_failed | Neúspěšná připojení | Počet | Celkem
Ano | Microsoft.Sql/servers/databases | connection_successful | Úspěšná připojení | Počet | Celkem
Ano | Microsoft.Sql/servers/databases | cpu_limit | Limit procesoru | Počet | Průměr
Ano | Microsoft.Sql/servers/databases | cpu_percent | Procento CPU | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | cpu_used | Využitý procesor | Počet | Průměr
Ano | Microsoft.Sql/servers/databases | ukončení | Zablokování | Počet | Celkem
Ano | Microsoft.Sql/servers/databases | dtu_consumption_percent | Procento DTU | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | dtu_limit | Limit DTU | Počet | Průměr
Ano | Microsoft.Sql/servers/databases | dtu_used | Využité DTU | Počet | Průměr
Ano | Microsoft.Sql/servers/databases | dwu_consumption_percent | Procento DWU | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | dwu_limit | DWU limit | Počet | Maximum
Ano | Microsoft.Sql/servers/databases | dwu_used | DWU použito | Počet | Maximum
Ano | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Místní procento databáze tempdb | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | log_write_percent | Procentní hodnota protokolu v/v | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | memory_usage_percent | Procento paměti | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | physical_data_read_percent | Procento datových V/V | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | sessions_percent | Procento relací | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | úložiště | Využité místo pro data | B | Maximum
Ano | Microsoft.Sql/servers/databases | storage_percent | Procento využitého datového prostoru | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | tempdb_data_size | Velikost datového souboru tempdb v kilobajtech | Počet | Maximum
Ano | Microsoft.Sql/servers/databases | tempdb_log_size | Velikost souboru protokolu tempdb v kilobajtech | Počet | Maximum
Ano | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Použit protokol tempdb v procentech | Procento | Maximum
Ano | Microsoft.Sql/servers/databases | workers_percent | Procento pracovních procesů | Procento | Průměr
Ano | Microsoft.Sql/servers/databases | xtp_storage_percent | Procentuální hodnota úložiště OLTP v paměti | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Přidělené datové místo | B | Průměr
Ano | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Procentuální hodnota přiděleného datového prostoru | Procento | Maximum
Ano | Microsoft.Sql/servers/elasticPools | cpu_limit | Limit procesoru | Počet | Průměr
Ano | Microsoft.Sql/servers/elasticPools | cpu_percent | Procento CPU | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | cpu_used | Využitý procesor | Počet | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Přidělené datové místo | B | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_cpu_limit | Limit procesoru | Počet | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Procento CPU | Procento | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_cpu_used | Využitý procesor | Počet | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | Procento DTU | Procento | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_eDTU_used | využité eDTU | Počet | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Procentní hodnota protokolu v/v | Procento | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Procento datových V/V | Procento | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Procento relací | Procento | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_storage_used | Využité místo pro data | B | Průměr
Ne | Microsoft.Sql/servers/elasticPools | database_workers_percent | Procento pracovních procesů | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Procento DTU | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | eDTU_limit | limit eDTU | Počet | Průměr
Ano | Microsoft.Sql/servers/elasticPools | eDTU_used | využité eDTU | Počet | Průměr
Ano | Microsoft.Sql/servers/elasticPools | log_write_percent | Procentní hodnota protokolu v/v | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Procento datových V/V | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | sessions_percent | Procento relací | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | storage_limit | Maximální velikost dat | B | Průměr
Ano | Microsoft.Sql/servers/elasticPools | storage_percent | Procento využitého datového prostoru | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | storage_used | Využité místo pro data | B | Průměr
Ano | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Velikost datového souboru tempdb v kilobajtech | Počet | Maximum
Ano | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Velikost souboru protokolu tempdb v kilobajtech | Počet | Maximum
Ano | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Použit protokol tempdb v procentech | Procento | Maximum
Ano | Microsoft.Sql/servers/elasticPools | workers_percent | Procento pracovních procesů | Procento | Průměr
Ano | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Procentuální hodnota úložiště OLTP v paměti | Procento | Průměr
Ano | Microsoft.Storage/storageAccounts | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft.Storage/storageAccounts | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts | Transakce | Transakce | Počet | Celkem
Ne | Microsoft.Storage/storageAccounts | UsedCapacity | Použitá kapacita | B | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | Dostupnost | Dostupnost | Procento | Průměr
Ne | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Kapacita služby Blob | B | Průměr
Ne | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Počet objektů blob | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Počet kontejnerů služby Blob | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ne | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Kapacita indexu | B | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/blobServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft. Storage/storageAccounts/služby | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft. Storage/storageAccounts/služby | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ne | Microsoft. Storage/storageAccounts/služby | Kapacita zařízení | Kapacita souboru | B | Průměr
Ne | Microsoft. Storage/storageAccounts/služby | FileCount | Počet souborů | Počet | Průměr
Ne | Microsoft. Storage/storageAccounts/služby | FileShareCount | Počet sdílených souborů | Počet | Průměr
Ne | Microsoft. Storage/storageAccounts/služby | FileShareQuota | Velikost kvóty sdílení souborů | B | Průměr
Ne | Microsoft. Storage/storageAccounts/služby | FileShareSnapshotCount | Počet snímků sdílené složky | Počet | Průměr
Ne | Microsoft. Storage/storageAccounts/služby | FileShareSnapshotSize | Velikost snímku sdílené složky | B | Průměr
Ano | Microsoft. Storage/storageAccounts/služby | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft. Storage/storageAccounts/služby | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft. Storage/storageAccounts/služby | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft. Storage/storageAccounts/služby | Transakce | Transakce | Počet | Celkem
Ano | Microsoft.Storage/storageAccounts/queueServices | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts/queueServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Kapacita služby Queue | B | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Počet front | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Počet zpráv fronty | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/queueServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft.Storage/storageAccounts/tableServices | Dostupnost | Dostupnost | Procento | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | Výchozí přenos dat | Výchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts/tableServices | Příchozí přenos dat | Příchozí přenos dat | B | Celkem
Ano | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Kapacita služby Table | B | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | TableCount | Počet tabulek | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Počet entit tabulek | Počet | Průměr
Ano | Microsoft.Storage/storageAccounts/tableServices | Transakce | Transakce | Počet | Celkem
Ano | Microsoft. StorageCache/caches | ClientIOPS | Celkový počet vstupně-výstupních operací klienta | Počet | Průměr
Ano | Microsoft. StorageCache/caches | ClientLatency | Průměrná latence klienta | Milisekundy | Průměr
Ano | Microsoft. StorageCache/caches | ClientLockIOPS | IOPS zámku klienta | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientMetadataReadIOPS | Metadata klienta přečetla IOPS | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientMetadataWriteIOPS | Zápis metadat klienta za sekundu | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientReadIOPS | Klientský vstupně-výstupní operace čtení | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientReadThroughput | Průměrná propustnost čtení mezipaměti | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientWriteIOPS | Zápis klientského IOPS za sekundu | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | ClientWriteThroughput | Průměrná propustnost zápisu mezipaměti | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetAsyncWriteThroughput | Propustnost asynchronního zápisu StorageTarget | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetFillThroughput | Propustnost výplně StorageTarget | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetHealth | Stav cíle úložiště | Počet | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetIOPS | Celkem StorageTarget IOPS | Počet | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetLatency | Latence StorageTarget | Milisekundy | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetMetadataReadIOPS | StorageTarget metadata Read IOPS | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetMetadataWriteIOPS | Zápisy metadat StorageTarget IOPS | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetReadAheadThroughput | Propustnost před čtením StorageTarget | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetReadIOPS | StorageTarget čtení IOPS | CountPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetSyncWriteThroughput | Propustnost synchronního zápisu StorageTarget | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetTotalReadThroughput | StorageTarget celkové propustnosti čtení | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetTotalWriteThroughput | StorageTarget celkové propustnosti zápisu | BytesPerSecond | Průměr
Ano | Microsoft. StorageCache/caches | StorageTargetWriteIOPS | StorageTarget zápis IOPS | Počet | Průměr
Ano | Microsoft. StorageCache/caches | Doba provozu | Doba provozu | Počet | Průměr
Ano | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Výsledek relace synchronizace | Počet | Průměr
Ano | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Synchronizované bajty | B | Celkem
Ano | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Velikost odvolání při vrstvení cloudu podle aplikace | B | Celkem
Ano | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Velikost odvolání při vyvolání vrstvy cloudu | B | Celkem
Ano | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Stažení vrstvení cloudu | B | Celkem
Ano | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Propustnost volání při navracení cloudových vrstev | BytesPerSecond | Průměr
Ano | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Online stav serveru | Počet | Maximum
Ano | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Synchronizované soubory | Počet | Celkem
Ano | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Soubory se nesynchronizují | Počet | Celkem
Ano | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Online stav serveru | Počet | Maximum
Ano | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Stažení vrstvení cloudu | B | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Synchronizované bajty | B | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Synchronizované soubory | Počet | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Soubory se nesynchronizují | Počet | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Synchronizované bajty | B | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Synchronizované soubory | Počet | Celkem
Ano | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Soubory se nesynchronizují | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Nezdařené požadavky na funkce | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Události funkcí | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Požadavky na funkce | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Chyby převodu dat | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Chyby deserializace vstupu | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Události mimo pořadí | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Události předčasného vstupu | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | chyby | Chyby za běhu | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Bajty vstupních událostí | B | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Vstupní události | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Vstupní události v backlogu | Počet | Maximum
Ano | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Přijaté vstupní zdroje | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Pozdní vstupní události | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Výstupní události | Počet | Celkem
Ano | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Zpoždění vodoznaku | Sekund | Maximum
Ano | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | % využití SU | Procento | Maximum
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Bajty čtení z disku | Bajty čtení z disku | B | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Operace čtení z disku/s | Operace čtení z disku/s | CountPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Bajty zápisu na disk | Bajty zápisu na disk | B | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Operace zápisu na disk/s | Operace zápisu na disk/s | CountPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Bajty čtení z disku/s | BytesPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadLatency | Latence čtení disku | Milisekundy | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskReadOperations | Operace čtení z disku | Počet | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Bajty zápisu na disk/s | BytesPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteLatency | Latence zápisu na disk | Milisekundy | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | DiskWriteOperations | Operace zápisu na disk | Počet | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | MemoryActive | Paměť aktivní | B | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | MemoryGranted | Přidělená paměť | B | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | MemoryUsed | Využitá paměť | B | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Síťové vstupy | Síťové vstupy | B | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Síťové výstupy | Síťové výstupy | B | Celkem
Ano | Microsoft. VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Síť v bajtech/s | BytesPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Výstupní bajty sítě/s | BytesPerSecond | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | Procento CPU | Procento CPU | Procento | Průměr
Ano | Microsoft. VMwareCloudSimple/virtualMachines | PercentageCpuReady | Procento připraveného procesoru | Milisekundy | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Aktivní požadavky | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Průměrná doba odezvy | Sekund | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Data v | B | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Výstupní data | B | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Procento procesoru | Procento | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Délka fronty disku | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401 | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | HTTP 403 | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | HTTP 404 | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Chyby serveru http | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Délka fronty http | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Zaměstnanci s velkými App Servicey plánu | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Střední App Service plánování pracovních procesů | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Procento paměti | Procento | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | Požadavky | Požadavky | Počet | Celkem
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Plánování pracovníků malých App Service | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Celkový počet front-endy | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | Procento procesoru | Procento | Průměr
Ano | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | Procento paměti | Procento | Průměr
Ano | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | Zaměstnanci, kteří jsou k dispozici | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | Celkový počet pracovníků | Počet | Průměr
Ano | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | Využívané pracovní procesy | Počet | Průměr
Ano | Microsoft.Web/serverfarms | BytesReceived | Data v | B | Celkem
Ano | Microsoft.Web/serverfarms | BytesSent | Výstupní data | B | Celkem
Ano | Microsoft.Web/serverfarms | CpuPercentage | Procento procesoru | Procento | Průměr
Ano | Microsoft.Web/serverfarms | DiskQueueLength | Délka fronty disku | Počet | Průměr
Ano | Microsoft.Web/serverfarms | HttpQueueLength | Délka fronty http | Počet | Průměr
Ano | Microsoft.Web/serverfarms | MemoryPercentage | Procento paměti | Procento | Průměr
Ano | Microsoft.Web/serverfarms | TcpCloseWait | Ukončení čekání protokolu TCP | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpClosing | Ukončení protokolu TCP | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpEstablished | TCP – vytvořeno | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpFinWait1 | TCP FIN – čekání 1 | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpFinWait2 | TCP FIN – čekání 2 | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpLastAck | Poslední potvrzení TCP | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpSynReceived | Přijato TCP syn | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpSynSent | Odesláno TCP syn | Počet | Průměr
Ano | Microsoft.Web/serverfarms | TcpTimeWait | Doba čekání protokolu TCP | Počet | Průměr
Ano | Microsoft.Web/sites | AppConnections | Připojení | Počet | Průměr
Ano | Microsoft.Web/sites | AverageMemoryWorkingSet | Průměrná pracovní sada paměti | B | Průměr
Ano | Microsoft.Web/sites | AverageResponseTime | Průměrná doba odezvy | Sekund | Průměr
Ano | Microsoft.Web/sites | BytesReceived | Data v | B | Celkem
Ano | Microsoft.Web/sites | BytesSent | Výstupní data | B | Celkem
Ano | Microsoft.Web/sites | CpuTime | Čas procesoru | Sekund | Celkem
Ano | Microsoft.Web/sites | CurrentAssemblies | Aktuální sestavení | Počet | Průměr
Ano | Microsoft.Web/sites | FunctionExecutionCount | Počet spuštění funkce | Počet | Celkem
Ano | Microsoft.Web/sites | FunctionExecutionUnits | Jednotky spuštění funkce | Počet | Celkem
Ano | Microsoft.Web/sites | Gen0Collections | Generace paměti gen 0 | Počet | Celkem
Ano | Microsoft.Web/sites | Gen1Collections | Generace paměti 1. generace | Počet | Celkem
Ano | Microsoft.Web/sites | Gen2Collections | Uvolňování paměti 2. generace | Počet | Celkem
Ano | Microsoft.Web/sites | Řeší | Počet popisovačů | Počet | Průměr
Ano | Microsoft.Web/sites | HealthCheckStatus | Stav kontroly stavu | Počet | Průměr
Ano | Microsoft.Web/sites | Http101 | Http 101 | Počet | Celkem
Ano | Microsoft.Web/sites | Http2xx | Http 2xx | Počet | Celkem
Ano | Microsoft.Web/sites | Http3xx | Http 3xx | Počet | Celkem
Ano | Microsoft.Web/sites | Http401 | HTTP 401 | Počet | Celkem
Ano | Microsoft.Web/sites | Http403 | HTTP 403 | Počet | Celkem
Ano | Microsoft.Web/sites | Http404 | HTTP 404 | Počet | Celkem
Ano | Microsoft.Web/sites | Http406 | Http 406 | Počet | Celkem
Ano | Microsoft.Web/sites | Http4xx | Http 4xx | Počet | Celkem
Ano | Microsoft.Web/sites | Http5xx | Chyby serveru http | Počet | Celkem
Ano | Microsoft.Web/sites | HttpResponseTime | Doba odezvy | Sekund | Průměr
Ano | Microsoft.Web/sites | IoOtherBytesPerSecond | IO – ostatní bajty za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | IoOtherOperationsPerSecond | V/v – ostatní operace za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | IoReadBytesPerSecond | Bajty čtení v/v za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | IoReadOperationsPerSecond | Vstupně-výstupní operace čtení za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | IoWriteBytesPerSecond | Vstupně-výstupní bajty zápisu za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | IoWriteOperationsPerSecond | Vstupně-výstupní operace zápisu za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites | MemoryWorkingSet | Pracovní sada paměti | B | Průměr
Ano | Microsoft.Web/sites | PrivateBytes | Soukromé bajty | B | Průměr
Ano | Microsoft.Web/sites | Požadavky | Požadavky | Počet | Celkem
Ano | Microsoft.Web/sites | RequestsInApplicationQueue | Žádosti ve frontě aplikací | Počet | Průměr
Ano | Microsoft.Web/sites | Vlákna | Počet vláken | Počet | Průměr
Ano | Microsoft.Web/sites | TotalAppDomains | Celkový počet domén aplikace | Počet | Průměr
Ano | Microsoft.Web/sites | TotalAppDomainsUnloaded | Celkový počet uvolněných domén aplikace | Počet | Průměr
Ano | Microsoft.Web/sites/slots | AppConnections | Připojení | Počet | Průměr
Ano | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Průměrná pracovní sada paměti | B | Průměr
Ano | Microsoft.Web/sites/slots | AverageResponseTime | Průměrná doba odezvy | Sekund | Průměr
Ano | Microsoft.Web/sites/slots | BytesReceived | Data v | B | Celkem
Ano | Microsoft.Web/sites/slots | BytesSent | Výstupní data | B | Celkem
Ano | Microsoft.Web/sites/slots | CpuTime | Čas procesoru | Sekund | Celkem
Ano | Microsoft.Web/sites/slots | CurrentAssemblies | Aktuální sestavení | Počet | Průměr
Ano | Microsoft.Web/sites/slots | FunctionExecutionCount | Počet spuštění funkce | Počet | Celkem
Ano | Microsoft.Web/sites/slots | FunctionExecutionUnits | Jednotky spuštění funkce | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Gen0Collections | Generace paměti gen 0 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Gen1Collections | Generace paměti 1. generace | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Gen2Collections | Uvolňování paměti 2. generace | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Řeší | Počet popisovačů | Počet | Průměr
Ano | Microsoft.Web/sites/slots | HealthCheckStatus | Stav kontroly stavu | Počet | Průměr
Ano | Microsoft.Web/sites/slots | Http101 | Http 101 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http2xx | Http 2xx | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http3xx | Http 3xx | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http401 | HTTP 401 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http403 | HTTP 403 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http404 | HTTP 404 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http406 | Http 406 | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http4xx | Http 4xx | Počet | Celkem
Ano | Microsoft.Web/sites/slots | Http5xx | Chyby serveru http | Počet | Celkem
Ano | Microsoft.Web/sites/slots | HttpResponseTime | Doba odezvy | Sekund | Průměr
Ano | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | IO – ostatní bajty za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | V/v – ostatní operace za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | IoReadBytesPerSecond | Bajty čtení v/v za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | Vstupně-výstupní operace čtení za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | Vstupně-výstupní bajty zápisu za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | Vstupně-výstupní operace zápisu za sekundu | BytesPerSecond | Celkem
Ano | Microsoft.Web/sites/slots | MemoryWorkingSet | Pracovní sada paměti | B | Průměr
Ano | Microsoft.Web/sites/slots | PrivateBytes | Soukromé bajty | B | Průměr
Ano | Microsoft.Web/sites/slots | Požadavky | Požadavky | Počet | Celkem
Ano | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Žádosti ve frontě aplikací | Počet | Průměr
Ano | Microsoft.Web/sites/slots | Vlákna | Počet vláken | Počet | Průměr
Ano | Microsoft.Web/sites/slots | TotalAppDomains | Celkový počet domén aplikace | Počet | Průměr
Ano | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Celkový počet uvolněných domén aplikace | Počet | Průměr
