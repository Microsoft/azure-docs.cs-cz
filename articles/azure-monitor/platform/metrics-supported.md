---
title: Azure Monitor podporované metriky podle typu prostředku
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: f2e3c03ba599128cc4552f64637ebd63efcb4578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128452"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s Azure Monitor

> [!NOTE]
> Tento seznam je z velké části automaticky generovaný z Azure Monitor metrik REST API. Jakékoli změny provedené v tomto seznamu prostřednictvím GitHubu se můžou zapisovat bez upozornění. Podrobnosti o tom, jak provádět trvalé aktualizace, získáte od autora tohoto článku.

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. 

Tento článek je úplný seznam všech metrik platformy (tj. automaticky shromážděných), které jsou aktuálně k dispozici s kanálem konsolidované metriky Azure Monitor. Poslední aktualizace seznamu: březen 27, 2020. Metriky se změnily nebo přidaly po tomto datu se nemusí zobrazit níže. Pokud chcete získat dotaz na seznam metrik a přistupovat k němu programově, použijte prosím [rozhraní API verze 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) .

Jiné metriky mohou být k dispozici na portálu nebo pomocí starších rozhraní API. Metriky pro hostovaný operační systém (hostovaný operační systém), který běží v Azure Virtual Machines, Service Fabric a Cloud Services tady **nejsou uvedené.** Ta musí být shromažďována prostřednictvím jednoho nebo více agentů, kteří se spouštějí v systému nebo jako součást operačního systému. Metriky agenta můžete odeslat do databáze metriky platformy pomocí vlastních rozhraní API [metriky](metrics-custom-overview.md) , které jsou aktuálně ve verzi Public Preview. Pak můžete graf, upozornění a jinak používat metriky operačního systému hosta, jako jsou metriky platforem. Další informace najdete v tématu [Přehled agentů monitorování](agents-overview.md).    

