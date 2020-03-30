---
title: Metriky platformy Azure Monitor exportovatelné pomocí nastavení diagnostiky
description: Seznam metrik, které jsou k dispozici pro každý typ prostředku pomocí Azure Monitoru.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661358"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metriky platformy Azure Monitor exportovatelné pomocí nastavení diagnostiky

Azure Monitor poskytuje [metriky platformy](data-platform-metrics.md) ve výchozím nastavení bez konfigurace. Poskytuje několik způsobů, jak pracovat s metrikami platformy, včetně jejich vytváření grafů na portálu, přístupu k nim prostřednictvím rozhraní REST API nebo dotazování pomocí prostředí PowerShell nebo CLI. Podívejte se na [metriky podporované](metrics-supported.md) pro úplný seznam metrik platformy, které jsou momentálně dostupné v konsolidovaném kanálu metrik Azure Monitoru. Chcete-li dotazovat a přistupovat k těmto metrikám, použijte [verzi api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

Metriky platformy můžete exportovat z kanálu monitorování Azure do jiných umístění jedním ze dvou způsobů.
1. Pomocí [diagnostických nastavení](diagnostic-settings.md) se můžete odeslat do Log Analytics, Event Hubs nebo Azure Storage.
2. Použití [metrik rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Kvůli složitosti v back-endu Azure Monitor, ne všechny metriky jsou exportovatelné pomocí nastavení diagnostiky. V následující tabulce jsou uvedeny, které lze a nelze exportovat pomocí diagnostických nastavení.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Změna chování pro hodnoty NULL a Nula 
 
Pro metriky platformy, které lze exportovat prostřednictvím nastavení diagnostiky, existuje několik metrik, pro které Azure Monitor interpretuje '0s' jako "Nulls". To způsobilo určité nejasnosti mezi skutečné '0s' (vyzařované zdrojem) a interpretovány '0s' (Nulls). **dubna 2020** metriky platformy exportované prostřednictvím nastavení diagnostiky již nebudou exportovat "0", pokud nebyly skutečně emitovány podkladovým zdrojem. Poznámka:

1.  Pokud odstraníte skupinu prostředků nebo konkrétní prostředek, metrická data z inkasovaných prostředků již nebudou odeslána do diagnostických cílů exportu. To znamená, že se již nebude zobrazovat v centrech událostí, účtech úložiště a pracovních prostorech analýzy protokolů.
2.  Toto zlepšení bude k dispozici ve všech veřejných a soukromých cloudech.
3.  Tato změna nebude mít vliv na chování některé z následujících prostředí: 
   - Protokoly prostředků platformy exportované pomocí nastavení diagnostiky
   - Grafy metrik v Průzkumníku metrik
   - Upozornění na metriky platformy
 
## <a name="metrics-exportable-table"></a>Tabulka s exportovatelnými metrikami 

Tabulka obsahuje následující sloupce. 
- Exportovatelné pomocí diagnostických nastavení? 
- Provedeno nulou / 0 
- ResourceType 
- Metrika 
- Název metrického zobrazení
- Jednotka 
- AgregaceTyp


> [!NOTE]
> Níže uvedená tabulka může mít v dolní části vodorovný posuvník. Pokud se domníváte, že vám chybí informace, zkontrolujte, zda je posuvník úplně vlevo.  


| Exportovatelné pomocí diagnostických nastavení?  | Vyzařuje nulls |  ResourceType  |  Metrika  |  Název metrického zobrazení  |  Jednotka  |  AgregaceTyp | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  CleanerCurrentCena  |  Paměť: Čistší aktuální cena  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  CleanerMemoryNeshrinkable  |  Paměť: Čistší paměť nesmrštná  |  Bajty  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  CleanerMemoryShrinkable  |  Paměť: Čistší paměť smrštitelná  |  Bajty  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  Podprocesy CommandPoolBusy  |  Vlákna: Podprocesy zaneprázdněné příkazy  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  Příkazy Pročinné a  |  Vlákna: Nečinná vlákna fondu příkazů  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  Délka fronty commandpoolu  |  Délka fronty úlohy fondu příkazů  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  CurrentConnections  |  Připojení: Aktuální připojení  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  CurrentUserSessions  |  Aktuální uživatelské relace  |  Počet  |  Průměr | 
| Ano****  | Ne |  Microsoft.AnalysisServices/servery  |  LongParsingBusyVlákna  |  Vlákna: Dlouhá analýza zaneprázdněných vláken  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  LongParsingIdleThreads  |  Závity: Dlouhá analýza nečinných závitů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Délka fronty  |  Vlákna: Délka fronty úloh dlouhé analýzy  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  mashup_engine_memory_metric  |  M Paměť motoru  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  mashup_engine_private_bytes_metric  |  Soukromé bajty motoru M  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  mashup_engine_qpu_metric  |  M Motor QPU  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  mashup_engine_virtual_bytes_metric  |  M Virtuální bajty motoru  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  memory_metric  |  Memory (Paměť)  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  memory_thrashing_metric  |  Thrashing paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  MemoryLimitHard  |  Paměť: Limit paměti pevný  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  MemoryLimitHigh  |  Paměť: Maximální limit paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  MemoryLimitLow  |  Paměť: Nedostatek limitu paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  PaměťLimitVertiPaq  |  Paměť: Limit paměti VertiPaq  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Využití paměti  |  Paměť: Využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  private_bytes_metric  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolBusyIOJobThreads  |  Vlákna: Zpracování fondu zaneprázdněnvstupně-v.,V úlohy podprocesů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolBusyNonIOThreads  |  Vlákna: Zpracování fondu zaneprázdněné vlákny, která nejsou vstupně-vstupně-va.  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolIdleIOJobThreads  |  Vlákna: Zpracování nečinných vstupně-vi úloh fondu  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolIdleNonIOThreads  |  Vlákna: Zpracování nečinnosti fondu bez vstupně-v/V podprocesů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolIOJobQueueLength  |  Vlákna: Délka fronty úloh vstupně-v.i.a. fondu  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ProcessingPoolJobQueueLength  |  Délka fronty úlohy fondu pro zpracování  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  qpu_metric  |  QPU  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Podprocesy QueryPoolBusy  |  Zaneprázdněná vlákna fondu dotazů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  QueryPoolIdleThreads  |  Vlákna: Nečinná vlákna fondu dotazů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  QueryPoolJobQueueLength  |  Vlákna: Délka fronty úloh fondu dotazů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Kvóta  |  Paměť: Kvóta  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Kvóta blokována  |  Paměť: Kvóta byla zablokována  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  RowsConvertedPerSec  |  Zpracování: Řádky převedené za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  RowsReadPerSec  |  Zpracování: Řádky přečtené za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  RowsWrittenPerSec  |  Zpracování: Řádky napsané za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ShortParsingBusyVlákna  |  Vlákna: Krátká analýza zaneprázdněných vláken  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ShortParsingIdleThreads  |  Závity: Krátká analýza nečinných závitů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  ShortParsingJobQueueLength  |  Vlákna: Délka fronty úlohy krátké analýzy  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  SuccessfullConnectionsPerSec  |  Úspěšná připojení za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  Selhání totalconnections  |  Celkový počet selhání připojení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  TotalConnectionRequests  |  Celkový počet požadavků na připojení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  VertiPaqNonpaged  |  Paměť: VertiPaq Nonpaged  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  VertiPaqPaged  |  Paměť: VertiPaq Stránkované  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AnalysisServices/servery  |  virtual_bytes_metric  |  Virtuální bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Doba trvání back-endových požadavků  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ApiManagement/service  |  Kapacita  |  Kapacita  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Doba trvání  |  Celková doba trvání požadavků brány  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Zrušené události EventHub  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Odmítnuté události EventHub  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubÚspěšné události  |  Úspěšné události EventHub  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Události eventhubu s omezením  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Události EventHubTimedoutEvents  |  Události EventHub s časovým výtažkem  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Velikost událostí EventHub  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Celkový počet událostí EventHub  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Neúspěšné události eventhubu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Požadavky na selhání  |  Neúspěšné požadavky na bránu (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Ostatní požadavky  |  Další požadavky na bránu (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Žádosti  |  Žádosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Úspěšné požadavky  |  Úspěšné požadavky na bránu (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Celkový počet požadavků na bránu (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.ApiManagement/service  |  Neautorizované požadavky  |  Neautorizované požadavky na bránu (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  AppCpuUsageProcento  |  Procento využití procesoru aplikace  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  AppMemoryCommitted  |  Přiřazená paměť aplikace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  AppMemoryMax  |  Paměť aplikace Max  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Použitá paměť aplikace  |  Použitá paměť aplikace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Počet gcpausetotalcount  |  Počet pozastavení GC  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Čas gcpausetotaltime  |  Celkový čas pozastavení GC  |  Milisekund  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Bajty maxoldgenmemorypool  |  Maximální dostupná velikost dat staré generace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  OldGenMemoryPoolBajty  |  Velikost dat staré generace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  OldGenPromotedBajty  |  Zvýšit velikost dat staré generace  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  SystemCpuUsageProcento  |  Procento využití procesoru systému  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Počet chyb Tomcat  |  Globální chyba Společnosti Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatReceivedBytes  |  Tomcat Celkem přijatých bajtů  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatRequestMaxTime  |  Maximální čas požadavku Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatRequestTotalCount  |  Celkový počet požadavků na kotomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatRequestTotalTime  |  Tomcat Žádost Celkem Krát  |  Milisekund  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatResponseAvgTime  |  Průměrná doba požadavku Tomcat  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatSentBytes  |  Celkový počet odeslaných bajtů kotomak  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatSessionActiveCurrentCount  |  Počet živých relací Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatSessionActiveMaxCount  |  Počet aktivních aktivit relace Kotomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatSessionAliveMaxČas  |  Tomcat Session Max Alive Čas  |  Milisekund  |  Maximum | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Počet vytvořených v tomcatSession  |  Počet vytvořených relací Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  Počet buněk TomcatSessionExpiredCount  |  Počet prošlých platností relace Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  TomcatSessionRejectedCount  |  Počet odmítnutých relace Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.AppPlatform/jaro  |  YoungGenPromotedBajty  |  Povýšení na velikost dat mladé generace  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Automation/automationÚčty  |  TotalJob  |  Celkový počet úloh  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Automation/automationÚčty  |  TotalUpdateDeploymentMachineRuns  |  Celkový počet spuštění počítače pro nasazení aktualizace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Automation/automationÚčty  |  Počet nasazení totalupdatedeploymentruns  |  Celkový počet spuštění nasazení aktualizace  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  Počet corecount  |  Vyhrazený počet jádra  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Vytvoření počtu uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Počet nečinných uzlů  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Úloha odstranit úplné události  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost JobDeleteStartEvent  |  Události spuštění s odstraněním úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Zakázat dokončení událostí úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost JobDisableStartEvent  |  Zakázat události zahájení úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost Úloha StartEvent  |  Události zahájení úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Akce JobTerminateCompleteEvent  |  Události ukončení úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost JobTerminateStartEvent  |  Události ukončení úlohy zahájení  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Počet uzlů opuštění fondu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Počet jádra LowPriority  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Počet uzlů offline  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Události vytvoření fondu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Úplné události odstranění fondu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost PoolDeleteStartEvent  |  Události spuštění odstranění fondu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Události dokončení velikosti fondu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Události zahájení změny velikosti fondu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Počet předplyňovaných uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Restartování počtu uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Počet reimagingových uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Počet spuštěné uzlu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Počáteční počet uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  Počet nepodařilose spustit úkol  |  Počet neúspěšných uzlů spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost úkolu CompleteEvent  |  Události dokončení úkolu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost úlohy FailEvent  |  Události selhání úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Batch/batchAccounts  |  Událost TaskStartEvent  |  Události zahájení úkolu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Počet uzlů s nízkou prioritou  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Počet vyhrazených uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Počet nepoužitelných uzlů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Čekání na počet počátečních uzlů úloh  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Aktivní jádra  |  Aktivní jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Aktivní uzly  |  Aktivní uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Nečinná jádra  |  Nečinná jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Nečinné uzly  |  Nečinné uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Úloha dokončena  |  Úloha dokončena  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Odeslaná úloha  |  Odeslaná úloha  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Opuštění jader  |  Opuštění jader  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Opuštění uzlů  |  Opuštění uzlů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Předpjatojádra  |  Předpjatojádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Předpisované uzly  |  Předpisované uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Procento využití kvóty  |  Procento využití kvóty  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Jádra celkem  |  Jádra celkem  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Uzly celkem  |  Uzly celkem  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Nepoužitelná jádra  |  Nepoužitelná jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Pracovní prostory Microsoft.BatchAI  |  Nepoužitelné uzly  |  Nepoužitelné uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Připojení přijato  |  Přijatá připojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  ConnectionActive  |  Aktivní připojení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  ConnectionHandled  |  Manipulovní připojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  CpuUsagePercentageInDouble  |  Procento využití procesoru  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  IOReadBytes  |  Čtení bajtů v iO  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  IOWriteBytes  |  Vypoučovat bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Limit paměti  |  Limit paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Využití paměti  |  Využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  MemoryUsagePercentageInDouble  |  Procento využití paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Čekající transakce  |  Čekající transakce  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Zpracované bloky  |  Zpracované bloky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Zpracované transakce  |  Zpracované transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Transakce ve frontě  |  Transakce ve frontě  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Zpracování požadavku  |  Zpracované požadavky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Blockchain/blockchainČlenové  |  Využití úložiště  |  Využití úložiště  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits  |  Přístupy do mezipaměti  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits0  |  Přístupy do mezipaměti (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits1  |  Přístupy do mezipaměti (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits2  |  Přístupy do mezipaměti (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits3  |  Přístupy do mezipaměti (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits4  |  Přístupy do mezipaměti (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits5  |  Přístupy do mezipaměti (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits6  |  Přístupy do mezipaměti (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits7  |  Přístupy do mezipaměti (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits8  |  Přístupy do mezipaměti (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachehits9  |  Přístupy do mezipaměti (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheLatency  |  Mikrosekunda latence mezipaměti (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachemine  |  Počet neúspěšných přístupů do mezipaměti  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachemisses0  |  Počet neúspěšných přístupů do mezipaměti (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí1  |  Počet neúspěšných přístupů do mezipaměti (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí2  |  Počet neúspěšných přístupů do mezipaměti (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí3  |  Počet neúspěšných přístupů do mezipaměti (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí4  |  Počet neúspěšných přístupů do mezipaměti (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí5  |  Počet neúspěšných přístupů do mezipaměti (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí6  |  Počet neúspěšných přístupů do mezipaměti (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí7  |  Počet neúspěšných přístupů do mezipaměti (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cachemisses8  |  Počet neúspěšných přístupů do mezipaměti (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  počet mezipamětí9  |  Počet neúspěšných přístupů do mezipaměti (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead  |  Čtení mezipaměti  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead0  |  Čtení mezipaměti (střep 0)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead1  |  Čtení mezipaměti (střep 1)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead2  |  Čtení mezipaměti (střep 2)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead3  |  Čtení mezipaměti (střep 3)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead4  |  Čtení mezipaměti (střep 4)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead5  |  Čtení mezipaměti (střep 5)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead6  |  Čtení mezipaměti (střep 6)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead7  |  Čtení mezipaměti (střep 7)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead8  |  Čtení mezipaměti (střep 8)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheRead9  |  Čtení mezipaměti (střep 9)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite  |  Zápis do mezipaměti  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite0  |  Zápis do mezipaměti (střep 0)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite1  |  Zápis do mezipaměti (střep 1)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite2  |  Zápis do mezipaměti (střep 2)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite3  |  Zápis do mezipaměti (střep 3)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite4  |  Zápis do mezipaměti (střep 4)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite5  |  Zápis do mezipaměti (střep 5)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite6  |  Zápis do mezipaměti (střep 6)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite7  |  Zápis do mezipaměti (střep 7)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite8  |  Zápis do mezipaměti (střep 8)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  cacheWrite9  |  Zápis do mezipaměti (střep 9)  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti  |  Připojení klienti  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti0  |  Připojení klienti (střep 0)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti1  |  Připojení klienti (Shard 1)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti2  |  Připojení klienti (Shard 2)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti3  |  Připojení klienti (Shard 3)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti4  |  Připojení klienti (Shard 4)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti5  |  Připojení klienti (Shard 5)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti6  |  Připojení klienti (Shard 6)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti7  |  Připojení klienti (Shard 7)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti8  |  Připojení klienti (Shard 8)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  připojení klienti9  |  Připojení klienti (Shard 9)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  chyby  |  chyby  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů  |  Vystěhované klíče  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů0  |  Vystěhované klíče (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů1  |  Vystěhované klíče (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazovacíklíče2  |  Vystěhované klíče (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů3  |  Vystěhované klíče (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů4  |  Vystěhované klíče (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů5  |  Vystěhované klíče (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů6  |  Vystěhované klíče (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazovacíklíče7  |  Vystěhované klíče (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů8  |  Vystěhované klíče (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  vyřazení klíčů9  |  Vystěhované klíče (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  klávesy expired  |  Klíče s prošlou platností  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys0  |  Klíče s prošlou platností (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys1  |  Klíče s prošlou platností (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys2  |  Klíče s prošlou platností (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys3  |  Klíče s prošlou platností (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys4  |  Klíče s prošlou platností (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys5  |  Klíče s prošlou platností (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys6  |  Klíče s prošlou platností (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys7  |  Klíče s prošlou platností (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys8  |  Klíče s prošlou platností (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  expiredkeys9  |  Klíče s prošlou platností (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get  |  Získá  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get0  |  Získá (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get1  |  Získá (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get2  |  Získá (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get3  |  Získá (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get4  |  Získá (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get5  |  Získá (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get6  |  Získá (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get7  |  Získá (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get8  |  Získá (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  příkazy get9  |  Získá (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond  |  Operace za sekundu  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePo sekundách  |  Operace za sekundu (střep 0)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond1  |  Operace za sekundu (střep 1)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operaceSekunda2  |  Operace za sekundu (střep 2)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operaceSecond3  |  Operace za sekundu (střep 3)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operaceSecond4  |  Operace za sekundu (střep 4)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond5  |  Operace za sekundu (střep 5)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond6  |  Operace za sekundu (střep 6)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond7  |  Operace za sekundu (střep 7)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond8  |  Operace za sekundu (střep 8)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  operacePerSecond9  |  Operace za sekundu (střep 9)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime  |  Procesor  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime0  |  Procesor (úlomek 0)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime1  |  Procesor (střep 1)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime2  |  Procesor (střep 2)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime3  |  Procesor (střep 3)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime4  |  Procesor (střep 4)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime5  |  Procesor (střep 5)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime6  |  Procesor (střep 6)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime7  |  Procesor (úlomek 7)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime8  |  Procesor (střep 8)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  percentProcessorTime9  |  Procesor (střep 9)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad  |  Zatížení serveru  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad0  |  Zatížení serveru (střep 0)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad1  |  Zatížení serveru (střep 1)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad2  |  Zatížení serveru (střep 2)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad3  |  Zatížení serveru (střep 3)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad4  |  Zatížení serveru (střep 4)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad5  |  Zatížení serveru (střep 5)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad6  |  Zatížení serveru (střep 6)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad7  |  Zatížení serveru (střep 7)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad8  |  Zatížení serveru (střep 8)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  serverLoad9  |  Zatížení serveru (střep 9)  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands  |  Sady  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands0  |  Sady (úlomek 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands1  |  Sady (úlomek 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands2  |  Sady (úlomek 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands3  |  Sady (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands4  |  Sady (úlomek 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands5  |  Sady (úlomek 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands6  |  Sady (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands7  |  Sady (úlomek 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands8  |  Sady (úlomek 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  setcommands9  |  Sady (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandszpracovány  |  Celkový počet operací  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Celkový počet operací (střep 0)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Celkový počet operací (střep 1)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Celkový počet operací (střep 2)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Celkový počet operací (střep 3)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Celkový počet operací (střep 4)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Celkový počet operací (střep 5)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Celkový počet operací (střep 6)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Celkový počet operací (střep 7)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Celkový počet operací (střep 8)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Celkový počet operací (střep 9)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys  |  Celkový počet klíčů  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys0  |  Celkový počet klíčů (střep 0)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys1  |  Celkový počet klíčů (střep 1)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys2  |  Celkový počet klíčů (střep 2)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys3  |  Celkový počet klíčů (střep 3)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys4  |  Celkový počet klíčů (střep 4)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys5  |  Celkový počet klíčů (střep 5)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys6  |  Celkový počet klíčů (střep 6)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys7  |  Celkový počet klíčů (střep 7)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys8  |  Celkový počet klíčů (střep 8)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  totalkeys9  |  Celkový počet klíčů (střep 9)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použitá paměť  |  Použitá paměť  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti0  |  Použitá paměť (střep 0)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti1  |  Použitá paměť (střep 1)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti2  |  Použitá paměť (střep 2)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti3  |  Použitá paměť (střep 3)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti4  |  Použitá paměť (střep 4)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti5  |  Použitá paměť (střep 5)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použitá paměť6  |  Použitá paměť (střep 6)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použitá paměť7  |  Použitá paměť (střep 7)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použitá paměť8  |  Použitá paměť (střep 8)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité paměti9  |  Použitá paměť (střep 9)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  procento použité paměti  |  Procento použité paměti  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss  |  Použité paměti RSS  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss0  |  Použité paměti RSS (střep 0)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss1  |  Použité paměti RSS (střep 1)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss2  |  Použité paměti RSS (střep 2)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss3  |  Použité paměti RSS (střep 3)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss4  |  Použité paměti RSS (střep 4)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss5  |  Použité paměti RSS (střep 5)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss6  |  Použité paměti RSS (střep 6)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss7  |  Použité paměti RSS (střep 7)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss8  |  Použité paměti RSS (střep 8)  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Cache/redis  |  použité pamětiRss9  |  Použité paměti RSS (Shard 9)  |  Bajty  |  Maximum | 
| Ne  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Čtení bajtů disku/s  |  Čtení na disku  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Bajty zápisu disku/s  |  Zápis na disk  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Čtení bajtů disku/s  |  Čtení na disku  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Bajty zápisu disku/s  |  Zápis na disk  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicCompute/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts  |  Usedcapacity  |  Využitá kapacita  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Možnost objektů blob  |  Kapacita objektu blob  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Počet objektů BlobCount  |  Počet objektů blob  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Počet kontejnerů objektů blob  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Kapacita indexu  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Souborkapacita  |  Kapacita souboru  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Počet souborů  |  Počet souborů  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Počet sdílení souborů  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Velikost kvóty sdílení souborů  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Počet snímků sdílené složky  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Soubor ShareSnapshotSize  |  Velikost snímku sdílené složky  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Kapacita fronty  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Počet front  |  Počet front  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Počet zpráv fronty  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TabulkaKapacita  |  Kapacita tabulky  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Počet tabulek  |  Počet tabulek  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Počet tabulkových entií  |  Počet entit tabulky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Blokované hovory  |  Blokovaná volání  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  ZnakyTrénované  |  Vycvičené postavy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  ZnakyPřeloženo  |  Přeložené znaky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Chyby klienta  |  Chyby klienta  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  DataIn  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Datový out  |  Data Out  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Latence  |  Latence  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Serverchyby  |  Chyby serveru  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Trvání speechsession  |  Doba trvání relace řeči  |  Sekundy  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Úspěšné volání  |  Úspěšné volání  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Celkový počet volání  |  Celkový počet volání  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Celkemchyby  |  Celkový počet chyb  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  Volání TotalToken  |  Celkový počet volání tokenu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.CognitiveServices/účty  |  TotalTransactions  |  Transakce celkem  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Hloubka fronty datového disku  |  Hloubka fronty datového disku (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Čtení bajtů datového disku/s  |  Čtení bajtů datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace čtení datového disku/s  |  Operace čtení datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty zápisu datového disku/s  |  Bajty za zápis datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace zápisu datového disku/s  |  Operace zápisu datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty čtení disku  |  Bajty čtení disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty zápisu disku  |  Bajty zápisu disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Příchozí toky  |  Příchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Maximální míra vytvoření příchozích toků  |  Maximální míra vytvoření příchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Síťové vstupy  |  Fakturovatelné síťové (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Síť celkem  |  Síť celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Síťové výstupy  |  Vyúčtování sítě (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Síť out celkem  |  Síť out celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty čtení disku operačního systému/s  |  Čtení bajtů/s disku operačního systému (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace čtení disku operačního systému/s  |  Operace čtení disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty zápisu disku operačního systému/s  |  Bajty za zápis disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace zápisu disku operačního systému/s  |  Operace zápisu disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operační systém na disku QD  |  Operační disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty pro čtení na disk/s  |  Čtení bajtů/s disku operačního systému (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace čtení na disk na disk/s  |  Operace čtení disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty zápisu na disk na disk/s  |  Bajty zapisované na disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk na disk/s  |  Operace zápisu disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Odchozí toky  |  Odchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Maximální míra vytvoření odchozích toků  |  Maximální míra vytvoření odchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Na disk QD  |  Datový disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty pro čtení na disk/s  |  Čtení bajtů datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace čtení na disk/s  |  Operace čtení datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Bajty zápisu na disk/s  |  Bajty zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Požadavek na čtení mezipaměti premium data  |  Požadavek na čtení mezipaměti premium data (náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Chybět čtení mezipaměti premium data  |  Premium Data Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Přístupový přístup ke čtení mezipaměti disku premium oS  |  Premium OS Disk Cache Čtení Hit (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Čtení Miss  |  Premium OS Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Hloubka fronty datového disku  |  Hloubka fronty datového disku (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Čtení bajtů datového disku/s  |  Čtení bajtů datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení datového disku/s  |  Operace čtení datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu datového disku/s  |  Bajty za zápis datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu datového disku/s  |  Operace zápisu datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty čtení disku  |  Bajty čtení disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu disku  |  Bajty zápisu disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Příchozí toky  |  Příchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Maximální míra vytvoření příchozích toků  |  Maximální míra vytvoření příchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Síťové vstupy  |  Fakturovatelné síťové (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Síť celkem  |  Síť celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Síťové výstupy  |  Vyúčtování sítě (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Síť out celkem  |  Síť out celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty čtení disku operačního systému/s  |  Čtení bajtů/s disku operačního systému (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení disku operačního systému/s  |  Operace čtení disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu disku operačního systému/s  |  Bajty za zápis disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu disku operačního systému/s  |  Operace zápisu disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operační systém na disku QD  |  Operační disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty pro čtení na disk/s  |  Čtení bajtů/s disku operačního systému (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení na disk na disk/s  |  Operace čtení disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu na disk na disk/s  |  Bajty zapisované na disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk na disk/s  |  Operace zápisu disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Odchozí toky  |  Odchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Maximální míra vytvoření odchozích toků  |  Maximální míra vytvoření odchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Na disk QD  |  Datový disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty pro čtení na disk/s  |  Čtení bajtů datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace čtení na disk/s  |  Operace čtení datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zápisu na disk/s  |  Bajty zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Operace zápisu na disk/s  |  Operace zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Požadavek na čtení mezipaměti premium data  |  Požadavek na čtení mezipaměti premium data (náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Chybět čtení mezipaměti premium data  |  Premium Data Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Přístupový přístup ke čtení mezipaměti disku premium oS  |  Premium OS Disk Cache Čtení Hit (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Čtení Miss  |  Premium OS Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Spotřebované kredity procesoru  |  Spotřebované kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Zbývající kredity procesoru  |  Zbývající kredity procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Hloubka fronty datového disku  |  Hloubka fronty datového disku (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Čtení bajtů datového disku/s  |  Čtení bajtů datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace čtení datového disku/s  |  Operace čtení datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu datového disku/s  |  Bajty za zápis datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace zápisu datového disku/s  |  Operace zápisu datového disku/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty čtení disku  |  Bajty čtení disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu disku  |  Bajty zápisu disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Příchozí toky  |  Příchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximální míra vytvoření příchozích toků  |  Maximální míra vytvoření příchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Síťové vstupy  |  Fakturovatelné síťové (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Síť celkem  |  Síť celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Síťové výstupy  |  Vyúčtování sítě (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Síť out celkem  |  Síť out celkem  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Hloubka fronty disku s operačním systémem  |  Hloubka fronty disku operačního systému (náhled)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty čtení disku operačního systému/s  |  Čtení bajtů/s disku operačního systému (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace čtení disku operačního systému/s  |  Operace čtení disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu disku operačního systému/s  |  Bajty za zápis disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace zápisu disku operačního systému/s  |  Operace zápisu disku operačního systému/s (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operační systém na disku QD  |  Operační disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty pro čtení na disk/s  |  Čtení bajtů/s disku operačního systému (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace čtení na disk na disk/s  |  Operace čtení disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu na disk na disk/s  |  Bajty zapisované na disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk na disk/s  |  Operace zápisu disku operačního systému/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Odchozí toky  |  Odchozí toky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximální míra vytvoření odchozích toků  |  Maximální míra vytvoření odchozích toků (náhled)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Na disk QD  |  Datový disk QD (zastaralé)  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty pro čtení na disk/s  |  Čtení bajtů datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace čtení na disk/s  |  Operace čtení datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bajty zápisu na disk/s  |  Bajty zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu datového disku/s (zastaralé)  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Požadavek na čtení mezipaměti premium data  |  Požadavek na čtení mezipaměti premium data (náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Chybět čtení mezipaměti premium data  |  Premium Data Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Přístupový přístup ke čtení mezipaměti disku premium oS  |  Premium OS Disk Cache Čtení Hit (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Disk Cache Čtení Miss  |  Premium OS Disk Cache Číst Miss (Náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerInstance/containerGroups  |  Využití procesoru  |  Využití procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerInstance/containerGroups  |  Využití paměti  |  Využití paměti  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Přijaté síťové bajty za sekundu  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Přenesené síťové bajty za sekundu  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerRegistry/registry  |  Doba trvání běhu  |  Doba trvání spuštění  |  Milisekund  |  Celkem | 
| **Ano**  | Ne |  Microsoft.ContainerRegistry/registry  |  ÚspěšnýPullCount  |  Úspěšný počet pull  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerRegistry/registry  |  Úspěšný počet stisknuté hod.  |  Úspěšný počet nabízených kliby  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerRegistry/registry  |  TotalPullCount  |  Celkový počet tahů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.ContainerRegistry/registry  |  TotalPushCount  |  Celkový počet nabízených tiskovin  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ContainerService/spravované clustery  |  kube_node_status_allocatable_cpu_cores  |  Celkový počet dostupných jader procesoru ve spravovaném clusteru  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ContainerService/spravované clustery  |  kube_node_status_allocatable_memory_bytes  |  Celkové množství dostupné paměti ve spravovaném clusteru  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ContainerService/spravované clustery  |  kube_node_status_condition  |  Stavy pro různé podmínky uzlu  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ContainerService/spravované clustery  |  kube_pod_status_phase  |  Počet podů podle fáze  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ContainerService/spravované clustery  |  kube_pod_status_ready  |  Počet podů ve stavu Připraveno  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Dostupná kapacita  |  Dostupná kapacita  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BajtyNahranéToCloud  |  Nahrané bajty v cloudu (zařízení)  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BajtůNahrálToCloudPerShare  |  Nahrané bajty v cloudu (sdílet)  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Propustnost stahování v cloudu  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Propustnost stahování v cloudu (sdílení)  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Propustnost nahrávání v cloudu  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Propustnost nahrávání v cloudu (sdílení)  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Využití hypervmemory  |  Edge Compute – využití paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Využití virtuálního procesoru HyperV  |  Edge Compute – procentuální procesor  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NicReadThroughput  |  Propustnost čtení (síť)  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Průchodnost zápisu NIC  |  Propustnost zápisu (síť)  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Celková kapacita  |  Celková kapacita  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataFactory/datafactories  |  Selhání spustí  |  Selhání spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/datafactories  |  Úspěšné spuštění  |  Úspěšné spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  ActivityCancelledRuns  |  Zrušená aktivita spustí metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  ActivityFailedRuns  |  Neúspěšná aktivita spustí metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  Úspěšné spuštění aktivity  |  Úspěšná aktivita spustí metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  FactorySizeInGbUnits  |  Celková velikost továrny (GB jednotka)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  IntegrationRuntimeAvailableMemory  |  Integrační paměť s runtime  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  IntegrationRuntimeAverageTaskPickupDelay  |  Doba trvání fronty za běhu integrace  |  Sekundy  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  IntegraceRuntimeCpuProcento  |  Využití procesoru runtime integrace  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  IntegrationRuntimeQueueLength  |  Délka fronty za běhu integrace  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  MaxAllowedFactorySizeIngbUnits  |  Maximální povolená tovární velikost (jednotka GB)  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  MaxAllowedResourceCount  |  Počet maximálních povolených entit  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  PipelineCancelledRuns  |  Zrušené kanály spouští metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  Spuštění pipelinefailedruns  |  Metriky spuštění neúspěšného kanálu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  PipelineSucceededRuns  |  Úspěšné metriky spuštění kanálu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  ResourceCount  |  Celkový počet entit  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  TriggerCancelledRuns  |  Zrušené aktivační události spustí metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  Spuštění spouštění  |  Metriky spuštění aktivační události se nezdařilo.  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataFactory/továrny  |  TriggerSucceededRuns  |  Úspěšné aktivační události spustí metriky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  JobAUEndedZrušeno  |  Zrušený čas AU  |  Sekundy  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  JobauendedSelhání  |  Neúspěšný čas au  |  Sekundy  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  JobauendedÚspěch  |  Úspěšný čas AU  |  Sekundy  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  Úloha Byla zrušena.  |  Zrušené úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  JobendedSelhání  |  Neúspěšné úlohy  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeAnalytics/účty  |  JobEndedÚspěch  |  Úspěšná pracovní místa  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeStore/účty  |  Čtení dat  |  Čtení dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeStore/účty  |  DataWritten  |  Zapsaná data  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeStore/účty  |  ReadRequests  |  Číst požadavky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DataLakeStore/účty  |  TotalStorage  |  Celkové úložiště  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DataLakeStore/účty  |  Požadavky na zápis  |  Psát požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  active_connections  |  Aktivní připojení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  backup_storage_used  |  Použité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  connections_failed  |  Neúspěšná připojení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  cpu_percent  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  io_consumption_percent  |  IO procent  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  memory_percent  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  seconds_behind_master  |  Zpoždění replikace v sekundách  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  serverlog_storage_percent  |  Procento úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  serverlog_storage_usage  |  Použité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  storage_limit  |  Limit úložiště  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMariaDB/servery  |  storage_used  |  Použité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  active_connections  |  Aktivní připojení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  backup_storage_used  |  Použité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  connections_failed  |  Neúspěšná připojení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  cpu_percent  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  io_consumption_percent  |  IO procent  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  memory_percent  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  seconds_behind_master  |  Zpoždění replikace v sekundách  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  serverlog_storage_percent  |  Procento úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  serverlog_storage_usage  |  Použité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  storage_limit  |  Limit úložiště  |  Bajty  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DBforMySQL/servery  |  storage_used  |  Použité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  active_connections  |  Aktivní připojení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  backup_storage_used  |  Použité úložiště záloh  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  connections_failed  |  Neúspěšná připojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  cpu_percent  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  io_consumption_percent  |  IO procent  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  memory_percent  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  pg_replica_log_delay_in_bytes  |  Maximální zpoždění mezi replikami  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  pg_replica_log_delay_in_seconds  |  Zpoždění replik  |  Sekundy  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  serverlog_storage_limit  |  Limit úložiště protokolu serveru  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  serverlog_storage_percent  |  Procento úložiště protokolu serveru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  serverlog_storage_usage  |  Použité úložiště protokolu serveru  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  storage_limit  |  Limit úložiště  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/servery  |  storage_used  |  Použité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Aktivní připojení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  Iops  |  IOPS  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Procento úložiště  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Použité úložiště  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/Účet  |  digitaltwins.telemetry.uzly  |  Zástupný symbol telemetrie digitálních dvojčat  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Zprávy C2D byly opuštěny.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Dokončené dodávky zpráv C2D  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Zprávy C2D byly odmítnuty.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.methods.selhání  |  Neúspěšná přímá metoda vyvolání  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestVelikost  |  Velikost požadavku na vyvolání přímé metody  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseVelikost  |  Velikost odpovědi přímé metody vyvolání  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Úspěšné přímé vyvolání metody  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.selhání  |  Neúspěšné dvojče čte ze zadního konce  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Velikost odpovědi dvojčete čte ze back-endu  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Úspěšné dvojče čte z back-endu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.selhání  |  Neúspěšné aktualizace dvojčete ze back-endu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Velikost twin aktualizace z back-endu  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Úspěšné aktualizace dvojčat z back-endu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  C2DMessagesVypršela  |  Platnost zpráv C2D vypršela (náhled)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  Konfigurace  |  Metriky konfigurace  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Devices/IotHubs  |  připojenýPočet zařízení  |  Připojená zařízení (náhled)  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Směrování: zprávy doručené do zpráv/událostí  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Směrování: zprávy doručené do centra událostí  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Směrování: zprávy doručené do fronty služby Service Bus  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTémata  |  Směrování: zprávy doručené do tématu sběrnice  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Směrování: zprávy doručené do úložiště  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  objekty blob d2c.endpoints.egress.storage.  |  Směrování: objekty BLOB dodané do úložiště  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Směrování: data dodaná do úložiště  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Směrování: latence zpráv pro zprávy nebo události  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Směrování: latence zpráv pro centrum událostí  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Směrování: latence zpráv pro frontu služby Service Bus  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Směrování: latence zpráv pro téma služby Service Bus  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Směrování: latence zpráv pro úložiště  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Směrování: zprávy telemetrie vynechány   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Směrování: zprávy doručovány do záložní  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Směrování: telemetrické zprávy nekompatibilní  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Směrování: telemetrické zprávy osamocené   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Směrování: doručovány telemetrické zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Pokusy o odeslání telemetrické zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Počet chyb omezení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Odeslané telemetrické zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.selhání  |  Selhání dvojčete čte ze zařízení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Velikost odezvy dvojčete čte ze zařízení  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Úspěšná dvojitá čtení ze zařízení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.selhání  |  Neúspěšné aktualizace dvojčete ze zařízení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Velikost aktualizací dvojčete ze zařízení  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Úspěšné aktualizace dvojčete ze zařízení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Celkový počet použitých zpráv  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Celkové využití dat zařízení  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Celkové využití dat zařízení (náhled)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Připojená zařízení (zastaralé)   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Zařízení celkem (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Dodávky v gridu událostí (náhled)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  Latence eventgridu  |  Latence sítě událostí (náhled)  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Neúspěšné zrušení úlohy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Úspěšné zrušení úlohy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Dokončené úlohy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Neúspěšné vytváření úloh vyvolání metody  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Úspěšné vytváření úloh vyvolání metody  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Neúspěšné vytvoření úloh s dvojčaty  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Úspěšné výtvory úloh s dvojčaty  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Neúspěšné úlohy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.selhání  |  Neúspěšná volání seznam úloh  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Úspěšná volání do seznamu úloh  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Dotazy na úlohy se nezdařilo  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Dotazy na úspěšnou úlohu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Celkový počet zařízení (náhled)  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Neúspěšné dotazy na dvojče  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Velikost výsledku twin queries  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Úspěšné dotazy na dvojče  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/provisioningServices  |  Pokusy o atestaci  |  Pokusy o atestaci  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/provisioningServices  |  Přiřazení zařízení  |  Přiřazená zařízení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Devices/provisioningServices  |  Pokusy o registraci  |  Pokusy o registraci  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Dostupné úložiště  |  Úložiště k dispozici  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Uzavření připojení Cassandra  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra požadovat poplatky  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  CassandraPožadavky  |  Cassandra žádosti  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Využití dat  |  Využití dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Odstranit virtuální síť  |  Odstranit virtuální síť  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Počet dokumentů  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Kvóta dokumentu  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Využití indexu  |  Využití indexu  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Požadavky na metadata  |  Požadavky na metadata  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Poplatek za žádost Mongo  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  MongoPožadavky  |  Mongo žádosti  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Míra požadavků Mongo  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsOdstranit  |  Rychlost odstranění požadavku Mongo  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Rychlost požadavků vložení mongo  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Rychlost požadavků na dotazy Mongo  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsAktualizace  |  Rychlost požadavků na aktualizaci mongo  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Zřízená průchodnost  |  Zřízená propustnost  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Latence replikace  |  Latence replikace P99  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.DocumentDB/databaseAccounts  |  Dostupnost služby  |  Dostupnost služby  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Požadavky celkem  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.DocumentDB/databaseAccounts  |  Jednotky TotalRequest  |  Celkový počet jednotek požadavků  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EnterpriseKnowledgeGraph/služby  |  Počet selhání  |  Failure Count  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.EnterpriseKnowledgeGraph/služby  |  Počet úspěšných  |  Success Count  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.EnterpriseKnowledgeGraph/služby  |  SuccessLatency  |  Latence úspěchu  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.EnterpriseKnowledgeGraph/služby  |  Počet transakcí  |  Počet transakcí  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  DeadLetteredCount  |  Události s mrtvými písmeny  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventGrid/domény  |  DeliveryAttemptFailCount  |  Události doručení se nezdařilo  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  DeliverySuccessCountCount  |  Dodané události  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventGrid/domény  |  DestinationProcessingDurationInMs  |  Doba zpracování cíle  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  Vynechánípočtu událostí  |  Zrušené události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  MatchedEventCount  |  Odpovídající události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  PublishFailCount  |  Publikovat neúspěšné události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  PublishSuccessCount  |  Publikované události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/domény  |  PublishSuccessLatencyInMs  |  Publikovat latenci úspěšnosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Události s mrtvými písmeny  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Události doručení se nezdařilo  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCountCount  |  Dodané události  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Doba zpracování cíle  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/eventSubscriptions  |  Vynechánípočtu událostí  |  Zrušené události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Odpovídající události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Publikovat neúspěšné události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Publikované události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Publikovat latenci úspěšnosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Bezkonkurenční události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/témata  |  PublishFailCount  |  Publikovat neúspěšné události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/témata  |  PublishSuccessCount  |  Publikované události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/témata  |  PublishSuccessLatencyInMs  |  Publikovat latenci úspěšnosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventGrid/témata  |  UnmatchedEventCount  |  Bezkonkurenční události  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Aktivní připojení  |  Aktivní připojení  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  AvailableMemory  |  Paměť k dispozici  |  Procento  |  Maximum | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  CaptureBacklog  |  Zachytit nevyřízené položky.  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Zachycené bajty  |  Zachycené bajty.  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Zachycené zprávy  |  Zachycené zprávy.  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Připojení uzavřeno  |  Připojení uzavřena.  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Otevřená připojení  |  Připojení byla otevřena.  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Procesor  |  Procesor  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.EventHub/clustery  |  Příchozí bajty  |  Příchozí bajty.  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventHub/clustery  |  Příchozí zprávy  |  Příchozí zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventHub/clustery  |  Příchozí požadavky  |  Příchozí požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventHub/clustery  |  OutgoingBytes  |  Odchozí bajty.  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.EventHub/clustery  |  Odchozí zprávy  |  Odchozí zprávy  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Kvótaexceededchyb  |  Kvóta byla překročena chyby.  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Serverchyby  |  Chyby serveru.  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Úspěšné požadavky  |  Úspěšné požadavky  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  ThrottledRequests  |  Omezené požadavky.  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.EventHub/clustery  |  Uživatelské chyby  |  Chyby uživatele.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Aktivní připojení  |  Aktivní připojení  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  CaptureBacklog  |  Zachytit nevyřízené položky.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Zachycené bajty  |  Zachycené bajty.  |  Bajty  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Zachycené zprávy  |  Zachycené zprávy.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Připojení uzavřeno  |  Připojení uzavřena.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Otevřená připojení  |  Připojení byla otevřena.  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHABL  |  Archivovat nevyřízené zprávy (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHAMBS  |  Archivovat propustnost zpráv (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHAMSGS  |  Archivní zprávy (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHINBYTES  |  Příchozí bajty (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHINMBS  |  Příchozí bajty (zastaralé) (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHINMSGS  |  Příchozí zprávy (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHOUTBYTES  |  Odchozí bajty (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHOUTMBS  |  Odchozí bajty (zastaralé) (zastaralé)  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  EHOUTMSGS  |  Odchozí zprávy (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  FUNKCE FAILREQ  |  Neúspěšné požadavky (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Příchozí bajty  |  Příchozí bajty.  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Příchozí zprávy  |  Příchozí zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Příchozí požadavky  |  Příchozí požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  INMSGS  |  Příchozí zprávy (zastaralé) (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Inreqs  |  Příchozí požadavky (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  INTERR  |  Chyby interního serveru (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Miscerr  |  Jiné chyby (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  OutgoingBytes  |  Odchozí bajty.  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  Odchozí zprávy  |  Odchozí zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  OUTMSGS  |  Odchozí zprávy (zastaralé) (zastaralé)  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Kvótaexceededchyb  |  Kvóta byla překročena chyby.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Serverchyby  |  Chyby serveru.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Velikost  |  Velikost  |  Bajty  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Úspěšné požadavky  |  Úspěšné požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  SUCCREQ  |  Úspěšné požadavky (zastaralé)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.EventHub/Namespaces  |  SVRBSY  |  Chyby zaneprázdnění serveru (zastaralé)  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  ThrottledRequests  |  Omezené požadavky.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.EventHub/Namespaces  |  Uživatelské chyby  |  Chyby uživatele.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.HDInsight/clustery  |  Požadavky na brány zařazené do kategorií  |  Požadavky na brány zařazené do kategorií  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.HDInsight/clustery  |  Požadavky na brány  |  Požadavky na brány  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.HDInsight/clustery  |  NumActiveWorkers  |  Počet aktivních pracovníků  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.HDInsight/clustery  |  Škálování požadavků  |  Škálování požadavků  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Insights/AutoscaleSettings  |  Metrická prahová hodnota  |  Prahová hodnota metriky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/AutoscaleSettings  |  Pozorovaná kapacita  |  Pozorovaná kapacita  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/AutoscaleSettings  |  Hodnota ObservedMetric  |  Zjištěná metrická hodnota  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Zahájené akce škálování  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  availabilityResults/availabilityPercentage  |  Dostupnost  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  availabilityResults/count  |  Testy dostupnosti  |  Počet  |  Počet | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  availabilityResults/duration  |  Doba trvání testu dostupnosti  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  browserTimings/networkDuration  |  Čas připojení sítě načítání stránky  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  browserTimings/processingDuration  |  Doba zpracování klienta  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  browserTimings/receiveDuration  |  Doba odezvy příjmu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  browserTimings/sendDuration  |  Čas odeslání požadavku  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  browserTimings/totalDuration  |  Čas načítání stránky prohlížeče  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  závislosti/počet  |  Volání závislostí  |  Počet  |  Počet | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  závislosti/doba trvání  |  Doba trvání závislosti  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  závislosti/selhání  |  Selhání volání závislostí  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  výjimky/prohlížeč  |  Výjimky prohlížečů  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  výjimky/počet  |  Výjimky  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  výjimky/server  |  Výjimky serveru  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  zobrazení/počet stránek  |  Zobrazení stránky  |  Počet  |  Počet | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  pageViews/duration pageViews/duration pageViews/duration pageViews  |  Doba načítání zobrazení stránky  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  performanceCounters/exceptionsPerSecond  |  Míra výjimek  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/memoryAvailableBytes  |  Dostupná paměť  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/processCpuPercentage  |  Procesní procesor  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/processIOBytesPerSecond  |  Rychlost vi.  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/processorCpuPercentage  |  Čas procesoru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/processPrivateBytes  |  Zpracovat soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  performanceCounters/requestExecutionTime  |  Doba spuštění požadavku HTTP  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  performanceCounters/requestsInQueue  |  Požadavky HTTP ve frontě aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  performanceCounters/requestsPerSecond  |  Rychlost požadavků HTTP  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  požadavky/počet  |  Serverové požadavky  |  Počet  |  Počet | 
| **Ano**  | Ne |  Microsoft.Insights/Komponenty  |  žádosti/doba trvání  |  Doba odezvy serveru  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  požadavky/neúspěšné  |  Neúspěšné požadavky  |  Počet  |  Počet | 
| Ne  | Ne |  Microsoft.Insights/Komponenty  |  žádosti/sazba  |  Rychlost požadavků serveru  |  CountPerSecond  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Insights/Komponenty  |  stopy/počet  |  Trasování  |  Počet  |  Počet | 
| **Ano**  | Ne |  Úložiště Microsoft.KeyVault/trezory  |  ServiceApiHit  |  Celkový počet přístupů rozhraní API služby  |  Počet  |  Počet | 
| **Ano**  | Ne |  Úložiště Microsoft.KeyVault/trezory  |  ServiceApiLatency  |  Celková latence rozhraní API služby  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Úložiště Microsoft.KeyVault/trezory  |  ServiceApiResult  |  Celkový počet výsledků rozhraní API služby  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Využití mezipaměti  |  Využití mezipaměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  ContinuousExportMaxLatenessMinutes  |  Minut maximální zpoždění nepřetržitého exportu  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  ContinuousExportNumOfRecordsExported  |  Nepřetržitý export - číslo exportovaných záznamů  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  ContinuousExportPendingCount  |  Počet čekajícího nepřetržitého exportu  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Výsledek průběžného exportu  |  Výsledek nepřetržitého exportu  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Procesor  |  Procesor  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  UdálostiProcessedForEventHubs  |  Zpracované události (pro centra událostí/IoT)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Využití exportu  |  Využití exportu  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  IngestionLatencyInSeconds  |  Latence požití (v sekundách)  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Výsledek požití  |  Výsledek požití  |  Počet  |  Počet | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  IngestionUtilizace  |  Využití požití  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  PožitíVolumeInMB  |  Svazek požití (v MB)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Keepalive  |  Udržujte naživu  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Trvání dotazu  |  Doba trvání dotazu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  SteamingestRequestRate  |  Rychlost požadavků ingestování datových proudů  |  Počet  |  RateRequestsPerSecond | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  StreamingIngestData  |  Rychlost streamování ingestování dat  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  Streamovánítrvání  |  Doba trvání ingestování streamování  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Kusto/Clustery  |  StreamingIngestResults  |  Výsledek ingestování streamování  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  Latence akce  |  Latence akce   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Dokončené akce  |  Dokončené akce   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Akce se nezdařily.  |  Akce se nezdařily.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  AkceSkipped  |  Akce přeskočeny   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  AkceZahájené  |  Zahájené akce   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Akce proběhly úspěšně.  |  Akce proběhly úspěšně.   |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  AkceSuccessLatency  |  Latence úspěšnosti akce   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Akce Akce  |  Akce škrticí události  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Využití paměti konektoru pro prostředí služby Integrace  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Využití procesoru konektoru pro prostředí služby integrace  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Využití paměti pracovního postupu pro prostředí integrační ch služeb  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Využití procesoru pracovního postupu pro prostředí integračních služeb  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Procento selhání systému Run  |  Procento selhání spuštění  |  Procento  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Spustit latenci  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Spuštěnízrušeno  |  Spuštění byla zrušena.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Spuštění Dokončeno  |  Spuštění dokončeno  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Spuštění se nezdařilo.  |  Spuštění se nezdařilo.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Spuštění  |  Spuštění bylo zahájeno.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Spuštění proběhlo úspěšně.  |  Spuštění proběhlo úspěšně.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Akce RunStartŠkrtdEvents  |  Spustit události s omezením spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Spustit latenci úspěchu  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Spustit události s omezením  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Aktivovat latenci požáru   |  Sekundy  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  Latence spouště  |  Latence aktivační události   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Aktivační události dokončeny  |  Aktivační události dokončeny   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Aktivační události se nezdařily  |  Aktivační události se nezdařily.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Aktivační události aktivována   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Přeskočené aktivační události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Aktivační události byly spuštěny.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  Aktivační události proběhly úspěšně.  |  Aktivační události byly úspěšné.   |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Aktivace latence úspěšnosti   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Události s omezením aktivace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  Latence akce  |  Latence akce   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Dokončené akce  |  Dokončené akce   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Akce se nezdařily.  |  Akce se nezdařily.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  AkceSkipped  |  Akce přeskočeny   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  AkceZahájené  |  Zahájené akce   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Akce proběhly úspěšně.  |  Akce proběhly úspěšně.   |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  AkceSuccessLatency  |  Latence úspěšnosti akce   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Akce Akce  |  Akce škrticí události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillableActionExecutions  |  Fakturovatelné spuštění akcí  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillableTriggerExecutions  |  Fakturovatelné spuštění aktivační události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageNativeOperation  |  Využití fakturace pro nativní spuštění operací  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageNativeOperation  |  Využití fakturace pro nativní spuštění operací  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageStandardConnector  |  Využití fakturace pro spuštění standardního konektoru  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageStandardConnector  |  Využití fakturace pro spuštění standardního konektoru  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageStorageConsumptionS  |  Využití fakturace pro spouštění spotřeby úložiště  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  BillingUsageStorageConsumptionS  |  Využití fakturace pro spouštění spotřeby úložiště  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Procento selhání systému Run  |  Procento selhání spuštění  |  Procento  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  RunLatency  |  Spustit latenci  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Spuštěnízrušeno  |  Spuštění byla zrušena.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Spuštění Dokončeno  |  Spuštění dokončeno  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Spuštění se nezdařilo.  |  Spuštění se nezdařilo.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Spuštění  |  Spuštění bylo zahájeno.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Spuštění proběhlo úspěšně.  |  Spuštění proběhlo úspěšně.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Akce RunStartŠkrtdEvents  |  Spustit události s omezením spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  RunSuccessLatency  |  Spustit latenci úspěchu  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  RunThrottledEvents  |  Spustit události s omezením  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  TotalBillableExecutions  |  Fakturovatelná spuštění celkem  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  TriggerFireLatency  |  Aktivovat latenci požáru   |  Sekundy  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  Latence spouště  |  Latence aktivační události   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Aktivační události dokončeny  |  Aktivační události dokončeny   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Aktivační události se nezdařily  |  Aktivační události se nezdařily.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  TriggersFired  |  Aktivační události aktivována   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  TriggersSkipped  |  Přeskočené aktivační události  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  TriggersStarted  |  Aktivační události byly spuštěny.   |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  Aktivační události proběhly úspěšně.  |  Aktivační události byly úspěšné.   |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Logic/pracovní postupy  |  TriggerSuccessLatency  |  Aktivace latence úspěšnosti   |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Logic/pracovní postupy  |  TriggerThrottledEvents  |  Události s omezením aktivace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Aktivní jádra  |  Aktivní jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Aktivní uzly  |  Aktivní uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Dokončené spuštění  |  Dokončené spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Selhání spuštění  |  Selhání spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nečinná jádra  |  Nečinná jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nečinné uzly  |  Nečinné uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Opuštění jader  |  Opuštění jader  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Opuštění uzlů  |  Opuštění uzlů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nasazení modelu se nezdařilo.  |  Nasazení modelu se nezdařilo.  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Zahájení nasazení modelu  |  Zahájení nasazení modelu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nasazení modelu bylo úspěšné.  |  Nasazení modelu bylo úspěšné.  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Registr modelů se nezdařil.  |  Registr modelů se nezdařil.  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Registr modelů byl úspěšný.  |  Registr modelů byl úspěšný.  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Předpjatojádra  |  Předpjatojádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Předpisované uzly  |  Předpisované uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Procento využití kvóty  |  Procento využití kvóty  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Spuštěno spuštění  |  Spuštěno spuštění  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Jádra celkem  |  Jádra celkem  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Uzly celkem  |  Uzly celkem  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nepoužitelná jádra  |  Nepoužitelná jádra  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.MachineLearningServices/pracovní prostory  |  Nepoužitelné uzly  |  Nepoužitelné uzly  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Maps/účty  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Maps/účty  |  Využití  |  Využití  |  Počet  |  Počet | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  Počet majetku  |  Počet majetku  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  AssetQuota  |  Kvóta aktiv  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  Procento použitého assetaused  |  Procento použité kvóty aktiv  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Počet zásad klíče obsahu  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Kvóta zásad klíče obsahu  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  Procento contentkeypolicyquotausedprocento  |  Procento použité kvóty zásad obsahu  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Počet zásad streamování  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Kvóta zásad streamování  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Procento použité kvóty zásad streamování  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices/streamingKoncové body  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices/streamingKoncové body  |  Žádosti  |  Žádosti  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Media/mediaservices/streamingKoncové body  |  SuccessE2ELatency  |  Úspěšnost latence od konce ke konci  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Počet gcpausetotalcount  |  Počet pozastavení GC  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Čas gcpausetotaltime  |  Celkový čas pozastavení GC  |  Milisekund  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Bajty maxoldgenmemorypool  |  Maximální dostupná velikost dat staré generace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBajty  |  Velikost dat staré generace  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBajty  |  Zvýšit velikost dat staré generace  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procento využití procesoru služby  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Přiřazena paměť služby  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Servisní paměť Max  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Použitá servisní paměť  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsageProcento  |  Procento využití procesoru systému  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Počet chyb Tomcat  |  Globální chyba Společnosti Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat Celkem přijatých bajtů  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maximální čas požadavku Tomcat  |  Milisekund  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Celkový počet požadavků na kotomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat Žádost Celkem Krát  |  Milisekund  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Průměrná doba požadavku Tomcat  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Celkový počet odeslaných bajtů kotomak  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Počet živých relací Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Počet aktivních aktivit relace Kotomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxČas  |  Tomcat Session Max Alive Čas  |  Milisekund  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Počet vytvořených v tomcatSession  |  Počet vytvořených relací Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  Počet buněk TomcatSessionExpiredCount  |  Počet prošlých platností relace Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Počet odmítnutých relace Tomcat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBajty  |  Povýšení na velikost dat mladé generace  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Přidělený objemový fond  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Celková logická velikost fondu svazků  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Průměrná latence  |  Průměrná latence čtení  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Průměrná latence zápisu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  Přečtěte si iops  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Logická velikost svazku  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Velikost snímku svazku  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  Napište iops  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Celkový čas aplikační brány  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Požadavky za minutu na zdravého hostitele  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Čas připojení back-endu  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Doba odezvy prvního bajtu back-endu  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Doba odezvy posledního bajtu back-endu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Stav backendové odpovědi  |  Stav back-endové odpovědi  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Blokovaný počet  |  Brána firewall pro blokované požadavky webové aplikace distribuce pravidel  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Počet blokovaných požadavků brány firewall webové aplikace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Přijaté bajty  |  Přijaté bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  BajtůOdeslání  |  Odeslané bajty  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  Kapacitní jednotky  |  Jednotky aktuální kapacity  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  KlientRtt  |  RTT klienta  |  Milisekund  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  Výpočetní jednotky  |  Aktuální výpočetní jednotky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Aktuální připojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Požadavky na selhání  |  Neúspěšné požadavky  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Počet hostitelů v pořádku  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Distribuce celkového pravidla brány firewall webových aplikací  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Stav odpovědi  |  Stav odpovědi  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Network/applicationGateways  |  Propustnost  |  Propustnost  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Protokol TL  |  Protokol TLS klienta  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Požadavky celkem  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/applicationGateways  |  Nezdravýpočet hostitelů  |  Počet hostitelů není v pořádku  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/azurefirewalls  |  AplikaceRuleHit  |  Počet přístupů pravidel aplikace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/azurefirewalls  |  Data zpracována  |  Zpracovávaná data  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/azurefirewalls  |  Stav brány firewall  |  Stav brány firewall  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Počet přístupů síťových pravidel  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/azurefirewalls  |  SNATPortUtilizace  |  Využití portu SNAT  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/připojení  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/připojení  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/dnszones  |  QueryVolume  |  Svazek dotazu  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Network/dnszones  |  Využití kapacity recordSetCapacity  |  Využití kapacity sady záznamů  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/dnszones  |  RecordSetCount  |  Počet sad záznamů  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Arp Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRouteCircuits  |  Dostupnost protokolu BGP  |  Dostupnost protokolu BGP  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  Globální DosahBitsInPerSecond  |  Globální DosahBitsInPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  VynechánoInBitsPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  VynechánoOutBitsPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| Ne  | Ne |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  Stav správce  |  Stav správce  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  Úroveň RxLight  |  Úroveň RxLight  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  Procento backendhealth  |  Procento stavu back-endu  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Počet požadavků back-endu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Latence back-endu  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  Fakturovatelná velikost odpovědi  |  Fakturovatelná velikost odpovědi  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  RequestCount  |  Počet požadavků  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  RequestSize  |  Velikost požadavku  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  Velikost odpovědi  |  Velikost odpovědi  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  Celková latence  |  Celková latence  |  Milisekund  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Počet požadavků brány firewall webové aplikace  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Network/loadBalancers  |  Přidělenésnatporty  |  Přidělené porty SNAT (náhled)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  Bytecount  |  Počet bajtů  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Stav sondy stavu  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  PacketCount  |  Počet paketů  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Počet připojení SNAT  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  POČET SYNCount  |  Počet SYN  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Network/loadBalancers  |  PoužitéPorty  |  Použité porty SNAT (náhled)  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/loadBalancers  |  VipDostupnost  |  Dostupnost cesty k datům  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/networkInterfaces  |  Míra obdržené hod.  |  Přijaté bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Odeslané bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/networkInterfaces  |  Míra paketů ReceivedRate  |  Přijaté pakety  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Odeslané pakety  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/networkWatchers/connectionMonitory  |  AverageRoundtripMs  |  Vzdušnou dobu odezvy (ms)  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/networkWatchers/connectionMonitory  |  ChecksFailedPercent  |  Kontroluje procento se nezdařilo (náhled)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/networkWatchers/connectionMonitory  |  SondaFailedPercent  |  % sond se nezdařilo.  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/networkWatchers/connectionMonitory  |  RoundTripTimeMs  |  Doba odezvy (ms) (náhled)  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Bytecount  |  Počet bajtů  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  BajtůKleslDDoS  |  Příchozí bajty vynechány DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  BajtůForwardedDDoS  |  Příchozí bajty předaných DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  BajtůInDDoS  |  DDoS příchozích bajtů  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Pakety DDoSTriggerSYN  |  Příchozí pakety SYN pro aktivaci zmírnění ddos  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Pakety DDoSTriggerTCP  |  Příchozí pakety TCP pro aktivaci zmírnění ddos  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Pakety DDoSTriggerUDP  |  Příchozí pakety UDP pro aktivaci zmírnění ddos  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Ifunderddosattack  |  Pod DDoS útokem nebo ne  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  PacketCount  |  Počet paketů  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  PaketyDroppedDDoS  |  Příchozí pakety vynechány DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  PacketsForwardedDDoS  |  Příchozí pakety předané DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  PacketsInDDoS  |  Vstupní pakety DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  Počet synů  |  Počet SYN  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPBytesDroppedDDoS  |  Příchozí bajty TCP vynechány DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPBytesForwardedDDoS  |  Příchozí tcp bajty předané DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPBytesInDDoS  |  Příchozí tcp bajty DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPPacketsDroppedDDoS  |  Příchozí pakety TCP vynechanou DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPPacketsForwardedDDoS  |  Příchozí pakety TCP předané DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  TCPPacketsInDDoS  |  Příchozí pakety TCP DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPBytesDroppedDDoS  |  Příchozí udp bajty vynechány DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPBytesForwardedDDoS  |  Příchozí udp předané ddos  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPBytesInDDoS  |  Příchozí UDP bajty DDoS  |  BajtyPerSekunda  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPPacketsDroppedDDoS  |  Příchozí pakety UDP vynechanou DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPPacketsForwardedDDoS  |  Příchozí pakety UDP předané DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  UDPPacketsInDDoS  |  Příchozí UDP pakety DDoS  |  CountPerSecond  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/publicIPAdresy  |  VipDostupnost  |  Dostupnost cesty k datům  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Stav koncového bodu podle koncového bodu  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Vrácené dotazy podle koncového bodu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  Průměrná šířka pásma  |  Šířka pásma brány S2S  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  Šířka pásma P2S  |  Šířka pásma brány P2S  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Počet připojení P2S  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Šířka pásma tunelu  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Tunelové propojení bajtů  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Přetažení paketu ts neodpovídajícího přenosu tunelového přenosu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pakety odchozího přenosu tunelového propojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Příchozí bajty příchozího přenosu dat tunelového propojení  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Přetažení paketu neshody příchozího přenosu dat v tunelovém přenosu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Pakety příchozího přenosu dat do tunelového propojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Doba odezvy pro příkazy Ping k virtuálnímu virtuálnímu virtuálnímu virtuálnímu okamžiku  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Neúspěšný příkaz ping pro virtuální hospodařící s virtuálním mem  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  Příchozí  |  Příchozí zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  požadavky incoming.all.failedrequests  |  Všechny příchozí neúspěšné požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  incoming.all.requests  |  Všechny příchozí požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  incoming.scheduled  |  Odeslaná naplánovaná nabízená oznámení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  incoming.scheduled.cancel  |  Naplánovaná nabízená oznámení byla zrušena.  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  instalace.všechny  |  Operace správy instalace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  installation.delete  |  Odstranit instalační operace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  installation.get  |  Získat instalační operace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  installation.patch  |  Operace instalace opravy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  instalace.upsert  |  Vytvořit nebo aktualizovat instalační operace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  notificationhub.push  |  Všechna odchozí oznámení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.allpns.badorexpiredchannel  |  Chybné chyby kanálu nebo chyby kanálu s ukončenou platností  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozí.allpns.channel  |  Chyby kanálu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.allpns.invalidpayload  |  Chyby datové části  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozí.allpns.pns  |  Chyby systému externích oznámení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.allpns.success  |  Úspěšná oznámení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.apns.badchannel  |  Chyba chybného kanálu APNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.apns.expiredchannel  |  Chyba kanálu apns vypršela  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.apns.invalidcredentials  |  Chyby autorizace APNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Chyba neplatné velikosti oznámení APNs  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.apns.pnserror  |  Chyby APNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.apns.success  |  Úspěšná oznámení APNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozího.gcm.authentication  |  Chyby ověřování globálního katalogu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.badchannel  |  Chyba chybného kanálu GCM  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.expiredchannel  |  Chyba kanálu vypršení platnosti gcm  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.invalidcredentials  |  Chyby autorizace gcm (neplatná pověření)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Formát neplatného oznámení globálního katalogu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Chyba neplatné velikosti oznámení v rámci globálního katalogu  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.pnserror  |  Chyby GCM  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.success  |  Úspěšná oznámení GCM  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.throttled  |  Oznámení s omezením gcm  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.gcm.wrongchannel  |  Chyba nesprávného kanálu gcm  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozího.mpns.authentication  |  Chyby ověřování souborů MPN  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.badchannel  |  Chyba chybného kanálu MPNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.channelodpojen  |  Kanál MPNS odpojen  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.dropped  |  Zrušená oznámení mpns  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.invalidcredentials  |  Neplatná pověření protokolu MPNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Formát oznámení protokolu MPNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozí.mpns.pns  |  Chyby mpns  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.úspěch  |  Úspěšná oznámení mpns  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.mpns.omezena  |  Oznámení s omezením systému MPNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  chyba odchozí.wns.authentication  |  Chyby ověřování služby WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.badchannel  |  Chyba chybného kanálu WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.channelodpojen  |  Kanál WNS odpojen  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.channelthrottled  |  Kanál WNS s omezením  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.dropped  |  Zrušená oznámení systému WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.expiredchannel  |  Chyba kanálu vypršela platnost i wns  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.invalidcredentials  |  Chyby autorizace aktualizace WNS (neplatná pověření)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Neplatný formát oznámení systému WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Chyba neplatné velikosti oznámení služba WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.invalidtoken  |  Chyby autorizace wns (neplatný token)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.pnserror  |  Chyby wns  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.success  |  Úspěšná oznámení systému WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.škrtil  |  Oznámení s omezením systému WNS  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.tokenproviderunreachable  |  Chyby autorizace WNS (nedostupné)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  odchozí.wns.wrongtoken  |  Chyby autorizace wns (nesprávný token)  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  registration.all  |  Registrační operace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  registration.create  |  Operace vytvoření registrace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  registration.delete  |  Registrace odstranit operace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  registrace.získat  |  Operace čtení registrace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  registration.update  |  Operace aktualizace registrace  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.NotificationHubs/Obory názvů/NotificationHubs  |  scheduled.pending  |  Nevyřízená naplánovaná oznámení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% dostupné paměti  |  % dostupné paměti  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% k dispozici odkládací prostor  |  % dostupného odkládacího prostoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% potvrzených bajtů, které se používají  |  % potvrzených bajtů je používáno  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% času DPC  |  % času DPC  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% volných inodů  |  % volných inodů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% volného místa  |  % volného místa  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% volného místa  |  % volného místa  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% doba nečinnosti  |  % doby nečinnosti  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% doba přerušení  |  % času přerušení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% Čekací doba IO  |  % čekací doby vi  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% pěkný čas  |  % Pěkný čas  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% privilegovaný čas  |  % privilegovaného času  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% času procesoru  |  Procesorový čas v %  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% času procesoru  |  Procesorový čas v %  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% Použité Inody  |  % použitých inodů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% použité paměti  |  % použité paměti  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% využitý prostor  |  % využitého místa  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% využitý odkládací prostor  |  % použitého odkládacího prostoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_% uživatelský čas  |  % času uživatele  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Available Bajty  |  Dostupné mbajty  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Available mbajtů paměti  |  Dostupná paměť MBytes  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Available mbajtů swap  |  K dispozici mbytes swap  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/čtení  |  Vně disku sec/čtení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/čtení  |  Vně disku sec/čtení  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Avg. Diskový sk/Přenos  |  Vznětový disk ový s/přenos  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/zápis  |  Vg. Disk sec/Zápis  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Avg. doba disku/zápis  |  Vg. Disk sec/Zápis  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Bytes přijato/s  |  Přijaté bajty/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Bytes odesláno/s  |  Odeslané bajty/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Bytes celkem/s  |  Celkový počet bajtů/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Current délka fronty disku  |  Aktuální délka fronty disku  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk čtení bajtů/s  |  Čtení bajtů disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk čtení/s  |  Čtení disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk čtení/s  |  Čtení disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk převody/s  |  Přenosy disků/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk převody/s  |  Přenosy disků/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk zápis bajtů/s  |  Bajty zápisu disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk zápisy/s  |  Zápisy na disk/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Disk zápisy/s  |  Zápisy na disk/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Free megabajty  |  Megabajty zdarma  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Free megabajty  |  Megabajty zdarma  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Free fyzická paměť  |  Volná fyzická paměť  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Free místa v stránkovacích souborech  |  Volné místo v stránkovacích souborech  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Free virtuální paměti  |  Virtuální paměť zdarma  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Logical bajty disku/s  |  Bajty logického disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Page čtení/s  |  Čtení stránky/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Page zápisy/s  |  Zápisy stránky/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Pages/s  |  Stránky/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Pct privilegovaný čas  |  Pct privilegovaný čas  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Pct uživatelský čas  |  Pct uživatelský čas  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Physical bajty disku/s  |  Bajty fyzického disku/s  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Processes  |  Procesy  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Processor délka fronty  |  Délka fronty procesoru  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Size uloženy v stránkovacích souborech  |  Velikost uložená v stránkovacích souborech  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total bajtů  |  Celkový počet bajtů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  přijaté bajty Average_Total  |  Celkový počet přijatých bajtů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  přenesené Average_Total bajty  |  Celkový počet přenesených bajtů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total Kolize  |  Celkový počet kolizí  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total přijaté pakety  |  Celkový počet přijatých paketů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total přenášených paketů  |  Celkový počet přenesených paketů  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total chybr  |  Celkový počet chyb Rx  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Total chyby Tx  |  Celkový počet chyb Tx  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Uptime  |  Uptime  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Used bajtů swapové mezery  |  Použité mbytes swap ové místo  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Used paměťové kby  |  Kby využité paměti  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Used paměti MBytes  |  Mbytes použité paměti  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Users  |  Uživatelé  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Average_Virtual sdílená paměť  |  Virtuální sdílená paměť  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Událost  |  Událost  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.OperationalInsights/pracovní prostory  |  Prezenční signál  |  Prezenční signál  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.OperationalInsights/pracovní prostory  |  Aktualizace  |  Aktualizace  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.PowerBIDedicated/kapacity  |  memory_metric  |  Memory (Paměť)  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.PowerBIDedicated/kapacity  |  memory_thrashing_metric  |  Paměť výprask (datové sady)  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.PowerBIDedicated/kapacity  |  qpu_high_utilization_metric  |  Vysoké využití procesoru  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.PowerBIDedicated/kapacity  |  Trvání dotazu  |  Doba trvání dotazu (datové sady)  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.PowerBIDedicated/kapacity  |  QueryPoolJobQueueLength  |  Délka fronty úlohfondu dotazů (datové sady)  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  Aktivní připojení  |  Aktivní připojení  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  Aktivní posluchači  |  Aktivní posluchači  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.Relay/Namespaces  |  BytesPřevedeno  |  BytesPřevedeno  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  Chyba ListenerConnections-ClientError  |  Chyba ListenerConnections-ClientError  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  ListenerConnections-Úspěch  |  ListenerConnections-Úspěch  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  Chyba SenderConnections-ClientError  |  Chyba SenderConnections-ClientError  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  Chyba senderConnections-ServerError  |  Chyba senderConnections-ServerError  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  SenderConnections-Úspěch  |  SenderConnections-Úspěch  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.Relay/Namespaces  |  OdesílatelOdpojidne  |  OdesílatelOdpojidne  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Search/searchServices  |  SearchLatency  |  Latence hledání  |  Sekundy  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Search/searchServices  |  Vyhledávací dotazyPerSecond  |  Vyhledávací dotazy za sekundu  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Search/searchServices  |  OmezeníVyhledávací dotazyProcento  |  Procento dotazů s omezením  |  Procento  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Aktivní připojení  |  Aktivní připojení  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Aktivní zprávy  |  Počet aktivních zpráv ve frontě nebo tématu.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Připojení uzavřeno  |  Připojení uzavřena.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Otevřená připojení  |  Připojení byla otevřena.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  CPUXNS  |  PROCESOR (zastaralé)  |  Procento  |  Maximum | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Zprávy s mrtvým písmem  |  Počet nedoručených zpráv ve frontě nebo tématu.  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.ServiceBus  |  Příchozí zprávy  |  Příchozí zprávy  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.ServiceBus  |  Příchozí požadavky  |  Příchozí požadavky  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Zprávy  |  Počet zpráv ve frontě nebo tématu.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Využití prostředků_prostředků názvů  |  Procesor  |  Procento  |  Maximum | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Využití paměti oboru názvů  |  Využití paměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Obory názvů Microsoft.ServiceBus  |  Odchozí zprávy  |  Odchozí zprávy  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Naplánované zprávy  |  Počet naplánovaných zpráv ve frontě nebo tématu.  |  Počet  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Serverchyby  |  Chyby serveru.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Velikost  |  Velikost  |  Bajty  |  Průměr | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Úspěšné požadavky  |  Úspěšné požadavky  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  ThrottledRequests  |  Omezené požadavky.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  Uživatelské chyby  |  Chyby uživatele.  |  Počet  |  Celkem | 
| Ne  | Ne |  Obory názvů Microsoft.ServiceBus  |  WSXNS  |  Využití paměti (zastaralé)  |  Procento  |  Maximum | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  ActualCpu  |  ActualCpu  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Skutečná paměť  |  Skutečná paměť  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Přidělení procesoru  |  Přidělení procesoru  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Přidělená paměť  |  Přidělená paměť  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Stav aplikace  |  Stav aplikace  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  ContainerStatus  |  ContainerStatus  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Využití procesoru  |  Využití procesoru  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Využití paměti  |  Využití paměti  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Restartovatcount  |  Restartovatcount  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.ServiceFabricMesh/aplikace  |  Stav služby  |  Stav služby  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Počet připojení  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  Příchozí provoz  |  Příchozí provoz  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  Počet zpráv  |  Počet zpráv  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  Odchozí provoz  |  Odchozí provoz  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  Chyby systému  |  Systémové chyby  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.SignalRService/SignalR  |  Uživatelské chyby  |  Uživatelské chyby  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Průměrné procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Čtení bajtů vi  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  IO bajty napsané  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  io_requests  |  Počet požadavků vi  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Vyhrazený úložný prostor  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Využitý úložný prostor  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Počet virtuálních jader  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.SQL/servery  |  database_dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.SQL/servery  |  database_storage_used  |  Využitý datový prostor  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.SQL/servery  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.SQL/servery  |  dtu_used  |  Používá se DTU  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.SQL/servery  |  storage_used  |  Využitý datový prostor  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  allocated_data_storage  |  Přidělený datový prostor  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  app_cpu_billed  |  Fakturováno s procesorem aplikace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  app_cpu_percent  |  Procento procesoru aplikace  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  app_memory_percent  |  Procento paměti aplikace  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  blocked_by_firewall  |  Blokováno bránou firewall  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  cache_hit_percent  |  Procento přístupů do mezipaměti  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  cache_used_percent  |  Procento použité mezipaměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  connection_failed  |  Neúspěšná připojení  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  connection_successful  |  Úspěšná připojení  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  cpu_limit  |  Limit procesoru  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  cpu_used  |  Použitý procesor  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  Zablokování  |  Zablokování  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  dtu_limit  |  DTU Limit  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  dtu_used  |  Používá se DTU  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  dwu_consumption_percent  |  Procento DWU  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  dwu_limit  |  DWU limit  |  Počet  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  dwu_used  |  Použitý DWU  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  local_tempdb_usage_percent  |  Procento místní databáze tempdb  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  log_write_percent  |  Procento vio protokolu  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  memory_usage_percent  |  Procento paměti  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  sessions_percent  |  Procento návštěv  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  úložiště  |  Využitý datový prostor  |  Bajty  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  storage_percent  |  Procento využitého datového prostoru  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  tempdb_data_size  |  Velikost datového souboru Tempdb kilobajty  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  tempdb_log_size  |  Velikost souboru protokolu databáze Tempdb kilobajty  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/databáze  |  tempdb_log_used_percent  |  Použitý protokol procenta databáze dat  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  workers_percent  |  Procento pracovníků  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/databáze  |  xtp_storage_percent  |  Procento úložiště OLTP v paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  allocated_data_storage  |  Přidělený datový prostor  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  allocated_data_storage_percent  |  Procento přidělené datového prostoru  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  cpu_limit  |  Limit procesoru  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  cpu_used  |  Použitý procesor  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_allocated_data_storage  |  Přidělený datový prostor  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_cpu_limit  |  Limit procesoru  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_cpu_percent  |  Procento CPU  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_cpu_used  |  Použitý procesor  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_eDTU_used  |  použitý eDTU  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_log_write_percent  |  Procento vio protokolu  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_sessions_percent  |  Procento návštěv  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_storage_used  |  Využitý datový prostor  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.Sql/servery/elastické bazény  |  database_workers_percent  |  Procento pracovníků  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  dtu_consumption_percent  |  Procento DTU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  eDTU_limit  |  limit eDTU  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  eDTU_used  |  použitý eDTU  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  log_write_percent  |  Procento vio protokolu  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  physical_data_read_percent  |  Procento datových V/V  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  sessions_percent  |  Procento návštěv  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  storage_limit  |  Maximální velikost dat  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  storage_percent  |  Procento využitého datového prostoru  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  storage_used  |  Využitý datový prostor  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  tempdb_data_size  |  Velikost datového souboru Tempdb kilobajty  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  tempdb_log_size  |  Velikost souboru protokolu databáze Tempdb kilobajty  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.Sql/servery/elastické bazény  |  tempdb_log_used_percent  |  Použitý protokol procenta databáze dat  |  Procento  |  Maximum | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  workers_percent  |  Procento pracovníků  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Sql/servery/elastické bazény  |  xtp_storage_percent  |  Procento úložiště OLTP v paměti  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts  |  Usedcapacity  |  Využitá kapacita  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Možnost objektů blob  |  Kapacita objektu blob  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Počet objektů BlobCount  |  Počet objektů blob  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Počet kontejnerů objektů blob  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Kapacita indexu  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/blobServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Souborkapacita  |  Kapacita souboru  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Počet souborů  |  Počet souborů  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Počet sdílení souborů  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Velikost kvóty sdílení souborů  |  Bajty  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Počet snímků sdílené složky  |  Počet  |  Průměr | 
| Ne  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Soubor ShareSnapshotSize  |  Velikost snímku sdílené složky  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/fileServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Kapacita fronty  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  Počet front  |  Počet front  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Počet zpráv fronty  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/queueServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Dostupnost  |  Dostupnost  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Výchozí přenos dat  |  Výchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Příchozí přenos dat  |  Příchozí přenos dat  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Celková latence při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence serveru při úspěchu  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  TabulkaKapacita  |  Kapacita tabulky  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Počet tabulek  |  Počet tabulek  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Počet tabulkových entií  |  Počet entit tabulky  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.Storage/storageAccounts/tableServices  |  Transakce  |  Transakce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Klientské ops  |  Celkový počet viponek klienta  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Latence klienta  |  Průměrná latence klienta  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  ClientlockiOPS  |  VĚTEV zámku klienta  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientMetadataReadIOPS  |  Metadata klienta čtení VOPS  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientMetadataWriteIOPS  |  VIOP zápisu metadat klienta  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientreadiOPS  |  Klient číst vops  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientReadThroughput  |  Průměrná propustnost čtení mezipaměti  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientwriteIOPS  |  Klient zapsat viopy  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  KlientWriteThroughput  |  Průměrná propustnost zápisu do mezipaměti  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Propustnost asynchronního zápisu StorageTarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Správce výplně TargetFillput  |  Propustnost výplně StorageTarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Stav cíle úložiště  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Optimalizace pro storagetarget  |  Celkový počet vyskladnění cílů úložiště  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetLatency  |  Latence cíle úložiště  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  Viposlužby pro čtení metadat StorageTarget  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  Viposlužby zápisu metadat StorageTarget  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  ÚložištěCílReadAheadThroughputput  |  Propustnost pročtení dopředu StorageTarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Zásobníky připravenosti cíl storageTarget  |  Vyčitatá ops pro čtení storagetarget  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Propustnost zápisu StorageTarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Celková propustnost čtení storagetarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Celková propustnost zápisu StorageTarget  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  Vyps atleta cílová možnost zápisu StorageTarget  |  Počet  |  Průměr | 
| **Ano**  | Ne |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  Počet  |  Průměr | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  Výsledek serverové synchronizuje  |  Výsledek synchronizace relace  |  Počet  |  Průměr | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Velikost vyvolání vrstvení v cloudu podle aplikace  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Velikost vyvolání vrstvení v cloudu  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizebajty  |  Vyvolání vrstvení cloudu  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputtesPerSecond  |  Propustnost odvolání vrstvení v cloudu  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  Heartbeat služby StorageSyncServer  |  Stav online serveru  |  Počet  |  Maximum | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Synchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices  |  Počet chyb a chyb y synchronizačních synchronizujích položek  |  Nesynchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/registeredServers  |  Serverheartbeat  |  Stav online serveru  |  Počet  |  Maximum | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Vyvolání vrstvení cloudu  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchPřevedenyFileBytes  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Synchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemPočet chyb  |  Nesynchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchpřevedenéfileby  |  Synchronizované bajty  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedAppliedFilesCount  |  Synchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  Počet chyb chyb v centru serveru EndPointSyncSessionPerItem  |  Nesynchronizované soubory  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Požadavky AMLCalloutFailed  |  Neúspěšné požadavky na funkci  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Události funkcí  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutPožadavky  |  Požadavky na funkce  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Chyby převodu  |  Chyby převodu dat  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Chyba deserializace  |  Vstupní chyby deserializace  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Vynecháno Nebo AdjustedEvents  |  Události mimo pořadí  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Události včasného vstupu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  chyby  |  Chyby za běhu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  VstupOvatbajty  |  Bajty vstupních událostí  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Vstupní události  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Zpětné vstupní události  |  Počet  |  Maximum | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Přijaté vstupní zdroje  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Události pozdního vstupu  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Výstupní události  |  Výstupní události  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  VýstupVozemdelayseconds  |  Zpoždění vodoznaku  |  Sekundy  |  Maximum | 
| **Ano**  | Ne |  Microsoft.StreamAnalytics/streamingjobs  |  Využití prostředků  |  Využití SU %  |  Procento  |  Maximum | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bajty čtení disku  |  Bajty čtení disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operace čtení disku/s  |  Operace čtení disku/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bajty zápisu disku  |  Bajty zápisu disku  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operace zápisu na disk/s  |  Operace zápisu na disk/s  |  CountPerSecond  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBajtůmPerSecond  |  Čtení bajtů disku/s  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Latence čtení disku  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Operace čtení disku  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Bajty zápisu disku/s  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latence zápisu disku  |  Milisekund  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operace zápisu na disk  |  Počet  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Paměť aktivní  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Paměť udělena  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Využité paměti  |  Použitá paměť  |  Bajty  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Síťové vstupy  |  Síťové vstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Síťové výstupy  |  Síťové výstupy  |  Bajty  |  Celkem | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  V síti v bajtů/s  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Nezletěné bajty sítě/s  |  BajtyPerSekunda  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  Procento CPU  |  Procento CPU  |  Procento  |  Průměr | 
| **Ano**  | Ne |  Microsoft.VMwareCloudSimple/virtualMachines  |  ProcentoCpuReady  |  Procento připravenosti procesoru  |  Milisekund  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Aktivní požadavky  |  Aktivní požadavky  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Průměrný čas odezvy  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Přijaté bajty  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BajtůOdeslání  |  Data Out  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Procento procesoru  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Délka fronty disků  |  Délka fronty disku  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Chyby serveru HTTP  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Délka fronty Http  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Instance LargeAppServicePlan  |  Pracovníci plánu velkých služeb aplikace  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Instance MediumAppServicePlan  |  Pracovníci plánu středních služeb aplikace  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Procento paměti  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Žádosti  |  Žádosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Instance SmallAppServicePlan  |  Pracovníci plánu malých aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Celkový počet předních konců  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/workerPools  |  Procento procesoru  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/workerPools  |  Procento paměti  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/workerPools  |  PracovníciK dispozici  |  Dostupní pracovníci  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/workerPools  |  Celkem pracovníků  |  Celkový počet pracovníků  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/hostingEnvironments/workerPools  |  Použité pracovníky  |  Ojeté pracovníky  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Přijaté bajty  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  BajtůOdeslání  |  Data Out  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Procento procesoru  |  Procento procesoru  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Délka fronty disků  |  Délka fronty disku  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  HttpQueueLength  |  Délka fronty Http  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Procento paměti  |  Procento paměti  |  Procento  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpCloseWait  |  Čekání na zavření protokolu TCP  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpClosing  |  Uzávěrka protokolu TCP  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpEstablished  |  TCP založena  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpFinWait1  |  TCP Fin Čekání 1  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpFinWait2  |  TCP Fin Čekání 2  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpLastAck  |  Poslední potvrzení protokolu TCP  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Protokol TcpSynObdržel  |  Protokol TCP Syn byl přijat  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  Protokol TcpSynSent  |  Protokol TCP Syn odeslán  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Farmy Microsoft.Web/server  |  TcpTimeWait  |  Čas čekání protokolu TCP  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Připojení aplikací  |  Připojení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  AverageMemoryWorkingSet  |  Průměrná pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Průměrný čas odezvy  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Přijaté bajty  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  BajtůOdeslání  |  Data Out  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  ProcesorOvý čas  |  Čas procesoru  |  Sekundy  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  CurrentAssemblies  |  Aktuální sestavení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  FunctionExecutionCount  |  Počet spuštění funkce  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  FunkceExecutionUnits  |  Jednotky provádění funkcí  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Gen0Kolekce  |  Gen 0 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Gen1Kolekce  |  Gen 1 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Gen2Kolekce  |  Gen 2 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Handles  |  Počet popisovačů  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Stav kontroly stavu  |  Stav kontroly stavu  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http101  |  Http 101  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http2xx  |  Http 2xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http3xx  |  Http 3xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http401  |  Http 401  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http403  |  Http 403  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http404  |  Http 404  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http406  |  Http 406  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http4xx  |  Http 4xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Http5xx  |  Chyby serveru HTTP  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  HttpResponseTime  |  Doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoOtherBytesPerSecond  |  IO ostatní bajty za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoOtherOperationsPerSecond  |  Vi další operace za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoReadBytesPerSecond  |  Čtení bajtů v iza sekundě  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoReadOperationsPerSekunda  |  Operace čtení vi za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoWriteBytesPerSecond  |  IO zápis bajtů za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  IoWriteOperationsPerSecond  |  Operace zápisu vi za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Paměťworkingová sada  |  Pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Soukromé bajty  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Žádosti  |  Žádosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  RequestsInApplicationQueue  |  Požadavky ve frontě aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  Vlákna  |  Počet vláken  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  TotalAppDomains  |  Celkový počet domén aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Web/weby společnosti Microsoft  |  TotalAppDomainsUnloaded  |  Celkový počet uvolněných domén aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Připojení aplikací  |  Připojení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  AverageMemoryWorkingSet  |  Průměrná pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Průměrný čas odezvy  |  Průměrná doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Přijaté bajty  |  Data v  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  BajtůOdeslání  |  Data Out  |  Bajty  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  ProcesorOvý čas  |  Čas procesoru  |  Sekundy  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  CurrentAssemblies  |  Aktuální sestavení  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  FunctionExecutionCount  |  Počet spuštění funkce  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  FunkceExecutionUnits  |  Jednotky provádění funkcí  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Gen0Kolekce  |  Gen 0 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Gen1Kolekce  |  Gen 1 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Gen2Kolekce  |  Gen 2 Uvolňování paměti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Handles  |  Počet popisovačů  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Stav kontroly stavu  |  Stav kontroly stavu  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http101  |  Http 101  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http2xx  |  Http 2xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http3xx  |  Http 3xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http401  |  Http 401  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http403  |  Http 403  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http404  |  Http 404  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http406  |  Http 406  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http4xx  |  Http 4xx  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Http5xx  |  Chyby serveru HTTP  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  HttpResponseTime  |  Doba odezvy  |  Sekundy  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoOtherBytesPerSecond  |  IO ostatní bajty za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoOtherOperationsPerSecond  |  Vi další operace za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoReadBytesPerSecond  |  Čtení bajtů v iza sekundě  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoReadOperationsPerSekunda  |  Operace čtení vi za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoWriteBytesPerSecond  |  IO zápis bajtů za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  IoWriteOperationsPerSecond  |  Operace zápisu vi za sekundu  |  BajtyPerSekunda  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Paměťworkingová sada  |  Pracovní sada paměti  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Soukromé bajty  |  Soukromé bajty  |  Bajty  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Žádosti  |  Žádosti  |  Počet  |  Celkem | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  RequestsInApplicationQueue  |  Požadavky ve frontě aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  Vlákna  |  Počet vláken  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  TotalAppDomains  |  Celkový počet domén aplikací  |  Počet  |  Průměr | 
| **Ano**  | **Ano** |  Microsoft.Web/weby/sloty  |  TotalAppDomainsUnloaded  |  Celkový počet uvolněných domén aplikací  |  Počet  |  Průměr | 
