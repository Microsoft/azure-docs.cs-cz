---
title: Azure Monitor podporované metriky podle typu prostředku
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: e8bae2062051156d6de378e54bc354b3f785e403
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515457"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s Azure Monitor

> [!NOTE]
> Tento seznam je z velké části automaticky generovaný z Azure Monitor metrik REST API. Jakékoli změny provedené v tomto seznamu prostřednictvím GitHubu se můžou zapisovat bez upozornění. Podrobnosti o tom, jak provádět trvalé aktualizace, získáte od autora tohoto článku.

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. 

Tento článek je úplný seznam všech metrik platformy (tj. automaticky shromážděných), které jsou aktuálně k dispozici s kanálem konsolidované metriky Azure Monitor. Poslední aktualizace seznamu: březen 27, 2020. Metriky se změnily nebo přidaly po tomto datu se nemusí zobrazit níže. Chcete-li se dotazovat na seznam metrik a přistupovat k nim prostřednictvím kódu programu, použijte [rozhraní API verze 2018-01-01](/rest/api/monitor/metricdefinitions). Jiné metriky, které nejsou v tomto seznamu, mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

Metriky jsou uspořádány podle zprostředkovatelů prostředků a typu prostředku. Seznam služeb a poskytovatelů prostředků, kteří k nim patří, najdete v tématu [poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Metriky hostovaného operačního systému

Metriky pro hostovaný operační systém (hostovaný operační systém), který běží v Azure Virtual Machines, Service Fabric a Cloud Services tady **nejsou uvedené.** Místo toho se musí metriky výkonu hostovaného operačního systému shromažďovat prostřednictvím jednoho nebo více agentů, kteří se spouštějí nebo jako součást hostovaného operačního systému.  Metriky hostovaného operačního systému zahrnují čítače výkonu, které sledují procento využití procesoru hosta nebo paměti, které se často používají pro automatické škálování nebo upozorňování.  Pomocí [rozšíření Azure Diagnostics](diagnostics-extension-overview.md)můžete odesílat metriky výkonu hostovaného operačního systému do stejné databáze, kde jsou uložené metriky platforem. Směruje metriky operačního systému hosta prostřednictvím rozhraní API pro [vlastní metriky](metrics-custom-overview.md) . Pak můžete graf, upozornění a jinak používat metriky operačního systému hosta, jako jsou metriky platforem. Další informace najdete v tématu [Přehled agentů monitorování](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Směrování metrik platforem do jiných umístění

[Nastavení diagnostiky](diagnostic-settings.md) můžete použít k směrování metrik platforem do Azure Storage, Azure monitor protokolů (a tak Log Analytics) a centra událostí.  

Existují určitá omezení, která je možné směrovat, a formulář, ve kterém jsou uložené. 
- Ne všechny metriky je možné exportovat do jiných umístění. Seznam metrik platforem, které můžete exportovat prostřednictvím nastavení diagnostiky, najdete v [tomto článku](metrics-supported-export-diagnostic-settings.md).

- Posílání multidimenzionálních metrik do jiných umístění prostřednictvím nastavení diagnostiky není aktuálně podporováno. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
*Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|memory_metric|Paměť|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|private_bytes_metric|Soukromé bajty|Bajty|Průměr|Soukromé bajty.|ServerResourceType|
|virtual_bytes_metric|Virtuální bajty|Bajty|Průměr|Virtuální bajty.|ServerResourceType|
|TotalConnectionRequests|Požadavky na připojení celkem|Count|Průměr|Celkový počet požadavků na připojení Jedná se o příjem.|ServerResourceType|
|SuccessfullConnectionsPerSec|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|ServerResourceType|
|TotalConnectionFailures|Celkový počet selhání připojení|Count|Průměr|Celkový počet neúspěšných pokusů o připojení|ServerResourceType|
|CurrentUserSessions|Aktuální uživatelské relace|Count|Průměr|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|QueryPoolBusyThreads|Zaneprázdněná vlákna fondu dotazů|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|CommandPoolJobQueueLength|Délka fronty úloh fondu příkazů|Count|Průměr|Počet úloh ve frontě fondu vláken příkazů|ServerResourceType|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Count|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|CurrentConnections|Připojení: aktuální připojení|Count|Průměr|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CleanerCurrentPrice|Paměť: aktuální cena čisticího modulu|Count|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Paměť: velikost čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|ServerResourceType|
|CleanerMemoryNonshrinkable|Paměť: nezmenšovaná paměť čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|ServerResourceType|
|MemoryUsage|Paměť: využití paměti|Bajty|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Rovná se čítači Process\PrivateBytes a velikostí dat mapovaných do paměti, přičemž se ignoruje jakákoli paměť, která byla namapována nebo přidělena xVelocitym stroji pro analýzu paměti (VertiPaq), nad rámec limitu paměti stroje xVelocity.|ServerResourceType|
|MemoryLimitHard|Paměť: limit paměti – pevný|Bajty|Průměr|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|Hodnota memorylimithigh|Paměť: limit paměti – vysoká|Bajty|Průměr|Horní limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Paměť: limit paměti – nízká|Bajty|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Paměť: limit paměti VertiPaq|Bajty|Průměr|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|Kvóta|Paměť: kvóta|Bajty|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|ServerResourceType|
|QuotaBlocked|Paměť: kvóta blokována|Count|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|ServerResourceType|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkované|Bajty|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Paměť: VertiPaq stránkované|Bajty|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|RowsReadPerSec|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsConvertedPerSec|Zpracování: počet převedených řádků za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsWrittenPerSec|Zpracování: počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|CommandPoolBusyThreads|Vlákna: zaneprázdněná vlákna fondu příkazů|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Vlákna: nečinná vlákna fondu příkazů|Count|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|LongParsingBusyThreads|Vlákna: zaneprázdněná vlákna s dlouhou analýzou|Count|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingIdleThreads|Vlákna: nečinná vlákna při dlouhé analýze|Count|Průměr|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingJobQueueLength|Vlákna: délka fronty úloh dlouhého rozboru|Count|Průměr|Počet úloh ve frontě fondu vláken dlouhého analýzy.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování|Count|Průměr|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování|Count|Průměr|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Vlákna: délka fronty úloh v/v fondu zpracování|Count|Průměr|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Vlákna: vlákna nečinných úloh v/v fondu zpracování|Count|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Vlákna: nečinná vlákna v/v fondu zpracování|Count|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|ServerResourceType|
|QueryPoolIdleThreads|Vlákna: nečinná vlákna fondu dotazů|Count|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Count|Průměr|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|ShortParsingBusyThreads|Vlákna: krátkodobá analýza zaneprázdněných vláken|Count|Průměr|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingIdleThreads|Vlákna: nečinná vlákna krátké analýzy|Count|Průměr|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingJobQueueLength|Vlákna: délka fronty úlohy krátké analýzy|Count|Průměr|Počet úloh ve frontě krátkého analýzy fondu vláken.|ServerResourceType|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|ServerResourceType|
|mashup_engine_qpu_metric|QPU modulu M|Count|Průměr|QPU využití hybridních procesů v hybridních modulech|ServerResourceType|
|mashup_engine_memory_metric|Paměť motoru M|Bajty|Průměr|Využití paměti procesy modulu hybridní webové aplikace|ServerResourceType|
|mashup_engine_private_bytes_metric|Počet privátních bajtů modulu M|Bajty|Průměr|Používání privátních bajtů procesy v hybridních hybridních modulech.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuální bajty motoru M|Bajty|Průměr|Využití virtuálních bajtů procesy modulu hybridní webové aplikace|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalRequests|Požadavky brány celkem (zastaralé)|Count|Celkem|Počet požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|SuccessfulRequests|Úspěšné požadavky brány (zastaralé)|Count|Celkem|Počet úspěšných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|UnauthorizedRequests|Neautorizované žádosti o bránu (zastaralé)|Count|Celkem|Počet neautorizovaných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|FailedRequests|Neúspěšné požadavky brány (zastaralé)|Count|Celkem|Počet chyb v žádostech brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory.|Umístění, název hostitele|
|OtherRequests|Jiné požadavky na bránu (zastaralé)|Count|Celkem|Počet dalších požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|Doba trvání|Celková doba trvání žádostí o bránu|Milisekund|Průměr|Celková doba trvání požadavků brány v milisekundách|Umístění, název hostitele|
|BackendDuration|Doba trvání požadavků back-endu|Milisekund|Průměr|Doba trvání požadavků back-endu v milisekundách|Umístění, název hostitele|
|Kapacita|Kapacita|Procento|Průměr|Metrika využití pro službu ApiManagement|Umístění|
|EventHubTotalEvents|Celkový počet událostí EventHub|Count|Celkem|Počet událostí odeslaných do centra EventHub|Umístění|
|EventHubSuccessfulEvents|Úspěšné události EventHub|Count|Celkem|Počet úspěšných událostí EventHub|Umístění|
|EventHubTotalFailedEvents|Neúspěšné události EventHub|Count|Celkem|Počet neúspěšných událostí EventHub|Umístění|
|EventHubRejectedEvents|Odmítnuté události EventHub|Count|Celkem|Počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neoprávněná)|Umístění|
|EventHubThrottledEvents|Omezené události EventHub|Count|Celkem|Počet omezených událostí EventHub|Umístění|
|EventHubTimedoutEvents|Vypršel časový limit událostí EventHub.|Count|Celkem|Počet vypršení časového limitu událostí EventHub|Umístění|
|EventHubDroppedEvents|Vyřazené události EventHub|Count|Celkem|Počet vynechaných událostí z důvodu dosažení limitu velikosti fronty|Umístění|
|EventHubTotalBytesSent|Velikost událostí EventHub|Bajty|Celkem|Celková velikost událostí EventHub v bajtech|Umístění|
|Žádosti|Žádosti|Count|Celkem|Metriky žádostí brány s více dimenzemi|Umístění, název hostitele, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Stav připojení prostředků k síti (Preview)|Count|Celkem|Stav připojení k síti u typů závislých prostředků od služby API Management|Umístění, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Celkový počet příchozích požadavků HTTP.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Průměr|Latence požadavku HTTP.|StatusCode|


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
|GCPauseTotalCount|Počet pozastavení GC|Count|Celkem|Počet pozastavení GC|AppName, pod|
|GCPauseTotalTime|Celkový čas pozastavení GC|Milisekund|Celkem|Celkový čas pozastavení GC|AppName, pod|
|TomcatSentBytes|Celkový počet odeslaných bajtů Tomcat|Bajty|Celkem|Celkový počet odeslaných bajtů Tomcat|AppName, pod|
|TomcatReceivedBytes|Celkový počet přijatých bajtů Tomcat|Bajty|Celkem|Celkový počet přijatých bajtů Tomcat|AppName, pod|
|TomcatRequestTotalTime|Celkový počet požadavků Tomcat|Milisekund|Celkem|Celkový počet požadavků Tomcat|AppName, pod|
|TomcatRequestTotalCount|Celkový počet žádostí Tomcat|Count|Celkem|Celkový počet žádostí Tomcat|AppName, pod|
|TomcatResponseAvgTime|Průměrná doba požadavku Tomcat|Milisekund|Průměr|Průměrná doba požadavku Tomcat|AppName, pod|
|TomcatRequestMaxTime|Maximální čas požadavku Tomcat|Milisekund|Maximum|Maximální čas požadavku Tomcat|AppName, pod|
|TomcatErrorCount|Globální chyba Tomcat|Count|Celkem|Globální chyba Tomcat|AppName, pod|
|TomcatSessionActiveMaxCount|Maximální počet aktivních relací Tomcat|Count|Celkem|Maximální počet aktivních relací Tomcat|AppName, pod|
|TomcatSessionAliveMaxTime|Maximální doba běhu relace Tomcat|Milisekund|Maximum|Maximální doba běhu relace Tomcat|AppName, pod|
|TomcatSessionActiveCurrentCount|Počet otevřených relací Tomcat|Count|Celkem|Počet otevřených relací Tomcat|AppName, pod|
|TomcatSessionCreatedCount|Počet vytvořených relací Tomcat|Count|Celkem|Počet vytvořených relací Tomcat|AppName, pod|
|TomcatSessionExpiredCount|Počet vypršení relace Tomcat|Count|Celkem|Počet vypršení relace Tomcat|AppName, pod|
|TomcatSessionRejectedCount|Počet odmítnutých relací Tomcat|Count|Celkem|Počet odmítnutých relací Tomcat|AppName, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalJob|Celkový počet úloh|Count|Celkem|Celkový počet úloh|Runbook, stav|
|TotalUpdateDeploymentRuns|Celkový počet spuštění nasazení aktualizací|Count|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName, stav|
|TotalUpdateDeploymentMachineRuns|Celkový počet spuštěných počítačů nasazení aktualizace|Count|Celkem|Celkový počet spuštěných počítačů nasazení aktualizace softwaru v běhu nasazení aktualizace softwaru|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CoreCount|Vyhrazený počet jader|Count|Celkem|Celkový počet vyhrazených jader v účtu Batch|Žádný|
|TotalNodeCount|Počet vyhrazených uzlů|Count|Celkem|Celkový počet vyhrazených uzlů v účtu Batch|Žádný|
|LowPriorityCoreCount|Počet jader LowPriority|Count|Celkem|Celkový počet jader s nízkou prioritou v účtu Batch|Žádný|
|TotalLowPriorityNodeCount|Počet uzlů s nízkou prioritou|Count|Celkem|Celkový počet uzlů s nízkou prioritou v účtu Batch|Žádný|
|CreatingNodeCount|Vytváření počtu uzlů|Count|Celkem|Počet vytvořených uzlů|Žádný|
|StartingNodeCount|Počáteční počet uzlů|Count|Celkem|Počet spuštěných uzlů|Žádný|
|WaitingForStartTaskNodeCount|Čekání na počet uzlů spouštěcí úlohy|Count|Celkem|Počet uzlů, které čekají na dokončení počátečního úkolu|Žádný|
|StartTaskFailedNodeCount|Počet neúspěšných spuštění úlohy – počet uzlů|Count|Celkem|Počet uzlů, ve kterých se spouštěcí úkol nezdařil|Žádný|
|IdleNodeCount|Počet nečinných uzlů|Count|Celkem|Počet nečinných uzlů|Žádný|
|OfflineNodeCount|Počet uzlů v režimu offline|Count|Celkem|Počet offline uzlů|Žádný|
|RebootingNodeCount|Restartování počtu uzlů|Count|Celkem|Počet restartování uzlů|Žádný|
|ReimagingNodeCount|Počet uzlů obnovování imagí|Count|Celkem|Počet uzlů obnovování imagí|Žádný|
|RunningNodeCount|Počet spuštěných uzlů|Count|Celkem|Počet spuštěných uzlů|Žádný|
|LeavingPoolNodeCount|Počet ponechávání uzlů fondu|Count|Celkem|Počet uzlů opouštících fond|Žádný|
|UnusableNodeCount|Počet nepoužitelných uzlů|Count|Celkem|Počet nepoužitelných uzlů|Žádný|
|PreemptedNodeCount|Počet zrušených uzlů|Count|Celkem|Počet zrušených uzlů|Žádný|
|TaskStartEvent|Události zahájení úlohy|Count|Celkem|Celkový počet úloh, které byly spuštěny|poolId, jobId|
|TaskCompleteEvent|Události dokončení úlohy|Count|Celkem|Celkový počet úloh, které byly dokončeny|poolId, jobId|
|TaskFailEvent|Události neúspěšných úloh|Count|Celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|poolId, jobId|
|PoolCreateEvent|Vytváření fondů – události|Count|Celkem|Celkový počet vytvořených fondů|poolId|
|PoolResizeStartEvent|Události spuštění změny velikosti fondu|Count|Celkem|Celkový počet změněných počtu fondů, které byly spuštěny|poolId|
|PoolResizeCompleteEvent|Události dokončení změny velikosti fondu|Count|Celkem|Celkový počet změněných velikostí fondu, které byly dokončeny|poolId|
|PoolDeleteStartEvent|Události spuštění odstranění fondu|Count|Celkem|Celkový počet odstranění fondů, které byly spuštěny|poolId|
|PoolDeleteCompleteEvent|Události dokončení odstranění fondu|Count|Celkem|Celkový počet odstranění fondů, které byly dokončeny|poolId|
|JobDeleteCompleteEvent|Události dokončení odstranění úlohy|Count|Celkem|Celkový počet úspěšně odstraněných úloh.|Úlohy|
|JobDeleteStartEvent|Události spuštění odstranění úlohy|Count|Celkem|Celkový počet úloh, které byly vyžádány k odstranění.|Úlohy|
|JobDisableCompleteEvent|Úloha zakázat kompletní události|Count|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|Úlohy|
|JobDisableStartEvent|Úloha zakázat počáteční události|Count|Celkem|Celkový počet úloh, které byly vyžádány k zakázání.|Úlohy|
|JobStartEvent|Události spuštění úlohy|Count|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|Úlohy|
|JobTerminateCompleteEvent|Ukončit události dokončení úlohy|Count|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|Úlohy|
|JobTerminateStartEvent|Události spuštění ukončení úlohy|Count|Celkem|Celkový počet úloh, které byly vyžádány k ukončení.|Úlohy|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/Workspaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Úloha odeslána|Úloha odeslána|Count|Celkem|Počet odeslaných úloh|Scénář, název_clusteru|
|Dokončená úloha|Dokončená úloha|Count|Celkem|Počet dokončených úloh|Scénář, název_clusteru, ResultType|
|Celkem uzlů|Celkem uzlů|Count|Průměr|Počet uzlů celkem|Scénář, název_clusteru|
|Aktivní uzly|Aktivní uzly|Count|Průměr|Počet spuštěných uzlů|Scénář, název_clusteru|
|Nečinné uzly|Nečinné uzly|Count|Průměr|Počet nečinných uzlů|Scénář, název_clusteru|
|Nepoužité uzly|Nepoužité uzly|Count|Průměr|Počet nepoužitelných uzlů|Scénář, název_clusteru|
|Přepnuté uzly|Přepnuté uzly|Count|Průměr|Počet zrušených uzlů|Scénář, název_clusteru|
|Ukončení uzlů|Ukončení uzlů|Count|Průměr|Počet opouštících uzlů|Scénář, název_clusteru|
|Celkový počet jader|Celkový počet jader|Count|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Aktivní jádra|Aktivní jádra|Count|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Jádra nečinných|Jádra nečinných|Count|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nepoužitelné jádra|Nepoužitelné jádra|Count|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Zrušené jádra|Zrušené jádra|Count|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Vynechávání jader|Vynechávání jader|Count|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Count|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procento využití procesoru|Procento|Maximum|Procento využití procesoru|Uzel|
|MemoryUsage|Využití paměti|Bajty|Průměr|Využití paměti|Uzel|
|MemoryLimit|Omezení paměti|Bajty|Průměr|Omezení paměti|Uzel|
|MemoryUsagePercentageInDouble|Procento využití paměti|Procento|Průměr|Procento využití paměti|Uzel|
|StorageUsage|Využití úložiště|Bajty|Průměr|Využití úložiště|Uzel|
|IOReadBytes|Bajty čtení v/v|Bajty|Celkem|Bajty čtení v/v|Uzel|
|IOWriteBytes|Bajty zápisu v/v|Bajty|Celkem|Bajty zápisu v/v|Uzel|
|ConnectionAccepted|Přijatá připojení|Count|Celkem|Přijatá připojení|Uzel|
|ConnectionHandled|Zpracovaná připojení|Count|Celkem|Zpracovaná připojení|Uzel|
|ConnectionActive|Aktivní připojení|Count|Průměr|Aktivní připojení|Uzel|
|RequestHandled|Zpracované žádosti|Count|Celkem|Zpracované žádosti|Uzel|
|ProcessedBlocks|Zpracované bloky|Count|Celkem|Zpracované bloky|Uzel|
|ProcessedTransactions|Zpracované transakce|Count|Celkem|Zpracované transakce|Uzel|
|PendingTransactions|Nedokončené transakce|Count|Průměr|Nedokončené transakce|Uzel|
|QueuedTransactions|Transakce ve frontě|Count|Průměr|Transakce ve frontě|Uzel|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedclients|Připojení klienti|Count|Maximum||ShardId|
|totalcommandsprocessed|Celkový počet operací|Count|Celkem||ShardId|
|cachehits|Přístupy do mezipaměti|Count|Celkem||ShardId|
|cachemisses|Neúspěšné přístupy do mezipaměti|Count|Celkem||ShardId|
|cachemissrate|Frekvence neúspěšných přístupů do mezipaměti|Procento|cachemissrate||ShardId|
|GetCommands|Příkazy Get|Count|Celkem||ShardId|
|setcommands|Příkazy Set|Count|Celkem||ShardId|
|operationsPerSecond|Operace za sekundu|Count|Maximum||ShardId|
|evictedkeys|Vyloučené klíče|Count|Celkem||ShardId|
|totalkeys|Celkem klíčů|Count|Maximum||ShardId|
|expiredkeys|Prošlé klíče|Count|Celkem||ShardId|
|usedmemory|Využitá paměť|Bajty|Maximum||ShardId|
|usedmemorypercentage|Využitá paměť v procentech|Procento|Maximum||ShardId|
|usedmemoryRss|RSS využité paměti|Bajty|Maximum||ShardId|
|serverLoad|Zatížení serveru|Procento|Maximum||ShardId|
|cacheWrite|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|Procesor|Procento|Maximum||ShardId|
|cacheLatency|Mikrosekundy latence mezipaměti (Preview)|Count|Průměr||ShardId|
|chyby|Chyby|Count|Maximum||ShardId,ErrorType|
|connectedclients0|Připojení klienti (horizontálních oddílů 0)|Count|Maximum||Žádný|
|totalcommandsprocessed0|Celkem operací (horizontálních oddílů 0)|Count|Celkem||Žádný|
|cachehits0|Přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádný|
|cachemisses0|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Count|Celkem||Žádný|
|getcommands0|Načtení (horizontálních oddílů 0)|Count|Celkem||Žádný|
|setcommands0|Sady (horizontálních oddílů 0)|Count|Celkem||Žádný|
|operationsPerSecond0|Operací za sekundu (horizontálních oddílů 0)|Count|Maximum||Žádný|
|evictedkeys0|Vyřazené klíče (horizontálních oddílů 0)|Count|Celkem||Žádný|
|totalkeys0|Celkem klíčů (horizontálních oddílů 0)|Count|Maximum||Žádný|
|expiredkeys0|Klíče vypršení platnosti (horizontálních oddílů 0)|Count|Celkem||Žádný|
|usedmemory0|Využitá paměť (horizontálních oddílů 0)|Bajty|Maximum||Žádný|
|usedmemoryRss0|RSS využité paměti (horizontálních oddílů 0)|Bajty|Maximum||Žádný|
|serverLoad0|Zatížení serveru (horizontálních oddílů 0)|Procento|Maximum||Žádný|
|cacheWrite0|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádný|
|cacheRead0|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime0|PROCESOR (horizontálních oddílů 0)|Procento|Maximum||Žádný|
|connectedclients1|Připojení klienti (horizontálních oddílů 1)|Count|Maximum||Žádný|
|totalcommandsprocessed1|Celkem operací (horizontálních oddílů 1)|Count|Celkem||Žádný|
|cachehits1|Přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádný|
|cachemisses1|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Count|Celkem||Žádný|
|getcommands1|Načtení (horizontálních oddílů 1)|Count|Celkem||Žádný|
|setcommands1|Sady (horizontálních oddílů 1)|Count|Celkem||Žádný|
|operationsPerSecond1|Operací za sekundu (horizontálních oddílů 1)|Count|Maximum||Žádný|
|evictedkeys1|Vyřazené klíče (horizontálních oddílů 1)|Count|Celkem||Žádný|
|totalkeys1|Celkem klíčů (horizontálních oddílů 1)|Count|Maximum||Žádný|
|expiredkeys1|Klíče vypršení platnosti (horizontálních oddílů 1)|Count|Celkem||Žádný|
|usedmemory1|Využitá paměť (horizontálních oddílů 1)|Bajty|Maximum||Žádný|
|usedmemoryRss1|RSS využité paměti (horizontálních oddílů 1)|Bajty|Maximum||Žádný|
|serverLoad1|Zatížení serveru (horizontálních oddílů 1)|Procento|Maximum||Žádný|
|cacheWrite1|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádný|
|cacheRead1|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime1|PROCESOR (horizontálních oddílů 1)|Procento|Maximum||Žádný|
|connectedclients2|Připojení klienti (horizontálních oddílů 2)|Count|Maximum||Žádný|
|totalcommandsprocessed2|Celkem operací (horizontálních oddílů 2)|Count|Celkem||Žádný|
|cachehits2|Přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádný|
|cachemisses2|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Count|Celkem||Žádný|
|getcommands2|Get (horizontálních oddílů 2)|Count|Celkem||Žádný|
|setcommands2|Sady (horizontálních oddílů 2)|Count|Celkem||Žádný|
|operationsPerSecond2|Operací za sekundu (horizontálních oddílů 2)|Count|Maximum||Žádný|
|evictedkeys2|Vyřazené klíče (horizontálních oddílů 2)|Count|Celkem||Žádný|
|totalkeys2|Celkem klíčů (horizontálních oddílů 2)|Count|Maximum||Žádný|
|expiredkeys2|Klíče vypršení platnosti (horizontálních oddílů 2)|Count|Celkem||Žádný|
|usedmemory2|Využitá paměť (horizontálních oddílů 2)|Bajty|Maximum||Žádný|
|usedmemoryRss2|RSS využité paměti (horizontálních oddílů 2)|Bajty|Maximum||Žádný|
|serverLoad2|Zatížení serveru (horizontálních oddílů 2)|Procento|Maximum||Žádný|
|cacheWrite2|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádný|
|cacheRead2|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime2|PROCESOR (horizontálních oddílů 2)|Procento|Maximum||Žádný|
|connectedclients3|Připojení klienti (horizontálních oddílů 3)|Count|Maximum||Žádný|
|totalcommandsprocessed3|Celkem operací (horizontálních oddílů 3)|Count|Celkem||Žádný|
|cachehits3|Přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádný|
|cachemisses3|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Count|Celkem||Žádný|
|getcommands3|Get (horizontálních oddílů 3)|Count|Celkem||Žádný|
|setcommands3|Sady (horizontálních oddílů 3)|Count|Celkem||Žádný|
|operationsPerSecond3|Operací za sekundu (horizontálních oddílů 3)|Count|Maximum||Žádný|
|evictedkeys3|Vyřazené klíče (horizontálních oddílů 3)|Count|Celkem||Žádný|
|totalkeys3|Celkem klíčů (horizontálních oddílů 3)|Count|Maximum||Žádný|
|expiredkeys3|Klíče vypršení platnosti (horizontálních oddílů 3)|Count|Celkem||Žádný|
|usedmemory3|Využitá paměť (horizontálních oddílů 3)|Bajty|Maximum||Žádný|
|usedmemoryRss3|RSS využité paměti (horizontálních oddílů 3)|Bajty|Maximum||Žádný|
|serverLoad3|Zatížení serveru (horizontálních oddílů 3)|Procento|Maximum||Žádný|
|cacheWrite3|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádný|
|cacheRead3|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime3|PROCESOR (horizontálních oddílů 3)|Procento|Maximum||Žádný|
|connectedclients4|Připojení klienti (horizontálních oddílů 4)|Count|Maximum||Žádný|
|totalcommandsprocessed4|Celkem operací (horizontálních oddílů 4)|Count|Celkem||Žádný|
|cachehits4|Přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádný|
|cachemisses4|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Count|Celkem||Žádný|
|getcommands4|Get (horizontálních oddílů 4)|Count|Celkem||Žádný|
|setcommands4|Sady (horizontálních oddílů 4)|Count|Celkem||Žádný|
|operationsPerSecond4|Operací za sekundu (horizontálních oddílů 4)|Count|Maximum||Žádný|
|evictedkeys4|Vyřazené klíče (horizontálních oddílů 4)|Count|Celkem||Žádný|
|totalkeys4|Celkem klíčů (horizontálních oddílů 4)|Count|Maximum||Žádný|
|expiredkeys4|Klíče vypršení platnosti (horizontálních oddílů 4)|Count|Celkem||Žádný|
|usedmemory4|Využitá paměť (horizontálních oddílů 4)|Bajty|Maximum||Žádný|
|usedmemoryRss4|RSS využité paměti (horizontálních oddílů 4)|Bajty|Maximum||Žádný|
|serverLoad4|Zatížení serveru (horizontálních oddílů 4)|Procento|Maximum||Žádný|
|cacheWrite4|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádný|
|cacheRead4|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime4|PROCESOR (horizontálních oddílů 4)|Procento|Maximum||Žádný|
|connectedclients5|Připojení klienti (horizontálních oddílů 5)|Count|Maximum||Žádný|
|totalcommandsprocessed5|Celkem operací (horizontálních oddílů 5)|Count|Celkem||Žádný|
|cachehits5|Přístupy do mezipaměti (horizontálních oddílů 5)|Count|Celkem||Žádný|
|cachemisses5|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)|Count|Celkem||Žádný|
|getcommands5|Získá (horizontálních oddílů 5)|Count|Celkem||Žádný|
|setcommands5|Sady (horizontálních oddílů 5)|Count|Celkem||Žádný|
|operationsPerSecond5|Operací za sekundu (horizontálních oddílů 5)|Count|Maximum||Žádný|
|evictedkeys5|Vyřazení klíčů (horizontálních oddílů 5)|Count|Celkem||Žádný|
|totalkeys5|Celkem klíčů (horizontálních oddílů 5)|Count|Maximum||Žádný|
|expiredkeys5|Klíče vypršení platnosti (horizontálních oddílů 5)|Count|Celkem||Žádný|
|usedmemory5|Využitá paměť (horizontálních oddílů 5)|Bajty|Maximum||Žádný|
|usedmemoryRss5|RSS využité paměti (horizontálních oddílů 5)|Bajty|Maximum||Žádný|
|serverLoad5|Zatížení serveru (horizontálních oddílů 5)|Procento|Maximum||Žádný|
|cacheWrite5|Zápis do mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádný|
|cacheRead5|Čtení z mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime5|PROCESOR (horizontálních oddílů 5)|Procento|Maximum||Žádný|
|connectedclients6|Připojení klienti (horizontálních oddílů 6)|Count|Maximum||Žádný|
|totalcommandsprocessed6|Celkem operací (horizontálních oddílů 6)|Count|Celkem||Žádný|
|cachehits6|Přístupy do mezipaměti (horizontálních oddílů 6)|Count|Celkem||Žádný|
|cachemisses6|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)|Count|Celkem||Žádný|
|getcommands6|Načtení (horizontálních oddílů 6)|Count|Celkem||Žádný|
|setcommands6|Sady (horizontálních oddílů 6)|Count|Celkem||Žádný|
|operationsPerSecond6|Operací za sekundu (horizontálních oddílů 6)|Count|Maximum||Žádný|
|evictedkeys6|Vyřazené klíče (horizontálních oddílů 6)|Count|Celkem||Žádný|
|totalkeys6|Celkem klíčů (horizontálních oddílů 6)|Count|Maximum||Žádný|
|expiredkeys6|Klíče vypršení platnosti (horizontálních oddílů 6)|Count|Celkem||Žádný|
|usedmemory6|Využitá paměť (horizontálních oddílů 6)|Bajty|Maximum||Žádný|
|usedmemoryRss6|RSS využité paměti (horizontálních oddílů 6)|Bajty|Maximum||Žádný|
|serverLoad6|Zatížení serveru (horizontálních oddílů 6)|Procento|Maximum||Žádný|
|cacheWrite6|Zápis do mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádný|
|cacheRead6|Čtení z mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime6|PROCESOR (horizontálních oddílů 6)|Procento|Maximum||Žádný|
|connectedclients7|Připojení klienti (horizontálních oddílů 7)|Count|Maximum||Žádný|
|totalcommandsprocessed7|Celkem operací (horizontálních oddílů 7)|Count|Celkem||Žádný|
|cachehits7|Přístupy do mezipaměti (horizontálních oddílů 7)|Count|Celkem||Žádný|
|cachemisses7|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)|Count|Celkem||Žádný|
|getcommands7|Načtení (horizontálních oddílů 7)|Count|Celkem||Žádný|
|setcommands7|Sady (horizontálních oddílů 7)|Count|Celkem||Žádný|
|operationsPerSecond7|Operací za sekundu (horizontálních oddílů 7)|Count|Maximum||Žádný|
|evictedkeys7|Vyřazení klíčů (horizontálních oddílů 7)|Count|Celkem||Žádný|
|totalkeys7|Celkem klíčů (horizontálních oddílů 7)|Count|Maximum||Žádný|
|expiredkeys7|Klíče vypršení platnosti (horizontálních oddílů 7)|Count|Celkem||Žádný|
|usedmemory7|Využitá paměť (horizontálních oddílů 7)|Bajty|Maximum||Žádný|
|usedmemoryRss7|RSS využité paměti (horizontálních oddílů 7)|Bajty|Maximum||Žádný|
|serverLoad7|Zatížení serveru (horizontálních oddílů 7)|Procento|Maximum||Žádný|
|cacheWrite7|Zápis do mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádný|
|cacheRead7|Čtení z mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime7|PROCESOR (horizontálních oddílů 7)|Procento|Maximum||Žádný|
|connectedclients8|Připojení klienti (horizontálních oddílů 8)|Count|Maximum||Žádný|
|totalcommandsprocessed8|Celkem operací (horizontálních oddílů 8)|Count|Celkem||Žádný|
|cachehits8|Přístupy do mezipaměti (horizontálních oddílů 8)|Count|Celkem||Žádný|
|cachemisses8|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)|Count|Celkem||Žádný|
|getcommands8|Získá (horizontálních oddílů 8)|Count|Celkem||Žádný|
|setcommands8|Sady (horizontálních oddílů 8)|Count|Celkem||Žádný|
|operationsPerSecond8|Operací za sekundu (horizontálních oddílů 8)|Count|Maximum||Žádný|
|evictedkeys8|Vyřazené klíče (horizontálních oddílů 8)|Count|Celkem||Žádný|
|totalkeys8|Celkem klíčů (horizontálních oddílů 8)|Count|Maximum||Žádný|
|expiredkeys8|Klíče vypršení platnosti (horizontálních oddílů 8)|Count|Celkem||Žádný|
|usedmemory8|Využitá paměť (horizontálních oddílů 8)|Bajty|Maximum||Žádný|
|usedmemoryRss8|RSS využité paměti (horizontálních oddílů 8)|Bajty|Maximum||Žádný|
|serverLoad8|Zatížení serveru (horizontálních oddílů 8)|Procento|Maximum||Žádný|
|cacheWrite8|Zápis do mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádný|
|cacheRead8|Čtení z mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime8|PROCESOR (horizontálních oddílů 8)|Procento|Maximum||Žádný|
|connectedclients9|Připojení klienti (horizontálních oddílů 9)|Count|Maximum||Žádný|
|totalcommandsprocessed9|Celkem operací (horizontálních oddílů 9)|Count|Celkem||Žádný|
|cachehits9|Přístupy do mezipaměti (horizontálních oddílů 9)|Count|Celkem||Žádný|
|cachemisses9|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)|Count|Celkem||Žádný|
|getcommands9|Načtení (horizontálních oddílů 9)|Count|Celkem||Žádný|
|setcommands9|Sady (horizontálních oddílů 9)|Count|Celkem||Žádný|
|operationsPerSecond9|Operací za sekundu (horizontálních oddílů 9)|Count|Maximum||Žádný|
|evictedkeys9|Vyřazené klíče (horizontálních oddílů 9)|Count|Celkem||Žádný|
|totalkeys9|Celkem klíčů (horizontálních oddílů 9)|Count|Maximum||Žádný|
|expiredkeys9|Klíče vypršení platnosti (horizontálních oddílů 9)|Count|Celkem||Žádný|
|usedmemory9|Využitá paměť (horizontálních oddílů 9)|Bajty|Maximum||Žádný|
|usedmemoryRss9|RSS využité paměti (horizontálních oddílů 9)|Bajty|Maximum||Žádný|
|serverLoad9|Zatížení serveru (horizontálních oddílů 9)|Procento|Maximum||Žádný|
|cacheWrite9|Zápis do mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádný|
|cacheRead9|Čtení z mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádný|
|percentProcessorTime9|PROCESOR (horizontálních oddílů 9)|Procento|Maximum||Žádný|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Count|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|Žádný|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|Žádný|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádný|
|Bajty čtení z disku/s|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|Žádný|
|Bajty zápisu na disk/s|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|Žádný|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádný|


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
|UsedCapacity|Využitá kapacita|Bajty|Průměr|Kapacita využitá účtem|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů BLOB|Count|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů objektů BLOB|Count|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádný|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita tabulky|Bajty|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádný|
|TableCount|Počet tabulek|Count|Průměr|Počet tabulek v Table service účtu úložiště|Žádný|
|TableEntityCount|Počet entit tabulky|Count|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita souboru|Bajty|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Sdílená složka|
|FileCount|Počet souborů|Count|Průměr|Počet souborů v Souborové službě účtu úložiště.|Sdílená složka|
|FileShareCount|Počet sdílených souborů|Count|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádný|
|FileShareSnapshotCount|Počet snímků sdílené složky|Count|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílená složka|
|FileShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílená složka|
|FileShareCapacityQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílená složka|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádný|
|QueueCount|Počet front|Count|Průměr|Počet front v Služba front účtu úložiště.|Žádný|
|QueueMessageCount|Počet zpráv ve frontě|Count|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalCalls|Celkový počet volání|Count|Celkem|Celkový počet volání.|ApiName, OperationName, oblast|
|SuccessfulCalls|Úspěšná volání|Count|Celkem|Počet úspěšných volání.|ApiName, OperationName, oblast|
|TotalErrors|Celkový počet chyb|Count|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName, OperationName, oblast|
|BlockedCalls|Blokovaná volání|Count|Celkem|Počet volání, která překročily limit nebo kvótu.|ApiName, OperationName, oblast|
|ServerErrors|Chyby serveru|Count|Celkem|Počet volání s interní chybou služby (kód odpovědi HTTP 5xx).|ApiName, OperationName, oblast|
|ClientErrors|Chyby klienta|Count|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName, OperationName, oblast|
|DataIn|Data v|Bajty|Celkem|Velikost příchozích dat v bajtech|ApiName, OperationName, oblast|
|Data|Výstupní data|Bajty|Celkem|Velikost odchozích dat v bajtech|ApiName, OperationName, oblast|
|Latence|Latence|Milisekund|Průměr|Latence v milisekundách|ApiName, OperationName, oblast|
|TotalTokenCalls|Celkový počet volání tokenu|Count|Celkem|Celkový počet volání tokenů|ApiName, OperationName, oblast|
|CharactersTranslated|Přeložené znaky|Count|Celkem|Celkový počet znaků v příchozím textovém požadavku.|ApiName, OperationName, oblast|
|CharactersTrained|Vyškolené znaky|Count|Celkem|Celkový počet vyškolených znaků|ApiName, OperationName, oblast|
|SpeechSessionDuration|Doba trvání relace řeči|Sekundy|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName, OperationName, oblast|
|TotalTransactions|Celkový počet transakcí|Count|Celkem|Celkový počet transakcí|Žádný|
|ProcessedImages|Zpracované bitové kopie|Count|Celkem| Počet transakcí pro zpracování bitové kopie.|ApiName, vlastnost vlastnosti, kanál, oblast|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádný|
|Síťové vstupy|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádný|
|Síťové výstupy|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádný|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádný|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádný|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádný|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Count|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Count|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)] (Portal-disk-Metrics-deprecation.MD)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádný|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádný|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádný|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádný|
|Příchozí toky|Příchozí toky|Count|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádný|
|Odchozí toky|Odchozí toky|Count|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádný|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádný|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádný|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádný|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádný|
|Celková síť|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádný|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádný|


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
|Zbývající kredity procesoru|Zbývající kredity procesoru|Count|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Count|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádný|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádný|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LUN, VMName|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LUN, VMName|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|LUN, VMName|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|LUN, VMName|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|LUN, VMName|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Příchozí toky|Příchozí toky|Count|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|VMName|
|Odchozí toky|Odchozí toky|Count|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|VMName|
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
|Procento CPU|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádný|
|Síťové vstupy|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádný|
|Síťové výstupy|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádný|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádný|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádný|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádný|
|Zbývající kredity procesoru|Zbývající kredity procesoru|Count|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádný|
|Spotřebované kredity procesoru|Spotřebované kredity procesoru|Count|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem|Žádný|
|Přečtené bajty podle disku/s|Bajty přečtené z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|SlotId|
|Bajty zápisu na disk/s|Bajty zapsané na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|SlotId|
|Operace čtení na disk/s|Operace čtení z datového disku za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|SlotId|
|Operace zápisu na disk/s|Operace zápisu na datový disk za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|SlotId|
|Hloubka fronty na disk|Datový disk hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|SlotId|
|Přečtené bajty v operačním systému na disk/s|Bajty přečtené z disku s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Bajty zapsané v operačním systému na disk/s|Bajty zápisu na disk s operačním systémem/s [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádný|
|Operace čtení z operačního systému na disk/s|Operace čtení z disku s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operace zápisu v operačním systému na disk/s|Operace zápisu na disk s operačním systémem za sekundu [(zastaralé)](portal-disk-metrics-deprecation.md)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operační systém na disk hloubka fronty|Disk s operačním systémem hloubka fronty [(zastaralé)](portal-disk-metrics-deprecation.md)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádný|
|Bajty přečtené z datového disku za sekundu|Bajty přečtené z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Bajty zapsané na datový disk/s|Bajty zapsané na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace čtení z datového disku za sekundu|Operace čtení z datového disku za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Operace zápisu na datový disk/s|Operace zápisu na datový disk za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Hloubka fronty datového disku|Hloubka fronty datových disků (Preview)|Count|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z disku s operačním systémem/s|Počet přečtených bajtů disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Bajty zápisu na disk s operačním systémem/s|Bajty zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádný|
|Operace čtení z disku s operačním systémem za sekundu|Operace čtení z disku s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Operace zápisu na disk s operačním systémem za sekundu|Operace zápisu na disk s operačním systémem za sekundu (Preview)|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádný|
|Hloubka fronty disku s operačním systémem|Hloubka fronty disku operačního systému (Preview)|Count|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádný|
|Příchozí toky|Příchozí toky|Count|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádný|
|Odchozí toky|Odchozí toky|Count|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádný|
|Maximální rychlost vytváření příchozích toků|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádný|
|Maximální rychlost vytváření odchozích toků|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádný|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Počet přístupů do mezipaměti pro datový disk úrovně Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Neúspěšné čtení mezipaměti datových disků Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Pozice pro čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádný|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Neúspěšné čtení mezipaměti disku s operačním systémem Premium (Preview)|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádný|
|Celková síť|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádný|
|Celkový počet síťových výstupů|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádný|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuUsage|Využití procesoru|Count|Průměr|Využití CPU na všech jádrech v millicores.|containerName|
|MemoryUsage|Využití paměti|Bajty|Průměr|Celkové využití paměti v bajtech|containerName|
|NetworkBytesReceivedPerSecond|Počet přijatých bajtů sítě za sekundu|Bajty|Průměr|Počet přijatých bajtů sítě za sekundu.|Žádný|
|NetworkBytesTransmittedPerSecond|Bajty přenášené přes síť za sekundu|Bajty|Průměr|Počet bajtů přenášených sítí za sekundu.|Žádný|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalPullCount|Celkový počet vyžádané replikace|Count|Průměr|Celkový počet načtených dat imagí|Žádný|
|SuccessfulPullCount|Počet úspěšných vyžádané replikace|Count|Průměr|Počet úspěšných stažení imagí|Žádný|
|TotalPushCount|Celkový počet nabízených oznámení|Count|Průměr|Celkový počet nabízených oznámení imagí|Žádný|
|SuccessfulPushCount|Počet úspěšných vložení|Count|Průměr|Počet úspěšných vložení imagí|Žádný|
|RunDuration|Doba trvání běhu|Milisekund|Celkem|Doba běhu v milisekundách|Žádný|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Count|Průměr|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Žádný|
|kube_node_status_allocatable_memory_bytes|Celková velikost dostupné paměti ve spravovaném clusteru|Bajty|Průměr|Celková velikost dostupné paměti ve spravovaném clusteru|Žádný|
|kube_pod_status_ready|Počet lusků ve stavu připraveno|Count|Průměr|Počet lusků ve stavu připraveno|obor názvů, pod|
|kube_node_status_condition|Stavy pro různé podmínky uzlu|Count|Průměr|Stavy pro různé podmínky uzlu|podmínka, stav, status2, uzel|
|kube_pod_status_phase|Počet lusků podle fáze|Count|Průměr|Počet lusků podle fáze|fáze, obor názvů pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfullRequests|Úspěšné požadavky|Count|Celkem|Úspěšné požadavky vytvořené vlastním poskytovatelem|HttpMethod, CallPath, StatusCode|
|FailedRequests|Neúspěšné požadavky|Count|Celkem|Získá dostupné protokoly pro vlastní poskytovatele prostředků.|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|NICReadThroughput|Propustnost čtení (síť)|BytesPerSecond|Průměr|Propustnost čtení síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Propustnost zápisu (síť)|BytesPerSecond|Průměr|Propustnost zápisu síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|CloudReadThroughputPerShare|Propustnost stahování do cloudu (sdílení)|BytesPerSecond|Průměr|Propustnost stahování do Azure ze sdílené složky během období generování sestav.|Sdílení|
|CloudUploadThroughputPerShare|Propustnost nahrávání do cloudu (sdílení)|BytesPerSecond|Průměr|Odeslání propustnosti do Azure ze sdílené složky během období generování sestav.|Sdílení|
|BytesUploadedToCloudPerShare|Odeslané bajty v cloudu (sdílená složka)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze sdílené složky během období generování sestav.|Sdílení|
|Úložiště|Celková kapacita|Bajty|Průměr|Celková kapacita|Žádný|
|Availablecapacity;)|Dostupná kapacita|Bajty|Průměr|Dostupná kapacita v bajtech během období generování sestav.|Žádný|
|CloudUploadThroughput|Propustnost nahrávání do cloudu|BytesPerSecond|Průměr|Propustnost nahrávání do cloudu do Azure během období generování sestav.|Žádný|
|CloudReadThroughput|Propustnost stahování v cloudu|BytesPerSecond|Průměr|Propustnost stahování cloudu do Azure během období generování sestav.|Žádný|
|BytesUploadedToCloud|Odeslané bajty v cloudu (zařízení)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze zařízení během období generování sestav.|Žádný|
|HyperVVirtualProcessorUtilization|Výpočetní prostředí Edge – procento využití procesoru|Procento|Průměr|Procento využití procesoru|InstanceName|
|HyperVMemoryUtilization|Výpočet využití paměti na hraničních zařízeních|Procento|Průměr|Velikost využité paměti RAM|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|FailedRuns|Neúspěšná spuštění|Count|Celkem||profilace, Activity|
|SuccessfulRuns|Úspěšná spuštění|Count|Celkem||profilace, Activity|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PipelineFailedRuns|Neúspěšná metrika spuštění kanálu|Count|Celkem||FailureType, název|
|PipelineSucceededRuns|Úspěšné metriky spuštění kanálu|Count|Celkem||FailureType, název|
|PipelineCancelledRuns|Zrušené metriky spuštění kanálu|Count|Celkem||FailureType, název|
|ActivityFailedRuns|Neúspěšná aktivita spustí metriky|Count|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivitySucceededRuns|Úspěšná aktivita spustí metriky|Count|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivityCancelledRuns|Zrušené metriky spuštění aktivit|Count|Celkem||ActivityType, název kanálu, FailureType, název|
|TriggerFailedRuns|Neúspěšná aktivační událost spustí metriky|Count|Celkem||Název, FailureType|
|TriggerSucceededRuns|Úspěšná aktivační událost spustí metriky|Count|Celkem||Název, FailureType|
|TriggerCancelledRuns|Zrušené aktivační události spustí metriky|Count|Celkem||Název, FailureType|
|IntegrationRuntimeCpuPercentage|Využití procesoru prostředí Integration runtime|Procento|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAvailableMemory|Dostupná paměť modulu runtime integrace|Bajty|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAverageTaskPickupDelay|Doba trvání fronty prostředí Integration runtime|Sekundy|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Délka fronty prostředí Integration runtime|Count|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Počet dostupných uzlů prostředí Integration runtime|Count|Průměr||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximální počet povolených entit|Count|Maximum||Žádný|
|MaxAllowedFactorySizeInGbUnits|Maximální povolená velikost továrny (jednotka GB)|Count|Maximum||Žádný|
|ResourceCount|Celkový počet entit|Count|Maximum||Žádný|
|FactorySizeInGbUnits|Celková velikost továrny (jednotka GB)|Count|Maximum||Žádný|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|JobEndedSuccess|Úspěšné úlohy|Count|Celkem|Počet úspěšných úloh|Žádný|
|JobEndedFailure|Neúspěšné úlohy|Count|Celkem|Počet neúspěšných úloh|Žádný|
|JobEndedCancelled|Zrušené úlohy|Count|Celkem|Počet zrušených úloh|Žádný|
|JobAUEndedSuccess|Úspěšná doba AU|Sekundy|Celkem|Celková doba AU pro úspěšné úlohy.|Žádný|
|JobAUEndedFailure|Neúspěšná doba aktualizace AU|Sekundy|Celkem|Celková doba AU pro neúspěšné úlohy.|Žádný|
|JobAUEndedCancelled|Čas AU se zrušil.|Sekundy|Celkem|Celková doba AU pro zrušené úlohy.|Žádný|
|JobStage|Úlohy ve fázi|Count|Celkem|Počet úloh v každé fázi.|Žádný|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TotalStorage|Celkové úložiště|Bajty|Maximum|Celková velikost dat uložených v účtu.|Žádný|
|Napsáno|Zapsaná data|Bajty|Celkem|Celkové množství dat zapsaných na účet.|Žádný|
|Čtení z|Přečtená data|Bajty|Celkem|Celkový objem dat načtených z účtu.|Žádný|
|WriteRequests|Požadavky na zápis|Count|Celkem|Počet požadavků na zápis dat na účet.|Žádný|
|ReadRequests|Žádosti o čtení|Count|Celkem|Počet požadavků na čtení dat pro účet.|Žádný|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ShareCount|Odeslané sdílené složky|Count|Maximum|Počet odeslaných sdílených složek v účtu|ShareName|
|ShareSubscriptionCount|Přijaté sdílené složky|Count|Maximum|Počet přijatých sdílených složek v účtu|ShareSubscriptionName|
|SucceededShareSynchronizations|Úspěšně se odeslaly snímky s úspěšným sdílením|Count|Count|Počet odeslaných snímků s úspěšným sdílením v účtu|Žádný|
|FailedShareSynchronizations|Odeslané neúspěšné snímky sdílené složky|Count|Count|Počet odeslaných snímků neúspěšných sdílení v účtu|Žádný|
|SucceededShareSubscriptionSynchronizations|Úspěšně přijaté sdílené snímky|Count|Count|Počet přijatých snímků s úspěšným sdílením v účtu|Žádný|
|FailedShareSubscriptionSynchronizations|Přijaté sdílené složky se nepodařilo vytvořit snímky.|Count|Count|Počet přijatých snímků neúspěšných sdílení v účtu|Žádný|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádný|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádný|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádný|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádný|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Průměr|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádný|
|seconds_behind_master|Prodleva replikace v sekundách|Count|Maximum|Prodleva replikace v sekundách|Žádný|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádný|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádný|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádný|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádný|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádný|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádný|
|seconds_behind_master|Prodleva replikace v sekundách|Count|Maximum|Prodleva replikace v sekundách|Žádný|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádný|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádný|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádný|
|io_consumption_percent|V/v procenta|Procento|Průměr|V/v procenta|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádný|
|storage_limit|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádný|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádný|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádný|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádný|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádný|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádný|
|backup_storage_used|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádný|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v sekundách|Žádný|
|pg_replica_log_delay_in_bytes|Maximální prodleva napříč replikami|Bajty|Maximum|Prodleva z největšího zpoždění repliky v bajtech|Žádný|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádný|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádný|
|IOPS|IOPS|Count|Průměr|Vstupně-výstupní operace za sekundu|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádný|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádný|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádný|
|memory_percent|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádný|
|IOPS|IOPS|Count|Průměr|Vstupně-výstupní operace za sekundu|Žádný|
|storage_percent|Procento úložiště|Procento|Průměr|Procento úložiště|Žádný|
|storage_used|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádný|
|active_connections|Aktivní připojení|Count|Průměr|Aktivní připojení|Žádný|
|network_bytes_egress|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádný|
|network_bytes_ingress|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádný|
|connections_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádný|
|connections_succeeded|Úspěšná připojení|Count|Celkem|Úspěšná připojení|Žádný|
|maximum_used_transactionIDs|Maximální počet použitých ID transakcí|Count|Průměr|Maximální počet použitých ID transakcí|Žádný|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|D2C. telemetrie. příchozí přenos dat allProtocol|Počet pokusů o odeslání zprávy telemetrie|Count|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádný|
|D2C. telemetrie. příchozí přenos dat. úspěch|Odeslané zprávy telemetrie|Count|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádný|
|C2D. Commands.. Complete. Complete. Success|Doručení zpráv C2D bylo dokončeno.|Count|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádný|
|C2D. Commands. odchozí. Abandon. Success|Zrušené zprávy C2D|Count|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádný|
|C2D. Commands. odchozí. remítat. Success|Odmítnuté zprávy C2D|Count|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádný|
|C2DMessagesExpired|C2D zprávy prošly (Preview)|Count|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádný|
|Devices. totalDevices|Celkem zařízení (zastaralé)|Count|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádný|
|Devices. connectedDevices. allProtocol|Připojená zařízení (zastaralé) |Count|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádný|
|D2C. telemetrie. odchozí. úspěch|Směrování: doručené zprávy telemetrie|Count|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádný|
|D2C. telemetrie. výstup. vyřazeno|Směrování: vyřazené zprávy telemetrie |Count|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádný|
|D2C. telemetrie. výstup. osamocený|Směrování: osamocené zprávy telemetrie |Count|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádný|
|D2C. telemetrie. invýstup. neplatné|Směrování: nekompatibilní zprávy telemetrie|Count|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádný|
|D2C. telemetrie. odchozí. Fallback|Směrování: zprávy doručené do záložního režimu|Count|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádný|
|D2C. Endpoints. odchozí. eventHubs|Směrování: zprávy doručené do centra událostí|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádný|
|D2C. Endpoints. latence. eventHubs|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádný|
|D2C. Endpoints. odchozí. serviceBusQueues|Směrování: zprávy doručené do fronty Service Bus|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádný|
|D2C. Endpoints. latence. serviceBusQueues|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádný|
|D2C. Endpoints. odchozí. serviceBusTopics|Směrování: zprávy doručené do Service Bus tématu|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádný|
|D2C. Endpoints. latence. serviceBusTopics|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádný|
|D2C. Endpoints. invýstups. builtIns. events|Směrování: zprávy doručené zprávám/událostem|Count|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události).|Žádný|
|D2C. Endpoints. latence. builtIn. events|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události)|Žádný|
|D2C. Endpoints. odchozí úložiště. Storage|Směrování: zprávy doručené do úložiště|Count|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádný|
|D2C. Endpoints. latence. Storage|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádný|
|D2C. Endpoints. invýstups. Storage. bytes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádný|
|D2C. Endpoints. výstup. Storage. BLOBs|Směrování: objekty blob doručené do úložiště|Count|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádný|
|EventGridDeliveries|Event Grid doručení (Preview)|Count|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události ( https://aka.ms/ioteventgrid) .|ResourceId, výsledek, EventType|
|EventGridLatency|Latence Event Grid (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|ResourceId, EventType|
|RoutingDeliveries|Směrování doručení (Preview)|Milisekund|Celkem|Počet pokusů IoT Hub o doručení zpráv do všech koncových bodů pomocí směrování. Chcete-li zobrazit počet úspěšných nebo neúspěšných pokusů, použijte výslednou dimenzi. Chcete-li zobrazit důvod selhání, jako je například neplatný, zrušený nebo osamocený, použijte dimenzi FailureReasonCategory. Můžete také použít dimenze koncový bod a EndpointType, abyste pochopili, kolik zpráv bylo doručeno do různých koncových bodů. Hodnota metriky se u každého pokusu o doručení zvyšuje o jednu, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručí do stejného koncového bodu víckrát.|ResourceId, EndpointType, koncové číslo, FailureReasonCategory, výsledek, RoutingSource|
|RoutingDeliveryLatency|Latence doručení směrování (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozím a příchozím zprávou pro IoT Hub a zprávy telemetrie do koncového bodu. Pomocí dimenzí koncového bodu a EndpointType můžete pochopit latenci různých koncových bodů.|ResourceId, EndpointType, koncový bod, RoutingSource|
|D2C. vláken. Read. Success|Úspěšné čtení ze zařízení|Count|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádný|
|D2C. nevlákenný. Read. Failure|Neúspěšná čtení ze zařízení|Count|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádný|
|D2C. nevlákenný. Read. Size|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádný|
|D2C. nevlákenná. Update. Success|Úspěšné nedokončené změny ze zařízení|Count|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádný|
|D2C. nevlákenná. aktualizace. selhání|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Count|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádný|
|D2C. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádný|
|C2D. Methods. Success|Úspěšná volání přímé metody|Count|Celkem|Počet všech úspěšných volání přímé metody.|Žádný|
|C2D. Methods. Failure|Neúspěšná volání přímé metody|Count|Celkem|Počet všech neúspěšných volání metody Direct|Žádný|
|C2D. Methods. requestSize|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádný|
|C2D. Methods. responseSize|Velikost odezvy volání přímých metod|Bajty|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádný|
|C2D. vláken. Read. Success|Úspěšné zdvojené čtení z back-endu|Count|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádný|
|C2D. nevlákenný. Read. Failure|Neúspěšné čtení z back-endu ze zadních vláken|Count|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádný|
|C2D. nevlákenný. Read. Size|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádný|
|C2D. nevlákenná. Update. Success|Úspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádný|
|C2D. nevlákenná. aktualizace. selhání|Neúspěšné zdvojené aktualizace z back-endu|Count|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádný|
|C2D. nevlákenná. Update. Size|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádný|
|twinQueries. Success|Úspěšné zdvojené dotazy|Count|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádný|
|twinQueries. selhání|Neúspěšné zdvojené dotazy|Count|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádný|
|twinQueries.resultSize|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádný|
|Jobs. createTwinUpdateJob. Success|Úspěšné vytváření zdvojených úloh aktualizace|Count|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádný|
|Jobs. createTwinUpdateJob. selhání|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Count|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádný|
|Jobs. createDirectMethodJob. Success|Úspěšné vytváření úloh vyvolání metod|Count|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádný|
|Jobs. createDirectMethodJob. selhání|Nepovedlo se vytvořit úlohy vyvolání metody|Count|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádný|
|Jobs. listJobs. Success|Úspěšná volání na seznam úloh|Count|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádný|
|Jobs. listJobs. selhání|Neúspěšná volání pro výpis úloh|Count|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádný|
|Jobs. cancelJob. Success|Úspěšná zrušení úlohy|Count|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádný|
|Jobs. cancelJob. selhání|Neúspěšná zrušení úloh|Count|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádný|
|Jobs. queryJobs. Success|Úspěšné dotazy na úlohy|Count|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádný|
|Jobs. queryJobs. selhání|Neúspěšné dotazy na úlohy|Count|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádný|
|dokončené úlohy|Dokončené úlohy|Count|Celkem|Počet všech dokončených úloh.|Žádný|
|úlohy. nezdařilo se|Neúspěšné úlohy|Count|Celkem|Počet všech neúspěšných úloh.|Žádný|
|D2C. telemetrie. příchozí přenos dat sendThrottle|Počet chyb omezování|Count|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádný|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Count|Průměr|Počet celkem aktuálně využívaných zpráv|Žádný|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádný|
|deviceDataUsageV2|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádný|
|totalDeviceCount|Celkem zařízení (Preview)|Count|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádný|
|connectedDeviceCount|Připojená zařízení (Preview)|Count|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádný|
|konfiguračních|Metriky konfigurace|Count|Celkem|Metriky pro operace konfigurace|Žádný|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RegistrationAttempts|Pokusy o registraci|Count|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Přiřazená zařízení|Count|Celkem|Počet zařízení přiřazených ke centru IoT Hub|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Pokusy o ověření identity|Count|Celkem|Počet pokusů o ověření identity zařízení|ProvisioningServiceName, status, protokol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AddRegion|Přidání oblasti|Count|Count|Přidání oblasti|Oblast|
|AvailableStorage|Dostupné úložiště|Bajty|Celkem|Celkové dostupné úložiště hlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Ukončení připojení Cassandra|Count|Celkem|Počet uzavřených připojení Cassandra, která se hlásí s členitou úrovní 1 minuty|APIType, oblast, ClosureReason|
|CassandraKeyspaceDelete|Odstraněné místo na Cassandra|Count|Count|Odstraněné místo na Cassandra|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|CassandraKeyspaceThroughputUpdate|Propustnost Cassandraho místa na disku se aktualizovala|Count|Count|Propustnost Cassandraho místa na disku se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra se aktualizované místo na disku|Count|Count|Cassandra se aktualizované místo na disku|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Poplatky za žádosti Cassandra|Count|Celkem|Ru spotřebované pro vytvořené požadavky Cassandra|APIType, DatabaseName, CollectionName, region, typem operace OperationType, ResourceType|
|CassandraRequests|Žádosti Cassandra|Count|Count|Počet provedených požadavků Cassandra|APIType, DatabaseName, CollectionName, region, typem operace OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Tabulka Cassandra se odstranila.|Count|Count|Tabulka Cassandra se odstranila.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|CassandraTableThroughputUpdate|Propustnost tabulky Cassandra se aktualizovala.|Count|Count|Propustnost tabulky Cassandra se aktualizovala.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Tabulka Cassandra se aktualizovala.|Count|Count|Tabulka Cassandra se aktualizovala.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Účet vytvořen|Count|Count|Účet vytvořen|Žádný|
|Využití datausage|Využití dat|Bajty|Celkem|Celkové využití dat nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|DeleteAccount|Účet se odstranil.|Count|Count|Účet se odstranil.|Žádný|
|DocumentCount|Počet dokumentů|Count|Celkem|Celkový počet dokumentů hlášených v rozmezí 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Kvóta dokumentu|Bajty|Celkem|Celková kvóta úložiště vykazovaná s členitosti 5 minut|CollectionName, DatabaseName, region|
|GremlinDatabaseDelete|Databáze Gremlin se odstranila.|Count|Count|Databáze Gremlin se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|GremlinDatabaseThroughputUpdate|Propustnost databáze Gremlin se aktualizovala|Count|Count|Propustnost databáze Gremlin se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Databáze Gremlin se aktualizovala.|Count|Count|Databáze Gremlin se aktualizovala.|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Graf Gremlin se odstranil.|Count|Count|Graf Gremlin se odstranil.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|GremlinGraphThroughputUpdate|Byla aktualizována propustnost grafu Gremlin|Count|Count|Byla aktualizována propustnost grafu Gremlin|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Graf Gremlin se aktualizoval.|Count|Count|Graf Gremlin se aktualizoval.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Využití indexu|Bajty|Celkem|Celkové využití indexu nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Žádosti o metadata|Count|Count|Počet požadavků na metadata Cosmos DB udržuje shromažďování systémových metadat pro každý účet, což vám umožní vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma.|DatabaseName, CollectionName, region, StatusCode, role|
|MongoCollectionDelete|Kolekce Mongo se odstranila.|Count|Count|Kolekce Mongo se odstranila.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|MongoCollectionThroughputUpdate|Byla aktualizována propustnost kolekce Mongo|Count|Count|Byla aktualizována propustnost kolekce Mongo|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Kolekce Mongo se aktualizovala|Count|Count|Kolekce Mongo se aktualizovala|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Databáze Mongo se aktualizovala.|Count|Count|Databáze Mongo se aktualizovala.|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Databáze Mongo se odstranila.|Count|Count|Databáze Mongo se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|MongoDatabaseThroughputUpdate|Propustnost databáze Mongo se aktualizovala|Count|Count|Propustnost databáze Mongo se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Poplatek za požadavek Mongo|Count|Celkem|Spotřebované jednotky žádosti Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequests|Žádosti Mongo|Count|Count|Počet provedených požadavků Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequestsCount|Počet požadavků Mongo|CountPerSecond|Průměr|Počet požadavků Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsDelete|Frekvence žádosti o odstranění Mongo|CountPerSecond|Průměr|Mongo žádosti o odstranění za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsInsert|Frekvence požadavků na vložení Mongo|CountPerSecond|Průměr|Mongo vložení počtu za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsQuery|Frekvence požadavků na dotaz Mongo|CountPerSecond|Průměr|Požadavek na dotaz Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|MongoRequestsUpdate|Frekvence požadavků na aktualizace Mongo|CountPerSecond|Průměr|Žádost o aktualizaci Mongo za sekundu|DatabaseName, CollectionName, region, příkaz, ErrorCode|
|NormalizedRUConsumption|Normalizovaná spotřeba RU|Procento|Maximum|Maximální procento spotřeby v procentech za minutu|CollectionName, DatabaseName, region|
|ProvisionedThroughput|Zřízená propustnost|Count|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|RegionFailover|Převzetí služeb při selhání oblasti|Count|Count|Převzetí služeb při selhání oblasti|Žádný|
|RemoveRegion|Oblast odebrána|Count|Count|Oblast odebrána|Oblast|
|ReplicationLatency|Latence replikace p99|Milisekund|Průměr|Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet|SourceRegion,TargetRegion|
|ServerSideLatency|Latence na straně serveru|Milisekund|Průměr|Latence na straně serveru|DatabaseName, CollectionName, region, ConnectionMode, typem operace OperationType, PublicAPIType|
|ServiceAvailability|Dostupnost služby|Procento|Průměr|Dostupnost žádostí o účet v časovém rozmezí jedné hodiny, dne nebo měsíce|Žádný|
|SqlContainerDelete|Kontejner SQL se odstranil.|Count|Count|Kontejner SQL se odstranil.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|SqlContainerThroughputUpdate|Propustnost kontejneru SQL se aktualizovala|Count|Count|Propustnost kontejneru SQL se aktualizovala|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Kontejner SQL se aktualizoval.|Count|Count|Kontejner SQL se aktualizoval.|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|Databáze SQL se odstranila|Count|Count|Databáze SQL se odstranila|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|SqlDatabaseThroughputUpdate|Propustnost SQL Database se aktualizovala|Count|Count|Propustnost SQL Database se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL Database se aktualizovala|Count|Count|SQL Database se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|Tabulka Azure se odstranila.|Count|Count|Tabulka Azure se odstranila.|ResourceName, ApiKind, ApiKindResourceType, typem operace OperationType|
|TableTableThroughputUpdate|Aktualizace propustnosti tabulky Azure|Count|Count|Aktualizace propustnosti tabulky Azure|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Tabulka Azure se aktualizovala|Count|Count|Tabulka Azure se aktualizovala|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Celkový počet jednotek žádostí|Count|Celkem|Spotřebované jednotky žádosti|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|TotalRequests|Požadavky celkem|Count|Count|Počet provedených požadavků|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|UpdateAccountKeys|Klíče účtu se aktualizovaly.|Count|Count|Klíče účtu se aktualizovaly.|KeyType|
|UpdateAccountNetworkSettings|Nastavení sítě účtu se aktualizovala.|Count|Count|Nastavení sítě účtu se aktualizovala.|Žádný|
|UpdateAccountReplicationSettings|Nastavení replikace účtu se aktualizovala|Count|Count|Nastavení replikace účtu se aktualizovala|Žádný|
|UpdateDiagnosticsSettings|Nastavení diagnostiky účtu se aktualizovala|Count|Count|Nastavení diagnostiky účtu se aktualizovala|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TransactionCount|Počet transakcí|Count|Count|Celkový počet transakcí|TransactionCount|
|SuccessCount|Počet úspěchů|Count|Count|Počet úspěšných transakcí|SuccessCount|
|FailureCount|Počet selhání|Count|Count|Počet neúspěšných transakcí|FailureCount|
|SuccessLatency|Latence úspěchu|Milisekund|Průměr|Latence úspěšných transakcí|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishFailCount|Publikovat neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádný|
|MatchedEventCount|Spárované události|Count|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Count|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Count|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádný|
|MatchedEventCount|Spárované události|Count|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason,EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Count|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Count|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádný|
|MatchedEventCount|Spárované události|Count|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason,EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Count|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Spárované události|Count|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádný|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Count|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádný|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádný|
|DroppedEventCount|Vyřazené události|Count|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Count|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Count|Celkem|Celkový počet událostí publikovaných na toto téma|Žádný|
|PublishFailCount|Publikovat neúspěšné události|Count|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Count|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádný|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádný|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|Celkem|Úspěšné požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Count|Celkem|Chyby serveru pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Count|Celkem|Chyby uživatele pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|QuotaExceededErrors|Chyby překročení kvóty.|Count|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|EntityName, výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Count|Celkem|Omezené požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Count|Celkem|Příchozí požadavky pro Microsoft. EventHub|EntityName|
|IncomingMessages|Příchozí zprávy|Count|Celkem|Příchozí zprávy pro Microsoft. EventHub|EntityName|
|OutgoingMessages|Odchozí zprávy|Count|Celkem|Odchozí zprávy pro Microsoft. EventHub|EntityName|
|IncomingBytes|Příchozí bajty|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub|EntityName|
|OutgoingBytes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub|EntityName|
|ActiveConnections|ActiveConnections|Count|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádný|
|ConnectionsOpened|Otevřená připojení.|Count|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|ConnectionsClosed|Zavřená připojení.|Count|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|CaptureBacklog|Zachyťte nevyřízené položky.|Count|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|EntityName|
|CapturedMessages|Zachycené zprávy.|Count|Celkem|Zachycené zprávy pro Microsoft. EventHub|EntityName|
|CapturedBytes|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub|EntityName|
|Velikost|Velikost|Bajty|Průměr|Velikost centra EventHub v bajtech|EntityName|
|INREQS|Příchozí požadavky (zastaralé)|Count|Celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů (zastaralé)|Žádný|
|SUCCREQ|Úspěšné požadavky (zastaralé)|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)|Žádný|
|FAILREQ|Neúspěšné žádosti (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádný|
|SVRBSY|Chyby zaneprázdněnosti serveru (zastaralé)|Count|Celkem|Celkový počet chyb zaneprázdněných serverem pro obor názvů (zastaralé)|Žádný|
|MEZI sebou|Interní chyby serveru (zastaralé)|Count|Celkem|Celkový počet interních chyb serveru pro obor názvů (zastaralé)|Žádný|
|MISCERR|Další chyby (zastaralé)|Count|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)|Žádný|
|INMSGS|Příchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích zpráv (zastaralé).|Žádný|
|EHINMSGS|Příchozí zprávy (zastaralé)|Count|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)|Žádný|
|OUTMSGS|Odchozí zprávy (zastaralé) (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích zpráv (zastaralé).|Žádný|
|EHOUTMSGS|Odchozí zprávy (zastaralé)|Count|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)|Žádný|
|EHINMBS|Příchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích bajtů (zastaralé).|Žádný|
|EHINBYTES|Příchozí bajty (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádný|
|EHOUTMBS|Odchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích bajtů (zastaralé).|Žádný|
|EHOUTBYTES|Odchozí bajty (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)|Žádný|
|EHABL|Archivovat nevyřízené zprávy (zastaralé)|Count|Celkem|Archivní zprávy centra událostí v backlogu pro obor názvů (zastaralé)|Žádný|
|EHAMSGS|Archivní zprávy (zastaralé)|Count|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)|Žádný|
|EHAMBS|Propustnost zpráv archivu (zastaralé)|Bajty|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)|Žádný|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|Celkem|Úspěšné požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Count|Celkem|Chyby serveru pro Microsoft. EventHub|Výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Count|Celkem|Chyby uživatele pro Microsoft. EventHub|Výsledek operace uvnitř|
|QuotaExceededErrors|Chyby překročení kvóty.|Count|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|Výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Count|Celkem|Omezené požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Count|Celkem|Příchozí požadavky pro Microsoft. EventHub|Žádný|
|IncomingMessages|Příchozí zprávy|Count|Celkem|Příchozí zprávy pro Microsoft. EventHub|Žádný|
|OutgoingMessages|Odchozí zprávy|Count|Celkem|Odchozí zprávy pro Microsoft. EventHub|Žádný|
|IncomingBytes|Příchozí bajty|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub|Žádný|
|OutgoingBytes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub|Žádný|
|ActiveConnections|ActiveConnections|Count|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub|Žádný|
|ConnectionsOpened|Otevřená připojení.|Count|Průměr|Otevřená připojení pro Microsoft. EventHub|Žádný|
|ConnectionsClosed|Zavřená připojení.|Count|Průměr|Otevřená připojení pro Microsoft. EventHub|Žádný|
|CaptureBacklog|Zachyťte nevyřízené položky.|Count|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|Žádný|
|CapturedMessages|Zachycené zprávy.|Count|Celkem|Zachycené zprávy pro Microsoft. EventHub|Žádný|
|CapturedBytes|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub|Žádný|
|Procesor|Procesor|Procento|Maximum|Využití CPU pro cluster centra událostí jako procento|Role|
|AvailableMemory|Paměť k dispozici|Procento|Maximum|Dostupná paměť pro cluster centra událostí jako procento z celkové paměti.|Role|
|Velikost|Velikost centra EventHub v bajtech|Bajty|Průměr|Velikost centra EventHub v bajtech|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|GatewayRequests|Žádosti o bránu|Count|Celkem|Počet žádostí o bránu|Stavu protokolu http|
|CategorizedGatewayRequests|Zařadit požadavky na bránu|Count|Celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|Stavu protokolu http|
|NumActiveWorkers|Počet aktivních pracovníků|Count|Maximum|Počet aktivních pracovníků|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ObservedMetricValue|Zaznamenaná hodnota metriky|Count|Průměr|Hodnota vypočítaná AutoScale při spuštění|MetricTriggerSource|
|MetricThreshold|Prahová hodnota metriky|Count|Průměr|Nakonfigurované prahové hodnoty automatického škálování, když se spustilo automatické škálování.|MetricTriggerRule|
|ObservedCapacity|Zjištěná kapacita|Count|Průměr|Kapacita nahlášená pro automatické škálování při jejím spuštění.|Žádný|
|ScaleActionsInitiated|Zahájené akce škálování|Count|Celkem|Směr operace škálování.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityResult/název, availabilityResult/umístění|
|availabilityResults/Count|Testy dostupnosti|Count|Count|Počet testů dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Doba trvání testu dostupnosti|Milisekund|Průměr|Doba trvání testu dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Doba připojení k síti – načtení stránky|Milisekund|Průměr|Doba mezi požadavkem uživatele a připojením k síti. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádný|
|browserTimings/processingDuration|Doba zpracování klienta|Milisekund|Průměr|Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky.|Žádný|
|browserTimings/receiveDuration|Doba přijetí odezvy|Milisekund|Průměr|Čas mezi prvním a posledním bajtů nebo až do odpojení|Žádný|
|browserTimings/sendDuration|Čas požadavku na odeslání|Milisekund|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádný|
|browserTimings/totalDuration|Doba načítání stránky v prohlížeči|Milisekund|Průměr|Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí.|Žádný|
|závislosti/počet|Volání závislostí|Count|Count|Počet volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/doba trvání|Doba trvání závislosti|Milisekund|Průměr|Doba trvání volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/selhání|Selhání volání závislostí|Count|Count|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|pageViews/Count|Zobrazení stránek|Count|Count|Počet zobrazení stránek|provoz/syntetické, cloudové/roleName|
|pageViews/doba trvání|Doba načítání zobrazení stránky|Milisekund|Průměr|Doba načítání zobrazení stránky|provoz/syntetické, cloudové/roleName|
|Čítače výkonu/requestExecutionTime|Doba provádění požadavku HTTP|Milisekund|Průměr|Čas provedení posledního požadavku.|Cloud/roleInstance|
|Čítače výkonu/requestsInQueue|Požadavky HTTP ve frontě aplikací|Count|Průměr|Délka fronty požadavků aplikace|Cloud/roleInstance|
|Čítače výkonu/requestsPerSecond|Rychlost požadavku HTTP|CountPerSecond|Průměr|Míra všech požadavků na aplikaci za sekundu z ASP.NET.|Cloud/roleInstance|
|Čítače výkonu/exceptionsPerSecond|Míra výjimek|CountPerSecond|Průměr|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|Cloud/roleInstance|
|Čítače výkonu/processIOBytesPerSecond|Rychlost zpracování v/v|BytesPerSecond|Průměr|Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení.|Cloud/roleInstance|
|Čítače výkonu/processCpuPercentage|PROCESOR procesů|Procento|Průměr|Procentuální hodnota uplynulého času, který všechny podprocesy procesu používají k provádění instrukcí. Může se lišit od 0 do 100. Tato metrika indikuje výkon samotného procesu W3wp.|Cloud/roleInstance|
|Čítače výkonu/processorCpuPercentage|Čas procesoru|Procento|Průměr|Procento času, které procesor stráví v nečinných vláknech|Cloud/roleInstance|
|Čítače výkonu/memoryAvailableBytes|Dostupná paměť|Bajty|Průměr|Fyzická paměť je okamžitě k dispozici pro přidělení procesu nebo pro použití systémem.|Cloud/roleInstance|
|Čítače výkonu/processPrivateBytes|Nesdílené bajty procesu|Bajty|Průměr|Paměť exkluzivně přiřazená k procesům monitorovaných aplikací.|Cloud/roleInstance|
|žádosti/doba trvání|Doba odezvy serveru|Milisekund|Průměr|Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/počet|Žádosti serveru|Count|Count|Počet dokončených požadavků HTTP|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/selhání|Neúspěšné požadavky|Count|Count|Počet požadavků HTTP označených jako neúspěšné Ve většině případů se jedná o žádosti s kódem odpovědi >= 400 a nerovná se 401.|požadavek/performanceBucket, žádosti/resultCode, požadavky/úspěch, provoz/syntetické, Cloud/roleInstance, Cloud/roleName|
|žádosti/rychlost|Počet požadavků serveru|CountPerSecond|Průměr|Frekvence požadavků serveru za sekundu|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|výjimky/počet|Výjimky|Count|Count|Kombinovaný počet všech nezachycených výjimek.|Cloud/roleName, Cloud/roleInstance, klient/typ|
|výjimky/prohlížeč|Výjimky prohlížečů|Count|Count|Počet nezachycených výjimek vyvolaných v prohlížeči|klient/server, Cloud/roleName|
|výjimky/Server|Výjimky serveru|Count|Count|Počet nezachycených výjimek vyvolaných v serverové aplikaci|klient/server, Cloud/roleName, Cloud/roleInstance|
|trasování/počet|Trasování|Count|Count|Počet dokumentů trasování|Trace/severityLevel, provozní/syntetické, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|connectedDeviceCount|Celkem připojených zařízení|Count|Průměr|Počet zařízení připojených k IoT Central|Žádný|
|C2D. Property. Read. Success|Úspěšné čtení vlastností zařízení z IoT Central|Count|Celkem|Počet všech úspěšných čtení vlastností zahájených z IoT Central|Žádný|
|C2D. Property. Read. Failure|Neúspěšná čtení vlastností zařízení z IoT Central|Count|Celkem|Počet všech neúspěšných čtení vlastností zahájených z IoT Central|Žádný|
|D2C. Property. Read. Success|Úspěšná čtení vlastností zařízení ze zařízení|Count|Celkem|Počet všech úspěšných čtení vlastností inicializovaných ze zařízení|Žádný|
|D2C. Property. Read. Failure|Neúspěšná čtení vlastností zařízení ze zařízení|Count|Celkem|Počet všech neúspěšných čtení vlastností inicializovaných ze zařízení|Žádný|
|C2D. Property. Update. Success|Úspěšná aktualizace vlastností zařízení z IoT Central|Count|Celkem|Počet všech úspěšných aktualizací vlastností zahájených z IoT Central|Žádný|
|C2D. Property. Update. Failure|Neúspěšné aktualizace vlastností zařízení z IoT Central|Count|Celkem|Počet všech neúspěšných aktualizací vlastností iniciované z IoT Central|Žádný|
|D2C. Property. Update. Success|Úspěšná aktualizace vlastností zařízení ze zařízení|Count|Celkem|Počet všech úspěšných aktualizací vlastností inicializovaných ze zařízení|Žádný|
|D2C. Property. Update. Failure|Neúspěšné aktualizace vlastností zařízení ze zařízení|Count|Celkem|Počet všech neúspěšných aktualizací vlastností inicializovaných ze zařízení|Žádný|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServiceApiHit|Celkový počet přístupů k rozhraní API služby|Count|Count|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|ServiceApiResult|Celkový počet výsledků rozhraní API služby|Count|Count|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|SaturationShoebox|Celkové sytosti trezoru|Procento|Průměr|Využitá kapacita trezoru|ActivityType, Activity, TransactionType|
|Dostupnost|Celková dostupnost trezoru|Procento|Průměr|Dostupnost žádostí o trezor|ActivityType, Activity, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CacheUtilization|Využití mezipaměti|Procento|Průměr|Úroveň využití v oboru clusteru|Žádný|
|QueryDuration|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazů v sekundách|QueryStatus|
|IngestionUtilization|Využití příjmu|Procento|Průměr|Poměr využitých slotů pro přijímání na clusteru|Žádný|
|Udržení|Zachovat naživu|Count|Průměr|Správnosti check indikuje, že cluster reaguje na dotazy|Žádný|
|IngestionVolumeInMB|Objem příjmu (v MB)|Count|Celkem|Celkový objem zpracovaných dat do clusteru (v MB)|databáze|
|IngestionLatencyInSeconds|Latence příjmu (v sekundách)|Sekundy|Průměr|Doba ingestování ze zdroje (např. zpráva je v centru EventHub) do clusteru v řádu sekund|Žádný|
|EventsProcessedForEventHubs|Zpracované události (pro Event/centra IoT)|Count|Celkem|Počet událostí zpracovaných clusterem při příjmu z události nebo IoT Hub|EventStatus|
|IngestionResult|Výsledek ingestování|Count|Count|Počet operací ingestování|IngestionResultDetails|
|Procesor|Procesor|Procento|Průměr|Úroveň využití procesoru|Žádný|
|ContinuousExportNumOfRecordsExported|Průběžný export – počet exportovaných záznamů|Count|Celkem|Počet exportovaných záznamů, které se vyvolaly pro každý artefakt úložiště zapsaný během operace exportu|ContinuousExportName, databáze|
|ExportUtilization|Využití exportu|Procento|Maximum|Využití exportu|Žádný|
|ContinuousExportPendingCount|Počet nevyřízených položek průběžného exportu|Count|Maximum|Počet probíhajících úloh průběžného exportu připravených k provedení|Žádný|
|ContinuousExportMaxLatenessMinutes|Maximální zpoždění průběžného exportu|Count|Maximum|Zpoždění (v minutách) hlášené úlohami průběžného exportu v clusteru|Žádný|
|ContinuousExportResult|Výsledek průběžného exportu|Count|Count|Indikuje, jestli se průběžný export zdařil nebo selhal.|ContinuousExportName, výsledek, databáze|
|StreamingIngestDuration|Doba ingestování streamování|Milisekund|Průměr|Doba ingestování streamování v milisekundách|Žádný|
|StreamingIngestDataRate|Přenosová rychlost ingestování datových proudů|Count|Průměr|Přenosová rychlost ingestování datových proudů (MB za sekundu)|Žádný|
|SteamingIngestRequestRate|Rychlost přijímání požadavků pro streamování|Count|RateRequestsPerSecond|Míra požadavků ingestování datových proudů (počet požadavků za sekundu)|Žádný|
|StreamingIngestResults|Výsledek ingestování streamování|Count|Průměr|Výsledek ingestování streamování|Výsledek|
|TotalNumberOfConcurrentQueries|Celkový počet souběžných dotazů|Count|Celkem|Celkový počet souběžných dotazů|Žádný|
|TotalNumberOfThrottledQueries|Celkový počet omezených dotazů|Count|Celkem|Celkový počet omezených dotazů|Žádný|
|TotalNumberOfThrottledCommands|Celkový počet příkazů s omezením|Count|Celkem|Celkový počet příkazů s omezením|CommandType|
|TotalNumberOfExtents|Celkový počet rozsahů|Count|Celkem|Celkový počet rozsahů dat|Žádný|
|InstanceCount|Počet instancí|Count|Průměr|Celkový počet instancí|Žádný|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet spuštěných pracovních postupů|Žádný|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet dokončených běhů pracovního postupu.|Žádný|
|RunsSucceeded|Úspěšná spuštění|Count|Celkem|Počet úspěšných běhů pracovního postupu.|Žádný|
|RunsFailed|Neúspěšná spuštění|Count|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádný|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet zrušených běhů pracovního postupu.|Žádný|
|RunLatency|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádný|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádný|
|RunThrottledEvents|Události omezeného spuštění|Count|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádný|
|RunStartThrottledEvents|Spustit omezené události|Count|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádný|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádný|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu.|Žádný|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádný|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet úspěšných akcí pracovního postupu.|Žádný|
|ActionsFailed|Neúspěšné akce |Count|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádný|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet vynechaných akcí pracovního postupu.|Žádný|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádný|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádný|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádný|
|TriggersStarted|Spuštěné aktivační události |Count|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádný|
|TriggersCompleted|Aktivační události dokončeny |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádný|
|TriggersSucceeded|Aktivační události byly úspěšné |Count|Celkem|Počet úspěšných triggerů pracovního postupu|Žádný|
|TriggersFailed|Neúspěšná triggery |Count|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádný|
|TriggersSkipped|Aktivační události přeskočeny|Count|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádný|
|TriggersFired|Aktivační události aktivovány |Count|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádný|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádný|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádný|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádný|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádný|
|BillableActionExecutions|Fakturovatelné provádění akcí|Count|Celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádný|
|BillableTriggerExecutions|Fakturovatelná spuštění triggerů|Count|Celkem|Počet fakturovaných provedení triggerů pracovního postupu|Žádný|
|TotalBillableExecutions|Fakturovatelná spuštění celkem|Count|Celkem|Počet fakturovaných provedení pracovního postupu|Žádný|
|BillingUsageNativeOperation|Využití fakturace pro provádění nativních operací|Count|Celkem|Počet fakturovaných provedení nativních operací|Žádný|
|BillingUsageStandardConnector|Využití fakturace pro spuštění standardních konektorů|Count|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádný|
|BillingUsageStorageConsumption|Využití fakturace pro provádění spotřeby úložiště|Count|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádný|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RunsStarted|Spuštěné běhy|Count|Celkem|Počet spuštěných pracovních postupů|Žádný|
|RunsCompleted|Dokončené běhy|Count|Celkem|Počet dokončených běhů pracovního postupu.|Žádný|
|RunsSucceeded|Úspěšná spuštění|Count|Celkem|Počet úspěšných běhů pracovního postupu.|Žádný|
|RunsFailed|Neúspěšná spuštění|Count|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádný|
|RunsCancelled|Zrušené běhy|Count|Celkem|Počet zrušených běhů pracovního postupu.|Žádný|
|RunLatency|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádný|
|RunSuccessLatency|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádný|
|RunThrottledEvents|Události omezeného spuštění|Count|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádný|
|RunStartThrottledEvents|Spustit omezené události|Count|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádný|
|RunFailurePercentage|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádný|
|ActionsStarted|Spuštěné akce |Count|Celkem|Počet spuštěných akcí pracovního postupu.|Žádný|
|ActionsCompleted|Dokončené akce |Count|Celkem|Počet dokončených akcí pracovního postupu.|Žádný|
|ActionsSucceeded|Úspěšné akce |Count|Celkem|Počet úspěšných akcí pracovního postupu.|Žádný|
|ActionsFailed|Neúspěšné akce |Count|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádný|
|ActionsSkipped|Vynechané akce |Count|Celkem|Počet vynechaných akcí pracovního postupu.|Žádný|
|ActionLatency|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádný|
|ActionSuccessLatency|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádný|
|ActionThrottledEvents|Omezené události akcí|Count|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádný|
|TriggersStarted|Spuštěné aktivační události |Count|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádný|
|TriggersCompleted|Aktivační události dokončeny |Count|Celkem|Počet dokončených triggerů pracovního postupu.|Žádný|
|TriggersSucceeded|Aktivační události byly úspěšné |Count|Celkem|Počet úspěšných triggerů pracovního postupu|Žádný|
|TriggersFailed|Neúspěšná triggery |Count|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádný|
|TriggersSkipped|Aktivační události přeskočeny|Count|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádný|
|TriggersFired|Aktivační události aktivovány |Count|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádný|
|TriggerLatency|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádný|
|TriggerFireLatency|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádný|
|TriggerSuccessLatency|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádný|
|TriggerThrottledEvents|Omezené události triggeru|Count|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádný|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Využití procesoru pracovního postupu pro prostředí integrační služby|Procento|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Využití paměti workflowu pro prostředí integrační služby|Procento|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Využití procesoru konektoru pro prostředí integrační služby|Procento|Průměr|Využití procesoru konektoru pro prostředí integrační služby.|Žádný|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Využití paměti konektoru pro prostředí integrační služby|Procento|Průměr|Využití paměti konektoru pro prostředí integrační služby.|Žádný|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Zrušená spuštění|Zrušená spuštění|Count|Celkem|Počet zrušených spuštění pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Zrušit požadovaná spuštění|Zrušit požadovaná spuštění|Count|Celkem|Počet spuštění, kde se pro tento pracovní prostor požádalo o zrušení|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Dokončená spuštění|Dokončená spuštění|Count|Celkem|Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Neúspěšná spuštění|Neúspěšná spuštění|Count|Celkem|Počet spuštění se pro tento pracovní prostor nezdařil.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Dokončují se běhy.|Dokončují se běhy.|Count|Celkem|Počet běhů, které vstoupily do stavu dokončení pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Neodpovídá spuštění|Neodpovídá spuštění|Count|Celkem|Počet běhů nereagujících na tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nespuštěná spuštění|Nespuštěná spuštění|Count|Celkem|Počet spuštění v neaktivním stavu pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Připravují se běhy.|Připravují se běhy.|Count|Celkem|Počet běhů, které se připravují pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštění zřizování|Spuštění zřizování|Count|Celkem|Počet spuštěných zřizování pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštění ve frontě|Spuštění ve frontě|Count|Celkem|Počet spuštění zařazených do fronty pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spuštěná spuštění|Spuštěná spuštění|Count|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Spouštění spuštění|Spouštění spuštění|Count|Celkem|Počet spuštění spuštěných pro tento pracovní prostor|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Chyby|Chyby|Count|Celkem|Počet chyb spuštění v tomto pracovním prostoru|Scénář|
|Upozornění|Upozornění|Count|Celkem|Počet upozornění spuštění v tomto pracovním prostoru|Scénář|
|Registr modelu byl úspěšný.|Registr modelu byl úspěšný.|Count|Celkem|Počet registrací modelů, které byly v tomto pracovním prostoru úspěšně dokončeny|Scénář|
|Nepovedlo se zaregistrovat model|Nepovedlo se zaregistrovat model|Count|Celkem|Počet registrací modelů, které se v tomto pracovním prostoru nezdařily|Scénář, StatusCode|
|Nasazení modelu spuštěn|Nasazení modelu spuštěn|Count|Celkem|Počet nasazení modelů spuštěných v tomto pracovním prostoru|Scénář|
|Nasazení modelu bylo úspěšné.|Nasazení modelu bylo úspěšné.|Count|Celkem|Počet nasazení modelů, která byla v tomto pracovním prostoru úspěšná|Scénář|
|Nasazení modelu se nezdařilo|Nasazení modelu se nezdařilo|Count|Celkem|Počet nasazení modelů, která v tomto pracovním prostoru selhala|Scénář, StatusCode|
|Celkem uzlů|Celkem uzlů|Count|Průměr|Počet uzlů celkem Tento součet zahrnuje některé aktivní uzly, nečinné uzly, nepoužité uzly, uzly Premepted a opouští uzly.|Scénář, název_clusteru|
|Aktivní uzly|Aktivní uzly|Count|Průměr|Počet uzlů Active Jedná se o uzly, které aktivně spouštějí úlohu.|Scénář, název_clusteru|
|Nečinné uzly|Nečinné uzly|Count|Průměr|Počet nečinných uzlů. Nečinné uzly jsou uzly, které nespouštějí žádné úlohy, ale mohou přijmout novou úlohu, je-li k dispozici.|Scénář, název_clusteru|
|Nepoužité uzly|Nepoužité uzly|Count|Průměr|Počet nepoužitelných uzlů. Nepoužité uzly nejsou funkční kvůli nějakému problému s nepřeložitelné. Azure bude tyto uzly recyklovat.|Scénář, název_clusteru|
|Přepnuté uzly|Přepnuté uzly|Count|Průměr|Počet zrušených uzlů. Tyto uzly jsou uzly s nízkou prioritou, které jsou mimo dostupný fond uzlů.|Scénář, název_clusteru|
|Ukončení uzlů|Ukončení uzlů|Count|Průměr|Počet opouštících uzlů. Ukončení uzlů jsou uzly, které právě dokončí zpracování úlohy a přestanou do stavu nečinnosti.|Scénář, název_clusteru|
|Celkový počet jader|Celkový počet jader|Count|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Aktivní jádra|Aktivní jádra|Count|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Jádra nečinných|Jádra nečinných|Count|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nepoužitelné jádra|Nepoužitelné jádra|Count|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Zrušené jádra|Zrušené jádra|Count|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Vynechávání jader|Vynechávání jader|Count|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Procento využití kvóty|Procento využití kvóty|Count|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Count|Průměr|PROCESOR (Preview)|Scénář, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Count|Průměr|GPU (Náhled)|Scénář, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/Accounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Využití|Využití|Count|Count|Počet volání rozhraní API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostupnost|Dostupnost|Procento|Průměr|Dostupnost rozhraní API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/starají

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech).|OutputFormat|
|SuccessE2ELatency|Koncová latence úspěch|Milisekund|Průměr|Průměrná latence pro úspěšné požadavky v milisekundách.|OutputFormat|
|Žádosti|Žádosti|Count|Celkem|Požadavky na koncový bod streamování.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetQuota|Kvóta prostředků|Count|Průměr|Počet povolených prostředků pro aktuální účet Media Service|Žádný|
|AssetCount|Počet assetů|Count|Průměr|Kolik prostředků již bylo vytvořeno v aktuálním účtu Media Service|Žádný|
|AssetQuotaUsedPercentage|Procento využité kvóty prostředků|Procento|Průměr|Procento využitého prostředku v aktuálním účtu Media Service|Žádný|
|ContentKeyPolicyQuota|Kvóta zásad pro klíč obsahu|Count|Průměr|Kolik zásad pro klíč obsahu je pro aktuální účet Media Service povolené|Žádný|
|ContentKeyPolicyCount|Počet zásad klíče obsahu|Count|Průměr|Kolik zásad pro klíč obsahu se už v aktuálním účtu Media Service vytvořilo|Žádný|
|ContentKeyPolicyQuotaUsedPercentage|Procento využité kvóty zásad klíčů obsahu|Procento|Průměr|Procento použitých zásad klíče obsahu v aktuálním účtu Media Service|Žádný|
|StreamingPolicyQuota|Kvóta zásad streamování|Count|Průměr|Počet povolených zásad streamování pro aktuální účet Media Service|Žádný|
|StreamingPolicyCount|Počet zásad streamování|Count|Průměr|Kolik zásad streamování už je v aktuálním účtu Media Service vytvořené|Žádný|
|StreamingPolicyQuotaUsedPercentage|Procento využité kvóty zásad streamování|Procento|Průměr|Procento využité zásady streamování v aktuálním účtu Media Service|Žádný|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetsConverted|Převedené prostředky|Count|Celkem|Celkový počet převedených prostředků|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktivní relace vykreslování|Count|Celkem|Celkový počet aktivních relací vykreslování|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/svazky

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageReadLatency|Průměrná latence čtení|Milisekund|Průměr|Průměrná latence čtení v milisekundách na operaci|Žádný|
|AverageWriteLatency|Průměrná latence zápisu|Milisekund|Průměr|Průměrná latence zápisu v milisekundách na operaci|Žádný|
|VolumeLogicalSize|Velikost spotřebovaného svazku|Bajty|Průměr|Logická velikost svazku (použité bajty)|Žádný|
|VolumeSnapshotSize|Velikost snímku svazku|Bajty|Průměr|Velikost všech snímků ve svazku|Žádný|
|ReadIops|Čtení IOPS|CountPerSecond|Průměr|Načíst vstupně-výstupní operace za sekundu|Žádný|
|WriteIops|Zápis IOPS|CountPerSecond|Průměr|Zapsat vstupně-výstupní operace za sekundu|Žádný|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Fond přidělený velikosti svazku|Bajty|Průměr|Přidělená velikost fondu, která se používá|Žádný|
|VolumePoolTotalLogicalSize|Velikost spotřebovaného fondu|Bajty|Průměr|Součet logické velikosti všech svazků patřících do fondu|Žádný|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesSentRate|Odeslané bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní odeslalo|Žádný|
|BytesReceivedRate|Přijaté bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní přijalo|Žádný|
|PacketsSentRate|Odeslané pakety|Count|Celkem|Počet paketů, které síťové rozhraní odeslalo|Žádný|
|PacketsReceivedRate|Přijaté pakety|Count|Celkem|Počet paketů, které síťové rozhraní přijalo|Žádný|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|VipAvailability|Dostupnost cesty k datům|Count|Průměr|Průměrná dostupnost cesty k datům Load Balancer za dobu trvání|FrontendIPAddress,FrontendPort|
|DipAvailability|Stav sondy stavu|Count|Průměr|Průměrný stav testu stavu Load Balancer za dobu trvání|Typprotokolu, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet odeslaných bajtů v časovém období|FrontendIPAddress, FrontendPort, směr|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet odeslaných paketů v časovém období|FrontendIPAddress, FrontendPort, směr|
|SYNCount|Počet SYN|Count|Celkem|Celkový počet odeslaných paketů SYN v časovém období|FrontendIPAddress, FrontendPort, směr|
|SnatConnectionCount|Počet připojení SNAT|Count|Celkem|Celkový počet nových připojení SNAT vytvořených během časového období|FrontendIPAddress, BackendIPAddress, vlastnost ConnectionState|
|AllocatedSnatPorts|Přidělené porty SNAT (Preview)|Count|Celkem|Celkový počet portů SNAT přidělených v rámci časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, IsAwaitingRemoval|
|UsedSnatPorts|Využité porty SNAT (Preview)|Count|Celkem|Celkový počet portů SNAT používaných během časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Count|Celkem|Počet dotazů poskytovaných pro zónu DNS|Žádný|
|RecordSetCount|Počet sad záznamů|Count|Maximum|Počet sad záznamů v zóně DNS|Žádný|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádný|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS příchozích paketů|CountPerSecond|Maximum|DDoS příchozích paketů|Žádný|
|PacketsDroppedDDoS|Vynechané příchozí pakety DDoS|CountPerSecond|Maximum|Vynechané příchozí pakety DDoS|Žádný|
|PacketsForwardedDDoS|DDoS předaných příchozích paketů|CountPerSecond|Maximum|DDoS předaných příchozích paketů|Žádný|
|TCPPacketsInDDoS|DDoS příchozí pakety TCP|CountPerSecond|Maximum|DDoS příchozí pakety TCP|Žádný|
|TCPPacketsDroppedDDoS|Zrušené příchozí pakety protokolu TCP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety protokolu TCP DDoS|Žádný|
|TCPPacketsForwardedDDoS|DDoS předaných paketů příchozího protokolu TCP|CountPerSecond|Maximum|DDoS předaných paketů příchozího protokolu TCP|Žádný|
|UDPPacketsInDDoS|Příchozí pakety UDP DDoS|CountPerSecond|Maximum|Příchozí pakety UDP DDoS|Žádný|
|UDPPacketsDroppedDDoS|Zrušené příchozí pakety UDP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety UDP DDoS|Žádný|
|UDPPacketsForwardedDDoS|Příchozí pakety UDP předané DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předané DDoS|Žádný|
|BytesInDDoS|Příchozí bajty DDoS|BytesPerSecond|Maximum|Příchozí bajty DDoS|Žádný|
|BytesDroppedDDoS|Příchozí bajty vynechané DDoS|BytesPerSecond|Maximum|Příchozí bajty vynechané DDoS|Žádný|
|BytesForwardedDDoS|Příchozí bajty předané DDoS|BytesPerSecond|Maximum|Příchozí bajty předané DDoS|Žádný|
|TCPBytesInDDoS|Příchozí bajty DDoS TCP|BytesPerSecond|Maximum|Příchozí bajty DDoS TCP|Žádný|
|TCPBytesDroppedDDoS|Příchozí bajty protokolu TCP vyhozené DDoS|BytesPerSecond|Maximum|Příchozí bajty protokolu TCP vyhozené DDoS|Žádný|
|TCPBytesForwardedDDoS|Příchozí DDoS předaných bajtů protokolu TCP|BytesPerSecond|Maximum|Příchozí DDoS předaných bajtů protokolu TCP|Žádný|
|UDPBytesInDDoS|Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP DDoS|Žádný|
|UDPBytesDroppedDDoS|Zrušené Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Zrušené Příchozí bajty UDP DDoS|Žádný|
|UDPBytesForwardedDDoS|Příchozí DDoS předávaných bajtů UDP|BytesPerSecond|Maximum|Příchozí DDoS předávaných bajtů UDP|Žádný|
|IfUnderDDoSAttack|V části útok DDoS nebo ne|Count|Maximum|V části útok DDoS nebo ne|Žádný|
|DDoSTriggerTCPPackets|Příchozí pakety TCP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci zmírnění DDoS|Žádný|
|DDoSTriggerUDPPackets|Příchozí pakety UDP pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci zmírnění DDoS|Žádný|
|DDoSTriggerSYNPackets|Příchozí pakety SYN pro aktivaci zmírnění DDoS|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci zmírnění DDoS|Žádný|
|VipAvailability|Dostupnost cesty k datům|Count|Průměr|Průměrná dostupnost IP adresy za dobu trvání|Port|
|ByteCount|Počet bajtů|Count|Celkem|Celkový počet odeslaných bajtů v časovém období|Port, směr|
|PacketCount|Počet paketů|Count|Celkem|Celkový počet odeslaných paketů v časovém období|Port, směr|
|SynCount|Počet SYN|Count|Celkem|Celkový počet odeslaných paketů SYN v časovém období|Port, směr|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Čas odezvy na virtuální počítač pomocí příkazů pro odeslání|Milisekund|Průměr|Čas odezvy pro příkazy pro odeslání do cílového virtuálního počítače|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Nepovedlo se odeslat příkazy do virtuálního počítače|Procento|Průměr|Procento počtu neúspěšných příkazů pro odeslání z testu na celkový počet odeslaných příkazů VMM cílového virtuálního počítače|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ApplicationRuleHit|Počet volání pravidel aplikace|Count|Celkem|Počet přístupů k pravidlům aplikace|Stav, důvod, protokol|
|NetworkRuleHit|Počet volání síťových pravidel|Count|Celkem|Počet přístupů k síťovým pravidlům|Stav, důvod, protokol|
|FirewallHealth|Stav brány firewall|Procento|Průměr|Stav brány firewall|Stav, důvod|
|Zpracováno na zpracování|Zpracovaná data|Bajty|Celkem|Celkový objem dat zpracovaných bránou firewall|Žádný|
|SNATPortUtilization|Využití portu SNAT|Procento|Průměr|Využití portu SNAT|Žádný|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Propustnost|Propustnost|BytesPerSecond|Průměr|Počet bajtů za sekundu, které Application Gateway zasloužily|Žádný|
|UnhealthyHostCount|Počet hostitelů není v pořádku|Count|Průměr|Počet nezdravých hostitelů back-endu|BackendSettingsPool|
|HealthyHostCount|Počet hostitelů v pořádku|Count|Průměr|Počet nefunkčních hostitelů back-endu|BackendSettingsPool|
|TotalRequests|Požadavky celkem|Count|Celkem|Počet úspěšných požadavků, které Application Gateway obsluhovány|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Počet požadavků za minutu na hostitele v pořádku|Count|Průměr|Průměrný počet požadavků za minutu na hostitele back-endu v pořádku ve fondu|BackendSettingsPool|
|FailedRequests|Neúspěšné požadavky|Count|Celkem|Počet neúspěšných žádostí, které Application Gateway obsluhovány|BackendSettingsPool|
|ResponseStatus|Stav odpovědi|Count|Celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|CurrentConnections|Aktuální připojení|Count|Celkem|Počet aktuálních připojení vytvořených pomocí Application Gateway|Žádný|
|NewConnectionsPerSecond|Nová připojení za sekundu|CountPerSecond|Průměr|Nová připojení za sekundu vytvořená Application Gateway|Žádný|
|CpuUtilization|Využití procesoru|Procento|Průměr|Aktuální využití CPU v Application Gateway|Žádný|
|CapacityUnits|Aktuální jednotky kapacity|Count|Průměr|Spotřebované jednotky kapacity|Žádný|
|FixedBillableCapacityUnits|Pevné fakturovatelné jednotky kapacity|Count|Průměr|Minimální jednotky kapacity, které se budou účtovat|Žádný|
|EstimatedBilledCapacityUnits|Odhadované jednotky s rozpisem kapacity|Count|Průměr|Odhadované jednotky kapacity, které se budou účtovat|Žádný|
|ComputeUnits|Aktuální výpočetní jednotky|Count|Průměr|Spotřebované výpočetní jednotky|Žádný|
|BackendResponseStatus|Stav odpovědi back-endu|Count|Celkem|Počet kódů odpovědí HTTP generovaných členy back-end. Nezahrnuje žádné kódy odpovědí vygenerované Application Gateway.|BackendServer, problémových, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protokol TLS klienta|Count|Celkem|Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil spojení s Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).|Naslouchací proces, TlsProtocol|
|BytesSent|Odeslané bajty|Bajty|Celkem|Celkový počet bajtů odeslaných Application Gateway klientům|Naslouchací proces|
|BytesReceived|Přijaté bajty|Bajty|Celkem|Celkový počet bajtů přijatých Application Gateway od klientů|Naslouchací proces|
|ClientRtt|Čas odezvy klienta|Milisekund|Průměr|Průměrná doba odezvy mezi klienty a Application Gateway. Tato metrika indikuje, jak dlouho trvá navázání připojení a vrácení potvrzení.|Naslouchací proces|
|ApplicationGatewayTotalTime|Čas Application Gateway celkem|Milisekund|Průměr|Průměrná doba, kterou trvá zpracování požadavku, a jeho odpověď k odeslání. Počítá se jako průměr intervalu od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že to obvykle zahrnuje dobu zpracování Application Gateway, čas, po který jsou pakety požadavků a odpovědí přenášeny přes síť, a čas, kdy server back-end trvala odpověď.|Naslouchací proces|
|BackendConnectTime|Čas připojení back-endu|Milisekund|Průměr|Čas strávený navázáním spojení s back-end serverem|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendFirstByteResponseTime|Doba odezvy prvního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a přijetí prvního bajtu hlavičky odpovědi, odhad doby zpracování back-endu serveru|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendLastByteResponseTime|Doba odezvy posledního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a příjem posledního bajtu textu odpovědi|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|MatchedCount|Distribuce všech pravidel v bráně firewall webových aplikací v1|Count|Celkem|Celkové rozdělení pravidla pro příchozí provoz z firewallu webových aplikací v1|Rules, RuleId|
|BlockedCount|Distribuce pravidla Blokované požadavky firewallu webových aplikací v1|Count|Celkem|Distribuce pravidla Blokované požadavky firewallu webových aplikací v1|Rules, RuleId|
|BlockedReqCount|Počet blokovaných požadavků firewallu webových aplikací v1|Count|Celkem|Počet blokovaných požadavků firewallu webových aplikací v1|Žádný|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AverageBandwidth|Šířka pásma S2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádný|
|P2SBandwidth|Šířka pásma P2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádný|
|P2SConnectionCount|Počet připojení P2S|Count|Maximum|Počet připojení typu point-to-site brány|Protokol|
|TunnelAverageBandwidth|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Počet bajtů výchozího přenosu dat tunelu|Bajty|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Počet bajtů příchozího přenosu dat tunelu|Bajty|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Počet paketů výchozího přenosu dat tunelu|Count|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunelové pakety pro příchozí připojení|Count|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Počet zahození paketů výchozího přenosu dat tunelu kvůli neshodě SP|Count|Celkem|Počet zahození odchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Počet zahození paketů příchozího přenosu dat tunelu kvůli neshodě SP|Count|Celkem|Počet zahození příchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Průměr|Úroveň světla příjmu v dBm|Odkaz, Lane|
|TxLightLevel|TxLightLevel|Count|Průměr|Úroveň Light tx v dBm|Odkaz, Lane|
|AdminState|AdminState|Count|Průměr|Stav Správce portu|Odkaz|
|LineProtocol|LineProtocol|Count|Průměr|Stav protokolu linky portu|Odkaz|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Odkaz|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Odkaz|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|PeeredCircuitSKey|
|BgpAvailability|Dostupnost protokolu BGP|Procento|Průměr|Dostupnost protokolu BGP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|ArpAvailability|Dostupnost protokolu ARP|Procento|Průměr|Dostupnost protokolu ARP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Průměr|Počet vyřazených bitů dat za sekundu|Žádný|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Průměr|Výstupní bity zahozených dat za sekundu|Žádný|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Žádný|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Žádný|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Žádný|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Žádný|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QpsByEndpoint|Dotazy podle vráceného koncového bodu|Count|Celkem|Počet vrácených Traffic Managerho koncového bodu v daném časovém rámci|Koncový bod|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stav koncového bodu podle koncového bodu|Count|Maximum|1, pokud je stav testu koncového bodu zapnuto, 0 jinak.|Koncový bod|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ProbesFailedPercent|% PROBE selhalo|Procento|Průměr|% sond monitorování připojení selhalo.|Žádný|
|AverageRoundtripMs|Průměrná doba odezvy (MS)|Milisekund|Průměr|Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem|Žádný|
|ChecksFailedPercent|Počet neúspěšných kontrol v procentech (Preview)|Procento|Průměr|% kontrol monitorování připojení selhalo.|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Doba odezvy (MS) (Preview)|Milisekund|Průměr|Doba odezvy v milisekundách pro kontroly monitorování připojení|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|RequestCount|Počet požadavků|Count|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Velikost požadavku|Bajty|Celkem|Počet bajtů odeslaných jako požadavek od klientů na proxy server HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Velikost odpovědi|Bajty|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Fakturovatelná velikost odpovědi|Bajty|Celkem|Počet fakturovatelných bajtů (minimálně 2 KB na požadavek) odeslaných jako odpověď z proxy serveru HTTP/S na klienty.|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Počet požadavků back-endu|Count|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S do back-endu|Stavu protokolu HTTP, HttpStatusGroup, back-end|
|BackendRequestLatency|Latence žádosti back-endu|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek odeslán proxy HTTP/S do back-endu do back-endu, dokud proxy protokolu HTTP/S nedostalo poslední bajt odpovědi z back-endu|Back-end|
|TotalLatency|Celková latence|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procento stavu back-endu|Procento|Průměr|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endu|Back-end, problémových|
|WebApplicationFirewallRequestCount|Počet požadavků firewallu webových aplikací|Count|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueryVolume|Svazek dotazu|Count|Celkem|Počet dotazů poskytovaných pro zónu Privátní DNS|Žádný|
|RecordSetCount|Počet sad záznamů|Count|Maximum|Počet sad záznamů v zóně Privátní DNS|Žádný|
|RecordSetCapacityUtilization|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou Privátní DNS|Žádný|
|VirtualNetworkLinkCount|Virtual Network počet odkazů|Count|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS|Žádný|
|VirtualNetworkLinkCapacityUtilization|Využití kapacity propojení Virtual Network|Procento|Maximum|Procento kapacity propojení Virtual Network využité zónou Privátní DNS|Žádný|
|VirtualNetworkWithRegistrationLinkCount|Počet odkazů na registraci Virtual Network|Count|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS s povolenou automatickou registrací|Žádný|
|VirtualNetworkWithRegistrationCapacityUtilization|Využití kapacity odkazu na registraci Virtual Network|Procento|Maximum|Procento Virtual Network propojení s kapacitou automatického registrace využívané Privátní DNS zónou|Žádný|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/obory názvů/NotificationHubs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|registrace. vše|Operace registrace|Count|Celkem|Počet všech úspěšných operací registrace (vytváření dotazů a odstraňování aktualizací). |Žádný|
|registrace. Create|Operace vytvoření registrace|Count|Celkem|Počet všech úspěšných vytvoření registrace.|Žádný|
|registrace. aktualizace|Operace aktualizace registrace|Count|Celkem|Počet všech úspěšných aktualizací registrace.|Žádný|
|registrace. Get|Registrace – operace čtení|Count|Celkem|Počet všech úspěšných dotazů na registraci.|Žádný|
|registrace. odstranění|Registrace – operace odstranění|Count|Celkem|Počet všech úspěšných odstranění registrací.|Žádný|
|přijíman|Příchozí zprávy|Count|Celkem|Počet všech úspěšných volání rozhraní API pro odeslání. |Žádný|
|příchozí. plánováno|Odeslaná naplánovaná nabízená oznámení|Count|Celkem|Zrušená plánovaná nabízená oznámení|Žádný|
|příchozí. plánováno. Cancel|Zrušená plánovaná nabízená oznámení|Count|Celkem|Zrušená plánovaná nabízená oznámení|Žádný|
|naplánováno. čeká na|Nedokončená plánovaná oznámení|Count|Celkem|Nedokončená plánovaná oznámení|Žádný|
|instalace. vše|Operace správy instalace|Count|Celkem|Operace správy instalace|Žádný|
|instalace. Get|Získat operace instalace|Count|Celkem|Získat operace instalace|Žádný|
|instalace. Upsert|Operace vytvoření nebo aktualizace instalace|Count|Celkem|Operace vytvoření nebo aktualizace instalace|Žádný|
|instalace. patch|Operace instalace opravy|Count|Celkem|Operace instalace opravy|Žádný|
|instalace. odstranit|Odstranit operace instalace|Count|Celkem|Odstranit operace instalace|Žádný|
|odchozí. allpns. úspěch|Úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádný|
|odchozí. allpns. invalidpayload|Chyby datové části|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS vrátil chybnou datovou část.|Žádný|
|odchozí. allpns. pnserror|Chyby systému externích oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože došlo k potížím při komunikaci s PNS (nezahrnuje problémy s ověřováním).|Žádný|
|odchozí. allpns. channelerror|Chyby kanálu|Count|Celkem|Počet nabízených oznámení, která selhala, protože kanál byl neplatný, ale není přidružený ke správné aplikaci nebo vypršela její platnost.|Žádný|
|odchozí. allpns. badorexpiredchannel|Chybné nebo prošlé chyby kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršela platnost kanálu/tokenu nebo registrationId, nebo je neplatná.|Žádný|
|odchozí. WNS. úspěch|WNS úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádný|
|odchozí. WNS. invalidcredentials|WNS – chyby autorizace (neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované. (Windows Live nerozpoznává přihlašovací údaje.)|Žádný|
|odchozí. WNS. badchannel|Chyba WNS špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav WNS: 404 nebyl nalezen).|Žádný|
|odchozí. WNS. expiredchannel|Chyba kanálu vypršení platnosti WNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože platnost parametr channeluri vypršela (stav WNS: 410 pryč).|Žádný|
|odchozí. WNS. omezení|WNS omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože WNS omezuje tuto aplikaci (stav WNS: 406 není přijatelné).|Žádný|
|odchozí. WNS. tokenproviderunreachable|WNS – chyby autorizace (nedostupné)|Count|Celkem|Systém Windows Live není dostupný.|Žádný|
|odchozí. WNS. invalidtoken|WNS – chyby autorizace (neplatný token)|Count|Celkem|Token poskytnutý pro WNS není platný (stav WNS: 401 Neautorizováno).|Žádný|
|odchozí. WNS. wrongtoken|WNS chyby autorizace (špatný token)|Count|Celkem|Token poskytnutý pro WNS je platný, ale pro jinou aplikaci (stav WNS: 403 zakázaný). K tomu může dojít, pokud je parametr channeluri v registraci přidružen k jiné aplikaci. Ověřte, že je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádný|
|odchozí. WNS. invalidnotificationformat|WNS neplatný formát oznámení|Count|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že WNS neumožňuje odmítat všechny neplatné datové části.|Žádný|
|odchozí. WNS. invalidnotificationsize|WNSá Chyba neplatné velikosti oznámení|Count|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádný|
|odchozí. WNS. channelthrottled|WNS kanál – omezení|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-NotificationStatus: channelThrottled).|Žádný|
|odchozí. WNS. channeldisconnected|WNS kanál odpojen|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-DeviceConnectionStatus: odpojeno).|Žádný|
|odchozí. WNS. vyřazeno|WNS Vyřazená oznámení|Count|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (X-WNS-NotificationStatus: vyřazeno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádný|
|odchozí. WNS. pnserror|WNS chyby|Count|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s WNS.|Žádný|
|odchozí. WNS. authenticationerror|WNS chyby ověřování|Count|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje nebo chybný token.|Žádný|
|odchozí. APNs. Success|Úspěšná oznámení APNS|Count|Celkem|Počet všech úspěšných oznámení|Žádný|
|odchozí. APNs. invalidcredentials|Chyby autorizace APNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádný|
|odchozí. APNs. badchannel|Chyba služby APN Bad Channel|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (Stavový kód služby APN: 8).|Žádný|
|odchozí. APNs. expiredchannel|Chyba kanálu vypršení platnosti služby APNS|Count|Celkem|Počet tokenů, u kterých se zrušila platnost kanálu zpětné vazby APNS.|Žádný|
|odchozí. APNs. invalidnotificationsize|APN – Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (Stavový kód APNS: 7).|Žádný|
|odchozí. APNs. pnserror|Chyby služby APN|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádný|
|odchozí. GCM. úspěch|GCM úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádný|
|odchozí. GCM. invalidcredentials|GCM – chyby autorizace (neplatné přihlašovací údaje)|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádný|
|odchozí. GCM. badchannel|Chyba GCM špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci nebyl rozpoznán (výsledek GCM: Neplatná registrace).|Žádný|
|odchozí. GCM. expiredchannel|Chyba kanálu vypršení platnosti GCM|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádný|
|odchozí. GCM. omezení|GCM omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože aplikace GCM omezila tuto aplikaci (kód stavu GCM: 501-599 nebo výsledek: nedostupný)|Žádný|
|odchozí. GCM. invalidnotificationformat|GCM neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část nebyla správně naformátována (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádný|
|odchozí. GCM. invalidnotificationsize|GCMá Chyba neplatné velikosti oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádný|
|odchozí. GCM. wrongchannel|GCM chybná chyba kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružená k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádný|
|odchozí. GCM. pnserror|GCM chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádný|
|odchozí. GCM. authenticationerror|GCM chyby ověřování|Count|Celkem|Počet nabízených oznámení, která selhala, protože PNS nepřijaly zadané přihlašovací údaje, jsou přihlašovací údaje blokované nebo SenderId není v aplikaci správně nakonfigurovaný (výsledek GCM: MismatchedSenderId).|Žádný|
|odchozí. MPNS. úspěch|MPNS úspěšná oznámení|Count|Celkem|Počet všech úspěšných oznámení|Žádný|
|odchozí. MPNS. invalidcredentials|Neplatné přihlašovací údaje MPNS|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádný|
|odchozí. MPNS. badchannel|Chyba MPNS špatného kanálu|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav MPNS: 404 nebyl nalezen).|Žádný|
|odchozí. MPNS. omezení|MPNS omezená oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 není přijatelné).|Žádný|
|odchozí. MPNS. invalidnotificationformat|MPNS neplatný formát oznámení|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část oznámení byla příliš velká.|Žádný|
|odchozí. MPNS. channeldisconnected|MPNS kanál odpojen|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci byl odpojen (stav MPNS: 412 nebyl nalezen).|Žádný|
|odchozí. MPNS. vyřazeno|MPNS Vyřazená oznámení|Count|Celkem|Počet nabídek, které byly vyřazeny pomocí MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo potlačeno).|Žádný|
|odchozí. MPNS. pnserror|MPNS chyby|Count|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádný|
|odchozí. MPNS. authenticationerror|MPNS chyby ověřování|Count|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádný|
|notificationhub. push|Všechna odchozí oznámení|Count|Celkem|Všechna odchozí oznámení centra oznámení|Žádný|
|příchozí. All. – žádosti|Všechny příchozí žádosti|Count|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádný|
|příchozí. All. failedrequests|Všechny příchozí neúspěšné požadavky|Count|Celkem|Celkový počet příchozích neúspěšných žádostí pro Centrum oznámení|Žádný|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Average_ uzlů inode% Free|% Bezplatného uzlů inode|Count|Průměr|Average_ uzlů inode% Free|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|% Volného místa|Count|Průměr|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využití uzlů inode|% Použitého uzlů inode|Count|Průměr|Average_% využití uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využité místo Average_%|% Využitého místa|Count|Průměr|Využité místo Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přečtených bajtů/s|Bajty čtení z disku/s|Count|Průměr|Average_Disk přečtených bajtů/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Čtení z disku/s|Count|Průměr|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosů za sekundu|Přenosy disku/s|Count|Průměr|Average_Disk přenosů za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bajty zápisu za sekundu|Bajty zápisu na disk/s|Count|Průměr|Average_Disk bajty zápisu za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Disk za sekundu|Zápisy na disk/s|Count|Průměr|Zápisy Average_Disk za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Volné megabajty|Count|Průměr|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Logical disku za sekundu|Bajty logického disku/s|Count|Průměr|Počet bajtů Average_Logical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ k dispozici paměti|% Dostupné paměti|Count|Průměr|Average_ k dispozici paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostupného odkládacího prostoru|% Dostupného odkládacího prostoru|Count|Průměr|Average_% dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využitá paměť Average_%|% Využité paměti|Count|Průměr|Využitá paměť Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využitého místa odkládacího souboru|% Využitého místa odkládacího souboru|Count|Průměr|Average_% využitého místa odkládacího souboru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Available v MB|Dostupná paměť v MB|Count|Průměr|Paměť Average_Available v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přeměna Average_Available MB|Dostupný počet MB swap|Count|Průměr|Přeměna Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page čtení za sekundu|Čtení stránek/s|Count|Průměr|Average_Page čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Page za sekundu|Zápisy stránek/s|Count|Průměr|Zápisy Average_Page za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stránky/s|Count|Průměr|Average_Pages/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB odkládacího prostoru|Využité místo odkládacího souboru v MB|Count|Průměr|Average_Used MB odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used paměť v MB|Využitá paměť v MB|Count|Průměr|Average_Used paměť v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přenesené bajty Average_Total|Celkový počet odeslaných bajtů|Count|Průměr|Přenesené bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté bajty Average_Total|Celkový počet přijatých bajtů|Count|Průměr|Přijaté bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Bajty celkem|Count|Průměr|Average_Total bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Total pakety|Celkový počet odeslaných paketů|Count|Průměr|Odeslané Average_Total pakety|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté pakety Average_Total|Celkový počet přijatých paketů|Count|Průměr|Přijaté pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total chyby příjmu|Celkový počet chyb příjmu|Count|Průměr|Average_Total chyby příjmu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Chyby při odesílání Average_Total|Chyby odesílání celkem|Count|Průměr|Chyby při odesílání Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizí|Celkový počet kolizí|Count|Průměr|Average_Total kolizí|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/čtení|Střední doba disku/čtení|Count|Průměr|Average_Avg. doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/přenos|Střední doba disku/přenos|Count|Průměr|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/zápis|Střední doba disku/zápis|Count|Průměr|Average_Avg. doba disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Physical disku za sekundu|Bajty fyzického disku/s|Count|Průměr|Počet bajtů Average_Physical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegovaného času|Privilegovaný čas protokolu PCT|Count|Průměr|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_Pct|Doba uživatele v protokolu PCT|Count|Průměr|Čas uživatele Average_Pct|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used kB paměti|Využitá paměť v kilobajtech|Count|Průměr|Average_Used kB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual sdílená paměť|Virtuální sdílená paměť|Count|Průměr|Average_Virtual sdílená paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času DPC|% Času DPC|Count|Průměr|Average_% času DPC|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času nečinnosti|% Času nečinnosti|Count|Průměr|Average_% času nečinnosti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času přerušení|% Času přerušení|Count|Průměr|Average_% času přerušení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času čekání na v/v|% Času čekání na v/v|Count|Průměr|Average_% času čekání na v/v|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dobrý čas|% Dobrý čas|Count|Průměr|Average_% dobrý čas|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegovaného času|% Privilegovaného času|Count|Průměr|Average_% privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|Procesorový čas v %|Count|Průměr|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_%|% Uživatelského času|Count|Průměr|Čas uživatele Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fyzická paměť|Volná fyzická paměť|Count|Průměr|Average_Free fyzická paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free mezera v stránkovacích souborech|Volné místo ve stránkovacích souborech|Count|Průměr|Average_Free mezera v stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuální paměti|Volná virtuální paměť|Count|Průměr|Average_Free virtuální paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Count|Průměr|Average_Processes|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size uložený ve stránkovacích souborech|Velikost uložená ve stránkovacích souborech|Count|Průměr|Average_Size uložený ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Doba provozu|Count|Průměr|Average_Uptime|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Uživatelé|Count|Průměr|Average_Users|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty disku Average_Current|Aktuální délka fronty disku|Count|Průměr|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostupné MB|Count|Průměr|Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% používaných potvrzených bajtů|% Používaných potvrzených bajtů|Count|Průměr|Average_% používaných potvrzených bajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté Average_Bytes za sekundu|Přijaté bajty/s|Count|Průměr|Přijaté Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Bytes za sekundu|Odeslané bajty/s|Count|Průměr|Odeslané Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes celkem/s|Bajty celkem/s|Count|Průměr|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty Average_Processor|Délka fronty procesoru|Count|Průměr|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tep|Tep|Count|Celkem|Tep|Počítač, OSType, verze, SourceComputerId|
|Aktualizace|Aktualizace|Count|Průměr|Aktualizace|Počítač, produkt, klasifikace, UpdateState, volitelné, schválené|
|Událost|Událost|Count|Průměr|Událost|Zdroj, protokol událostí, počítač, EventCategory, EventLevel, EventLevelName, ID události|

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
|QueryPoolJobQueueLength|Vlákna: délka fronty úloh fondu dotazů|Count|Průměr|Počet úloh ve frontě fondu vláken dotazů.|Žádné dimenze|
|qpu_high_utilization_metric|Vysoké využití procesoru|Count|Celkem|QPU vysoké využití za poslední minutu, 1 pro vysoké využití QPU, jinak 0|Žádné dimenze|
|memory_metric|Paměť|Bajty|Průměr|Memory: Rozsah 0-3 GB pro a1, 0-5 GB pro a2, 0-10 GB pro a3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6|Žádné dimenze|
|memory_thrashing_metric|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|Žádné dimenze|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/účty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuce prostředků podle klasifikace|Count|Celkem|Označuje počet prostředků s přiřazenou určitou klasifikací, tj. jsou klasifikovány s tímto popiskem.|Klasifikace, zdroj, ResourceId|
|AssetDistributionByStorageType|Distribuce prostředků podle typu úložiště|Count|Celkem|Označuje počet prostředků s určitým typem úložiště.|StorageType, ResourceId|
|CatalogActiveUsers|Každodenní aktivní uživatelé|Count|Celkem|Počet aktivních uživatelů denně|ResourceId|
|CatalogUsage|Distribuce využití podle operace|Count|Celkem|Označuje počet operací, které uživatel operace provede v katalogu, tj. přístup, hledání, Glosář.|Operace, ResourceId|
|NumberOfAssetsWithClassifications|Počet prostředků s minimálně jednou klasifikací|Count|Průměr|Určuje počet prostředků s nejméně jednou klasifikací značek.|ResourceId|
|ScanCancelled|Kontrola zrušena|Count|Celkem|Určuje počet zrušených kontrol.|ResourceId|
|ScanCompleted|Kontrola dokončena|Count|Celkem|Určuje počet kontrol, které byly úspěšně dokončeny.|ResourceId|
|ScanFailed|Kontrola se nezdařila|Count|Celkem|Indikuje, že počet kontrol se nezdařil.|ResourceId|
|ScanTimeTaken|Doba kontroly provedena|Sekundy|Celkem|Určuje celkovou dobu kontroly v sekundách.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ListenerConnections – úspěch|ListenerConnections – úspěch|Count|Celkem|Úspěšně se ListenerConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|ListenerConnections – ClientError|ListenerConnections – ClientError|Count|Celkem|ClientError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections – ServerError|ListenerConnections – ServerError|Count|Celkem|ServerError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections – úspěch|SenderConnections – úspěch|Count|Celkem|Úspěšně se SenderConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|SenderConnections – ClientError|SenderConnections – ClientError|Count|Celkem|ClientError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections – ServerError|SenderConnections – ServerError|Count|Celkem|ServerError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections – TotalRequests|ListenerConnections – TotalRequests|Count|Celkem|Total ListenerConnections for Microsoft. Relay|EntityName|
|SenderConnections – TotalRequests|SenderConnections – TotalRequests|Count|Celkem|Požadavky SenderConnections (celkem) pro Microsoft. Relay|EntityName|
|ActiveConnections|ActiveConnections|Count|Celkem|Total ActiveConnections for Microsoft. Relay|EntityName|
|ActiveListeners|ActiveListeners|Count|Celkem|Total ActiveListeners for Microsoft. Relay|EntityName|
|BytesTransferred|BytesTransferred|Count|Celkem|Total BytesTransferred for Microsoft. Relay|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Celkem|Total ListenerDisconnects for Microsoft. Relay|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Celkem|Total SenderDisconnects for Microsoft. Relay|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SearchLatency|Latence hledání|Sekundy|Průměr|Průměrná latence hledání pro vyhledávací službu|Žádný|
|SearchQueriesPerSecond|Hledání dotazů za sekundu|CountPerSecond|Průměr|Hledání dotazů za sekundu pro vyhledávací službu|Žádný|
|ThrottledSearchQueriesPercentage|Procento omezených vyhledávacích dotazů|Procento|Průměr|Procento vyhledávacích dotazů, které byly pro vyhledávací službu omezené|Žádný|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SuccessfulRequests|Úspěšné požadavky|Count|Celkem|Celkový počet úspěšných žádostí pro obor názvů|EntityName, výsledek operace uvnitř|
|ServerErrors|Chyby serveru.|Count|Celkem|Chyby serveru pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|UserErrors|Chyby uživatele.|Count|Celkem|Chyby uživatele pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|ThrottledRequests|Omezené požadavky.|Count|Celkem|Omezené požadavky pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|IncomingRequests|Příchozí žádosti|Count|Celkem|Příchozí požadavky pro Microsoft. ServiceBus|EntityName|
|IncomingMessages|Příchozí zprávy|Count|Celkem|Příchozí zprávy pro Microsoft. ServiceBus|EntityName|
|OutgoingMessages|Odchozí zprávy|Count|Celkem|Odchozí zprávy pro Microsoft. ServiceBus|EntityName|
|ActiveConnections|ActiveConnections|Count|Celkem|Celkový počet aktivních připojení pro Microsoft. ServiceBus|Žádný|
|ConnectionsOpened|Otevřená připojení.|Count|Průměr|Otevřená připojení pro Microsoft. ServiceBus|EntityName|
|ConnectionsClosed|Zavřená připojení.|Count|Průměr|Uzavřená připojení pro Microsoft. ServiceBus|EntityName|
|Velikost|Velikost|Bajty|Průměr|Velikost fronty nebo tématu v bajtech|EntityName|
|Zprávy|Počet zpráv ve frontě/tématu.|Count|Průměr|Počet zpráv ve frontě/tématu.|EntityName|
|ActiveMessages|Počet aktivních zpráv ve frontě nebo tématu.|Count|Průměr|Počet aktivních zpráv ve frontě nebo tématu.|EntityName|
|DeadletteredMessages|Počet nedoručených zpráv ve frontě nebo tématu|Count|Průměr|Počet nedoručených zpráv ve frontě nebo tématu|EntityName|
|ScheduledMessages|Počet naplánovaných zpráv ve frontě nebo tématu.|Count|Průměr|Počet naplánovaných zpráv ve frontě nebo tématu.|EntityName|
|NamespaceCpuUsage|Procesor|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium.|Replika|
|NamespaceMemoryUsage|Využití paměti|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium|Replika|
|CPUXNS|PROCESOR (zastaralé)|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium. Tato metrika je depricated. Místo toho prosím použijte metriku CPU (NamespaceCpuUsage).|Replika|
|WSXNS|Využití paměti (zastaralé)|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium Tato metrika je zastaralá. Místo toho prosím použijte metriku využití paměti (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikace

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Průměr|Procesor přidělený tomuto kontejneru v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bajty|Průměr|Paměť přidělená tomuto kontejneru v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Průměr|Skutečné využití CPU v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bajty|Průměr|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Průměr|Stav aplikace Service Fabric mřížka|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Průměr|Stav služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Průměr|Stav repliky služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Průměr|Stav kontejneru v aplikaci Service Fabric mřížka|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Count|Průměr|Restartování počtu kontejnerů v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ConnectionCount|Počet připojení|Count|Maximum|Množství připojení uživatele.|Koncový bod|
|MessageCount|Počet zpráv|Count|Celkem|Celková velikost zpráv|Žádný|
|InboundTraffic|Příchozí provoz|Bajty|Celkem|Příchozí provoz služby|Žádný|
|OutboundTraffic|Odchozí provoz|Bajty|Celkem|Odchozí provoz služby|Žádný|
|UserErrors|Chyby uživatele|Procento|Maximum|Procento uživatelských chyb|Žádný|
|SystemErrors|Systémové chyby|Procento|Maximum|Procento systémových chyb|Žádný|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádný|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádný|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v. Neplatí pro datové sklady.|Žádný|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro databáze založené na DTU.|Žádný|
|úložiště|Využité místo pro data|Bajty|Maximum|Využité místo pro data Neplatí pro datové sklady.|Žádný|
|connection_successful|Úspěšná připojení|Count|Celkem|Úspěšná připojení|Žádný|
|connection_failed|Neúspěšná připojení|Count|Celkem|Neúspěšná připojení|Žádný|
|blocked_by_firewall|Blokováno bránou firewall|Count|Celkem|Blokováno bránou firewall|Žádný|
|ukončení|Zablokování|Count|Celkem|Zablokování. Neplatí pro datové sklady.|Žádný|
|storage_percent|Procento využitého datového prostoru|Procento|Maximum|Procento využitého datového prostoru Neplatí pro datové sklady nebo databáze na úrovni dat.|Žádný|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti Neplatí pro datové sklady.|Žádný|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů Neplatí pro datové sklady.|Žádný|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací Neplatí pro datové sklady.|Žádný|
|dtu_limit|Limit DTU|Count|Průměr|Limit DTU. Platí pro databáze založené na DTU.|Žádný|
|dtu_used|Využité DTU|Count|Průměr|Používá se DTU. Platí pro databáze založené na DTU.|Žádný|
|cpu_limit|Limit procesoru|Count|Průměr|Limit procesoru. Platí pro databáze založené na vCore.|Žádný|
|cpu_used|Využitý procesor|Count|Průměr|Využitý procesor. Platí pro databáze založené na vCore.|Žádný|
|dwu_limit|DWU limit|Count|Maximum|DWU limit. Platí jenom pro datové sklady.|Žádný|
|dwu_consumption_percent|Procento DWU|Procento|Maximum|Procento DWU Platí jenom pro datové sklady.|Žádný|
|dwu_used|DWU použito|Count|Maximum|DWU použito. Platí jenom pro datové sklady.|Žádný|
|cache_hit_percent|Procento přístupů do mezipaměti|Procento|Maximum|Procento přístupů do mezipaměti Platí jenom pro datové sklady.|Žádný|
|cache_used_percent|Procento využité mezipaměti|Procento|Maximum|Procento využité mezipaměti Platí jenom pro datové sklady.|Žádný|
|sqlserver_process_core_percent<sup>1</sup> |Základní procento procesu SQL Server|Procento|Maximum|Procento využití procesoru pro proces SQL Server, jak je měřené operačním systémem.|Žádný|
|sqlserver_process_memory_percent<sup>1</sup> |% SQL Server paměti procesu|Procento|Maximum|Procento využití paměti pro proces SQL Server, jak je měřené operačním systémem.|Žádný|
|tempdb_data_size<sup>1</sup> |Velikost datového souboru tempdb v kilobajtech|Count|Maximum|Velikost datového souboru tempdb v kilobajtech|Žádný|
|tempdb_log_size<sup>1</sup> |Velikost souboru protokolu tempdb v kilobajtech|Count|Maximum|Velikost souboru protokolu tempdb v kilobajtech|Žádný|
|tempdb_log_used_percent<sup>1</sup> |Použit protokol tempdb v procentech|Procento|Maximum|Byl použit protokol tempdb Percent.|Žádný|
|local_tempdb_usage_percent|Místní procento databáze tempdb|Procento|Průměr|Místní procento databáze tempdb. Platí jenom pro datové sklady.|Žádný|
|app_cpu_billed|CPU aplikace se fakturuje.|Count|Celkem|CPU aplikace se fakturuje. Platí pro databáze bez serveru.|Žádný|
|app_cpu_percent|Procento využití procesoru aplikací|Procento|Průměr|Procento využití procesoru aplikací Platí pro databáze bez serveru.|Žádný|
|app_memory_percent|Procento paměti aplikace|Procento|Průměr|Procento paměti aplikace. Platí pro databáze bez serveru.|Žádný|
|allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené úložiště dat. Neplatí pro datové sklady.|Žádný|
|memory_usage_percent|Procento paměti|Procento|Maximum|Procento paměti. Platí jenom pro datové sklady.|Žádný|
|dw_backup_size_gb|Velikost úložiště dat|Count|Celkem|Velikost úložiště dat se skládá z velikosti dat a protokolu transakcí. Metrika se počítá směrem k části úložiště vašeho vyúčtování. Platí jenom pro datové sklady.|Žádný|
|dw_snapshot_size_gb|Velikost úložiště snímků|Count|Celkem|Velikost úložiště snímků je velikost přírůstkových změn zachycených snímky k vytvoření uživatelem definovaných a automatických bodů obnovení. Metrika se počítá směrem k části úložiště vašeho vyúčtování. Platí jenom pro datové sklady.|Žádný|
|dw_geosnapshot_size_gb|Velikost úložiště pro zotavení po havárii|Count|Celkem|Velikost úložiště pro zotavení po havárii se ve vašem vyúčtování projeví jako úložiště zotavení po havárii. Platí jenom pro datové sklady.|Žádný|
|wlg_allocation_relative_to_system_percent|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Přidělené procento prostředků relativních k celému systému na skupinu úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Přidělení skupiny úloh podle zakončení prostředků v procentech|Procento|Maximum|Přidělené procento prostředků vzhledem k zadaným prostředkům Cap na skupinu úloh Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktivní dotazy skupiny úloh|Count|Celkem|Aktivní dotazy v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Dotazování skupin úloh ve frontě|Count|Celkem|Dotazy zařazené do fronty v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktivní dotazy|Count|Celkem|Aktivní dotazy napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádný|
|queued_queries|Dotazy ve frontě|Count|Celkem|Dotazy ve frontě napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádný|
|wlg_active_queries_timeouts|Vypršení časového limitu dotazu skupiny úloh|Count|Celkem|Dotazy, jejichž časový limit pro skupinu úloh vypršel. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Efektivní minimální procento prostředků|Procento|Maximum|Minimální procento prostředků rezervovaných a izolovaných pro skupinu úloh s ohledem na minimum úrovně služby. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Procento efektivního Cap prostředku|Procento|Maximum|Pevné omezení procentuální hodnoty prostředků povolených pro skupinu úloh s ohledem na efektivní minimální procento prostředků přidělených pro jiné skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Velikost úložiště pro úplné zálohování|Bajty|Maximum|Celková velikost úložiště pro úplné zálohování Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádný|
|diff_backup_size_bytes|Rozdílová velikost úložiště zálohování|Bajty|Maximum|Kumulativní velikost úložiště rozdílové zálohy. Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádný|
|log_backup_size_bytes|Velikost úložiště zálohy protokolu|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního protokolu. Platí pro databáze založené na vCore a na škálovatelných databázích.|Žádný|
|snapshot_backup_size_bytes|Velikost úložiště záloh snímků|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního snímku. Platí pro databáze s škálovatelným škálováním.|Žádný|
|base_blob_size_bytes|Základní velikost úložiště objektů BLOB|Bajty|Maximum|Základní velikost úložiště objektů BLOB Platí pro databáze s škálovatelným škálováním.|Žádný|

<sup>1</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní model založený na DTU. 

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/servery/elasticPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|Žádný|
|database_cpu_percent|Procento CPU|Procento|Průměr|Procento CPU|DatabaseResourceId|
|physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádný|
|database_physical_data_read_percent|Procento datových V/V|Procento|Průměr|Procento datových V/V|DatabaseResourceId|
|log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|Žádný|
|database_log_write_percent|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|DatabaseResourceId|
|dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU Platí pro elastické fondy založené na DTU.|Žádný|
|database_dtu_consumption_percent|Procento DTU|Procento|Průměr|Procento DTU|DatabaseResourceId|
|storage_percent|Procento využitého datového prostoru|Procento|Průměr|Procento využitého datového prostoru|Žádný|
|workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|Žádný|
|database_workers_percent|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|DatabaseResourceId|
|sessions_percent|Procento relací|Procento|Průměr|Procento relací|Žádný|
|database_sessions_percent|Procento relací|Procento|Průměr|Procento relací|DatabaseResourceId|
|eDTU_limit|limit eDTU|Count|Průměr|limit eDTU. Platí pro elastické fondy založené na DTU.|Žádný|
|storage_limit|Maximální velikost dat|Bajty|Průměr|Maximální velikost dat|Žádný|
|eDTU_used|využité eDTU|Count|Průměr|používá se eDTU. Platí pro elastické fondy založené na DTU.|Žádný|
|database_eDTU_used|využité eDTU|Count|Průměr|využité eDTU|DatabaseResourceId|
|storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|Žádný|
|database_storage_used|Využité místo pro data|Bajty|Průměr|Využité místo pro data|DatabaseResourceId|
|xtp_storage_percent|Procentuální hodnota úložiště OLTP v paměti|Procento|Průměr|Procentuální hodnota úložiště OLTP v paměti|Žádný|
|cpu_limit|Limit procesoru|Count|Průměr|Limit procesoru. Platí pro elastické fondy založené na vCore.|Žádný|
|database_cpu_limit|Limit procesoru|Count|Průměr|Limit procesoru|DatabaseResourceId|
|cpu_used|Využitý procesor|Count|Průměr|Využitý procesor. Platí pro elastické fondy založené na vCore.|Žádný|
|database_cpu_used|Využitý procesor|Count|Průměr|Využitý procesor|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Základní procento procesu SQL Server|Procento|Maximum|Procento využití procesoru pro proces SQL Server, jak je měřené operačním systémem. Platí pro elastické fondy. |Žádný|
|sqlserver_process_memory_percent<sup>1</sup>|% SQL Server paměti procesu|Procento|Maximum|Procento využití paměti pro proces SQL Server, jak je měřené operačním systémem. Platí pro elastické fondy. |Žádný|
|tempdb_data_size<sup>1</sup>|Velikost datového souboru tempdb v kilobajtech|Count|Maximum|Velikost datového souboru tempdb v kilobajtech|Žádný|
|tempdb_log_size<sup>1</sup>|Velikost souboru protokolu tempdb v kilobajtech|Count|Maximum|Velikost souboru protokolu tempdb v kilobajtech |Žádný|
|tempdb_log_used_percent<sup>1</sup>|Použit protokol tempdb v procentech|Procento|Maximum|Byl použit protokol tempdb Percent.|Žádný|
|allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|Žádný|
|database_allocated_data_storage|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|DatabaseResourceId|
|allocated_data_storage_percent|Procentuální hodnota přiděleného datového prostoru|Procento|Maximum|Procentuální hodnota přiděleného datového prostoru|Žádný|

<sup>1</sup> Tato metrika je dostupná pro databáze používající model nákupu Vcore s 2 virtuální jádra a vyšší nebo 200 DTU a vyšší pro nákupní model založený na DTU. 

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|virtual_core_count|Počet virtuálních jader|Count|Průměr|Počet virtuálních jader|Žádný|
|avg_cpu_percent|Průměrné procento procesoru|Procento|Průměr|Průměrné procento procesoru|Žádný|
|reserved_storage_mb|Rezervované místo v úložišti|Count|Průměr|Rezervované místo v úložišti|Žádný|
|storage_space_used_mb|Využité místo úložiště|Count|Průměr|Využité místo úložiště|Žádný|
|io_requests|Počet požadavků v/v|Count|Průměr|Počet požadavků v/v|Žádný|
|io_bytes_read|Přečtené vstupně-výstupní bajty|Bajty|Průměr|Přečtené vstupně-výstupní bajty|Žádný|
|io_bytes_written|Zapsané vstupně-výstupní bajty|Bajty|Průměr|Zapsané vstupně-výstupní bajty|Žádný|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|UsedCapacity|Využitá kapacita|Bajty|Průměr|Kapacita využitá účtem|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BlobCapacity|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|Počet objektů BLOB|Count|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Počet kontejnerů objektů BLOB|Count|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádný|
|IndexCapacity|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|TableCapacity|Kapacita tabulky|Bajty|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádný|
|TableCount|Počet tabulek|Count|Průměr|Počet tabulek v Table service účtu úložiště|Žádný|
|TableEntityCount|Počet entit tabulky|Count|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Kapacita zařízení|Kapacita souboru|Bajty|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Sdílená složka|
|FileCount|Počet souborů|Count|Průměr|Počet souborů v Souborové službě účtu úložiště.|Sdílená složka|
|FileShareCount|Počet sdílených souborů|Count|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádný|
|FileShareSnapshotCount|Počet snímků sdílené složky|Count|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílená složka|
|FileShareSnapshotSize|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílená složka|
|FileShareCapacityQuota|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílená složka|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|QueueCapacity|Kapacita fronty|Bajty|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádný|
|QueueCount|Počet front|Count|Průměr|Počet front v Služba front účtu úložiště.|Žádný|
|QueueMessageCount|Počet zpráv ve frontě|Count|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádný|
|Transakce|Transakce|Count|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|Příchozí přenos dat|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|SuccessServerLatency|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Průměrná latence E2E.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|Dostupnost|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/caches

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ClientIOPS|Celkový počet vstupně-výstupních operací klienta|Count|Průměr|Frekvence operací souborů klienta zpracovávaných mezipamětí.|Žádný|
|ClientLatency|Průměrná latence klienta|Milisekund|Průměr|Průměrná latence operací s klientskými soubory do mezipaměti úložiště|Žádný|
|ClientReadIOPS|Klientský vstupně-výstupní operace čtení|CountPerSecond|Průměr|Klientské operace čtení za sekundu.|Žádný|
|ClientReadThroughput|Průměrná propustnost čtení mezipaměti|BytesPerSecond|Průměr|Rychlost přenosu dat čtení klienta.|Žádný|
|ClientWriteIOPS|Zápis klientského IOPS za sekundu|CountPerSecond|Průměr|Operace zápisu klienta za sekundu|Žádný|
|ClientWriteThroughput|Průměrná propustnost zápisu mezipaměti|BytesPerSecond|Průměr|Přenosová rychlost zápisu dat klienta.|Žádný|
|ClientMetadataReadIOPS|Metadata klienta přečetla IOPS|CountPerSecond|Průměr|Frekvence operací s klientskými soubory odesílanými do mezipaměti s výjimkou čtení dat, která nemění trvalý stav.|Žádný|
|ClientMetadataWriteIOPS|Zápis metadat klienta za sekundu|CountPerSecond|Průměr|Počet operací klientského souboru odeslaných do mezipaměti s výjimkou zápisů dat, které upravují trvalý stav.|Žádný|
|ClientLockIOPS|IOPS zámku klienta|CountPerSecond|Průměr|Operace uzamčení souborů klienta za sekundu|Žádný|
|StorageTargetHealth|Stav cíle úložiště|Count|Průměr|Logické výsledky testu připojení mezi cíli mezipaměti a úložištěm.|Žádný|
|Doba provozu|Doba provozu|Count|Průměr|Logické výsledky testu připojení mezi mezipamětí a systémem monitorování.|Žádný|
|StorageTargetIOPS|Celkem StorageTarget IOPS|Count|Průměr|Frekvence všech operací se soubory, které mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget zápis IOPS|Count|Průměr|Frekvence operací zápisu souborů, které mezipaměť odesílá do konkrétní StorageTarget.|StorageTarget|
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
|ServerSyncSessionResult|Výsledek relace synchronizace|Count|Průměr|Metrika, která protokoluje hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizované soubory|Count|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Count|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Online stav serveru|Count|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Velikost odvolání při vyvolání vrstvy cloudu|Bajty|Celkem|Velikost vrácených dat|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Propustnost volání při navracení cloudových vrstev|BytesPerSecond|Průměr|Velikost propustnosti odvolání dat|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Velikost odvolání při vrstvení cloudu podle aplikace|Bajty|Celkem|Velikost dat vrácených aplikací|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synchronizované soubory|Count|Celkem|Počet synchronizovaných souborů|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Count|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synchronizované soubory|Count|Celkem|Počet synchronizovaných souborů|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Soubory se nesynchronizují|Count|Celkem|Počet souborů, jejichž synchronizace se nezdařila|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ServerHeartbeat|Online stav serveru|Count|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|ResourceUtilization|Využití SU%|Procento|Maximum|Využití SU%|ID logického, PartitionId|
|InputEvents|Události vstupu|Count|Celkem|Události vstupu|ID logického, PartitionId|
|InputEventBytes|Bajty vstupních událostí|Bajty|Celkem|Bajty vstupních událostí|ID logického, PartitionId|
|LateInputEvents|Zpožděné vstupní události|Count|Celkem|Zpožděné vstupní události|ID logického, PartitionId|
|OutputEvents|Výstupní události|Count|Celkem|Výstupní události|ID logického, PartitionId|
|ConversionErrors|Chyby převodu dat|Count|Celkem|Chyby převodu dat|ID logického, PartitionId|
|Chyby|Běhové chyby|Count|Celkem|Běhové chyby|ID logického, PartitionId|
|DroppedOrAdjustedEvents|Události mimo pořadí|Count|Celkem|Události mimo pořadí|ID logického, PartitionId|
|AMLCalloutRequests|Žádosti o funkce|Count|Celkem|Žádosti o funkce|ID logického, PartitionId|
|AMLCalloutFailedRequests|Neúspěšné žádosti o funkce|Count|Celkem|Neúspěšné žádosti o funkce|ID logického, PartitionId|
|AMLCalloutInputEvents|Události funkcí|Count|Celkem|Události funkcí|ID logického, PartitionId|
|DeserializationError|Chyby při deserializaci vstupu|Count|Celkem|Chyby při deserializaci vstupu|ID logického, PartitionId|
|EarlyInputEvents|Události předčasného vstupu|Count|Celkem|Události předčasného vstupu|ID logického, PartitionId|
|OutputWatermarkDelaySeconds|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|ID logického, PartitionId|
|InputEventsSourcesBacklogged|Nevyřízené události vstupu|Count|Maximum|Nevyřízené události vstupu|ID logického, PartitionId|
|InputEventsSourcesPerSecond|Přijaté vstupní zdroje|Count|Celkem|Přijaté vstupní zdroje|ID logického, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Běh kanálu skončil.|Count|Celkem|Počet úspěšných spuštění kanálu orchestrace, selhání nebo zrušení|Výsledek, FailureType, kanál|
|OrchestrationActivityRunsEnded|Běh aktivity skončil.|Count|Celkem|Počet zrušených aktivit orchestrace, selhání nebo zrušení|Výsledek, FailureType, aktivita, ActivityType, kanál|
|OrchestrationTriggersEnded|Aktivační události ukončeny|Count|Celkem|Počet zrušených aktivačních událostí orchestrace, selhání nebo zrušení|Výsledek, FailureType, Trigger|
|SQLOnDemandLoginAttempts|Pokusy o přihlášení|Count|Celkem|Počet pokusů o přihlášení, které nasadilo nebo selhaly|Výsledek|
|SQLOnDemandQueriesEnded|Ukončené dotazy|Count|Celkem|Počet dotazů, které úspěšně uspěly, selhaly nebo byly zrušeny|Výsledek|
|SQLOnDemandQueryProcessedBytes|Zpracovaná data|Bajty|Celkem|Množství dat zpracovaných dotazy|Žádný|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|SparkJobsEnded|Ukončené aplikace|Count|Celkem|Počet ukončených aplikací|JobType, výsledek úlohy|
|CoresCapacity|Kapacita jader|Count|Maximum|Kapacita jader|Žádný|
|MemoryCapacityGB|Kapacita paměti (GB)|Count|Maximum|Kapacita paměti (GB)|Žádný|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DWULimit|DWU limit|Count|Maximum|Cíl na úrovni služby pro fond SQL|Žádný|
|DWUUsed|DWU použito|Count|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měřeno podle limitu DWU * procento DWU|Žádný|
|DWUUsedPercent|Procento využitého DWU|Procento|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měří se tak, že se převezme maximum mezi procenty CPU a v/v.|Žádný|
|ConnectionsBlockedByFirewall|Připojení blokovaná bránou firewall|Count|Celkem|Počet připojení blokovaných pravidly brány firewall Přečtěte si zásady řízení přístupu pro váš fond SQL a sledujte tato připojení, pokud je počet vysoký.|Žádný|
|AdaptiveCacheHitPercent|Procento přístupů do adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procentuální hodnoty přístupů do mezipaměti určete, jestli se má škálovat pro další kapacitu, nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádný|
|AdaptiveCacheUsedPercent|Procento využití adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procenta využití mezipaměti určete, jestli se má škálovat pro další kapacitu nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádný|
|LocalTempDBUsedPercent|Procento využité místní databáze tempdb|Procento|Maximum|Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se vysílají každých pět minut.|Žádný|
|MemoryUsedPercent|Procento využité paměti|Procento|Maximum|Využití paměti ve všech uzlech ve fondu SQL|Žádný|
|Připojení|Připojení|Count|Celkem|Celkový počet přihlášení ke fondu SQL|Výsledek|
|WLGActiveQueries|Aktivní dotazy skupiny úloh|Count|Celkem|Aktivní dotazy v rámci skupiny úloh. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému.|IsUserDefined, pracovní vytížení|
|WLGActiveQueriesTimeouts|Vypršení časového limitu dotazu skupiny úloh|Count|Celkem|Dotazy na skupinu úloh, jejichž časový limit vypršel. Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky).|IsUserDefined, pracovní vytížení|
|WLGAllocationBySystemPercent|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Procento přidělení prostředků vzhledem k celému systému|IsUserDefined, pracovní vytížení|
|WLGAllocationByMaxResourcePercent|Přidělení skupiny úloh podle maximálního procenta prostředků|Procento|Maximum|Zobrazuje procento přidělení prostředků vzhledem k procentuální hodnotě prostředku efektivního Cap na skupinu úloh. Tato metrika poskytuje efektivní využití skupiny úloh.|IsUserDefined, pracovní vytížení|
|WLGEffectiveCapResourcePercent|Procento efektivního Cap prostředku|Procento|Maximum|Procentuální hodnota prostředku efektivního Cap pro skupinu úloh. Pokud jsou k dispozici jiné skupiny úloh s min_percentage_resource > 0, effective_cap_percentage_resource se v poměru dolů.|IsUserDefined, pracovní vytížení|
|wlg_effective_min_resource_percent|Efektivní minimální procento prostředků|Procento|Minimum|Nastavení platné minimální procento prostředků s povoleným zvážením úrovně služby a nastavení skupiny úloh. Efektivní min_percentage_resource lze zvýšit na nižší úrovni služeb.|IsUserDefined, pracovní vytížení|
|WLGQueuedQueries|Dotazování skupin úloh ve frontě|Count|Celkem|Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti|IsUserDefined, pracovní vytížení|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Count|Celkem|Počet zpráv načtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádný|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Count|Celkem|Počet neplatných zpráv přečtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádný|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze všech zdrojů událostí|Žádný|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádný|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Count|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádný|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádný|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Count|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádný|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Count|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádný|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Count|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádný|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|IngressReceivedMessages|Příchozí zprávy příchozího přenosu dat|Count|Celkem|Počet zpráv přečtených ze zdroje události|Žádný|
|IngressReceivedInvalidMessages|Příchozí přenos dat přijal neplatné zprávy.|Count|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádný|
|IngressReceivedBytes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze zdroje události|Žádný|
|IngressStoredBytes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádný|
|IngressStoredEvents|Uložené události příchozího přenosu dat|Count|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádný|
|IngressReceivedMessagesTimeLag|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádný|
|IngressReceivedMessagesCountLag|Prodleva počtu přijatých zpráv příchozího přenosu|Count|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádný|
|WarmStorageMaxProperties|Maximální počet vlastností služby teplé úložiště|Count|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádný|
|WarmStorageUsedProperties|Vlastnosti použití teplého úložiště |Count|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádný|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty čtení z disku/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací čtení v období vzorkování.|Žádný|
|DiskWriteBytesPerSecond|Bajty zápisu na disk/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací zápisu v období vzorkování.|Žádný|
|Bajty čtení z disku|Bajty čtení z disku|Bajty|Celkem|Celková propustnost disku z důvodu operací čtení v období vzorkování.|Žádný|
|Bajty zápisu na disk|Bajty zápisu na disk|Bajty|Celkem|Celková propustnost disku v důsledku operací zápisu v období vzorkování.|Žádný|
|DiskReadOperations|Operace čtení z disku|Count|Celkem|Počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádný|
|DiskWriteOperations|Operace zápisu na disk|Count|Celkem|Počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádný|
|Operace čtení z disku/s|Operace čtení z disku/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádný|
|Operace zápisu na disk/s|Operace zápisu na disk/s|CountPerSecond|Průměr|Průměrný počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádný|
|DiskReadLatency|Latence čtení disku|Milisekund|Průměr|Celková latence čtení Součet latencí čtení zařízení a jádra.|Žádný|
|DiskWriteLatency|Latence zápisu na disk|Milisekund|Průměr|Celková latence zápisu Součet latencí zápisu zařízení a jádra.|Žádný|
|NetworkInBytesPerSecond|Síť v bajtech/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přijatý provoz.|Žádný|
|NetworkOutBytesPerSecond|Výstupní bajty sítě/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přenos přenášených dat.|Žádný|
|Síťové vstupy|Síťové vstupy|Bajty|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádný|
|Síťové výstupy|Síťové výstupy|Bajty|Celkem|Celková propustnost sítě pro přenos přenášených dat.|Žádný|
|MemoryUsed|Využitá paměť|Bajty|Průměr|Velikost paměti počítače, kterou virtuální počítač používá.|Žádný|
|MemoryGranted|Přidělená paměť|Bajty|Průměr|Velikost paměti, která byla k virtuálnímu počítači udělena hostitelem. Hostiteli není pro hostitele udělené, dokud se nedotknete jednou, a pokud VMkernel potřebuje paměť, může se tato paměť vyměnit.|Žádný|
|MemoryActive|Paměť aktivní|Bajty|Průměr|Velikost paměti, kterou virtuální počítač využíval v posledních malých oknech času. Toto je "pravdivý" počet paměti, které virtuální počítač v současnosti potřebuje. Dodatečná, nevyužitá paměť může být vyměněna nebo v bublině bez dopadu na výkon hosta.|Žádný|
|Procento CPU|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je hlášena s 100%, která představuje všechny jádra procesoru v systému. Příklad: oboustranný virtuální počítač, který používá 50% systému se čtyřmi jádry, plně používá dvě jádra.|Žádný|
|PercentageCpuReady|Procento připraveného procesoru|Milisekund|Celkem|Čas připravenosti je doba, po kterou se bude čekat na dostupnost PROCESORů v minulém intervalu aktualizace.|Žádný|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/serverových farem

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Count|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Count|Průměr|Délka fronty http|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|TcpSynSent|Odesláno TCP syn|Count|Průměr|Odesláno TCP syn|Instance|
|TcpSynReceived|Přijato TCP syn|Count|Průměr|Přijato TCP syn|Instance|
|TcpEstablished|TCP – vytvořeno|Count|Průměr|TCP – vytvořeno|Instance|
|TcpFinWait1|TCP FIN – čekání 1|Count|Průměr|TCP FIN – čekání 1|Instance|
|TcpFinWait2|TCP FIN – čekání 2|Count|Průměr|TCP FIN – čekání 2|Instance|
|TcpClosing|Ukončení protokolu TCP|Count|Průměr|Ukončení protokolu TCP|Instance|
|TcpCloseWait|Ukončení čekání protokolu TCP|Count|Průměr|Ukončení čekání protokolu TCP|Instance|
|TcpLastAck|Poslední potvrzení TCP|Count|Průměr|Poslední potvrzení TCP|Instance|
|TcpTimeWait|Doba čekání protokolu TCP|Count|Průměr|Doba čekání protokolu TCP|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/weby (kromě funkcí) 

