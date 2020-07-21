---
title: Azure Monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: f0a8fd186862cf95ebdbb2d5bd92d8ff860b3ba1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515474"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky

Azure Monitor poskytuje ve výchozím nastavení [metriky platforem](data-platform-metrics.md) bez konfigurace. Nabízí několik způsobů, jak pracovat s metrikami platforem, včetně jejich grafů na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. Úplný seznam metrik platforem, které jsou aktuálně k dispozici v konsolidovaném kanálu metriky Azure Monitor, najdete v tématu [metriky – podporované](metrics-supported.md) . Pro dotazování a přístup k těmto metrikám použijte [verzi api 2018-01-01](/rest/api/monitor/metricdefinitions). Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

Metriky platformy můžete exportovat z kanálu Azure monitor do jiných umístění jedním ze dvou způsobů.
1. Použití [nastavení diagnostiky](diagnostic-settings.md) k odeslání do Log Analytics, Event Hubs nebo Azure Storage.
2. Použít [REST API metriky](/rest/api/monitor/metrics/list)

Vzhledem k tomu, že v Azure Monitor back-endu složitými rozhraními, nejsou všechny metriky exportovatelné pomocí nastavení diagnostiky. Následující tabulka uvádí, které lze a nelze exportovat pomocí nastavení diagnostiky.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Změna chování pro hodnoty NULL a nulové hodnoty 
 
Pro metriky platforem, které se dají exportovat prostřednictvím nastavení diagnostiky, je k dispozici několik metrik, pro které Azure Monitor interpretuje 0s jako null. To způsobilo nejasnost mezi skutečnou ' 0s ' (vysílanou prostředkem) a interpretována ' 0s ' (hodnoty null). Brzy dojde ke změně a metriky platforem exportované prostřednictvím nastavení diagnostiky již nebudou exportovány "0s", pokud je skutečně nevygeneroval základní prostředek. 

> [!CAUTION]
> Změna v chování popsané výše je naplánována na 1. června 2020.

Poznámka:

1.  Pokud odstraníte skupinu prostředků nebo konkrétní prostředek, data metriky z ovlivněných prostředků už nebudou odesílána do nastavení diagnostiky export cílů. To znamená, že se už nebude zobrazovat v Event Hubs, účtech úložiště a Log Analytics pracovních prostorech.
2.  Toto vylepšení bude k dispozici ve všech veřejných i privátních cloudech.
3.  Tato změna nebude mít vliv na chování některého z následujících možností: 
   - Protokoly prostředků platformy exportované prostřednictvím nastavení diagnostiky
   - Vytváření grafů metrik v Průzkumník metrik
   - Upozornění na metriky platforem
 
## <a name="metrics-exportable-table"></a>Vyexportovaná tabulka s metrikami 

Tabulka obsahuje následující sloupce. 
- Exportovatelné přes nastavení diagnostiky? 
- Ovlivněno hodnotou NULL/0 
- ResourceType 
- Metrika 
- MetricDisplayName
- Jednotka 
- AggregationType


> [!NOTE]
> Následující tabulka může mít v dolní části vodorovný posuvník. Pokud se domníváte, že jste neztratili nějaké informace, zkontrolujte, že posuvník je zcela vlevo.  


