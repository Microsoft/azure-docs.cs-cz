---
title: Azure Monitor podporuje metriky podle typu prostředku
description: Seznam metrik, které jsou k dispozici pro každý typ prostředku pomocí Azure Monitoru.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 79bae9712cea04425cc36414ec56fdddd4345eab
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586022"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky pomocí Azure Monitoru

> [!NOTE]
> Tento seznam je z velké části automaticky generované z rozhraní REST rozhraní API metriky monitorování Azure. Jakékoli změny provedené v tomto seznamu přes Github mohou být zapsány bez varování. Chcete-li získat podrobnosti o tom, jak provádět trvalé aktualizace, obraťte se na autora tohoto článku.

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich vytváření grafů na portálu, přístupu k nim prostřednictvím rozhraní REST API nebo dotazování pomocí prostředí PowerShell nebo CLI. Níže je úplný seznam všech metrik, které jsou aktuálně dostupné v kanálu metrik Azure Monitoru. Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API. Tento seznam níže zahrnuje pouze metriky, které jsou k dispozici pomocí konsolidovaného kanálu metrikazure monitoru. Metriky jsou uspořádány podle oboru názvů. Seznam služeb a obory názvů, které jim patří, najdete v [tématu zprostředkovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Chcete-li tyto metriky dotazovat a přistupovat k nim programově, použijte [verzi api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
> Seznam metrik platformy exportovatelné pomocí nastavení diagnostiky naleznete v [tomto článku](metrics-supported-export-diagnostic-settings.md).


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|private_bytes_metric|Soukromé bajty|Bajty|Průměr|Soukromé bajty.|ServerResourceType|
|virtual_bytes_metric|Virtuální bajty|Bajty|Průměr|Virtuální bajty.|ServerResourceType|
|TotalConnectionRequests|Celkový počet požadavků na připojení|Počet|Průměr|Celkový počet požadavků na připojení. Tohle jsou příjezdy.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Rychlost úspěšného dokončení připojení.|ServerResourceType|
|Selhání totalconnections|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení.|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet vytvořených uživatelských relací.|ServerResourceType|
|Podprocesy QueryPoolBusy|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazu.|ServerResourceType|
|Délka fronty commandpoolu|Délka fronty úlohy fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů.|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úlohy fondu pro zpracování|Počet|Průměr|Počet úloh bez vstupně-va ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: Aktuální připojení|Počet|Průměr|Aktuální počet navázání připojení klientů.|ServerResourceType|
|CleanerCurrentCena|Paměť: Čistší aktuální cena|Počet|Průměr|Aktuální cena paměti, $/bajt/čas, normalizována na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: Čistší paměť smrštitelná|Bajty|Průměr|Množství paměti v bajtů, které podléhají vymazání čističem pozadí.|ServerResourceType|
|CleanerMemoryNeshrinkable|Paměť: Čistší paměť nesmrštná|Bajty|Průměr|Množství paměti v bajtů, které nejsou předmětem čištění čističem pozadí.|ServerResourceType|
|Využití paměti|Paměť: Využití paměti|Bajty|Průměr|Využití paměti procesu serveru, jak je použito při výpočtu čistší ceny paměti. Rovno čítač Process\PrivateBytes plus velikost dat mapovaných v paměti, ignoruje všechny paměti, která byla mapována nebo přidělena xVelocity v paměti analytický modul (VertiPaq) nad limit paměti motoru xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: Limit paměti pevný|Bajty|Průměr|Pevný limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitHigh|Paměť: Maximální limit paměti|Bajty|Průměr|Vysoký limit paměti z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: Nedostatek limitu paměti|Bajty|Průměr|Nízký limit paměti z konfiguračního souboru.|ServerResourceType|
|PaměťLimitVertiPaq|Paměť: Limit paměti VertiPaq|Bajty|Průměr|Limit v paměti z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: Kvóta|Bajty|Průměr|Aktuální kvóta paměti v bajtech. Kvóta paměti se také označuje jako udělení paměti nebo rezervace paměti.|ServerResourceType|
|Kvóta blokována|Paměť: Kvóta byla zablokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nejsou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq Nonpaged|Bajty|Průměr|Bajty paměti uzamčeny v pracovní sadě pro použití v modulu v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq Stránkované|Bajty|Průměr|Bajty stránkované paměti, které se používají pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Řádky přečtené za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: Řádky převedené za sekundu|CountPerSecond|Průměr|Rychlost řádků převedených během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: Řádky napsané za sekundu|CountPerSecond|Průměr|Rychlost řádků zapsaných během zpracování.|ServerResourceType|
|Podprocesy CommandPoolBusy|Vlákna: Podprocesy zaneprázdněné příkazy|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|Příkazy Pročinné a|Vlákna: Nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyVlákna|Vlákna: Dlouhá analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve fondu dlouhých analyzátorů.|ServerResourceType|
|LongParsingIdleThreads|Závity: Dlouhá analýza nečinných závitů|Počet|Průměr|Počet nečinných vláken ve fondu dlouhých analyzujících vláken.|ServerResourceType|
|Délka fronty|Vlákna: Délka fronty úloh dlouhé analýzy|Počet|Průměr|Počet úloh ve frontě fondu dlouhých analyzátorů vláken.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vlákna: Zpracování fondu zaneprázdněnvstupně-v.,V úlohy podprocesů|Počet|Průměr|Počet podprocesů spuštěných vstupně-v., které jsou ve fondu vláken zpracování vláken.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vlákna: Zpracování fondu zaneprázdněné vlákny, která nejsou vstupně-vstupně-va.|Počet|Průměr|Počet podprocesů, na kterých jsou ve fondu vláken zpracování vláken spuštěny úlohy bez vstupně-videa.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vlákna: Délka fronty úloh vstupně-v.i.a. fondu|Počet|Průměr|Počet vstupně-v.i.,v.i.,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vlákna: Zpracování nečinných vstupně-vi úloh fondu|Počet|Průměr|Počet nečinných podprocesů pro vstupně-tovitých úloh ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vlákna: Zpracování nečinnosti fondu bez vstupně-v/V podprocesů|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazených pro úlohy bez vstupně-v..|ServerResourceType|
|QueryPoolIdleThreads|Vlákna: Nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných podprocesů pro vstupně-tovitých úloh ve fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vlákna: Lengt fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|ServerResourceType|
|ShortParsingBusyVlákna|Vlákna: Krátká analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve fondu krátkých analyzátorů.|ServerResourceType|
|ShortParsingIdleThreads|Závity: Krátká analýza nečinných závitů|Počet|Průměr|Počet nečinných vláken ve fondu krátkých analyzátorů.|ServerResourceType|
|ShortParsingJobQueueLength|Vlákna: Délka fronty úlohy krátké analýzy|Počet|Průměr|Počet úloh ve frontě fondu krátkých analyzátorů vláken.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná paměť výprask.|ServerResourceType|
|mashup_engine_qpu_metric|M Motor QPU|Počet|Průměr|Využití QPU procesy mashup motoru|ServerResourceType|
|mashup_engine_memory_metric|M Paměť motoru|Bajty|Průměr|Využití paměti procesy mashup motoru|ServerResourceType|
|mashup_engine_private_bytes_metric|Soukromé bajty motoru M|Bajty|Průměr|Soukromé bajty využití procesy mashup motoru.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Virtuální bajty motoru|Bajty|Průměr|Virtuální bajty využití procesy mashup motoru.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Celkový počet požadavků na bránu (zastaralé)|Počet|Celkem|Počet požadavků brány – místo toho použijte metriku požadavku s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění,Název hostitele|
|Úspěšné požadavky|Úspěšné požadavky na bránu (zastaralé)|Počet|Celkem|Počet úspěšných požadavků brány – místo toho použijte metriku požadavku na více dimenzí s dimenzí GatewayResponseCodeCategory|Umístění,Název hostitele|
|Neautorizované požadavky|Neautorizované požadavky na bránu (zastaralé)|Počet|Celkem|Počet neoprávněných požadavků brány – místo toho použijte metriku požadavku na více dimenzí s dimenzí GatewayResponseCodeCategory|Umístění,Název hostitele|
|Požadavky na selhání|Neúspěšné požadavky na bránu (zastaralé)|Počet|Celkem|Počet chyb v požadavcích na bránu – místo toho použijte metriku požadavku na více dimenzí s dimenzí GatewayResponseCodeCategory|Umístění,Název hostitele|
|Ostatní požadavky|Další požadavky na bránu (zastaralé)|Počet|Celkem|Počet dalších požadavků brány – místo toho použijte metriku požadavku na více dimenzí s dimenzí GatewayResponseCodeCategory|Umístění,Název hostitele|
|Doba trvání|Celková doba trvání požadavků brány|Milisekund|Průměr|Celková doba trvání požadavků brány v milisekundách|Umístění,Název hostitele|
|BackendDuration|Doba trvání back-endových požadavků|Milisekund|Průměr|Doba trvání back-endových požadavků v milisekundách|Umístění,Název hostitele|
|Kapacita|Kapacita|Procento|Průměr|Metrika využití pro službu ApiManagement|Umístění|
|EventHubTotalEvents|Celkový počet událostí EventHub|Počet|Celkem|Počet událostí odeslaných na EventHub|Umístění|
|EventHubÚspěšné události|Úspěšné události EventHub|Počet|Celkem|Počet úspěšných událostí EventHub|Umístění|
|EventHubTotalFailedEvents|Neúspěšné události eventhubu|Počet|Celkem|Počet neúspěšných událostí EventHub|Umístění|
|EventHubRejectedEvents|Odmítnuté události EventHub|Počet|Celkem|Počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neautorizovaná)|Umístění|
|EventHubThrottledEvents|Události eventhubu s omezením|Počet|Celkem|Počet událostí s omezením EventHub|Umístění|
|Události EventHubTimedoutEvents|Události EventHub s časovým výtažkem|Počet|Celkem|Počet časových časových událostí EventHub|Umístění|
|EventHubDroppedEvents|Zrušené události EventHub|Počet|Celkem|Počet událostí přeskočených z důvodu dosaženého limitu velikosti fronty|Umístění|
|EventHubTotalBytesSent|Velikost událostí EventHub|Bajty|Celkem|Celková velikost událostí EventHub v bajtech|Umístění|
|Žádosti|Žádosti|Počet|Celkem|Metriky požadavků brány s více dimenzemi|Umístění,Název_hostitele,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Připojení k síti|Stav připojení k síti prostředků (preview)|Počet|Celkem|Stav síťového připojení závislých typů prostředků ze služby API Management|Umístění,Typ zdroje|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppKonfigurace/configurationStores

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Počet|Počet|Celkový počet příchozích požadavků http.|Statuscode|
|Doba trvání httpIncomingrequest|Doba trvání httpIncomingrequest|Počet|Průměr|Latence na požadavek http.|Statuscode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/jaro

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SystemCpuUsageProcento|Procento využití procesoru systému|Procento|Průměr|Nedávné využití procesoru pro celý systém|Název aplikace,Pod|
|AppCpuUsageProcento|Procento využití procesoru aplikace|Procento|Průměr|Procento využití procesoru JVM aplikace|Název aplikace,Pod|
|AppMemoryCommitted|Přiřazená paměť aplikace|Bajty|Průměr|Paměť přiřazená JVM v bajtech|Název aplikace,Pod|
|Použitá paměť aplikace|Použitá paměť aplikace|Bajty|Průměr|Paměť aplikace používaná v bajtech|Název aplikace,Pod|
|AppMemoryMax|Paměť aplikace Max|Bajty|Maximum|Maximální velikost paměti v bajtů, které lze použít pro správu paměti|Název aplikace,Pod|
|Bajty maxoldgenmemorypool|Maximální dostupná velikost dat staré generace|Bajty|Průměr|Maximální velikost fondu paměti staré generace|Název aplikace,Pod|
|OldGenMemoryPoolBajty|Velikost dat staré generace|Bajty|Průměr|Velikost fondu paměti staré generace po úplném GC|Název aplikace,Pod|
|OldGenPromotedBajty|Zvýšit velikost dat staré generace|Bajty|Maximum|Počet kladných zvýšení velikosti fondu paměti staré generace před GC po GC|Název aplikace,Pod|
|YoungGenPromotedBajty|Povýšení na velikost dat mladé generace|Bajty|Maximum|Zvýšení velikosti fondu paměti mladé generace po jednom GC na před dalším|Název aplikace,Pod|
|Počet gcpausetotalcount|Počet pozastavení GC|Počet|Celkem|Počet pozastavení GC|Název aplikace,Pod|
|Čas gcpausetotaltime|Celkový čas pozastavení GC|Milisekund|Celkem|Celkový čas pozastavení GC|Název aplikace,Pod|
|TomcatSentBytes|Celkový počet odeslaných bajtů kotomak|Bajty|Celkem|Celkový počet odeslaných bajtů kotomak|Název aplikace,Pod|
|TomcatReceivedBytes|Tomcat Celkem přijatých bajtů|Bajty|Celkem|Tomcat Celkem přijatých bajtů|Název aplikace,Pod|
|TomcatRequestTotalTime|Tomcat Žádost Celkem Krát|Milisekund|Celkem|Tomcat Žádost Celkem Krát|Název aplikace,Pod|
|TomcatRequestTotalCount|Celkový počet požadavků na kotomcat|Počet|Celkem|Celkový počet požadavků na kotomcat|Název aplikace,Pod|
|TomcatResponseAvgTime|Průměrná doba požadavku Tomcat|Milisekund|Průměr|Průměrná doba požadavku Tomcat|Název aplikace,Pod|
|TomcatRequestMaxTime|Maximální čas požadavku Tomcat|Milisekund|Maximum|Maximální čas požadavku Tomcat|Název aplikace,Pod|
|Počet chyb Tomcat|Globální chyba Společnosti Tomcat|Počet|Celkem|Globální chyba Společnosti Tomcat|Název aplikace,Pod|
|TomcatSessionActiveMaxCount|Počet aktivních aktivit relace Kotomcat|Počet|Celkem|Počet aktivních aktivit relace Kotomcat|Název aplikace,Pod|
|TomcatSessionAliveMaxČas|Tomcat Session Max Alive Čas|Milisekund|Maximum|Tomcat Session Max Alive Čas|Název aplikace,Pod|
|TomcatSessionActiveCurrentCount|Počet živých relací Tomcat|Počet|Celkem|Počet živých relací Tomcat|Název aplikace,Pod|
|Počet vytvořených v tomcatSession|Počet vytvořených relací Tomcat|Počet|Celkem|Počet vytvořených relací Tomcat|Název aplikace,Pod|
|Počet buněk TomcatSessionExpiredCount|Počet prošlých platností relace Tomcat|Počet|Celkem|Počet prošlých platností relace Tomcat|Název aplikace,Pod|
|TomcatSessionRejectedCount|Počet odmítnutých relace Tomcat|Počet|Celkem|Počet odmítnutých relace Tomcat|Název aplikace,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationÚčty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Počet|Celkem|Celkový počet pracovních míst|Runbook,Stav|
|Počet nasazení totalupdatedeploymentruns|Celkový počet spuštění nasazení aktualizace|Počet|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName,Stav|
|TotalUpdateDeploymentMachineRuns|Celkový počet spuštění počítače pro nasazení aktualizace|Počet|Celkem|Celkový počet spuštění počítače pro nasazení aktualizací softwaru při spuštění nasazení aktualizace softwaru|SoftwareUpdateConfigurationName,Stav,Cílový počítač,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Počet corecount|Vyhrazený počet jádra|Počet|Celkem|Celkový počet vyhrazených jader v dávkovém účtu|Žádný|
|TotalNodeCount|Počet vyhrazených uzlů|Počet|Celkem|Celkový počet vyhrazených uzlů v dávkovém účtu|Žádný|
|LowPriorityCoreCount|Počet jádra LowPriority|Počet|Celkem|Celkový počet jader s nízkou prioritou v dávkovém účtu|Žádný|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Počet|Celkem|Celkový počet uzlů s nízkou prioritou v dávkovém účtu|Žádný|
|CreatingNodeCount|Vytvoření počtu uzlů|Počet|Celkem|Počet vytvářených uzlů|Žádný|
|StartingNodeCount|Počáteční počet uzlů|Počet|Celkem|Počet počátečních uzlů|Žádný|
|WaitingForStartTaskNodeCount|Čekání na počet počátečních uzlů úloh|Počet|Celkem|Počet uzlů čekajících na dokončení počáteční úlohy|Žádný|
|Počet nepodařilose spustit úkol|Počet neúspěšných uzlů spuštění|Počet|Celkem|Počet uzlů, ve kterých se nezdařila počáteční úloha|Žádný|
|IdleNodeCount|Počet nečinných uzlů|Počet|Celkem|Počet nečinných uzlů|Žádný|
|OfflineNodeCount|Počet uzlů offline|Počet|Celkem|Počet uzlů offline|Žádný|
|RebootingNodeCount|Restartování počtu uzlů|Počet|Celkem|Počet restartování uzlů|Žádný|
|ReimagingNodeCount|Počet reimagingových uzlů|Počet|Celkem|Počet uzlů reimagingu|Žádný|
|RunningNodeCount|Počet spuštěné uzlu|Počet|Celkem|Počet spuštěných uzlů|Žádný|
|LeavingPoolNodeCount|Počet uzlů opuštění fondu|Počet|Celkem|Počet uzlů opouštějících fond|Žádný|
|UnusableNodeCount|Počet nepoužitelných uzlů|Počet|Celkem|Počet nepoužitelných uzlů|Žádný|
|PreemptedNodeCount|Počet předplyňovaných uzlů|Počet|Celkem|Počet předplyňovaných uzlů|Žádný|
|Událost TaskStartEvent|Události zahájení úkolu|Počet|Celkem|Celkový počet zahájených úkolů|poolId,jobId|
|Událost úkolu CompleteEvent|Události dokončení úkolu|Počet|Celkem|Celkový počet dokončených úkolů|poolId,jobId|
|Událost úlohy FailEvent|Události selhání úlohy|Počet|Celkem|Celkový počet úkolů, které byly dokončeny ve stavu selhání|poolId,jobId|
|PoolCreateEvent|Události vytvoření fondu|Počet|Celkem|Celkový počet vytvořených fondů|poolId|
|PoolResizeStartEvent|Události zahájení změny velikosti fondu|Počet|Celkem|Celkový počet velikostí fondu, které byly zahájeny|poolId|
|PoolResizeCompleteEvent|Události dokončení velikosti fondu|Počet|Celkem|Celkový počet velikostí fondu, které byly dokončeny|poolId|
|Událost PoolDeleteStartEvent|Události spuštění odstranění fondu|Počet|Celkem|Celkový počet odstranění fondu, které byly zahájeny|poolId|
|PoolDeleteCompleteEvent|Úplné události odstranění fondu|Počet|Celkem|Celkový počet odstraněných objemů fondu, které byly dokončeny|poolId|
|JobDeleteCompleteEvent|Úloha odstranit úplné události|Počet|Celkem|Celkový počet úloh, které byly úspěšně odstraněny.|jobId|
|Událost JobDeleteStartEvent|Události spuštění s odstraněním úlohy|Počet|Celkem|Celkový počet úloh, které byly požádány o odstranění.|jobId|
|JobDisableCompleteEvent|Zakázat dokončení událostí úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|jobId|
|Událost JobDisableStartEvent|Zakázat události zahájení úlohy|Počet|Celkem|Celkový počet úloh, které byly požadovány k zakázání.|jobId|
|Událost Úloha StartEvent|Události zahájení úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|jobId|
|Akce JobTerminateCompleteEvent|Události ukončení úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|jobId|
|Událost JobTerminateStartEvent|Události ukončení úlohy zahájení|Počet|Celkem|Celkový počet úloh, které byly požadovány k ukončení.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Pracovní prostory Microsoft.BatchAI

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Odeslaná úloha|Odeslaná úloha|Počet|Celkem|Počet odeslaných pracovních míst|Scénář,Název_clusteru|
|Úloha dokončena|Úloha dokončena|Počet|Celkem|Počet dokončených úloh|Scénář,Název_clusteru,Typ výsledku|
|Uzly celkem|Uzly celkem|Počet|Průměr|Počet uzlů součtu|Scénář,Název_clusteru|
|Aktivní uzly|Aktivní uzly|Počet|Průměr|Počet spuštěných uzlů|Scénář,Název_clusteru|
|Nečinné uzly|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů|Scénář,Název_clusteru|
|Nepoužitelné uzly|Nepoužitelné uzly|Počet|Průměr|Počet nepoužitelných uzlů|Scénář,Název_clusteru|
|Předpisované uzly|Předpisované uzly|Počet|Průměr|Počet předplyňovaných uzlů|Scénář,Název_clusteru|
|Opuštění uzlů|Opuštění uzlů|Počet|Průměr|Počet opouštějících uzlů|Scénář,Název_clusteru|
|Jádra celkem|Jádra celkem|Počet|Průměr|Počet celkových jader|Scénář,Název_clusteru|
|Aktivní jádra|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář,Název_clusteru|
|Nečinná jádra|Nečinná jádra|Počet|Průměr|Počet nečinných jader|Scénář,Název_clusteru|
|Nepoužitelná jádra|Nepoužitelná jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář,Název_clusteru|
|Předpjatojádra|Předpjatojádra|Počet|Průměr|Počet preemptovaných jader|Scénář,Název_clusteru|
|Opuštění jader|Opuštění jader|Počet|Průměr|Počet odnechávacích jader|Scénář,Název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář,Název_clusteru,Název_vmFamily,Priorita VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainČlenové

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procento využití procesoru|Procento|Maximum|Procento využití procesoru|Node|
|Využití paměti|Využití paměti|Bajty|Průměr|Využití paměti|Node|
|Limit paměti|Limit paměti|Bajty|Průměr|Limit paměti|Node|
|MemoryUsagePercentageInDouble|Procento využití paměti|Procento|Průměr|Procento využití paměti|Node|
|Využití úložiště|Využití úložiště|Bajty|Průměr|Využití úložiště|Node|
|IOReadBytes|Čtení bajtů v iO|Bajty|Celkem|Čtení bajtů v iO|Node|
|IOWriteBytes|Vypoučovat bajty|Bajty|Celkem|Vypoučovat bajty|Node|
|Připojení přijato|Přijatá připojení|Počet|Celkem|Přijatá připojení|Node|
|ConnectionHandled|Manipulovní připojení|Počet|Celkem|Manipulovní připojení|Node|
|ConnectionActive|Aktivní připojení|Počet|Průměr|Aktivní připojení|Node|
|Zpracování požadavku|Zpracované požadavky|Počet|Celkem|Zpracované požadavky|Node|
|Zpracované bloky|Zpracované bloky|Počet|Celkem|Zpracované bloky|Node|
|Zpracované transakce|Zpracované transakce|Počet|Celkem|Zpracované transakce|Node|
|Čekající transakce|Čekající transakce|Počet|Průměr|Čekající transakce|Node|
|Transakce ve frontě|Transakce ve frontě|Počet|Průměr|Transakce ve frontě|Node|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|připojení klienti|Připojení klienti|Počet|Maximum||ShardId|
|totalcommandszpracovány|Celkový počet operací|Počet|Celkem||ShardId|
|cachehits|Přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachemine|Počet neúspěšných přístupů do mezipaměti|Počet|Celkem||ShardId|
|cachemissrate|Míra nedoletosti mezipaměti|Procento|cachemissrate||ShardId|
|příkazy get|Získá|Počet|Celkem||ShardId|
|setcommands|Sady|Počet|Celkem||ShardId|
|operacePerSecond|Operace za sekundu|Počet|Maximum||ShardId|
|vyřazení klíčů|Vystěhované klíče|Počet|Celkem||ShardId|
|totalkeys|Celkový počet klíčů|Počet|Maximum||ShardId|
|klávesy expired|Klíče s prošlou platností|Počet|Celkem||ShardId|
|použitá paměť|Použitá paměť|Bajty|Maximum||ShardId|
|procento použité paměti|Procento použité paměti|Procento|Maximum||ShardId|
|použité pamětiRss|Použité paměti RSS|Bajty|Maximum||ShardId|
|serverLoad|Zatížení serveru|Procento|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BajtyPerSekunda|Maximum||ShardId|
|cacheRead|Čtení mezipaměti|BajtyPerSekunda|Maximum||ShardId|
|percentProcessorTime|Procesor|Procento|Maximum||ShardId|
|cacheLatency|Mikrosekunda latence mezipaměti (náhled)|Počet|Průměr||ShardId|
|chyby|chyby|Počet|Maximum||ShardId,Typ chyby|
|připojení klienti0|Připojení klienti (střep 0)|Počet|Maximum||Žádný|
|totalcommandsprocessed0|Celkový počet operací (střep 0)|Počet|Celkem||Žádný|
|cachehits0|Přístupy do mezipaměti (střep 0)|Počet|Celkem||Žádný|
|cachemisses0|Počet neúspěšných přístupů do mezipaměti (střep 0)|Počet|Celkem||Žádný|
|příkazy get0|Získá (střep 0)|Počet|Celkem||Žádný|
|setcommands0|Sady (úlomek 0)|Počet|Celkem||Žádný|
|operacePo sekundách|Operace za sekundu (střep 0)|Počet|Maximum||Žádný|
|vyřazení klíčů0|Vystěhované klíče (střep 0)|Počet|Celkem||Žádný|
|totalkeys0|Celkový počet klíčů (střep 0)|Počet|Maximum||Žádný|
|expiredkeys0|Klíče s prošlou platností (střep 0)|Počet|Celkem||Žádný|
|použité paměti0|Použitá paměť (střep 0)|Bajty|Maximum||Žádný|
|použité pamětiRss0|Použité paměti RSS (střep 0)|Bajty|Maximum||Žádný|
|serverLoad0|Zatížení serveru (střep 0)|Procento|Maximum||Žádný|
|cacheWrite0|Zápis do mezipaměti (střep 0)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead0|Čtení mezipaměti (střep 0)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime0|Procesor (úlomek 0)|Procento|Maximum||Žádný|
|připojení klienti1|Připojení klienti (Shard 1)|Počet|Maximum||Žádný|
|totalcommandsprocessed1|Celkový počet operací (střep 1)|Počet|Celkem||Žádný|
|cachehits1|Přístupy do mezipaměti (střep 1)|Počet|Celkem||Žádný|
|počet mezipamětí1|Počet neúspěšných přístupů do mezipaměti (střep 1)|Počet|Celkem||Žádný|
|příkazy get1|Získá (střep 1)|Počet|Celkem||Žádný|
|setcommands1|Sady (úlomek 1)|Počet|Celkem||Žádný|
|operacePerSecond1|Operace za sekundu (střep 1)|Počet|Maximum||Žádný|
|vyřazení klíčů1|Vystěhované klíče (střep 1)|Počet|Celkem||Žádný|
|totalkeys1|Celkový počet klíčů (střep 1)|Počet|Maximum||Žádný|
|expiredkeys1|Klíče s prošlou platností (střep 1)|Počet|Celkem||Žádný|
|použité paměti1|Použitá paměť (střep 1)|Bajty|Maximum||Žádný|
|použité pamětiRss1|Použité paměti RSS (střep 1)|Bajty|Maximum||Žádný|
|serverLoad1|Zatížení serveru (střep 1)|Procento|Maximum||Žádný|
|cacheWrite1|Zápis do mezipaměti (střep 1)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead1|Čtení mezipaměti (střep 1)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime1|Procesor (střep 1)|Procento|Maximum||Žádný|
|připojení klienti2|Připojení klienti (Shard 2)|Počet|Maximum||Žádný|
|totalcommandsprocessed2|Celkový počet operací (střep 2)|Počet|Celkem||Žádný|
|cachehits2|Přístupy do mezipaměti (střep 2)|Počet|Celkem||Žádný|
|počet mezipamětí2|Počet neúspěšných přístupů do mezipaměti (střep 2)|Počet|Celkem||Žádný|
|příkazy get2|Získá (střep 2)|Počet|Celkem||Žádný|
|setcommands2|Sady (úlomek 2)|Počet|Celkem||Žádný|
|operaceSekunda2|Operace za sekundu (střep 2)|Počet|Maximum||Žádný|
|vyřazovacíklíče2|Vystěhované klíče (střep 2)|Počet|Celkem||Žádný|
|totalkeys2|Celkový počet klíčů (střep 2)|Počet|Maximum||Žádný|
|expiredkeys2|Klíče s prošlou platností (střep 2)|Počet|Celkem||Žádný|
|použité paměti2|Použitá paměť (střep 2)|Bajty|Maximum||Žádný|
|použité pamětiRss2|Použité paměti RSS (střep 2)|Bajty|Maximum||Žádný|
|serverLoad2|Zatížení serveru (střep 2)|Procento|Maximum||Žádný|
|cacheWrite2|Zápis do mezipaměti (střep 2)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead2|Čtení mezipaměti (střep 2)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime2|Procesor (střep 2)|Procento|Maximum||Žádný|
|připojení klienti3|Připojení klienti (Shard 3)|Počet|Maximum||Žádný|
|totalcommandsprocessed3|Celkový počet operací (střep 3)|Počet|Celkem||Žádný|
|cachehits3|Přístupy do mezipaměti (střep 3)|Počet|Celkem||Žádný|
|počet mezipamětí3|Počet neúspěšných přístupů do mezipaměti (střep 3)|Počet|Celkem||Žádný|
|příkazy get3|Získá (střep 3)|Počet|Celkem||Žádný|
|setcommands3|Sady (střep 3)|Počet|Celkem||Žádný|
|operaceSecond3|Operace za sekundu (střep 3)|Počet|Maximum||Žádný|
|vyřazení klíčů3|Vystěhované klíče (střep 3)|Počet|Celkem||Žádný|
|totalkeys3|Celkový počet klíčů (střep 3)|Počet|Maximum||Žádný|
|expiredkeys3|Klíče s prošlou platností (střep 3)|Počet|Celkem||Žádný|
|použité paměti3|Použitá paměť (střep 3)|Bajty|Maximum||Žádný|
|použité pamětiRss3|Použité paměti RSS (střep 3)|Bajty|Maximum||Žádný|
|serverLoad3|Zatížení serveru (střep 3)|Procento|Maximum||Žádný|
|cacheWrite3|Zápis do mezipaměti (střep 3)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead3|Čtení mezipaměti (střep 3)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime3|Procesor (střep 3)|Procento|Maximum||Žádný|
|připojení klienti4|Připojení klienti (Shard 4)|Počet|Maximum||Žádný|
|totalcommandsprocessed4|Celkový počet operací (střep 4)|Počet|Celkem||Žádný|
|cachehits4|Přístupy do mezipaměti (střep 4)|Počet|Celkem||Žádný|
|počet mezipamětí4|Počet neúspěšných přístupů do mezipaměti (střep 4)|Počet|Celkem||Žádný|
|příkazy get4|Získá (střep 4)|Počet|Celkem||Žádný|
|setcommands4|Sady (úlomek 4)|Počet|Celkem||Žádný|
|operaceSecond4|Operace za sekundu (střep 4)|Počet|Maximum||Žádný|
|vyřazení klíčů4|Vystěhované klíče (střep 4)|Počet|Celkem||Žádný|
|totalkeys4|Celkový počet klíčů (střep 4)|Počet|Maximum||Žádný|
|expiredkeys4|Klíče s prošlou platností (střep 4)|Počet|Celkem||Žádný|
|použité paměti4|Použitá paměť (střep 4)|Bajty|Maximum||Žádný|
|použité pamětiRss4|Použité paměti RSS (střep 4)|Bajty|Maximum||Žádný|
|serverLoad4|Zatížení serveru (střep 4)|Procento|Maximum||Žádný|
|cacheWrite4|Zápis do mezipaměti (střep 4)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead4|Čtení mezipaměti (střep 4)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime4|Procesor (střep 4)|Procento|Maximum||Žádný|
|připojení klienti5|Připojení klienti (Shard 5)|Počet|Maximum||Žádný|
|totalcommandsprocessed5|Celkový počet operací (střep 5)|Počet|Celkem||Žádný|
|cachehits5|Přístupy do mezipaměti (střep 5)|Počet|Celkem||Žádný|
|počet mezipamětí5|Počet neúspěšných přístupů do mezipaměti (střep 5)|Počet|Celkem||Žádný|
|příkazy get5|Získá (střep 5)|Počet|Celkem||Žádný|
|setcommands5|Sady (úlomek 5)|Počet|Celkem||Žádný|
|operacePerSecond5|Operace za sekundu (střep 5)|Počet|Maximum||Žádný|
|vyřazení klíčů5|Vystěhované klíče (střep 5)|Počet|Celkem||Žádný|
|totalkeys5|Celkový počet klíčů (střep 5)|Počet|Maximum||Žádný|
|expiredkeys5|Klíče s prošlou platností (střep 5)|Počet|Celkem||Žádný|
|použité paměti5|Použitá paměť (střep 5)|Bajty|Maximum||Žádný|
|použité pamětiRss5|Použité paměti RSS (střep 5)|Bajty|Maximum||Žádný|
|serverLoad5|Zatížení serveru (střep 5)|Procento|Maximum||Žádný|
|cacheWrite5|Zápis do mezipaměti (střep 5)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead5|Čtení mezipaměti (střep 5)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime5|Procesor (střep 5)|Procento|Maximum||Žádný|
|připojení klienti6|Připojení klienti (Shard 6)|Počet|Maximum||Žádný|
|totalcommandsprocessed6|Celkový počet operací (střep 6)|Počet|Celkem||Žádný|
|cachehits6|Přístupy do mezipaměti (střep 6)|Počet|Celkem||Žádný|
|počet mezipamětí6|Počet neúspěšných přístupů do mezipaměti (střep 6)|Počet|Celkem||Žádný|
|příkazy get6|Získá (střep 6)|Počet|Celkem||Žádný|
|setcommands6|Sady (střep 6)|Počet|Celkem||Žádný|
|operacePerSecond6|Operace za sekundu (střep 6)|Počet|Maximum||Žádný|
|vyřazení klíčů6|Vystěhované klíče (střep 6)|Počet|Celkem||Žádný|
|totalkeys6|Celkový počet klíčů (střep 6)|Počet|Maximum||Žádný|
|expiredkeys6|Klíče s prošlou platností (střep 6)|Počet|Celkem||Žádný|
|použitá paměť6|Použitá paměť (střep 6)|Bajty|Maximum||Žádný|
|použité pamětiRss6|Použité paměti RSS (střep 6)|Bajty|Maximum||Žádný|
|serverLoad6|Zatížení serveru (střep 6)|Procento|Maximum||Žádný|
|cacheWrite6|Zápis do mezipaměti (střep 6)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead6|Čtení mezipaměti (střep 6)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime6|Procesor (střep 6)|Procento|Maximum||Žádný|
|připojení klienti7|Připojení klienti (Shard 7)|Počet|Maximum||Žádný|
|totalcommandsprocessed7|Celkový počet operací (střep 7)|Počet|Celkem||Žádný|
|cachehits7|Přístupy do mezipaměti (střep 7)|Počet|Celkem||Žádný|
|počet mezipamětí7|Počet neúspěšných přístupů do mezipaměti (střep 7)|Počet|Celkem||Žádný|
|příkazy get7|Získá (střep 7)|Počet|Celkem||Žádný|
|setcommands7|Sady (úlomek 7)|Počet|Celkem||Žádný|
|operacePerSecond7|Operace za sekundu (střep 7)|Počet|Maximum||Žádný|
|vyřazovacíklíče7|Vystěhované klíče (střep 7)|Počet|Celkem||Žádný|
|totalkeys7|Celkový počet klíčů (střep 7)|Počet|Maximum||Žádný|
|expiredkeys7|Klíče s prošlou platností (střep 7)|Počet|Celkem||Žádný|
|použitá paměť7|Použitá paměť (střep 7)|Bajty|Maximum||Žádný|
|použité pamětiRss7|Použité paměti RSS (střep 7)|Bajty|Maximum||Žádný|
|serverLoad7|Zatížení serveru (střep 7)|Procento|Maximum||Žádný|
|cacheWrite7|Zápis do mezipaměti (střep 7)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead7|Čtení mezipaměti (střep 7)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime7|Procesor (úlomek 7)|Procento|Maximum||Žádný|
|připojení klienti8|Připojení klienti (Shard 8)|Počet|Maximum||Žádný|
|totalcommandsprocessed8|Celkový počet operací (střep 8)|Počet|Celkem||Žádný|
|cachehits8|Přístupy do mezipaměti (střep 8)|Počet|Celkem||Žádný|
|cachemisses8|Počet neúspěšných přístupů do mezipaměti (střep 8)|Počet|Celkem||Žádný|
|příkazy get8|Získá (střep 8)|Počet|Celkem||Žádný|
|setcommands8|Sady (úlomek 8)|Počet|Celkem||Žádný|
|operacePerSecond8|Operace za sekundu (střep 8)|Počet|Maximum||Žádný|
|vyřazení klíčů8|Vystěhované klíče (střep 8)|Počet|Celkem||Žádný|
|totalkeys8|Celkový počet klíčů (střep 8)|Počet|Maximum||Žádný|
|expiredkeys8|Klíče s prošlou platností (střep 8)|Počet|Celkem||Žádný|
|použitá paměť8|Použitá paměť (střep 8)|Bajty|Maximum||Žádný|
|použité pamětiRss8|Použité paměti RSS (střep 8)|Bajty|Maximum||Žádný|
|serverLoad8|Zatížení serveru (střep 8)|Procento|Maximum||Žádný|
|cacheWrite8|Zápis do mezipaměti (střep 8)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead8|Čtení mezipaměti (střep 8)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime8|Procesor (střep 8)|Procento|Maximum||Žádný|
|připojení klienti9|Připojení klienti (Shard 9)|Počet|Maximum||Žádný|
|totalcommandsprocessed9|Celkový počet operací (střep 9)|Počet|Celkem||Žádný|
|cachehits9|Přístupy do mezipaměti (střep 9)|Počet|Celkem||Žádný|
|počet mezipamětí9|Počet neúspěšných přístupů do mezipaměti (střep 9)|Počet|Celkem||Žádný|
|příkazy get9|Získá (střep 9)|Počet|Celkem||Žádný|
|setcommands9|Sady (střep 9)|Počet|Celkem||Žádný|
|operacePerSecond9|Operace za sekundu (střep 9)|Počet|Maximum||Žádný|
|vyřazení klíčů9|Vystěhované klíče (střep 9)|Počet|Celkem||Žádný|
|totalkeys9|Celkový počet klíčů (střep 9)|Počet|Maximum||Žádný|
|expiredkeys9|Klíče s prošlou platností (střep 9)|Počet|Celkem||Žádný|
|použité paměti9|Použitá paměť (střep 9)|Bajty|Maximum||Žádný|
|použité pamětiRss9|Použité paměti RSS (Shard 9)|Bajty|Maximum||Žádný|
|serverLoad9|Zatížení serveru (střep 9)|Procento|Maximum||Žádný|
|cacheWrite9|Zápis do mezipaměti (střep 9)|BajtyPerSekunda|Maximum||Žádný|
|cacheRead9|Čtení mezipaměti (střep 9)|BajtyPerSekunda|Maximum||Žádný|
|percentProcessorTime9|Procesor (střep 9)|Procento|Maximum||Žádný|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Zásady brány firewall microsoft.cdn/cdnwebapplication

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Počet požadavků brány firewall webové aplikace|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webové aplikace|Název_zásady,Název_pravidla,Akce|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálním počítačem.|Žádný|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozíprovoz).|Žádný|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Počet bajtů ve všech síťových rozhraních virtuálním počítačem (odchozíprovoz).|Žádný|
|Čtení bajtů disku/s|Čtení na disku|BajtyPerSekunda|Průměr|Průměrné bajty přečtené z disku během období sledování.|Žádný|
|Bajty zápisu disku/s|Zápis na disk|BajtyPerSekunda|Průměr|Průměrné bajty zapsané na disk během období sledování.|Žádný|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|Disk číst IOPS.|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Vipony pro zápis disku.|Žádný|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálním počítačem.|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozíprovoz).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Počet bajtů ve všech síťových rozhraních virtuálním počítačem (odchozíprovoz).|RoleInstanceId|
|Čtení bajtů disku/s|Čtení na disku|BajtyPerSekunda|Průměr|Průměrné bajty přečtené z disku během období sledování.|RoleInstanceId|
|Bajty zápisu disku/s|Zápis na disk|BajtyPerSekunda|Průměr|Průměrné bajty zapsané na disk během období sledování.|RoleInstanceId|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|Disk číst IOPS.|RoleInstanceId|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Vipony pro zápis disku.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Usedcapacity|Využitá kapacita|Bajty|Průměr|Využitá kapacita účtu|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|End-to-end latence úspěšné požadavky na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Možnost objektů blob|Kapacita objektu blob|Bajty|Průměr|Velikost úložiště používané službou Blob účtu úložiště v bajtů.|Objekt BlobType,vrstva|
|Počet objektů BlobCount|Počet objektů blob|Počet|Průměr|Počet objektů Blob ve službě objektu blob účtu úložiště.|Objekt BlobType,vrstva|
|ContainerCount|Počet kontejnerů objektů blob|Počet|Průměr|Počet kontejnerů ve službě objektu Blob účtu úložiště.|Žádný|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště používaného indexem ADLS Gen2 (Hierarchické) v bajtech.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|End-to-end latence úspěšné požadavky na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TabulkaKapacita|Kapacita tabulky|Bajty|Průměr|Velikost úložiště používané službou Table účtu úložiště v bajtech.|Žádný|
|Počet tabulek|Počet tabulek|Počet|Průměr|Číslo tabulky ve službě Table účtu úložiště.|Žádný|
|Počet tabulkových entií|Počet entit tabulky|Počet|Průměr|Počet entit tabulky ve službě Table účtu úložiště.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|End-to-end latence úspěšné požadavky na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Souborkapacita|Kapacita souboru|Bajty|Průměr|Velikost úložiště používaného službou File účtu úložiště v bajtech.|Fileshare|
|Počet souborů|Počet souborů|Počet|Průměr|Počet souborů ve službě Soubor účtu úložiště.|Fileshare|
|FileShareCount|Počet sdílení souborů|Počet|Průměr|Počet sdílených složek ve službě Soubor účtu úložiště.|Žádný|
|FileShareSnapshotCount|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve sdílené složce ve službě souborů účtu úložiště.|Fileshare|
|Soubor ShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště používané snímky v účtu úložiště souborové služby v bajtů.|Fileshare|
|FileShareQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit na velikost úložiště, které můžete použít službou Azure Files Service v bajtů.|Fileshare|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování,Sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování,Sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|geotype,ApiName,Ověřování,Sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|End-to-end latence úspěšné požadavky na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování,Sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování,Sdílení souborů|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště používané službou Fronty účtu úložiště v bajtech.|Žádný|
|Počet front|Počet front|Počet|Průměr|Počet fronty ve službě Fronty účtu úložiště.|Žádný|
|QueueMessageCount|Počet zpráv fronty|Počet|Průměr|Přibližný počet zpráv fronty ve službě Fronty účtu úložiště.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|End-to-end latence úspěšné požadavky na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Celkový počet volání|Celkový počet volání|Počet|Celkem|Celkový počet hovorů.|ApiName,OperationName,Region|
|Úspěšné volání|Úspěšné volání|Počet|Celkem|Počet úspěšných volání.|ApiName,OperationName,Region|
|Celkemchyby|Celkový počet chyb|Počet|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName,OperationName,Region|
|Blokované hovory|Blokovaná volání|Počet|Celkem|Počet volání, která překročila rychlost nebo limit kvóty.|ApiName,OperationName,Region|
|Serverchyby|Chyby serveru|Počet|Celkem|Počet volání s vnitřní chybou služby (kód odpovědi HTTP 5xx).|ApiName,OperationName,Region|
|Chyby klienta|Chyby klienta|Počet|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName,OperationName,Region|
|DataIn|Data v|Bajty|Celkem|Velikost příchozích dat v bajtech.|ApiName,OperationName,Region|
|Datový out|Data Out|Bajty|Celkem|Velikost odchozích dat v bajtech.|ApiName,OperationName,Region|
|Latence|Latence|Milisekund|Průměr|Latence v milisekundách.|ApiName,OperationName,Region|
|Volání TotalToken|Celkový počet volání tokenu|Počet|Celkem|Celkový počet volání tokenu.|ApiName,OperationName,Region|
|ZnakyPřeloženo|Přeložené znaky|Počet|Celkem|Celkový počet znaků v žádosti o příchozí text.|ApiName,OperationName,Region|
|ZnakyTrénované|Vycvičené postavy|Počet|Celkem|Celkový počet trénovaných znaků.|ApiName,OperationName,Region|
|Trvání speechsession|Doba trvání relace řeči|Sekundy|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName,OperationName,Region|
|TotalTransactions|Transakce celkem|Počet|Celkem|Celkový počet transakcí.|Žádný|
|Zpracované obrázky|Zpracované obrázky|Počet|Celkem| Počet transakcí pro zpracování obrazu.|apiName,FeatureName,Channel,Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálním počítačem|Žádný|
|Síťové vstupy|Fakturovatelné síťové (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozí provoz) (zastaralé)|Žádný|
|Síťové výstupy|Vyúčtování sítě (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz) (zastaralé)|Žádný|
|Bajty čtení disku|Bajty čtení disku|Bajty|Celkem|Bajty přečtené z disku během období sledování|Žádný|
|Bajty zápisu disku|Bajty zápisu disku|Bajty|Celkem|Bajty zapsané na disk během období sledování|Žádný|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|VIS pro čtení disku|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Viposlužby pro zápis disku|Žádný|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných k výbuchu|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Bajty pro čtení na disk/s|Čtení bajtů datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|SlotId|
|Bajty zápisu na disk/s|Bajty zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|SlotId|
|Operace čtení na disk/s|Operace čtení datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|SlotId|
|Operace zápisu na disk/s|Operace zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|SlotId|
|Na disk QD|Datový disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|SlotId|
|Bajty pro čtení na disk/s|Čtení bajtů/s disku operačního systému [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|Žádný|
|Bajty zápisu na disk na disk/s|Bajty zapisované na disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|Žádný|
|Operace čtení na disk na disk/s|Operace čtení disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|Žádný|
|Operace zápisu na disk na disk/s|Operace zápisu disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|Žádný|
|Operační systém na disku QD|Operační disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|Žádný|
|Čtení bajtů datového disku/s|Čtení bajtů datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|Lun|
|Bajty zápisu datového disku/s|Bajty za zápis datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|Lun|
|Operace čtení datového disku/s|Operace čtení datového disku/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|Lun|
|Operace zápisu datového disku/s|Operace zápisu datového disku/s (náhled)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|Lun|
|Hloubka fronty datového disku|Hloubka fronty datového disku (náhled)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|Lun|
|Bajty čtení disku operačního systému/s|Čtení bajtů/s disku operačního systému (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|Žádný|
|Bajty zápisu disku operačního systému/s|Bajty za zápis disku operačního systému/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|Žádný|
|Operace čtení disku operačního systému/s|Operace čtení disku operačního systému/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|Žádný|
|Operace zápisu disku operačního systému/s|Operace zápisu disku operačního systému/s (náhled)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|Žádný|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (náhled)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|Žádný|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky jsou počet aktuálních toků ve směru příchozí (provoz směřující do virtuálního mísy)|Žádný|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky jsou počet aktuálních toků v odchozím směru (provoz z virtuálního účtu)|Žádný|
|Maximální míra vytvoření příchozích toků|Maximální míra vytvoření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz do virtuálního provozu)|Žádný|
|Maximální míra vytvoření odchozích toků|Maximální míra vytvoření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (přenos y odchozí z virtuálního provozu)|Žádný|
|Požadavek na čtení mezipaměti premium data|Požadavek na čtení mezipaměti premium data (náhled)|Procento|Průměr|Požadavek na čtení mezipaměti premium data|Lun|
|Chybět čtení mezipaměti premium data|Premium Data Disk Cache Číst Miss (Náhled)|Procento|Průměr|Chybět čtení mezipaměti premium data|Lun|
|Přístupový přístup ke čtení mezipaměti disku premium oS|Premium OS Disk Cache Čtení Hit (Náhled)|Procento|Průměr|Přístupový přístup ke čtení mezipaměti disku premium oS|Žádný|
|Premium OS Disk Cache Čtení Miss|Premium OS Disk Cache Číst Miss (Náhled)|Procento|Průměr|Premium OS Disk Cache Čtení Miss|Žádný|
|Síť celkem|Síť celkem|Bajty|Celkem|Počet bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozím provozem)|Žádný|
|Síť out celkem|Síť out celkem|Bajty|Celkem|Počet bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz)|Žádný|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálním počítačem|VMName|
|Síťové vstupy|Fakturovatelné síťové (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozí provoz) (zastaralé)|VMName|
|Síťové výstupy|Vyúčtování sítě (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz) (zastaralé)|VMName|
|Bajty čtení disku|Bajty čtení disku|Bajty|Celkem|Bajty přečtené z disku během období sledování|VMName|
|Bajty zápisu disku|Bajty zápisu disku|Bajty|Celkem|Bajty zapsané na disk během období sledování|VMName|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|VIS pro čtení disku|VMName|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Viposlužby pro zápis disku|VMName|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných k výbuchu|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Bajty pro čtení na disk/s|Čtení bajtů datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|SlotId|
|Bajty zápisu na disk/s|Bajty zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|SlotId|
|Operace čtení na disk/s|Operace čtení datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|SlotId|
|Operace zápisu na disk/s|Operace zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|SlotId|
|Na disk QD|Datový disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|SlotId|
|Bajty pro čtení na disk/s|Čtení bajtů/s disku operačního systému [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|Žádný|
|Bajty zápisu na disk na disk/s|Bajty zapisované na disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|Žádný|
|Operace čtení na disk na disk/s|Operace čtení disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|Žádný|
|Operace zápisu na disk na disk/s|Operace zápisu disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|Žádný|
|Operační systém na disku QD|Operační disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|Žádný|
|Čtení bajtů datového disku/s|Čtení bajtů datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|LUN,Název VM|
|Bajty zápisu datového disku/s|Bajty za zápis datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|LUN,Název VM|
|Operace čtení datového disku/s|Operace čtení datového disku/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|LUN,Název VM|
|Operace zápisu datového disku/s|Operace zápisu datového disku/s (náhled)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|LUN,Název VM|
|Hloubka fronty datového disku|Hloubka fronty datového disku (náhled)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|LUN,Název VM|
|Bajty čtení disku operačního systému/s|Čtení bajtů/s disku operačního systému (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|VMName|
|Bajty zápisu disku operačního systému/s|Bajty za zápis disku operačního systému/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|VMName|
|Operace čtení disku operačního systému/s|Operace čtení disku operačního systému/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|VMName|
|Operace zápisu disku operačního systému/s|Operace zápisu disku operačního systému/s (náhled)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (náhled)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky jsou počet aktuálních toků ve směru příchozí (provoz směřující do virtuálního mísy)|VMName|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky jsou počet aktuálních toků v odchozím směru (provoz z virtuálního účtu)|VMName|
|Maximální míra vytvoření příchozích toků|Maximální míra vytvoření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz do virtuálního provozu)|VMName|
|Maximální míra vytvoření odchozích toků|Maximální míra vytvoření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (přenos y odchozí z virtuálního provozu)|VMName|
|Požadavek na čtení mezipaměti premium data|Požadavek na čtení mezipaměti premium data (náhled)|Procento|Průměr|Požadavek na čtení mezipaměti premium data|LUN,Název VM|
|Chybět čtení mezipaměti premium data|Premium Data Disk Cache Číst Miss (Náhled)|Procento|Průměr|Chybět čtení mezipaměti premium data|LUN,Název VM|
|Přístupový přístup ke čtení mezipaměti disku premium oS|Premium OS Disk Cache Čtení Hit (Náhled)|Procento|Průměr|Přístupový přístup ke čtení mezipaměti disku premium oS|VMName|
|Premium OS Disk Cache Čtení Miss|Premium OS Disk Cache Číst Miss (Náhled)|Procento|Průměr|Premium OS Disk Cache Čtení Miss|VMName|
|Síť celkem|Síť celkem|Bajty|Celkem|Počet bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozím provozem)|VMName|
|Síť out celkem|Síť out celkem|Bajty|Celkem|Počet bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálním počítačem|Žádný|
|Síťové vstupy|Fakturovatelné síťové (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozí provoz) (zastaralé)|Žádný|
|Síťové výstupy|Vyúčtování sítě (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz) (zastaralé)|Žádný|
|Bajty čtení disku|Bajty čtení disku|Bajty|Celkem|Bajty přečtené z disku během období sledování|Žádný|
|Bajty zápisu disku|Bajty zápisu disku|Bajty|Celkem|Bajty zapsané na disk během období sledování|Žádný|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|VIS pro čtení disku|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Viposlužby pro zápis disku|Žádný|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných k výbuchu|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Bajty pro čtení na disk/s|Čtení bajtů datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|SlotId|
|Bajty zápisu na disk/s|Bajty zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|SlotId|
|Operace čtení na disk/s|Operace čtení datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|SlotId|
|Operace zápisu na disk/s|Operace zápisu datového disku/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|SlotId|
|Na disk QD|Datový disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|SlotId|
|Bajty pro čtení na disk/s|Čtení bajtů/s disku operačního systému [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|Žádný|
|Bajty zápisu na disk na disk/s|Bajty zapisované na disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|Žádný|
|Operace čtení na disk na disk/s|Operace čtení disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|Žádný|
|Operace zápisu na disk na disk/s|Operace zápisu disku operačního systému/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|Žádný|
|Operační systém na disku QD|Operační disk QD [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|Žádný|
|Čtení bajtů datového disku/s|Čtení bajtů datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování|Lun|
|Bajty zápisu datového disku/s|Bajty za zápis datového disku/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování|Lun|
|Operace čtení datového disku/s|Operace čtení datového disku/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování|Lun|
|Operace zápisu datového disku/s|Operace zápisu datového disku/s (náhled)|CountPerSecond|Průměr|Zápis viop z jednoho disku během období sledování|Lun|
|Hloubka fronty datového disku|Hloubka fronty datového disku (náhled)|Počet|Průměr|Hloubka fronty datového disku (nebo délka fronty)|Lun|
|Bajty čtení disku operačního systému/s|Čtení bajtů/s disku operačního systému (náhled)|CountPerSecond|Průměr|Bajty/s čtení z jednoho disku během období sledování disku operačního systému|Žádný|
|Bajty zápisu disku operačního systému/s|Bajty za zápis disku operačního systému/s (náhled)|CountPerSecond|Průměr|Bajty/s zapsané na jeden disk během období sledování disku operačního systému|Žádný|
|Operace čtení disku operačního systému/s|Operace čtení disku operačního systému/s (náhled)|CountPerSecond|Průměr|Čtení vipozd z jednoho disku během období sledování disku operačního systému|Žádný|
|Operace zápisu disku operačního systému/s|Operace zápisu disku operačního systému/s (náhled)|CountPerSecond|Průměr|Zápis viop ů z jednoho disku během období sledování disku operačního systému|Žádný|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (náhled)|Počet|Průměr|Hloubka fronty disku operačního systému (nebo délka fronty)|Žádný|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky jsou počet aktuálních toků ve směru příchozí (provoz směřující do virtuálního mísy)|Žádný|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky jsou počet aktuálních toků v odchozím směru (provoz z virtuálního účtu)|Žádný|
|Maximální míra vytvoření příchozích toků|Maximální míra vytvoření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz do virtuálního provozu)|Žádný|
|Maximální míra vytvoření odchozích toků|Maximální míra vytvoření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (přenos y odchozí z virtuálního provozu)|Žádný|
|Požadavek na čtení mezipaměti premium data|Požadavek na čtení mezipaměti premium data (náhled)|Procento|Průměr|Požadavek na čtení mezipaměti premium data|Lun|
|Chybět čtení mezipaměti premium data|Premium Data Disk Cache Číst Miss (Náhled)|Procento|Průměr|Chybět čtení mezipaměti premium data|Lun|
|Přístupový přístup ke čtení mezipaměti disku premium oS|Premium OS Disk Cache Čtení Hit (Náhled)|Procento|Průměr|Přístupový přístup ke čtení mezipaměti disku premium oS|Žádný|
|Premium OS Disk Cache Čtení Miss|Premium OS Disk Cache Číst Miss (Náhled)|Procento|Průměr|Premium OS Disk Cache Čtení Miss|Žádný|
|Síť celkem|Síť celkem|Bajty|Celkem|Počet bajtů přijatých ve všech síťových rozhraních virtuálním počítačem (příchozím provozem)|Žádný|
|Síť out celkem|Síť out celkem|Bajty|Celkem|Počet bajtů ve všech síťových rozhraních virtuálním počítačem (odchozí provoz)|Žádný|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití procesoru|Využití procesoru|Počet|Průměr|Využití procesoru na všech jádrech v milijádrích.|containerName|
|Využití paměti|Využití paměti|Bajty|Průměr|Celkové využití paměti v bajtu.|containerName|
|NetworkBytesReceivedPerSecond|Přijaté síťové bajty za sekundu|Bajty|Průměr|Počet přijatých síťových bajtů za sekundu.|Žádný|
|NetworkBytesTransmittedPerSecond|Přenesené síťové bajty za sekundu|Bajty|Průměr|Sítě se přenášejí za sekundu.|Žádný|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registry

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Celkový počet tahů|Počet|Průměr|Celkový počet tažení obrázků|Žádný|
|ÚspěšnýPullCount|Úspěšný počet pull|Počet|Průměr|Počet úspěšných bitových naležek|Žádný|
|TotalPushCount|Celkový počet nabízených tiskovin|Počet|Průměr|Celkový počet nabízených oznámení obrázků|Žádný|
|Úspěšný počet stisknuté hod.|Úspěšný počet nabízených kliby|Počet|Průměr|Počet úspěšných nabízených oznámení obrázku|Žádný|
|Doba trvání běhu|Doba trvání spuštění|Milisekund|Celkem|Doba trvání běhu v milisekundách|Žádný|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/spravované clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Počet|Průměr|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Žádný|
|kube_node_status_allocatable_memory_bytes|Celkové množství dostupné paměti ve spravovaném clusteru|Bajty|Průměr|Celkové množství dostupné paměti ve spravovaném clusteru|Žádný|
|kube_pod_status_ready|Počet podů ve stavu Připraveno|Počet|Průměr|Počet podů ve stavu Připraveno|obor názvů,pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Počet|Průměr|Stavy pro různé podmínky uzlu|podmínka,stav,stav2,uzel|
|kube_pod_status_phase|Počet podů podle fáze|Počet|Průměr|Počet podů podle fáze|fáze,obor názvů,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úspěšné požadavky|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky provedené vlastním zprostředkovatelem|HttpMethod,CallPath,StatusCode|
|Požadavky na selhání|Neúspěšné požadavky|Počet|Celkem|Získá dostupné protokoly pro vlastní zprostředkovatele prostředků|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NicReadThroughput|Propustnost čtení (síť)|BajtyPerSekunda|Průměr|Propustnost čtení síťového rozhraní na zařízení v období vykazování pro všechny svazky v bráně.|Název_instance|
|Průchodnost zápisu NIC|Propustnost zápisu (síť)|BajtyPerSekunda|Průměr|Propustnost zápisu síťového rozhraní na zařízení v období vykazování pro všechny svazky v bráně.|Název_instance|
|CloudReadThroughputPerShare|Propustnost stahování v cloudu (sdílení)|BajtyPerSekunda|Průměr|Propustnost stahování do Azure ze sdílené složky během období vykazování.|Sdílet|
|CloudUploadThroughputPerShare|Propustnost nahrávání v cloudu (sdílení)|BajtyPerSekunda|Průměr|Propustnost nahrávání do Azure ze sdílené složky během období vykazování.|Sdílet|
|BajtůNahrálToCloudPerShare|Nahrané bajty v cloudu (sdílet)|Bajty|Průměr|Celkový počet bajtů, které se nahrají do Azure ze sdílené složky během vykazovaného období.|Sdílet|
|Celková kapacita|Celková kapacita|Bajty|Průměr|Celková kapacita|Žádný|
|Dostupná kapacita|Dostupná kapacita|Bajty|Průměr|Dostupná kapacita v bajtech během vykazovaného období.|Žádný|
|CloudUploadThroughput|Propustnost nahrávání v cloudu|BajtyPerSekunda|Průměr|Propustnost cloudu nahrát do Azure během období vykazování.|Žádný|
|CloudReadThroughput|Propustnost stahování v cloudu|BajtyPerSekunda|Průměr|Propustnost cloudu ke stažení do Azure během období vykazování.|Žádný|
|BajtyNahranéToCloud|Nahrané bajty v cloudu (zařízení)|Bajty|Průměr|Celkový počet bajtů, které se nahrají do Azure ze zařízení během období vykazování.|Žádný|
|Využití virtuálního procesoru HyperV|Edge Compute – procentuální procesor|Procento|Průměr|Procento využití procesoru|Název_instance|
|Využití hypervmemory|Edge Compute – využití paměti|Procento|Průměr|Množství paměti RAM v provozu|Název_instance|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetDistributionbyKlasifikace|Rozdělení aktiv podle klasifikace|Počet|Celkem|Udává počet aktiv s určitou přiřazenou klasifikací, tj.|Klasifikace,Zdroj|
|AssetDistributionByStorageType|Rozdělení majetku podle typu úložiště|Počet|Celkem|Označuje počet datových zdrojů s určitým typem úložiště.|StorageType|
|NumberOfAssetsWithClassifications|Počet aktiv s alespoň jednou klasifikací|Počet|Průměr|Označuje počet datových zdrojů s alespoň jednou klasifikací značek.|Žádný|
|ScanZrušeno|Prohledávací sken byl zrušen.|Počet|Celkem|Označuje počet prohledávacích.|Žádný|
|Skenování bylo dokončeno.|Prohledávací sken byl dokončen.|Počet|Celkem|Označuje počet úspěšně dokončených prohledávek.|Žádný|
|ScanFailed|Prohledávací sken se nezdařil.|Počet|Celkem|Označuje počet neúspěšných prohledávek.|Žádný|
|ScanTimeTaken|Doba skenování|Sekundy|Celkem|Označuje celkový čas skenování v sekundách.|Žádný|
|CatalogActiveUsers|Denní aktivní uživatelé|Počet|Celkem|Počet aktivních uživatelů denně|Žádný|
|Využití katalogu|Distribuce využití podle operace|Počet|Celkem|Uveďte počet operací, které uživatel provede v katalogu, tj.|Operace|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Selhání spustí|Selhání spuštění|Počet|Celkem||pipelineName,activityName|
|Úspěšné spuštění|Úspěšné spuštění|Počet|Celkem||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/továrny

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Spuštění pipelinefailedruns|Metriky spuštění neúspěšného kanálu|Počet|Celkem||Typ selhání,Název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Počet|Celkem||Typ selhání,Název|
|PipelineCancelledRuns|Zrušené kanály spouští metriky|Počet|Celkem||Typ selhání,Název|
|ActivityFailedRuns|Neúspěšná aktivita spustí metriky|Počet|Celkem||ActivityType,PipelineName,FailureType,Name|
|Úspěšné spuštění aktivity|Úspěšná aktivita spustí metriky|Počet|Celkem||ActivityType,PipelineName,FailureType,Name|
|ActivityCancelledRuns|Zrušená aktivita spustí metriky|Počet|Celkem||ActivityType,PipelineName,FailureType,Name|
|Spuštění spouštění|Metriky spuštění aktivační události se nezdařilo.|Počet|Celkem||Název,Typ selhání|
|TriggerSucceededRuns|Úspěšné aktivační události spustí metriky|Počet|Celkem||Název,Typ selhání|
|TriggerCancelledRuns|Zrušené aktivační události spustí metriky|Počet|Celkem||Název,Typ selhání|
|IntegraceRuntimeCpuProcento|Využití procesoru runtime integrace|Procento|Průměr||IntegrationRunTimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Integrační paměť s runtime|Bajty|Průměr||IntegrationRunTimeName,NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Doba trvání fronty za běhu integrace|Sekundy|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Délka fronty za běhu integrace|Počet|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Počet dostupných uzlů za běhu integrace|Počet|Průměr||IntegrationRuntimeName|
|MaxAllowedResourceCount|Počet maximálních povolených entit|Počet|Maximum||Žádný|
|MaxAllowedFactorySizeIngbUnits|Maximální povolená tovární velikost (jednotka GB)|Počet|Maximum||Žádný|
|ResourceCount|Celkový počet entit|Počet|Maximum||Žádný|
|FactorySizeInGbUnits|Celková velikost továrny (GB jednotka)|Počet|Maximum||Žádný|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedÚspěch|Úspěšná pracovní místa|Počet|Celkem|Počet úspěšných úloh.|Žádný|
|JobendedSelhání|Neúspěšné úlohy|Počet|Celkem|Počet neúspěšných úloh.|Žádný|
|Úloha Byla zrušena.|Zrušené úlohy|Počet|Celkem|Počet zrušených úloh.|Žádný|
|JobauendedÚspěch|Úspěšný čas AU|Sekundy|Celkem|Celkový čas AU pro úspěšné úlohy.|Žádný|
|JobauendedSelhání|Neúspěšný čas au|Sekundy|Celkem|Celkový čas AU pro neúspěšné úlohy.|Žádný|
|JobAUEndedZrušeno|Zrušený čas AU|Sekundy|Celkem|Celkový čas AU pro zrušené úlohy.|Žádný|
|Pracovní plocha|Úlohy ve fázi|Počet|Celkem|Počet úloh v každé fázi.|Žádný|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Celkové úložiště|Bajty|Maximum|Celkové množství dat uložených v účtu.|Žádný|
|DataWritten|Zapsaná data|Bajty|Celkem|Celkové množství dat zapsaných na účet.|Žádný|
|Čtení dat|Čtení dat|Bajty|Celkem|Celkové množství dat přečtených z účtu.|Žádný|
|Požadavky na zápis|Psát požadavky|Počet|Celkem|Počet požadavků na zápis dat do účtu.|Žádný|
|ReadRequests|Číst požadavky|Počet|Celkem|Počet požadavků na čtení dat k účtu.|Žádný|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ShareCount|Odeslané sdílené složky|Počet|Maximum|Počet odeslaných akcií na účtu|Název_sdílené_položky|
|ShareSubscriptionCount|Přijaté akcie|Počet|Maximum|Počet přijatých akcií na účtu|ShareSubscriptionName|
|Úspěšné synchronizace akcií|Odeslané snímky sdílené složky byly úspěšné|Počet|Počet|Počet úspěšných snímků odeslané sdílené složky v účtu|Žádný|
|Synchronizace služby FailedShare|Odeslané snímky se nezdařilo sdílení|Počet|Počet|Počet neúspěšných snímků odeslané sdílené složky v účtu|Žádný|
|SucceededShareSubscriptionSynchronizations|Přijaté sdílené složky úspěšné snímky|Počet|Počet|Počet přijatých snímků sdílené složky v účtu|Žádný|
|Synchronizace služby FailedShareSubscription|Přijaté snímky se nezdařily o sdílenou složku|Počet|Počet|Počet neúspěšných snímků přijaté sdílené složky v účtu|Žádný|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Průměr|Procento procesoru|Žádný|
|memory_percent|Procento paměti|Procento|Průměr|Procento paměti|Žádný|
|io_consumption_percent|IO procent|Procento|Průměr|IO procent|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Použité úložiště|Bajty|Průměr|Použité úložiště|Žádný|
|storage_limit|Limit úložiště|Bajty|Maximum|Limit úložiště|Žádný|
|serverlog_storage_percent|Procento úložiště protokolu serveru|Procento|Průměr|Procento úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Použité úložiště protokolu serveru|Bajty|Průměr|Použité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Průměr|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádný|
|seconds_behind_master|Zpoždění replikace v sekundách|Počet|Maximum|Zpoždění replikace v sekundách|Žádný|
|backup_storage_used|Použité úložiště záloh|Bajty|Průměr|Použité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Síť mezi aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v napříč aktivními připojeními|Žádný|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Průměr|Procento procesoru|Žádný|
|memory_percent|Procento paměti|Procento|Průměr|Procento paměti|Žádný|
|io_consumption_percent|IO procent|Procento|Průměr|IO procent|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Použité úložiště|Bajty|Průměr|Použité úložiště|Žádný|
|storage_limit|Limit úložiště|Bajty|Maximum|Limit úložiště|Žádný|
|serverlog_storage_percent|Procento úložiště protokolu serveru|Procento|Průměr|Procento úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Použité úložiště protokolu serveru|Bajty|Průměr|Použité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádný|
|seconds_behind_master|Zpoždění replikace v sekundách|Počet|Maximum|Zpoždění replikace v sekundách|Žádný|
|backup_storage_used|Použité úložiště záloh|Bajty|Průměr|Použité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Síť mezi aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v napříč aktivními připojeními|Žádný|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Průměr|Procento procesoru|Žádný|
|memory_percent|Procento paměti|Procento|Průměr|Procento paměti|Žádný|
|io_consumption_percent|IO procent|Procento|Průměr|IO procent|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Použité úložiště|Bajty|Průměr|Použité úložiště|Žádný|
|storage_limit|Limit úložiště|Bajty|Maximum|Limit úložiště|Žádný|
|serverlog_storage_percent|Procento úložiště protokolu serveru|Procento|Průměr|Procento úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Použité úložiště protokolu serveru|Bajty|Průměr|Použité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádný|
|backup_storage_used|Použité úložiště záloh|Bajty|Průměr|Použité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Síť mezi aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v napříč aktivními připojeními|Žádný|
|pg_replica_log_delay_in_seconds|Zpoždění replik|Sekundy|Maximum|Zpoždění repliky v sekundách|Žádný|
|pg_replica_log_delay_in_bytes|Maximální zpoždění mezi replikami|Bajty|Maximum|Zpoždění v bajtů nejvíce zaostávající repliky|Žádný|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Průměr|Procento procesoru|Žádný|
|memory_percent|Procento paměti|Procento|Průměr|Procento paměti|Žádný|
|Iops|IOPS|Počet|Průměr|IO Operace za sekundu|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Použité úložiště|Bajty|Průměr|Použité úložiště|Žádný|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Síť mezi aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v napříč aktivními připojeními|Žádný|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Průměr|Procento procesoru|Žádný|
|memory_percent|Procento paměti|Procento|Průměr|Procento paměti|Žádný|
|Iops|IOPS|Počet|Průměr|IO Operace za sekundu|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Použité úložiště|Bajty|Průměr|Použité úložiště|Žádný|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Síť mezi aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v napříč aktivními připojeními|Žádný|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádný|
|connections_succeeded|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádný|
|maximum_used_transactionIDs|Maximální použitá ID transakcí|Počet|Průměr|Maximální použitá ID transakcí|Žádný|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslání telemetrické zprávy|Počet|Celkem|Počet telemetrických zpráv mezi zařízeními a cloudy, které se pokusily odeslat do služby IoT hub|Žádný|
|d2c.telemetry.ingress.success|Odeslané telemetrické zprávy|Počet|Celkem|Počet telemetrických zpráv mezi zařízeními a cloudy, které byly úspěšně odeslány do služby IoT hub|Žádný|
|c2d.commands.egress.complete.success|Dokončené dodávky zpráv C2D|Počet|Celkem|Počet doručování zpráv z cloudu na zařízení, které zařízení úspěšně dokončilo|Žádný|
|c2d.commands.egress.abandon.success|Zprávy C2D byly opuštěny.|Počet|Celkem|Počet zpráv mezi cloudy a zařízení, které zařízení opustilo|Žádný|
|c2d.commands.egress.reject.success|Zprávy C2D byly odmítnuty.|Počet|Celkem|Počet zpráv mezi cloudy a zařízení, které zařízení odmítlo|Žádný|
|C2DMessagesVypršela|Platnost zpráv C2D vypršela (náhled)|Počet|Celkem|Počet zpráv s prošlým platností v oblasti cloudu se zařízením|Žádný|
|devices.totalDevices|Zařízení celkem (zastaralé)|Počet|Celkem|Počet zařízení registrovaných do vašeho centra IoT hub|Žádný|
|devices.connectedDevices.allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených k vašemu centru IoT hub|Žádný|
|d2c.telemetry.egress.success|Směrování: doručovány telemetrické zprávy|Počet|Celkem|Počet, kolikrát byly zprávy úspěšně doručeny do všech koncových bodů pomocí směrování služby IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se zvýší o jeden pro každou úspěšnou doručení. Pokud je zpráva doručena do stejného koncového bodu vícekrát, tato hodnota se zvýší o jeden pro každou úspěšnou doručení.|Žádný|
|d2c.telemetry.egress.dropped|Směrování: zprávy telemetrie vynechány |Počet|Celkem|Počet, kolikrát zprávy byly vynechány směrování služby IoT Hub z důvodu mrtvé koncové body. Tato hodnota nepočítá zprávy doručovány do záložní trasy jako vynecháné zprávy nejsou doručovány tam.|Žádný|
|d2c.telemetry.egress.orphaned|Směrování: telemetrické zprávy osamocené |Počet|Celkem|Počet osamocených zpráv směrováním služby IoT Hub, protože neodpovídaly žádným pravidlům směrování (včetně záložního pravidla). |Žádný|
|d2c.telemetry.egress.invalid|Směrování: telemetrické zprávy nekompatibilní|Počet|Celkem|Počet směrování služby IoT Hub se nezdařilo doručit zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakování.|Žádný|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručovány do záložní|Počet|Celkem|Počet, kolikrát ioT Hub směrování doručovány zprávy do koncového bodu přidruženého k záložní trasy.|Žádný|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů centra událostí.|Žádný|
|d2c.endpoints.latency.eventHubs|Směrování: latence zpráv pro centrum událostí|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem zpráv do koncového bodu centra událostí.|Žádný|
|d2c.endpoints.egress.serviceBusQueues|Směrování: zprávy doručené do fronty služby Service Bus|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů fronty služby Service Bus.|Žádný|
|d2c.endpoints.latency.serviceBusQueues|Směrování: latence zpráv pro frontu služby Service Bus|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zprávy do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu fronty služby Service Bus.|Žádný|
|d2c.endpoints.egress.serviceBusTémata|Směrování: zprávy doručené do tématu sběrnice|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů tématu služby Service Bus.|Žádný|
|d2c.endpoints.latency.serviceBusTopics|Směrování: latence zpráv pro téma služby Service Bus|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu tématu služby Service Bus.|Žádný|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručené do zpráv/událostí|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do předdefinovaného koncového bodu (zprávy nebo události).|Žádný|
|d2c.endpoints.latency.builtIn.events|Směrování: latence zpráv pro zprávy nebo události|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do předdefinovaného koncového bodu (zprávy/události).|Žádný|
|d2c.endpoints.egress.storage|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy koncovým bodům úložiště.|Žádný|
|d2c.endpoints.latency.storage|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu úložiště.|Žádný|
|d2c.endpoints.egress.storage.bytes|Směrování: data dodaná do úložiště|Bajty|Celkem|Množství dat (bajtů) Směrování služby IoT Hub dodané do koncových bodů úložiště.|Žádný|
|objekty blob d2c.endpoints.egress.storage.|Směrování: objekty BLOB dodané do úložiště|Počet|Celkem|Kolikrát směrování služby IoT Hub doručovalo objekty blob do koncových bodů úložiště.|Žádný|
|EventGridDeliveries|Dodávek v gridu událostí (náhled)|Počet|Celkem|Počet událostí ioT hub publikovaných do mřížky událostí. Dimenzi Výsledek použijte pro počet úspěšných a neúspěšných požadavků. Dimenze EventType zobrazuje typhttps://aka.ms/ioteventgrid)události ( .|ResourceId,Výsledek,EventType|
|Latence eventgridu|Latence sítě událostí (náhled)|Milisekund|Průměr|Průměrná čekací doba (milisekund) od doby, kdy byla generována událost centra Iot Hub, do doby, kdy byla událost publikována do mřížky událostí. Toto číslo je průměr mezi všemi typy událostí. Pomocí dimenze EventType můžete zobrazit latenci určitého typu události.|ResourceId,EventType|
|Služby RoutingDeliveries|Dodávky směrování (náhled)|Milisekund|Celkem|Počet pokusů služby IoT Hub doručit zprávy do všech koncových bodů pomocí směrování. Chcete-li zobrazit počet úspěšných nebo neúspěšných pokusů, použijte dimenzi Výsledek. Chcete-li zobrazit důvod selhání, jako je neplatný, vynechání nebo osamocené, použijte FailureReasonCategory dimenze. Dimenze EndpointName a EndpointType můžete také použít k pochopení počtu zpráv doručených do různých koncových bodů. Hodnota metriky se zvyšuje o jeden pro každý pokus o doručení, včetně pokud je zpráva doručena do více koncových bodů nebo pokud je zpráva doručena do stejného koncového bodu vícekrát.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Výsledek,Zdroj směrování|
|RoutingDeliveryLatency|Latence doručení směrování (náhled)|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zprávy do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu. Dimenze EndpointName a EndpointType můžete použít k pochopení latence různých koncových bodů.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Úspěšná dvojitá čtení ze zařízení|Počet|Celkem|Počet všech úspěšných dvojčetů iniciovaných zařízením.|Žádný|
|d2c.twin.read.selhání|Selhání dvojčete čte ze zařízení|Počet|Celkem|Počet všech neúspěšných dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.read.size|Velikost odezvy dvojčete čte ze zařízení|Bajty|Průměr|Průměr, min a max všech úspěšných zařízení-inicioval dvojče čte.|Žádný|
|d2c.twin.update.success|Úspěšné aktualizace dvojčete ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.update.selhání|Neúspěšné aktualizace dvojčete ze zařízení|Počet|Celkem|Počet všech neúspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.update.size|Velikost aktualizací dvojčete ze zařízení|Bajty|Průměr|Průměrná, min a maximální velikost všech úspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|c2d.methods.success|Úspěšné přímé vyvolání metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádný|
|c2d.methods.selhání|Neúspěšná přímá metoda vyvolání|Počet|Celkem|Počet všech neúspěšných volání přímé metody.|Žádný|
|c2d.methods.requestVelikost|Velikost požadavku na vyvolání přímé metody|Bajty|Průměr|Průměr, min a max všech úspěšných požadavků přímé metody.|Žádný|
|c2d.methods.responseVelikost|Velikost odpovědi přímé metody vyvolání|Bajty|Průměr|Průměr, min a max všech úspěšných přímých odpovědí metod.|Žádný|
|c2d.twin.read.success|Úspěšné dvojče čte z back-endu|Počet|Celkem|Počet všech úspěšných back-end iniciované dvojče čte.|Žádný|
|c2d.twin.read.selhání|Neúspěšné dvojče čte ze zadního konce|Počet|Celkem|Počet všech neúspěšných čtení dvojčete iniciovaného back-endem.|Žádný|
|c2d.twin.read.size|Velikost odpovědi dvojčete čte ze back-endu|Bajty|Průměr|Průměr, min a max všech úspěšných back-end-inicioval dvojče čte.|Žádný|
|c2d.twin.update.success|Úspěšné aktualizace dvojčat z back-endu|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|c2d.twin.update.selhání|Neúspěšné aktualizace dvojčete ze back-endu|Počet|Celkem|Počet všech neúspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|c2d.twin.update.size|Velikost twin aktualizace z back-endu|Bajty|Průměr|Průměrná, min a maximální velikost všech úspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|twinQueries.success|Úspěšné dotazy na dvojče|Počet|Celkem|Počet všech úspěšných twin dotazy.|Žádný|
|twinQueries.failure|Neúspěšné dotazy na dvojče|Počet|Celkem|Počet všech neúspěšných dotazů dvojčat.|Žádný|
|twinQueries.resultSize|Velikost výsledku twin queries|Bajty|Průměr|Průměr, min a max velikost výsledku všech úspěšných twin dotazů.|Žádný|
|jobs.createTwinUpdateJob.success|Úspěšné výtvory úloh s dvojčaty|Počet|Celkem|Počet všech úspěšných vytvoření úloh s dvojčaty.|Žádný|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úloh s dvojčaty|Počet|Celkem|Počet všech neúspěšných vytvoření úloh s dvojčaty aktualizací.|Žádný|
|jobs.createDirectMethodJob.success|Úspěšné vytváření úloh vyvolání metody|Počet|Celkem|Počet všech úspěšných vytvoření úloh přímého vyvolání metody.|Žádný|
|jobs.createDirectMethodJob.failure|Neúspěšné vytváření úloh vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh přímého vyvolání metody.|Žádný|
|jobs.listJobs.success|Úspěšná volání do seznamu úloh|Počet|Celkem|Počet všech úspěšných volání do seznamu úloh.|Žádný|
|jobs.listJobs.selhání|Neúspěšná volání seznam úloh|Počet|Celkem|Počet všech neúspěšných volání seznam úloh.|Žádný|
|jobs.cancelJob.success|Úspěšné zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání zrušit úlohu.|Žádný|
|jobs.cancelJob.failure|Neúspěšné zrušení úlohy|Počet|Celkem|Počet všech neúspěšných volání zrušit úlohu.|Žádný|
|jobs.queryJobs.success|Dotazy na úspěšnou úlohu|Počet|Celkem|Počet všech úspěšných volání dotazovacích úloh.|Žádný|
|jobs.queryJobs.failure|Dotazy na úlohy se nezdařilo|Počet|Celkem|Počet všech neúspěšných volání dotazovacích úloh.|Žádný|
|jobs.completed|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádný|
|jobs.failed|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádný|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezení|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnost zařízení|Žádný|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet dnes použitých zpráv|Žádný|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajty přenesené do a z všech zařízení připojených k IotHub|Žádný|
|deviceDataUsageV2|Celkové využití dat zařízení (náhled)|Bajty|Celkem|Bajty přenesené do a z všech zařízení připojených k IotHub|Žádný|
|totalDeviceCount|Celkový počet zařízení (náhled)|Počet|Průměr|Počet zařízení registrovaných do vašeho centra IoT hub|Žádný|
|připojenýPočet zařízení|Připojená zařízení (náhled)|Počet|Průměr|Počet zařízení připojených k vašemu centru IoT hub|Žádný|
|Konfigurace|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádný|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Pokusy o registraci|Pokusy o registraci|Počet|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName,IotHubName,Stav|
|Přiřazení zařízení|Přiřazená zařízení|Počet|Celkem|Počet zařízení přiřazených k centru IoT|ProvisioningServiceName,IotHubName|
|Pokusy o atestaci|Pokusy o atestaci|Počet|Celkem|Počet pokusů o ateace zařízení|ProvisioningServiceName,Stav,Protokol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Přidat oblast|Přidána oblast|Počet|Počet|Přidána oblast|Region (Oblast)|
|Dostupné úložiště|Úložiště k dispozici|Bajty|Celkem|Celkové dostupné úložiště nahlášené při rozlišovací schopnosti 5 minut|CollectionName,Název_databáze,Oblast|
|CassandraConnectionClosures|Uzavření připojení Cassandra|Počet|Celkem|Počet uzavřených připojení Cassandra, hlášených v 1 minutě granularity|APIType,Oblast,Důvod uzavření|
|CassandraKeyspaceOdstranit|Cassandra Keyspace odstraněna|Počet|Počet|Cassandra Keyspace odstraněna|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra Keyspace Propustnost aktualizována|Počet|Počet|Cassandra Keyspace Propustnost aktualizována|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceAktualizace|Cassandra Keyspace Aktualizováno|Počet|Počet|Cassandra Keyspace Aktualizováno|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Cassandra požadovat poplatky|Počet|Celkem|Ru spotřebované pro cassandra požadavky|APIType,Název_databáze,Název_kolekce,Oblast,Typ operace,Typ_prostředku|
|CassandraPožadavky|Cassandra žádosti|Počet|Počet|Počet žádostí společnosti Cassandra|APIType,Název_databáze,Název_kolekce,Region,Typ operace,Typ_prostředku,Kód chyby|
|CassandraTableDelete|Cassandra tabulka odstraněna|Počet|Počet|Cassandra tabulka odstraněna|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUpdate|Propustnost tabulky Cassandra byla aktualizována.|Počet|Počet|Propustnost tabulky Cassandra byla aktualizována.|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|CassandraTableUpdate|Cassandra Tabulka aktualizována|Počet|Počet|Cassandra Tabulka aktualizována|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|Vytvořitúčet|Účet vytvořen|Počet|Počet|Účet vytvořen|Žádný|
|Využití dat|Využití dat|Bajty|Celkem|Celkové využití dat vykázané při rozlišovací schopnosti 5 minut|CollectionName,Název_databáze,Oblast|
|Odstranitúčet|Účet byl odstraněn.|Počet|Počet|Účet byl odstraněn.|Žádný|
|DocumentCount|Počet dokumentů|Počet|Celkem|Celkový počet dokumentů hlášených při rozlišovací schopnosti 5 minut|CollectionName,Název_databáze,Oblast|
|DocumentQuota|Kvóta dokumentu|Bajty|Celkem|Celková kvóta úložiště vykázaná při rozlišovací schopnost5 minut|CollectionName,Název_databáze,Oblast|
|GremlinDatabaseDelete|Databáze Gremlin byla odstraněna.|Počet|Počet|Databáze Gremlin byla odstraněna.|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|Aktualizace GremlinDatabaseThroughputUpdate|Propustnost databáze Gremlin byla aktualizována.|Počet|Počet|Propustnost databáze Gremlin byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Databáze Gremlin byla aktualizována.|Počet|Počet|Databáze Gremlin byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graf odstraněn|Počet|Počet|Gremlin Graf odstraněn|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|Aktualizace GremlinGraphThroughputUpdate|Propustnost gremlinového grafu byla aktualizována.|Počet|Počet|Propustnost gremlinového grafu byla aktualizována.|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|GremlinGraphUpdate|Gremlin Graf aktualizován|Počet|Počet|Gremlin Graf aktualizován|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|Využití indexu|Využití indexu|Bajty|Celkem|Celkové využití indexu hlášené při rozlišovací schopnosti 5 minut|CollectionName,Název_databáze,Oblast|
|Požadavky na metadata|Požadavky na metadata|Počet|Počet|Počet požadavků metadat. Cosmos DB udržuje kolekci metadat systému pro každý účet, který umožňuje vytvořit výčet kolekcí, databází atd.|Název_databáze,Název_kolekce,Oblast,Stavový kód,Role|
|MongoCollectionDelete|Kolekce Mongo byla odstraněna.|Počet|Počet|Kolekce Mongo byla odstraněna.|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUpdate|Propustnost kolekce Mongo byla aktualizována.|Počet|Počet|Propustnost kolekce Mongo byla aktualizována.|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|Aktualizace MongoCollection|Kolekce Mongo byla aktualizována|Počet|Počet|Kolekce Mongo byla aktualizována|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|Aktualizace databáze MongoDB|Databáze Mongo byla aktualizována|Počet|Počet|Databáze Mongo byla aktualizována|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Databáze Mongo byla odstraněna.|Počet|Počet|Databáze Mongo byla odstraněna.|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Propustnost databáze Mongo byla aktualizována.|Počet|Počet|Propustnost databáze Mongo byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Poplatek za žádost Mongo|Počet|Celkem|Spotřebované jednotky požadavku Mongo|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode,Stav|
|MongoPožadavky|Mongo žádosti|Počet|Počet|Počet posudků Mongo|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode,Stav|
|MongoRequestsCount|Míra požadavků Mongo|CountPerSecond|Průměr|Mongo požadavek Počet za sekundu|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode|
|MongoRequestsOdstranit|Rychlost odstranění požadavku Mongo|CountPerSecond|Průměr|Mongo Odstranit požadavek za sekundu|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode|
|MongoRequestsInsert|Rychlost požadavků vložení mongo|CountPerSecond|Průměr|Počet vložení Mongo za sekundu|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode|
|MongoRequestsQuery|Rychlost požadavků na dotazy Mongo|CountPerSecond|Průměr|Požadavek na dotaz Mongo za sekundu|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode|
|MongoRequestsAktualizace|Rychlost požadavků na aktualizaci mongo|CountPerSecond|Průměr|Požadavek na aktualizaci Mongo za sekundu|Název_databáze,Název_kolekce,Oblast,CommandName,ErrorCode|
|Normalizovaná spotřeba|Normalizovaná spotřeba ŽP|Procento|Maximum|Maximální procento spotřeby RU za minutu|CollectionName,Název_databáze,Oblast|
|Zřízená průchodnost|Zřízená propustnost|Počet|Maximum|Zřízená propustnost|Název_databáze,Název_kolekce|
|Selhání oblasti|Převzetí selhání oblasti|Počet|Počet|Převzetí selhání oblasti|Žádný|
|Odebrat oblast|Oblast odebrána|Počet|Počet|Oblast odebrána|Region (Oblast)|
|Latence replikace|Latence replikace P99|Milisekund|Průměr|Latence replikace P99 napříč zdrojovými a cílovými oblastmi pro geograficky povolený účet|SourceRegion,TargetRegion|
|ServerSideLatency|Latence na straně serveru|Milisekund|Průměr|Latence na straně serveru|Název_databáze,Název_kolekce,Oblast,Režim připojení,Typ operace,PublicAPIType|
|Dostupnost služby|Dostupnost služby|Procento|Průměr|Dostupnost požadavků na účet v rozlišovací schopnost jedné hodiny, dne nebo měsíce|Žádný|
|SqlContainerDelete|Kontejner SQL odstraněn|Počet|Počet|Kontejner SQL odstraněn|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|Aktualizace SQLContainerThroughputUpdate|Propustnost kontejneru SQL byla aktualizována.|Počet|Počet|Propustnost kontejneru SQL byla aktualizována.|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|SqlContainerUpdate|Kontejner SQL byl aktualizován|Počet|Počet|Kontejner SQL byl aktualizován|ResourceName,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequestRequest|
|SqlDatabaseDelete|Databáze SQL byla odstraněna.|Počet|Počet|Databáze SQL byla odstraněna.|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|Aktualizace sqlDatabaseThroughputUpdate|Propustnost databáze SQL byla aktualizována.|Počet|Počet|Propustnost databáze SQL byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Aktualizace sqldatabaseupdate|Databáze SQL byla aktualizována.|Počet|Počet|Databáze SQL byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete|Odstraněná tabulka AzureTable|Počet|Počet|Odstraněná tabulka AzureTable|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdate|Propustnost tabulky AzureTable byla aktualizována.|Počet|Počet|Propustnost tabulky AzureTable byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Aktualizace tabletableupdate|Tabulka AzureTable byla aktualizována.|Počet|Počet|Tabulka AzureTable byla aktualizována.|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Jednotky TotalRequest|Celkový počet jednotek požadavků|Počet|Celkem|Spotřebované jednotky požadavku|Název_databáze,Název_kolekce,Oblast,StatusCode,Typ operace,Stav|
|TotalRequests|Požadavky celkem|Počet|Počet|Počet posudků|Název_databáze,Název_kolekce,Oblast,StatusCode,Typ operace,Stav|
|Aktualizovat klávesy AccountKeys|Klíče účtu byly aktualizovány.|Počet|Počet|Klíče účtu byly aktualizovány.|Keytype|
|Aktualizace Nastavení sítě|Aktualizováno nastavení sítě účtů|Počet|Počet|Aktualizováno nastavení sítě účtů|Žádný|
|Aktualizovat Nastavení replikace účtů|Aktualizováno nastavení replikace účtu|Počet|Počet|Aktualizováno nastavení replikace účtu|Žádný|
|UpdateDiagnosticsSettings|Aktualizováno nastavení diagnostiky účtu|Počet|Počet|Aktualizováno nastavení diagnostiky účtu|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Počet transakcí|Počet transakcí|Počet|Počet|Celkový počet transakcí|Počet transakcí|
|Počet úspěšných|Success Count|Počet|Počet|Počet úspěšných transakcí|Počet úspěšných|
|Počet selhání|Failure Count|Počet|Počet|Počet neúspěšných transakcí|Počet selhání|
|SuccessLatency|Latence úspěchu|Milisekund|Průměr|Latence úspěšných transakcí|Počet úspěšných|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domény

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných v tomto tématu|Téma|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí, které se nepodařilo publikovat na toto téma.|Téma,Typ chyby,Chyba|
|PublishSuccessLatencyInMs|Publikovat latenci úspěšnosti|Milisekund|Celkem|Publikování latence úspěšnosti v milisekundách|Žádný|
|MatchedEventCount|Odpovídající události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto předplatnému události|Téma,Název odběru události,Název_aplikace DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Události doručení se nezdařilo|Počet|Celkem|Celkový počet událostí se nepodařilo doručit do tohoto odběru událostí|Téma,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCountCount|Dodané události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma,Název odběru události,Název_aplikace DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doba zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma,Název odběru události,Název_aplikace DomainEventSubscriptionName|
|Vynechánípočtu událostí|Zrušené události|Počet|Celkem|Celkový počet vyřazených událostí odpovídajících tomuto předplatnému události|Téma,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Události s mrtvými písmeny|Počet|Celkem|Celkový počet nedoručených událostí odpovídajících tomuto předplatnému události|Téma,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/témata

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných v tomto tématu|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí, které se nepodařilo publikovat na toto téma.|Typ chyby,Chyba|
|UnmatchedEventCount|Bezkonkurenční události|Počet|Celkem|Celkový počet událostí, které neodpovídají žádnému odběru událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Publikovat latenci úspěšnosti|Milisekund|Celkem|Publikování latence úspěšnosti v milisekundách|Žádný|
|MatchedEventCount|Odpovídající události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto předplatnému události|Název odběru_události|
|DeliveryAttemptFailCount|Události doručení se nezdařilo|Počet|Celkem|Celkový počet událostí se nepodařilo doručit do tohoto odběru událostí|Chyba,ErrorType,EventSubscriptionName|
|DeliverySuccessCountCount|Dodané události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Název odběru_události|
|DestinationProcessingDurationInMs|Doba zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Název odběru_události|
|Vynechánípočtu událostí|Zrušené události|Počet|Celkem|Celkový počet vyřazených událostí odpovídajících tomuto předplatnému události|DropReason,Název odběru události|
|DeadLetteredCount|Události s mrtvými písmeny|Počet|Celkem|Celkový počet nedoručených událostí odpovídajících tomuto předplatnému události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných v tomto tématu|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí, které se nepodařilo publikovat na toto téma.|Typ chyby,Chyba|
|UnmatchedEventCount|Bezkonkurenční události|Počet|Celkem|Celkový počet událostí, které neodpovídají žádnému odběru událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Publikovat latenci úspěšnosti|Milisekund|Celkem|Publikování latence úspěšnosti v milisekundách|Žádný|
|MatchedEventCount|Odpovídající události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto předplatnému události|Název odběru_události|
|DeliveryAttemptFailCount|Události doručení se nezdařilo|Počet|Celkem|Celkový počet událostí se nepodařilo doručit do tohoto odběru událostí|Chyba,ErrorType,EventSubscriptionName|
|DeliverySuccessCountCount|Dodané události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Název odběru_události|
|DestinationProcessingDurationInMs|Doba zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Název odběru_události|
|Vynechánípočtu událostí|Zrušené události|Počet|Celkem|Celkový počet vyřazených událostí odpovídajících tomuto předplatnému události|DropReason,Název odběru události|
|DeadLetteredCount|Události s mrtvými písmeny|Počet|Celkem|Celkový počet nedoručených událostí odpovídajících tomuto předplatnému události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Odpovídající události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto předplatnému události|Žádný|
|DeliveryAttemptFailCount|Události doručení se nezdařilo|Počet|Celkem|Celkový počet událostí se nepodařilo doručit do tohoto odběru událostí|Chyba,Typ chyby|
|DeliverySuccessCountCount|Dodané události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádný|
|DestinationProcessingDurationInMs|Doba zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádný|
|Vynechánípočtu událostí|Zrušené události|Počet|Celkem|Celkový počet vyřazených událostí odpovídajících tomuto předplatnému události|Důvod přetažení|
|DeadLetteredCount|Události s mrtvými písmeny|Počet|Celkem|Celkový počet nedoručených událostí odpovídajících tomuto předplatnému události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných v tomto tématu|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí, které se nepodařilo publikovat na toto téma.|Typ chyby,Chyba|
|UnmatchedEventCount|Bezkonkurenční události|Počet|Celkem|Celkový počet událostí, které neodpovídají žádnému odběru událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Publikovat latenci úspěšnosti|Milisekund|Celkem|Publikování latence úspěšnosti v milisekundách|Žádný|




## <a name="microsofteventhubnamespaces"></a>Obory názvů Microsoft.EventHub/Namespaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úspěšné požadavky|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft.EventHub.|EntityName,Výsledek operace|
|Serverchyby|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft.EventHub.|EntityName,Výsledek operace|
|Uživatelské chyby|Chyby uživatele.|Počet|Celkem|Chyby uživatelů pro Microsoft.EventHub.|EntityName,Výsledek operace|
|Kvótaexceededchyb|Kvóta byla překročena chyby.|Počet|Celkem|Kvóta byla překročena chyby pro Microsoft.EventHub.|EntityName,Výsledek operace|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft.EventHub.|EntityName,Výsledek operace|
|Příchozí požadavky|Příchozí požadavky|Počet|Celkem|Příchozí požadavky pro Microsoft.EventHub.|Název entity|
|Příchozí zprávy|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft.EventHub.|Název entity|
|Odchozí zprávy|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft.EventHub.|Název entity|
|Příchozí bajty|Příchozí bajty.|Bajty|Celkem|Příchozí bajty pro Microsoft.EventHub.|Název entity|
|OutgoingBytes|Odchozí bajty.|Bajty|Celkem|Odchozí bajty pro Microsoft.EventHub.|Název entity|
|Aktivní připojení|Aktivní připojení|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft.EventHub.|Žádný|
|Otevřená připojení|Připojení byla otevřena.|Počet|Průměr|Otevřená připojení pro Microsoft.EventHub.|Název entity|
|Připojení uzavřeno|Připojení uzavřena.|Počet|Průměr|Připojení uzavřena pro Microsoft.EventHub.|Název entity|
|CaptureBacklog|Zachytit nevyřízené položky.|Počet|Celkem|Zachytit nevyřízené položky pro Microsoft.EventHub.|Název entity|
|Zachycené zprávy|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft.EventHub.|Název entity|
|Zachycené bajty|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft.EventHub.|Název entity|
|Velikost|Velikost|Bajty|Průměr|Velikost EventHub v bajtů.|Název entity|
|Inreqs|Příchozí požadavky (zastaralé)|Počet|Celkem|Celkový počet příchozích požadavků na odeslání oboru názvů (Zastaralé)|Žádný|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Počet|Celkem|Celkový počet úspěšných požadavků na obor názvů (zastaralé)|Žádný|
|FUNKCE FAILREQ|Neúspěšné požadavky (zastaralé)|Počet|Celkem|Celkový počet neúspěšných požadavků na obor názvů (zastaralé)|Žádný|
|SVRBSY|Chyby zaneprázdnění serveru (zastaralé)|Počet|Celkem|Celkový počet chyb serveru zaneprázdněnpro obor názvů (zastaralé)|Žádný|
|INTERR|Chyby interního serveru (zastaralé)|Počet|Celkem|Celkový počet chyb interního serveru pro obor názvů (zastaralé)|Žádný|
|Miscerr|Jiné chyby (zastaralé)|Počet|Celkem|Celkový počet neúspěšných požadavků na obor názvů (zastaralé)|Žádný|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho použijte metriku Příchozí zprávy (zastaralé)|Žádný|
|EHINMSGS|Příchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádný|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho použijte metriku Odchozí zprávy (zastaralé)|Žádný|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádný|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Místo toho použijte metriku Příchozí bajty (Zastaralé)|Žádný|
|EHINBYTES|Příchozí bajty (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádný|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů. Tato metrika je zastaralá. Místo toho použijte metriku odchozích bajtů (zastaralé)|Žádný|
|EHOUTBYTES|Odchozí bajty (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádný|
|EHABL|Archivovat nevyřízené zprávy (zastaralé)|Počet|Celkem|Archivní zprávy centra událostí v nevyřízených položkách pro obor názvů (zastaralé)|Žádný|
|EHAMSGS|Archivní zprávy (zastaralé)|Počet|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)|Žádný|
|EHAMBS|Archivovat propustnost zpráv (zastaralé)|Bajty|Celkem|Propustnost zpráv centra událostí v oboru názvů (zastaralé)|Žádný|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úspěšné požadavky|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft.EventHub.|Výsledek operace|
|Serverchyby|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft.EventHub.|Výsledek operace|
|Uživatelské chyby|Chyby uživatele.|Počet|Celkem|Chyby uživatelů pro Microsoft.EventHub.|Výsledek operace|
|Kvótaexceededchyb|Kvóta byla překročena chyby.|Počet|Celkem|Kvóta byla překročena chyby pro Microsoft.EventHub.|Výsledek operace|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft.EventHub.|Výsledek operace|
|Příchozí požadavky|Příchozí požadavky|Počet|Celkem|Příchozí požadavky pro Microsoft.EventHub.|Žádný|
|Příchozí zprávy|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft.EventHub.|Žádný|
|Odchozí zprávy|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft.EventHub.|Žádný|
|Příchozí bajty|Příchozí bajty.|Bajty|Celkem|Příchozí bajty pro Microsoft.EventHub.|Žádný|
|OutgoingBytes|Odchozí bajty.|Bajty|Celkem|Odchozí bajty pro Microsoft.EventHub.|Žádný|
|Aktivní připojení|Aktivní připojení|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft.EventHub.|Žádný|
|Otevřená připojení|Připojení byla otevřena.|Počet|Průměr|Otevřená připojení pro Microsoft.EventHub.|Žádný|
|Připojení uzavřeno|Připojení uzavřena.|Počet|Průměr|Připojení uzavřena pro Microsoft.EventHub.|Žádný|
|CaptureBacklog|Zachytit nevyřízené položky.|Počet|Celkem|Zachytit nevyřízené položky pro Microsoft.EventHub.|Žádný|
|Zachycené zprávy|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft.EventHub.|Žádný|
|Zachycené bajty|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft.EventHub.|Žádný|
|Procesor|Procesor|Procento|Maximum|Využití procesoru pro cluster centra událostí v procentech|Role|
|AvailableMemory|Paměť k dispozici|Procento|Maximum|Dostupná paměť pro cluster centra událostí jako procento celkové paměti.|Role|
|Velikost|Velikost EventHub v bajtů.|Bajty|Průměr|Velikost EventHub v bajtů.|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Požadavky na brány|Požadavky na brány|Počet|Celkem|Počet požadavků brány|HttpStatus|
|Požadavky na brány zařazené do kategorií|Požadavky na brány zařazené do kategorií|Počet|Celkem|Počet požadavků na brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Počet aktivních pracovníků|Počet|Maximum|Počet aktivních pracovníků|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Hodnota ObservedMetric|Zjištěná metrická hodnota|Počet|Průměr|Hodnota vypočítaná automatickým měřítkem při spuštění|Zdroj metriky spouštěčů|
|Metrická prahová hodnota|Prahová hodnota metriky|Počet|Průměr|Nakonfigurovaná prahová hodnota automatického škálování při spuštění automatického škálování.|Metrické pravidlo spouštění|
|Pozorovaná kapacita|Pozorovaná kapacita|Počet|Průměr|Kapacita hlášena automatické škálování při jeho spuštění.|Žádný|
|ScaleActionsInitiated|Zahájené akce škálování|Počet|Celkem|Směr operace měřítka.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Komponenty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityVýsledek/název,dostupnostVýsledek/umístění|
|availabilityResults/count|Testy dostupnosti|Počet|Počet|Počet testů dostupnosti|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|availabilityResults/duration|Doba trvání testu dostupnosti|Milisekund|Průměr|Doba trvání testu dostupnosti|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|browserTimings/networkDuration|Čas připojení sítě načítání stránky|Milisekund|Průměr|Doba mezi požadavkem uživatele a síťovým připojením. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádný|
|browserTimings/processingDuration|Doba zpracování klienta|Milisekund|Průměr|Doba mezi přijetím posledního bajtu dokumentu do načtení dom. Asynchronní požadavky mohou být stále zpracování.|Žádný|
|browserTimings/receiveDuration|Doba odezvy příjmu|Milisekund|Průměr|Čas mezi prvním a posledním bajtem nebo do odpojení.|Žádný|
|browserTimings/sendDuration|Čas odeslání požadavku|Milisekund|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádný|
|browserTimings/totalDuration|Čas načítání stránky prohlížeče|Milisekund|Průměr|Čas od požadavku uživatele až do DOM, styly, skripty a obrázky jsou načteny.|Žádný|
|závislosti/počet|Volání závislostí|Počet|Počet|Počet volání provedených aplikací do externích prostředků.|závislost/typ,závislost/performanceBucket,závislost/úspěch,závislost/cíl,závislost/resultCode,operace/syntetické,cloud/roleInstance,cloud/roleName|
|závislosti/doba trvání|Doba trvání závislosti|Milisekund|Průměr|Doba trvání volání aplikace na externí prostředky.|závislost/typ,závislost/performanceBucket,závislost/úspěch,závislost/cíl,závislost/resultCode,operace/syntetické,cloud/roleInstance,cloud/roleName|
|závislosti/selhání|Selhání volání závislostí|Počet|Počet|Počet neúspěšných volání závislostí provedených aplikací na externí prostředky.|závislost/typ,závislost/performanceBucket,závislost/úspěch,závislost/cíl,závislost/resultCode,operace/syntetické,cloud/roleInstance,cloud/roleName|
|zobrazení/počet stránek|Zobrazení stránky|Počet|Počet|Počet zobrazení stránky.|operace/syntetické,cloud/roleName|
|pageViews/duration pageViews/duration pageViews/duration pageViews|Doba načítání zobrazení stránky|Milisekund|Průměr|Doba načítání zobrazení stránky|operace/syntetické,cloud/roleName|
|performanceCounters/requestExecutionTime|Doba spuštění požadavku HTTP|Milisekund|Průměr|Doba spuštění poslední ho požadavku.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Požadavky HTTP ve frontě aplikací|Počet|Průměr|Délka fronty žádostí o aplikaci.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Rychlost požadavků HTTP|CountPerSecond|Průměr|Rychlost všech požadavků na aplikaci za sekundu od ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Míra výjimek|CountPerSecond|Průměr|Počet zpracovaných a neošetřených výjimek hlášených do systému Windows, včetně výjimek .NET a nespravovaných výjimek, které jsou převedeny na výjimky rozhraní .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Rychlost vi.|BajtyPerSekunda|Průměr|Celkový počet bajtů za sekundu čtení a zápis do souborů, sítě a zařízení.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Procesní procesor|Procento|Průměr|Procento uplynulého času, který všechna vlákna procesu používala procesor k provádění pokynů. To se může pohybovat mezi 0 až 100. Tato metrika označuje výkon samotného procesu w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Čas procesoru|Procento|Průměr|Procento času, který procesor stráví v nečinné vlákna.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Dostupná paměť|Bajty|Průměr|Fyzická paměť okamžitě k dispozici pro přidělení procesu nebo pro použití v systému.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Zpracovat soukromé bajty|Bajty|Průměr|Paměť je přiřazena výhradně procesům monitorované aplikace.|cloud/roleInstance|
|žádosti/doba trvání|Doba odezvy serveru|Milisekund|Průměr|Doba mezi přijetím požadavku HTTP a dokončením odeslání odpovědi.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|požadavky/počet|Serverové požadavky|Počet|Počet|Počet dokončených požadavků HTTP.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|požadavky/neúspěšné|Neúspěšné požadavky|Počet|Počet|Počet požadavků HTTP označených jako neúspěšné. Ve většině případů se jedná o požadavky s kódem odpovědi >= 400 a nerovná se 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|žádosti/sazba|Rychlost požadavků serveru|CountPerSecond|Průměr|Rychlost požadavků serveru za sekundu|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|výjimky/počet|Výjimky|Počet|Počet|Kombinovaný počet všech nezachycených výjimek.|cloud/roleName,cloud/roleInstance,klient/typ|
|výjimky/prohlížeč|Výjimky prohlížečů|Počet|Počet|Počet nezachycených výjimek vyváděných v prohlížeči.|client/isServer,cloud/roleName|
|výjimky/server|Výjimky serveru|Počet|Počet|Počet nezachycených výjimek vyzvaných v serverové aplikaci.|client/isServer,cloud/roleName,cloud/roleInstance|
|stopy/počet|Trasování|Počet|Počet|Počet trasovacích dokumentů|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|připojenýPočet zařízení|Celkový počet připojených zařízení|Počet|Průměr|Počet zařízení připojených k IoT Central|Žádný|
|c2d.property.read.success|Úspěšné čtení vlastností zařízení ze začátku ioT central|Počet|Celkem|Počet všech úspěšných čtení vlastností iniciovaných z IoT Central|Žádný|
|c2d.property.read.selhání|Čtení vlastností zařízení se nezdařilo z ioT central|Počet|Celkem|Počet všech čtení vlastností, která selhala, iniciovaná ze systému IoT Central|Žádný|
|d2c.property.read.success|Úspěšné čtení vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných čtení vlastností iniciovaných ze zařízení|Žádný|
|d2c.property.read.selhání|Čtení vlastností zařízení se nezdařilo ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení vlastností iniciovaných ze zařízení|Žádný|
|c2d.property.update.success|Úspěšné aktualizace vlastností zařízení z IoT Central|Počet|Celkem|Počet všech úspěšných aktualizací vlastností iniciovaných ze služby IoT Central|Žádný|
|c2d.property.update.selhání|Neúspěšné aktualizace vlastností zařízení ze služby IoT Central|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností iniciovaných ze služby IoT Central|Žádný|
|d2c.property.update.success|Úspěšné aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací vlastností iniciovaných ze zařízení|Žádný|
|d2c.property.update.selhání|Neúspěšné aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností iniciovaných ze zařízení|Žádný|


## <a name="microsoftkeyvaultvaults"></a>Úložiště Microsoft.KeyVault/trezory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkový počet přístupů rozhraní API služby|Počet|Počet|Počet celkových přístupů rozhraní API služby|ActivityType,ActivityName|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků rozhraní API služby|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult|Celkový počet výsledků rozhraní API služby|Počet|Počet|Počet celkových výsledků rozhraní API služby|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|SytostKrabice na boty|Celková sytost trezoru|Procento|Průměr|Využitá kapacita trezoru|ActivityType,ActivityName,TransactionType|
|Dostupnost|Celková dostupnost trezoru|Procento|Průměr|Dostupnost požadavků na úložiště|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití mezipaměti|Využití mezipaměti|Procento|Průměr|Úroveň využití v oboru clusteru|Žádný|
|Trvání dotazu|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazů v sekundách|Stav dotazu|
|IngestionUtilizace|Využití požití|Procento|Průměr|Poměr použitých ingestačních slotů v clusteru|Žádný|
|Keepalive|Udržujte naživu|Počet|Průměr|Kontrola příčetnosti označuje, že cluster odpovídá na dotazy|Žádný|
|PožitíVolumeInMB|Svazek požití (v MB)|Počet|Celkem|Celkový objem ingemovaných dat do clusteru (v MB)|databáze|
|IngestionLatencyInSeconds|Latence požití (v sekundách)|Sekundy|Průměr|Doba požití ze zdroje (např. zpráva je v EventHubu) do clusteru v sekundách|Žádný|
|UdálostiProcessedForEventHubs|Zpracované události (pro centra událostí/IoT)|Počet|Celkem|Počet událostí zpracovaných clusterem při ingestování z centra Event/IoT Hub|EventStatus|
|Výsledek požití|Výsledek požití|Počet|Počet|Počet operací požití|IngestionResultDetails|
|Procesor|Procesor|Procento|Průměr|Úroveň využití procesoru|Žádný|
|ContinuousExportNumOfRecordsExported|Nepřetržitý export – číslo exportovaných záznamů|Počet|Celkem|Počet exportovaných záznamů, vytápěných pro každý artefakt úložiště napsaný během operace exportu|ContinuousExportName,Databáze|
|Využití exportu|Využití exportu|Procento|Maximum|Využití exportu|Žádný|
|ContinuousExportPendingCount|Počet čekajícího nepřetržitého exportu|Počet|Maximum|Počet čekajících úloh průběžného exportu připravených k provedení|Žádný|
|ContinuousExportMaxLatenessMinutes|Maximální zpoždění nepřetržitého exportu|Počet|Maximum|Zpoždění (v minutách) hlášené nepřetržitými exportními úlohami v clusteru|Žádný|
|Výsledek průběžného exportu|Výsledek nepřetržitého exportu|Počet|Počet|Označuje, zda byl nepřetržitý export úspěšný nebo neúspěšný.|ContinuousExportName,Výsledek,Databáze|
|Streamovánítrvání|Doba trvání ingestování streamování|Milisekund|Průměr|Doba streamování v milisekundách|Žádný|
|StreamingIngestData|Rychlost streamování ingestování dat|Počet|Průměr|Rychlost streamování dat ingestuce (MB za sekundu)|Žádný|
|SteamingestRequestRate|Rychlost požadavků ingestování datových proudů|Počet|RateRequestsPerSecond|Rychlost streamování požadavků na ingestování (požadavky za sekundu)|Žádný|
|StreamingIngestResults|Výsledek ingestování streamování|Počet|Průměr|Výsledek streamování|Výsledek|
|TotalNumberOfConcurrentQueries|Celkový počet souběžných dotazů|Počet|Celkem|Celkový počet souběžných dotazů|Žádný|
|TotalNumberOfThrottledQueries|Celkový počet dotazů s omezením|Počet|Celkem|Celkový počet dotazů s omezením|Žádný|
|TotalNumberOfThrottledCommands|Celkový počet příkazů s omezením|Počet|Celkem|Celkový počet příkazů s omezením|Commandtype|
|Celkový počet rozsahů|Celkový počet rozsahů|Počet|Celkem|Celkový počet datových rozsahů|Žádný|
|Počet instancí|Počet instancí|Počet|Průměr|Celkový počet instancí|Žádný|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/pracovní postupy

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Spuštění|Spuštění bylo zahájeno.|Počet|Celkem|Počet spuštění pracovního postupu.|Žádný|
|Spuštění Dokončeno|Spuštění dokončeno|Počet|Celkem|Počet dokončených spuštění pracovního postupu.|Žádný|
|Spuštění proběhlo úspěšně.|Spuštění proběhlo úspěšně.|Počet|Celkem|Počet spuštění pracovního postupu byl úspěšný.|Žádný|
|Spuštění se nezdařilo.|Spuštění se nezdařilo.|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádný|
|Spuštěnízrušeno|Spuštění byla zrušena.|Počet|Celkem|Počet spuštění pracovního postupu byl zrušen.|Žádný|
|RunLatency|Spustit latenci|Sekundy|Průměr|Latence dokončeného pracovního postupu je spuštěna.|Žádný|
|RunSuccessLatency|Spustit latenci úspěchu|Sekundy|Průměr|Latence úspěšného pracovního postupu běží.|Žádný|
|RunThrottledEvents|Spustit události s omezením|Počet|Celkem|Počet akcí pracovního postupu nebo aktivační události omezení.|Žádný|
|Akce RunStartŠkrtdEvents|Spustit události s omezením spuštění|Počet|Celkem|Počet spuštění spuštění pracovního postupu události omezení.|Žádný|
|Procento selhání systému Run|Procento selhání spuštění|Procento|Celkem|Procento spuštění pracovního postupu se nezdařilo.|Žádný|
|AkceZahájené|Zahájené akce |Počet|Celkem|Počet zahájených akcí pracovního postupu.|Žádný|
|Dokončené akce|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádný|
|Akce proběhly úspěšně.|Akce proběhly úspěšně. |Počet|Celkem|Počet akcí pracovního postupu byl úspěšný.|Žádný|
|Akce se nezdařily.|Akce se nezdařily. |Počet|Celkem|Počet akcí pracovního postupu se nezdařil.|Žádný|
|AkceSkipped|Akce přeskočeny |Počet|Celkem|Počet akcí pracovního postupu přeskočen.|Žádný|
|Latence akce|Latence akce |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádný|
|AkceSuccessLatency|Latence úspěšnosti akce |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádný|
|Akce Akce|Akce škrticí události|Počet|Celkem|Počet událostí akce pracovního postupu omezení..|Žádný|
|TriggersStarted|Aktivační události byly spuštěny. |Počet|Celkem|Počet spuštěných aktivačních událostí pracovního postupu|Žádný|
|Aktivační události dokončeny|Aktivační události dokončeny |Počet|Celkem|Počet aktivačních událostí pracovního postupu byl dokončen.|Žádný|
|Aktivační události proběhly úspěšně.|Aktivační události byly úspěšné. |Počet|Celkem|Počet aktivačních událostí pracovního postupu byl úspěšný.|Žádný|
|Aktivační události se nezdařily|Aktivační události se nezdařily. |Počet|Celkem|Počet aktivačních událostí pracovního postupu se nezdařil.|Žádný|
|TriggersSkipped|Přeskočené aktivační události|Počet|Celkem|Počet aktivačních událostí pracovního postupu přeskočen.|Žádný|
|TriggersFired|Aktivační události aktivována |Počet|Celkem|Počet aktivačních událostí pracovního postupu.|Žádný|
|Latence spouště|Latence aktivační události |Sekundy|Průměr|Latence dokončených aktivačních událostí pracovního postupu.|Žádný|
|TriggerFireLatency|Aktivovat latenci požáru |Sekundy|Průměr|Latence aktivačních událostí vytápěného pracovního postupu.|Žádný|
|TriggerSuccessLatency|Aktivace latence úspěšnosti |Sekundy|Průměr|Latence úspěšných aktivačních událostí pracovního postupu.|Žádný|
|TriggerThrottledEvents|Události s omezením aktivace|Počet|Celkem|Počet událostí omezení omezení pracovního postupu.|Žádný|
|BillableActionExecutions|Fakturovatelné spuštění akcí|Počet|Celkem|Počet spuštění akce pracovního postupu, které se účtují.|Žádný|
|BillableTriggerExecutions|Fakturovatelné spuštění aktivační události|Počet|Celkem|Počet spuštění aktivačníudálosti pracovního postupu, která se účtují.|Žádný|
|TotalBillableExecutions|Fakturovatelná spuštění celkem|Počet|Celkem|Počet spuštění pracovního postupu, které se účtují.|Žádný|
|BillingUsageNativeOperation|Využití fakturace pro nativní spuštění operací|Počet|Celkem|Počet nativních spuštění operací, které se účtují.|Žádný|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardního konektoru|Počet|Celkem|Počet spuštění standardníkonektor, které se účtují.|Žádný|
|BillingUsageStorageConsumptionS|Využití fakturace pro spouštění spotřeby úložiště|Počet|Celkem|Počet spuštění spotřeby úložiště, které se účtují.|Žádný|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Spuštění|Spuštění bylo zahájeno.|Počet|Celkem|Počet spuštění pracovního postupu.|Žádný|
|Spuštění Dokončeno|Spuštění dokončeno|Počet|Celkem|Počet dokončených spuštění pracovního postupu.|Žádný|
|Spuštění proběhlo úspěšně.|Spuštění proběhlo úspěšně.|Počet|Celkem|Počet spuštění pracovního postupu byl úspěšný.|Žádný|
|Spuštění se nezdařilo.|Spuštění se nezdařilo.|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádný|
|Spuštěnízrušeno|Spuštění byla zrušena.|Počet|Celkem|Počet spuštění pracovního postupu byl zrušen.|Žádný|
|RunLatency|Spustit latenci|Sekundy|Průměr|Latence dokončeného pracovního postupu je spuštěna.|Žádný|
|RunSuccessLatency|Spustit latenci úspěchu|Sekundy|Průměr|Latence úspěšného pracovního postupu běží.|Žádný|
|RunThrottledEvents|Spustit události s omezením|Počet|Celkem|Počet akcí pracovního postupu nebo aktivační události omezení.|Žádný|
|Akce RunStartŠkrtdEvents|Spustit události s omezením spuštění|Počet|Celkem|Počet spuštění spuštění pracovního postupu události omezení.|Žádný|
|Procento selhání systému Run|Procento selhání spuštění|Procento|Celkem|Procento spuštění pracovního postupu se nezdařilo.|Žádný|
|AkceZahájené|Zahájené akce |Počet|Celkem|Počet zahájených akcí pracovního postupu.|Žádný|
|Dokončené akce|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádný|
|Akce proběhly úspěšně.|Akce proběhly úspěšně. |Počet|Celkem|Počet akcí pracovního postupu byl úspěšný.|Žádný|
|Akce se nezdařily.|Akce se nezdařily. |Počet|Celkem|Počet akcí pracovního postupu se nezdařil.|Žádný|
|AkceSkipped|Akce přeskočeny |Počet|Celkem|Počet akcí pracovního postupu přeskočen.|Žádný|
|Latence akce|Latence akce |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádný|
|AkceSuccessLatency|Latence úspěšnosti akce |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádný|
|Akce Akce|Akce škrticí události|Počet|Celkem|Počet událostí akce pracovního postupu omezení..|Žádný|
|TriggersStarted|Aktivační události byly spuštěny. |Počet|Celkem|Počet spuštěných aktivačních událostí pracovního postupu|Žádný|
|Aktivační události dokončeny|Aktivační události dokončeny |Počet|Celkem|Počet aktivačních událostí pracovního postupu byl dokončen.|Žádný|
|Aktivační události proběhly úspěšně.|Aktivační události byly úspěšné. |Počet|Celkem|Počet aktivačních událostí pracovního postupu byl úspěšný.|Žádný|
|Aktivační události se nezdařily|Aktivační události se nezdařily. |Počet|Celkem|Počet aktivačních událostí pracovního postupu se nezdařil.|Žádný|
|TriggersSkipped|Přeskočené aktivační události|Počet|Celkem|Počet aktivačních událostí pracovního postupu přeskočen.|Žádný|
|TriggersFired|Aktivační události aktivována |Počet|Celkem|Počet aktivačních událostí pracovního postupu.|Žádný|
|Latence spouště|Latence aktivační události |Sekundy|Průměr|Latence dokončených aktivačních událostí pracovního postupu.|Žádný|
|TriggerFireLatency|Aktivovat latenci požáru |Sekundy|Průměr|Latence aktivačních událostí vytápěného pracovního postupu.|Žádný|
|TriggerSuccessLatency|Aktivace latence úspěšnosti |Sekundy|Průměr|Latence úspěšných aktivačních událostí pracovního postupu.|Žádný|
|TriggerThrottledEvents|Události s omezením aktivace|Počet|Celkem|Počet událostí omezení omezení pracovního postupu.|Žádný|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integračních služeb|Procento|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti pracovního postupu pro prostředí integrační ch služeb|Procento|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí služby integrace|Procento|Průměr|Využití procesoru konektoru pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí služby Integrace|Procento|Průměr|Využití paměti konektoru pro prostředí služby integrace.|Žádný|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Zrušené spuštění|Zrušené spuštění|Počet|Celkem|Počet spuštění zrušených pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Zrušit požadované spuštění|Zrušit požadované spuštění|Počet|Celkem|Počet spuštění, u kterých bylo požadováno zrušení pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Dokončené spuštění|Dokončené spuštění|Počet|Celkem|Počet úspěšně dokončených spuštění pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Selhání spuštění|Selhání spuštění|Počet|Celkem|Počet spuštění se nezdařil o tento pracovní prostor.|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Dokončení spuštění|Dokončení spuštění|Počet|Celkem|Počet spuštění zadaných stav dokončení pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Neodpovídá spuštění|Neodpovídá spuštění|Počet|Celkem|Počet spuštění, která neodpovídají pro tento pracovní prostor.|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Nespuštěno|Nespuštěno|Počet|Celkem|Počet spuštění v nespuštěném stavu pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Příprava spuštění|Příprava spuštění|Počet|Celkem|Počet spuštění, která se připravují na tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Spuštění zřizování|Spuštění zřizování|Počet|Celkem|Počet spuštění, které zřčují pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Spuštění ve frontě|Spuštění ve frontě|Počet|Celkem|Počet spuštění, která jsou zařazena do fronty pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Spuštěno spuštění|Spuštěno spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Spuštění spuštění|Spuštění spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|chyby|chyby|Počet|Celkem|Počet chyb spuštění v tomto pracovním prostoru|Scénář|
|Upozornění|Upozornění|Počet|Celkem|Počet upozornění spuštění v tomto pracovním prostoru|Scénář|
|Registr modelů byl úspěšný.|Registr modelů byl úspěšný.|Počet|Celkem|Počet registrací modelu, které byly úspěšné v tomto pracovním prostoru|Scénář|
|Registr modelů se nezdařil.|Registr modelů se nezdařil.|Počet|Celkem|Počet registrací modelu, které se v tomto pracovním prostoru nezdařily|Scénář,StatusCode|
|Zahájení nasazení modelu|Zahájení nasazení modelu|Počet|Celkem|Počet nasazení modelu spuštěných v tomto pracovním prostoru|Scénář|
|Nasazení modelu bylo úspěšné.|Nasazení modelu bylo úspěšné.|Počet|Celkem|Počet nasazení modelu, která byla úspěšná v tomto pracovním prostoru|Scénář|
|Nasazení modelu se nezdařilo.|Nasazení modelu se nezdařilo.|Počet|Celkem|Počet nasazení modelu, která se v tomto pracovním prostoru nezdařila|Scénář,StatusCode|
|Uzly celkem|Uzly celkem|Počet|Průměr|Počet uzlů součtu. Tento součet zahrnuje některé aktivní uzly, nečinné uzly, nepoužitelné uzly, předpomívené uzly, opuštění uzlů|Scénář,Název_clusteru|
|Aktivní uzly|Aktivní uzly|Počet|Průměr|Počet acitve uzlů. Jedná se o uzly, které jsou aktivně spuštěny úlohy.|Scénář,Název_clusteru|
|Nečinné uzly|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů. Nečinné uzly jsou uzly, které nejsou spuštěny žádné úlohy, ale může přijmout novou úlohu, pokud je k dispozici.|Scénář,Název_clusteru|
|Nepoužitelné uzly|Nepoužitelné uzly|Počet|Průměr|Počet nepoužitelných uzlů. Nepoužitelné uzly nejsou funkční z důvodu některých neřešitelných problémů. Azure tyto uzly recykluje.|Scénář,Název_clusteru|
|Předpisované uzly|Předpisované uzly|Počet|Průměr|Počet předplyňovaných uzlů. Tyto uzly jsou uzly s nízkou prioritou, které jsou odebrány z fondu k dispozici uzel.|Scénář,Název_clusteru|
|Opuštění uzlů|Opuštění uzlů|Počet|Průměr|Počet opouštějících uzlů. Opuštění uzly jsou uzly, které právě dokončili zpracování úlohy a přejde do stavu Nečinnosti.|Scénář,Název_clusteru|
|Jádra celkem|Jádra celkem|Počet|Průměr|Počet celkových jader|Scénář,Název_clusteru|
|Aktivní jádra|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář,Název_clusteru|
|Nečinná jádra|Nečinná jádra|Počet|Průměr|Počet nečinných jader|Scénář,Název_clusteru|
|Nepoužitelná jádra|Nepoužitelná jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář,Název_clusteru|
|Předpjatojádra|Předpjatojádra|Počet|Průměr|Počet preemptovaných jader|Scénář,Název_clusteru|
|Opuštění jader|Opuštění jader|Počet|Průměr|Počet odnechávacích jader|Scénář,Název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář,Název_clusteru,Název_vmFamily,Priorita VmPriority|
|Využití procesoru|Využití procesoru|Počet|Průměr|PROCESOR (náhled)|Scénář,runId,NodeId,CreatedTime|
|GpuUtilization|GpuUtilization|Počet|Průměr|GPU (náhled)|Scénář,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Počet volání rozhraní API|ApiCategory,ApiName,ResultType,ResponseCode|
|Dostupnost|Dostupnost|Procento|Průměr|Dostupnost api|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingKoncové body

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech.|Výstupní formát|
|SuccessE2ELatency|Úspěšnost latence od konce ke konci|Milisekund|Průměr|Průměrná latence pro úspěšné požadavky v milisekundách.|Výstupní formát|
|Žádosti|Žádosti|Počet|Celkem|Požadavky na koncový bod streamování.|OutputFormat,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetQuota|Kvóta aktiv|Počet|Průměr|Kolik datových zdrojů je povoleno pro běžný účet mediálních služeb|Žádný|
|Počet majetku|Počet majetku|Počet|Průměr|Kolik datových zdrojů je již vytvořeno na běžném účtu mediální chslužeb|Žádný|
|Procento použitého assetaused|Procento použité kvóty aktiv|Procento|Průměr|Procento použitého majetku v aktuálním účtu mediální služby|Žádný|
|ContentKeyPolicyQuota|Kvóta zásad klíče obsahu|Počet|Průměr|Kolik klíčových textových kláves obsahu je povoleno pro běžný účet mediálních služeb|Žádný|
|ContentKeyPolicyCount|Počet zásad klíče obsahu|Počet|Průměr|Kolik zásad klíče obsahu je již vytvořeno v aktuálním účtu mediální služby|Žádný|
|Procento contentkeypolicyquotausedprocento|Procento použité kvóty zásad obsahu|Procento|Průměr|Zásady klíče obsahu použily procento v aktuálním účtu mediální služby|Žádný|
|StreamingPolicyQuota|Kvóta zásad streamování|Počet|Průměr|Kolik zásad streamování je povoleno pro aktuální účet mediální služby|Žádný|
|StreamingPolicyCount|Počet zásad streamování|Počet|Průměr|Kolik zásad streamování je již vytvořeno v aktuálním účtu mediální služby|Žádný|
|StreamingPolicyQuotaUsedPercentage|Procento použité kvóty zásad streamování|Procento|Průměr|Zásady streamování použité procento v aktuálním účtu mediální chod|Žádný|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Převedené datové zdroje|Převedený majetek|Počet|Celkem|Celkový počet převedených aktiv|Id aplikace,ResourceId,SDKVersion|
|ActiveRenderingSessions|Aktivní relace vykreslování|Počet|Celkem|Celkový počet aktivních relací vykreslování|Id aplikace,ResourceId,Typ relace,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Průměrná latence|Průměrná latence čtení|Milisekund|Průměr|Průměrná latence čtení v milisekundách na operaci|Žádný|
|AverageWriteLatency|Průměrná latence zápisu|Milisekund|Průměr|Průměrná latence zápisu v milisekundách na operaci|Žádný|
|VolumeLogicalSize|Velikost spotřebovaného objemu|Bajty|Průměr|Logická velikost svazku (použité bajty)|Žádný|
|VolumeSnapshotSize|Velikost snímku svazku|Bajty|Průměr|Velikost všech snímků ve svazku|Žádný|
|ReadIops|Přečtěte si iops|CountPerSecond|Průměr|Čtení operací In/out za sekundu|Žádný|
|WriteIops|Napište iops|CountPerSecond|Průměr|Zapsat operace in/out za sekundu|Žádný|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Fond přidělený velikosti svazku|Bajty|Průměr|Přidělená použitá velikost fondu|Žádný|
|VolumePoolTotalLogicalSize|Velikost spotřebovaného fondu|Bajty|Průměr|Součet logické velikosti všech svazků patřících do fondu|Žádný|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Bajty|Celkem|Počet odeslaných bajtů síťového rozhraní|Žádný|
|Míra obdržené hod.|Přijaté bajty|Bajty|Celkem|Počet přijatých bajtů síťového rozhraní|Žádný|
|PacketsSentRate|Odeslané pakety|Počet|Celkem|Počet odeslaných paketů síťového rozhraní|Žádný|
|Míra paketů ReceivedRate|Přijaté pakety|Počet|Celkem|Počet přijatých paketů síťového rozhraní|Žádný|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipDostupnost|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost cesty k datům vykladače zatížení za dobu trvání|FrontendIPAddress,FrontendPort|
|DipAvailability|Stav sondy stavu|Počet|Průměr|Průměrný stav sondy pro vyrovnávání zatížení za dobu trvání|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|Bytecount|Počet bajtů|Počet|Celkem|Celkový počet bajtů přenesených v časovém období|FrontendIPAddress,FrontendPort,Směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet paketů odeslaných v časovém období|FrontendIPAddress,FrontendPort,Směr|
|POČET SYNCount|Počet SYN|Počet|Celkem|Celkový počet paketů SYN odeslaných v časovém období|FrontendIPAddress,FrontendPort,Směr|
|SnatConnectionCount|Počet připojení SNAT|Počet|Celkem|Celkový počet nových připojení SNAT vytvořených v časovém období|FrontendIPAddress,BackendIPAddress,ConnectionState|
|Přidělenésnatporty|Přidělené porty SNAT (náhled)|Počet|Celkem|Celkový počet portů SNAT přidělených v časovém období|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|PoužitéPorty|Použité porty SNAT (náhled)|Počet|Celkem|Celkový počet portů SNAT použitých v časovém období|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Počet|Celkem|Počet dotazů obsluhovaných pro zónu DNS|Žádný|
|RecordSetCount|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně DNS|Žádný|
|Využití kapacity recordSetCapacity|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádný|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAdresy

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|Vstupní pakety DDoS|CountPerSecond|Maximum|Vstupní pakety DDoS|Žádný|
|PaketyDroppedDDoS|Příchozí pakety vynechány DDoS|CountPerSecond|Maximum|Příchozí pakety vynechány DDoS|Žádný|
|PacketsForwardedDDoS|Příchozí pakety předané DDoS|CountPerSecond|Maximum|Příchozí pakety předané DDoS|Žádný|
|TCPPacketsInDDoS|Příchozí pakety TCP DDoS|CountPerSecond|Maximum|Příchozí pakety TCP DDoS|Žádný|
|TCPPacketsDroppedDDoS|Příchozí pakety TCP vynechanou DDoS|CountPerSecond|Maximum|Příchozí pakety TCP vynechanou DDoS|Žádný|
|TCPPacketsForwardedDDoS|Příchozí pakety TCP předané DDoS|CountPerSecond|Maximum|Příchozí pakety TCP předané DDoS|Žádný|
|UDPPacketsInDDoS|Příchozí UDP pakety DDoS|CountPerSecond|Maximum|Příchozí UDP pakety DDoS|Žádný|
|UDPPacketsDroppedDDoS|Příchozí pakety UDP vynechanou DDoS|CountPerSecond|Maximum|Příchozí pakety UDP vynechanou DDoS|Žádný|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předané DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předané DDoS|Žádný|
|BajtůInDDoS|DDoS příchozích bajtů|BajtyPerSekunda|Maximum|DDoS příchozích bajtů|Žádný|
|BajtůKleslDDoS|Příchozí bajty vynechány DDoS|BajtyPerSekunda|Maximum|Příchozí bajty vynechány DDoS|Žádný|
|BajtůForwardedDDoS|Příchozí bajty předaných DDoS|BajtyPerSekunda|Maximum|Příchozí bajty předaných DDoS|Žádný|
|TCPBytesInDDoS|Příchozí tcp bajty DDoS|BajtyPerSekunda|Maximum|Příchozí tcp bajty DDoS|Žádný|
|TCPBytesDroppedDDoS|Příchozí bajty TCP vynechány DDoS|BajtyPerSekunda|Maximum|Příchozí bajty TCP vynechány DDoS|Žádný|
|TCPBytesForwardedDDoS|Příchozí tcp bajty předané DDoS|BajtyPerSekunda|Maximum|Příchozí tcp bajty předané DDoS|Žádný|
|UDPBytesInDDoS|Příchozí UDP bajty DDoS|BajtyPerSekunda|Maximum|Příchozí UDP bajty DDoS|Žádný|
|UDPBytesDroppedDDoS|Příchozí udp bajty vynechány DDoS|BajtyPerSekunda|Maximum|Příchozí udp bajty vynechány DDoS|Žádný|
|UDPBytesForwardedDDoS|Příchozí udp předané ddos|BajtyPerSekunda|Maximum|Příchozí udp předané ddos|Žádný|
|Ifunderddosattack|Pod DDoS útokem nebo ne|Počet|Maximum|Pod DDoS útokem nebo ne|Žádný|
|Pakety DDoSTriggerTCP|Příchozí pakety TCP pro aktivaci zmírnění ddos|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci zmírnění ddos|Žádný|
|Pakety DDoSTriggerUDP|Příchozí pakety UDP pro aktivaci zmírnění ddos|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci zmírnění ddos|Žádný|
|Pakety DDoSTriggerSYN|Příchozí pakety SYN pro aktivaci zmírnění ddos|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci zmírnění ddos|Žádný|
|VipDostupnost|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost IP adresy za dobu trvání|Port|
|Bytecount|Počet bajtů|Počet|Celkem|Celkový počet bajtů přenesených v časovém období|Port,Směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet paketů odeslaných v časovém období|Port,Směr|
|Počet synů|Počet SYN|Počet|Celkem|Celkový počet paketů SYN odeslaných v časovém období|Port,Směr|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Doba odezvy pro příkazy Ping k virtuálnímu virtuálnímu virtuálnímu virtuálnímu okamžiku|Milisekund|Průměr|Doba odezvy pro příkazy Ping odeslané do cílového virtuálního montovny|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Neúspěšný příkaz ping pro virtuální hospodařící s virtuálním mem|Procento|Průměr|Procento počtu neúspěšných pingů k celkovému počtu odeslaných příkazů Ping cílového virtuálního účtu|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AplikaceRuleHit|Počet přístupů pravidel aplikace|Počet|Celkem|Počet přístupů k pravidlům aplikace|Stav,Důvod,Protokol|
|NetworkRuleHit|Počet přístupů síťových pravidel|Počet|Celkem|Počet přístupů k pravidlům sítě|Stav,Důvod,Protokol|
|Stav brány firewall|Stav brány firewall|Procento|Průměr|Stav brány firewall|Stav,Důvod|
|Data zpracována|Zpracovávaná data|Bajty|Celkem|Celkové množství dat zpracovávaných bránou firewall|Žádný|
|SNATPortUtilizace|Využití portu SNAT|Procento|Průměr|Využití portu SNAT|Žádný|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BajtyPerSekunda|Průměr|Počet bajtů za sekundu, které aplikační brána obsluhovala|Žádný|
|Nezdravýpočet hostitelů|Počet hostitelů není v pořádku|Počet|Průměr|Počet hostitelů back-endu není v pořádku|Fond backendsettingspool|
|HealthyHostCount|Počet hostitelů v pořádku|Počet|Průměr|Počet hostitelů back-endu v pořádku|Fond backendsettingspool|
|TotalRequests|Požadavky celkem|Počet|Celkem|Počet úspěšných požadavků, kterým aplikační brána sloužila|Fond backendsettingspool|
|AvgRequestCountPerHealthyHost|Požadavky za minutu na zdravého hostitele|Počet|Průměr|Průměrný počet požadavků za minutu na hostitele back-endu v pořádku ve fondu|Fond backendsettingspool|
|Požadavky na selhání|Neúspěšné požadavky|Počet|Celkem|Počet neúspěšných požadavků, které aplikace Gateway obsluhovala|Fond backendsettingspool|
|Stav odpovědi|Stav odpovědi|Počet|Celkem|Stav odpovědi http vrácený aplikací brány|Skupina HttpStatus|
|CurrentConnections|Aktuální připojení|Počet|Celkem|Počet aktuálních připojení navazujících pomocí aplikační brány|Žádný|
|NewConnectionsPerSecond|Nová připojení za sekundu|CountPerSecond|Průměr|Nová připojení za sekundu vytvořená pomocí aplikační brány|Žádný|
|Využití procesoru|Využití procesoru|Procento|Průměr|Aktuální využití procesoru aplikační brány|Žádný|
|Kapacitní jednotky|Jednotky aktuální kapacity|Počet|Průměr|Spotřebované jednotky kapacity|Žádný|
|Pevné fakturovatelné kapacitní jednotky|Jednotky s pevnou fakturovatelnou kapacitou|Počet|Průměr|Jednotky s minimální kapacitou, které budou účtovány|Žádný|
|Odhadované fakturované kapacitní jednotky|Jednotky odhadované fakturované kapacity|Počet|Průměr|Jednotky odhadované kapacity, které budou účtovány|Žádný|
|Výpočetní jednotky|Aktuální výpočetní jednotky|Počet|Průměr|Spotřebované výpočetní jednotky|Žádný|
|Stav backendové odpovědi|Stav back-endové odpovědi|Počet|Celkem|Počet kódů odpovědi HTTP generovaných členy back-endu. To nezahrnuje žádné kódy odpovědí generované aplikační bránou.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|Protokol TL|Protokol TLS klienta|Počet|Celkem|Počet požadavků TLS a non-TLS iniciovaných klientem, který navázal spojení s aplikační bránou. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS dimenze.|Naslouchací proces,Protokol TlsProtocol|
|BajtůOdeslání|Odeslané bajty|Bajty|Celkem|Celkový počet bajtů odeslaných aplikační bránou klientům|Naslouchací proces|
|Přijaté bajty|Přijaté bajty|Bajty|Celkem|Celkový počet bajtů přijatých aplikační bránou od klientů|Naslouchací proces|
|KlientRtt|RTT klienta|Milisekund|Průměr|Průměrná doba odezvy mezi klienty a aplikační bránou. Tato metrika označuje, jak dlouho trvá navázání připojení a vrácení potvrzení|Naslouchací proces|
|ApplicationGatewayTotalTime|Celkový čas aplikační brány|Milisekund|Průměr|Průměrná doba, která trvá pro zpracování požadavku a jeho odpověď, která má být odeslána. Vypočítá se jako průměr intervalu od okamžiku, kdy brána aplikace obdrží první bajt požadavku HTTP do okamžiku dokončení operace odeslání odpovědi. Je důležité si uvědomit, že to obvykle zahrnuje dobu zpracování aplikační brány, čas, který pakety požadavku a odpovědi cestují po síti a čas, který server back-endu trval a odpověděl.|Naslouchací proces|
|BackendConnectTime|Čas připojení back-endu|Milisekund|Průměr|Čas strávený navázáním spojení s back-endovým serverem|Naslouchací proces,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Doba odezvy prvního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navazování připojení k serveru back-end a přijetím prvního bajtu hlavičky odpovědi, přiblížení množin y back-endového serveru|Naslouchací proces,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Doba odezvy posledního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navazování připojení k serveru back-end a přijetím posledního bajtu těla odezvy|Naslouchací proces,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Brána firewall webových aplikací v1 Distribuce celkového pravidla|Počet|Celkem|Brána firewall webových aplikací v1 Celková distribuce pravidel pro příchozí přenosy|RuleGroup,RuleId|
|Blokovaný počet|Brána firewall webových aplikací v1 Distribuce pravidel blokovaných požadavků|Počet|Celkem|Brána firewall webových aplikací v1 blokované požadavky pravidlo distribuce|RuleGroup,RuleId|
|BlockedReqCount|Brána firewall webových aplikací v1 Počet blokovaných požadavků|Počet|Celkem|Počet blokovaných požadavků brány firewall webové aplikace v1|Žádný|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Průměrná šířka pásma|Šířka pásma brány S2S|BajtyPerSekunda|Průměr|Průměrná šířka pásma mezi lokalitami brány v bajtech za sekundu|Žádný|
|Šířka pásma P2S|Šířka pásma brány P2S|BajtyPerSekunda|Průměr|Průměrná šířka pásma brány od bodu do bodu k lokalitě v bajtech za sekundu|Žádný|
|P2SConnectionCount|Počet připojení P2S|Počet|Maximum|Počet připojení bodu k webu brány|Protocol (Protokol)|
|TunnelAverageBandwidth|Šířka pásma tunelu|BajtyPerSekunda|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName,RemoteIP|
|TunnelEgressBytes|Tunelové propojení bajtů|Bajty|Celkem|Odchozí bajty tunelu|ConnectionName,RemoteIP|
|TunnelIngressBytes|Příchozí bajty příchozího přenosu dat tunelového propojení|Bajty|Celkem|Příchozí bajty tunelu|ConnectionName,RemoteIP|
|TunnelEgressPackets|Pakety odchozího přenosu tunelového propojení|Počet|Celkem|Počet odchozích paketů tunelového propojení|ConnectionName,RemoteIP|
|TunnelIngressPackets|Pakety příchozího přenosu dat do tunelového propojení|Počet|Celkem|Počet příchozích paketů tunelového propojení|ConnectionName,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Přetažení paketu ts neodpovídajícího přenosu tunelového přenosu|Počet|Celkem|Počet přetažení odchozích paketů z neshody voliče přenosů tunelového propojení|ConnectionName,RemoteIP|
|TunnelIngressPacketDropTSMismatch|Přetažení paketu neshody příchozího přenosu dat v tunelovém přenosu|Počet|Celkem|Počet příchozích paketů z neshody voliče přenosů tunelového propojení|ConnectionName,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úroveň RxLight|Úroveň RxLight|Počet|Průměr|Rx Úroveň světla v dBm|Odkaz,Jízdní pruh|
|TxLightLevel|TxLightLevel|Počet|Průměr|Tx úroveň světla v dBm|Odkaz,Jízdní pruh|
|Stav správce|Stav správce|Počet|Průměr|Stav správce portu|Odkaz|
|LineProtocol|LineProtocol|Počet|Průměr|Stav čárového protokolu portu|Odkaz|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|Odkaz|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|Odkaz|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|Typ partnerského vztahu|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|Typ partnerského vztahu|
|Globální DosahBitsInPerSecond|Globální DosahBitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|PeeredCircuitSKey|
|Dostupnost protokolu BGP|Dostupnost protokolu BGP|Procento|Průměr|Dostupnost Protokolu BGP od msee ke všem protějškům.|PeeringType,Partner|
|ArpAvailability|Arp Dostupnost|Procento|Průměr|ARP Dostupnost od MSEE ke všem vrstevníkům.|PeeringType,Partner|
|QosDropBitsInPerSecond|VynechánoInBitsPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat poklesl za sekundu|Žádný|
|QosDropBitsOutPerSecond|VynechánoOutBitsPerSecond|CountPerSecond|Průměr|Odchozí bity dat vynechány za sekundu|Žádný|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|Žádný|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|Žádný|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/připojení

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|Žádný|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|Žádný|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInSecond|BitsInPerSecond|CountPerSecond|Průměr|Bity příchozí do Azure za sekundu|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Bity odchozí Azure za sekundu|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Vrácené dotazy podle koncového bodu|Počet|Celkem|Počet vrácených koncových bodů Traffic Manageru v daném časovém rámci|Název koncového bodu|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu podle koncového bodu|Počet|Maximum|1 Pokud je stav sondy koncového bodu "Povoleno", 0 jinak.|Název koncového bodu|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SondaFailedPercent|% sond se nezdařilo.|Procento|Průměr|% sond pro monitorování připojení se nezdařilo.|Žádný|
|AverageRoundtripMs|Vzdušnou dobu odezvy (ms)|Milisekund|Průměr|Průměrná doba odezvy sítě (ms) pro sondy pro monitorování připojení odeslané mezi zdrojem a cílem|Žádný|
|ChecksFailedPercent|Kontroluje procento se nezdařilo (náhled)|Procento|Průměr|% kontrol monitorování připojení se nezdařilo.|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Doba odezvy (ms) (náhled)|Milisekund|Průměr|Doba odezvy v milisekundách pro kontroly monitorování připojení|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Počet|Celkem|Počet požadavků klientů obsluhovaných proxy serverem HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|Velikost požadavku|Bajty|Celkem|Počet bajtů odeslaných jako požadavky od klientů na proxy server HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Velikost odpovědi|Velikost odpovědi|Bajty|Celkem|Počet bajtů odeslaných jako odpovědi z http/s proxy klientům|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Fakturovatelná velikost odpovědi|Fakturovatelná velikost odpovědi|Bajty|Celkem|Počet fakturovatelných bajtů (minimálně 2 KB na požadavek) odeslaných klientům jako odpovědi z proxy serveru HTTP/S.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Počet|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S back-endům|HttpStatus,HttpStatusGroup,Back-end|
|BackendRequestLatency|Latence back-endu|Milisekund|Průměr|Čas vypočítaný od okamžiku odeslání požadavku serverem HTTP/S do back-endu, dokud proxy server HTTP/S neobdržel poslední bajt odpovědi z back-endu|Back-end|
|Celková latence|Celková latence|Milisekund|Průměr|Čas vypočítaný od přijetí požadavku klienta serverem PROXY HTTP/S, dokud klient nepotvrdil poslední bajt odpovědi z proxy serveru HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Procento backendhealth|Procento stavu back-endu|Procento|Průměr|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endů|Back-end,BackendPool|
|WebApplicationFirewallRequestCount|Počet požadavků brány firewall webové aplikace|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webové aplikace|Název_zásady,Název_pravidla,Akce|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Počet|Celkem|Počet dotazů obsluhovaných pro privátní zónu DNS|Žádný|
|RecordSetCount|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v privátní zóně DNS|Žádný|
|Využití kapacity recordSetCapacity|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou Private DNS|Žádný|
|VirtualNetworkLinkCount|Počet propojení virtuální sítě|Počet|Maximum|Počet virtuálních sítí propojených se soukromou zónou DNS|Žádný|
|VirtualNetworkLinkCapacityUtilization|Využití kapacity propojení virtuální sítě|Procento|Maximum|Procento kapacity propojení virtuální sítě využité privátní zónou DNS|Žádný|
|VirtualNetworkWithRegistrationLinkCountCount|Počet registračních odkazů virtuální sítě|Počet|Maximum|Počet virtuálních sítí propojených se soukromou zónou DNS s povolenou automatickou registrací|Žádný|
|VirtualNetworkWithRegistrationCapacityUtilization|Využití kapacity registračního propojení virtuální sítě|Procento|Maximum|Procento propojení virtuální sítě s kapacitou automatické registrace využité privátní zónou DNS|Žádný|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Obory názvů/NotificationHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registration.all|Registrační operace|Počet|Celkem|Počet všech úspěšných registračních operací (vytváření aktualizuje dotazy a odstranění). |Žádný|
|registration.create|Operace vytvoření registrace|Počet|Celkem|Počet všech úspěšných registrací výtvorů.|Žádný|
|registration.update|Operace aktualizace registrace|Počet|Celkem|Počet všech úspěšných aktualizací registrace.|Žádný|
|registrace.získat|Operace čtení registrace|Počet|Celkem|Počet všech úspěšných registračních dotazů.|Žádný|
|registration.delete|Registrace odstranit operace|Počet|Celkem|Počet všech úspěšných odstranění registrace.|Žádný|
|Příchozí|Příchozí zprávy|Počet|Celkem|Počet všech úspěšných volání rozhraní API pro odesílání. |Žádný|
|incoming.scheduled|Odeslaná naplánovaná nabízená oznámení|Počet|Celkem|Naplánovaná nabízená oznámení byla zrušena.|Žádný|
|incoming.scheduled.cancel|Naplánovaná nabízená oznámení byla zrušena.|Počet|Celkem|Naplánovaná nabízená oznámení byla zrušena.|Žádný|
|scheduled.pending|Nevyřízená naplánovaná oznámení|Počet|Celkem|Nevyřízená naplánovaná oznámení|Žádný|
|instalace.všechny|Operace správy instalace|Počet|Celkem|Operace správy instalace|Žádný|
|installation.get|Získat instalační operace|Počet|Celkem|Získat instalační operace|Žádný|
|instalace.upsert|Vytvořit nebo aktualizovat instalační operace|Počet|Celkem|Vytvořit nebo aktualizovat instalační operace|Žádný|
|installation.patch|Operace instalace opravy|Počet|Celkem|Operace instalace opravy|Žádný|
|installation.delete|Odstranit instalační operace|Počet|Celkem|Odstranit instalační operace|Žádný|
|odchozí.allpns.success|Úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení.|Žádný|
|odchozí.allpns.invalidpayload|Chyby datové části|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože server PNS vrátil chybnou chybu datové části.|Žádný|
|chyba odchozí.allpns.pns|Chyby systému externích oznámení|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože došlo k potížím s komunikací s pns (vylučuje problémy s ověřováním).|Žádný|
|chyba odchozí.allpns.channel|Chyby kanálu|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože kanál byl neplatný není spojen a správné aplikace omezena nebo vypršela.|Žádný|
|odchozí.allpns.badorexpiredchannel|Chybné chyby kanálu nebo chyby kanálu s ukončenou platností|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože platnost kanálu/tokenu/id v registraci vypršela nebo byla neplatná.|Žádný|
|odchozí.wns.success|Úspěšná oznámení systému WNS|Počet|Celkem|Počet všech úspěšných oznámení.|Žádný|
|odchozí.wns.invalidcredentials|Chyby autorizace aktualizace WNS (neplatná pověření)|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože zpráva PNS nepřijala zadaných pověření nebo pověření jsou blokována. (Služba Windows Live nerozpozná pověření).|Žádný|
|odchozí.wns.badchannel|Chyba chybného kanálu WNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože ChannelURI v registraci nebyl rozpoznán (WNS stav: 404 nebyl nalezen).|Žádný|
|odchozí.wns.expiredchannel|Chyba kanálu vypršela platnost i wns|Počet|Celkem|Počet nabízených oznámení, které se nezdařily, protože channeluri vypršela (WNS stav: 410 Gone).|Žádný|
|odchozí.wns.škrtil|Oznámení s omezením systému WNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože wns je omezení této aplikace (WNS stav: 406 nepřijatelné).|Žádný|
|odchozí.wns.tokenproviderunreachable|Chyby autorizace WNS (nedostupné)|Počet|Celkem|Služba Windows Live není dostupná.|Žádný|
|odchozí.wns.invalidtoken|Chyby autorizace wns (neplatný token)|Počet|Celkem|Token poskytnutý společnosti WNS není platný (stav WNS: 401 Neautorizováno).|Žádný|
|odchozí.wns.wrongtoken|Chyby autorizace wns (nesprávný token)|Počet|Celkem|Token poskytnutý wns je platný, ale pro jinou aplikaci (WNS stav: 403 Zakázáno). K tomu může dojít, pokud channeluri v registraci je přidružen k jiné aplikaci. Zkontrolujte, zda je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádný|
|outgoing.wns.invalidnotificationformat|Neplatný formát oznámení systému WNS|Počet|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že služba WNS neodmítne všechny neplatné datové části.|Žádný|
|outgoing.wns.invalidnotificationsize|Chyba neplatné velikosti oznámení služba WNS|Počet|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádný|
|odchozí.wns.channelthrottled|Kanál WNS s omezením|Počet|Celkem|Oznámení bylo vynecháno, protože ChannelURI v registraci je omezen (WNS hlavička odpovědi: X-WNS-NotificationStatus:channelThrottled).|Žádný|
|odchozí.wns.channelodpojen|Kanál WNS odpojen|Počet|Celkem|Oznámení bylo vynecháno, protože ChannelURI v registraci je omezen (WNS hlavička odpovědi: X-WNS-DeviceConnectionStatus: odpojeno).|Žádný|
|odchozí.wns.dropped|Zrušená oznámení systému WNS|Počet|Celkem|Oznámení bylo vynecháno, protože ChannelURI v registraci je omezen (X-WNS-NotificationStatus: vynecháno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádný|
|odchozí.wns.pnserror|Chyby wns|Počet|Celkem|Oznámení nebylo doručeno z důvodu chyb při komunikaci se společností WNS.|Žádný|
|chyba odchozí.wns.authentication|Chyby ověřování služby WNS|Počet|Celkem|Oznámení nebylo doručeno z důvodu chyb při komunikaci se službou Windows Live neplatných přihlašovacích údajů nebo nesprávného tokenu.|Žádný|
|odchozí.apns.success|Úspěšná oznámení APNS|Počet|Celkem|Počet všech úspěšných oznámení.|Žádný|
|odchozí.apns.invalidcredentials|Chyby autorizace APNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože zpráva PNS nepřijala zadaných pověření nebo pověření jsou blokována.|Žádný|
|odchozí.apns.badchannel|Chyba chybného kanálu APNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože token je neplatný (stavový kód APNS: 8).|Žádný|
|odchozí.apns.expiredchannel|Chyba kanálu apns vypršela|Počet|Celkem|Počet tokenu, které byly zrušeny kanálem zpětné vazby APNS.|Žádný|
|outgoing.apns.invalidnotificationsize|Chyba neplatné velikosti oznámení APNs|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože datová část byla příliš velká (stavový kód APNS: 7).|Žádný|
|odchozí.apns.pnserror|Chyby APNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila z důvodu chyb při komunikaci s apns.|Žádný|
|odchozí.gcm.success|Úspěšná oznámení GCM|Počet|Celkem|Počet všech úspěšných oznámení.|Žádný|
|odchozí.gcm.invalidcredentials|Chyby autorizace gcm (neplatná pověření)|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože zpráva PNS nepřijala zadaných pověření nebo pověření jsou blokována.|Žádný|
|odchozí.gcm.badchannel|Chyba chybného kanálu GCM|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože nebylrozpoznán id registrace v registraci (výsledek GCM: Neplatná registrace).|Žádný|
|odchozí.gcm.expiredchannel|Chyba kanálu vypršení platnosti gcm|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože registraceId v registraci vypršela (VÝSLEDEK GCM: NotRegistered).|Žádný|
|odchozí.gcm.throttled|Oznámení s omezením gcm|Počet|Celkem|Počet nabízených oznámení, které se nezdařily, protože GCM omezena tuto aplikaci (GCM stavový kód: 501-599 nebo výsledek:Není k dispozici).|Žádný|
|outgoing.gcm.invalidnotificationformat|Formát neplatného oznámení globálního katalogu|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože datová část nebyla správně formátována (výsledek GCM: InvalidDataKey nebo InvalidTttl).|Žádný|
|outgoing.gcm.invalidnotificationsize|Chyba neplatné velikosti oznámení v rámci globálního katalogu|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádný|
|odchozí.gcm.wrongchannel|Chyba nesprávného kanálu gcm|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože id registrace není přidružena k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádný|
|odchozí.gcm.pnserror|Chyby GCM|Počet|Celkem|Počet nabízených oznámení, která se nezdařila z důvodu chyb při komunikaci s GCM.|Žádný|
|chyba odchozího.gcm.authentication|Chyby ověřování globálního katalogu|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože pns nepřijal zadaných pověření pověření jsou blokovány nebo SenderId není správně nakonfigurován v aplikaci (VÝSLEDEK GCM: NeshodaSenderId).|Žádný|
|odchozí.mpns.úspěch|Úspěšná oznámení mpns|Počet|Celkem|Počet všech úspěšných oznámení.|Žádný|
|odchozí.mpns.invalidcredentials|Neplatná pověření protokolu MPNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože zpráva PNS nepřijala zadaných pověření nebo pověření jsou blokována.|Žádný|
|odchozí.mpns.badchannel|Chyba chybného kanálu MPNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože channeluri v registraci nebyl rozpoznán (MPNS stav: 404 nebyl nalezen).|Žádný|
|odchozí.mpns.omezena|Oznámení s omezením systému MPNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože mpns je omezení této aplikace (WNS MPNS: 406 nepřijatelné).|Žádný|
|outgoing.mpns.invalidnotificationformat|Formát oznámení protokolu MPNS|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože datová část oznámení byla příliš velká.|Žádný|
|odchozí.mpns.channelodpojen|Kanál MPNS odpojen|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože ChannelURI v registraci byla odpojena (MPNS stav: 412 nebyl nalezen).|Žádný|
|odchozí.mpns.dropped|Zrušená oznámení mpns|Počet|Celkem|Počet nabízených oznámení, které byly vynechány mpns (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo Suppressed).|Žádný|
|chyba odchozí.mpns.pns|Chyby mpns|Počet|Celkem|Počet nabízených oznámení, která se nezdařila z důvodu chyb při komunikaci se sadou MPNS.|Žádný|
|chyba odchozího.mpns.authentication|Chyby ověřování souborů MPN|Počet|Celkem|Počet nabízených oznámení, která se nezdařila, protože zpráva PNS nepřijala zadaných pověření nebo pověření jsou blokována.|Žádný|
|notificationhub.push|Všechna odchozí oznámení|Počet|Celkem|Všechna odchozí oznámení centra oznámení|Žádný|
|incoming.all.requests|Všechny příchozí požadavky|Počet|Celkem|Celkový počet příchozích žádostí o centrum oznámení|Žádný|
|požadavky incoming.all.failedrequests|Všechny příchozí neúspěšné požadavky|Počet|Celkem|Celkový počet příchozích neúspěšných požadavků na centrum oznámení|Žádný|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_% volných inodů|% volných inodů|Počet|Průměr|Average_% volných inodů|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% volného místa|% volného místa|Počet|Průměr|Average_% volného místa|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% Použité Inody|% použitých inodů|Počet|Průměr|Average_% Použité Inody|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% využitý prostor|% využitého místa|Počet|Průměr|Average_% využitý prostor|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Disk čtení bajtů/s|Čtení bajtů disku/s|Počet|Průměr|Average_Disk čtení bajtů/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Disk čtení/s|Čtení disku/s|Počet|Průměr|Average_Disk čtení/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Disk převody/s|Přenosy disků/s|Počet|Průměr|Average_Disk převody/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Disk zápis bajtů/s|Bajty zápisu disku/s|Počet|Průměr|Average_Disk zápis bajtů/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Disk zápisy/s|Zápisy na disk/s|Počet|Průměr|Average_Disk zápisy/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Free megabajty|Megabajty zdarma|Počet|Průměr|Average_Free megabajty|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Logical bajty disku/s|Bajty logického disku/s|Počet|Průměr|Average_Logical bajty disku/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% dostupné paměti|% dostupné paměti|Počet|Průměr|Average_% dostupné paměti|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% k dispozici odkládací prostor|% dostupného odkládacího prostoru|Počet|Průměr|Average_% k dispozici odkládací prostor|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% použité paměti|% použité paměti|Počet|Průměr|Average_% použité paměti|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% využitý odkládací prostor|% použitého odkládacího prostoru|Počet|Průměr|Average_% využitý odkládací prostor|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Available mbajtů paměti|Dostupná paměť MBytes|Počet|Průměr|Average_Available mbajtů paměti|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Available mbajtů swap|K dispozici mbytes swap|Počet|Průměr|Average_Available mbajtů swap|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Page čtení/s|Čtení stránky/s|Počet|Průměr|Average_Page čtení/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Page zápisy/s|Zápisy stránky/s|Počet|Průměr|Average_Page zápisy/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Pages/s|Stránky/s|Počet|Průměr|Average_Pages/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Used bajtů swapové mezery|Použité mbytes swap ové místo|Počet|Průměr|Average_Used bajtů swapové mezery|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Used paměti MBytes|Mbytes použité paměti|Počet|Průměr|Average_Used paměti MBytes|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|přenesené Average_Total bajty|Celkový počet přenesených bajtů|Počet|Průměr|přenesené Average_Total bajty|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|přijaté bajty Average_Total|Celkový počet přijatých bajtů|Počet|Průměr|přijaté bajty Average_Total|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total bajtů|Celkový počet bajtů|Počet|Průměr|Average_Total bajtů|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total přenášených paketů|Celkový počet přenesených paketů|Počet|Průměr|Average_Total přenášených paketů|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total přijaté pakety|Celkový počet přijatých paketů|Počet|Průměr|Average_Total přijaté pakety|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total chybr|Celkový počet chyb Rx|Počet|Průměr|Average_Total chybr|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total chyby Tx|Celkový počet chyb Tx|Počet|Průměr|Average_Total chyby Tx|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Total Kolize|Celkový počet kolizí|Počet|Průměr|Average_Total Kolize|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Avg. doba disku/čtení|Vně disku sec/čtení|Počet|Průměr|Average_Avg. doba disku/čtení|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Avg. Diskový sk/Přenos|Vznětový disk ový s/přenos|Počet|Průměr|Average_Avg. Diskový sk/Přenos|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Avg. doba disku/zápis|Vg. Disk sec/Zápis|Počet|Průměr|Average_Avg. doba disku/zápis|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Physical bajty disku/s|Bajty fyzického disku/s|Počet|Průměr|Average_Physical bajty disku/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Pct privilegovaný čas|Pct privilegovaný čas|Počet|Průměr|Average_Pct privilegovaný čas|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Pct uživatelský čas|Pct uživatelský čas|Počet|Průměr|Average_Pct uživatelský čas|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Used paměťové kby|Kby využité paměti|Počet|Průměr|Average_Used paměťové kby|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Virtual sdílená paměť|Virtuální sdílená paměť|Počet|Průměr|Average_Virtual sdílená paměť|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% času DPC|% času DPC|Počet|Průměr|Average_% času DPC|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% doba nečinnosti|% doby nečinnosti|Počet|Průměr|Average_% doba nečinnosti|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% doba přerušení|% času přerušení|Počet|Průměr|Average_% doba přerušení|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% Čekací doba IO|% čekací doby vi|Počet|Průměr|Average_% Čekací doba IO|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% pěkný čas|% Pěkný čas|Počet|Průměr|Average_% pěkný čas|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% privilegovaný čas|% privilegovaného času|Počet|Průměr|Average_% privilegovaný čas|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% času procesoru|Procesorový čas v %|Počet|Průměr|Average_% času procesoru|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% uživatelský čas|% času uživatele|Počet|Průměr|Average_% uživatelský čas|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Free fyzická paměť|Volná fyzická paměť|Počet|Průměr|Average_Free fyzická paměť|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Free místa v stránkovacích souborech|Volné místo v stránkovacích souborech|Počet|Průměr|Average_Free místa v stránkovacích souborech|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Free virtuální paměti|Virtuální paměť zdarma|Počet|Průměr|Average_Free virtuální paměti|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Processes|Procesy|Počet|Průměr|Average_Processes|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Size uloženy v stránkovacích souborech|Velikost uložená v stránkovacích souborech|Počet|Průměr|Average_Size uloženy v stránkovacích souborech|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Uptime|Uptime|Počet|Průměr|Average_Uptime|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Users|Uživatelé|Počet|Průměr|Average_Users|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Current délka fronty disku|Aktuální délka fronty disku|Počet|Průměr|Average_Current délka fronty disku|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Available Bajty|Dostupné mbajty|Počet|Průměr|Average_Available Bajty|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_% potvrzených bajtů, které se používají|% potvrzených bajtů je používáno|Počet|Průměr|Average_% potvrzených bajtů, které se používají|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Bytes přijato/s|Přijaté bajty/s|Počet|Průměr|Average_Bytes přijato/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Bytes odesláno/s|Odeslané bajty/s|Počet|Průměr|Average_Bytes odesláno/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Bytes celkem/s|Celkový počet bajtů/s|Počet|Průměr|Average_Bytes celkem/s|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Average_Processor délka fronty|Délka fronty procesoru|Počet|Průměr|Average_Processor délka fronty|Počítač,Název_objektu,InstanceName,Proticestní,Zdrojový systém|
|Prezenční signál|Prezenční signál|Počet|Celkem|Prezenční signál|Počítač,OSType,Verze,SourceComputerId|
|Aktualizace|Aktualizace|Počet|Průměr|Aktualizace|počítač,produkt,klasifikace,UpdateState,volitelné,schváleno|
|Událost|Událost|Počet|Průměr|Událost|Zdroj,EventLog,Počítač,EventCategory,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Služby Microsoft.Peering/peeringServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PrefixLatency|Latence předpony|Milisekund|Průměr|Medián latence předpony|Název předpony|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Dostupnost relaceV4|Dostupnost relace V4|Procento|Průměr|Dostupnost relace V4|ConnectionId|
|Dostupnost relaceV6|Dostupnost relace V6|Procento|Průměr|Dostupnost relace V6|ConnectionId|
|Míra přenosu dat|Rychlost přenosu dat|BitsPerSecond|Průměr|Rychlost přenosu dat v bitech za sekundu|ConnectionId|
|EgressTrafficRate|Rychlost odchozího přenosu|BitsPerSecond|Průměr|Rychlost odchozího přenosu v bitech za sekundu|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/kapacity

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Trvání dotazu|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazu jazyka DAX v posledním intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vlákna: Délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|Žádné dimenze|
|qpu_high_utilization_metric|Vysoké využití procesoru|Počet|Celkem|QPU vysoké využití v last minute, 1 pro vysoké QPU využití, jinak 0|Žádné dimenze|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-3 GB pro A1, 0-5 GB pro A2, 0-10 GB pro A3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná paměť výprask.|Žádné dimenze|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetDistributionbyKlasifikace|Rozdělení aktiv podle klasifikace|Počet|Celkem|Udává počet aktiv s určitou přiřazenou klasifikací, tj.|Klasifikace,Zdroj,Id prostředků|
|AssetDistributionByStorageType|Rozdělení majetku podle typu úložiště|Počet|Celkem|Označuje počet datových zdrojů s určitým typem úložiště.|StorageType,ResourceId|
|CatalogActiveUsers|Denní aktivní uživatelé|Počet|Celkem|Počet aktivních uživatelů denně|ResourceId|
|Využití katalogu|Distribuce využití podle operace|Počet|Celkem|Uveďte počet operací, které uživatel provede v katalogu, tj.|Operace,ResourceId|
|NumberOfAssetsWithClassifications|Počet aktiv s alespoň jednou klasifikací|Počet|Průměr|Označuje počet datových zdrojů s alespoň jednou klasifikací značek.|ResourceId|
|ScanZrušeno|Prohledávací sken byl zrušen.|Počet|Celkem|Označuje počet prohledávacích.|ResourceId|
|Skenování bylo dokončeno.|Prohledávací sken byl dokončen.|Počet|Celkem|Označuje počet úspěšně dokončených prohledávek.|ResourceId|
|ScanFailed|Prohledávací sken se nezdařil.|Počet|Celkem|Označuje počet neúspěšných prohledávek.|ResourceId|
|ScanTimeTaken|Doba skenování|Sekundy|Celkem|Označuje celkový čas skenování v sekundách.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Obory názvů Microsoft.Relay/Namespaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections-Úspěch|ListenerConnections-Úspěch|Počet|Celkem|Úspěšné ListenerConnections pro Microsoft.Relay.|EntityName,Výsledek operace|
|Chyba ListenerConnections-ClientError|Chyba ListenerConnections-ClientError|Počet|Celkem|ClientError na ListenerConnections pro Microsoft.Relay.|EntityName,Výsledek operace|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Počet|Celkem|Chyba serveru při připojení listenerconnections pro Microsoft.Relay.|EntityName,Výsledek operace|
|SenderConnections-Úspěch|SenderConnections-Úspěch|Počet|Celkem|Úspěšné SenderConnections pro Microsoft.Relay.|EntityName,Výsledek operace|
|Chyba SenderConnections-ClientError|Chyba SenderConnections-ClientError|Počet|Celkem|ClientError na SenderConnections pro Microsoft.Relay.|EntityName,Výsledek operace|
|Chyba senderConnections-ServerError|Chyba senderConnections-ServerError|Počet|Celkem|Chyba serveru v připojení SenderConnections pro microsoft.relay.|EntityName,Výsledek operace|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Počet|Celkem|Celkový počet listenerconnections pro Microsoft.Relay.|Název entity|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Počet|Celkem|Celkový počet požadavků senderconnections pro Microsoft.Relay.|Název entity|
|Aktivní připojení|Aktivní připojení|Počet|Celkem|Celkový počet aktivních připojení pro Microsoft.Relay.|Název entity|
|Aktivní posluchači|Aktivní posluchači|Počet|Celkem|Celkový počet aktivních listenerů pro microsoft.relay.|Název entity|
|BytesPřevedeno|BytesPřevedeno|Počet|Celkem|Celkový počet bajtů převedených pro Microsoft.Relay.|Název entity|
|ListenerDisconnects|ListenerDisconnects|Počet|Celkem|Celkový listenerDisconnects pro Microsoft.Relay.|Název entity|
|OdesílatelOdpojidne|OdesílatelOdpojidne|Počet|Celkem|Celkem SenderDisconnects pro Microsoft.Relay.|Název entity|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence hledání|Sekundy|Průměr|Průměrná latence vyhledávání pro vyhledávací službu|Žádný|
|Vyhledávací dotazyPerSecond|Vyhledávací dotazy za sekundu|CountPerSecond|Průměr|Vyhledávací dotazy za sekundu pro vyhledávací službu|Žádný|
|OmezeníVyhledávací dotazyProcento|Procento dotazů s omezením|Procento|Průměr|Procento vyhledávacích dotazů, které byly omezeny pro vyhledávací službu|Žádný|


## <a name="microsoftservicebusnamespaces"></a>Obory názvů Microsoft.ServiceBus

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úspěšné požadavky|Úspěšné požadavky|Počet|Celkem|Celkový počet úspěšných požadavků na obor názvů|EntityName,Výsledek operace|
|Serverchyby|Chyby serveru.|Počet|Celkem|Chyby serveru pro microsoft.servicebus.|EntityName,Výsledek operace|
|Uživatelské chyby|Chyby uživatele.|Počet|Celkem|Chyby uživatelů pro Microsoft.ServiceBus.|EntityName,Výsledek operace|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft.ServiceBus.|EntityName,Výsledek operace|
|Příchozí požadavky|Příchozí požadavky|Počet|Celkem|Příchozí požadavky na Microsoft.ServiceBus.|Název entity|
|Příchozí zprávy|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft.ServiceBus.|Název entity|
|Odchozí zprávy|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft.ServiceBus.|Název entity|
|Aktivní připojení|Aktivní připojení|Počet|Celkem|Celkový počet aktivních připojení pro microsoft.servicebus.|Žádný|
|Otevřená připojení|Připojení byla otevřena.|Počet|Průměr|Otevřená připojení pro microsoft.servicebus.|Název entity|
|Připojení uzavřeno|Připojení uzavřena.|Počet|Průměr|Připojení uzavřena pro Microsoft.ServiceBus.|Název entity|
|Velikost|Velikost|Bajty|Průměr|Velikost fronty/tématu v bajtech.|Název entity|
|Zprávy|Počet zpráv ve frontě nebo tématu.|Počet|Průměr|Počet zpráv ve frontě nebo tématu.|Název entity|
|Aktivní zprávy|Počet aktivních zpráv ve frontě nebo tématu.|Počet|Průměr|Počet aktivních zpráv ve frontě nebo tématu.|Název entity|
|Zprávy s mrtvým písmem|Počet nedoručených zpráv ve frontě nebo tématu.|Počet|Průměr|Počet nedoručených zpráv ve frontě nebo tématu.|Název entity|
|Naplánované zprávy|Počet naplánovaných zpráv ve frontě nebo tématu.|Počet|Průměr|Počet naplánovaných zpráv ve frontě nebo tématu.|Název entity|
|Využití prostředků_prostředků názvů|Procesor|Procento|Maximum|Metrika využití procesoru oboru výkonu oboru výkonu oboru výkonu služby Service bus premium.|Replika|
|Využití paměti oboru názvů|Využití paměti|Procento|Maximum|Metrika využití paměti oboru názvů Service Bus premium.|Replika|
|CPUXNS|PROCESOR (zastaralé)|Procento|Maximum|Metrika využití procesoru oboru výkonu oboru výkonu oboru výkonu služby Service bus premium. Tato metrika je depricated. Místo toho použijte metriku procesoru (NamespaceCpuUsage).|Replika|
|WSXNS|Využití paměti (zastaralé)|Procento|Maximum|Metrika využití paměti oboru názvů Service Bus premium. Tato metrika je zastaralá. Místo toho použijte metriku Využití paměti (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/aplikace

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Přidělení procesoru|Přidělení procesoru|Počet|Průměr|Procesor přidělený tomuto kontejneru v jádrech mili|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Přidělená paměť|Přidělená paměť|Bajty|Průměr|Paměť přidělená tomuto kontejneru v MB|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|Počet|Průměr|Skutečné využití procesoru v jádrech mili|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Skutečná paměť|Skutečná paměť|Bajty|Průměr|Skutečné využití paměti v MB|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Využití procesoru|Využití procesoru|Procento|Průměr|Využití procesoru pro tento kontejner jako procento přiděleného procesoru|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Využití paměti|Využití paměti|Procento|Průměr|Využití procesoru pro tento kontejner jako procento přiděleného procesoru|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|Stav aplikace|Stav aplikace|Počet|Průměr|Stav aplikace Síťovina service fabric|Název_aplikace,Stav|
|Stav služby|Stav služby|Počet|Průměr|Stav služby v aplikaci Service Fabric Mesh|Název_aplikace,Stav,Název_služby|
|ServiceReplicaStatus|ServiceReplicaStatus|Počet|Průměr|Stav repliky služby v aplikaci Service Fabric Mesh|ApplicationName,Status,ServiceName,ServiceReplicaName|
|ContainerStatus|ContainerStatus|Počet|Průměr|Stav kontejneru v aplikaci Service Fabric Mesh|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName,Status|
|Restartovatcount|Restartovatcount|Počet|Průměr|Počet restartování kontejneru v aplikaci Service Fabric Mesh|Název_aplikace,Status,Název_služby,Název_služby__|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Počet|Maximum|Velikost připojení uživatele.|Koncový bod|
|Počet zpráv|Počet zpráv|Počet|Celkem|Celkový počet zpráv.|Žádný|
|Příchozí provoz|Příchozí provoz|Bajty|Celkem|Příchozí provoz služeb|Žádný|
|Odchozí provoz|Odchozí provoz|Bajty|Celkem|Odchozí provoz služby|Žádný|
|Uživatelské chyby|Uživatelské chyby|Procento|Maximum|Procento uživatelských chyb|Žádný|
|Chyby systému|Systémové chyby|Procento|Maximum|Procento systémových chyb|Žádný|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servery/databáze

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádný|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádný|
|log_write_percent|Procento vio protokolu|Procento|Průměr|Procento vypořivodé evidence. Nevztahuje se na datové sklady.|Žádný|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU. Platí pro databáze založené na DTU.|Žádný|
|úložiště|Využitý datový prostor|Bajty|Maximum|Využitý datový prostor. Nevztahuje se na datové sklady.|Žádný|
|connection_successful|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádný|
|connection_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádný|
|blocked_by_firewall|Blokováno bránou firewall|Počet|Celkem|Blokováno bránou firewall|Žádný|
|Zablokování|Zablokování|Počet|Celkem|Zablokování. Nevztahuje se na datové sklady.|Žádný|
|storage_percent|Procento využitého datového prostoru|Procento|Maximum|V procentech využitého datového prostoru. Nevztahuje se na datové sklady nebo hyperškálovatdatabáze.|Žádný|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Procento|Průměr|Procento úložiště OLTP v paměti. Nevztahuje se na datové sklady.|Žádný|
|workers_percent|Procento pracovníků|Procento|Průměr|Procento pracovníků. Nevztahuje se na datové sklady.|Žádný|
|sessions_percent|Procento návštěv|Procento|Průměr|Procento relací. Nevztahuje se na datové sklady.|Žádný|
|dtu_limit|DTU Limit|Počet|Průměr|Limit DTU. Platí pro databáze založené na DTU.|Žádný|
|dtu_used|Používá se DTU|Počet|Průměr|Používá se DTU. Platí pro databáze založené na DTU.|Žádný|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro databáze založené na virtuálních jádrech.|Žádný|
|cpu_used|Použitý procesor|Počet|Průměr|Použitý procesor. Platí pro databáze založené na virtuálních jádrech.|Žádný|
|dwu_limit|DWU limit|Počet|Maximum|DWU limit. Platí pouze pro datové sklady.|Žádný|
|dwu_consumption_percent|Procento DWU|Procento|Maximum|Procento DWU. Platí pouze pro datové sklady.|Žádný|
|dwu_used|Použitý DWU|Počet|Maximum|DWU používá. Platí pouze pro datové sklady.|Žádný|
|cache_hit_percent|Procento přístupů do mezipaměti|Procento|Maximum|Procento zásahu do mezipaměti. Platí pouze pro datové sklady.|Žádný|
|cache_used_percent|Procento použité mezipaměti|Procento|Maximum|Procento použité mezipaměti. Platí pouze pro datové sklady.|Žádný|
|sqlserver_process_core_percent|Procento jádra procesu SQL Serveru|Procento|Maximum|Procento využití procesoru pro proces serveru SQL Server měřené operačním systémem.|Žádný|
|sqlserver_process_memory_percent|Procento paměti procesu serveru SQL Server|Procento|Maximum|Procento využití paměti pro proces serveru SQL Server měřeno operačním systémem.|Žádný|
|tempdb_data_size|Velikost datového souboru Tempdb kilobajty|Počet|Maximum|Velikost datového souboru Tempdb kilobajty. Nevztahuje se na datové sklady.|Žádný|
|tempdb_log_size|Velikost souboru protokolu databáze Tempdb kilobajty|Počet|Maximum|Velikost souboru protokolu databáze Tempdb kilobajtů. Nevztahuje se na datové sklady.|Žádný|
|tempdb_log_used_percent|Použitý protokol procenta databáze dat|Procento|Maximum|Použitý protokol procenta databáze. Nevztahuje se na datové sklady.|Žádný|
|local_tempdb_usage_percent|Procento místní databáze tempdb|Procento|Průměr|Procento místní databáze tempdb. Platí pouze pro datové sklady.|Žádný|
|app_cpu_billed|Fakturováno s procesorem aplikace|Počet|Celkem|Fakturováno s procesorem aplikace. Platí pro databáze bez serveru.|Žádný|
|app_cpu_percent|Procento procesoru aplikace|Procento|Průměr|Procento procesoru aplikace. Platí pro databáze bez serveru.|Žádný|
|app_memory_percent|Procento paměti aplikace|Procento|Průměr|Procento paměti aplikace. Platí pro databáze bez serveru.|Žádný|
|allocated_data_storage|Přidělený datový prostor|Bajty|Průměr|Přidělené úložiště dat. Nevztahuje se na datové sklady.|Žádný|
|memory_usage_percent|Procento paměti|Procento|Maximum|Procento paměti. Platí pouze pro datové sklady.|Žádný|
|dw_backup_size_gb|Velikost úložiště dat|Počet|Celkem|Velikost úložiště dat se skládá z velikosti vašich dat a transakčního protokolu. Metrika se započítává do části úložiště vaší faktury. Platí pouze pro datové sklady.|Žádný|
|dw_snapshot_size_gb|Velikost úložiště snímku|Počet|Celkem|Velikost úložiště snímků je velikost přírůstkových změn zachycených snímky k vytvoření uživatelem definovaných a automatických bodů obnovení. Metrika se započítává do části úložiště vaší faktury. Platí pouze pro datové sklady.|Žádný|
|dw_geosnapshot_size_gb|Velikost úložiště zotavení po havárii|Počet|Celkem|Velikost úložiště pro zotavení po havárii se ve vaší vyúčtování projeví jako "Úložiště pro zotavení po havárii". Platí pouze pro datové sklady.|Žádný|
|wlg_allocation_relative_to_system_percent|Přidělení skupiny pracovních vytížek podle procent systému|Procento|Maximum|Přidělené procento zdrojů vzhledem k celému systému na skupinu pracovního vytížení. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Přidělení skupiny pracovních vytížek podle procenta prostředků cap|Procento|Maximum|Přidělené procento zdrojů vzhledem k určeným zdrojům limitu na skupinu pracovního vytížení. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny pracovního vytížení. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Dotazy skupiny úloh ve frontě|Počet|Celkem|Dotazy zařazené do fronty v rámci skupiny úloh. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktivní dotazy|Počet|Celkem|Aktivní dotazy napříč všemi skupinami úloh. Platí pouze pro datové sklady.|Žádný|
|queued_queries|Dotazy zařazené do fronty|Počet|Celkem|Dotazy zařazené do fronty ve všech skupinách úloh. Platí pouze pro datové sklady.|Žádný|
|wlg_active_queries_timeouts|Časové limity dotazů skupiny úloh|Počet|Celkem|Dotazy, které mají časový čas pro skupinu úloh. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Efektivní min zdroj procent|Procento|Maximum|Minimální procento zdrojů rezervovaných a izolovaných pro skupinu pracovního vytížení s přihlédnutím k minimální úrovni služby. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Procento prostředků efektivního limitu|Procento|Maximum|Pevný limit na procento zdrojů povolených pro skupinu pracovního vytížení, s přihlédnutím k efektivní min procento zdrojů přidělené pro jiné skupiny pracovního vytížení. Platí pouze pro datové sklady.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Úplná velikost úložiště záloh|Bajty|Maximum|Kumulativní úplná velikost úložiště záloh. Platí pro databáze založené na virtuálních jádrech. Nevztahuje se na hyperškálovací databáze.|Žádný|
|diff_backup_size_bytes|Velikost úložiště rozdílového úložiště|Bajty|Maximum|Kumulativní velikost rozdílového úložiště záloh. Platí pro databáze založené na virtuálních jádrech. Nevztahuje se na hyperškálovací databáze.|Žádný|
|log_backup_size_bytes|Protokolovat velikost úložiště záloh|Bajty|Maximum|Kumulativní velikost úložiště záloh protokolu. Platí pro databáze založené na virtuálních jádrech a hyperškálování.|Žádný|
|snapshot_backup_size_bytes|Velikost úložiště záloh snímků|Bajty|Maximum|Kumulativní velikost úložiště zálohy snímků. Platí pro hyperškálovací databáze.|Žádný|
|base_blob_size_bytes|Velikost základního úložiště objektů blob|Bajty|Maximum|Základní velikost úložiště objektů blob. Platí pro hyperškálovací databáze.|Žádný|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servery/elastické bazény

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádný|
|database_cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Id databázového zdroje|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádný|
|database_physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Id databázového zdroje|
|log_write_percent|Procento vio protokolu|Procento|Průměr|Procento vio protokolu|Žádný|
|database_log_write_percent|Procento vio protokolu|Procento|Průměr|Procento vio protokolu|Id databázového zdroje|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU. Platí pro elastické fondy založené na DTU.|Žádný|
|database_dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|Id databázového zdroje|
|storage_percent|Procento využitého datového prostoru|Procento|Průměr|Procento využitého datového prostoru|Žádný|
|workers_percent|Procento pracovníků|Procento|Průměr|Procento pracovníků|Žádný|
|database_workers_percent|Procento pracovníků|Procento|Průměr|Procento pracovníků|Id databázového zdroje|
|sessions_percent|Procento návštěv|Procento|Průměr|Procento návštěv|Žádný|
|database_sessions_percent|Procento návštěv|Procento|Průměr|Procento návštěv|Id databázového zdroje|
|eDTU_limit|limit eDTU|Počet|Průměr|eDTU. Platí pro elastické fondy založené na DTU.|Žádný|
|storage_limit|Maximální velikost dat|Bajty|Průměr|Maximální velikost dat|Žádný|
|eDTU_used|použitý eDTU|Počet|Průměr|eDTU. Platí pro elastické fondy založené na DTU.|Žádný|
|database_eDTU_used|použitý eDTU|Počet|Průměr|použitý eDTU|Id databázového zdroje|
|storage_used|Využitý datový prostor|Bajty|Průměr|Využitý datový prostor|Žádný|
|database_storage_used|Využitý datový prostor|Bajty|Průměr|Využitý datový prostor|Id databázového zdroje|
|xtp_storage_percent|Procento úložiště OLTP v paměti|Procento|Průměr|Procento úložiště OLTP v paměti|Žádný|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro elastické fondy založené na virtuálních jádrech.|Žádný|
|database_cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru|Id databázového zdroje|
|cpu_used|Použitý procesor|Počet|Průměr|Použitý procesor. Platí pro elastické fondy založené na virtuálních jádrech.|Žádný|
|database_cpu_used|Použitý procesor|Počet|Průměr|Použitý procesor|Id databázového zdroje|
|sqlserver_process_core_percent|Procento jádra procesu SQL Serveru|Procento|Maximum|Procento využití procesoru pro proces serveru SQL Server měřené operačním systémem. Platí pro elastické bazény.|Žádný|
|sqlserver_process_memory_percent|Procento paměti procesu serveru SQL Server|Procento|Maximum|Procento využití paměti pro proces serveru SQL Server měřeno operačním systémem. Platí pro elastické bazény.|Žádný|
|tempdb_data_size|Velikost datového souboru Tempdb kilobajty|Počet|Maximum|Velikost datového souboru Tempdb kilobajty|Žádný|
|tempdb_log_size|Velikost souboru protokolu databáze Tempdb kilobajty|Počet|Maximum|Velikost souboru protokolu databáze Tempdb kilobajty|Žádný|
|tempdb_log_used_percent|Použitý protokol procenta databáze dat|Procento|Maximum|Použitý protokol procenta databáze dat|Žádný|
|allocated_data_storage|Přidělený datový prostor|Bajty|Průměr|Přidělený datový prostor|Žádný|
|database_allocated_data_storage|Přidělený datový prostor|Bajty|Průměr|Přidělený datový prostor|Id databázového zdroje|
|allocated_data_storage_percent|Procento přidělené datového prostoru|Procento|Maximum|Procento přidělené datového prostoru|Žádný|

## <a name="microsoftsqlservers"></a>Microsoft.SQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|Elastické PoolResourceId|
|database_dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|Id databázového prostředku,ElasticPoolResourceId|
|storage_used|Využitý datový prostor|Bajty|Průměr|Využitý datový prostor|Elastické PoolResourceId|
|database_storage_used|Využitý datový prostor|Bajty|Průměr|Využitý datový prostor|Id databázového prostředku,ElasticPoolResourceId|
|dtu_used|Používá se DTU|Počet|Průměr|Používá se DTU|Id databázového zdroje|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Počet|Průměr|Počet virtuálních jader|Žádný|
|avg_cpu_percent|Průměrné procento procesoru|Procento|Průměr|Průměrné procento procesoru|Žádný|
|reserved_storage_mb|Vyhrazený úložný prostor|Počet|Průměr|Vyhrazený úložný prostor|Žádný|
|storage_space_used_mb|Využitý úložný prostor|Počet|Průměr|Využitý úložný prostor|Žádný|
|io_requests|Počet požadavků vi|Počet|Průměr|Počet požadavků vi|Žádný|
|io_bytes_read|Čtení bajtů vi|Bajty|Průměr|Čtení bajtů vi|Žádný|
|io_bytes_written|IO bajty napsané|Bajty|Průměr|IO bajty napsané|Žádný|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Usedcapacity|Využitá kapacita|Bajty|Průměr|Využitá kapacita účtu|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná latence od konce na konec úspěšných požadavků provedených na službě úložiště nebo zadané operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Možnost objektů blob|Kapacita objektu blob|Bajty|Průměr|Velikost úložiště používané službou Blob účtu úložiště v bajtů.|Objekt BlobType,vrstva|
|Počet objektů BlobCount|Počet objektů blob|Počet|Průměr|Počet objektů Blob ve službě objektu blob účtu úložiště.|Objekt BlobType,vrstva|
|ContainerCount|Počet kontejnerů objektů blob|Počet|Průměr|Počet kontejnerů ve službě objektu Blob účtu úložiště.|Žádný|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště používaného indexem ADLS Gen2 (Hierarchické) v bajtech.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná latence od konce na konec úspěšných požadavků provedených na službě úložiště nebo zadané operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TabulkaKapacita|Kapacita tabulky|Bajty|Průměr|Velikost úložiště používané službou Table účtu úložiště v bajtech.|Žádný|
|Počet tabulek|Počet tabulek|Počet|Průměr|Číslo tabulky ve službě Table účtu úložiště.|Žádný|
|Počet tabulkových entií|Počet entit tabulky|Počet|Průměr|Počet entit tabulky ve službě Table účtu úložiště.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná latence od konce na konec úspěšných požadavků provedených na službě úložiště nebo zadané operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Souborkapacita|Kapacita souboru|Bajty|Průměr|Velikost úložiště používaného službou File účtu úložiště v bajtech.|Fileshare|
|Počet souborů|Počet souborů|Počet|Průměr|Počet souborů ve službě Soubor účtu úložiště.|Fileshare|
|FileShareCount|Počet sdílení souborů|Počet|Průměr|Počet sdílených složek ve službě Soubor účtu úložiště.|Žádný|
|FileShareSnapshotCount|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve sdílené složce ve službě souborů účtu úložiště.|Fileshare|
|Soubor ShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště používané snímky v účtu úložiště souborové služby v bajtů.|Fileshare|
|FileShareQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit na velikost úložiště, které můžete použít službou Azure Files Service v bajtů.|Fileshare|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování,Sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování,Sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|geotype,ApiName,Ověřování,Sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná latence od konce na konec úspěšných požadavků provedených na službě úložiště nebo zadané operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování,Sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování,Sdílení souborů|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště používané službou Fronty účtu úložiště v bajtech.|Žádný|
|Počet front|Počet front|Počet|Průměr|Počet fronty ve službě Fronty účtu úložiště.|Žádný|
|QueueMessageCount|Počet zpráv fronty|Počet|Průměr|Přibližný počet zpráv fronty ve službě Fronty účtu úložiště.|Žádný|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|Type odezvy,GeoType,ApiName,Ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství dat příchozího přenosu dat v bajtů. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|geotype,ApiName,Ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Množství dat odchozího přenosu v bajtech. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|geotype,ApiName,Ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná službou Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|geotype,ApiName,Ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná latence od konce na konec úspěšných požadavků provedených na službě úložiště nebo zadané operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|geotype,ApiName,Ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|geotype,ApiName,Ověřování|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Klientské ops|Celkový počet viponek klienta|Počet|Průměr|Rychlost operací klientských souborů zpracovaných mezipamětí.|Žádný|
|Latence klienta|Průměrná latence klienta|Milisekund|Průměr|Průměrná latence operací klientských souborů do mezipaměti úložiště.|Žádný|
|KlientreadiOPS|Klient číst vops|CountPerSecond|Průměr|Operace čtení klienta za sekundu.|Žádný|
|KlientReadThroughput|Průměrná propustnost čtení mezipaměti|BajtyPerSekunda|Průměr|Klient čte rychlost přenosu dat.|Žádný|
|KlientwriteIOPS|Klient zapsat viopy|CountPerSecond|Průměr|Operace zápisu klienta za sekundu.|Žádný|
|KlientWriteThroughput|Průměrná propustnost zápisu do mezipaměti|BajtyPerSekunda|Průměr|Rychlost přenosu dat klienta.|Žádný|
|KlientMetadataReadIOPS|Metadata klienta čtení VOPS|CountPerSecond|Průměr|Rychlost operací souborů klienta odeslaných do mezipaměti, s výjimkou čtení dat, které nemění trvalý stav.|Žádný|
|KlientMetadataWriteIOPS|VIOP zápisu metadat klienta|CountPerSecond|Průměr|Rychlost operací souborů klienta odeslaných do mezipaměti, s výjimkou zápisů dat, které upravují trvalý stav.|Žádný|
|ClientlockiOPS|VĚTEV zámku klienta|CountPerSecond|Průměr|Operace uzamčení souborů klienta za sekundu.|Žádný|
|StorageTargetHealth|Stav cíle úložiště|Počet|Průměr|Logické výsledky testu připojení mezi cíli mezipaměti a úložiště.|Žádný|
|Uptime|Uptime|Počet|Průměr|Logické výsledky testu připojení mezi mezipamětí a monitorovacím systémem.|Žádný|
|Optimalizace pro storagetarget|Celkový počet vyskladnění cílů úložiště|Počet|Průměr|Rychlost všech operací se soubory cache odešle na konkrétní StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Vyps atleta cílová možnost zápisu StorageTarget|Počet|Průměr|Rychlost operací zápisu souboru, které mezipaměť odesílá určitému cíli storagetarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Propustnost asynchronního zápisu StorageTarget|BajtyPerSekunda|Průměr|Rychlost cache asynchronně zapisuje data do konkrétní StorageTarget. Jedná se o oportunistické zápisy, které nezpůsobí, že klienti blokovat.|StorageTarget|
|StorageTargetSyncWriteThroughput|Propustnost zápisu StorageTarget|BajtyPerSekunda|Průměr|Rychlost mezipaměti synchronně zapisuje data do konkrétnístoragetarget. Jedná se o zápisy, které způsobují, že klienti blokují.|StorageTarget|
|StorageTargetTotalWriteThroughput|Celková propustnost zápisu StorageTarget|BajtyPerSekunda|Průměr|Celková rychlost, že mezipaměť zapíše data na konkrétní StorageTarget.|StorageTarget|
|StorageTargetLatency|Latence cíle úložiště|Milisekund|Průměr|Průměrná latence odezvy všech operací souborů, které mezipaměť odesílá partrikulárnímu úložištiTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Viposlužby pro čtení metadat StorageTarget|CountPerSecond|Průměr|Rychlost operací se soubory, které nemění trvalý stav a s výjimkou operace čtení, které mezipaměti odešle na konkrétní StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Viposlužby zápisu metadat StorageTarget|CountPerSecond|Průměr|Rychlost operací se soubory, které upravují trvalý stav a bez operace zápisu, kterou mezipaměť odesílá do konkrétního cíle úložiště.|StorageTarget|
|Zásobníky připravenosti cíl storageTarget|Vyčitatá ops pro čtení storagetarget|CountPerSecond|Průměr|Rychlost operací čtení souborů, které mezipaměť odesílá určitému cíli storagetarget.|StorageTarget|
|ÚložištěCílReadAheadThroughputput|Propustnost pročtení dopředu StorageTarget|BajtyPerSekunda|Průměr|Rychlost cache příležitostně čte data z StorageTarget.|StorageTarget|
|Správce výplně TargetFillput|Propustnost výplně StorageTarget|BajtyPerSekunda|Průměr|Rychlost, jakou cache čte data z storagetarget pro zpracování mezipaměť chybí.|StorageTarget|
|StorageTargetTotalReadThroughput|Celková propustnost čtení storagetarget|BajtyPerSekunda|Průměr|Celková rychlost, že mezipaměť čte data z konkrétní storagetarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Výsledek serverové synchronizuje|Výsledek synchronizace relace|Počet|Průměr|Metrika, která zaznamenává hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|Počet chyb a chyb y synchronizačních synchronizujích položek|Nesynchronizované soubory|Počet|Celkem|Počet souborů se nepodařilo synchronizovat|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souboru přenesená pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|
|Heartbeat služby StorageSyncServer|Stav online serveru|Počet|Maximum|Metrika, která zaznamenává hodnotu 1 pokaždé, když resigtered server úspěšně zaznamená prezenční signál s koncovým bodem cloudu|ServerName|
|StorageSyncRecallIOTotalSizebajty|Vyvolání vrstvení cloudu|Bajty|Celkem|Celková velikost dat stažených serverem|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Velikost vyvolání vrstvení v cloudu|Bajty|Celkem|Velikost stažených dat|SyncGroupName,Název_serveru|
|StorageSyncRecallThroughputtesPerSecond|Propustnost odvolání vrstvení v cloudu|BajtyPerSekunda|Průměr|Velikost propustnost vyvolání dat|SyncGroupName,Název_serveru|
|StorageSyncRecalledNetworkBytesByApplication|Velikost vyvolání vrstvení v cloudu podle aplikace|Bajty|Celkem|Velikost dat stažených aplikací|SyncGroupName,Název_serveru,Název_aplikace|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemPočet chyb|Nesynchronizované soubory|Počet|Celkem|Počet souborů se nepodařilo synchronizovat|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchPřevedenyFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souboru přenesená pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|Název_serveru EndpointName,Směr synchronizace|
|Počet chyb chyb v centru serveru EndPointSyncSessionPerItem|Nesynchronizované soubory|Počet|Celkem|Počet souborů se nepodařilo synchronizovat|Název_serveru EndpointName,Směr synchronizace|
|ServerEndpointBatchpřevedenéfileby|Synchronizované bajty|Bajty|Celkem|Celková velikost souboru přenesená pro relace synchronizace|Název_serveru EndpointName,Směr synchronizace|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Serverheartbeat|Stav online serveru|Počet|Maximum|Metrika, která zaznamenává hodnotu 1 pokaždé, když resigtered server úspěšně zaznamená prezenční signál s koncovým bodem cloudu|ServerResourceId,Název_serveru|
|ServerRecallIOTotalSizeBytes|Vyvolání vrstvení cloudu|Bajty|Celkem|Celková velikost dat stažených serverem|ServerResourceId,Název_serveru|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití prostředků|Využití SU %|Procento|Maximum|Využití SU %|LogicalName,PartitionId|
|InputEvents|Vstupní události|Počet|Celkem|Vstupní události|LogicalName,PartitionId|
|VstupOvatbajty|Bajty vstupních událostí|Bajty|Celkem|Bajty vstupních událostí|LogicalName,PartitionId|
|LateInputEvents|Události pozdního vstupu|Počet|Celkem|Události pozdního vstupu|LogicalName,PartitionId|
|Výstupní události|Výstupní události|Počet|Celkem|Výstupní události|LogicalName,PartitionId|
|Chyby převodu|Chyby převodu dat|Počet|Celkem|Chyby převodu dat|LogicalName,PartitionId|
|chyby|Chyby za běhu|Počet|Celkem|Chyby za běhu|LogicalName,PartitionId|
|Vynecháno Nebo AdjustedEvents|Události mimo pořadí|Počet|Celkem|Události mimo pořadí|LogicalName,PartitionId|
|AMLCalloutPožadavky|Požadavky na funkce|Počet|Celkem|Požadavky na funkce|LogicalName,PartitionId|
|Požadavky AMLCalloutFailed|Neúspěšné požadavky na funkci|Počet|Celkem|Neúspěšné požadavky na funkci|LogicalName,PartitionId|
|AMLCalloutInputEvents|Události funkcí|Počet|Celkem|Události funkcí|LogicalName,PartitionId|
|Chyba deserializace|Vstupní chyby deserializace|Počet|Celkem|Vstupní chyby deserializace|LogicalName,PartitionId|
|EarlyInputEvents|Události včasného vstupu|Počet|Celkem|Události včasného vstupu|LogicalName,PartitionId|
|VýstupVozemdelayseconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|LogicalName,PartitionId|
|InputEventsSourcesBacklogged|Zpětné vstupní události|Počet|Maximum|Zpětné vstupní události|LogicalName,PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Počet|Celkem|Přijaté vstupní zdroje|LogicalName,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|OrchestracePipelineRunsEnded|Spuštění kanálu bylo ukončeno.|Počet|Celkem|Počet spuštění kanálu orchestrace, které bylo úspěšné, neúspěšné nebo bylo zrušeno.|Výsledek,Typ selhání,Kanál|
|OrchestraceActivityRunsEnded|Spuštění aktivity bylo ukončeno.|Počet|Celkem|Počet aktivit orchestrace, které byly úspěšné, neúspěšné nebo byly zrušeny.|Výsledek,Typ selhání,Aktivita,Typ aktivity,Kanál|
|OrchestraceTriggersEnded|Aktivační události byly ukončeny.|Počet|Celkem|Počet aktivačních událostí orchestrace, které byly úspěšné, neúspěšné nebo byly zrušeny|Výsledek,Typ selhání,Aktivační událost|
|SQLOnDemandLoginPokusy|Pokusy o přihlášení|Počet|Celkem|Počet pokusů o přihlášení, které se nezdařily nebo se nezdařily|Výsledek|
|SQLOnDemandQueriesUkončeno|Dotazy byly ukončeny.|Počet|Celkem|Počet dotazů, které byly úspěšné, neúspěšné nebo byly zrušeny|Výsledek|
|SQLOnDemandQueryZpracovanéedbytes|Zpracovávaná data|Bajty|Celkem|Množství dat zpracovávaných dotazy|Žádný|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/pracovní prostory/bigDataPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SparkJobsUkončeno|Ukončené aplikace|Počet|Celkem|Počet ukončených žádostí|JobType,Výsledek úlohy|
|CoresKapacita|Kapacita jader|Počet|Maximum|Kapacita jader|Žádný|
|MemoryCapacityGB|Kapacita paměti (GB)|Počet|Maximum|Kapacita paměti (GB)|Žádný|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/pracovní prostory/sqlPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DWULimit|DWU limit|Počet|Maximum|Cíl úrovně služeb fondu SQL|Žádný|
|DWUUsed|Použitý DWU|Počet|Maximum|Představuje vysoké úrovni reprezentace využití v celém fondu SQL. Měřeno mezí DWU * Procento DWU|Žádný|
|DWUUsedPercent|Procento použitého DWU|Procento|Maximum|Představuje vysoké úrovni reprezentace využití v celém fondu SQL. Měřeno maximálním procentem mezi procentem procesoru a vprocentázoucími údaji o datech|Žádný|
|ConnectionsBlockedByFirewall|Připojení blokovaná bránou firewall|Počet|Celkem|Počet připojení blokovaných pravidly brány firewall. Znovu navštivte zásady řízení přístupu pro váš fond SQL a sledujte tato připojení, pokud je počet vysoký|Žádný|
|AdaptiveCacheHitPercent|Adaptivní procento přístupů do mezipaměti|Procento|Maximum|Měří, jak dobře úlohy využívají adaptivní mezipaměti. Pomocí této metriky s metrikou procenta přístupů do mezipaměti určete, zda se má škálovat pro další kapacitu nebo znovu spustit úlohy za účelem hydratace mezipaměti.|Žádný|
|AdaptiveCacheUsedPercent|Adaptivní mezipaměť použité procento|Procento|Maximum|Měří, jak dobře úlohy využívají adaptivní mezipaměti. Pomocí této metriky s metrikou procenta použité v mezipaměti určete, zda se má škálovat pro další kapacitu nebo znovu spustit úlohy za účelem hydratace mezipaměti.|Žádný|
|LocalTempDBUsedPercent|Místní tempdb použité procento|Procento|Maximum|Místní využití databáze tempdb ve všech výpočetních uzlech – hodnoty jsou vydávány každých pět minut|Žádný|
|MemoryUsedPercent|Procento použité paměti|Procento|Maximum|Využití paměti ve všech uzlech ve fondu SQL|Žádný|
|Připojení|Připojení|Počet|Celkem|Počet celkových přihlášení do fondu SQL|Výsledek|
|WLGActiveQueries|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny úloh. Pomocí této metriky nefiltrované a nerozdělené zobrazí všechny aktivní dotazy spuštěné v systému|IsUserDefined,Skupina úloh|
|WLGActiveQueriesČasové výprodejy|Časové limity dotazů skupiny úloh|Počet|Celkem|Dotazy na skupinu úloh, které mají časový čas. Časové limity dotazu hlášené touto metrikou jsou pouze po spuštění dotazu (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání prostředků)|IsUserDefined,Skupina úloh|
|WLGAllocationBySystemPercent|Přidělení skupiny pracovních vytížek podle procent systému|Procento|Maximum|Procentuální rozdělení zdrojů vzhledem k celému systému|IsUserDefined,Skupina úloh|
|WLGAllocationByMaxResourcePercent|Přidělení skupiny pracovních vytížek podle maximálního procenta prostředků|Procento|Maximum|Zobrazí procentuální přidělení zdrojů vzhledem k percentrovému podílu prostředků na skupinu pracovního vytížení. Tato metrika poskytuje efektivní využití skupiny pracovních vytížek|IsUserDefined,Skupina úloh|
|WLGEffectiveCapResourcePercent|Procento prostředků efektivního limitu|Procento|Maximum|Efektivní cap procento prostředků pro skupinu pracovního vytížení. Pokud existují další skupiny úloh s min_percentage_resource > 0, effective_cap_percentage_resource je snížena proporcionálně|IsUserDefined,Skupina úloh|
|wlg_effective_min_resource_percent|Efektivní min zdroj procent|Procento|Minimální|Efektivní nastavení procenta minimálního zdroje povoleno s ohledem na úroveň služby a nastavení skupiny pracovních vytížek. Efektivní min_percentage_resource lze nastavit vyšší na nižších úrovních služeb|IsUserDefined,Skupina úloh|
|Dotazy WLGQueued|Dotazy skupiny úloh ve frontě|Počet|Celkem|Kumulativní počet požadavků zařazených do fronty po dosažení maximálního limitu souběžnosti|IsUserDefined,Skupina úloh|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/prostředí

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Příchozípřijaté zprávy|Příchozí odchozí přenos zpráv|Počet|Celkem|Počet zpráv přečtených ze všech zdrojů událostí Centra událostí nebo IoT hubu|Žádný|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy|Počet|Celkem|Počet neplatných zpráv přečtených ze všech zdrojů událostí Centra událostí nebo IoT hubu|Žádný|
|IngressReceivedBytes|Příchozí přijaté bajty|Bajty|Celkem|Počet bajtů přečtených ze všech zdrojů událostí|Žádný|
|IngressStoredBytes|Příchozí uložené bajty|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádný|
|IngressStoredEvents|Příchozí uložené události|Počet|Celkem|Počet složených událostí, které byly úspěšně zpracovány a jsou k dispozici pro dotaz|Žádný|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazena do fronty ve zdroji událostí, a časem, kdy je zpracována v příchozím přenosu dat|Žádný|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu dat|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy zařazené do fronty ve zdrojovém oddílu události a pořadovým číslem zpráv zpracovávaných v příchozím přenosu dat|Žádný|
|WarmStorageMaxProperties|Maximální vlastnosti teplého úložiště|Počet|Maximum|Maximální počet vlastností povolených prostředím pro skladovou položku S1/S2 a maximální počet vlastností povolených službou Warm Store pro sku PAYG|Žádný|
|Vlastnosti WarmStorageUsedUsed|Teplé úložiště použité vlastnosti |Počet|Maximum|Počet vlastností používaných prostředím pro skladovou položku S1/S2 a počet vlastností používaných službou Warm Store pro sku PAYG|Žádný|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/prostředí/zdroje událostí

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Příchozípřijaté zprávy|Příchozí odchozí přenos zpráv|Počet|Celkem|Počet zpráv přečtených ze zdroje událostí|Žádný|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy|Počet|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádný|
|IngressReceivedBytes|Příchozí přijaté bajty|Bajty|Celkem|Počet bajtů přečtených ze zdroje událostí|Žádný|
|IngressStoredBytes|Příchozí uložené bajty|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádný|
|IngressStoredEvents|Příchozí uložené události|Počet|Celkem|Počet složených událostí, které byly úspěšně zpracovány a jsou k dispozici pro dotaz|Žádný|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazena do fronty ve zdroji událostí, a časem, kdy je zpracována v příchozím přenosu dat|Žádný|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu dat|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy zařazené do fronty ve zdrojovém oddílu události a pořadovým číslem zpráv zpracovávaných v příchozím přenosu dat|Žádný|
|WarmStorageMaxProperties|Maximální vlastnosti teplého úložiště|Počet|Maximum|Maximální počet vlastností povolených prostředím pro skladovou položku S1/S2 a maximální počet vlastností povolených službou Warm Store pro sku PAYG|Žádný|
|Vlastnosti WarmStorageUsedUsed|Teplé úložiště použité vlastnosti |Počet|Maximum|Počet vlastností používaných prostředím pro skladovou položku S1/S2 a počet vlastností používaných službou Warm Store pro sku PAYG|Žádný|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBajtůmPerSecond|Čtení bajtů disku/s|BajtyPerSekunda|Průměr|Průměrná propustnost disku z důvodu operací čtení za ukázkové období.|Žádný|
|DiskWriteBytesPerSecond|Bajty zápisu disku/s|BajtyPerSekunda|Průměr|Průměrná propustnost disku z důvodu zápisu operace za ukázkové období.|Žádný|
|Bajty čtení disku|Bajty čtení disku|Bajty|Celkem|Celková propustnost disku z důvodu operací čtení za ukázkové období.|Žádný|
|Bajty zápisu disku|Bajty zápisu disku|Bajty|Celkem|Celková propustnost disku z důvodu zápisu operace za ukázkové období.|Žádný|
|DiskReadOperations|Operace čtení disku|Počet|Celkem|Počet operací čtení vplatnosti v předchozím ukázkovém období. Všimněte si, že tyto operace mohou mít velikost proměnné velikosti.|Žádný|
|DiskWriteOperations|Operace zápisu na disk|Počet|Celkem|Počet operací zápisu vsazení vpředchozí ukázkové období. Všimněte si, že tyto operace mohou mít velikost proměnné velikosti.|Žádný|
|Operace čtení disku/s|Operace čtení disku/s|CountPerSecond|Průměr|Průměrný počet operací čtení vi v předchozí ukázkové období. Všimněte si, že tyto operace mohou mít velikost proměnné velikosti.|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Průměrný počet operací zápisu vi v předchozím ukázkovém období. Všimněte si, že tyto operace mohou mít velikost proměnné velikosti.|Žádný|
|DiskReadLatency|Latence čtení disku|Milisekund|Průměr|Celková latence čtení. Součet latence čtení zařízení a jádra.|Žádný|
|DiskWriteLatency|Latence zápisu disku|Milisekund|Průměr|Celková latence zápisu. Součet latencí zápisu zařízení a jádra.|Žádný|
|NetworkInBytesPerSecond|V síti v bajtů/s|BajtyPerSekunda|Průměr|Průměrná propustnost sítě pro přijatý provoz.|Žádný|
|NetworkOutBytesPerSecond|Nezletěné bajty sítě/s|BajtyPerSekunda|Průměr|Průměrná propustnost sítě pro přenášený provoz.|Žádný|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádný|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Celková propustnost sítě pro přenášený provoz.|Žádný|
|Využité paměti|Použitá paměť|Bajty|Průměr|Množství paměti počítače, která je používána virtuálním počítačem.|Žádný|
|MemoryGranted|Paměť udělena|Bajty|Průměr|Množství paměti, která byla udělena virtuálnímu zařízení hostitelem. Paměť není udělena hostiteli, dokud se jí nedotknete jednou a udělená paměť může být vyměněna nebo nafouknuta, pokud vmkernel potřebuje paměť.|Žádný|
|MemoryActive|Paměť aktivní|Bajty|Průměr|Množství paměti používané virtuálním virtuálním min v minulém malém časovém období. Toto je "true" číslo, kolik paměti virtuálního zařízení aktuálně potřebuje. Další nevyužité paměti může být vyměněnnebo nafouknutý bez vlivu na výkon hosta.|Žádný|
|Procento CPU|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je hlášena se 100 % představující všechna procesorová jádra v systému. Například dvoucestný virtuální virtuální ms používající 50 % čtyřjádrového systému zcela používá dvě jádra.|Žádný|
|ProcentoCpuReady|Procento připravenosti procesoru|Milisekund|Celkem|Doba připravenosti je čas čekání na procesory, které mají být k dispozici v minulém intervalu aktualizace.|Žádný|

## <a name="microsoftwebserverfarms"></a>Farmy Microsoft.Web/server

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento procesoru|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|Procento paměti|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|Délka fronty disků|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty Http|Počet|Průměr|Délka fronty Http|Instance|
|Přijaté bajty|Data v|Bajty|Celkem|Data v|Instance|
|BajtůOdeslání|Data Out|Bajty|Celkem|Data Out|Instance|
|Protokol TcpSynSent|Protokol TCP Syn odeslán|Počet|Průměr|Protokol TCP Syn odeslán|Instance|
|Protokol TcpSynObdržel|Protokol TCP Syn byl přijat|Počet|Průměr|Protokol TCP Syn byl přijat|Instance|
|TcpEstablished|TCP založena|Počet|Průměr|TCP založena|Instance|
|TcpFinWait1|TCP Fin Čekání 1|Počet|Průměr|TCP Fin Čekání 1|Instance|
|TcpFinWait2|TCP Fin Čekání 2|Počet|Průměr|TCP Fin Čekání 2|Instance|
|TcpClosing|Uzávěrka protokolu TCP|Počet|Průměr|Uzávěrka protokolu TCP|Instance|
|TcpCloseWait|Čekání na zavření protokolu TCP|Počet|Průměr|Čekání na zavření protokolu TCP|Instance|
|TcpLastAck|Poslední potvrzení protokolu TCP|Počet|Průměr|Poslední potvrzení protokolu TCP|Instance|
|TcpTimeWait|Čas čekání protokolu TCP|Počet|Průměr|Čas čekání protokolu TCP|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/weby (kromě funkcí) 

> [!NOTE]
> **Využití systému souborů** je nová metrika, která se zavádí globálně, žádná data se neočekávají, pokud jste nebyli zařazeni na seznam povolených pro privátní náhled.

> [!IMPORTANT]
> **Průměrná doba odezvy** bude zastaralá, aby nedošlo k záměně s agregacemi metrik. Použijte **dobu odezvy** jako náhradu.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProcesorOvý čas|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|Přijaté bajty|Data v|Bajty|Celkem|Data v|Instance|
|BajtůOdeslání|Data Out|Bajty|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|Celkem|Chyby serveru HTTP|Instance|
|Paměťworkingová sada|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|Doba odezvy|Doba odezvy|Sekundy|Celkem|Doba odezvy|Instance|
|Průměrný čas odezvy|Průměrná doba odezvy (zastaralá)|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|Připojení aplikací|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|Soukromé bajty|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Čtení bajtů v iza sekundě|BajtyPerSekunda|Celkem|Čtení bajtů v iza sekundě|Instance|
|IoWriteBytesPerSecond|IO zápis bajtů za sekundu|BajtyPerSekunda|Celkem|IO zápis bajtů za sekundu|Instance|
|IoOtherBytesPerSecond|IO ostatní bajty za sekundu|BajtyPerSekunda|Celkem|IO ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSekunda|Operace čtení vi za sekundu|BajtyPerSekunda|Celkem|Operace čtení vi za sekundu|Instance|
|IoWriteOperationsPerSecond|Operace zápisu vi za sekundu|BajtyPerSekunda|Celkem|Operace zápisu vi za sekundu|Instance|
|IoOtherOperationsPerSecond|Vi další operace za sekundu|BajtyPerSekunda|Celkem|Vi další operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikací|Počet|Průměr|Požadavky ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikací|Počet|Průměr|Celkový počet domén aplikací|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikací|Počet|Průměr|Celkový počet uvolněných domén aplikací|Instance|
|Gen0Kolekce|Gen 0 Uvolňování paměti|Počet|Celkem|Gen 0 Uvolňování paměti|Instance|
|Gen1Kolekce|Gen 1 Uvolňování paměti|Počet|Celkem|Gen 1 Uvolňování paměti|Instance|
|Gen2Kolekce|Gen 2 Uvolňování paměti|Počet|Celkem|Gen 2 Uvolňování paměti|Instance|
|Stav kontroly stavu|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|Využití systému souborů|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/weby (funkce)

> [!NOTE]
> **Využití systému souborů** je nová metrika, která se zavádí globálně, žádná data se neočekávají, pokud jste nebyli zařazeni na seznam povolených pro privátní náhled.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Přijaté bajty|Data v|Bajty|Celkem|Data v|Instance|
|BajtůOdeslání|Data Out|Bajty|Celkem|Data Out|Instance|
|Http5xx|Chyby serveru HTTP|Počet|Celkem|Chyby serveru HTTP|Instance|
|Paměťworkingová sada|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|FunkceExecutionUnits|Jednotky provádění funkcí|MB / Milisekundy|Celkem|[Jednotky provádění funkcí](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|Soukromé bajty|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Čtení bajtů v iza sekundě|BajtyPerSekunda|Celkem|Čtení bajtů v iza sekundě|Instance|
|IoWriteBytesPerSecond|IO zápis bajtů za sekundu|BajtyPerSekunda|Celkem|IO zápis bajtů za sekundu|Instance|
|IoOtherBytesPerSecond|IO ostatní bajty za sekundu|BajtyPerSekunda|Celkem|IO ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSekunda|Operace čtení vi za sekundu|BajtyPerSekunda|Celkem|Operace čtení vi za sekundu|Instance|
|IoWriteOperationsPerSecond|Operace zápisu vi za sekundu|BajtyPerSekunda|Celkem|Operace zápisu vi za sekundu|Instance|
|IoOtherOperationsPerSecond|Vi další operace za sekundu|BajtyPerSekunda|Celkem|Vi další operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikací|Počet|Průměr|Požadavky ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikací|Počet|Průměr|Celkový počet domén aplikací|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikací|Počet|Průměr|Celkový počet uvolněných domén aplikací|Instance|
|Gen0Kolekce|Gen 0 Uvolňování paměti|Počet|Celkem|Gen 0 Uvolňování paměti|Instance|
|Gen1Kolekce|Gen 1 Uvolňování paměti|Počet|Celkem|Gen 1 Uvolňování paměti|Instance|
|Gen2Kolekce|Gen 2 Uvolňování paměti|Počet|Celkem|Gen 2 Uvolňování paměti|Instance|
|Stav kontroly stavu|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|Využití systému souborů|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/weby/sloty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProcesorOvý čas|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|Přijaté bajty|Data v|Bajty|Celkem|Data v|Instance|
|BajtůOdeslání|Data Out|Bajty|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|Celkem|Chyby serveru HTTP|Instance|
|Paměťworkingová sada|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|Průměrný čas odezvy|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|HttpResponseTime|Doba odezvy|Sekundy|Průměr|Doba odezvy|Instance|
|FunkceExecutionUnits|Jednotky provádění funkcí|Počet|Celkem|Jednotky provádění funkcí|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|Připojení aplikací|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|Soukromé bajty|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Čtení bajtů v iza sekundě|BajtyPerSekunda|Celkem|Čtení bajtů v iza sekundě|Instance|
|IoWriteBytesPerSecond|IO zápis bajtů za sekundu|BajtyPerSekunda|Celkem|IO zápis bajtů za sekundu|Instance|
|IoOtherBytesPerSecond|IO ostatní bajty za sekundu|BajtyPerSekunda|Celkem|IO ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSekunda|Operace čtení vi za sekundu|BajtyPerSekunda|Celkem|Operace čtení vi za sekundu|Instance|
|IoWriteOperationsPerSecond|Operace zápisu vi za sekundu|BajtyPerSekunda|Celkem|Operace zápisu vi za sekundu|Instance|
|IoOtherOperationsPerSecond|Vi další operace za sekundu|BajtyPerSekunda|Celkem|Vi další operace za sekundu|Instance|
|RequestsInApplicationQueue|Požadavky ve frontě aplikací|Počet|Průměr|Požadavky ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Počet|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikací|Počet|Průměr|Celkový počet domén aplikací|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikací|Počet|Průměr|Celkový počet uvolněných domén aplikací|Instance|
|Gen0Kolekce|Gen 0 Uvolňování paměti|Počet|Celkem|Gen 0 Uvolňování paměti|Instance|
|Gen1Kolekce|Gen 1 Uvolňování paměti|Počet|Celkem|Gen 1 Uvolňování paměti|Instance|
|Gen2Kolekce|Gen 2 Uvolňování paměti|Počet|Celkem|Gen 2 Uvolňování paměti|Instance|
|Stav kontroly stavu|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|Využití systému souborů|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|Přijaté bajty|Data v|Bajty|Celkem|Data v|Instance|
|BajtůOdeslání|Data Out|Bajty|Celkem|Data Out|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|Http 2xx|Počet|Celkem|Http 2xx|Instance|
|Http3xx|Http 3xx|Počet|Celkem|Http 3xx|Instance|
|Http401|Http 401|Počet|Celkem|Http 401|Instance|
|Http403|Http 403|Počet|Celkem|Http 403|Instance|
|Http404|Http 404|Počet|Celkem|Http 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|Http 4xx|Počet|Celkem|Http 4xx|Instance|
|Http5xx|Chyby serveru HTTP|Počet|Celkem|Chyby serveru HTTP|Instance|
|Průměrný čas odezvy|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|Procento procesoru|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|Procento paměti|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|Délka fronty disků|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty Http|Počet|Průměr|Délka fronty Http|Instance|
|Aktivní požadavky|Aktivní požadavky|Počet|Celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet předních konců|Počet|Průměr|Celkový počet předních konců|Žádný|
|Instance SmallAppServicePlan|Pracovníci plánu malých aplikací|Počet|Průměr|Pracovníci plánu malých aplikací|Žádný|
|Instance MediumAppServicePlan|Pracovníci plánu středních služeb aplikace|Počet|Průměr|Pracovníci plánu středních služeb aplikace|Žádný|
|Instance LargeAppServicePlan|Pracovníci plánu velkých služeb aplikace|Počet|Průměr|Pracovníci plánu velkých služeb aplikace|Žádný|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Celkem pracovníků|Celkový počet pracovníků|Počet|Průměr|Celkový počet pracovníků|Žádný|
|PracovníciK dispozici|Dostupní pracovníci|Počet|Průměr|Dostupní pracovníci|Žádný|
|Použité pracovníky|Ojeté pracovníky|Počet|Průměr|Ojeté pracovníky|Žádný|
|Procento procesoru|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|Procento paměti|Procento paměti|Procento|Průměr|Procento paměti|Instance|
## <a name="next-steps"></a>Další kroky
* [Přečtěte si o metrikách ve Službě Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Export metrik do úložiště, Centra událostí nebo analýzy protokolů](platform-logs-overview.md)