Metriky jsou uspořádány podle oboru názvů. Seznam služeb a obory názvů, které do nich patří, najdete v tématu [poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
> Seznam metrik platforem, které můžete exportovat prostřednictvím nastavení diagnostiky, najdete v [tomto článku](metrics-supported-export-diagnostic-settings.md).


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|private_bytes_metric|Soukromé bajty|Bajty|Průměr|Soukromé bajty.|ServerResourceType|
|virtual_bytes_metric|Virtuální bajty|Bajty|Průměr|Virtuální bajty.|ServerResourceType|
|TotalConnectionRequests|Požadavky na připojení celkem|Počet|Průměr|Celkový počet požadavků na připojení Jedná se o příjem.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|ServerResourceType|
|TotalConnectionFailures|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: aktuální připojení|Počet|Průměr|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: aktuální cena čisticího modulu|Počet|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: velikost čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: nezmenšovaná paměť čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|ServerResourceType|
|MemoryUsage|Paměť: využití paměti|Bajty|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Rovná se čítači Process\PrivateBytes a velikostí dat mapovaných do paměti, přičemž se ignoruje jakákoli paměť, která byla namapována nebo přidělena xVelocitym stroji pro analýzu paměti (VertiPaq), nad rámec limitu paměti stroje xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: limit paměti – pevný|Bajty|Průměr|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|Hodnota memorylimithigh|Paměť: limit paměti – vysoká|Bajty|Průměr|Horní limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: limit paměti – nízká|Bajty|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: limit paměti VertiPaq|Bajty|Průměr|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: kvóta|Bajty|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|ServerResourceType|
|QuotaBlocked|Paměť: kvóta blokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|Bajty|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq stránkované|Bajty|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
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
|mashup_engine_memory_metric|Paměť motoru M|Bajty|Průměr|Využití paměti procesy modulu hybridní webové aplikace|ServerResourceType|
|mashup_engine_private_bytes_metric|Počet privátních bajtů modulu M|Bajty|Průměr|Používání privátních bajtů procesy v hybridních hybridních modulech.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuální bajty motoru M|Bajty|Průměr|Využití virtuálních bajtů procesy modulu hybridní webové aplikace|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Požadavky brány celkem (zastaralé)|Počet|Celkem|Počet požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány (zastaralé)|Počet|Celkem|Počet úspěšných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované žádosti o bránu (zastaralé)|Počet|Celkem|Počet neautorizovaných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|FailedRequests|Neúspěšné požadavky brány (zastaralé)|Počet|Celkem|Počet chyb v žádostech brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory.|Umístění, název hostitele|
|OtherRequests|Jiné požadavky na bránu (zastaralé)|Počet|Celkem|Počet dalších požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|Doba trvání|Celková doba trvání žádostí o bránu|Milisekund|Průměr|Celková doba trvání požadavků brány v milisekundách|Umístění, název hostitele|
|BackendDuration|Doba trvání požadavků back-endu|Milisekund|Průměr|Doba trvání požadavků back-endu v milisekundách|Umístění, název hostitele|
|Kapacita|Kapacita|Procento|Průměr|Metrika využití pro službu ApiManagement|Umístění|
|EventHubTotalEvents|Celkový počet událostí EventHub|Počet|Celkem|Počet událostí odeslaných do centra EventHub|Umístění|
|EventHubSuccessfulEvents|Úspěšné události EventHub|Počet|Celkem|Počet úspěšných událostí EventHub|Umístění|
|EventHubTotalFailedEvents|Neúspěšné události EventHub|Počet|Celkem|Počet neúspěšných událostí EventHub|Umístění|
|EventHubRejectedEvents|Odmítnuté události EventHub|Počet|Celkem|Počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neoprávněná)|Umístění|
|EventHubThrottledEvents|Omezené události EventHub|Počet|Celkem|Počet omezených událostí EventHub|Umístění|
|EventHubTimedoutEvents|Vypršel časový limit událostí EventHub.|Počet|Celkem|Počet vypršení časového limitu událostí EventHub|Umístění|
|EventHubDroppedEvents|Vyřazené události EventHub|Počet|Celkem|Počet vynechaných událostí z důvodu dosažení limitu velikosti fronty|Umístění|
|EventHubTotalBytesSent|Velikost událostí EventHub|Bajty|Celkem|Celková velikost událostí EventHub v bajtech|Umístění|
|Žádosti|Žádosti|Počet|Celkem|Metriky žádostí brány s více dimenzemi|Umístění, název hostitele, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Stav připojení prostředků k síti (Preview)|Počet|Celkem|Stav připojení k síti u typů závislých prostředků od služby API Management|Umístění, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Počet|Počet|Celkový počet příchozích požadavků HTTP.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Počet|Průměr|Latence požadavku HTTP.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procento využití procesoru v systému|Procento|Průměr|Poslední využití CPU pro celý systém|AppName, pod|
|AppCpuUsagePercentage|Procento využití procesoru aplikací|Procento|Průměr|Procento využití CPU App JVM|AppName, pod|
|AppMemoryCommitted|Přiřazená paměť aplikace|Bajty|Průměr|Paměť přiřazená k JVM v bajtech|AppName, pod|
|AppMemoryUsed|Využitá paměť aplikace|Bajty|Průměr|Využitá paměť aplikace v bajtech|AppName, pod|
|AppMemoryMax|Maximální velikost paměti aplikace|Bajty|Maximum|Maximální množství paměti v bajtech, které lze použít pro správu paměti|AppName, pod|
|MaxOldGenMemoryPoolBytes|Maximální velikost dostupných starých dat generace|Bajty|Průměr|Maximální velikost staré generace fondu paměti|AppName, pod|
|OldGenMemoryPoolBytes|Stará velikost dat generace|Bajty|Průměr|Velikost staré generace fondu paměti po úplném GC|AppName, pod|
|OldGenPromotedBytes|Zvýšit úroveň na starou velikost dat generace|Bajty|Maximum|Počet kladných zvýšení velikosti staré paměti generace před GC na po GC|AppName, pod|
|YoungGenPromotedBytes|Zvýšení úrovně na velikost dat malé generace|Bajty|Maximum|Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC na další|AppName, pod|
|GCPauseTotalCount|Počet pozastavení GC|Počet|Celkem|Počet pozastavení GC|AppName, pod|
|GCPauseTotalTime|Celkový čas pozastavení GC|Milisekund|Celkem|Celkový čas pozastavení GC|AppName, pod|
|TomcatSentBytes|Celkový počet odeslaných bajtů Tomcat|Bajty|Celkem|Celkový počet odeslaných bajtů Tomcat|AppName, pod|
|TomcatReceivedBytes|Celkový počet přijatých bajtů Tomcat|Bajty|Celkem|Celkový počet přijatých bajtů Tomcat|AppName, pod|
|TomcatRequestTotalTime|Celkový počet požadavků Tomcat|Milisekund|Celkem|Celkový počet požadavků Tomcat|AppName, pod|
|TomcatRequestTotalCount|Celkový počet žádostí Tomcat|Počet|Celkem|Celkový počet žádostí Tomcat|AppName, pod|
|TomcatResponseAvgTime|Průměrná doba požadavku Tomcat|Milisekund|Průměr|Průměrná doba požadavku Tomcat|AppName, pod|
|TomcatRequestMaxTime|Maximální čas požadavku Tomcat|Milisekund|Maximum|Maximální čas požadavku Tomcat|AppName, pod|
|TomcatErrorCount|Globální chyba Tomcat|Počet|Celkem|Globální chyba Tomcat|AppName, pod|
|TomcatSessionActiveMaxCount|Maximální počet aktivních relací Tomcat|Počet|Celkem|Maximální počet aktivních relací Tomcat|AppName, pod|
|TomcatSessionAliveMaxTime|Maximální doba běhu relace Tomcat|Milisekund|Maximum|Maximální doba běhu relace Tomcat|AppName, pod|
|TomcatSessionActiveCurrentCount|Počet otevřených relací Tomcat|Počet|Celkem|Počet otevřených relací Tomcat|AppName, pod|
|TomcatSessionCreatedCount|Počet vytvořených relací Tomcat|Počet|Celkem|Počet vytvořených relací Tomcat|AppName, pod|
|TomcatSessionExpiredCount|Počet vypršení relace Tomcat|Počet|Celkem|Počet vypršení relace Tomcat|AppName, pod|
|TomcatSessionRejectedCount|Počet odmítnutých relací Tomcat|Počet|Celkem|Počet odmítnutých relací Tomcat|AppName, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Počet|Celkem|Celkový počet úloh|Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet spuštění nasazení aktualizací|Počet|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName, stav|
|TotalUpdateDeploymentMachineRuns|Celkový počet spuštěných počítačů nasazení aktualizace|Počet|Celkem|Celkový počet spuštěných počítačů nasazení aktualizace softwaru v běhu nasazení aktualizace softwaru|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft. Batch/batchAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Vyhrazený počet jader|Počet|Celkem|Celkový počet vyhrazených jader v účtu Batch|Žádná|
|TotalNodeCount|Počet vyhrazených uzlů|Počet|Celkem|Celkový počet vyhrazených uzlů v účtu Batch|Žádná|
|LowPriorityCoreCount|Počet jader LowPriority|Počet|Celkem|Celkový počet jader s nízkou prioritou v účtu Batch|Žádná|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Počet|Celkem|Celkový počet uzlů s nízkou prioritou v účtu Batch|Žádná|
|CreatingNodeCount|Vytváření počtu uzlů|Počet|Celkem|Počet vytvořených uzlů|Žádná|
|StartingNodeCount|Počáteční počet uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádná|
|WaitingForStartTaskNodeCount|Čekání na počet uzlů spouštěcí úlohy|Počet|Celkem|Počet uzlů, které čekají na dokončení počátečního úkolu|Žádná|
|StartTaskFailedNodeCount|Počet neúspěšných spuštění úlohy – počet uzlů|Počet|Celkem|Počet uzlů, ve kterých se spouštěcí úkol nezdařil|Žádná|
|IdleNodeCount|Počet nečinných uzlů|Počet|Celkem|Počet nečinných uzlů|Žádná|
|OfflineNodeCount|Počet uzlů v režimu offline|Počet|Celkem|Počet offline uzlů|Žádná|
|RebootingNodeCount|Restartování počtu uzlů|Počet|Celkem|Počet restartování uzlů|Žádná|
|ReimagingNodeCount|Počet uzlů obnovování imagí|Počet|Celkem|Počet uzlů obnovování imagí|Žádná|
|RunningNodeCount|Počet spuštěných uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádná|
|LeavingPoolNodeCount|Počet ponechávání uzlů fondu|Počet|Celkem|Počet uzlů opouštících fond|Žádná|
|UnusableNodeCount|Počet nepoužitelných uzlů|Počet|Celkem|Počet nepoužitelných uzlů|Žádná|
|PreemptedNodeCount|Počet zrušených uzlů|Počet|Celkem|Počet zrušených uzlů|Žádná|
|TaskStartEvent|Události zahájení úlohy|Počet|Celkem|Celkový počet úloh, které byly spuštěny|poolId, jobId|
|TaskCompleteEvent|Události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly dokončeny|poolId, jobId|
|TaskFailEvent|Události neúspěšných úloh|Počet|Celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|poolId, jobId|
|PoolCreateEvent|Vytváření fondů – události|Počet|Celkem|Celkový počet vytvořených fondů|poolId|
|PoolResizeStartEvent|Události spuštění změny velikosti fondu|Počet|Celkem|Celkový počet změněných počtu fondů, které byly spuštěny|poolId|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Počet|Celkem|Celkový počet změněných velikostí fondu, které byly dokončeny|poolId|
|PoolDeleteStartEvent|Události spuštění odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly spuštěny|poolId|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly dokončeny|poolId|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Počet|Celkem|Celkový počet úspěšně odstraněných úloh.|Úlohy|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k odstranění.|Úlohy|
|JobDisableCompleteEvent|Úloha zakázat kompletní události|Počet|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|Úlohy|
|JobDisableStartEvent|Úloha zakázat počáteční události|Počet|Celkem|Celkový počet úloh, které byly vyžádány k zakázání.|Úlohy|
|JobStartEvent|Události spuštění úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|Úlohy|
|JobTerminateCompleteEvent|Ukončit události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|Úlohy|
|JobTerminateStartEvent|Události spuštění ukončení úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k ukončení.|Úlohy|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úloha odeslána|Úloha odeslána|Počet|Celkem|Počet odeslaných úloh|Scénář, název_clusteru|
|Dokončená úloha|Dokončená úloha|Počet|Celkem|Počet dokončených úloh|Scénář, název_clusteru, ResultType|
|Celkem uzlů|Celkem uzlů|Počet|Průměr|Počet uzlů celkem|Scénář, název_clusteru|
|Aktivní uzly|Aktivní uzly|Počet|Průměr|Počet spuštěných uzlů|Scénář, název_clusteru|
|Nečinné uzly|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů|Scénář, název_clusteru|
|Nepoužité uzly|Nepoužité uzly|Počet|Průměr|Počet nepoužitelných uzlů|Scénář, název_clusteru|
|Přepnuté uzly|Přepnuté uzly|Počet|Průměr|Počet zrušených uzlů|Scénář, název_clusteru|
|Ukončení uzlů|Ukončení uzlů|Počet|Průměr|Počet opouštících uzlů|Scénář, název_clusteru|
|Celkový počet jader|Celkový počet jader|Počet|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Aktivní jádra|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Jádra nečinných|Jádra nečinných|Počet|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nepoužitelné jádra|Nepoužitelné jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Zrušené jádra|Zrušené jádra|Počet|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Vynechávání jader|Vynechávání jader|Počet|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procento využití procesoru|Procento|Maximum|Procento využití procesoru|Node|
|MemoryUsage|Využití paměti|Bajty|Průměr|Využití paměti|Node|
|MemoryLimit|Omezení paměti|Bajty|Průměr|Omezení paměti|Node|
|MemoryUsagePercentageInDouble|Procento využití paměti|Procento|Průměr|Procento využití paměti|Node|
|StorageUsage|Využití úložiště|Bajty|Průměr|Využití úložiště|Node|
|IOReadBytes|Bajty čtení v/v|Bajty|Celkem|Bajty čtení v/v|Node|
|IOWriteBytes|Bajty zápisu v/v|Bajty|Celkem|Bajty zápisu v/v|Node|
|ConnectionAccepted|Přijatá připojení|Počet|Celkem|Přijatá připojení|Node|
|ConnectionHandled|Zpracovaná připojení|Počet|Celkem|Zpracovaná připojení|Node|
|ConnectionActive|Aktivní připojení|Počet|Průměr|Aktivní připojení|Node|
|RequestHandled|Zpracované žádosti|Počet|Celkem|Zpracované žádosti|Node|
|ProcessedBlocks|Zpracované bloky|Počet|Celkem|Zpracované bloky|Node|
|ProcessedTransactions|Zpracované transakce|Počet|Celkem|Zpracované transakce|Node|
|PendingTransactions|Nedokončené transakce|Počet|Průměr|Nedokončené transakce|Node|
|QueuedTransactions|Transakce ve frontě|Počet|Průměr|Transakce ve frontě|Node|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Připojení klienti|Počet|Maximum||ShardId|
|totalcommandsprocessed|Celkem operací|Počet|Celkem||ShardId|
|cachehits|Přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachemissrate|Frekvence neúspěšných přístupů do mezipaměti|Procento|cachemissrate||ShardId|
|GetCommands|Získá|Počet|Celkem||ShardId|
|setcommands|Sady|Počet|Celkem||ShardId|
|operationsPerSecond|Operace za sekundu|Počet|Maximum||ShardId|
|evictedkeys|Vyřazení klíčů|Počet|Celkem||ShardId|
|totalkeys|Celkem klíčů|Počet|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Počet|Celkem||ShardId|
|usedmemory|Využitá paměť|Bajty|Maximum||ShardId|
|usedmemorypercentage|Procento využité paměti|Procento|Maximum||ShardId|
|usedmemoryRss|RSS využité paměti|Bajty|Maximum||ShardId|
|serverLoad|Zatížení serveru|Procento|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Procento|Maximum||ShardId|
|cacheLatency|Mikrosekundy latence mezipaměti (Preview)|Počet|Průměr||ShardId|
|chyby|chyby|Počet|Maximum||ShardId,ErrorType|
|connectedclients0|Připojení klienti (horizontálních oddílů 0)|Počet|Maximum||Žádná|
|totalcommandsprocessed0|Celkem operací (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|getcommands0|Načtení (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|setcommands0|Sady (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Počet|Maximum||Žádná|
|evictedkeys0|Vyřazené klíče (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|totalkeys0|Celkem klíčů (horizontálních oddílů 0)|Počet|Maximum||Žádná|
|expiredkeys0|Klíče vypršení platnosti (horizontálních oddílů 0)|Počet|Celkem||Žádná|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|Bajty|Maximum||Žádná|
|usedmemoryRss0|RSS využité paměti (horizontálních oddílů 0)|Bajty|Maximum||Žádná|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Procento|Maximum||Žádná|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádná|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime0|PROCESOR (horizontálních oddílů 0)|Procento|Maximum||Žádná|
|connectedclients1|Připojení klienti (horizontálních oddílů 1)|Počet|Maximum||Žádná|
|totalcommandsprocessed1|Celkem operací (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|getcommands1|Načtení (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|setcommands1|Sady (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Počet|Maximum||Žádná|
|evictedkeys1|Vyřazené klíče (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|totalkeys1|Celkem klíčů (horizontálních oddílů 1)|Počet|Maximum||Žádná|
|expiredkeys1|Klíče vypršení platnosti (horizontálních oddílů 1)|Počet|Celkem||Žádná|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|Bajty|Maximum||Žádná|
|usedmemoryRss1|RSS využité paměti (horizontálních oddílů 1)|Bajty|Maximum||Žádná|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Procento|Maximum||Žádná|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádná|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime1|PROCESOR (horizontálních oddílů 1)|Procento|Maximum||Žádná|
|connectedclients2|Připojení klienti (horizontálních oddílů 2)|Počet|Maximum||Žádná|
|totalcommandsprocessed2|Celkem operací (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|getcommands2|Get (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|setcommands2|Sady (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Počet|Maximum||Žádná|
|evictedkeys2|Vyřazené klíče (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|totalkeys2|Celkem klíčů (horizontálních oddílů 2)|Počet|Maximum||Žádná|
|expiredkeys2|Klíče vypršení platnosti (horizontálních oddílů 2)|Počet|Celkem||Žádná|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|Bajty|Maximum||Žádná|
|usedmemoryRss2|RSS využité paměti (horizontálních oddílů 2)|Bajty|Maximum||Žádná|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Procento|Maximum||Žádná|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádná|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime2|PROCESOR (horizontálních oddílů 2)|Procento|Maximum||Žádná|
|connectedclients3|Připojení klienti (horizontálních oddílů 3)|Počet|Maximum||Žádná|
|totalcommandsprocessed3|Celkem operací (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|getcommands3|Get (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|setcommands3|Sady (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Počet|Maximum||Žádná|
|evictedkeys3|Vyřazené klíče (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|totalkeys3|Celkem klíčů (horizontálních oddílů 3)|Počet|Maximum||Žádná|
|expiredkeys3|Klíče vypršení platnosti (horizontálních oddílů 3)|Počet|Celkem||Žádná|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|Bajty|Maximum||Žádná|
|usedmemoryRss3|RSS využité paměti (horizontálních oddílů 3)|Bajty|Maximum||Žádná|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Procento|Maximum||Žádná|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádná|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime3|PROCESOR (horizontálních oddílů 3)|Procento|Maximum||Žádná|
|connectedclients4|Připojení klienti (horizontálních oddílů 4)|Počet|Maximum||Žádná|
|totalcommandsprocessed4|Celkem operací (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|getcommands4|Get (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|setcommands4|Sady (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Počet|Maximum||Žádná|
|evictedkeys4|Vyřazené klíče (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|totalkeys4|Celkem klíčů (horizontálních oddílů 4)|Počet|Maximum||Žádná|
|expiredkeys4|Klíče vypršení platnosti (horizontálních oddílů 4)|Počet|Celkem||Žádná|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|Bajty|Maximum||Žádná|
|usedmemoryRss4|RSS využité paměti (horizontálních oddílů 4)|Bajty|Maximum||Žádná|
|serverLoad4|Zatížení serveru (horizontálních oddílů 4)|Procento|Maximum||Žádná|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádná|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime4|PROCESOR (horizontálních oddílů 4)|Procento|Maximum||Žádná|
|connectedclients5|Připojení klienti (horizontálních oddílů 5)|Počet|Maximum||Žádná|
|totalcommandsprocessed5|Celkem operací (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|cachehits5|Přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|getcommands5|Získá (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|setcommands5|Sady (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|operationsPerSecond5|Operací za sekundu (horizontálních oddílů 5)|Počet|Maximum||Žádná|
|evictedkeys5|Vyřazení klíčů (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|totalkeys5|Celkem klíčů (horizontálních oddílů 5)|Počet|Maximum||Žádná|
|expiredkeys5|Klíče vypršení platnosti (horizontálních oddílů 5)|Počet|Celkem||Žádná|
|usedmemory5|Využitá paměť (horizontálních oddílů 5)|Bajty|Maximum||Žádná|
|usedmemoryRss5|RSS využité paměti (horizontálních oddílů 5)|Bajty|Maximum||Žádná|
|serverLoad5|Zatížení serveru (horizontálních oddílů 5)|Procento|Maximum||Žádná|
|cacheWrite5|Zápis do mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádná|
|cacheRead5|Čtení z mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime5|PROCESOR (horizontálních oddílů 5)|Procento|Maximum||Žádná|
|connectedclients6|Připojení klienti (horizontálních oddílů 6)|Počet|Maximum||Žádná|
|totalcommandsprocessed6|Celkem operací (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|cachehits6|Přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|getcommands6|Načtení (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|setcommands6|Sady (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|operationsPerSecond6|Operací za sekundu (horizontálních oddílů 6)|Počet|Maximum||Žádná|
|evictedkeys6|Vyřazené klíče (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|totalkeys6|Celkem klíčů (horizontálních oddílů 6)|Počet|Maximum||Žádná|
|expiredkeys6|Klíče vypršení platnosti (horizontálních oddílů 6)|Počet|Celkem||Žádná|
|usedmemory6|Využitá paměť (horizontálních oddílů 6)|Bajty|Maximum||Žádná|
|usedmemoryRss6|RSS využité paměti (horizontálních oddílů 6)|Bajty|Maximum||Žádná|
|serverLoad6|Zatížení serveru (horizontálních oddílů 6)|Procento|Maximum||Žádná|
|cacheWrite6|Zápis do mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádná|
|cacheRead6|Čtení z mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime6|PROCESOR (horizontálních oddílů 6)|Procento|Maximum||Žádná|
|connectedclients7|Připojení klienti (horizontálních oddílů 7)|Počet|Maximum||Žádná|
|totalcommandsprocessed7|Celkem operací (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|cachehits7|Přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|getcommands7|Načtení (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|setcommands7|Sady (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|operationsPerSecond7|Operací za sekundu (horizontálních oddílů 7)|Počet|Maximum||Žádná|
|evictedkeys7|Vyřazení klíčů (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|totalkeys7|Celkem klíčů (horizontálních oddílů 7)|Počet|Maximum||Žádná|
|expiredkeys7|Klíče vypršení platnosti (horizontálních oddílů 7)|Počet|Celkem||Žádná|
|usedmemory7|Využitá paměť (horizontálních oddílů 7)|Bajty|Maximum||Žádná|
|usedmemoryRss7|RSS využité paměti (horizontálních oddílů 7)|Bajty|Maximum||Žádná|
|serverLoad7|Zatížení serveru (horizontálních oddílů 7)|Procento|Maximum||Žádná|
|cacheWrite7|Zápis do mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádná|
|cacheRead7|Čtení z mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime7|PROCESOR (horizontálních oddílů 7)|Procento|Maximum||Žádná|
|connectedclients8|Připojení klienti (horizontálních oddílů 8)|Počet|Maximum||Žádná|
|totalcommandsprocessed8|Celkem operací (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|cachehits8|Přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|getcommands8|Získá (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|setcommands8|Sady (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|operationsPerSecond8|Operací za sekundu (horizontálních oddílů 8)|Počet|Maximum||Žádná|
|evictedkeys8|Vyřazené klíče (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|totalkeys8|Celkem klíčů (horizontálních oddílů 8)|Počet|Maximum||Žádná|
|expiredkeys8|Klíče vypršení platnosti (horizontálních oddílů 8)|Počet|Celkem||Žádná|
|usedmemory8|Využitá paměť (horizontálních oddílů 8)|Bajty|Maximum||Žádná|
|usedmemoryRss8|RSS využité paměti (horizontálních oddílů 8)|Bajty|Maximum||Žádná|
|serverLoad8|Zatížení serveru (horizontálních oddílů 8)|Procento|Maximum||Žádná|
|cacheWrite8|Zápis do mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádná|
|cacheRead8|Čtení z mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime8|PROCESOR (horizontálních oddílů 8)|Procento|Maximum||Žádná|
|connectedclients9|Připojení klienti (horizontálních oddílů 9)|Počet|Maximum||Žádná|
|totalcommandsprocessed9|Celkem operací (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|cachehits9|Přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|getcommands9|Načtení (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|setcommands9|Sady (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|operationsPerSecond9|Operací za sekundu (horizontálních oddílů 9)|Počet|Maximum||Žádná|
|evictedkeys9|Vyřazené klíče (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|totalkeys9|Celkem klíčů (horizontálních oddílů 9)|Počet|Maximum||Žádná|
|expiredkeys9|Klíče vypršení platnosti (horizontálních oddílů 9)|Počet|Celkem||Žádná|
|usedmemory9|Využitá paměť (horizontálních oddílů 9)|Bajty|Maximum||Žádná|
|usedmemoryRss9|RSS využité paměti (horizontálních oddílů 9)|Bajty|Maximum||Žádná|
|serverLoad9|Zatížení serveru (horizontálních oddílů 9)|Procento|Maximum||Žádná|
|cacheWrite9|Zápis do mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádná|
|cacheRead9|Čtení z mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádná|
|percentProcessorTime9|PROCESOR (horizontálních oddílů 9)|Procento|Maximum||Žádná|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|Žádná|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|Žádná|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádná|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|Žádná|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|Žádná|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádná|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádná|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/sloty/role

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|RoleInstanceId|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|RoleInstanceId|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|RoleInstanceId|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|RoleInstanceId|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|RoleInstanceId|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Využitá kapacita|Bajty|Průměr|Kapacita využitého účtu|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů BLOB|Počet|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů objektů BLOB|Počet|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádná|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita tabulky|Bajty|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádná|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek v Table service účtu úložiště|Žádná|
|TableEntityCount|Počet entit tabulky|Počet|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita souboru|Bajty|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Sdílení souborů|
|FileCount|Počet souborů|Počet|Průměr|Počet souborů v Souborové službě účtu úložiště.|Sdílení souborů|
|FileShareCount|Počet sdílených souborů|Počet|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádná|
|FileShareSnapshotCount|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílení souborů|
|FileShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílení souborů|
|FileShareQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílení souborů|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádná|
|QueueCount|Počet front|Počet|Průměr|Počet front v Služba front účtu úložiště.|Žádná|
|QueueMessageCount|Počet zpráv ve frontě|Počet|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkový počet volání|Počet|Celkem|Celkový počet volání.|ApiName, OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Počet|Celkem|Počet úspěšných volání.|ApiName, OperationName, oblast|
|TotalErrors|Celkový počet chyb|Počet|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName, OperationName, oblast|
|BlockedCalls|Blokovaná volání|Počet|Celkem|Počet volání, která překročily limit nebo kvótu.|ApiName, OperationName, oblast|
|ServerErrors|Chyby serveru|Počet|Celkem|Počet volání s interní chybou služby (kód odpovědi HTTP 5xx).|ApiName, OperationName, oblast|
|ClientErrors|Chyby klienta|Počet|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName, OperationName, oblast|
|DataIn|Data v|Bajty|Celkem|Velikost příchozích dat v bajtech|ApiName, OperationName, oblast|
|Data|Výstupní data|Bajty|Celkem|Velikost odchozích dat v bajtech|ApiName, OperationName, oblast|
|Latence|Latence|Milisekund|Průměr|Latence v milisekundách|ApiName, OperationName, oblast|
|TotalTokenCalls|Celkový počet volání tokenu|Počet|Celkem|Celkový počet volání tokenů|ApiName, OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Počet|Celkem|Celkový počet znaků v příchozím textovém požadavku.|ApiName, OperationName, oblast|
|CharactersTrained|Vyškolené znaky|Počet|Celkem|Celkový počet vyškolených znaků|ApiName, OperationName, oblast|
|SpeechSessionDuration|Doba trvání relace řeči|Sekundy|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName, OperationName, oblast|
|TotalTransactions|Celkový počet transakcí|Počet|Celkem|Celkový počet transakcí|Žádná|
|ProcessedImages|Zpracované bitové kopie|Počet|Celkem| Počet transakcí pro zpracování bitové kopie.|ApiName, vlastnost vlastnosti, kanál, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádná|
|Síťové vstupy|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádná|
|Síťové výstupy|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádná|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádná|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádná|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádná|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádná|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádná|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádná|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)] (Portal-disk-Metrics-deprecation.MD)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádná|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádná|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádná|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádná|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádná|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádná|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádná|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádná|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádná|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádná|
|Celková síť|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádná|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádná|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|VMName|
|Síťové vstupy|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|VMName|
|Síťové výstupy|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|VMName|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|VMName|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|VMName|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|VMName|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|VMName|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádná|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádná|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádná|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádná|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LUN, VMName|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LUN, VMName|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|LUN, VMName|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|LUN, VMName|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|LUN, VMName|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|VMName|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|VMName|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|VMName|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|VMName|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|LUN, VMName|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|LUN, VMName|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|VMName|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|VMName|
|Celková síť|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádná|
|Síťové vstupy|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádná|
|Síťové výstupy|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádná|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádná|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádná|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádná|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádná|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádná|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádná|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádná|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádná|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádná|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádná|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádná|
|Příchozí toky|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádná|
|Odchozí toky|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádná|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádná|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádná|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádná|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádná|
|Celková síť|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádná|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádná|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití procesoru|Počet|Průměr|Využití CPU na všech jádrech v millicores.|containerName|
|MemoryUsage|Využití paměti|Bajty|Průměr|Celkové využití paměti v bajtech|containerName|
|NetworkBytesReceivedPerSecond|Počet přijatých bajtů sítě za sekundu|Bajty|Průměr|Počet přijatých bajtů sítě za sekundu.|Žádná|
|NetworkBytesTransmittedPerSecond|Bajty přenášené přes síť za sekundu|Bajty|Průměr|Počet bajtů přenášených sítí za sekundu.|Žádná|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Celkový počet vyžádané replikace|Počet|Průměr|Celkový počet načtených dat imagí|Žádná|
|SuccessfulPullCount|Počet úspěšných vyžádané replikace|Počet|Průměr|Počet úspěšných stažení imagí|Žádná|
|TotalPushCount|Celkový počet nabízených oznámení|Počet|Průměr|Celkový počet nabízených oznámení imagí|Žádná|
|SuccessfulPushCount|Počet úspěšných vložení|Počet|Průměr|Počet úspěšných vložení imagí|Žádná|
|RunDuration|Doba trvání běhu|Milisekund|Celkem|Doba běhu v milisekundách|Žádná|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Počet|Průměr|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Žádná|
|kube_node_status_allocatable_memory_bytes|Celková velikost dostupné paměti ve spravovaném clusteru|Bajty|Průměr|Celková velikost dostupné paměti ve spravovaném clusteru|Žádná|
|kube_pod_status_ready|Počet lusků ve stavu připraveno|Počet|Průměr|Počet lusků ve stavu připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Počet|Průměr|Stavy pro různé podmínky uzlu|podmínka, stav, status2, uzel|
|kube_pod_status_phase|Počet lusků podle fáze|Počet|Průměr|Počet lusků podle fáze|fáze, obor názvů pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfullRequests|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky vytvořené vlastním poskytovatelem|HttpMethod, CallPath, StatusCode|
|FailedRequests|Neúspěšné požadavky|Počet|Celkem|Získá dostupné protokoly pro vlastní poskytovatele prostředků.|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost čtení (síť)|BytesPerSecond|Průměr|Propustnost čtení síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Propustnost zápisu (síť)|BytesPerSecond|Průměr|Propustnost zápisu síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování do cloudu (sdílení)|BytesPerSecond|Průměr|Propustnost stahování do Azure ze sdílené složky během období generování sestav.|Sdílet|
|CloudUploadThroughputPerShare|Propustnost nahrávání do cloudu (sdílení)|BytesPerSecond|Průměr|Odeslání propustnosti do Azure ze sdílené složky během období generování sestav.|Sdílet|
|BytesUploadedToCloudPerShare|Odeslané bajty v cloudu (sdílená složka)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze sdílené složky během období generování sestav.|Sdílet|
|Úložiště|Celková kapacita|Bajty|Průměr|Celková kapacita|Žádná|
|Availablecapacity;)|Dostupná kapacita|Bajty|Průměr|Dostupná kapacita v bajtech během období generování sestav.|Žádná|
|CloudUploadThroughput|Propustnost nahrávání do cloudu|BytesPerSecond|Průměr|Propustnost nahrávání do cloudu do Azure během období generování sestav.|Žádná|
|CloudReadThroughput|Propustnost stahování v cloudu|BytesPerSecond|Průměr|Propustnost stahování cloudu do Azure během období generování sestav.|Žádná|
|BytesUploadedToCloud|Odeslané bajty v cloudu (zařízení)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze zařízení během období generování sestav.|Žádná|
|HyperVVirtualProcessorUtilization|Výpočetní prostředí Edge – procento využití procesoru|Procento|Průměr|Procento využití procesoru|InstanceName|
|HyperVMemoryUtilization|Výpočet využití paměti na hraničních zařízeních|Procento|Průměr|Velikost využité paměti RAM|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. datacatalog/datacatalogs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuce prostředků podle klasifikace|Počet|Celkem|Označuje počet prostředků s přiřazenou určitou klasifikací, tj. jsou klasifikovány s tímto popiskem.|Klasifikace, zdroj|
|AssetDistributionByStorageType|Distribuce prostředků podle typu úložiště|Počet|Celkem|Označuje počet prostředků s určitým typem úložiště.|StorageType|
|NumberOfAssetsWithClassifications|Počet prostředků s minimálně jednou klasifikací|Počet|Průměr|Určuje počet prostředků s nejméně jednou klasifikací značek.|Žádná|
|ScanCancelled|Kontrola zrušena|Počet|Celkem|Určuje počet zrušených kontrol.|Žádná|
|ScanCompleted|Kontrola dokončena|Počet|Celkem|Určuje počet kontrol, které byly úspěšně dokončeny.|Žádná|
|ScanFailed|Kontrola se nezdařila|Počet|Celkem|Indikuje, že počet kontrol se nezdařil.|Žádná|
|ScanTimeTaken|Doba kontroly provedena|Sekundy|Celkem|Určuje celkovou dobu kontroly v sekundách.|Žádná|
|CatalogActiveUsers|Každodenní aktivní uživatelé|Počet|Celkem|Počet aktivních uživatelů denně|Žádná|
|CatalogUsage|Distribuce využití podle operace|Počet|Celkem|Označuje počet operací, které uživatel operace provede v katalogu, tj. přístup, hledání, Glosář.|Operace|


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
|PipelineCancelledRuns|Zrušené metriky spuštění kanálu|Počet|Celkem||FailureType, název|
|ActivityFailedRuns|Neúspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivitySucceededRuns|Úspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivityCancelledRuns|Zrušené metriky spuštění aktivit|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|TriggerFailedRuns|Neúspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|
|TriggerSucceededRuns|Úspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|
|TriggerCancelledRuns|Zrušené aktivační události spustí metriky|Počet|Celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití procesoru prostředí Integration runtime|Procento|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAvailableMemory|Dostupná paměť modulu runtime integrace|Bajty|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAverageTaskPickupDelay|Doba trvání fronty prostředí Integration runtime|Sekundy|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Délka fronty prostředí Integration runtime|Počet|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Počet dostupných uzlů prostředí Integration runtime|Počet|Průměr||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximální počet povolených entit|Počet|Maximum||Žádná|
|MaxAllowedFactorySizeInGbUnits|Maximální povolená velikost továrny (jednotka GB)|Počet|Maximum||Žádná|
|ResourceCount|Celkový počet entit|Počet|Maximum||Žádná|
|FactorySizeInGbUnits|Celková velikost továrny (jednotka GB)|Počet|Maximum||Žádná|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Počet|Celkem|Počet úspěšných úloh|Žádná|
|JobEndedFailure|Neúspěšné úlohy|Počet|Celkem|Počet neúspěšných úloh|Žádná|
|JobEndedCancelled|Zrušené úlohy|Počet|Celkem|Počet zrušených úloh|Žádná|
|JobAUEndedSuccess|Úspěšná doba AU|Sekundy|Celkem|Celková doba AU pro úspěšné úlohy.|Žádná|
|JobAUEndedFailure|Neúspěšná doba aktualizace AU|Sekundy|Celkem|Celková doba AU pro neúspěšné úlohy.|Žádná|
|JobAUEndedCancelled|Čas AU se zrušil.|Sekundy|Celkem|Celková doba AU pro zrušené úlohy.|Žádná|
|JobStage|Úlohy ve fázi|Počet|Celkem|Počet úloh v každé fázi.|Žádná|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Celkové úložiště|Bajty|Maximum|Celková velikost dat uložených v účtu.|Žádná|
|Napsáno|Zapsaná data|Bajty|Celkem|Celkové množství dat zapsaných na účet.|Žádná|
|Čtení z|Přečtená data|Bajty|Celkem|Celkový objem dat načtených z účtu.|Žádná|
|WriteRequests|Požadavky na zápis|Počet|Celkem|Počet požadavků na zápis dat na účet.|Žádná|
|ReadRequests|Žádosti o čtení|Počet|Celkem|Počet požadavků na čtení dat pro účet.|Žádná|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ShareCount|Odeslané sdílené složky|Počet|Maximum|Počet odeslaných sdílených složek v účtu|ShareName|
|ShareSubscriptionCount|Přijaté sdílené složky|Počet|Maximum|Počet přijatých sdílených složek v účtu|ShareSubscriptionName|
|SucceededShareSynchronizations|Úspěšně se odeslaly snímky s úspěšným sdílením|Počet|Počet|Počet odeslaných snímků s úspěšným sdílením v účtu|Žádná|
|FailedShareSynchronizations|Odeslané neúspěšné snímky sdílené složky|Počet|Počet|Počet odeslaných snímků neúspěšných sdílení v účtu|Žádná|
|SucceededShareSubscriptionSynchronizations|Úspěšně přijaté sdílené snímky|Počet|Počet|Počet přijatých snímků s úspěšným sdílením v účtu|Žádná|
|FailedShareSubscriptionSynchronizations|Přijaté sdílené složky se nepodařilo vytvořit snímky.|Počet|Počet|Počet přijatých snímků neúspěšných sdílení v účtu|Žádná|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádná|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádná|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádná|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádná|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádná|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádná|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádná|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádná|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Průměr|Limit úložiště protokolu serveru|Žádná|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádná|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádná|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Maximum|Prodleva replikace v sekundách|Žádná|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádná|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádná|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádná|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádná|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádná|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádná|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádná|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádná|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádná|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádná|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádná|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádná|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádná|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádná|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Maximum|Prodleva replikace v sekundách|Žádná|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádná|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádná|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádná|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádná|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádná|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádná|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádná|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádná|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádná|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádná|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádná|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádná|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádná|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádná|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádná|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádná|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádná|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v sekundách|Žádná|
|pg_replica_log_delay_in_bytes|Maximální prodleva napříč replikami|Bajty|Maximum|Prodleva z největšího zpoždění repliky v bajtech|Žádná|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádná|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádná|
|IOPS|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|Žádná|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádná|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádná|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádná|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádná|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádná|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádná|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádná|
|IOPS|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|Žádná|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádná|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádná|
|active_connections|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádná|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádná|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádná|
|connections_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádná|
|connections_succeeded|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádná|
|maximum_used_transactionIDs|Maximální počet použitých ID transakcí|Počet|Průměr|Maximální počet použitých ID transakcí|Žádná|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|D2C. telemetrie. příchozí přenos dat allProtocol|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádná|
|D2C. telemetrie. příchozí přenos dat. úspěch|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádná|
|C2D. Commands.. Complete. Complete. Success|Doručení zpráv C2D bylo dokončeno.|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádná|
|C2D. Commands. odchozí. Abandon. Success|Zrušené zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádná|
|C2D. Commands. odchozí. remítat. Success|Odmítnuté zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádná|
|C2DMessagesExpired|C2D zprávy prošly (Preview)|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádná|
|Devices. totalDevices|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádná|
|Devices. connectedDevices. allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádná|
|D2C. telemetrie. odchozí. úspěch|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádná|
|D2C. telemetrie. výstup. vyřazeno|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádná|
|D2C. telemetrie. výstup. osamocený|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádná|
|D2C. telemetrie. invýstup. neplatné|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádná|
|D2C. telemetrie. odchozí. Fallback|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádná|
|D2C. Endpoints. odchozí. eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádná|
|D2C. Endpoints. latence. eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádná|
|D2C. Endpoints. odchozí. serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádná|
|D2C. Endpoints. latence. serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádná|
|D2C. Endpoints. odchozí. serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádná|
|D2C. Endpoints. latence. serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádná|
|D2C. Endpoints. invýstups. builtIns. events|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události).|Žádná|
|D2C. Endpoints. latence. builtIn. events|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události)|Žádná|
|D2C. Endpoints. odchozí úložiště. Storage|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádná|
|D2C. Endpoints. latence. Storage|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádná|
|D2C. Endpoints. invýstups. Storage. bytes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádná|
|D2C. Endpoints. výstup. Storage. BLOBs|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádná|
|EventGridDeliveries|Event Grid doručení (Preview)|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události (https://aka.ms/ioteventgrid).|ResourceId, výsledek, EventType|
|EventGridLatency|Latence Event Grid (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|ResourceId, EventType|
|RoutingDeliveries|Směrování doručení (Preview)|Milisekund|Celkem|Počet pokusů IoT Hub o doručení zpráv do všech koncových bodů pomocí směrování. Chcete-li zobrazit počet úspěšných nebo neúspěšných pokusů, použijte výslednou dimenzi. Chcete-li zobrazit důvod selhání, jako je například neplatný, zrušený nebo osamocený, použijte dimenzi FailureReasonCategory. Můžete také použít dimenze koncový bod a EndpointType, abyste pochopili, kolik zpráv bylo doručeno do různých koncových bodů. Hodnota metriky se u každého pokusu o doručení zvyšuje o jednu, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručí do stejného koncového bodu víckrát.|ResourceId, EndpointType, koncové číslo, FailureReasonCategory, výsledek, RoutingSource|
|RoutingDeliveryLatency|Latence doručení směrování (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozím a příchozím zprávou pro IoT Hub a zprávy telemetrie do koncového bodu. Pomocí dimenzí koncového bodu a EndpointType můžete pochopit latenci různých koncových bodů.|ResourceId, EndpointType, koncový bod, RoutingSource|
|D2C. vláken. Read. Success|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádná|
|D2C. nevlákenný. Read. Failure|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádná|
|D2C. nevlákenný. Read. Size|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádná|
|D2C. nevlákenná. Update. Success|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádná|
|D2C. nevlákenná. aktualizace. selhání|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádná|
|D2C. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádná|
|C2D. Methods. Success|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádná|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádná|
|C2D. Methods. requestSize|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádná|
|C2D. Methods. responseSize|Velikost odezvy volání přímých metod|Bajty|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádná|
|C2D. vláken. Read. Success|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádná|
|C2D. nevlákenný. Read. Failure|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádná|
|C2D. nevlákenný. Read. Size|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádná|
|C2D. nevlákenná. Update. Success|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádná|
|C2D. nevlákenná. aktualizace. selhání|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádná|
|C2D. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádná|
|twinQueries. Success|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádná|
|twinQueries. selhání|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádná|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádná|
|Jobs. createTwinUpdateJob. Success|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádná|
|Jobs. createTwinUpdateJob. selhání|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádná|
|Jobs. createDirectMethodJob. Success|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádná|
|Jobs. createDirectMethodJob. selhání|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádná|
|Jobs. listJobs. Success|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádná|
|Jobs. listJobs. selhání|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádná|
|Jobs. cancelJob. Success|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádná|
|Jobs. cancelJob. selhání|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádná|
|Jobs. queryJobs. Success|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádná|
|Jobs. queryJobs. selhání|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádná|
|dokončené úlohy|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádná|
|úlohy. nezdařilo se|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádná|
|D2C. telemetrie. příchozí přenos dat sendThrottle|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádná|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet celkem aktuálně využívaných zpráv|Žádná|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádná|
|deviceDataUsageV2|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádná|
|totalDeviceCount|Celkem zařízení (Preview)|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádná|
|connectedDeviceCount|Připojená zařízení (Preview)|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádná|
|konfiguračních|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádná|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Počet|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Přiřazená zařízení|Počet|Celkem|Počet zařízení přiřazených ke centru IoT Hub|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Počet|Celkem|Počet pokusů o ověření identity zařízení|ProvisioningServiceName, status, protokol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AddRegion|Přidání oblasti|Počet|Počet|Přidání oblasti|Oblast|
|AvailableStorage|Úložiště k dispozici|Bajty|Celkem|Celkové dostupné úložiště hlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Ukončení připojení Cassandra|Počet|Celkem|Počet uzavřených připojení Cassandra, která se hlásí s členitou úrovní 1 minuty|APIType, oblast, ClosureReason|
|CassandraKeyspaceDelete|Odstraněné místo na Cassandra|Počet|Počet|Odstraněné místo na Cassandra|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|CassandraKeyspaceThroughputUpdate|Propustnost Cassandraho místa na disku se aktualizovala|Počet|Počet|Propustnost Cassandraho místa na disku se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra se aktualizované místo na disku|Počet|Počet|Cassandra se aktualizované místo na disku|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Poplatky za žádosti Cassandra|Počet|Celkem|Ru spotřebované pro vytvořené požadavky Cassandra|APIType, DatabaseName, CollectionName, region, typem operace OperationType, ResourceType|
|CassandraRequests|Žádosti Cassandra|Počet|Počet|Počet provedených požadavků Cassandra|APIType, DatabaseName, CollectionName, region, typem operace OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Tabulka Cassandra se odstranila.|Počet|Počet|Tabulka Cassandra se odstranila.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|CassandraTableThroughputUpdate|Propustnost tabulky Cassandra se aktualizovala.|Počet|Počet|Propustnost tabulky Cassandra se aktualizovala.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Tabulka Cassandra se aktualizovala.|Počet|Počet|Tabulka Cassandra se aktualizovala.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Účet vytvořen|Počet|Počet|Účet vytvořen|Žádná|
|Využití datausage|Využití dat|Bajty|Celkem|Celkové využití dat nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|DeleteAccount|Účet se odstranil.|Počet|Počet|Účet se odstranil.|Žádná|
|DocumentCount|Počet dokumentů|Počet|Celkem|Celkový počet dokumentů hlášených v rozmezí 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Kvóta dokumentu|Bajty|Celkem|Celková kvóta úložiště vykazovaná s členitosti 5 minut|CollectionName, DatabaseName, region|
|GremlinDatabaseDelete|Databáze Gremlin se odstranila.|Počet|Počet|Databáze Gremlin se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|GremlinDatabaseThroughputUpdate|Propustnost databáze Gremlin se aktualizovala|Počet|Počet|Propustnost databáze Gremlin se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Databáze Gremlin se aktualizovala.|Počet|Počet|Databáze Gremlin se aktualizovala.|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Graf Gremlin se odstranil.|Počet|Počet|Graf Gremlin se odstranil.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|GremlinGraphThroughputUpdate|Byla aktualizována propustnost grafu Gremlin|Počet|Počet|Byla aktualizována propustnost grafu Gremlin|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Graf Gremlin se aktualizoval.|Počet|Počet|Graf Gremlin se aktualizoval.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Využití indexu|Bajty|Celkem|Celkové využití indexu nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Žádosti o metadata|Počet|Počet|Počet požadavků na metadata Cosmos DB udržuje shromažďování systémových metadat pro každý účet, což vám umožní vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma.|DatabaseName, CollectionName, region, StatusCode, role|
|MongoCollectionDelete|Kolekce Mongo se odstranila.|Počet|Počet|Kolekce Mongo se odstranila.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|MongoCollectionThroughputUpdate|Byla aktualizována propustnost kolekce Mongo|Počet|Počet|Byla aktualizována propustnost kolekce Mongo|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Kolekce Mongo se aktualizovala|Počet|Počet|Kolekce Mongo se aktualizovala|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Databáze Mongo se aktualizovala.|Počet|Počet|Databáze Mongo se aktualizovala.|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Databáze Mongo se odstranila.|Počet|Počet|Databáze Mongo se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|MongoDatabaseThroughputUpdate|Propustnost databáze Mongo se aktualizovala|Počet|Počet|Propustnost databáze Mongo se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Poplatek za požadavek Mongo|Počet|Celkem|Spotřebované jednotky žádosti Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequests|Žádosti Mongo|Počet|Počet|Počet provedených požadavků Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequestsCount|Počet požadavků Mongo|CountPerSecond|Průměr|Počet požadavků Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsDelete|Frekvence žádosti o odstranění Mongo|CountPerSecond|Průměr|Mongo žádosti o odstranění za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsInsert|Frekvence požadavků na vložení Mongo|CountPerSecond|Průměr|Mongo vložení počtu za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsQuery|Frekvence požadavků na dotaz Mongo|CountPerSecond|Průměr|Požadavek na dotaz Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsUpdate|Frekvence požadavků na aktualizace Mongo|CountPerSecond|Průměr|Žádost o aktualizaci Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|NormalizedRUConsumption|Normalizovaná spotřeba RU|Procento|Maximum|Maximální procento spotřeby v procentech za minutu|CollectionName, DatabaseName, region|
|ProvisionedThroughput|Zřízená propustnost|Počet|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|RegionFailover|Převzetí služeb při selhání oblasti|Počet|Počet|Převzetí služeb při selhání oblasti|Žádná|
|RemoveRegion|Oblast odebrána|Počet|Počet|Oblast odebrána|Oblast|
|ReplicationLatency|Latence replikace p99|Milisekund|Průměr|Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet|SourceRegion,TargetRegion|
|ServerSideLatency|Latence na straně serveru|Milisekund|Průměr|Latence na straně serveru|DatabaseName, CollectionName, region, ConnectionMode, typem operace OperationType, PublicAPIType|
|ServiceAvailability|Dostupnost služby|Procento|Průměr|Dostupnost žádostí o účet v časovém rozmezí jedné hodiny, dne nebo měsíce|Žádná|
|SqlContainerDelete|Kontejner SQL se odstranil.|Počet|Počet|Kontejner SQL se odstranil.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|SqlContainerThroughputUpdate|Propustnost kontejneru SQL se aktualizovala|Počet|Počet|Propustnost kontejneru SQL se aktualizovala|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Kontejner SQL se aktualizoval.|Počet|Počet|Kontejner SQL se aktualizoval.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|Databáze SQL se odstranila|Počet|Počet|Databáze SQL se odstranila|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|SqlDatabaseThroughputUpdate|Propustnost SQL Database se aktualizovala|Počet|Počet|Propustnost SQL Database se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL Database se aktualizovala|Počet|Počet|SQL Database se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|Tabulka Azure se odstranila.|Počet|Počet|Tabulka Azure se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|TableTableThroughputUpdate|Aktualizace propustnosti tabulky Azure|Počet|Počet|Aktualizace propustnosti tabulky Azure|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Tabulka Azure se aktualizovala|Počet|Počet|Tabulka Azure se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Celkový počet jednotek žádostí|Počet|Celkem|Spotřebované jednotky žádosti|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|TotalRequests|Požadavky celkem|Počet|Počet|Počet provedených požadavků|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|UpdateAccountKeys|Klíče účtu se aktualizovaly.|Počet|Počet|Klíče účtu se aktualizovaly.|KeyType|
|UpdateAccountNetworkSettings|Nastavení sítě účtu se aktualizovala.|Počet|Počet|Nastavení sítě účtu se aktualizovala.|Žádná|
|UpdateAccountReplicationSettings|Nastavení replikace účtu se aktualizovala|Počet|Počet|Nastavení replikace účtu se aktualizovala|Žádná|
|UpdateDiagnosticsSettings|Nastavení diagnostiky účtu se aktualizovala|Počet|Počet|Nastavení diagnostiky účtu se aktualizovala|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TransactionCount|Počet transakcí|Počet|Počet|Celkový počet transakcí|TransactionCount|
|SuccessCount|Success Count|Počet|Počet|Počet úspěšných transakcí|SuccessCount|
|FailureCount|Failure Count|Počet|Počet|Počet neúspěšných transakcí|FailureCount|
|SuccessLatency|Latence úspěchu|Milisekund|Průměr|Latence úspěšných transakcí|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádná|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádná|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádná|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádná|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason,EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádná|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádná|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádná|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason,EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádná|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádná|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádná|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádná|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádná|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádná|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|QuotaExceededErrors|Chyby překročení kvóty|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|EntityName, výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub|EntityName|
|IncomingMessages|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub|EntityName|
|OutgoingMessages|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub|EntityName|
|IncomingBytes|Příchozí bajty.|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub|EntityName|
|OutgoingBytes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub|EntityName|
|ActiveConnections|ActiveConnections|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádná|
|ConnectionsOpened|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|ConnectionsClosed|Uzavřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|CaptureBacklog|Zachyťte nevyřízené položky.|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|EntityName|
|CapturedMessages|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub|EntityName|
|CapturedBytes|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub|EntityName|
|Velikost|Velikost|Bajty|Průměr|Velikost centra EventHub v bajtech|EntityName|
|INREQS|Příchozí požadavky (zastaralé)|Počet|Celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů (zastaralé)|Žádná|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádná|
|FAILREQ|Neúspěšné žádosti (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádná|
|SVRBSY|Chyby zaneprázdněnosti serveru (zastaralé)|Počet|Celkem|Celkový počet chyb zaneprázdněných serverem pro obor názvů (zastaralé)|Žádná|
|MEZI sebou|Interní chyby serveru (zastaralé)|Počet|Celkem|Celkový počet interních chyb serveru pro obor názvů (zastaralé)|Žádná|
|MISCERR|Další chyby (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádná|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích zpráv (zastaralé).|Žádná|
|EHINMSGS|Příchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádná|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích zpráv (zastaralé).|Žádná|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádná|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích bajtů (zastaralé).|Žádná|
|EHINBYTES|Příchozí bajty (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádná|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích bajtů (zastaralé).|Žádná|
|EHOUTBYTES|Odchozí bajty (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádná|
|EHABL|Archivovat nevyřízené zprávy (zastaralé)|Počet|Celkem|Archivní zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádná|
|EHAMSGS|Archivní zprávy (zastaralé)|Počet|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)|Žádná|
|EHAMBS|Propustnost zpráv archivu (zastaralé)|Bajty|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádná|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. EventHub|Výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub|Výsledek operace uvnitř|
|QuotaExceededErrors|Chyby překročení kvóty|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|Výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub|Žádná|
|IncomingMessages|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub|Žádná|
|OutgoingMessages|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub|Žádná|
|IncomingBytes|Příchozí bajty.|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub|Žádná|
|OutgoingBytes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub|Žádná|
|ActiveConnections|ActiveConnections|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádná|
|ConnectionsOpened|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|Žádná|
|ConnectionsClosed|Uzavřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|Žádná|
|CaptureBacklog|Zachyťte nevyřízené položky.|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|Žádná|
|CapturedMessages|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub|Žádná|
|CapturedBytes|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub|Žádná|
|Procesor|Procesor|Procento|Maximum|Využití CPU pro cluster centra událostí jako procento|Role|
|AvailableMemory|Paměť k dispozici|Procento|Maximum|Dostupná paměť pro cluster centra událostí jako procento z celkové paměti.|Role|
|Velikost|Velikost centra EventHub v bajtech|Bajty|Průměr|Velikost centra EventHub v bajtech|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Žádosti o bránu|Počet|Celkem|Počet žádostí o bránu|Stavu protokolu http|
|CategorizedGatewayRequests|Zařadit požadavky na bránu|Počet|Celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|Stavu protokolu http|
|NumActiveWorkers|Počet aktivních pracovníků|Počet|Maximum|Počet aktivních pracovníků|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaznamenaná hodnota metriky|Počet|Průměr|Hodnota vypočítaná AutoScale při spuštění|MetricTriggerSource|
|MetricThreshold|Prahová hodnota metriky|Počet|Průměr|Nakonfigurované prahové hodnoty automatického škálování, když se spustilo automatické škálování.|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Počet|Průměr|Kapacita nahlášená pro automatické škálování při jejím spuštění.|Žádná|
|ScaleActionsInitiated|Zahájené akce škálování|Počet|Celkem|Směr operace škálování.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityResult/název, availabilityResult/umístění|
|availabilityResults/Count|Testy dostupnosti|Počet|Počet|Počet testů dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba trvání testu dostupnosti|Milisekund|Průměr|Doba trvání testu dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Doba připojení k síti – načtení stránky|Milisekund|Průměr|Doba mezi požadavkem uživatele a připojením k síti. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádná|
|browserTimings/processingDuration|Doba zpracování klienta|Milisekund|Průměr|Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky.|Žádná|
|browserTimings/receiveDuration|Doba přijetí odezvy|Milisekund|Průměr|Čas mezi prvním a posledním bajtů nebo až do odpojení|Žádná|
|browserTimings/sendDuration|Čas požadavku na odeslání|Milisekund|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádná|
|browserTimings/totalDuration|Doba načítání stránky v prohlížeči|Milisekund|Průměr|Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí.|Žádná|
|závislosti/počet|Volání závislostí|Počet|Počet|Počet volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/doba trvání|Doba trvání závislosti|Milisekund|Průměr|Doba trvání volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/selhání|Selhání volání závislostí|Počet|Počet|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|pageViews/Count|Zobrazení stránek|Počet|Počet|Počet zobrazení stránek|provoz/syntetické, cloudové/roleName|
|pageViews/doba trvání|Doba načítání zobrazení stránky|Milisekund|Průměr|Doba načítání zobrazení stránky|provoz/syntetické, cloudové/roleName|
|Čítače výkonu/requestExecutionTime|Doba provádění požadavku HTTP|Milisekund|Průměr|Čas provedení posledního požadavku.|Cloud/roleInstance|
|Čítače výkonu/requestsInQueue|Požadavky HTTP ve frontě aplikací|Počet|Průměr|Délka fronty požadavků aplikace|Cloud/roleInstance|
|Čítače výkonu/requestsPerSecond|Rychlost požadavku HTTP|CountPerSecond|Průměr|Míra všech požadavků na aplikaci za sekundu z ASP.NET.|Cloud/roleInstance|
|Čítače výkonu/exceptionsPerSecond|Míra výjimek|CountPerSecond|Průměr|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|Cloud/roleInstance|
|Čítače výkonu/processIOBytesPerSecond|Rychlost zpracování v/v|BytesPerSecond|Průměr|Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení.|Cloud/roleInstance|
|Čítače výkonu/processCpuPercentage|PROCESOR procesů|Procento|Průměr|Procentuální hodnota uplynulého času, který všechny podprocesy procesu používají k provádění instrukcí. Může se lišit od 0 do 100. Tato metrika indikuje výkon samotného procesu W3wp.|Cloud/roleInstance|
|Čítače výkonu/processorCpuPercentage|Čas procesoru|Procento|Průměr|Procento času, které procesor stráví v nečinných vláknech|Cloud/roleInstance|
|Čítače výkonu/memoryAvailableBytes|Dostupná paměť|Bajty|Průměr|Fyzická paměť je okamžitě k dispozici pro přidělení procesu nebo pro použití systémem.|Cloud/roleInstance|
|Čítače výkonu/processPrivateBytes|Nesdílené bajty procesu|Bajty|Průměr|Paměť exkluzivně přiřazená k procesům monitorovaných aplikací.|Cloud/roleInstance|
|žádosti/doba trvání|Doba odezvy serveru|Milisekund|Průměr|Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/počet|Žádosti serveru|Počet|Počet|Počet dokončených požadavků HTTP|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/selhání|Neúspěšné požadavky|Počet|Počet|Počet požadavků HTTP označených jako neúspěšné Ve většině případů se jedná o žádosti s kódem odpovědi >= 400 a nerovná se 401.|požadavek/performanceBucket, žádosti/resultCode, požadavky/úspěch, provoz/syntetické, Cloud/roleInstance, Cloud/roleName|
|žádosti/rychlost|Počet požadavků serveru|CountPerSecond|Průměr|Frekvence požadavků serveru za sekundu|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|výjimky/počet|Výjimky|Počet|Počet|Kombinovaný počet všech nezachycených výjimek.|Cloud/roleName, Cloud/roleInstance, klient/typ|
|výjimky/prohlížeč|Výjimky prohlížečů|Počet|Počet|Počet nezachycených výjimek vyvolaných v prohlížeči|klient/server, Cloud/roleName|
|výjimky/Server|Výjimky serveru|Počet|Počet|Počet nezachycených výjimek vyvolaných v serverové aplikaci|klient/server, Cloud/roleName, Cloud/roleInstance|
|trasování/počet|Trasování|Počet|Počet|Počet dokumentů trasování|Trace/severityLevel, provozní/syntetické, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedDeviceCount|Celkem připojených zařízení|Počet|Průměr|Počet zařízení připojených k IoT Central|Žádná|
|C2D. Property. Read. Success|Úspěšné čtení vlastností zařízení z IoT Central|Počet|Celkem|Počet všech úspěšných čtení vlastností zahájených z IoT Central|Žádná|
|C2D. Property. Read. Failure|Neúspěšná čtení vlastností zařízení z IoT Central|Počet|Celkem|Počet všech neúspěšných čtení vlastností zahájených z IoT Central|Žádná|
|D2C. Property. Read. Success|Úspěšná čtení vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných čtení vlastností inicializovaných ze zařízení|Žádná|
|D2C. Property. Read. Failure|Neúspěšná čtení vlastností zařízení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení vlastností inicializovaných ze zařízení|Žádná|
|C2D. Property. Update. Success|Úspěšná aktualizace vlastností zařízení z IoT Central|Počet|Celkem|Počet všech úspěšných aktualizací vlastností zahájených z IoT Central|Žádná|
|C2D. Property. Update. Failure|Neúspěšné aktualizace vlastností zařízení z IoT Central|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností iniciované z IoT Central|Žádná|
|D2C. Property. Update. Success|Úspěšná aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací vlastností inicializovaných ze zařízení|Žádná|
|D2C. Property. Update. Failure|Neúspěšné aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností inicializovaných ze zařízení|Žádná|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkový počet přístupů k rozhraní API služby|Počet|Počet|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|ServiceApiResult|Celkový počet výsledků rozhraní API služby|Počet|Počet|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|SaturationShoebox|Celkové sytosti trezoru|Procento|Průměr|Využitá kapacita trezoru|ActivityType, Activity, TransactionType|
|Dostupnost|Celková dostupnost trezoru|Procento|Průměr|Dostupnost žádostí o trezor|ActivityType, Activity, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Využití mezipaměti|Procento|Průměr|Úroveň využití v oboru clusteru|Žádná|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazů v sekundách|QueryStatus|
|IngestionUtilization|Využití příjmu|Procento|Průměr|Poměr využitých slotů pro přijímání na clusteru|Žádná|
|Udržení|Zachovat naživu|Počet|Průměr|Správnosti check indikuje, že cluster reaguje na dotazy|Žádná|
|IngestionVolumeInMB|Objem příjmu (v MB)|Počet|Celkem|Celkový objem zpracovaných dat do clusteru (v MB)|databáze|
|IngestionLatencyInSeconds|Latence příjmu (v sekundách)|Sekundy|Průměr|Doba ingestování ze zdroje (např. zpráva je v centru EventHub) do clusteru v řádu sekund|Žádná|
|EventsProcessedForEventHubs|Zpracované události (pro Event/centra IoT)|Počet|Celkem|Počet událostí zpracovaných clusterem při příjmu z události nebo IoT Hub|EventStatus|
|IngestionResult|Výsledek ingestování|Počet|Počet|Počet operací ingestování|IngestionResultDetails|
|Procesor|Procesor|Procento|Průměr|Úroveň využití procesoru|Žádná|
|ContinuousExportNumOfRecordsExported|Průběžný export – počet exportovaných záznamů|Počet|Celkem|Počet exportovaných záznamů, které se vyvolaly pro každý artefakt úložiště zapsaný během operace exportu|ContinuousExportName, databáze|
|ExportUtilization|Využití exportu|Procento|Maximum|Využití exportu|Žádná|
|ContinuousExportPendingCount|Počet nevyřízených položek průběžného exportu|Počet|Maximum|Počet probíhajících úloh průběžného exportu připravených k provedení|Žádná|
|ContinuousExportMaxLatenessMinutes|Maximální zpoždění průběžného exportu|Počet|Maximum|Zpoždění (v minutách) hlášené úlohami průběžného exportu v clusteru|Žádná|
|ContinuousExportResult|Výsledek průběžného exportu|Počet|Počet|Indikuje, jestli se průběžný export zdařil nebo selhal.|ContinuousExportName, výsledek, databáze|
|StreamingIngestDuration|Doba ingestování streamování|Milisekund|Průměr|Doba ingestování streamování v milisekundách|Žádná|
|StreamingIngestDataRate|Přenosová rychlost ingestování datových proudů|Počet|Průměr|Přenosová rychlost ingestování datových proudů (MB za sekundu)|Žádná|
|SteamingIngestRequestRate|Rychlost přijímání požadavků pro streamování|Počet|RateRequestsPerSecond|Míra požadavků ingestování datových proudů (počet požadavků za sekundu)|Žádná|
|StreamingIngestResults|Výsledek ingestování streamování|Počet|Průměr|Výsledek ingestování streamování|Výsledek|
|TotalNumberOfConcurrentQueries|Celkový počet souběžných dotazů|Počet|Celkem|Celkový počet souběžných dotazů|Žádná|
|TotalNumberOfThrottledQueries|Celkový počet omezených dotazů|Počet|Celkem|Celkový počet omezených dotazů|Žádná|
|TotalNumberOfThrottledCommands|Celkový počet příkazů s omezením|Počet|Celkem|Celkový počet příkazů s omezením|CommandType|
|TotalNumberOfExtents|Celkový počet rozsahů|Počet|Celkem|Celkový počet rozsahů dat|Žádná|
|InstanceCount|Počet instancí|Počet|Průměr|Celkový počet instancí|Žádná|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádná|
|RunsCompleted|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádná|
|RunsSucceeded|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádná|
|RunsFailed|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádná|
|RunsCancelled|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádná|
|RunLatency|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádná|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádná|
|RunThrottledEvents|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádná|
|RunStartThrottledEvents|Spustit omezené události|Počet|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádná|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádná|
|ActionsStarted|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádná|
|ActionsCompleted|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádná|
|ActionsSucceeded|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádná|
|ActionsFailed|Neúspěšné akce |Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádná|
|ActionsSkipped|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádná|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádná|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádná|
|ActionThrottledEvents|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádná|
|TriggersStarted|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádná|
|TriggersCompleted|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádná|
|TriggersSucceeded|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádná|
|TriggersFailed|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádná|
|TriggersSkipped|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádná|
|TriggersFired|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádná|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádná|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádná|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádná|
|TriggerThrottledEvents|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádná|
|BillableActionExecutions|Fakturovatelné provádění akcí|Počet|Celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádná|
|BillableTriggerExecutions|Fakturovatelná spuštění triggerů|Počet|Celkem|Počet fakturovaných provedení triggerů pracovního postupu|Žádná|
|TotalBillableExecutions|Fakturovatelná spuštění celkem|Počet|Celkem|Počet fakturovaných provedení pracovního postupu|Žádná|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Počet|Celkem|Počet fakturovaných provedení nativních operací|Žádná|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Počet|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádná|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Počet|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádná|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádná|
|RunsCompleted|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádná|
|RunsSucceeded|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádná|
|RunsFailed|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádná|
|RunsCancelled|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádná|
|RunLatency|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádná|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádná|
|RunThrottledEvents|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádná|
|RunStartThrottledEvents|Spustit omezené události|Počet|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádná|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádná|
|ActionsStarted|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádná|
|ActionsCompleted|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádná|
|ActionsSucceeded|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádná|
|ActionsFailed|Neúspěšné akce |Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádná|
|ActionsSkipped|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádná|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádná|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádná|
|ActionThrottledEvents|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádná|
|TriggersStarted|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádná|
|TriggersCompleted|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádná|
|TriggersSucceeded|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádná|
|TriggersFailed|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádná|
|TriggersSkipped|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádná|
|TriggersFired|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádná|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádná|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádná|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádná|
|TriggerThrottledEvents|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádná|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Procento|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádná|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti workflowu pro prostředí integrační služby|Procento|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádná|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí integrační služby|Procento|Průměr|Využití procesoru konektoru pro prostředí integrační služby.|Žádná|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí integrační služby|Procento|Průměr|Využití paměti konektoru pro prostředí integrační služby.|Žádná|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Zrušená spuštění|Zrušená spuštění|Počet|Celkem|Počet zrušených spuštění pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Zrušit požadovaná spuštění|Zrušit požadovaná spuštění|Počet|Celkem|Počet spuštění, kde se pro tento pracovní prostor požádalo o zrušení|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Dokončená spuštění|Dokončená spuštění|Počet|Celkem|Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Neúspěšná spuštění|Neúspěšná spuštění|Počet|Celkem|Počet spuštění se pro tento pracovní prostor nezdařil.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Dokončují se běhy.|Dokončují se běhy.|Počet|Celkem|Počet běhů, které vstoupily do stavu dokončení pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Neodpovídá spuštění|Neodpovídá spuštění|Počet|Celkem|Počet běhů nereagujících na tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nespuštěná spuštění|Nespuštěná spuštění|Počet|Celkem|Počet spuštění v neaktivním stavu pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Připravují se běhy.|Připravují se běhy.|Počet|Celkem|Počet běhů, které se připravují pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštění zřizování|Spuštění zřizování|Počet|Celkem|Počet spuštěných zřizování pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštění ve frontě|Spuštění ve frontě|Počet|Celkem|Počet spuštění zařazených do fronty pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštěná spuštění|Spuštěná spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spouštění spuštění|Spouštění spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|chyby|chyby|Počet|Celkem|Počet chyb spuštění v tomto pracovním prostoru|Scénář|
|Upozornění|Upozornění|Počet|Celkem|Počet upozornění spuštění v tomto pracovním prostoru|Scénář|
|Registr modelu byl úspěšný.|Registr modelu byl úspěšný.|Počet|Celkem|Počet registrací modelů, které byly v tomto pracovním prostoru úspěšně dokončeny|Scénář|
|Nepovedlo se zaregistrovat model|Nepovedlo se zaregistrovat model|Počet|Celkem|Počet registrací modelů, které se v tomto pracovním prostoru nezdařily|Scénář, StatusCode|
|Nasazení modelu spuštěn|Nasazení modelu spuštěn|Počet|Celkem|Počet nasazení modelů spuštěných v tomto pracovním prostoru|Scénář|
|Nasazení modelu bylo úspěšné.|Nasazení modelu bylo úspěšné.|Počet|Celkem|Počet nasazení modelů, která byla v tomto pracovním prostoru úspěšná|Scénář|
|Nasazení modelu se nezdařilo|Nasazení modelu se nezdařilo|Počet|Celkem|Počet nasazení modelů, která v tomto pracovním prostoru selhala|Scénář, StatusCode|
|Celkem uzlů|Celkem uzlů|Počet|Průměr|Počet uzlů celkem Tento součet zahrnuje některé aktivní uzly, nečinné uzly, nepoužité uzly, uzly Premepted a opouští uzly.|Scénář, název_clusteru|
|Aktivní uzly|Aktivní uzly|Počet|Průměr|Počet uzlů Active Jedná se o uzly, které aktivně spouštějí úlohu.|Scénář, název_clusteru|
|Nečinné uzly|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů. Nečinné uzly jsou uzly, které nespouštějí žádné úlohy, ale mohou přijmout novou úlohu, je-li k dispozici.|Scénář, název_clusteru|
|Nepoužité uzly|Nepoužité uzly|Počet|Průměr|Počet nepoužitelných uzlů. Nepoužité uzly nejsou funkční kvůli nějakému problému s nepřeložitelné. Azure bude tyto uzly recyklovat.|Scénář, název_clusteru|
|Přepnuté uzly|Přepnuté uzly|Počet|Průměr|Počet zrušených uzlů. Tyto uzly jsou uzly s nízkou prioritou, které jsou mimo dostupný fond uzlů.|Scénář, název_clusteru|
|Ukončení uzlů|Ukončení uzlů|Počet|Průměr|Počet opouštících uzlů. Ukončení uzlů jsou uzly, které právě dokončí zpracování úlohy a přestanou do stavu nečinnosti.|Scénář, název_clusteru|
|Celkový počet jader|Celkový počet jader|Počet|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Aktivní jádra|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Jádra nečinných|Jádra nečinných|Počet|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nepoužitelné jádra|Nepoužitelné jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Zrušené jádra|Zrušené jádra|Počet|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Vynechávání jader|Vynechávání jader|Počet|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Počet|Průměr|PROCESOR (Preview)|Scénář, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Počet|Průměr|GPU (Náhled)|Scénář, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/Accounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Počet|Počet|Počet volání rozhraní API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostupnost|Dostupnost|Procento|Průměr|Dostupnost rozhraní API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/starají

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech).|OutputFormat|
|SuccessE2ELatency|Koncová latence úspěch|Milisekund|Průměr|Průměrná latence pro úspěšné požadavky v milisekundách.|OutputFormat|
|Žádosti|Žádosti|Počet|Celkem|Požadavky na koncový bod streamování.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetQuota|Kvóta prostředků|Počet|Průměr|Počet povolených prostředků pro aktuální účet Media Service|Žádná|
|AssetCount|Počet assetů|Počet|Průměr|Kolik prostředků již bylo vytvořeno v aktuálním účtu Media Service|Žádná|
|AssetQuotaUsedPercentage|Procento využité kvóty prostředků|Procento|Průměr|Procento využitého prostředku v aktuálním účtu Media Service|Žádná|
|ContentKeyPolicyQuota|Kvóta zásad pro klíč obsahu|Počet|Průměr|Kolik zásad pro klíč obsahu je pro aktuální účet Media Service povolené|Žádná|
|ContentKeyPolicyCount|Počet zásad klíče obsahu|Počet|Průměr|Kolik zásad pro klíč obsahu se už v aktuálním účtu Media Service vytvořilo|Žádná|
|ContentKeyPolicyQuotaUsedPercentage|Procento využité kvóty zásad klíčů obsahu|Procento|Průměr|Procento použitých zásad klíče obsahu v aktuálním účtu Media Service|Žádná|
|StreamingPolicyQuota|Kvóta zásad streamování|Počet|Průměr|Počet povolených zásad streamování pro aktuální účet Media Service|Žádná|
|StreamingPolicyCount|Počet zásad streamování|Počet|Průměr|Kolik zásad streamování už je v aktuálním účtu Media Service vytvořené|Žádná|
|StreamingPolicyQuotaUsedPercentage|Procento využité kvóty zásad streamování|Procento|Průměr|Procento využité zásady streamování v aktuálním účtu Media Service|Žádná|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetsConverted|Převedené prostředky|Počet|Celkem|Celkový počet převedených prostředků|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktivní relace vykreslování|Počet|Celkem|Celkový počet aktivních relací vykreslování|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/svazky

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageReadLatency|Průměrná latence čtení|Milisekund|Průměr|Průměrná latence čtení v milisekundách na operaci|Žádná|
|AverageWriteLatency|Průměrná latence zápisu|Milisekund|Průměr|Průměrná latence zápisu v milisekundách na operaci|Žádná|
|VolumeLogicalSize|Velikost spotřebovaného svazku|Bajty|Průměr|Logická velikost svazku (použité bajty)|Žádná|
|VolumeSnapshotSize|Velikost snímku svazku|Bajty|Průměr|Velikost všech snímků ve svazku|Žádná|
|ReadIops|Čtení IOPS|CountPerSecond|Průměr|Načíst vstupně-výstupní operace za sekundu|Žádná|
|WriteIops|Zápis IOPS|CountPerSecond|Průměr|Zapsat vstupně-výstupní operace za sekundu|Žádná|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Fond přidělený velikosti svazku|Bajty|Průměr|Přidělená velikost fondu, která se používá|Žádná|
|VolumePoolTotalLogicalSize|Velikost spotřebovaného fondu|Bajty|Průměr|Součet logické velikosti všech svazků patřících do fondu|Žádná|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní odeslalo|Žádná|
|BytesReceivedRate|Přijaté bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní přijalo|Žádná|
|PacketsSentRate|Odeslané pakety|Počet|Celkem|Počet paketů, které síťové rozhraní odeslalo|Žádná|
|PacketsReceivedRate|Přijaté pakety|Počet|Celkem|Počet paketů, které síťové rozhraní přijalo|Žádná|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost cesty k datům Load Balancer za dobu trvání|FrontendIPAddress,FrontendPort|
|DipAvailability|Stav sondy stavu|Počet|Průměr|Průměrný stav testu stavu Load Balancer za dobu trvání|Typprotokolu, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Počet|Celkem|Celkový počet odeslaných bajtů v časovém období|FrontendIPAddress, FrontendPort, směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|FrontendIPAddress, FrontendPort, směr|
|SYNCount|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|FrontendIPAddress, FrontendPort, směr|
|SnatConnectionCount|Počet připojení SNAT|Počet|Celkem|Celkový počet nových připojení SNAT vytvořených během časového období|FrontendIPAddress, BackendIPAddress, vlastnost ConnectionState|
|AllocatedSnatPorts|Přidělené porty SNAT (Preview)|Počet|Celkem|Celkový počet portů SNAT přidělených v rámci časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, IsAwaitingRemoval|
|UsedSnatPorts|Využité porty SNAT (Preview)|Počet|Celkem|Celkový počet portů SNAT používaných během časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Počet|Celkem|Počet dotazů poskytovaných pro zónu DNS|Žádná|
|RecordSetCount|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně DNS|Žádná|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádná|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS příchozích paketů|CountPerSecond|Maximum|DDoS příchozích paketů|Žádná|
|PacketsDroppedDDoS|Vynechané příchozí pakety DDoS|CountPerSecond|Maximum|Vynechané příchozí pakety DDoS|Žádná|
|PacketsForwardedDDoS|DDoS předaných příchozích paketů|CountPerSecond|Maximum|DDoS předaných příchozích paketů|Žádná|
|TCPPacketsInDDoS|DDoS příchozí pakety TCP|CountPerSecond|Maximum|DDoS příchozí pakety TCP|Žádná|
|TCPPacketsDroppedDDoS|Zrušené příchozí pakety protokolu TCP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety protokolu TCP DDoS|Žádná|
|TCPPacketsForwardedDDoS|DDoS předaných paketů příchozího protokolu TCP|CountPerSecond|Maximum|DDoS předaných paketů příchozího protokolu TCP|Žádná|
|UDPPacketsInDDoS|Příchozí pakety UDP DDoS|CountPerSecond|Maximum|Příchozí pakety UDP DDoS|Žádná|
|UDPPacketsDroppedDDoS|Zrušené příchozí pakety UDP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety UDP DDoS|Žádná|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předané DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předané DDoS|Žádná|
|BytesInDDoS|Příchozí bajty DDoS|BytesPerSecond|Maximum|Příchozí bajty DDoS|Žádná|
|BytesDroppedDDoS|Příchozí bajty vynechané DDoS|BytesPerSecond|Maximum|Příchozí bajty vynechané DDoS|Žádná|
|BytesForwardedDDoS|Příchozí bajty předané DDoS|BytesPerSecond|Maximum|Příchozí bajty předané DDoS|Žádná|
|TCPBytesInDDoS|Příchozí bajty DDoS TCP|BytesPerSecond|Maximum|Příchozí bajty DDoS TCP|Žádná|
|TCPBytesDroppedDDoS|Příchozí bajty protokolu TCP vyhozené DDoS|BytesPerSecond|Maximum|Příchozí bajty protokolu TCP vyhozené DDoS|Žádná|
|TCPBytesForwardedDDoS|Příchozí DDoS předaných bajtů protokolu TCP|BytesPerSecond|Maximum|Příchozí DDoS předaných bajtů protokolu TCP|Žádná|
|UDPBytesInDDoS|Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP DDoS|Žádná|
|UDPBytesDroppedDDoS|Zrušené Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Zrušené Příchozí bajty UDP DDoS|Žádná|
|UDPBytesForwardedDDoS|Příchozí DDoS předávaných bajtů UDP|BytesPerSecond|Maximum|Příchozí DDoS předávaných bajtů UDP|Žádná|
|IfUnderDDoSAttack|V části útok DDoS nebo ne|Počet|Maximum|V části útok DDoS nebo ne|Žádná|
|DDoSTriggerTCPPackets|Příchozí pakety TCP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci zmírnění DDoS|Žádná|
|DDoSTriggerUDPPackets|Příchozí pakety UDP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci zmírnění DDoS|Žádná|
|DDoSTriggerSYNPackets|Příchozí pakety SYN pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci zmírnění DDoS|Žádná|
|VipAvailability|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost IP adresy za dobu trvání|Port|
|ByteCount|Počet bajtů|Počet|Celkem|Celkový počet odeslaných bajtů v časovém období|Port, směr|
|PacketCount|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|Port, směr|
|SynCount|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|Port, směr|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Čas odezvy na virtuální počítač pomocí příkazů pro odeslání|Milisekund|Průměr|Čas odezvy pro příkazy pro odeslání do cílového virtuálního počítače|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Nepovedlo se odeslat příkazy do virtuálního počítače|Procento|Průměr|Procento počtu neúspěšných příkazů pro odeslání z testu na celkový počet odeslaných příkazů VMM cílového virtuálního počítače|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet volání pravidel aplikace|Počet|Celkem|Počet přístupů k pravidlům aplikace|Stav, důvod, protokol|
|NetworkRuleHit|Počet volání síťových pravidel|Počet|Celkem|Počet přístupů k síťovým pravidlům|Stav, důvod, protokol|
|FirewallHealth|Stav brány firewall|Procento|Průměr|Stav brány firewall|Stav, důvod|
|Zpracováno na zpracování|Zpracovaná data|Bajty|Celkem|Celkový objem dat zpracovaných bránou firewall|Žádná|
|SNATPortUtilization|Využití portu SNAT|Procento|Průměr|Využití portu SNAT|Žádná|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Průměr|Počet bajtů za sekundu, které Application Gateway zasloužily|Žádná|
|UnhealthyHostCount|Počet hostitelů není v pořádku|Počet|Průměr|Počet nezdravých hostitelů back-endu|BackendSettingsPool|
|HealthyHostCount|Počet hostitelů v pořádku|Počet|Průměr|Počet nefunkčních hostitelů back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Počet|Celkem|Počet úspěšných požadavků, které Application Gateway obsluhovány|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Počet požadavků za minutu na hostitele v pořádku|Počet|Průměr|Průměrný počet požadavků za minutu na hostitele back-endu v pořádku ve fondu|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Počet|Celkem|Počet neúspěšných žádostí, které Application Gateway obsluhovány|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Počet|Celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální připojení|Počet|Celkem|Počet aktuálních připojení vytvořených pomocí Application Gateway|Žádná|
|NewConnectionsPerSecond|Nová připojení za sekundu|CountPerSecond|Průměr|Nová připojení za sekundu vytvořená Application Gateway|Žádná|
|CpuUtilization|Využití procesoru|Procento|Průměr|Aktuální využití CPU v Application Gateway|Žádná|
|CapacityUnits|Aktuální jednotky kapacity|Počet|Průměr|Spotřebované jednotky kapacity|Žádná|
|FixedBillableCapacityUnits|Pevné fakturovatelné jednotky kapacity|Počet|Průměr|Minimální jednotky kapacity, které se budou účtovat|Žádná|
|EstimatedBilledCapacityUnits|Odhadované jednotky s rozpisem kapacity|Počet|Průměr|Odhadované jednotky kapacity, které se budou účtovat|Žádná|
|ComputeUnits|Aktuální výpočetní jednotky|Počet|Průměr|Spotřebované výpočetní jednotky|Žádná|
|BackendResponseStatus|Stav odpovědi back-endu|Počet|Celkem|Počet kódů odpovědí HTTP generovaných členy back-end. Nezahrnuje žádné kódy odpovědí vygenerované Application Gateway.|BackendServer, problémových, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protokol TLS klienta|Počet|Celkem|Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil spojení s Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).|Naslouchací proces, TlsProtocol|
|BytesSent|Odeslané bajty|Bajty|Celkem|Celkový počet bajtů odeslaných Application Gateway klientům|Naslouchací proces|
|BytesReceived|Přijaté bajty|Bajty|Celkem|Celkový počet bajtů přijatých Application Gateway od klientů|Naslouchací proces|
|ClientRtt|Čas odezvy klienta|Milisekund|Průměr|Průměrná doba odezvy mezi klienty a Application Gateway. Tato metrika indikuje, jak dlouho trvá navázání připojení a vrácení potvrzení.|Naslouchací proces|
|ApplicationGatewayTotalTime|Čas Application Gateway celkem|Milisekund|Průměr|Průměrná doba, kterou trvá zpracování požadavku, a jeho odpověď k odeslání. Počítá se jako průměr intervalu od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že to obvykle zahrnuje dobu zpracování Application Gateway, čas, po který jsou pakety požadavků a odpovědí přenášeny přes síť, a čas, kdy server back-end trvala odpověď.|Naslouchací proces|
|BackendConnectTime|Čas připojení back-endu|Milisekund|Průměr|Čas strávený navázáním spojení s back-end serverem|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendFirstByteResponseTime|Doba odezvy prvního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a přijetí prvního bajtu hlavičky odpovědi, odhad doby zpracování back-endu serveru|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendLastByteResponseTime|Doba odezvy posledního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a příjem posledního bajtu textu odpovědi|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|MatchedCount|Distribuce všech pravidel v bráně firewall webových aplikací v1|Počet|Celkem|Celkové rozdělení pravidla pro příchozí provoz z firewallu webových aplikací v1|Rules, RuleId|
|BlockedCount|Distribuce pravidla Blokované požadavky firewallu webových aplikací v1|Počet|Celkem|Distribuce pravidla Blokované požadavky firewallu webových aplikací v1|Rules, RuleId|
|BlockedReqCount|Počet blokovaných požadavků firewallu webových aplikací v1|Počet|Celkem|Počet blokovaných požadavků firewallu webových aplikací v1|Žádná|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Šířka pásma S2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádná|
|P2SBandwidth|Šířka pásma P2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádná|
|P2SConnectionCount|Počet připojení P2S|Počet|Maximum|Počet připojení typu Point-to-site brány|Protocol (Protokol)|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Výstupní bajty tunelu|Bajty|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Vstupní bajty tunelového propojení|Bajty|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Odchozí tunelové pakety|Počet|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunelové pakety pro příchozí připojení|Počet|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Odpojení odchozího paketu o neshodě tunelového propojení TS|Počet|Celkem|Počet odchozích odkládacích paketů z výběrů neshody tunelového propojení|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Přetažení paketů neshody TS s tunelem pro příchozí přenosy|Počet|Celkem|Počet odkládacích paketů ze seznamu neshody pro výběr odchozího přenosu tunelu|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Počet|Průměr|Úroveň světla příjmu v dBm|Odkaz, Lane|
|TxLightLevel|TxLightLevel|Počet|Průměr|Úroveň Light tx v dBm|Odkaz, Lane|
|AdminState|AdminState|Počet|Průměr|Stav Správce portu|Odkaz|
|LineProtocol|LineProtocol|Počet|Průměr|Stav protokolu linky portu|Odkaz|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|Odkaz|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|Odkaz|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|PeeredCircuitSKey|
|BgpAvailability|Dostupnost protokolu BGP|Procento|Průměr|Dostupnost protokolu BGP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|ArpAvailability|Dostupnost protokolu ARP|Procento|Průměr|Dostupnost protokolu ARP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Průměr|Počet vyřazených bitů dat za sekundu|Žádná|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Průměr|Výstupní bity zahozených dat za sekundu|Žádná|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|Žádná|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|Žádná|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|Žádná|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|Žádná|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet příchozích přenosů Azure za sekundu v bitech|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|BITS – výstup Azure za sekundu|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle vráceného koncového bodu|Počet|Celkem|Počet vrácených Traffic Managerho koncového bodu v daném časovém rámci|Koncový bod|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu podle koncového bodu|Počet|Maximum|1, pokud je stav testu koncového bodu zapnuto, 0 jinak.|Koncový bod|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% PROBE selhalo|Procento|Průměr|% sond monitorování připojení selhalo.|Žádná|
|AverageRoundtripMs|Průměrná doba odezvy (MS)|Milisekund|Průměr|Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem|Žádná|
|ChecksFailedPercent|Počet neúspěšných kontrol v procentech (Preview)|Procento|Průměr|% kontrol monitorování připojení selhalo.|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Doba odezvy (MS) (Preview)|Milisekund|Průměr|Doba odezvy v milisekundách pro kontroly monitorování připojení|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Počet|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Velikost požadavku|Bajty|Celkem|Počet bajtů odeslaných jako požadavek od klientů na proxy server HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Velikost odpovědi|Bajty|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Fakturovatelná velikost odpovědi|Bajty|Celkem|Počet fakturovatelných bajtů (minimálně 2 KB na požadavek) odeslaných jako odpověď z proxy serveru HTTP/S na klienty.|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Počet|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S do back-endu|Stavu protokolu HTTP, HttpStatusGroup, back-end|
|BackendRequestLatency|Latence žádosti back-endu|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek odeslán proxy HTTP/S do back-endu do back-endu, dokud proxy protokolu HTTP/S nedostalo poslední bajt odpovědi z back-endu|Back-end|
|TotalLatency|Celková latence|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stavu back-endu|Procento|Průměr|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endu|Back-end, problémových|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Počet|Celkem|Počet dotazů poskytovaných pro zónu Privátní DNS|Žádná|
|RecordSetCount|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně Privátní DNS|Žádná|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou Privátní DNS|Žádná|
|VirtualNetworkLinkCount|Virtual Network počet odkazů|Počet|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS|Žádná|
|VirtualNetworkLinkCapacityUtilization|Využití kapacity propojení Virtual Network|Procento|Maximum|Procento kapacity propojení Virtual Network využité zónou Privátní DNS|Žádná|
|VirtualNetworkWithRegistrationLinkCount|Počet odkazů na registraci Virtual Network|Počet|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS s povolenou automatickou registrací|Žádná|
|VirtualNetworkWithRegistrationCapacityUtilization|Využití kapacity odkazu na registraci Virtual Network|Procento|Maximum|Procento Virtual Network propojení s kapacitou automatického registrace využívané Privátní DNS zónou|Žádná|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/obory názvů/NotificationHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registrace. vše|Operace registrace|Počet|Celkem|Počet všech úspěšných operací registrace (vytváření dotazů a odstraňování aktualizací). |Žádná|
|registrace. Create|Operace vytvoření registrace|Počet|Celkem|Počet všech úspěšných vytvoření registrace.|Žádná|
|registrace. aktualizace|Operace aktualizace registrace|Počet|Celkem|Počet všech úspěšných aktualizací registrace.|Žádná|
|registrace. Get|Registrace – operace čtení|Počet|Celkem|Počet všech úspěšných dotazů na registraci.|Žádná|
|registrace. odstranění|Registrace – operace odstranění|Počet|Celkem|Počet všech úspěšných odstranění registrací.|Žádná|
|přijíman|Příchozí zprávy|Počet|Celkem|Počet všech úspěšných volání rozhraní API pro odeslání. |Žádná|
|příchozí. plánováno|Odeslaná naplánovaná nabízená oznámení|Počet|Celkem|Zrušená plánovaná nabízená oznámení|Žádná|
|příchozí. plánováno. Cancel|Zrušená plánovaná nabízená oznámení|Počet|Celkem|Zrušená plánovaná nabízená oznámení|Žádná|
|naplánováno. čeká na|Nedokončená plánovaná oznámení|Počet|Celkem|Nedokončená plánovaná oznámení|Žádná|
|instalace. vše|Operace správy instalace|Počet|Celkem|Operace správy instalace|Žádná|
|instalace. Get|Získat operace instalace|Počet|Celkem|Získat operace instalace|Žádná|
|instalace. Upsert|Operace vytvoření nebo aktualizace instalace|Počet|Celkem|Operace vytvoření nebo aktualizace instalace|Žádná|
|instalace. patch|Operace instalace opravy|Počet|Celkem|Operace instalace opravy|Žádná|
|instalace. odstranit|Odstranit operace instalace|Počet|Celkem|Odstranit operace instalace|Žádná|
|odchozí. allpns. úspěch|Úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádná|
|odchozí. allpns. invalidpayload|Chyby datové části|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS vrátil chybnou datovou část.|Žádná|
|odchozí. allpns. pnserror|Chyby systému externích oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože došlo k potížím při komunikaci s PNS (nezahrnuje problémy s ověřováním).|Žádná|
|odchozí. allpns. channelerror|Chyby kanálu|Počet|Celkem|Počet nabízených oznámení, která selhala, protože kanál byl neplatný, ale není přidružený ke správné aplikaci nebo vypršela její platnost.|Žádná|
|odchozí. allpns. badorexpiredchannel|Chybné nebo prošlé chyby kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršela platnost kanálu/tokenu nebo registrationId, nebo je neplatná.|Žádná|
|odchozí. WNS. úspěch|WNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádná|
|odchozí. WNS. invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované. (Windows Live nerozpoznává přihlašovací údaje.)|Žádná|
|odchozí. WNS. badchannel|Chyba WNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav WNS: 404 nebyl nalezen).|Žádná|
|odchozí. WNS. expiredchannel|Chyba kanálu vypršení platnosti WNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože platnost parametr channeluri vypršela (stav WNS: 410 pryč).|Žádná|
|odchozí. WNS. omezení|WNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože WNS omezuje tuto aplikaci (stav WNS: 406 není přijatelné).|Žádná|
|odchozí. WNS. tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Počet|Celkem|Systém Windows Live není dostupný.|Žádná|
|odchozí. WNS. invalidtoken|WNS – chyby autorizace (neplatný token)|Počet|Celkem|Token poskytnutý pro WNS není platný (stav WNS: 401 Neautorizováno).|Žádná|
|odchozí. WNS. wrongtoken|WNS chyby autorizace (špatný token)|Počet|Celkem|Token poskytnutý pro WNS je platný, ale pro jinou aplikaci (stav WNS: 403 zakázaný). K tomu může dojít, pokud je parametr channeluri v registraci přidružen k jiné aplikaci. Ověřte, že je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádná|
|odchozí. WNS. invalidnotificationformat|WNS neplatný formát oznámení|Počet|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že WNS neumožňuje odmítat všechny neplatné datové části.|Žádná|
|odchozí. WNS. invalidnotificationsize|WNSá Chyba neplatné velikosti oznámení|Počet|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádná|
|odchozí. WNS. channelthrottled|WNS kanál – omezení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-NotificationStatus: channelThrottled).|Žádná|
|odchozí. WNS. channeldisconnected|WNS kanál odpojen|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-DeviceConnectionStatus: odpojeno).|Žádná|
|odchozí. WNS. vyřazeno|WNS Vyřazená oznámení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (X-WNS-NotificationStatus: vyřazeno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádná|
|odchozí. WNS. pnserror|WNS chyby|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s WNS.|Žádná|
|odchozí. WNS. authenticationerror|WNS chyby ověřování|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje nebo chybný token.|Žádná|
|odchozí. APNs. Success|Úspěšná oznámení APNS|Počet|Celkem|Počet všech úspěšných oznámení|Žádná|
|odchozí. APNs. invalidcredentials|Chyby autorizace APNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádná|
|odchozí. APNs. badchannel|Chyba služby APN Bad Channel|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (Stavový kód služby APN: 8).|Žádná|
|odchozí. APNs. expiredchannel|Chyba kanálu vypršení platnosti služby APNS|Počet|Celkem|Počet tokenů, u kterých se zrušila platnost kanálu zpětné vazby APNS.|Žádná|
|odchozí. APNs. invalidnotificationsize|APN – Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (Stavový kód APNS: 7).|Žádná|
|odchozí. APNs. pnserror|Chyby služby APN|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádná|
|odchozí. GCM. úspěch|GCM úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádná|
|odchozí. GCM. invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádná|
|odchozí. GCM. badchannel|Chyba GCM špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci nebyl rozpoznán (výsledek GCM: Neplatná registrace).|Žádná|
|odchozí. GCM. expiredchannel|Chyba kanálu vypršení platnosti GCM|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádná|
|odchozí. GCM. omezení|GCM omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože aplikace GCM omezila tuto aplikaci (kód stavu GCM: 501-599 nebo výsledek: nedostupný)|Žádná|
|odchozí. GCM. invalidnotificationformat|GCM neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část nebyla správně naformátována (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádná|
|odchozí. GCM. invalidnotificationsize|GCMá Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádná|
|odchozí. GCM. wrongchannel|GCM chybná chyba kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružená k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádná|
|odchozí. GCM. pnserror|GCM chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádná|
|odchozí. GCM. authenticationerror|GCM chyby ověřování|Počet|Celkem|Počet nabízených oznámení, která selhala, protože PNS nepřijaly zadané přihlašovací údaje, jsou přihlašovací údaje blokované nebo SenderId není v aplikaci správně nakonfigurovaný (výsledek GCM: MismatchedSenderId).|Žádná|
|odchozí. MPNS. úspěch|MPNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádná|
|odchozí. MPNS. invalidcredentials|Neplatné přihlašovací údaje MPNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádná|
|odchozí. MPNS. badchannel|Chyba MPNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav MPNS: 404 nebyl nalezen).|Žádná|
|odchozí. MPNS. omezení|MPNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 není přijatelné).|Žádná|
|odchozí. MPNS. invalidnotificationformat|MPNS neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část oznámení byla příliš velká.|Žádná|
|odchozí. MPNS. channeldisconnected|MPNS kanál odpojen|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci byl odpojen (stav MPNS: 412 nebyl nalezen).|Žádná|
|odchozí. MPNS. vyřazeno|MPNS Vyřazená oznámení|Počet|Celkem|Počet nabídek, které byly vyřazeny pomocí MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo potlačeno).|Žádná|
|odchozí. MPNS. pnserror|MPNS chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádná|
|odchozí. MPNS. authenticationerror|MPNS chyby ověřování|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádná|
|notificationhub. push|Všechna odchozí oznámení|Počet|Celkem|Všechna odchozí oznámení centra oznámení|Žádná|
|příchozí. All. – žádosti|Všechny příchozí žádosti|Počet|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádná|
|příchozí. All. failedrequests|Všechny příchozí neúspěšné požadavky|Počet|Celkem|Celkový počet příchozích neúspěšných žádostí pro Centrum oznámení|Žádná|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_ uzlů inode% Free|% Bezplatného uzlů inode|Počet|Průměr|Average_ uzlů inode% Free|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Počet|Průměr|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využití uzlů inode|% Použitého uzlů inode|Počet|Průměr|Average_% využití uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využité místo Average_%|% Využitého místa|Počet|Průměr|Využité místo Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přečtených bajtů/s|Bajty čtení z disku/s|Počet|Průměr|Average_Disk přečtených bajtů/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení z disku/s|Počet|Průměr|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosů za sekundu|Přenosy disku/s|Počet|Průměr|Average_Disk přenosů za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bajty zápisu za sekundu|Bajty zápisu na disk/s|Počet|Průměr|Average_Disk bajty zápisu za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Disk za sekundu|Zápisy na disk/s|Počet|Průměr|Zápisy Average_Disk za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Počet|Průměr|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Logical disku za sekundu|Bajty logického disku/s|Počet|Průměr|Počet bajtů Average_Logical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ k dispozici paměti|% Dostupné paměti|Počet|Průměr|Average_ k dispozici paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Počet|Průměr|Average_% dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využitá paměť Average_%|% Využité paměti|Počet|Průměr|Využitá paměť Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využitého místa odkládacího souboru|% Využitého místa odkládacího souboru|Počet|Průměr|Average_% využitého místa odkládacího souboru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Available v MB|Dostupná paměť v MB|Počet|Průměr|Paměť Average_Available v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přeměna Average_Available MB|Dostupný počet MB swap|Počet|Průměr|Přeměna Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page čtení za sekundu|Čtení stránek/s|Počet|Průměr|Average_Page čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Page za sekundu|Zápisy stránek/s|Počet|Průměr|Zápisy Average_Page za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stránky/s|Počet|Průměr|Average_Pages/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB odkládacího prostoru|Využité místo odkládacího souboru v MB|Počet|Průměr|Average_Used MB odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used paměť v MB|Využitá paměť v MB|Počet|Průměr|Average_Used paměť v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přenesené bajty Average_Total|Celkový počet odeslaných bajtů|Počet|Průměr|Přenesené bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Počet|Průměr|Přijaté bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Bajty celkem|Počet|Průměr|Average_Total bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Total pakety|Celkový počet odeslaných paketů|Počet|Průměr|Odeslané Average_Total pakety|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté pakety Average_Total|Celkový počet přijatých paketů|Počet|Průměr|Přijaté pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total chyby příjmu|Celkový počet chyb příjmu|Počet|Průměr|Average_Total chyby příjmu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Chyby při odesílání Average_Total|Chyby odesílání celkem|Počet|Průměr|Chyby při odesílání Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Počet|Průměr|Average_Total kolizí|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/čtení|Střední doba disku/čtení|Počet|Průměr|Average_Avg. doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední doba disku/přenos|Počet|Průměr|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/zápis|Střední doba disku/zápis|Počet|Průměr|Average_Avg. doba disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Physical disku za sekundu|Bajty fyzického disku/s|Počet|Průměr|Počet bajtů Average_Physical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegovaného času|Privilegovaný čas protokolu PCT|Počet|Průměr|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_Pct|Doba uživatele v protokolu PCT|Počet|Průměr|Čas uživatele Average_Pct|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used kB paměti|Využitá paměť v kilobajtech|Počet|Průměr|Average_Used kB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual sdílená paměť|Virtuální sdílená paměť|Počet|Průměr|Average_Virtual sdílená paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času DPC|% Času DPC|Počet|Průměr|Average_% času DPC|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času nečinnosti|% Času nečinnosti|Počet|Průměr|Average_% času nečinnosti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času přerušení|% Času přerušení|Počet|Průměr|Average_% času přerušení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času čekání na v/v|% Času čekání na v/v|Počet|Průměr|Average_% času čekání na v/v|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dobrý čas|% Dobrý čas|Počet|Průměr|Average_% dobrý čas|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegovaného času|% Privilegovaného času|Počet|Průměr|Average_% privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|Procesorový čas v %|Počet|Průměr|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_%|% Uživatelského času|Počet|Průměr|Čas uživatele Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fyzická paměť|Volná fyzická paměť|Počet|Průměr|Average_Free fyzická paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free mezera v stránkovacích souborech|Volné místo ve stránkovacích souborech|Počet|Průměr|Average_Free mezera v stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuální paměti|Volná virtuální paměť|Počet|Průměr|Average_Free virtuální paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Počet|Průměr|Average_Processes|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size uložený ve stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Počet|Průměr|Average_Size uložený ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Doba provozu|Počet|Průměr|Average_Uptime|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Uživatelé|Počet|Průměr|Average_Users|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Počet|Průměr|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostupné MB|Počet|Průměr|Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% používaných potvrzených bajtů|% Používaných potvrzených bajtů|Počet|Průměr|Average_% používaných potvrzených bajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté Average_Bytes za sekundu|Přijaté bajty/s|Počet|Průměr|Přijaté Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Bytes za sekundu|Odeslané bajty/s|Počet|Průměr|Odeslané Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Počet|Průměr|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Počet|Průměr|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Prezenční signál|Prezenční signál|Počet|Celkem|Prezenční signál|Počítač, OSType, verze, SourceComputerId|
|Aktualizace|Aktualizace|Počet|Průměr|Aktualizace|Počítač, produkt, klasifikace, UpdateState, volitelné, schválené|
|Událost|Událost|Počet|Průměr|Událost|Zdroj, protokol událostí, počítač, EventCategory, EventLevel, EventLevelName, ID události|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PrefixLatency|Latence předpony|Milisekund|Průměr|Střední latence předpony|Předpona|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Dostupnost relace v4|Procento|Průměr|Dostupnost relace v4|ConnectionId|
|SessionAvailabilityV6|Dostupnost relace V6|Procento|Průměr|Dostupnost relace V6|ConnectionId|
|IngressTrafficRate|Rychlost příchozího přenosu dat|BitsPerSecond|Průměr|Míra přenosů příchozích dat v bitech za sekundu|ConnectionId|
|EgressTrafficRate|Rychlost přenosů na výstup|BitsPerSecond|Průměr|Rychlost přenosů dat v bitech za sekundu|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazu DAX v posledním intervalu|Žádné dimenze|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|Vysoké využití procesoru|Počet|Celkem|QPU vysoké využití za poslední minutu, 1 pro vysoké využití QPU, jinak 0|Žádné dimenze|
|memory_metric|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-3 GB pro a1, 0-5 GB pro a2, 0-10 GB pro a3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|Žádné dimenze|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuce prostředků podle klasifikace|Počet|Celkem|Označuje počet prostředků s přiřazenou určitou klasifikací, tj. jsou klasifikovány s tímto popiskem.|Klasifikace, zdroj, ResourceId|
|AssetDistributionByStorageType|Distribuce prostředků podle typu úložiště|Počet|Celkem|Označuje počet prostředků s určitým typem úložiště.|StorageType, ResourceId|
|CatalogActiveUsers|Každodenní aktivní uživatelé|Počet|Celkem|Počet aktivních uživatelů denně|ResourceId|
|CatalogUsage|Distribuce využití podle operace|Počet|Celkem|Označuje počet operací, které uživatel operace provede v katalogu, tj. přístup, hledání, Glosář.|Operace, ResourceId|
|NumberOfAssetsWithClassifications|Počet prostředků s minimálně jednou klasifikací|Počet|Průměr|Určuje počet prostředků s nejméně jednou klasifikací značek.|ResourceId|
|ScanCancelled|Kontrola zrušena|Počet|Celkem|Určuje počet zrušených kontrol.|ResourceId|
|ScanCompleted|Kontrola dokončena|Počet|Celkem|Určuje počet kontrol, které byly úspěšně dokončeny.|ResourceId|
|ScanFailed|Kontrola se nezdařila|Počet|Celkem|Indikuje, že počet kontrol se nezdařil.|ResourceId|
|ScanTimeTaken|Doba kontroly provedena|Sekundy|Celkem|Určuje celkovou dobu kontroly v sekundách.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections – úspěch|ListenerConnections – úspěch|Počet|Celkem|Úspěšně se ListenerConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Počet|Celkem|ClientError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Počet|Celkem|ServerError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections – úspěch|SenderConnections – úspěch|Počet|Celkem|Úspěšně se SenderConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|SenderConnections – ClientError|SenderConnections – ClientError|Počet|Celkem|ClientError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections – ServerError|SenderConnections – ServerError|Počet|Celkem|ServerError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Počet|Celkem|Total ListenerConnections for Microsoft. Relay|EntityName|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Počet|Celkem|Požadavky SenderConnections (celkem) pro Microsoft. Relay|EntityName|
|ActiveConnections|ActiveConnections|Počet|Celkem|Total ActiveConnections for Microsoft. Relay|EntityName|
|ActiveListeners|ActiveListeners|Počet|Celkem|Total ActiveListeners for Microsoft. Relay|EntityName|
|BytesTransferred|BytesTransferred|Počet|Celkem|Total BytesTransferred for Microsoft. Relay|EntityName|
|ListenerDisconnects|ListenerDisconnects|Počet|Celkem|Total ListenerDisconnects for Microsoft. Relay|EntityName|
|SenderDisconnects|SenderDisconnects|Počet|Celkem|Total SenderDisconnects for Microsoft. Relay|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence hledání|Sekundy|Průměr|Průměrná latence hledání pro vyhledávací službu|Žádná|
|SearchQueriesPerSecond|Hledání dotazů za sekundu|CountPerSecond|Průměr|Hledání dotazů za sekundu pro vyhledávací službu|Žádná|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Procento|Průměr|Procento vyhledávacích dotazů, které byly pro vyhledávací službu omezené|Žádná|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů|EntityName, výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. ServiceBus|EntityName|
|IncomingMessages|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. ServiceBus|EntityName|
|OutgoingMessages|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. ServiceBus|EntityName|
|ActiveConnections|ActiveConnections|Počet|Celkem|Celkový počet aktivních připojení pro Microsoft. ServiceBus|Žádná|
|ConnectionsOpened|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. ServiceBus|EntityName|
|ConnectionsClosed|Uzavřená připojení.|Počet|Průměr|Uzavřená připojení pro Microsoft. ServiceBus|EntityName|
|Velikost|Velikost|Bajty|Průměr|Velikost fronty nebo tématu v bajtech|EntityName|
|Zprávy|Počet zpráv ve frontě nebo tématu.|Počet|Průměr|Počet zpráv ve frontě nebo tématu.|EntityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu.|Počet|Průměr|Počet aktivních zpráv ve frontě nebo tématu.|EntityName|
|DeadletteredMessages|Počet nedoručených zpráv ve frontě nebo tématu|Počet|Průměr|Počet nedoručených zpráv ve frontě nebo tématu|EntityName|
|ScheduledMessages|Počet naplánovaných zpráv ve frontě nebo tématu.|Počet|Průměr|Počet naplánovaných zpráv ve frontě nebo tématu.|EntityName|
|NamespaceCpuUsage|Procesor|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium.|Replika|
|NamespaceMemoryUsage|Využití paměti|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium|Replika|
|CPUXNS|PROCESOR (zastaralé)|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium. Tato metrika je depricated. Místo toho prosím použijte metriku CPU (NamespaceCpuUsage).|Replika|
|WSXNS|Využití paměti (zastaralé)|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium Tato metrika je zastaralá. Místo toho prosím použijte metriku využití paměti (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikace

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Počet|Průměr|Procesor přidělený tomuto kontejneru v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bajty|Průměr|Paměť přidělená tomuto kontejneru v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Počet|Průměr|Skutečné využití CPU v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bajty|Průměr|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
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
|MessageCount|Počet zpráv|Počet|Celkem|Celková velikost zpráv|Žádná|
|InboundTraffic|Příchozí provoz|Bajty|Celkem|Příchozí provoz služby|Žádná|
|OutboundTraffic|Odchozí provoz|Bajty|Celkem|Odchozí provoz služby|Žádná|
|UserErrors|Chyby uživatele|Procento|Maximum|Procento uživatelských chyb|Žádná|
|SystemErrors|Systémové chyby|Procento|Maximum|Procento systémových chyb|Žádná|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádná|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádná|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v. Neplatí pro datové sklady.|Žádná|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro databáze založené na DTU.|Žádná|
|úložiště|Využité místo pro data|Bajty|Maximum|Využité místo pro data Neplatí pro datové sklady.|Žádná|
|connection_successful|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádná|
|connection_failed|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádná|
|blocked_by_firewall|Blokováno bránou firewall|Počet|Celkem|Blokováno bránou firewall|Žádná|
|ukončení|Zablokování|Počet|Celkem|Zablokování. Neplatí pro datové sklady.|Žádná|
|storage_percent|Procento využitého datového prostoru|Procento|Maximum|Procento využitého datového prostoru Neplatí pro datové sklady nebo databáze na úrovni dat.|Žádná|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti Neplatí pro datové sklady.|Žádná|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů Neplatí pro datové sklady.|Žádná|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací Neplatí pro datové sklady.|Žádná|
|dtu_limit|Limit DTU|Počet|Průměr|Limit DTU. Platí pro databáze založené na DTU.|Žádná|
|dtu_used|Využité DTU|Počet|Průměr|Používá se DTU. Platí pro databáze založené na DTU.|Žádná|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro databáze založené na vCore.|Žádná|
|cpu_used|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro databáze založené na vCore.|Žádná|
|dwu_limit|DWU limit|Počet|Maximum|DWU limit. Platí jenom pro datové sklady.|Žádná|
|dwu_consumption_percent|Procento DWU|Procento|Maximum|Procento DWU Platí jenom pro datové sklady.|Žádná|
|dwu_used|DWU použito|Počet|Maximum|DWU použito. Platí jenom pro datové sklady.|Žádná|
|cache_hit_percent|Procento přístupů do mezipaměti|Procento|Maximum|Procento přístupů do mezipaměti Platí jenom pro datové sklady.|Žádná|
|cache_used_percent|Procento využité mezipaměti|Procento|Maximum|Procento využité mezipaměti Platí jenom pro datové sklady.|Žádná|
|sqlserver_process_core_percent<sup>1</sup> |Základní procento procesu SQL Server|Procento|Maximum|Procento využití procesoru pro proces SQL Server, jak je měřené operačním systémem.|Žádná|
|sqlserver_process_memory_percent<sup>1</sup> |% SQL Server paměti procesu|Procento|Maximum|Procento využití paměti pro proces SQL Server, jak je měřené operačním systémem.|Žádná|
|tempdb_data_size<sup>2</sup> |Velikost datového souboru tempdb v kilobajtech|Počet|Maximum|Velikost datového souboru tempdb v kilobajtech|Žádná|
|tempdb_log_size<sup>2</sup> |Velikost souboru protokolu tempdb v kilobajtech|Počet|Maximum|Velikost souboru protokolu tempdb v kilobajtech|Žádná|
|tempdb_log_used_percent<sup>2</sup> |Použit protokol tempdb v procentech|Procento|Maximum|Byl použit protokol tempdb Percent.|Žádná|
|local_tempdb_usage_percent|Místní procento databáze tempdb|Procento|Průměr|Místní procento databáze tempdb. Platí jenom pro datové sklady.|Žádná|
|app_cpu_billed|CPU aplikace se fakturuje.|Počet|Celkem|CPU aplikace se fakturuje. Platí pro databáze bez serveru.|Žádná|
|app_cpu_percent|Procento využití procesoru aplikací|Procento|Průměr|Procento využití procesoru aplikací Platí pro databáze bez serveru.|Žádná|
|app_memory_percent|Procento paměti aplikace|Procento|Průměr|Procento paměti aplikace. Platí pro databáze bez serveru.|Žádná|
|allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené úložiště dat. Neplatí pro datové sklady.|Žádná|
|memory_usage_percent|Procento paměti|Procento|Maximum|Procento paměti. Platí jenom pro datové sklady.|Žádná|
|dw_backup_size_gb|Velikost úložiště dat|Počet|Celkem|Velikost úložiště dat se skládá z velikosti dat a protokolu transakcí. Metrika se počítá směrem k části úložiště vašeho vyúčtování. Platí jenom pro datové sklady.|Žádná|
|dw_snapshot_size_gb|Velikost úložiště snímků|Počet|Celkem|Velikost úložiště snímků je velikost přírůstkových změn zachycených snímky k vytvoření uživatelem definovaných a automatických bodů obnovení. Metrika se počítá směrem k části úložiště vašeho vyúčtování. Platí jenom pro datové sklady.|Žádná|
|dw_geosnapshot_size_gb|Velikost úložiště pro zotavení po havárii|Počet|Celkem|Velikost úložiště pro zotavení po havárii se ve vašem vyúčtování projeví jako úložiště zotavení po havárii. Platí jenom pro datové sklady.|Žádná|
|wlg_allocation_relative_to_system_percent|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Přidělené procento prostředků relativních k celému systému na skupinu úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Přidělení skupiny úloh podle zakončení prostředků v procentech|Procento|Maximum|Přidělené procento prostředků vzhledem k zadaným prostředkům Cap na skupinu úloh Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Dotazování skupin úloh ve frontě|Počet|Celkem|Dotazy zařazené do fronty v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktivní dotazy|Počet|Celkem|Aktivní dotazy napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádná|
|queued_queries|Dotazy ve frontě|Počet|Celkem|Dotazy ve frontě napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádná|
|wlg_active_queries_timeouts|Vypršení časového limitu dotazu skupiny úloh|Počet|Celkem|Dotazy, jejichž časový limit pro skupinu úloh vypršel. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Efektivní minimální procento prostředků|Procento|Maximum|Minimální procento prostředků rezervovaných a izolovaných pro skupinu úloh s ohledem na minimum úrovně služby. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Procento efektivního Cap prostředku|Procento|Maximum|Pevné omezení procentuální hodnoty prostředků povolených pro skupinu úloh s ohledem na efektivní minimální procento prostředků přidělených pro jiné skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Velikost úložiště pro úplné zálohování|Bajty|Maximum|Celková velikost úložiště pro úplné zálohování Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádná|
|diff_backup_size_bytes|Rozdílová velikost úložiště zálohování|Bajty|Maximum|Kumulativní velikost úložiště rozdílové zálohy. Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádná|
|log_backup_size_bytes|Velikost úložiště zálohy protokolu|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního protokolu. Platí pro databáze založené na vCore a na škálovatelných databázích.|Žádná|
|snapshot_backup_size_bytes|Velikost úložiště záloh snímků|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního snímku. Platí pro databáze s škálovatelným škálováním.|Žádná|
|base_blob_size_bytes|Základní velikost úložiště objektů BLOB|Bajty|Maximum|Základní velikost úložiště objektů BLOB Platí pro databáze s škálovatelným škálováním.|Žádná|

<sup>1</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU. 

<sup>2</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU. Tato metrika není aktuálně k dispozici pro databáze nebo datové sklady ve škálovatelném měřítku.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/servery/elasticPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádná|
|database_cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|DatabaseResourceId|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádná|
|database_physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|DatabaseResourceId|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|Žádná|
|database_log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|DatabaseResourceId|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro elastické fondy založené na DTU.|Žádná|
|database_dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|DatabaseResourceId|
|storage_percent|Procento využitého datového prostoru|Procento|Průměr|Procento využitého datového prostoru|Žádná|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|Žádná|
|database_workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|DatabaseResourceId|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací|Žádná|
|database_sessions_percent|Procento relací|Procento|Průměr|Procento relací|DatabaseResourceId|
|eDTU_limit|limit eDTU|Počet|Průměr|limit eDTU. Platí pro elastické fondy založené na DTU.|Žádná|
|storage_limit|Maximální velikost dat|Bajty|Průměr|Maximální velikost dat|Žádná|
|eDTU_used|využité eDTU|Počet|Průměr|používá se eDTU. Platí pro elastické fondy založené na DTU.|Žádná|
|database_eDTU_used|využité eDTU|Počet|Průměr|využité eDTU|DatabaseResourceId|
|storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|Žádná|
|database_storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|DatabaseResourceId|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti|Žádná|
|cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro elastické fondy založené na vCore.|Žádná|
|database_cpu_limit|Limit procesoru|Počet|Průměr|Limit procesoru|DatabaseResourceId|
|cpu_used|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro elastické fondy založené na vCore.|Žádná|
|database_cpu_used|Využitý procesor|Počet|Průměr|Využitý procesor|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Základní procento procesu SQL Server|Procento|Maximum|Procento využití procesoru pro proces SQL Server, jak je měřené operačním systémem. Platí pro elastické fondy. |Žádná|
|sqlserver_process_memory_percent<sup>1</sup>|% SQL Server paměti procesu|Procento|Maximum|Procento využití paměti pro proces SQL Server, jak je měřené operačním systémem. Platí pro elastické fondy. |Žádná|
|tempdb_data_size<sup>2</sup>|Velikost datového souboru tempdb v kilobajtech|Počet|Maximum|Velikost datového souboru tempdb v kilobajtech|Žádná|
|tempdb_log_size<sup>2</sup>|Velikost souboru protokolu tempdb v kilobajtech|Počet|Maximum|Velikost souboru protokolu tempdb v kilobajtech |Žádná|
|tempdb_log_used_percent<sup>2</sup>|Použit protokol tempdb v procentech|Procento|Maximum|Byl použit protokol tempdb Percent.|Žádná|
|allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|Žádná|
|database_allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|DatabaseResourceId|
|allocated_data_storage_percent|Procentuální hodnota přiděleného datového prostoru|Procento|Maximum|Procentuální hodnota přiděleného datového prostoru|Žádná|

<sup>1</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU. 

<sup>2</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní modely založené na DTU. Tato metrika není aktuálně k dispozici pro databáze s škálovatelnými škálováními.


## <a name="microsoftsqlservers"></a>Microsoft. SQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|ElasticPoolResourceId|
|database_storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Využité DTU|Počet|Průměr|Využité DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Počet|Průměr|Počet virtuálních jader|Žádná|
|avg_cpu_percent|Průměrné procento procesoru|Procento|Průměr|Průměrné procento procesoru|Žádná|
|reserved_storage_mb|Rezervované místo v úložišti|Počet|Průměr|Rezervované místo v úložišti|Žádná|
|storage_space_used_mb|Využité místo úložiště|Počet|Průměr|Využité místo úložiště|Žádná|
|io_requests|Počet požadavků v/v|Počet|Průměr|Počet požadavků v/v|Žádná|
|io_bytes_read|Přečtené vstupně-výstupní bajty|Bajty|Průměr|Přečtené vstupně-výstupní bajty|Žádná|
|io_bytes_written|Zapsané vstupně-výstupní bajty|Bajty|Průměr|Zapsané vstupně-výstupní bajty|Žádná|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Využitá kapacita|Bajty|Průměr|Kapacita využitého účtu|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů BLOB|Počet|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů objektů BLOB|Počet|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádná|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita tabulky|Bajty|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádná|
|TableCount|Počet tabulek|Počet|Průměr|Počet tabulek v Table service účtu úložiště|Žádná|
|TableEntityCount|Počet entit tabulky|Počet|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita souboru|Bajty|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Sdílení souborů|
|FileCount|Počet souborů|Počet|Průměr|Počet souborů v Souborové službě účtu úložiště.|Sdílení souborů|
|FileShareCount|Počet sdílených souborů|Počet|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádná|
|FileShareSnapshotCount|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílení souborů|
|FileShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílení souborů|
|FileShareQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílení souborů|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádná|
|QueueCount|Počet front|Počet|Průměr|Počet front v Služba front účtu úložiště.|Žádná|
|QueueMessageCount|Počet zpráv ve frontě|Počet|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádná|
|Transakce|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/caches

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ClientIOPS|Celkový počet vstupně-výstupních operací klienta|Počet|Průměr|Frekvence operací souborů klienta zpracovávaných mezipamětí.|Žádná|
|ClientLatency|Průměrná latence klienta|Milisekund|Průměr|Průměrná latence operací s klientskými soubory do mezipaměti úložiště|Žádná|
|ClientReadIOPS|Klientský vstupně-výstupní operace čtení|CountPerSecond|Průměr|Klientské operace čtení za sekundu.|Žádná|
|ClientReadThroughput|Průměrná propustnost čtení mezipaměti|BytesPerSecond|Průměr|Rychlost přenosu dat čtení klienta.|Žádná|
|ClientWriteIOPS|Zápis klientského IOPS za sekundu|CountPerSecond|Průměr|Operace zápisu klienta za sekundu|Žádná|
|ClientWriteThroughput|Průměrná propustnost zápisu mezipaměti|BytesPerSecond|Průměr|Přenosová rychlost zápisu dat klienta.|Žádná|
|ClientMetadataReadIOPS|Metadata klienta přečetla IOPS|CountPerSecond|Průměr|Frekvence operací s klientskými soubory odesílanými do mezipaměti s výjimkou čtení dat, která nemění trvalý stav.|Žádná|
|ClientMetadataWriteIOPS|Zápis metadat klienta za sekundu|CountPerSecond|Průměr|Počet operací klientského souboru odeslaných do mezipaměti s výjimkou zápisů dat, které upravují trvalý stav.|Žádná|
|ClientLockIOPS|IOPS zámku klienta|CountPerSecond|Průměr|Operace uzamčení souborů klienta za sekundu|Žádná|
|StorageTargetHealth|Stav cíle úložiště|Počet|Průměr|Logické výsledky testu připojení mezi cíli mezipaměti a úložištěm.|Žádná|
|Doba provozu|Doba provozu|Počet|Průměr|Logické výsledky testu připojení mezi mezipamětí a systémem monitorování.|Žádná|
|StorageTargetIOPS|Celkem StorageTarget IOPS|Počet|Průměr|Frekvence všech operací se soubory, které mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget zápis IOPS|Počet|Průměr|Frekvence operací zápisu souborů, které mezipaměť odesílá do konkrétní StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Propustnost asynchronního zápisu StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť asynchronně zapisuje data do konkrétního StorageTarget. Jedná se o příležitostné zápisy, které nezpůsobují klientům blokování.|StorageTarget|
|StorageTargetSyncWriteThroughput|Propustnost synchronního zápisu StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť synchronně zapisuje data do konkrétního StorageTarget. Jedná se o zápisy, které způsobují blokování klientů.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget celkové propustnosti zápisu|BytesPerSecond|Průměr|Celková míra, kterou mezipaměť zapisuje data do konkrétního StorageTarget.|StorageTarget|
|StorageTargetLatency|Latence StorageTarget|Milisekund|Průměr|Průměrná latence odezvy všech operací se soubory, kterou mezipaměť odesílá na partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget metadata Read IOPS|CountPerSecond|Průměr|Frekvence operací se soubory, které nemění trvalý stav a s výjimkou operace čtení, kterou mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Zápisy metadat StorageTarget IOPS|CountPerSecond|Průměr|Frekvence operací se soubory, které upravují trvalý stav a s výjimkou operace zápisu, kterou mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget čtení IOPS|CountPerSecond|Průměr|Míra operací čtení souborů, které mezipaměť odesílá do konkrétního StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Propustnost před čtením StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť opportunisticly čte data z StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Propustnost výplně StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť čte data z StorageTarget a zpracovává tak Neúspěšné přístupy do mezipaměti|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget celkové propustnosti čtení|BytesPerSecond|Průměr|Celková míra, kterou mezipaměť čte data z konkrétní StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Výsledek relace synchronizace|Počet|Průměr|Metrika, která protokoluje hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Počet|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Velikost odvolání při vyvolání vrstvy cloudu|Bajty|Celkem|Velikost vrácených dat|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Propustnost volání při navracení cloudových vrstev|BytesPerSecond|Průměr|Velikost propustnosti odvolání dat|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Velikost odvolání při vrstvení cloudu podle aplikace|Bajty|Celkem|Velikost dat vrácených aplikací|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerHeartbeat|Online stav serveru|Počet|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|Využití SU%|Procento|Maximum|Využití SU%|ID logického, PartitionId|
|InputEvents|Události vstupu|Počet|Celkem|Události vstupu|ID logického, PartitionId|
|InputEventBytes|Bajty vstupních událostí|Bajty|Celkem|Bajty vstupních událostí|ID logického, PartitionId|
|LateInputEvents|Zpožděné vstupní události|Počet|Celkem|Zpožděné vstupní události|ID logického, PartitionId|
|OutputEvents|Výstupní události|Počet|Celkem|Výstupní události|ID logického, PartitionId|
|ConversionErrors|Chyby převodu dat|Počet|Celkem|Chyby převodu dat|ID logického, PartitionId|
|chyby|Běhové chyby|Počet|Celkem|Běhové chyby|ID logického, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Počet|Celkem|Události mimo pořadí|ID logického, PartitionId|
|AMLCalloutRequests|Žádosti o funkce|Počet|Celkem|Žádosti o funkce|ID logického, PartitionId|
|AMLCalloutFailedRequests|Neúspěšné žádosti o funkce|Počet|Celkem|Neúspěšné žádosti o funkce|ID logického, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Počet|Celkem|Události funkcí|ID logického, PartitionId|
|DeserializationError|Chyby při deserializaci vstupu|Počet|Celkem|Chyby při deserializaci vstupu|ID logického, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Počet|Celkem|Události předčasného vstupu|ID logického, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|ID logického, PartitionId|
|InputEventsSourcesBacklogged|Nevyřízené události vstupu|Počet|Maximum|Nevyřízené události vstupu|ID logického, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Počet|Celkem|Přijaté vstupní zdroje|ID logického, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Běh kanálu skončil.|Počet|Celkem|Počet úspěšných spuštění kanálu orchestrace, selhání nebo zrušení|Výsledek, FailureType, kanál|
|OrchestrationActivityRunsEnded|Běh aktivity skončil.|Počet|Celkem|Počet zrušených aktivit orchestrace, selhání nebo zrušení|Výsledek, FailureType, aktivita, ActivityType, kanál|
|OrchestrationTriggersEnded|Aktivační události ukončeny|Počet|Celkem|Počet zrušených aktivačních událostí orchestrace, selhání nebo zrušení|Výsledek, FailureType, Trigger|
|SQLOnDemandLoginAttempts|Pokusy o přihlášení|Počet|Celkem|Počet pokusů o přihlášení, které nasadilo nebo selhaly|Výsledek|
|SQLOnDemandQueriesEnded|Ukončené dotazy|Počet|Celkem|Počet dotazů, které úspěšně uspěly, selhaly nebo byly zrušeny|Výsledek|
|SQLOnDemandQueryProcessedBytes|Zpracovaná data|Bajty|Celkem|Množství dat zpracovaných dotazy|Žádná|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SparkJobsEnded|Ukončené aplikace|Počet|Celkem|Počet ukončených aplikací|JobType, výsledek úlohy|
|CoresCapacity|Kapacita jader|Počet|Maximum|Kapacita jader|Žádná|
|MemoryCapacityGB|Kapacita paměti (GB)|Počet|Maximum|Kapacita paměti (GB)|Žádná|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DWULimit|DWU limit|Počet|Maximum|Cíl na úrovni služby pro fond SQL|Žádná|
|DWUUsed|DWU použito|Počet|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měřeno podle limitu DWU * procento DWU|Žádná|
|DWUUsedPercent|Procento využitého DWU|Procento|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měří se tak, že se převezme maximum mezi procenty CPU a v/v.|Žádná|
|ConnectionsBlockedByFirewall|Připojení blokovaná bránou firewall|Počet|Celkem|Počet připojení blokovaných pravidly brány firewall Přečtěte si zásady řízení přístupu pro váš fond SQL a sledujte tato připojení, pokud je počet vysoký.|Žádná|
|AdaptiveCacheHitPercent|Procento přístupů do adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procentuální hodnoty přístupů do mezipaměti určete, jestli se má škálovat pro další kapacitu, nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádná|
|AdaptiveCacheUsedPercent|Procento využití adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procenta využití mezipaměti určete, jestli se má škálovat pro další kapacitu nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádná|
|LocalTempDBUsedPercent|Procento využité místní databáze tempdb|Procento|Maximum|Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se vysílají každých pět minut.|Žádná|
|MemoryUsedPercent|Procento využité paměti|Procento|Maximum|Využití paměti ve všech uzlech ve fondu SQL|Žádná|
|Připojení|Připojení|Počet|Celkem|Celkový počet přihlášení ke fondu SQL|Výsledek|
|WLGActiveQueries|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny úloh. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému.|IsUserDefined, pracovní vytížení|
|WLGActiveQueriesTimeouts|Vypršení časového limitu dotazu skupiny úloh|Počet|Celkem|Dotazy na skupinu úloh, jejichž časový limit vypršel. Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky).|IsUserDefined, pracovní vytížení|
|WLGAllocationBySystemPercent|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Procento přidělení prostředků vzhledem k celému systému|IsUserDefined, pracovní vytížení|
|WLGAllocationByMaxResourcePercent|Přidělení skupiny úloh podle maximálního procenta prostředků|Procento|Maximum|Zobrazuje procento přidělení prostředků vzhledem k procentuální hodnotě prostředku efektivního Cap na skupinu úloh. Tato metrika poskytuje efektivní využití skupiny úloh.|IsUserDefined, pracovní vytížení|
|WLGEffectiveCapResourcePercent|Procento efektivního Cap prostředku|Procento|Maximum|Procentuální hodnota prostředku efektivního Cap pro skupinu úloh. Pokud jsou k dispozici jiné skupiny úloh s min_percentage_resource > 0, effective_cap_percentage_resource se v poměru dolů.|IsUserDefined, pracovní vytížení|
|wlg_effective_min_resource_percent|Efektivní minimální procento prostředků|Procento|Minimální|Nastavení platné minimální procento prostředků s povoleným zvážením úrovně služby a nastavení skupiny úloh. Efektivní min_percentage_resource lze zvýšit na nižší úrovni služeb.|IsUserDefined, pracovní vytížení|
|WLGQueuedQueries|Dotazování skupin úloh ve frontě|Počet|Celkem|Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti|IsUserDefined, pracovní vytížení|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv načtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádná|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádná|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze všech zdrojů událostí|Žádná|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádná|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádná|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádná|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádná|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádná|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádná|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv přečtených ze zdroje události|Žádná|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádná|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze zdroje události|Žádná|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádná|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádná|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádná|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádná|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádná|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádná|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty čtení z disku/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací čtení v období vzorkování.|Žádná|
|DiskWriteBytesPerSecond|Bajty zápisu na disk/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací zápisu v období vzorkování.|Žádná|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Celková propustnost disku z důvodu operací čtení v období vzorkování.|Žádná|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Celková propustnost disku v důsledku operací zápisu v období vzorkování.|Žádná|
|DiskReadOperations|Operace čtení z disku|Počet|Celkem|Počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádná|
|DiskWriteOperations|Operace zápisu na disk|Počet|Celkem|Počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádná|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádná|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Průměrný počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádná|
|DiskReadLatency|Latence čtení disku|Milisekund|Průměr|Celková latence čtení Součet latencí čtení zařízení a jádra.|Žádná|
|DiskWriteLatency|Latence zápisu na disk|Milisekund|Průměr|Celková latence zápisu Součet latencí zápisu zařízení a jádra.|Žádná|
|NetworkInBytesPerSecond|Síť v bajtech/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přijatý provoz.|Žádná|
|NetworkOutBytesPerSecond|Výstupní bajty sítě/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přenos přenášených dat.|Žádná|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádná|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Celková propustnost sítě pro přenos přenášených dat.|Žádná|
|MemoryUsed|Využitá paměť|Bajty|Průměr|Velikost paměti počítače, kterou virtuální počítač používá.|Žádná|
|MemoryGranted|Přidělená paměť|Bajty|Průměr|Velikost paměti, která byla k virtuálnímu počítači udělena hostitelem. Hostiteli není pro hostitele udělené, dokud se nedotknete jednou, a pokud VMkernel potřebuje paměť, může se tato paměť vyměnit.|Žádná|
|MemoryActive|Paměť aktivní|Bajty|Průměr|Velikost paměti, kterou virtuální počítač využíval v posledních malých oknech času. Toto je "pravdivý" počet paměti, které virtuální počítač v současnosti potřebuje. Dodatečná, nevyužitá paměť může být vyměněna nebo v bublině bez dopadu na výkon hosta.|Žádná|
|Procento CPU|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je hlášena s 100%, která představuje všechny jádra procesoru v systému. Příklad: oboustranný virtuální počítač, který používá 50% systému se čtyřmi jádry, plně používá dvě jádra.|Žádná|
|PercentageCpuReady|Procento připraveného procesoru|Milisekund|Celkem|Čas připravenosti je doba, po kterou se bude čekat na dostupnost PROCESORů v minulém intervalu aktualizace.|Žádná|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/serverových farem

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Počet|Průměr|Délka fronty http|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|TcpSynSent|Odesláno TCP syn|Počet|Průměr|Odesláno TCP syn|Instance|
|TcpSynReceived|Přijato TCP syn|Počet|Průměr|Přijato TCP syn|Instance|
|TcpEstablished|TCP – vytvořeno|Počet|Průměr|TCP – vytvořeno|Instance|
|TcpFinWait1|TCP FIN – čekání 1|Počet|Průměr|TCP FIN – čekání 1|Instance|
|TcpFinWait2|TCP FIN – čekání 2|Počet|Průměr|TCP FIN – čekání 2|Instance|
|TcpClosing|Ukončení protokolu TCP|Počet|Průměr|Ukončení protokolu TCP|Instance|
|TcpCloseWait|Ukončení čekání protokolu TCP|Počet|Průměr|Ukončení čekání protokolu TCP|Instance|
|TcpLastAck|Poslední potvrzení TCP|Počet|Průměr|Poslední potvrzení TCP|Instance|
|TcpTimeWait|Doba čekání protokolu TCP|Počet|Průměr|Doba čekání protokolu TCP|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/weby (kromě funkcí) 

> [!NOTE]
> **Využití systému souborů** je nová metrika, která je globálně nasazená, ale neočekávají se žádná data, pokud jste si je nepřidali do seznamu povolených privátních náhledů.

> [!IMPORTANT]
> **Průměrná doba odezvy** bude zastaralá, aby nedocházelo k záměně s agregacemi metrik. Použijte **dobu odezvy** jako náhradu.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|HttpResponseTime|Doba odezvy|Sekundy|Celkem|Doba odezvy|Instance|
|AverageResponseTime|Průměrná doba odezvy (nepoužívané)|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
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
|HealthCheckStatus|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádná|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/lokality (funkce)

> [!NOTE]
> **Využití systému souborů** je nová metrika, která je globálně nasazená, ale neočekávají se žádná data, pokud jste si je nepřidali do seznamu povolených privátních náhledů.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|MB/milisekundy|Celkem|[Jednotky spuštění funkce](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
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
|HealthCheckStatus|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádná|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|HttpResponseTime|Doba odezvy|Sekundy|Průměr|Doba odezvy|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|Počet|Celkem|Jednotky spuštění funkce|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|AppConnections|Připojení|Počet|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Počet|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Počet|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
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
|HealthCheckStatus|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádná|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Žádosti|Žádosti|Počet|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Počet|Celkem|Http 101|Instance|
|Http2xx|2xx http|Počet|Celkem|2xx http|Instance|
|Http3xx|3xx http|Počet|Celkem|3xx http|Instance|
|Http401|HTTP 401|Počet|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Počet|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Počet|Celkem|HTTP 404|Instance|
|Http406|Http 406|Počet|Celkem|Http 406|Instance|
|Http4xx|4xx http|Počet|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Počet|Celkem|Chyby serveru http|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Počet|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Počet|Průměr|Délka fronty http|Instance|
|ActiveRequests|Aktivní požadavky|Počet|Celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet front-endy|Počet|Průměr|Celkový počet front-endy|Žádná|
|SmallAppServicePlanInstances|Plánování pracovníků malých App Service|Počet|Průměr|Plánování pracovníků malých App Service|Žádná|
|MediumAppServicePlanInstances|Střední App Service plánování pracovních procesů|Počet|Průměr|Střední App Service plánování pracovních procesů|Žádná|
|LargeAppServicePlanInstances|Zaměstnanci s velkými App Servicey plánu|Počet|Průměr|Zaměstnanci s velkými App Servicey plánu|Žádná|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WorkersTotal|Celkový počet pracovníků|Počet|Průměr|Celkový počet pracovníků|Žádná|
|WorkersAvailable|Zaměstnanci, kteří jsou k dispozici|Počet|Průměr|Zaměstnanci, kteří jsou k dispozici|Žádná|
|WorkersUsed|Využívané pracovní procesy|Počet|Průměr|Využívané pracovní procesy|Žádná|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
## <a name="next-steps"></a>Další kroky
* [Přečtěte si o metrikách v Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Exportovat metriky do úložiště, centra událostí nebo Log Analytics](platform-logs-overview.md)