| Exportovatelné přes nastavení diagnostiky?  | Již generuje hodnoty NULL. |  ResourceType  |  Metrika  |  MetricDisplayName  |  Jednotka  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CleanerCurrentPrice  |  Paměť: aktuální cena čisticího modulu  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CleanerMemoryNonshrinkable  |  Paměť: nezmenšovaná paměť čisticí paměti  |  Bajty  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CleanerMemoryShrinkable  |  Paměť: velikost čisticí paměti  |  Bajty  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CommandPoolBusyThreads  |  Vlákna: zaneprázdněná vlákna fondu příkazů  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CommandPoolIdleThreads  |  Vlákna: nečinná vlákna fondu příkazů  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CommandPoolJobQueueLength  |  Délka fronty úloh fondu příkazů  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CurrentConnections  |  Připojení: aktuální připojení  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  CurrentUserSessions  |  Aktuální uživatelské relace  |  Count  |  Průměr | 
| Ano * * * *  | No |  Microsoft. AnalysisServices/servery  |  LongParsingBusyThreads  |  Vlákna: zaneprázdněná vlákna s dlouhou analýzou  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  LongParsingIdleThreads  |  Vlákna: nečinná vlákna při dlouhé analýze  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  LongParsingJobQueueLength  |  Vlákna: délka fronty úloh dlouhého rozboru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  mashup_engine_memory_metric  |  Paměť motoru M  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  mashup_engine_private_bytes_metric  |  Počet privátních bajtů modulu M  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  mashup_engine_qpu_metric  |  QPU modulu M  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  mashup_engine_virtual_bytes_metric  |  Virtuální bajty motoru M  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  memory_metric  |  Paměť  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  memory_thrashing_metric  |  Thrashing paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  MemoryLimitHard  |  Paměť: limit paměti – pevný  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  Hodnota memorylimithigh  |  Paměť: limit paměti – vysoká  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  MemoryLimitLow  |  Paměť: limit paměti – nízká  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  MemoryLimitVertiPaq  |  Paměť: limit paměti VertiPaq  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  MemoryUsage  |  Paměť: využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  private_bytes_metric  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolBusyIOJobThreads  |  Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolBusyNonIOThreads  |  Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolIdleIOJobThreads  |  Vlákna: vlákna nečinných úloh v/v fondu zpracování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolIdleNonIOThreads  |  Vlákna: nečinná vlákna v/v fondu zpracování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolIOJobQueueLength  |  Vlákna: délka fronty úloh v/v fondu zpracování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ProcessingPoolJobQueueLength  |  Délka fronty úloh zpracování fondu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  qpu_metric  |  QPU  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  QueryPoolBusyThreads  |  Zaneprázdněná vlákna fondu dotazů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  QueryPoolIdleThreads  |  Vlákna: nečinná vlákna fondu dotazů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  QueryPoolJobQueueLength  |  Vlákna: délka fronty úloh fondu dotazů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  Kvóta  |  Paměť: kvóta  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  QuotaBlocked  |  Paměť: kvóta blokována  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  RowsConvertedPerSec  |  Zpracování: počet převedených řádků za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  RowsReadPerSec  |  Zpracování: Počet přečtených řádků za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  RowsWrittenPerSec  |  Zpracování: počet zapsaných řádků za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ShortParsingBusyThreads  |  Vlákna: krátkodobá analýza zaneprázdněných vláken  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ShortParsingIdleThreads  |  Vlákna: nečinná vlákna krátké analýzy  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  ShortParsingJobQueueLength  |  Vlákna: délka fronty úlohy krátké analýzy  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  SuccessfullConnectionsPerSec  |  Úspěšná připojení za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  TotalConnectionFailures  |  Celkový počet selhání připojení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  TotalConnectionRequests  |  Požadavky na připojení celkem  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  VertiPaqNonpaged  |  Paměť: VertiPaq nestránkované  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  VertiPaqPaged  |  Paměť: VertiPaq stránkované  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AnalysisServices/servery  |  virtual_bytes_metric  |  Virtuální bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Doba trvání požadavků back-endu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft.ApiManagement/service  |  Kapacita  |  Kapacita  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Doba trvání  |  Celková doba trvání žádostí o bránu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Vyřazené události EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Odmítnuté události EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Úspěšné události EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Omezené události EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Vypršel časový limit událostí EventHub.  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Velikost událostí EventHub  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Celkový počet událostí EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Neúspěšné události EventHub  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Neúspěšné požadavky brány (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Jiné požadavky na bránu (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Žádosti  |  Žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Úspěšné požadavky brány (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Požadavky brány celkem (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Neautorizované žádosti o bránu (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  AppCpuUsagePercentage  |  Procento využití procesoru aplikací  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  AppMemoryCommitted  |  Přiřazená paměť aplikace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  AppMemoryMax  |  Maximální velikost paměti aplikace  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  AppMemoryUsed  |  Využitá paměť aplikace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  GCPauseTotalCount  |  Počet pozastavení GC  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  GCPauseTotalTime  |  Celkový čas pozastavení GC  |  Milisekund  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  MaxOldGenMemoryPoolBytes  |  Maximální velikost dostupných starých dat generace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  OldGenMemoryPoolBytes  |  Stará velikost dat generace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  OldGenPromotedBytes  |  Zvýšit úroveň na starou velikost dat generace  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  SystemCpuUsagePercentage  |  Procento využití procesoru v systému  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatErrorCount  |  Globální chyba Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatReceivedBytes  |  Celkový počet přijatých bajtů Tomcat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatRequestMaxTime  |  Maximální čas požadavku Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatRequestTotalCount  |  Celkový počet žádostí Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatRequestTotalTime  |  Celkový počet požadavků Tomcat  |  Milisekund  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatResponseAvgTime  |  Průměrná doba požadavku Tomcat  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSentBytes  |  Celkový počet odeslaných bajtů Tomcat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionActiveCurrentCount  |  Počet otevřených relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionActiveMaxCount  |  Maximální počet aktivních relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionAliveMaxTime  |  Maximální doba běhu relace Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionCreatedCount  |  Počet vytvořených relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionExpiredCount  |  Počet vypršení relace Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  TomcatSessionRejectedCount  |  Počet odmítnutých relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. AppPlatform/pružina  |  YoungGenPromotedBytes  |  Zvýšení úrovně na velikost dat malé generace  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. Automation/automationAccounts  |  TotalJob  |  Celkový počet úloh  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Celkový počet spuštěných počítačů nasazení aktualizace  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Celkový počet spuštění nasazení aktualizací  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Vyhrazený počet jader  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Vytváření počtu uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Počet nečinných uzlů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Události dokončení odstranění úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Události spuštění odstranění úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Úloha zakázat kompletní události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Úloha zakázat počáteční události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Události spuštění úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Ukončit události dokončení úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Události spuštění ukončení úlohy  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Počet ponechávání uzlů fondu  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Počet jader LowPriority  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Počet uzlů v režimu offline  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Vytváření fondů – události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Události dokončení odstranění fondu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Události spuštění odstranění fondu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Události dokončení změny velikosti fondu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Události spuštění změny velikosti fondu  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Počet zrušených uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Restartování počtu uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Počet uzlů obnovování imagí  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Počet spuštěných uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Počáteční počet uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Počet neúspěšných spuštění úlohy – počet uzlů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Události dokončení úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Události neúspěšných úloh  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Události zahájení úlohy  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Počet uzlů s nízkou prioritou  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Počet vyhrazených uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Počet nepoužitelných uzlů  |  Count  |  Celkem | 
| No  | No |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Čekání na počet uzlů spouštěcí úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Aktivní jádra  |  Aktivní jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Aktivní uzly  |  Aktivní uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Jádra nečinných  |  Jádra nečinných  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Nečinné uzly  |  Nečinné uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Dokončená úloha  |  Dokončená úloha  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Úloha odeslána  |  Úloha odeslána  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Vynechávání jader  |  Vynechávání jader  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Ukončení uzlů  |  Ukončení uzlů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Zrušené jádra  |  Zrušené jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Přepnuté uzly  |  Přepnuté uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Procento využití kvóty  |  Procento využití kvóty  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Celkový počet jader  |  Celkový počet jader  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Celkem uzlů  |  Celkem uzlů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Nepoužitelné jádra  |  Nepoužitelné jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.BatchAI/Workspaces  |  Nepoužité uzly  |  Nepoužité uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  ConnectionAccepted  |  Přijatá připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  ConnectionActive  |  Aktivní připojení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  ConnectionHandled  |  Zpracovaná připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Procento využití procesoru  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  IOReadBytes  |  Bajty čtení v/v  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  IOWriteBytes  |  Bajty zápisu v/v  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  MemoryLimit  |  Omezení paměti  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  MemoryUsage  |  Využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Procento využití paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  PendingTransactions  |  Nedokončené transakce  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  ProcessedBlocks  |  Zpracované bloky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  ProcessedTransactions  |  Zpracované transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  QueuedTransactions  |  Transakce ve frontě  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  RequestHandled  |  Zpracované žádosti  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. blockchain/blockchainMembers  |  StorageUsage  |  Využití úložiště  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits  |  Přístupy do mezipaměti  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits0  |  Přístupy do mezipaměti (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits1  |  Přístupy do mezipaměti (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits2  |  Přístupy do mezipaměti (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits3  |  Přístupy do mezipaměti (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits4  |  Přístupy do mezipaměti (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits5  |  Přístupy do mezipaměti (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits6  |  Přístupy do mezipaměti (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits7  |  Přístupy do mezipaměti (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits8  |  Přístupy do mezipaměti (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachehits9  |  Přístupy do mezipaměti (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheLatency  |  Mikrosekundy latence mezipaměti (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses  |  Neúspěšné přístupy do mezipaměti  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses0  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses1  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses2  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses3  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses4  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses5  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses6  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses7  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses8  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cachemisses9  |  Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead  |  Čtení z mezipaměti  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead0  |  Čtení z mezipaměti (horizontálních oddílů 0)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead1  |  Čtení z mezipaměti (horizontálních oddílů 1)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead2  |  Čtení z mezipaměti (horizontálních oddílů 2)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead3  |  Čtení z mezipaměti (horizontálních oddílů 3)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead4  |  Čtení z mezipaměti (horizontálních oddílů 4)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead5  |  Čtení z mezipaměti (horizontálních oddílů 5)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead6  |  Čtení z mezipaměti (horizontálních oddílů 6)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead7  |  Čtení z mezipaměti (horizontálních oddílů 7)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead8  |  Čtení z mezipaměti (horizontálních oddílů 8)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheRead9  |  Čtení z mezipaměti (horizontálních oddílů 9)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite  |  Zápis do mezipaměti  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite0  |  Zápis do mezipaměti (horizontálních oddílů 0)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite1  |  Zápis do mezipaměti (horizontálních oddílů 1)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite2  |  Zápis do mezipaměti (horizontálních oddílů 2)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite3  |  Zápis do mezipaměti (horizontálních oddílů 3)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite4  |  Zápis do mezipaměti (horizontálních oddílů 4)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite5  |  Zápis do mezipaměti (horizontálních oddílů 5)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite6  |  Zápis do mezipaměti (horizontálních oddílů 6)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite7  |  Zápis do mezipaměti (horizontálních oddílů 7)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite8  |  Zápis do mezipaměti (horizontálních oddílů 8)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  cacheWrite9  |  Zápis do mezipaměti (horizontálních oddílů 9)  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients  |  Připojení klienti  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients0  |  Připojení klienti (horizontálních oddílů 0)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients1  |  Připojení klienti (horizontálních oddílů 1)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients2  |  Připojení klienti (horizontálních oddílů 2)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients3  |  Připojení klienti (horizontálních oddílů 3)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients4  |  Připojení klienti (horizontálních oddílů 4)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients5  |  Připojení klienti (horizontálních oddílů 5)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients6  |  Připojení klienti (horizontálních oddílů 6)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients7  |  Připojení klienti (horizontálních oddílů 7)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients8  |  Připojení klienti (horizontálních oddílů 8)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  connectedclients9  |  Připojení klienti (horizontálních oddílů 9)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  chyby  |  Chyby  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys  |  Vyloučené klíče  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys0  |  Vyřazené klíče (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys1  |  Vyřazené klíče (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys2  |  Vyřazené klíče (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys3  |  Vyřazené klíče (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys4  |  Vyřazené klíče (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys5  |  Vyřazení klíčů (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys6  |  Vyřazené klíče (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys7  |  Vyřazení klíčů (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys8  |  Vyřazené klíče (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  evictedkeys9  |  Vyřazené klíče (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys  |  Prošlé klíče  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys0  |  Klíče vypršení platnosti (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys1  |  Klíče vypršení platnosti (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys2  |  Klíče vypršení platnosti (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys3  |  Klíče vypršení platnosti (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys4  |  Klíče vypršení platnosti (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys5  |  Klíče vypršení platnosti (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys6  |  Klíče vypršení platnosti (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys7  |  Klíče vypršení platnosti (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys8  |  Klíče vypršení platnosti (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  expiredkeys9  |  Klíče vypršení platnosti (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  GetCommands  |  Příkazy Get  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands0  |  Načtení (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands1  |  Načtení (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands2  |  Get (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands3  |  Get (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands4  |  Get (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands5  |  Získá (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands6  |  Načtení (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands7  |  Načtení (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands8  |  Získá (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  getcommands9  |  Načtení (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond  |  Operace za sekundu  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond0  |  Operací za sekundu (horizontálních oddílů 0)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond1  |  Operací za sekundu (horizontálních oddílů 1)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond2  |  Operací za sekundu (horizontálních oddílů 2)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond3  |  Operací za sekundu (horizontálních oddílů 3)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond4  |  Operací za sekundu (horizontálních oddílů 4)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond5  |  Operací za sekundu (horizontálních oddílů 5)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond6  |  Operací za sekundu (horizontálních oddílů 6)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond7  |  Operací za sekundu (horizontálních oddílů 7)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond8  |  Operací za sekundu (horizontálních oddílů 8)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  operationsPerSecond9  |  Operací za sekundu (horizontálních oddílů 9)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime  |  Procesor  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime0  |  PROCESOR (horizontálních oddílů 0)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime1  |  PROCESOR (horizontálních oddílů 1)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime2  |  PROCESOR (horizontálních oddílů 2)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime3  |  PROCESOR (horizontálních oddílů 3)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime4  |  PROCESOR (horizontálních oddílů 4)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime5  |  PROCESOR (horizontálních oddílů 5)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime6  |  PROCESOR (horizontálních oddílů 6)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime7  |  PROCESOR (horizontálních oddílů 7)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime8  |  PROCESOR (horizontálních oddílů 8)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  percentProcessorTime9  |  PROCESOR (horizontálních oddílů 9)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad  |  Zatížení serveru  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad0  |  Zatížení serveru (horizontálních oddílů 0)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad1  |  Zatížení serveru (horizontálních oddílů 1)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad2  |  Zatížení serveru (horizontálních oddílů 2)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad3  |  Zatížení serveru (horizontálních oddílů 3)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad4  |  Zatížení serveru (horizontálních oddílů 4)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad5  |  Zatížení serveru (horizontálních oddílů 5)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad6  |  Zatížení serveru (horizontálních oddílů 6)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad7  |  Zatížení serveru (horizontálních oddílů 7)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad8  |  Zatížení serveru (horizontálních oddílů 8)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  serverLoad9  |  Zatížení serveru (horizontálních oddílů 9)  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands  |  Příkazy Set  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands0  |  Sady (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands1  |  Sady (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands2  |  Sady (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands3  |  Sady (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands4  |  Sady (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands5  |  Sady (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands6  |  Sady (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands7  |  Sady (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands8  |  Sady (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  setcommands9  |  Sady (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed  |  Celkový počet operací  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed0  |  Celkem operací (horizontálních oddílů 0)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed1  |  Celkem operací (horizontálních oddílů 1)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed2  |  Celkem operací (horizontálních oddílů 2)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed3  |  Celkem operací (horizontálních oddílů 3)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed4  |  Celkem operací (horizontálních oddílů 4)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed5  |  Celkem operací (horizontálních oddílů 5)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed6  |  Celkem operací (horizontálních oddílů 6)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed7  |  Celkem operací (horizontálních oddílů 7)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed8  |  Celkem operací (horizontálních oddílů 8)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalcommandsprocessed9  |  Celkem operací (horizontálních oddílů 9)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys  |  Celkem klíčů  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys0  |  Celkem klíčů (horizontálních oddílů 0)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys1  |  Celkem klíčů (horizontálních oddílů 1)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys2  |  Celkem klíčů (horizontálních oddílů 2)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys3  |  Celkem klíčů (horizontálních oddílů 3)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys4  |  Celkem klíčů (horizontálních oddílů 4)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys5  |  Celkem klíčů (horizontálních oddílů 5)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys6  |  Celkem klíčů (horizontálních oddílů 6)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys7  |  Celkem klíčů (horizontálních oddílů 7)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys8  |  Celkem klíčů (horizontálních oddílů 8)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  totalkeys9  |  Celkem klíčů (horizontálních oddílů 9)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory  |  Využitá paměť  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory0  |  Využitá paměť (horizontálních oddílů 0)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory1  |  Využitá paměť (horizontálních oddílů 1)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory2  |  Využitá paměť (horizontálních oddílů 2)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory3  |  Využitá paměť (horizontálních oddílů 3)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory4  |  Využitá paměť (horizontálních oddílů 4)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory5  |  Využitá paměť (horizontálních oddílů 5)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory6  |  Využitá paměť (horizontálních oddílů 6)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory7  |  Využitá paměť (horizontálních oddílů 7)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory8  |  Využitá paměť (horizontálních oddílů 8)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemory9  |  Využitá paměť (horizontálních oddílů 9)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemorypercentage  |  Využitá paměť v procentech  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss  |  RSS využité paměti  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss0  |  RSS využité paměti (horizontálních oddílů 0)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss1  |  RSS využité paměti (horizontálních oddílů 1)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss2  |  RSS využité paměti (horizontálních oddílů 2)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss3  |  RSS využité paměti (horizontálních oddílů 3)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss4  |  RSS využité paměti (horizontálních oddílů 4)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss5  |  RSS využité paměti (horizontálních oddílů 5)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss6  |  RSS využité paměti (horizontálních oddílů 6)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss7  |  RSS využité paměti (horizontálních oddílů 7)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss8  |  RSS využité paměti (horizontálních oddílů 8)  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. cache/Redis  |  usedmemoryRss9  |  RSS využité paměti (horizontálních oddílů 9)  |  Bajty  |  Maximum | 
| No  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Bajty čtení z disku/s  |  Čtení z disku  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Bajty zápisu na disk/s  |  Zápis na disk  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicCompute/domainNames/sloty/role  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| No  | No |  Microsoft. ClassicCompute/virtualMachines  |  Bajty čtení z disku/s  |  Čtení z disku  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/virtualMachines  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. ClassicCompute/virtualMachines  |  Bajty zápisu na disk/s  |  Zápis na disk  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicCompute/virtualMachines  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicCompute/virtualMachines  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicCompute/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts  |  UsedCapacity  |  Využitá kapacita  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Kapacita objektu BLOB  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Počet objektů BLOB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Počet kontejnerů objektů BLOB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Kapacita indexu  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  Kapacita zařízení  |  Kapacita souboru  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  FileCount  |  Počet souborů  |  Count  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  FileShareCount  |  Počet sdílených souborů  |  Count  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  FileShareQuota  |  Velikost kvóty sdílení souborů  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  FileShareSnapshotCount  |  Počet snímků sdílené složky  |  Count  |  Průměr | 
| No  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  FileShareSnapshotSize  |  Velikost snímku sdílené složky  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/služby  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Kapacita fronty  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Počet front  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Počet zpráv ve frontě  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Kapacita tabulky  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Počet tabulek  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Počet entit tabulky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  BlockedCalls  |  Blokovaná volání  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  CharactersTrained  |  Vyškolené znaky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  CharactersTranslated  |  Přeložené znaky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  ClientErrors  |  Chyby klienta  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  DataIn  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  Data  |  Výstupní data  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  Latence  |  Latence  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  ServerErrors  |  Chyby serveru  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  SpeechSessionDuration  |  Doba trvání relace řeči  |  Sekundy  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  SuccessfulCalls  |  Úspěšná volání  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  TotalCalls  |  Celkový počet volání  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  TotalErrors  |  Celkový počet chyb  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  TotalTokenCalls  |  Celkový počet volání tokenu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Cognitiveservices Account/účty  |  TotalTransactions  |  Celkový počet transakcí  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Hloubka fronty datového disku  |  Hloubka fronty datových disků (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty přečtené z datového disku za sekundu  |  Bajty přečtené z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace čtení z datového disku za sekundu  |  Operace čtení z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty zapsané na datový disk/s  |  Bajty zapsané na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace zápisu na datový disk/s  |  Operace zápisu na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty čtení z disku  |  Bajty čtení z disku  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty zápisu na disk  |  Bajty zápisu na disk  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Příchozí toky  |  Příchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Maximální rychlost vytváření příchozích toků  |  Míra vytváření maximálního počtu příchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Síťové vstupy  |  Síť v fakturovatelný (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Celková síť  |  Celková síť  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Síťové výstupy  |  Fakturovatelná odchozí síť (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Celkový počet síťových výstupů  |  Celkový počet síťových výstupů  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty přečtené z disku s operačním systémem/s  |  Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace čtení z disku s operačním systémem za sekundu  |  Operace čtení z disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty zápisu na disk s operačním systémem/s  |  Bajty zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk s operačním systémem za sekundu  |  Operace zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operační systém na disk hloubka fronty  |  Disk s operačním systémem hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Přečtené bajty v operačním systému na disk/s  |  Bajty přečtené z disku s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace čtení z operačního systému na disk/s  |  Operace čtení z disku s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty zapsané v operačním systému na disk/s  |  Bajty zápisu na disk s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace zápisu v operačním systému na disk/s  |  Operace zápisu na disk s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Odchozí toky  |  Odchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Maximální rychlost vytváření odchozích toků  |  Frekvence maximálního vytváření odchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Hloubka fronty na disk  |  Datový disk hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Přečtené bajty podle disku/s  |  Bajty přečtené z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace čtení na disk/s  |  Operace čtení z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Bajty zápisu na disk/s  |  Bajty zapsané na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Neúspěšné čtení mezipaměti datových disků Premium  |  Neúspěšné čtení mezipaměti datových disků Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Počet přístupů do mezipaměti disku s operačním systémem Premium  |  Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachines  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Hloubka fronty datového disku  |  Hloubka fronty datových disků (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty přečtené z datového disku za sekundu  |  Bajty přečtené z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení z datového disku za sekundu  |  Operace čtení z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapsané na datový disk/s  |  Bajty zapsané na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na datový disk/s  |  Operace zápisu na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty čtení z disku  |  Bajty čtení z disku  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu na disk  |  Bajty zápisu na disk  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Příchozí toky  |  Příchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Maximální rychlost vytváření příchozích toků  |  Míra vytváření maximálního počtu příchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Síťové vstupy  |  Síť v fakturovatelný (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Celková síť  |  Celková síť  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Síťové výstupy  |  Fakturovatelná odchozí síť (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Celkový počet síťových výstupů  |  Celkový počet síťových výstupů  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty přečtené z disku s operačním systémem/s  |  Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení z disku s operačním systémem za sekundu  |  Operace čtení z disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu na disk s operačním systémem/s  |  Bajty zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk s operačním systémem za sekundu  |  Operace zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operační systém na disk hloubka fronty  |  Disk s operačním systémem hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Přečtené bajty v operačním systému na disk/s  |  Bajty přečtené z disku s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení z operačního systému na disk/s  |  Operace čtení z disku s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapsané v operačním systému na disk/s  |  Bajty zápisu na disk s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu v operačním systému na disk/s  |  Operace zápisu na disk s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Odchozí toky  |  Odchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Maximální rychlost vytváření odchozích toků  |  Frekvence maximálního vytváření odchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Hloubka fronty na disk  |  Datový disk hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Přečtené bajty podle disku/s  |  Bajty přečtené z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení na disk/s  |  Operace čtení z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu na disk/s  |  Bajty zapsané na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk/s  |  Operace zápisu na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Neúspěšné čtení mezipaměti datových disků Premium  |  Neúspěšné čtení mezipaměti datových disků Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Počet přístupů do mezipaměti disku s operačním systémem Premium  |  Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft.Compute/virtualMachineScaleSets  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Hloubka fronty datového disku  |  Hloubka fronty datových disků (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty přečtené z datového disku za sekundu  |  Bajty přečtené z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace čtení z datového disku za sekundu  |  Operace čtení z datového disku za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapsané na datový disk/s  |  Bajty zapsané na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na datový disk/s  |  Operace zápisu na datový disk za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty čtení z disku  |  Bajty čtení z disku  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu na disk  |  Bajty zápisu na disk  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Příchozí toky  |  Příchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Maximální rychlost vytváření příchozích toků  |  Míra vytváření maximálního počtu příchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Síťové vstupy  |  Síť v fakturovatelný (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Celková síť  |  Celková síť  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Síťové výstupy  |  Fakturovatelná odchozí síť (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Celkový počet síťových výstupů  |  Celkový počet síťových výstupů  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (Preview)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty přečtené z disku s operačním systémem/s  |  Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace čtení z disku s operačním systémem za sekundu  |  Operace čtení z disku s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu na disk s operačním systémem/s  |  Bajty zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk s operačním systémem za sekundu  |  Operace zápisu na disk s operačním systémem za sekundu (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operační systém na disk hloubka fronty  |  Disk s operačním systémem hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Přečtené bajty v operačním systému na disk/s  |  Bajty přečtené z disku s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace čtení z operačního systému na disk/s  |  Operace čtení z disku s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapsané v operačním systému na disk/s  |  Bajty zápisu na disk s operačním systémem/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace zápisu v operačním systému na disk/s  |  Operace zápisu na disk s operačním systémem za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Odchozí toky  |  Odchozí toky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Maximální rychlost vytváření odchozích toků  |  Frekvence maximálního vytváření odchozích toků (Preview)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Hloubka fronty na disk  |  Datový disk hloubka fronty (zastaralé)  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Přečtené bajty podle disku/s  |  Bajty přečtené z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace čtení na disk/s  |  Operace čtení z datového disku za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu na disk/s  |  Bajty zapsané na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na datový disk za sekundu (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium  |  Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Neúspěšné čtení mezipaměti datových disků Premium  |  Neúspěšné čtení mezipaměti datových disků Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Počet přístupů do mezipaměti disku s operačním systémem Premium  |  Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium  |  Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerInstance/containerGroups  |  CpuUsage  |  Využití procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerInstance/containerGroups  |  MemoryUsage  |  Využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Počet přijatých bajtů sítě za sekundu  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bajty přenášené přes síť za sekundu  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerRegistry/Registry  |  RunDuration  |  Doba trvání běhu  |  Milisekund  |  Celkem | 
| **Ano**  | No |  Microsoft. ContainerRegistry/Registry  |  SuccessfulPullCount  |  Počet úspěšných vyžádané replikace  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerRegistry/Registry  |  SuccessfulPushCount  |  Počet úspěšných vložení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerRegistry/Registry  |  TotalPullCount  |  Celkový počet vyžádané replikace  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. ContainerRegistry/Registry  |  TotalPushCount  |  Celkový počet nabízených oznámení  |  Count  |  Průměr | 
| No  | No |  Microsoft. ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Celkový počet dostupných jader procesoru ve spravovaném clusteru  |  Count  |  Průměr | 
| No  | No |  Microsoft. ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Celková velikost dostupné paměti ve spravovaném clusteru  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ContainerService/managedClusters  |  kube_node_status_condition  |  Stavy pro různé podmínky uzlu  |  Count  |  Průměr | 
| No  | No |  Microsoft. ContainerService/managedClusters  |  kube_pod_status_phase  |  Počet lusků podle fáze  |  Count  |  Průměr | 
| No  | No |  Microsoft. ContainerService/managedClusters  |  kube_pod_status_ready  |  Počet lusků ve stavu připraveno  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Availablecapacity;)  |  Dostupná kapacita  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Odeslané bajty v cloudu (zařízení)  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Odeslané bajty v cloudu (sdílená složka)  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Propustnost stahování v cloudu  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Propustnost stahování do cloudu (sdílení)  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Propustnost nahrávání do cloudu  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Propustnost nahrávání do cloudu (sdílení)  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Výpočet využití paměti na hraničních zařízeních  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Výpočetní prostředí Edge – procento využití procesoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Propustnost čtení (síť)  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Propustnost zápisu (síť)  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Úložiště  |  Celková kapacita  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DataFactory/DataFactory  |  FailedRuns  |  Neúspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/DataFactory  |  SuccessfulRuns  |  Úspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  ActivityCancelledRuns  |  Zrušené metriky spuštění aktivit  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  ActivityFailedRuns  |  Neúspěšná aktivita spustí metriky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  ActivitySucceededRuns  |  Úspěšná aktivita spustí metriky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  FactorySizeInGbUnits  |  Celková velikost továrny (jednotka GB)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  IntegrationRuntimeAvailableMemory  |  Dostupná paměť modulu runtime integrace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  IntegrationRuntimeAverageTaskPickupDelay  |  Doba trvání fronty prostředí Integration runtime  |  Sekundy  |  Průměr | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  IntegrationRuntimeCpuPercentage  |  Využití procesoru prostředí Integration runtime  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  IntegrationRuntimeQueueLength  |  Délka fronty prostředí Integration runtime  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  MaxAllowedFactorySizeInGbUnits  |  Maximální povolená velikost továrny (jednotka GB)  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  MaxAllowedResourceCount  |  Maximální počet povolených entit  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  PipelineCancelledRuns  |  Zrušené metriky spuštění kanálu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  PipelineFailedRuns  |  Neúspěšná metrika spuštění kanálu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  PipelineSucceededRuns  |  Úspěšné metriky spuštění kanálu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  ResourceCount  |  Celkový počet entit  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  TriggerCancelledRuns  |  Zrušené aktivační události spustí metriky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  TriggerFailedRuns  |  Neúspěšná aktivační událost spustí metriky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataFactory/továrny  |  TriggerSucceededRuns  |  Úspěšná aktivační událost spustí metriky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobAUEndedCancelled  |  Čas AU se zrušil.  |  Sekundy  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobAUEndedFailure  |  Neúspěšná doba aktualizace AU  |  Sekundy  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobAUEndedSuccess  |  Úspěšná doba AU  |  Sekundy  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobEndedCancelled  |  Zrušené úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobEndedFailure  |  Neúspěšné úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeAnalytics/účty  |  JobEndedSuccess  |  Úspěšné úlohy  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeStore/účty  |  Čtení z  |  Přečtená data  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeStore/účty  |  Napsáno  |  Zapsaná data  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeStore/účty  |  ReadRequests  |  Žádosti o čtení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DataLakeStore/účty  |  TotalStorage  |  Celkové úložiště  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. DataLakeStore/účty  |  WriteRequests  |  Požadavky na zápis  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  active_connections  |  Aktivní připojení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  backup_storage_used  |  Využité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  connections_failed  |  Neúspěšná připojení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  cpu_percent  |  Procento využití procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  io_consumption_percent  |  V/v procenta  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  memory_percent  |  Procentuální hodnota paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  seconds_behind_master  |  Prodleva replikace v sekundách  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  serverlog_storage_percent  |  Procentuální hodnota úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  serverlog_storage_usage  |  Využité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  storage_limit  |  Omezení úložiště  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMariaDB/servery  |  storage_used  |  Využité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  active_connections  |  Aktivní připojení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  backup_storage_used  |  Využité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  connections_failed  |  Neúspěšná připojení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  cpu_percent  |  Procento využití procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  io_consumption_percent  |  V/v procenta  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  memory_percent  |  Procentuální hodnota paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  seconds_behind_master  |  Prodleva replikace v sekundách  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  serverlog_storage_percent  |  Procentuální hodnota úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  serverlog_storage_usage  |  Využité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  storage_limit  |  Omezení úložiště  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. DBforMySQL/servery  |  storage_used  |  Využité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  active_connections  |  Aktivní připojení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  backup_storage_used  |  Využité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  connections_failed  |  Neúspěšná připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  cpu_percent  |  Procento využití procesoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  io_consumption_percent  |  V/v procenta  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  memory_percent  |  Procentuální hodnota paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  pg_replica_log_delay_in_bytes  |  Maximální prodleva napříč replikami  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  pg_replica_log_delay_in_seconds  |  Prodleva repliky  |  Sekundy  |  Maximum | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  serverlog_storage_percent  |  Procentuální hodnota úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  serverlog_storage_usage  |  Využité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  storage_limit  |  Omezení úložiště  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/servery  |  storage_used  |  Využité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  active_connections  |  Aktivní připojení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  cpu_percent  |  Procento využití procesoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  IOPS  |  IOPS  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  memory_percent  |  Procentuální hodnota paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. DBforPostgreSQL/serversv2  |  storage_used  |  Využité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. zařízení/účet  |  digitaltwins. telemetrie. Nodes  |  Zástupný text telemetrie uzlu digitální vlákna  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Commands. odchozí. Abandon. Success  |  Zrušené zprávy C2D  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Commands.. Complete. Complete. Success  |  Doručení zpráv C2D bylo dokončeno.  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Commands. odchozí. remítat. Success  |  Odmítnuté zprávy C2D  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Methods. Failure  |  Neúspěšná volání přímé metody  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Methods. requestSize  |  Velikost žádosti o vyvolání přímé metody  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Methods. responseSize  |  Velikost odezvy volání přímých metod  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. Methods. Success  |  Úspěšná volání přímé metody  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. nevlákenný. Read. Failure  |  Neúspěšné čtení z back-endu ze zadních vláken  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. nevlákenný. Read. Size  |  Velikost odpovědi zdvojeného čtení z back-endu  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. vláken. Read. Success  |  Úspěšné zdvojené čtení z back-endu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. nevlákenná. aktualizace. selhání  |  Neúspěšné zdvojené aktualizace z back-endu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. nevlákenná. Update. Size  |  Velikost dvojitě aktualizovaných aktualizací z back-endu  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2D. nevlákenná. Update. Success  |  Úspěšné zdvojené aktualizace z back-endu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  C2DMessagesExpired  |  C2D zprávy prošly (Preview)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  konfiguračních  |  Metriky konfigurace  |  Count  |  Celkem | 
| No  | No |  Microsoft. Devices/IotHubs  |  connectedDeviceCount  |  Připojená zařízení (Preview)  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. invýstups. builtIns. events  |  Směrování: zprávy doručené zprávám/událostem  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. odchozí. eventHubs  |  Směrování: zprávy doručené do centra událostí  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. odchozí. serviceBusQueues  |  Směrování: zprávy doručené do fronty Service Bus  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. odchozí. serviceBusTopics  |  Směrování: zprávy doručené do Service Bus tématu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. odchozí úložiště. Storage  |  Směrování: zprávy doručené do úložiště  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. výstup. Storage. BLOBs  |  Směrování: objekty blob doručené do úložiště  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. invýstups. Storage. bytes  |  Směrování: data Doručená do úložiště  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. latence. builtIn. events  |  Směrování: latence zpráv pro zprávy/události  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. latence. eventHubs  |  Směrování: latence zprávy pro centrum událostí  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. latence. serviceBusQueues  |  Směrování: latence zprávy pro Service Bus frontu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. latence. serviceBusTopics  |  Směrování: latence zprávy pro Service Bus téma  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. Endpoints. latence. Storage  |  Směrování: latence zpráv pro úložiště  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. výstup. vyřazeno  |  Směrování: vyřazené zprávy telemetrie   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. odchozí. Fallback  |  Směrování: zprávy doručené do záložního režimu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. invýstup. neplatné  |  Směrování: nekompatibilní zprávy telemetrie  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. výstup. osamocený  |  Směrování: osamocené zprávy telemetrie   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. odchozí. úspěch  |  Směrování: doručené zprávy telemetrie  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. příchozí přenos dat allProtocol  |  Počet pokusů o odeslání zprávy telemetrie  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. příchozí přenos dat sendThrottle  |  Počet chyb omezování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. telemetrie. příchozí přenos dat. úspěch  |  Odeslané zprávy telemetrie  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. nevlákenný. Read. Failure  |  Neúspěšná čtení ze zařízení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. nevlákenný. Read. Size  |  Velikost odpovědi u dvojitých čtení ze zařízení  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. vláken. Read. Success  |  Úspěšné čtení ze zařízení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. nevlákenná. aktualizace. selhání  |  Neúspěšné aktualizace ze zařízení se zdvojenými chybami  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. nevlákenná. Update. Size  |  Velikost dvojitě aktualizovaných aktualizací ze zařízení  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  D2C. nevlákenná. Update. Success  |  Úspěšné nedokončené změny ze zařízení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  dailyMessageQuotaUsed  |  Celkový počet použitých zpráv  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  deviceDataUsage  |  Celkové využití dat zařízení  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  deviceDataUsageV2  |  Celkové využití dat zařízení (Preview)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Devices. connectedDevices. allProtocol  |  Připojená zařízení (zastaralé)   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Devices. totalDevices  |  Celkem zařízení (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  EventGridDeliveries  |  Event Grid doručení (Preview)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  EventGridLatency  |  Latence Event Grid (Preview)  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. cancelJob. selhání  |  Neúspěšná zrušení úloh  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. cancelJob. Success  |  Úspěšná zrušení úlohy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  dokončené úlohy  |  Dokončené úlohy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. createDirectMethodJob. selhání  |  Nepovedlo se vytvořit úlohy vyvolání metody  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. createDirectMethodJob. Success  |  Úspěšné vytváření úloh vyvolání metod  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. createTwinUpdateJob. selhání  |  Nepovedlo se vytvořit úlohy s dvojitou aktualizací  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. createTwinUpdateJob. Success  |  Úspěšné vytváření zdvojených úloh aktualizace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  úlohy. nezdařilo se  |  Neúspěšné úlohy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. listJobs. selhání  |  Neúspěšná volání pro výpis úloh  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. listJobs. Success  |  Úspěšná volání na seznam úloh  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. queryJobs. selhání  |  Neúspěšné dotazy na úlohy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  Jobs. queryJobs. Success  |  Úspěšné dotazy na úlohy  |  Count  |  Celkem | 
| No  | No |  Microsoft. Devices/IotHubs  |  totalDeviceCount  |  Celkem zařízení (Preview)  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  twinQueries. selhání  |  Neúspěšné zdvojené dotazy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  twinQueries.resultSize  |  Velikost výsledku nevlákenných dotazů  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Devices/IotHubs  |  twinQueries. Success  |  Úspěšné zdvojené dotazy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/provisioningServices  |  AttestationAttempts  |  Pokusy o ověření identity  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/provisioningServices  |  DeviceAssignments  |  Přiřazená zařízení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Devices/provisioningServices  |  RegistrationAttempts  |  Pokusy o registraci  |  Count  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Dostupné úložiště  |  Bajty  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Ukončení připojení Cassandra  |  Count  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Poplatky za žádosti Cassandra  |  Count  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Žádosti Cassandra  |  Count  |  Count | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  Využití datausage  |  Využití dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Počet dokumentů  |  Count  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Kvóta dokumentu  |  Bajty  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Využití indexu  |  Bajty  |  Celkem | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Žádosti o metadata  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Poplatek za požadavek Mongo  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Žádosti Mongo  |  Count  |  Count | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Počet požadavků Mongo  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Frekvence žádosti o odstranění Mongo  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Frekvence požadavků na vložení Mongo  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Frekvence požadavků na dotaz Mongo  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Frekvence požadavků na aktualizace Mongo  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Zřízená propustnost  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Latence replikace p99  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Dostupnost služby  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Požadavky celkem  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Celkový počet jednotek žádostí  |  Count  |  Celkem | 
| No  | No |  Microsoft. EnterpriseKnowledgeGraph/Services  |  FailureCount  |  Počet selhání  |  Count  |  Count | 
| No  | No |  Microsoft. EnterpriseKnowledgeGraph/Services  |  SuccessCount  |  Počet úspěchů  |  Count  |  Count | 
| No  | No |  Microsoft. EnterpriseKnowledgeGraph/Services  |  SuccessLatency  |  Latence úspěchu  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. EnterpriseKnowledgeGraph/Services  |  TransactionCount  |  Počet transakcí  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  DeadLetteredCount  |  Nedoručené události s písmeny  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventGrid/domény  |  DeliveryAttemptFailCount  |  Doručení – neúspěšné události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  DeliverySuccessCount  |  Doručené události  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventGrid/domény  |  DestinationProcessingDurationInMs  |  Doba trvání zpracování cíle  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  DroppedEventCount  |  Vyřazené události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  MatchedEventCount  |  Spárované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  PublishFailCount  |  Publikovat neúspěšné události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  PublishSuccessCount  |  Publikované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/domény  |  PublishSuccessLatencyInMs  |  Latence úspěšného publikování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Nedoručené události s písmeny  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Doručení – neúspěšné události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Doručené události  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Doba trvání zpracování cíle  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/eventSubscriptions  |  DroppedEventCount  |  Vyřazené události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/eventSubscriptions  |  MatchedEventCount  |  Spárované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/extensionTopics  |  PublishFailCount  |  Publikovat neúspěšné události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Publikované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Latence úspěšného publikování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Nespárované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/témata  |  PublishFailCount  |  Publikovat neúspěšné události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/témata  |  PublishSuccessCount  |  Publikované události  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/témata  |  PublishSuccessLatencyInMs  |  Latence úspěšného publikování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventGrid/témata  |  UnmatchedEventCount  |  Nespárované události  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  ActiveConnections  |  ActiveConnections  |  Count  |  Průměr | 
| No  | No |  Microsoft. EventHub/clustery  |  AvailableMemory  |  Paměť k dispozici  |  Procento  |  Maximum | 
| No  | No |  Microsoft. EventHub/clustery  |  CaptureBacklog  |  Zachyťte nevyřízené položky.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  CapturedBytes  |  Zachycené bajty.  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  CapturedMessages  |  Zachycené zprávy.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  ConnectionsClosed  |  Zavřená připojení.  |  Count  |  Průměr | 
| No  | No |  Microsoft. EventHub/clustery  |  ConnectionsOpened  |  Otevřená připojení.  |  Count  |  Průměr | 
| No  | No |  Microsoft. EventHub/clustery  |  Procesor  |  Procesor  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. EventHub/clustery  |  IncomingBytes  |  Příchozí bajty  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/clustery  |  IncomingMessages  |  Příchozí zprávy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/clustery  |  IncomingRequests  |  Příchozí žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/clustery  |  OutgoingBytes  |  Odchozí bajty  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/clustery  |  OutgoingMessages  |  Odchozí zprávy  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  QuotaExceededErrors  |  Chyby překročení kvóty.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  ServerErrors  |  Chyby serveru.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  SuccessfulRequests  |  Úspěšné požadavky  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  ThrottledRequests  |  Omezené požadavky.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/clustery  |  UserErrors  |  Chyby uživatele.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  ActiveConnections  |  ActiveConnections  |  Count  |  Průměr | 
| No  | No |  Microsoft. EventHub/obory názvů  |  CaptureBacklog  |  Zachyťte nevyřízené položky.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  CapturedBytes  |  Zachycené bajty.  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  CapturedMessages  |  Zachycené zprávy.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  ConnectionsClosed  |  Zavřená připojení.  |  Count  |  Průměr | 
| No  | No |  Microsoft. EventHub/obory názvů  |  ConnectionsOpened  |  Otevřená připojení.  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHABL  |  Archivovat nevyřízené zprávy (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHAMBS  |  Propustnost zpráv archivu (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHAMSGS  |  Archivní zprávy (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHINBYTES  |  Příchozí bajty (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHINMBS  |  Příchozí bajty (zastaralé) (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHINMSGS  |  Příchozí zprávy (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHOUTBYTES  |  Odchozí bajty (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHOUTMBS  |  Odchozí bajty (zastaralé) (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  EHOUTMSGS  |  Odchozí zprávy (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  FAILREQ  |  Neúspěšné žádosti (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  IncomingBytes  |  Příchozí bajty  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  IncomingMessages  |  Příchozí zprávy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  IncomingRequests  |  Příchozí žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  INMSGS  |  Příchozí zprávy (zastaralé) (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  INREQS  |  Příchozí požadavky (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  MEZI sebou  |  Interní chyby serveru (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  MISCERR  |  Další chyby (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  OutgoingBytes  |  Odchozí bajty  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  OutgoingMessages  |  Odchozí zprávy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  OUTMSGS  |  Odchozí zprávy (zastaralé) (zastaralé)  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  QuotaExceededErrors  |  Chyby překročení kvóty.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  ServerErrors  |  Chyby serveru.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  Velikost  |  Velikost  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. EventHub/obory názvů  |  SuccessfulRequests  |  Úspěšné požadavky  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  SUCCREQ  |  Úspěšné požadavky (zastaralé)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. EventHub/obory názvů  |  SVRBSY  |  Chyby zaneprázdněnosti serveru (zastaralé)  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  ThrottledRequests  |  Omezené požadavky.  |  Count  |  Celkem | 
| No  | No |  Microsoft. EventHub/obory názvů  |  UserErrors  |  Chyby uživatele.  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. HDInsight/clustery  |  CategorizedGatewayRequests  |  Zařadit požadavky na bránu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. HDInsight/clustery  |  GatewayRequests  |  Žádosti o bránu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. HDInsight/clustery  |  NumActiveWorkers  |  Počet aktivních pracovníků  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. HDInsight/clustery  |  ScalingRequests  |  Požadavky na škálování  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. Insights/AutoscaleSettings  |  MetricThreshold  |  Prahová hodnota metriky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/AutoscaleSettings  |  ObservedCapacity  |  Zjištěná kapacita  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/AutoscaleSettings  |  ObservedMetricValue  |  Zaznamenaná hodnota metriky  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Zahájené akce škálování  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Insights/Components  |  availabilityResults/availabilityPercentage  |  Dostupnost  |  Procento  |  Průměr | 
| No  | No |  Microsoft. Insights/Components  |  availabilityResults/Count  |  Testy dostupnosti  |  Count  |  Count | 
| **Ano**  | No |  Microsoft. Insights/Components  |  availabilityResults/doba trvání  |  Doba trvání testu dostupnosti  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  browserTimings/networkDuration  |  Doba připojení k síti – načtení stránky  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  browserTimings/processingDuration  |  Doba zpracování klienta  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  browserTimings/receiveDuration  |  Doba přijetí odezvy  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  browserTimings/sendDuration  |  Čas požadavku na odeslání  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  browserTimings/totalDuration  |  Doba načítání stránky v prohlížeči  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Insights/Components  |  závislosti/počet  |  Volání závislostí  |  Count  |  Count | 
| **Ano**  | No |  Microsoft. Insights/Components  |  závislosti/doba trvání  |  Doba trvání závislosti  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Insights/Components  |  závislosti/selhání  |  Selhání volání závislostí  |  Count  |  Count | 
| No  | No |  Microsoft. Insights/Components  |  výjimky/prohlížeč  |  Výjimky prohlížečů  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  výjimky/počet  |  Výjimky  |  Count  |  Count | 
| No  | No |  Microsoft. Insights/Components  |  výjimky/Server  |  Výjimky serveru  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  pageViews/Count  |  Zobrazení stránek  |  Count  |  Count | 
| **Ano**  | No |  Microsoft. Insights/Components  |  pageViews/doba trvání  |  Doba načítání zobrazení stránky  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  Čítače výkonu/exceptionsPerSecond  |  Míra výjimek  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/memoryAvailableBytes  |  Dostupná paměť  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/processCpuPercentage  |  PROCESOR procesů  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/processIOBytesPerSecond  |  Rychlost zpracování v/v  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/processorCpuPercentage  |  Čas procesoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/processPrivateBytes  |  Nesdílené bajty procesu  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Insights/Components  |  Čítače výkonu/requestExecutionTime  |  Doba provádění požadavku HTTP  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  Čítače výkonu/requestsInQueue  |  Požadavky HTTP ve frontě aplikací  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  Čítače výkonu/requestsPerSecond  |  Rychlost požadavku HTTP  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Insights/Components  |  požadavky/počet  |  Žádosti serveru  |  Count  |  Count | 
| **Ano**  | No |  Microsoft. Insights/Components  |  žádosti/doba trvání  |  Doba odezvy serveru  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Insights/Components  |  požadavky/selhání  |  Neúspěšné požadavky  |  Count  |  Count | 
| No  | No |  Microsoft. Insights/Components  |  žádosti/rychlost  |  Počet požadavků serveru  |  CountPerSecond  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Insights/Components  |  trasování/počet  |  Trasování  |  Count  |  Count | 
| **Ano**  | No |  Trezory a trezory Microsoft.  |  ServiceApiHit  |  Celkový počet přístupů k rozhraní API služby  |  Count  |  Count | 
| **Ano**  | No |  Trezory a trezory Microsoft.  |  ServiceApiLatency  |  Celková latence rozhraní API služby  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Trezory a trezory Microsoft.  |  ServiceApiResult  |  Celkový počet výsledků rozhraní API služby  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  CacheUtilization  |  Využití mezipaměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  ContinuousExportMaxLatenessMinutes  |  Maximální počet minut zpoždění pro průběžný export  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  ContinuousExportNumOfRecordsExported  |  Průběžný export – počet exportovaných záznamů  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  ContinuousExportPendingCount  |  Počet nevyřízených položek průběžného exportu  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  ContinuousExportResult  |  Výsledek průběžného exportu  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  Procesor  |  Procesor  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  EventsProcessedForEventHubs  |  Zpracované události (pro Event/centra IoT)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  ExportUtilization  |  Využití exportu  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  IngestionLatencyInSeconds  |  Latence příjmu (v sekundách)  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  IngestionResult  |  Výsledek ingestování  |  Count  |  Count | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  IngestionUtilization  |  Využití příjmu  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  IngestionVolumeInMB  |  Objem příjmu (v MB)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  Udržení  |  Zachovat naživu  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  QueryDuration  |  Doba trvání dotazu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  SteamingIngestRequestRate  |  Rychlost přijímání požadavků pro streamování  |  Count  |  RateRequestsPerSecond | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  StreamingIngestDataRate  |  Přenosová rychlost ingestování datových proudů  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  StreamingIngestDuration  |  Doba ingestování streamování  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Kusto/clustery  |  StreamingIngestResults  |  Výsledek ingestování streamování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  ActionLatency  |  Latence akcí   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Dokončené akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsFailed  |  Neúspěšné akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Vynechané akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsStarted  |  Spuštěné akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Úspěšné akce   |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latence úspěšných akcí   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Omezené události akcí  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Využití paměti konektoru pro prostředí integrační služby  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Využití procesoru konektoru pro prostředí integrační služby  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Využití paměti workflowu pro prostředí integrační služby  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Využití procesoru pracovního postupu pro prostředí integrační služby  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Procento selhání spuštění  |  Procento  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  RunLatency  |  Latence spuštění  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCancelled  |  Zrušené běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCompleted  |  Dokončené běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsFailed  |  Neúspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsStarted  |  Spuštěné běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Úspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Spustit omezené události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Latence úspěšného spuštění  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Události omezeného spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Latence požáru triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerLatency  |  Latence triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Aktivační události dokončeny   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFailed  |  Neúspěšná triggery   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFired  |  Aktivační události aktivovány   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Aktivační události přeskočeny  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersStarted  |  Spuštěné aktivační události   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Aktivační události byly úspěšné   |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latence úspěšnosti triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Omezené události triggeru  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  ActionLatency  |  Latence akcí   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionsCompleted  |  Dokončené akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionsFailed  |  Neúspěšné akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionsSkipped  |  Vynechané akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionsStarted  |  Spuštěné akce   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionsSucceeded  |  Úspěšné akce   |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  ActionSuccessLatency  |  Latence úspěšných akcí   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  ActionThrottledEvents  |  Omezené události akcí  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillableActionExecutions  |  Fakturovatelné provádění akcí  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillableTriggerExecutions  |  Fakturovatelná spuštění triggerů  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageNativeOperation  |  Využití fakturace pro provádění nativních operací  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageNativeOperation  |  Využití fakturace pro provádění nativních operací  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageStandardConnector  |  Využití fakturace pro spuštění standardních konektorů  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageStandardConnector  |  Využití fakturace pro spuštění standardních konektorů  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageStorageConsumption  |  Využití fakturace pro provádění spotřeby úložiště  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  BillingUsageStorageConsumption  |  Využití fakturace pro provádění spotřeby úložiště  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunFailurePercentage  |  Procento selhání spuštění  |  Procento  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  RunLatency  |  Latence spuštění  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunsCancelled  |  Zrušené běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunsCompleted  |  Dokončené běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunsFailed  |  Neúspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunsStarted  |  Spuštěné běhy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunsSucceeded  |  Úspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunStartThrottledEvents  |  Spustit omezené události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  RunSuccessLatency  |  Latence úspěšného spuštění  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  RunThrottledEvents  |  Události omezeného spuštění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TotalBillableExecutions  |  Fakturovatelná spuštění celkem  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  TriggerFireLatency  |  Latence požáru triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  TriggerLatency  |  Latence triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersCompleted  |  Aktivační události dokončeny   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersFailed  |  Neúspěšná triggery   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersFired  |  Aktivační události aktivovány   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersSkipped  |  Aktivační události přeskočeny  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersStarted  |  Spuštěné aktivační události   |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggersSucceeded  |  Aktivační události byly úspěšné   |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Logic/Workflows  |  TriggerSuccessLatency  |  Latence úspěšnosti triggeru   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Logic/Workflows  |  TriggerThrottledEvents  |  Omezené události triggeru  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Aktivní jádra  |  Aktivní jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Aktivní uzly  |  Aktivní uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Dokončená spuštění  |  Dokončená spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Neúspěšná spuštění  |  Neúspěšná spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Jádra nečinných  |  Jádra nečinných  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nečinné uzly  |  Nečinné uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Vynechávání jader  |  Vynechávání jader  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Ukončení uzlů  |  Ukončení uzlů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nasazení modelu se nezdařilo  |  Nasazení modelu se nezdařilo  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nasazení modelu spuštěn  |  Nasazení modelu spuštěn  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nasazení modelu bylo úspěšné.  |  Nasazení modelu bylo úspěšné.  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nepovedlo se zaregistrovat model  |  Nepovedlo se zaregistrovat model  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Registr modelu byl úspěšný.  |  Registr modelu byl úspěšný.  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Zrušené jádra  |  Zrušené jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Přepnuté uzly  |  Přepnuté uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Procento využití kvóty  |  Procento využití kvóty  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Spuštěná spuštění  |  Spuštěná spuštění  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Celkový počet jader  |  Celkový počet jader  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Celkem uzlů  |  Celkem uzlů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nepoužitelné jádra  |  Nepoužitelné jádra  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. MachineLearningServices/pracovní prostory  |  Nepoužité uzly  |  Nepoužité uzly  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Maps/Accounts  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| No  | No |  Microsoft. Maps/Accounts  |  Využití  |  Využití  |  Count  |  Count | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  AssetCount  |  Počet assetů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  AssetQuota  |  Kvóta prostředků  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  AssetQuotaUsedPercentage  |  Procento využité kvóty prostředků  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  ContentKeyPolicyCount  |  Počet zásad klíče obsahu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuota  |  Kvóta zásad pro klíč obsahu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuotaUsedPercentage  |  Procento využité kvóty zásad klíčů obsahu  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  StreamingPolicyCount  |  Počet zásad streamování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  StreamingPolicyQuota  |  Kvóta zásad streamování  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices  |  StreamingPolicyQuotaUsedPercentage  |  Procento využité kvóty zásad streamování  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Media/MediaServices/starají  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Media/MediaServices/starají  |  Žádosti  |  Žádosti  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Media/MediaServices/starají  |  SuccessE2ELatency  |  Koncová latence úspěch  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Počet pozastavení GC  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Celkový čas pozastavení GC  |  Milisekund  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maximální velikost dostupných starých dat generace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Stará velikost dat generace  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Zvýšit úroveň na starou velikost dat generace  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procento využití procesoru služby  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Přiřazená paměť služby  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maximální velikost paměti služby  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Využitá paměť služby  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Procento využití procesoru v systému  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Globální chyba Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Celkový počet přijatých bajtů Tomcat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maximální čas požadavku Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Celkový počet žádostí Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Celkový počet požadavků Tomcat  |  Milisekund  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Průměrná doba požadavku Tomcat  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Celkový počet odeslaných bajtů Tomcat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Počet otevřených relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Maximální počet aktivních relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Maximální doba běhu relace Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Počet vytvořených relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Počet vypršení relace Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Počet odmítnutých relací Tomcat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Zvýšení úrovně na velikost dat malé generace  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Využití přiděleného fondu svazků  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Celková logická velikost fondu svazků  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  AverageReadLatency  |  Průměrná latence čtení  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  AverageWriteLatency  |  Průměrná latence zápisu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  ReadIops  |  Čtení IOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  VolumeLogicalSize  |  Logická velikost svazku  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  VolumeSnapshotSize  |  Velikost snímku svazku  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. NetApp/netAppAccounts/capacityPools/svazky  |  WriteIops  |  Zápis IOPS  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Čas Application Gateway celkem  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Počet požadavků za minutu na hostitele v pořádku  |  Count  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  BackendConnectTime  |  Čas připojení back-endu  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  BackendFirstByteResponseTime  |  Doba odezvy prvního bajtu back-endu  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  BackendLastByteResponseTime  |  Doba odezvy posledního bajtu back-endu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  BackendResponseStatus  |  Stav odpovědi back-endu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  BlockedCount  |  Distribuce pravidla Blokované požadavky brány firewall webových aplikací  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  BlockedReqCount  |  Počet blokovaných požadavků firewallu webových aplikací  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  BytesReceived  |  Přijaté bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  BytesSent  |  Odeslané bajty  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. Network/applicationGateways  |  CapacityUnits  |  Aktuální jednotky kapacity  |  Count  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  ClientRtt  |  Čas odezvy klienta  |  Milisekund  |  Průměr | 
| No  | No |  Microsoft. Network/applicationGateways  |  ComputeUnits  |  Aktuální výpočetní jednotky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  CurrentConnections  |  Aktuální připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  FailedRequests  |  Neúspěšné požadavky  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  HealthyHostCount  |  Počet hostitelů v pořádku  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  MatchedCount  |  Distribuce všech pravidel v firewallu webových aplikací  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  ResponseStatus  |  Stav odpovědi  |  Count  |  Celkem | 
| No  | No |  Microsoft. Network/applicationGateways  |  Propustnost  |  Propustnost  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  TlsProtocol  |  Protokol TLS klienta  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  TotalRequests  |  Požadavky celkem  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/applicationGateways  |  UnhealthyHostCount  |  Počet hostitelů není v pořádku  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/azurefirewalls  |  ApplicationRuleHit  |  Počet volání pravidel aplikace  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/azurefirewalls  |  Zpracováno na zpracování  |  Zpracovaná data  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/azurefirewalls  |  FirewallHealth  |  Stav brány firewall  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/azurefirewalls  |  NetworkRuleHit  |  Počet volání síťových pravidel  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/azurefirewalls  |  SNATPortUtilization  |  Využití portu SNAT  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/připojení  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/připojení  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/dnszones  |  QueryVolume  |  Svazek dotazu  |  Count  |  Celkem | 
| No  | No |  Microsoft. Network/dnszones  |  RecordSetCapacityUtilization  |  Využití kapacity sady záznamů  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/dnszones  |  RecordSetCount  |  Počet sad záznamů  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/expressRouteCircuits  |  ArpAvailability  |  Dostupnost protokolu ARP  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRouteCircuits  |  BgpAvailability  |  Dostupnost protokolu BGP  |  Procento  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRouteCircuits/peering  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRouteCircuits/peering  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| No  | No |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  BackendHealthPercentage  |  Procento stavu back-endu  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  BackendRequestCount  |  Počet požadavků back-endu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  BackendRequestLatency  |  Latence žádosti back-endu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  BillableResponseSize  |  Fakturovatelná velikost odpovědi  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  RequestCount  |  Počet požadavků  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  RequestSize  |  Velikost požadavku  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  ResponseSize  |  Velikost odpovědi  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  TotalLatency  |  Celková latence  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Počet požadavků firewallu webových aplikací  |  Count  |  Celkem | 
| No  | No |  Microsoft. Network/loadBalancers  |  AllocatedSnatPorts  |  Přidělené porty SNAT (Preview)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  ByteCount  |  Počet bajtů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  DipAvailability  |  Stav sondy stavu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  PacketCount  |  Počet paketů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  SnatConnectionCount  |  Počet připojení SNAT  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  SYNCount  |  Počet SYN  |  Count  |  Celkem | 
| No  | No |  Microsoft. Network/loadBalancers  |  UsedSnatPorts  |  Využité porty SNAT (Preview)  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/loadBalancers  |  VipAvailability  |  Dostupnost cesty k datům  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/networkInterfaces  |  BytesReceivedRate  |  Přijaté bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/networkInterfaces  |  BytesSentRate  |  Odeslané bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/networkInterfaces  |  PacketsReceivedRate  |  Přijaté pakety  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/networkInterfaces  |  PacketsSentRate  |  Odeslané pakety  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Průměrná doba odezvy (MS)  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Počet neúspěšných kontrol v procentech (Preview)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % PROBE selhalo  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Doba odezvy (MS) (Preview)  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  ByteCount  |  Počet bajtů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  BytesDroppedDDoS  |  Příchozí bajty vynechané DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  BytesForwardedDDoS  |  Příchozí bajty předané DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  BytesInDDoS  |  Příchozí bajty DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Příchozí pakety SYN pro aktivaci zmírnění DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Příchozí pakety TCP pro aktivaci zmírnění DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Příchozí pakety UDP pro aktivaci zmírnění DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  IfUnderDDoSAttack  |  V části útok DDoS nebo ne  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  PacketCount  |  Počet paketů  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Vynechané příchozí pakety DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  PacketsForwardedDDoS  |  DDoS předaných příchozích paketů  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS příchozích paketů  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  SynCount  |  Počet SYN  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Příchozí bajty protokolu TCP vyhozené DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Příchozí DDoS předaných bajtů protokolu TCP  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPBytesInDDoS  |  Příchozí bajty DDoS TCP  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Zrušené příchozí pakety protokolu TCP DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  DDoS předaných paketů příchozího protokolu TCP  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  TCPPacketsInDDoS  |  DDoS příchozí pakety TCP  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Zrušené Příchozí bajty UDP DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Příchozí DDoS předávaných bajtů UDP  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPBytesInDDoS  |  Příchozí bajty UDP DDoS  |  BytesPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Zrušené příchozí pakety UDP DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Příchozí pakety UDP předané DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Příchozí pakety UDP DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/publicIPAddresses  |  VipAvailability  |  Dostupnost cesty k datům  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Stav koncového bodu podle koncového bodu  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. Network/trafficManagerProfiles  |  QpsByEndpoint  |  Dotazy podle vráceného koncového bodu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  AverageBandwidth  |  Šířka pásma S2S brány  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  P2SBandwidth  |  Šířka pásma P2S brány  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  P2SConnectionCount  |  Počet připojení P2S  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Šířka pásma tunelu  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Počet bajtů výchozího přenosu dat tunelu  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Počet zahození paketů výchozího přenosu dat tunelu kvůli neshodě SP  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Počet paketů výchozího přenosu dat tunelu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Počet bajtů příchozího přenosu dat tunelu  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Počet zahození paketů příchozího přenosu dat tunelu kvůli neshodě SP  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunelové pakety pro příchozí připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Čas odezvy na virtuální počítač pomocí příkazů pro odeslání  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Nepovedlo se odeslat příkazy do virtuálního počítače  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  přijíman  |  Příchozí zprávy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  příchozí. All. failedrequests  |  Všechny příchozí neúspěšné požadavky  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  příchozí. All. – žádosti  |  Všechny příchozí žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  příchozí. plánováno  |  Odeslaná naplánovaná nabízená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  příchozí. plánováno. Cancel  |  Zrušená plánovaná nabízená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  instalace. vše  |  Operace správy instalace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  instalace. odstranit  |  Odstranit operace instalace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  instalace. Get  |  Získat operace instalace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  instalace. patch  |  Operace instalace opravy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  instalace. Upsert  |  Operace vytvoření nebo aktualizace instalace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  notificationhub. push  |  Všechna odchozí oznámení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. allpns. badorexpiredchannel  |  Chybné nebo prošlé chyby kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. allpns. channelerror  |  Chyby kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. allpns. invalidpayload  |  Chyby datové části  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. allpns. pnserror  |  Chyby systému externích oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. allpns. úspěch  |  Úspěšná oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. badchannel  |  Chyba služby APN Bad Channel  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. expiredchannel  |  Chyba kanálu vypršení platnosti služby APNS  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. invalidcredentials  |  Chyby autorizace APNS  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. invalidnotificationsize  |  APN – Chyba neplatné velikosti oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. pnserror  |  Chyby služby APN  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. APNs. Success  |  Úspěšná oznámení APNS  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. authenticationerror  |  GCM chyby ověřování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. badchannel  |  Chyba GCM špatného kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. expiredchannel  |  Chyba kanálu vypršení platnosti GCM  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. invalidcredentials  |  GCM – chyby autorizace (neplatné přihlašovací údaje)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. invalidnotificationformat  |  GCM neplatný formát oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. invalidnotificationsize  |  GCMá Chyba neplatné velikosti oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. pnserror  |  GCM chyby  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. úspěch  |  GCM úspěšná oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. omezení  |  GCM omezená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. GCM. wrongchannel  |  GCM chybná chyba kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. authenticationerror  |  MPNS chyby ověřování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. badchannel  |  Chyba MPNS špatného kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. channeldisconnected  |  MPNS kanál odpojen  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. vyřazeno  |  MPNS Vyřazená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. invalidcredentials  |  Neplatné přihlašovací údaje MPNS  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. invalidnotificationformat  |  MPNS neplatný formát oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. pnserror  |  MPNS chyby  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. úspěch  |  MPNS úspěšná oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. MPNS. omezení  |  MPNS omezená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. authenticationerror  |  WNS chyby ověřování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. badchannel  |  Chyba WNS špatného kanálu  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. channeldisconnected  |  WNS kanál odpojen  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. channelthrottled  |  WNS kanál – omezení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. vyřazeno  |  WNS Vyřazená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. expiredchannel  |  Chyba kanálu vypršení platnosti WNS  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. invalidcredentials  |  WNS – chyby autorizace (neplatné přihlašovací údaje)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. invalidnotificationformat  |  WNS neplatný formát oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. invalidnotificationsize  |  WNSá Chyba neplatné velikosti oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. invalidtoken  |  WNS – chyby autorizace (neplatný token)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. pnserror  |  WNS chyby  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. úspěch  |  WNS úspěšná oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. omezení  |  WNS omezená oznámení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. tokenproviderunreachable  |  WNS – chyby autorizace (nedostupné)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  odchozí. WNS. wrongtoken  |  WNS chyby autorizace (špatný token)  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  registrace. vše  |  Operace registrace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  registrace. Create  |  Operace vytvoření registrace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  registrace. odstranění  |  Registrace – operace odstranění  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  registrace. Get  |  Registrace – operace čtení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  registrace. aktualizace  |  Operace aktualizace registrace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. NotificationHubs/obory názvů/NotificationHubs  |  naplánováno. čeká na  |  Nedokončená plánovaná oznámení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_ k dispozici paměti  |  % Dostupné paměti  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% dostupného odkládacího prostoru  |  % Dostupného odkládacího prostoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% používaných potvrzených bajtů  |  % Používaných potvrzených bajtů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času DPC  |  % Času DPC  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_ uzlů inode% Free  |  % Bezplatného uzlů inode  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% volného místa  |  % Volného místa  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% volného místa  |  % Volného místa  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času nečinnosti  |  % Času nečinnosti  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času přerušení  |  % Času přerušení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času čekání na v/v  |  % Času čekání na v/v  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% dobrý čas  |  % Dobrý čas  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% privilegovaného času  |  % Privilegovaného času  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času procesoru  |  Procesorový čas v %  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% času procesoru  |  Procesorový čas v %  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% využití uzlů inode  |  % Použitého uzlů inode  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Využitá paměť Average_%  |  % Využité paměti  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Využité místo Average_%  |  % Využitého místa  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_% využitého místa odkládacího souboru  |  % Využitého místa odkládacího souboru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Čas uživatele Average_%  |  % Uživatelského času  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Available MB  |  Dostupné MB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Paměť Average_Available v MB  |  Dostupná paměť v MB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Přeměna Average_Available MB  |  Dostupný počet MB swap  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/čtení  |  Střední doba disku/čtení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/čtení  |  Střední doba disku/čtení  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Avg. Doba disku/přenos  |  Střední doba disku/přenos  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/zápis  |  Střední doba disku/zápis  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/zápis  |  Střední doba disku/zápis  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Přijaté Average_Bytes za sekundu  |  Přijaté bajty/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Odeslané Average_Bytes za sekundu  |  Odeslané bajty/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Bytes celkem/s  |  Bajty celkem/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Délka fronty disku Average_Current  |  Aktuální délka fronty disku  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk přečtených bajtů/s  |  Bajty čtení z disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk čtení za sekundu  |  Čtení z disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk čtení za sekundu  |  Čtení z disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk přenosů za sekundu  |  Přenosy disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk přenosů za sekundu  |  Přenosy disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Disk bajty zápisu za sekundu  |  Bajty zápisu na disk/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Zápisy Average_Disk za sekundu  |  Zápisy na disk/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Zápisy Average_Disk za sekundu  |  Zápisy na disk/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Free megabajtů  |  Volné megabajty  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Free megabajtů  |  Volné megabajty  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Free fyzická paměť  |  Volná fyzická paměť  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Free mezera v stránkovacích souborech  |  Volné místo ve stránkovacích souborech  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Free virtuální paměti  |  Volná virtuální paměť  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Počet bajtů Average_Logical disku za sekundu  |  Bajty logického disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Page čtení za sekundu  |  Čtení stránek/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Zápisy Average_Page za sekundu  |  Zápisy stránek/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Pages/s  |  Stránky/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Pct privilegovaného času  |  Privilegovaný čas protokolu PCT  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Čas uživatele Average_Pct  |  Doba uživatele v protokolu PCT  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Počet bajtů Average_Physical disku za sekundu  |  Bajty fyzického disku/s  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Processes  |  Procesy  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Délka fronty Average_Processor  |  Délka fronty procesoru  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Size uložený ve stránkovacích souborech  |  Velikost uložená ve stránkovacích souborech  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Total bajty  |  Bajty celkem  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Přijaté bajty Average_Total  |  Celkový počet přijatých bajtů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Přenesené bajty Average_Total  |  Celkový počet odeslaných bajtů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Total kolizí  |  Celkový počet kolizí  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Přijaté pakety Average_Total  |  Celkový počet přijatých paketů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Odeslané Average_Total pakety  |  Celkový počet odeslaných paketů  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Total chyby příjmu  |  Celkový počet chyb příjmu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Chyby při odesílání Average_Total  |  Chyby odesílání celkem  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Uptime  |  Doba provozu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Used MB odkládacího prostoru  |  Využité místo odkládacího souboru v MB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Used kB paměti  |  Využitá paměť v kilobajtech  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Used paměť v MB  |  Využitá paměť v MB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Users  |  Uživatelé  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Average_Virtual sdílená paměť  |  Virtuální sdílená paměť  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Událost  |  Událost  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. OperationalInsights/pracovní prostory  |  Tep  |  Tep  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. OperationalInsights/pracovní prostory  |  Aktualizace  |  Aktualizace  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. PowerBIDedicated/– kapacity  |  memory_metric  |  Paměť  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. PowerBIDedicated/– kapacity  |  memory_thrashing_metric  |  Thrashing paměti (datové sady)  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. PowerBIDedicated/– kapacity  |  qpu_high_utilization_metric  |  Vysoké využití procesoru  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. PowerBIDedicated/– kapacity  |  QueryDuration  |  Doba trvání dotazu (datové sady)  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. PowerBIDedicated/– kapacity  |  QueryPoolJobQueueLength  |  Délka fronty úloh fondu dotazů (datové sady)  |  Count  |  Průměr | 
| No  | No |  Microsoft. Relay/obory názvů  |  ActiveConnections  |  ActiveConnections  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ActiveListeners  |  ActiveListeners  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Relay/obory názvů  |  BytesTransferred  |  BytesTransferred  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ListenerConnections – ClientError  |  ListenerConnections – ClientError  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ListenerConnections – ServerError  |  ListenerConnections – ServerError  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ListenerConnections – úspěch  |  ListenerConnections – úspěch  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ListenerConnections – TotalRequests  |  ListenerConnections – TotalRequests  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  SenderConnections – ClientError  |  SenderConnections – ClientError  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  SenderConnections – ServerError  |  SenderConnections – ServerError  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  SenderConnections – úspěch  |  SenderConnections – úspěch  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  SenderConnections – TotalRequests  |  SenderConnections – TotalRequests  |  Count  |  Celkem | 
| No  | No |  Microsoft. Relay/obory názvů  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Search/searchServices  |  SearchLatency  |  Latence hledání  |  Sekundy  |  Průměr | 
| **Ano**  | No |  Microsoft. Search/searchServices  |  SearchQueriesPerSecond  |  Hledání dotazů za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Procento omezených vyhledávacích dotazů  |  Procento  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ActiveConnections  |  ActiveConnections  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ActiveMessages  |  Počet aktivních zpráv ve frontě nebo tématu.  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ConnectionsClosed  |  Zavřená připojení.  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ConnectionsOpened  |  Otevřená připojení.  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  CPUXNS  |  PROCESOR (zastaralé)  |  Procento  |  Maximum | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  DeadletteredMessages  |  Počet nedoručených zpráv ve frontě nebo tématu  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. ServiceBus/obory názvů  |  IncomingMessages  |  Příchozí zprávy  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. ServiceBus/obory názvů  |  IncomingRequests  |  Příchozí žádosti  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  Zprávy  |  Počet zpráv ve frontě/tématu.  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  NamespaceCpuUsage  |  Procesor  |  Procento  |  Maximum | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  NamespaceMemoryUsage  |  Využití paměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. ServiceBus/obory názvů  |  OutgoingMessages  |  Odchozí zprávy  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ScheduledMessages  |  Počet naplánovaných zpráv ve frontě nebo tématu.  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ServerErrors  |  Chyby serveru.  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  Velikost  |  Velikost  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  SuccessfulRequests  |  Úspěšné požadavky  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  ThrottledRequests  |  Omezené požadavky.  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  UserErrors  |  Chyby uživatele.  |  Count  |  Celkem | 
| No  | No |  Microsoft. ServiceBus/obory názvů  |  WSXNS  |  Využití paměti (zastaralé)  |  Procento  |  Maximum | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ActualCpu  |  ActualCpu  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ActualMemory  |  ActualMemory  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  AllocatedCpu  |  AllocatedCpu  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  AllocatedMemory  |  AllocatedMemory  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ContainerStatus  |  ContainerStatus  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  CpuUtilization  |  CpuUtilization  |  Procento  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  MemoryUtilization  |  MemoryUtilization  |  Procento  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  RestartCount  |  RestartCount  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Průměr | 
| No  | No |  Microsoft. ServiceFabricMesh/aplikace  |  ServiceStatus  |  ServiceStatus  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  ConnectionCount  |  Počet připojení  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  InboundTraffic  |  Příchozí provoz  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  MessageCount  |  Počet zpráv  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  OutboundTraffic  |  Odchozí provoz  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  SystemErrors  |  Systémové chyby  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SignalRService/Signaler  |  UserErrors  |  Chyby uživatele  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/managedInstances  |  avg_cpu_percent  |  Průměrné procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  io_bytes_read  |  Přečtené vstupně-výstupní bajty  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  io_bytes_written  |  Zapsané vstupně-výstupní bajty  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  io_requests  |  Počet požadavků v/v  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  reserved_storage_mb  |  Rezervované místo v úložišti  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  storage_space_used_mb  |  Využité místo úložiště  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/managedInstances  |  virtual_core_count  |  Počet virtuálních jader  |  Count  |  Průměr | 
| No  | No |  Microsoft. SQL/servery  |  database_dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery  |  database_storage_used  |  Využité místo pro data  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery  |  dtu_used  |  Využité DTU  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery  |  storage_used  |  Využité místo pro data  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  allocated_data_storage  |  Přidělené datové místo  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  app_cpu_billed  |  CPU aplikace se fakturuje.  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  app_cpu_percent  |  Procento využití procesoru aplikací  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  app_memory_percent  |  Procento paměti aplikace  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  blocked_by_firewall  |  Blokováno bránou firewall  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  cache_hit_percent  |  Procento přístupů do mezipaměti  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  cache_used_percent  |  Procento využité mezipaměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  connection_failed  |  Neúspěšná připojení  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  connection_successful  |  Úspěšná připojení  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  cpu_limit  |  Limit procesoru  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  cpu_used  |  Využitý procesor  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  ukončení  |  Zablokování  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  dtu_limit  |  Limit DTU  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  dtu_used  |  Využité DTU  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  dwu_consumption_percent  |  Procento DWU  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  dwu_limit  |  DWU limit  |  Count  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  dwu_used  |  DWU použito  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  local_tempdb_usage_percent  |  Místní procento databáze tempdb  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  log_write_percent  |  Procentní hodnota protokolu v/v  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  memory_usage_percent  |  Procento paměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  sessions_percent  |  Procento relací  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  úložiště  |  Využité místo pro data  |  Bajty  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  storage_percent  |  Procento využitého datového prostoru  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  tempdb_data_size  |  Velikost datového souboru tempdb v kilobajtech  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  tempdb_log_size  |  Velikost souboru protokolu tempdb v kilobajtech  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/databáze  |  tempdb_log_used_percent  |  Použit protokol tempdb v procentech  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  workers_percent  |  Procento pracovních procesů  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/databáze  |  xtp_storage_percent  |  Procentuální hodnota úložiště OLTP v paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  allocated_data_storage  |  Přidělené datové místo  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  allocated_data_storage_percent  |  Procentuální hodnota přiděleného datového prostoru  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  cpu_limit  |  Limit procesoru  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  cpu_used  |  Využitý procesor  |  Count  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_allocated_data_storage  |  Přidělené datové místo  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_cpu_limit  |  Limit procesoru  |  Count  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_cpu_used  |  Využitý procesor  |  Count  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_eDTU_used  |  využité eDTU  |  Count  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_log_write_percent  |  Procentní hodnota protokolu v/v  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_sessions_percent  |  Procento relací  |  Procento  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_storage_used  |  Využité místo pro data  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. SQL/servery/elasticPools  |  database_workers_percent  |  Procento pracovních procesů  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  eDTU_limit  |  limit eDTU  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  eDTU_used  |  využité eDTU  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  log_write_percent  |  Procentní hodnota protokolu v/v  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  sessions_percent  |  Procento relací  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  storage_limit  |  Maximální velikost dat  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  storage_percent  |  Procento využitého datového prostoru  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  storage_used  |  Využité místo pro data  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  tempdb_data_size  |  Velikost datového souboru tempdb v kilobajtech  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  tempdb_log_size  |  Velikost souboru protokolu tempdb v kilobajtech  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. SQL/servery/elasticPools  |  tempdb_log_used_percent  |  Použit protokol tempdb v procentech  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  workers_percent  |  Procento pracovních procesů  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. SQL/servery/elasticPools  |  xtp_storage_percent  |  Procentuální hodnota úložiště OLTP v paměti  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| No  | No |  Microsoft. Storage/storageAccounts  |  UsedCapacity  |  Využitá kapacita  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCapacity  |  Kapacita objektu BLOB  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCount  |  Počet objektů BLOB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  ContainerCount  |  Počet kontejnerů objektů BLOB  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. Storage/storageAccounts/blobServices  |  IndexCapacity  |  Kapacita indexu  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/blobServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  Kapacita zařízení  |  Kapacita souboru  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  FileCount  |  Počet souborů  |  Count  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  FileShareCount  |  Počet sdílených souborů  |  Count  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  FileShareQuota  |  Velikost kvóty sdílení souborů  |  Bajty  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  FileShareSnapshotCount  |  Počet snímků sdílené složky  |  Count  |  Průměr | 
| No  | No |  Microsoft. Storage/storageAccounts/služby  |  FileShareSnapshotSize  |  Velikost snímku sdílené složky  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/služby  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCapacity  |  Kapacita fronty  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCount  |  Počet front  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Počet zpráv ve frontě  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/queueServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  TableCapacity  |  Kapacita tabulky  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  TableCount  |  Počet tabulek  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  TableEntityCount  |  Počet entit tabulky  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. Storage/storageAccounts/tableServices  |  Transakce  |  Transakce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientIOPS  |  Celkový počet vstupně-výstupních operací klienta  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientLatency  |  Průměrná latence klienta  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientLockIOPS  |  IOPS zámku klienta  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientMetadataReadIOPS  |  Metadata klienta přečetla IOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientMetadataWriteIOPS  |  Zápis metadat klienta za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientReadIOPS  |  Klientský vstupně-výstupní operace čtení  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientReadThroughput  |  Průměrná propustnost čtení mezipaměti  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientWriteIOPS  |  Zápis klientského IOPS za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  ClientWriteThroughput  |  Průměrná propustnost zápisu mezipaměti  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Propustnost asynchronního zápisu StorageTarget  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetFillThroughput  |  Propustnost výplně StorageTarget  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetHealth  |  Stav cíle úložiště  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetIOPS  |  Celkem StorageTarget IOPS  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetLatency  |  Latence StorageTarget  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  StorageTarget metadata Read IOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  Zápisy metadat StorageTarget IOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetReadAheadThroughput  |  Propustnost před čtením StorageTarget  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetReadIOPS  |  StorageTarget čtení IOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Propustnost synchronního zápisu StorageTarget  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetTotalReadThroughput  |  StorageTarget celkové propustnosti čtení  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  StorageTarget celkové propustnosti zápisu  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  StorageTargetWriteIOPS  |  StorageTarget zápis IOPS  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. StorageCache/caches  |  Doba provozu  |  Doba provozu  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Výsledek relace synchronizace  |  Count  |  Průměr | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Velikost odvolání při vrstvení cloudu podle aplikace  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Velikost odvolání při vyvolání vrstvy cloudu  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Stažení vrstvení cloudu  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Propustnost volání při navracení cloudových vrstev  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Online stav serveru  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Synchronizované soubory  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Soubory se nesynchronizují  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Online stav serveru  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Stažení vrstvení cloudu  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Synchronizované soubory  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Soubory se nesynchronizují  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Synchronizované soubory  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Soubory se nesynchronizují  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Neúspěšné žádosti o funkce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Události funkcí  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Žádosti o funkce  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  ConversionErrors  |  Chyby převodu dat  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  DeserializationError  |  Chyby při deserializaci vstupu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Události mimo pořadí  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Události předčasného vstupu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  Chyby  |  Běhové chyby  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bajty vstupních událostí  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  InputEvents  |  Události vstupu  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Nevyřízené události vstupu  |  Count  |  Maximum | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Přijaté vstupní zdroje  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  LateInputEvents  |  Zpožděné vstupní události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  OutputEvents  |  Výstupní události  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Zpoždění vodoznaku  |  Sekundy  |  Maximum | 
| **Ano**  | No |  Microsoft. StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Využití SU%  |  Procento  |  Maximum | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty čtení z disku  |  Bajty čtení z disku  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operace čtení z disku/s  |  Operace čtení z disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty zápisu na disk  |  Bajty zápisu na disk  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Bajty čtení z disku/s  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Latence čtení disku  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Operace čtení z disku  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Bajty zápisu na disk/s  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latence zápisu na disk  |  Milisekund  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operace zápisu na disk  |  Count  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Paměť aktivní  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Přidělená paměť  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Využitá paměť  |  Bajty  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Síť v bajtech/s  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Výstupní bajty sítě/s  |  BytesPerSecond  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | No |  Microsoft. VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Procento připraveného procesoru  |  Milisekund  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktivní požadavky  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Výstupní data  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Délka fronty disku  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http2xx  |  2xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http3xx  |  3xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http4xx  |  4xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Chyby serveru http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Délka fronty http  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Zaměstnanci s velkými App Servicey plánu  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Střední App Service plánování pracovních procesů  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Žádosti  |  Žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Plánování pracovníků malých App Service  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Celkový počet front-endy  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Zaměstnanci, kteří jsou k dispozici  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Celkový počet pracovníků  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Využívané pracovní procesy  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  BytesReceived  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  BytesSent  |  Výstupní data  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  CpuPercentage  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  DiskQueueLength  |  Délka fronty disku  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  HttpQueueLength  |  Délka fronty http  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  MemoryPercentage  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpCloseWait  |  Ukončení čekání protokolu TCP  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpClosing  |  Ukončení protokolu TCP  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpEstablished  |  TCP – vytvořeno  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpFinWait1  |  TCP FIN – čekání 1  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpFinWait2  |  TCP FIN – čekání 2  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpLastAck  |  Poslední potvrzení TCP  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpSynReceived  |  Přijato TCP syn  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpSynSent  |  Odesláno TCP syn  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/serverových farem  |  TcpTimeWait  |  Doba čekání protokolu TCP  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  AppConnections  |  Připojení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  AverageMemoryWorkingSet  |  Průměrná pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  AverageResponseTime  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  BytesReceived  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  BytesSent  |  Výstupní data  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  CpuTime  |  Čas procesoru  |  Sekundy  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  CurrentAssemblies  |  Aktuální sestavení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  FunctionExecutionCount  |  Počet spuštění funkce  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  FunctionExecutionUnits  |  Jednotky spuštění funkce  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Gen0Collections  |  Generace paměti gen 0  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Gen1Collections  |  Generace paměti 1. generace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Gen2Collections  |  Uvolňování paměti 2. generace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Handles  |  Počet popisovačů  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  HealthCheckStatus  |  Stav kontroly stavu  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http101  |  Http 101  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http2xx  |  2xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http3xx  |  3xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http401  |  HTTP 401  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http403  |  HTTP 403  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http404  |  HTTP 404  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http406  |  Http 406  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http4xx  |  4xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Http5xx  |  Chyby serveru http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  HttpResponseTime  |  Doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoOtherBytesPerSecond  |  IO – ostatní bajty za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoOtherOperationsPerSecond  |  V/v – ostatní operace za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoReadBytesPerSecond  |  Bajty čtení v/v za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoReadOperationsPerSecond  |  Vstupně-výstupní operace čtení za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoWriteBytesPerSecond  |  Vstupně-výstupní bajty zápisu za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  IoWriteOperationsPerSecond  |  Vstupně-výstupní operace zápisu za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  MemoryWorkingSet  |  Pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  PrivateBytes  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Žádosti  |  Žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  RequestsInApplicationQueue  |  Žádosti ve frontě aplikací  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  Vlákna  |  Počet vláken  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  TotalAppDomains  |  Celkový počet domén aplikace  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby  |  TotalAppDomainsUnloaded  |  Celkový počet uvolněných domén aplikace  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  AppConnections  |  Připojení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  AverageMemoryWorkingSet  |  Průměrná pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  AverageResponseTime  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  BytesReceived  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  BytesSent  |  Výstupní data  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  CpuTime  |  Čas procesoru  |  Sekundy  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  CurrentAssemblies  |  Aktuální sestavení  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  FunctionExecutionCount  |  Počet spuštění funkce  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  FunctionExecutionUnits  |  Jednotky spuštění funkce  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Gen0Collections  |  Generace paměti gen 0  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Gen1Collections  |  Generace paměti 1. generace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Gen2Collections  |  Uvolňování paměti 2. generace  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Handles  |  Počet popisovačů  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  HealthCheckStatus  |  Stav kontroly stavu  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http101  |  Http 101  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http2xx  |  2xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http3xx  |  3xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http401  |  HTTP 401  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http403  |  HTTP 403  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http404  |  HTTP 404  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http406  |  Http 406  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http4xx  |  4xx http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Http5xx  |  Chyby serveru http  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  HttpResponseTime  |  Doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoOtherBytesPerSecond  |  IO – ostatní bajty za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoOtherOperationsPerSecond  |  V/v – ostatní operace za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoReadBytesPerSecond  |  Bajty čtení v/v za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoReadOperationsPerSecond  |  Vstupně-výstupní operace čtení za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoWriteBytesPerSecond  |  Vstupně-výstupní bajty zápisu za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  IoWriteOperationsPerSecond  |  Vstupně-výstupní operace zápisu za sekundu  |  BytesPerSecond  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  MemoryWorkingSet  |  Pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  PrivateBytes  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Žádosti  |  Žádosti  |  Count  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  RequestsInApplicationQueue  |  Žádosti ve frontě aplikací  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  Vlákna  |  Počet vláken  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  TotalAppDomains  |  Celkový počet domén aplikace  |  Count  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft. Web/weby/sloty  |  TotalAppDomainsUnloaded  |  Celkový počet uvolněných domén aplikace  |  Count  |  Průměr | 