> [!NOTE]
> **Využití systému souborů** je nová metrika, která je globálně nasazená, ale neočekávají se žádná data, pokud jste si je nepřidali do seznamu povolených privátních náhledů.

> [!IMPORTANT]
> **Průměrná doba odezvy** bude zastaralá, aby nedocházelo k záměně s agregacemi metrik. Použijte **dobu odezvy** jako náhradu.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Count|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Count|Celkem|Http 101|Instance|
|Http2xx|2xx http|Count|Celkem|2xx http|Instance|
|Http3xx|3xx http|Count|Celkem|3xx http|Instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|Instance|
|Http406|Http 406|Count|Celkem|Http 406|Instance|
|Http4xx|4xx http|Count|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|HttpResponseTime|Doba odezvy|Sekundy|Celkem|Doba odezvy|Instance|
|AverageResponseTime|Průměrná doba odezvy (nepoužívané)|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|AppConnections|Připojení|Count|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Count|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Count|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Count|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Count|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Count|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Count|Celkem|Uvolňování paměti 2. generace|Instance|
|HealthCheckStatus|Stav kontroly stavu|Count|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/lokality (funkce)

> [!NOTE]
> **Využití systému souborů** je nová metrika, která je globálně nasazená, ale neočekávají se žádná data, pokud jste si je nepřidali do seznamu povolených privátních náhledů.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|MB/milisekundy|Celkem|[Jednotky spuštění funkce](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Count|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Count|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Count|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Count|Celkem|Uvolňování paměti 2. generace|Instance|
|HealthCheckStatus|Stav kontroly stavu|Count|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|CpuTime|Čas procesoru|Sekundy|Celkem|Čas procesoru|Instance|
|Žádosti|Žádosti|Count|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Count|Celkem|Http 101|Instance|
|Http2xx|2xx http|Count|Celkem|2xx http|Instance|
|Http3xx|3xx http|Count|Celkem|3xx http|Instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|Instance|
|Http406|Http 406|Count|Celkem|Http 406|Instance|
|Http4xx|4xx http|Count|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|Instance|
|MemoryWorkingSet|Pracovní sada paměti|Bajty|Průměr|Pracovní sada paměti|Instance|
|AverageMemoryWorkingSet|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná pracovní sada paměti|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|HttpResponseTime|Doba odezvy|Sekundy|Průměr|Doba odezvy|Instance|
|FunctionExecutionUnits|Jednotky spuštění funkce|Count|Celkem|Jednotky spuštění funkce|Instance|
|FunctionExecutionCount|Počet spuštění funkce|Count|Celkem|Počet spuštění funkce|Instance|
|AppConnections|Připojení|Count|Průměr|Připojení|Instance|
|Handles|Počet popisovačů|Count|Průměr|Počet popisovačů|Instance|
|Vlákna|Počet vláken|Count|Průměr|Počet vláken|Instance|
|PrivateBytes|Soukromé bajty|Bajty|Průměr|Soukromé bajty|Instance|
|IoReadBytesPerSecond|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Bajty čtení v/v za sekundu|Instance|
|IoWriteBytesPerSecond|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní bajty zápisu za sekundu|Instance|
|IoOtherBytesPerSecond|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|IO – ostatní bajty za sekundu|Instance|
|IoReadOperationsPerSecond|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace čtení za sekundu|Instance|
|IoWriteOperationsPerSecond|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Vstupně-výstupní operace zápisu za sekundu|Instance|
|IoOtherOperationsPerSecond|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|V/v – ostatní operace za sekundu|Instance|
|RequestsInApplicationQueue|Žádosti ve frontě aplikací|Count|Průměr|Žádosti ve frontě aplikací|Instance|
|CurrentAssemblies|Aktuální sestavení|Count|Průměr|Aktuální sestavení|Instance|
|TotalAppDomains|Celkový počet domén aplikace|Count|Průměr|Celkový počet domén aplikace|Instance|
|TotalAppDomainsUnloaded|Celkový počet uvolněných domén aplikace|Count|Průměr|Celkový počet uvolněných domén aplikace|Instance|
|Gen0Collections|Generace paměti gen 0|Count|Celkem|Generace paměti gen 0|Instance|
|Gen1Collections|Generace paměti 1. generace|Count|Celkem|Generace paměti 1. generace|Instance|
|Gen2Collections|Uvolňování paměti 2. generace|Count|Celkem|Uvolňování paměti 2. generace|Instance|
|HealthCheckStatus|Stav kontroly stavu|Count|Průměr|Stav kontroly stavu|Instance|
|FileSystemUsage|Využití systému souborů|Bajty|Průměr|Využití systému souborů|Žádný|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|Žádosti|Žádosti|Count|Celkem|Žádosti|Instance|
|BytesReceived|Data v|Bajty|Celkem|Data v|Instance|
|BytesSent|Výstupní data|Bajty|Celkem|Výstupní data|Instance|
|Http101|Http 101|Count|Celkem|Http 101|Instance|
|Http2xx|2xx http|Count|Celkem|2xx http|Instance|
|Http3xx|3xx http|Count|Celkem|3xx http|Instance|
|Http401|HTTP 401|Count|Celkem|HTTP 401|Instance|
|Http403|HTTP 403|Count|Celkem|HTTP 403|Instance|
|Http404|HTTP 404|Count|Celkem|HTTP 404|Instance|
|Http406|Http 406|Count|Celkem|Http 406|Instance|
|Http4xx|4xx http|Count|Celkem|4xx http|Instance|
|Http5xx|Chyby serveru http|Count|Celkem|Chyby serveru http|Instance|
|AverageResponseTime|Průměrná doba odezvy|Sekundy|Průměr|Průměrná doba odezvy|Instance|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
|DiskQueueLength|Délka fronty disku|Count|Průměr|Délka fronty disku|Instance|
|HttpQueueLength|Délka fronty http|Count|Průměr|Délka fronty http|Instance|
|ActiveRequests|Aktivní požadavky|Count|Celkem|Aktivní požadavky|Instance|
|TotalFrontEnds|Celkový počet front-endy|Count|Průměr|Celkový počet front-endy|Žádný|
|SmallAppServicePlanInstances|Plánování pracovníků malých App Service|Count|Průměr|Plánování pracovníků malých App Service|Žádný|
|MediumAppServicePlanInstances|Střední App Service plánování pracovních procesů|Count|Průměr|Střední App Service plánování pracovních procesů|Žádný|
|LargeAppServicePlanInstances|Zaměstnanci s velkými App Servicey plánu|Count|Průměr|Zaměstnanci s velkými App Servicey plánu|Žádný|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|WorkersTotal|Celkový počet pracovníků|Count|Průměr|Celkový počet pracovníků|Žádný|
|WorkersAvailable|Zaměstnanci, kteří jsou k dispozici|Count|Průměr|Zaměstnanci, kteří jsou k dispozici|Žádný|
|WorkersUsed|Využívané pracovní procesy|Count|Průměr|Využívané pracovní procesy|Žádný|
|CpuPercentage|Procento procesoru|Procento|Průměr|Procento procesoru|Instance|
|MemoryPercentage|Procento paměti|Procento|Průměr|Procento paměti|Instance|
## <a name="next-steps"></a>Další kroky
* [Přečtěte si o metrikách v Azure Monitor](data-platform.md)
* [Vytváření upozornění na metriky](alerts-overview.md)
* [Exportovat metriky do úložiště, centra událostí nebo Log Analytics](platform-logs-overview.md)
