---
title: Azure Monitor podporované metriky podle typu prostředku
description: Seznam metrik dostupných pro každý typ prostředku s Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/01/2021
ms.author: robb
ms.openlocfilehash: 0854d56930dbc8decfcf8f96a814f082e361de62
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167239"
---
# <a name="supported-metrics-with-azure-monitor"></a>Podporované metriky s Azure Monitor

> [!NOTE]
> Tento seznam je z velké části automaticky vygenerován. Jakékoli změny provedené v tomto seznamu prostřednictvím GitHubu se můžou zapisovat bez upozornění. Podrobnosti o tom, jak provádět trvalé aktualizace, získáte od autora tohoto článku.

Azure Monitor poskytuje několik způsobů, jak pracovat s metrikami, včetně jejich grafu na portálu, přístupu k nim prostřednictvím REST API nebo jejich dotazování pomocí PowerShellu nebo rozhraní příkazového řádku. 

Tento článek je úplný seznam všech metrik platformy (tj. automaticky shromážděných), které jsou aktuálně k dispozici s kanálem konsolidované metriky Azure Monitor. Metriky se změnily nebo přidaly po datu v horní části tohoto článku se nemusí zatím zobrazit níže. Chcete-li se dotazovat na seznam metrik a přistupovat k nim prostřednictvím kódu programu, použijte [rozhraní API verze 2018-01-01](/rest/api/monitor/metricdefinitions). Jiné metriky, které nejsou v tomto seznamu, mohou být k dispozici na portálu nebo pomocí starších rozhraní API.

Metriky jsou uspořádány podle zprostředkovatelů prostředků a typu prostředku. Seznam služeb a poskytovatelů prostředků a typů, které do nich patří, najdete v tématu [poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Export metrik platforem do jiných umístění

Metriky platformy můžete exportovat z kanálu Azure monitor do jiných umístění jedním ze dvou způsobů.
1. Použít [REST API metriky](/rest/api/monitor/metrics/list)
2. Použití [nastavení diagnostiky](../essentials/diagnostic-settings.md) k směrování metrik platforem na 
    - Azure Storage
    - Protokoly Azure Monitor (a tak Log Analytics)
    - Centra událostí, která vám pomohou získat systémy od jiných společností než Microsoft 

Použití nastavení diagnostiky je nejjednodušší způsob, jak směrovat metriky, ale existují určitá omezení: 

- **Některé bez možnosti exportu** – všechny metriky je možné exportovat pomocí REST API, některé ale nejdou exportovat pomocí nastavení diagnostiky z důvodu složitými rozhraními Azure monitor back-endu. Sloupec, který se dá *exportovat prostřednictvím nastavení diagnostiky* v tabulkách níže, které metriky se dají exportovat tímto způsobem.  

- **Multidimenzionální metriky** – odesílání multidimenzionálních metrik do jiných umístění prostřednictvím nastavení diagnostiky není aktuálně podporováno. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí. *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.

## <a name="guest-os-and-host-os-metrics"></a>Metriky operačního systému hosta a hostitele

> [!WARNING]
> Metriky pro hostovaný operační systém (hostovaný operační systém), který běží v Azure Virtual Machines, Service Fabric a Cloud Services tady **nejsou uvedené.** Metriky hostovaného operačního systému musí být shromážděny prostřednictvím jednoho nebo více agentů, které se spouští v systému nebo jako součást hostovaného operačního systému.  Metriky hostovaného operačního systému zahrnují čítače výkonu, které sledují procento využití procesoru hosta nebo paměti, které se často používají pro automatické škálování nebo upozorňování. 
>
> **Metriky operačního systému hostitele jsou k dispozici a jsou uvedeny níže.** Nejedná se o stejné. Metriky operačního systému hostitele se vztahují k relaci Hyper-V hostující relaci hostovaného operačního systému. 

> [!TIP]
> Osvědčeným postupem je použít a nakonfigurovat [rozšíření Azure Diagnostics](../agents/diagnostics-extension-overview.md) k odeslání metrik výkonu HOSTOVANÉHO operačního systému do stejné Azure monitor databáze metriky, kde jsou uložené metriky platforem. Rozšíření směruje metriky hostovaného operačního systému prostřednictvím [vlastních](../essentials/metrics-custom-overview.md) rozhraní API metrik. Pak můžete graf, upozornění a jinak používat metriky operačního systému hosta, jako jsou metriky platforem. Případně můžete také použít agenta Log Analytics k odeslání metriky hostovaného operačního systému do Azure Monitor protokolů/Log Analytics. Na tyto metriky se můžete dotazovat v kombinaci s nemetrickými daty. 

Důležité Další informace najdete v tématu [Přehled agentů monitorování](../agents/agents-overview.md).

## <a name="table-formatting"></a>Formátování tabulky

> [!IMPORTANT] 
> Tato nejnovější aktualizace přidá nový sloupec a změní pořadí metrik na abecední. Informace o přidaných objektech znamená, že následující tabulky mohou mít horizontální posuvník v dolní části, v závislosti na šířce okna prohlížeče. Pokud se domníváte, že jste neztratili nějaké informace, použijte posuvník a zobrazte celou tabulku.
## <a name="microsoftaadiamazureadmetrics"></a>Microsoft. aadiam/azureADMetrics

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Počet|Průměr|Metrika typu azureADMetrics|Žádné dimenze|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Paměť: aktuální cena čisticího modulu|Počet|Průměr|Aktuální cena paměti $ USD, normalizovaná na 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Paměť: nezmenšovaná paměť čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které není předmětem mazání čisticím pozadím.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Paměť: velikost čisticí paměti|Bajty|Průměr|Množství paměti (v bajtech), které se může vyčistit čisticím nástrojem na pozadí.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Vlákna: zaneprázdněná vlákna fondu příkazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Vlákna: nečinná vlákna fondu příkazů|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkazů.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Délka fronty úloh fondu příkazů|Počet|Průměr|Počet úloh ve frontě fondu vláken příkazů|ServerResourceType|
|CurrentConnections|Yes|Připojení: aktuální připojení|Počet|Průměr|Aktuální počet navázaných připojení klientů.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuální uživatelské relace|Počet|Průměr|Aktuální počet navázaných uživatelských relací.|ServerResourceType|
|LongParsingBusyThreads|Yes|Vlákna: zaneprázdněná vlákna s dlouhou analýzou|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingIdleThreads|Yes|Vlákna: nečinná vlákna při dlouhé analýze|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouhého analýzy.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Vlákna: délka fronty úloh dlouhého rozboru|Počet|Průměr|Počet úloh ve frontě fondu vláken dlouhého analýzy.|ServerResourceType|
|mashup_engine_memory_metric|Yes|Paměť motoru M|Bajty|Průměr|Využití paměti procesy modulu hybridní webové aplikace|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|Počet privátních bajtů modulu M|Bajty|Průměr|Používání privátních bajtů procesy v hybridních hybridních modulech.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|QPU modulu M|Počet|Průměr|QPU využití hybridních procesů v hybridních modulech|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Virtuální bajty motoru M|Bajty|Průměr|Využití virtuálních bajtů procesy modulu hybridní webové aplikace|ServerResourceType|
|memory_metric|Yes|Memory (Paměť)|Bajty|Průměr|Memory: Rozsah 0-25 GB pro S1, 0-50 GB pro S2 a 0-100 GB pro S4|ServerResourceType|
|memory_thrashing_metric|Yes|Thrashing paměti|Procento|Průměr|Průměrná velikost thrashing paměti|ServerResourceType|
|MemoryLimitHard|Yes|Paměť: limit paměti – pevný|Bajty|Průměr|Limit pevné paměti, z konfiguračního souboru.|ServerResourceType|
|Hodnota memorylimithigh|Yes|Paměť: limit paměti – vysoká|Bajty|Průměr|Horní limit paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitLow|Yes|Paměť: limit paměti – nízká|Bajty|Průměr|Omezení nedostatku paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Paměť: limit paměti VertiPaq|Bajty|Průměr|Limit v paměti, z konfiguračního souboru.|ServerResourceType|
|MemoryUsage|Yes|Paměť: využití paměti|Bajty|Průměr|Využití paměti procesu serveru, které se používá při výpočtu ceny čisticí paměti. Rovná se čítači Process\PrivateBytes a velikostí dat mapovaných do paměti, přičemž se ignoruje jakákoli paměť, která byla namapována nebo přidělena xVelocitym stroji pro analýzu paměti (VertiPaq), nad rámec limitu paměti stroje xVelocity.|ServerResourceType|
|private_bytes_metric|Yes|Soukromé bajty|Bajty|Průměr|Soukromé bajty.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Vlákna: zaneprázdněná vlákna úloh v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží úlohy v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Vlákna: zaneprázdněná vlákna, která nejsou v/v fondu zpracování|Počet|Průměr|Počet vláken, ve kterých běží jiné úlohy než v/v, ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Vlákna: vlákna nečinných úloh v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Vlákna: nečinná vlákna v/v fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu vláken zpracování vyhrazeného pro úlohy bez vstupně-výstupních operací.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Vlákna: délka fronty úloh v/v fondu zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet nevstupně-výstupních úloh ve frontě fondu vláken zpracování.|ServerResourceType|
|qpu_metric|Yes|QPU|Počet|Průměr|QPU. Rozsah 0-100 pro S1, 0-200 pro S2 a 0-400 pro S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Zaneprázdněná vlákna fondu dotazů|Počet|Průměr|Počet zaneprázdněných vláken ve fondu vláken dotazů.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Vlákna: nečinná vlákna fondu dotazů|Počet|Průměr|Počet nečinných vláken pro úlohy v/v ve fondu vláken zpracování.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Vlákna: délka fronty úloh fondu dotazů|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů.|ServerResourceType|
|Kvóta|Yes|Paměť: kvóta|Bajty|Průměr|Aktuální kvóta paměti (v bajtech). Kvóta paměti je také známá jako přidělení paměti nebo rezervace paměti.|ServerResourceType|
|QuotaBlocked|Yes|Paměť: kvóta blokována|Počet|Průměr|Aktuální počet požadavků na kvótu, které jsou blokovány, dokud nebudou uvolněny jiné kvóty paměti.|ServerResourceType|
|RowsConvertedPerSec|Yes|Zpracování: počet převedených řádků za sekundu|CountPerSecond|Průměr|Rychlost převodu řádků během zpracování.|ServerResourceType|
|RowsReadPerSec|Yes|Zpracování: Počet přečtených řádků za sekundu|CountPerSecond|Průměr|Rychlost čtení řádků ze všech relačních databází.|ServerResourceType|
|RowsWrittenPerSec|Yes|Zpracování: počet zapsaných řádků za sekundu|CountPerSecond|Průměr|Rychlost zápisu řádků během zpracování.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Vlákna: krátkodobá analýza zaneprázdněných vláken|Počet|Průměr|Počet zaneprázdněných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Vlákna: nečinná vlákna krátké analýzy|Počet|Průměr|Počet nečinných vláken ve zkráceném fondu vláken pro analýzu.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Vlákna: délka fronty úlohy krátké analýzy|Počet|Průměr|Počet úloh ve frontě krátkého analýzy fondu vláken.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Úspěšná připojení za sekundu|CountPerSecond|Průměr|Frekvence úspěšných dokončení připojení.|ServerResourceType|
|TotalConnectionFailures|Yes|Celkový počet selhání připojení|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení|ServerResourceType|
|TotalConnectionRequests|Yes|Požadavky na připojení celkem|Počet|Průměr|Celkový počet požadavků na připojení Jedná se o příjem.|ServerResourceType|
|VertiPaqNonpaged|Yes|Paměť: VertiPaq nestránkované|Bajty|Průměr|Počet bajtů paměti uzamčených v pracovní sadě pro použití modulem v paměti.|ServerResourceType|
|VertiPaqPaged|Yes|Paměť: VertiPaq stránkované|Bajty|Průměr|Počet bajtů stránkované paměti používaných pro data v paměti.|ServerResourceType|
|virtual_bytes_metric|Yes|Virtuální bajty|Bajty|Průměr|Virtuální bajty.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Doba trvání požadavků back-endu|Milisekund|Průměr|Doba trvání požadavků back-endu v milisekundách|Umístění, název hostitele|
|Kapacita|Yes|Kapacita|Procento|Průměr|Metrika využití pro službu ApiManagement|Umístění|
|Doba trvání|Yes|Celková doba trvání žádostí o bránu|Milisekund|Průměr|Celková doba trvání požadavků brány v milisekundách|Umístění, název hostitele|
|EventHubDroppedEvents|Yes|Vyřazené události EventHub|Počet|Celkem|Počet vynechaných událostí z důvodu dosažení limitu velikosti fronty|Umístění|
|EventHubRejectedEvents|Yes|Odmítnuté události EventHub|Počet|Celkem|Počet odmítnutých událostí EventHub (nesprávná konfigurace nebo neoprávněná)|Umístění|
|EventHubSuccessfulEvents|Yes|Úspěšné události EventHub|Počet|Celkem|Počet úspěšných událostí EventHub|Umístění|
|EventHubThrottledEvents|Yes|Omezené události EventHub|Počet|Celkem|Počet omezených událostí EventHub|Umístění|
|EventHubTimedoutEvents|Yes|Vypršel časový limit událostí EventHub.|Počet|Celkem|Počet vypršení časového limitu událostí EventHub|Umístění|
|EventHubTotalBytesSent|Yes|Velikost událostí EventHub|Bajty|Celkem|Celková velikost událostí EventHub v bajtech|Umístění|
|EventHubTotalEvents|Yes|Celkový počet událostí EventHub|Počet|Celkem|Počet událostí odeslaných do centra EventHub|Umístění|
|EventHubTotalFailedEvents|Yes|Neúspěšné události EventHub|Počet|Celkem|Počet neúspěšných událostí EventHub|Umístění|
|FailedRequests|Yes|Neúspěšné požadavky brány (zastaralé)|Počet|Celkem|Počet chyb v žádostech brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory.|Umístění, název hostitele|
|NetworkConnectivity|Yes|Stav připojení prostředků k síti (Preview)|Počet|Průměr|Stav připojení k síti u typů závislých prostředků od služby API Management|Umístění, ResourceType|
|OtherRequests|Yes|Jiné požadavky na bránu (zastaralé)|Počet|Celkem|Počet dalších požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|Žádosti|Yes|Žádosti|Počet|Celkem|Metriky žádostí brány s více dimenzemi|Umístění, název hostitele, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Úspěšné požadavky brány (zastaralé)|Počet|Celkem|Počet úspěšných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|TotalRequests|Yes|Požadavky brány celkem (zastaralé)|Počet|Celkem|Počet požadavků brány – použijte místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|
|UnauthorizedRequests|Yes|Neautorizované žádosti o bránu (zastaralé)|Počet|Celkem|Počet neautorizovaných požadavků brány – použít místo toho metriku žádostí s více dimenzemi s dimenzí GatewayResponseCodeCategory|Umístění, název hostitele|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Počet|Počet|Celkový počet příchozích požadavků HTTP.|StatusCode, ověřování|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Počet|Průměr|Latence požadavku HTTP.|StatusCode, ověřování|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Počet|Počet|Omezené požadavky HTTP.|Žádné dimenze|

## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|aktivní – časovač-počet|Yes|aktivní – časovač-počet|Počet|Průměr|Počet časovačů, které jsou aktuálně aktivní|Nasazení, AppName, pod|
|přidělení – sazba|Yes|přidělení – sazba|Bajty|Průměr|Počet bajtů přidělených ve spravované haldě|Nasazení, AppName, pod|
|AppCpuUsage|Yes|Využití CPU pro aplikaci |Procento|Průměr|Poslední využití procesoru pro aplikaci|Nasazení, AppName, pod|
|počet sestavení|Yes|počet sestavení|Počet|Průměr|Počet načtených sestavení|Nasazení, AppName, pod|
|využití procesoru|Yes|využití procesoru|Procento|Průměr|% doby využití procesoru procesem|Nasazení, AppName, pod|
|aktuální požadavky|Yes|aktuální požadavky|Počet|Průměr|Celkový počet požadavků zpracovávaných během životního cyklu procesu|Nasazení, AppName, pod|
|počet výjimek|Yes|počet výjimek|Počet|Celkem|Počet výjimek|Nasazení, AppName, pod|
|neúspěšné – požadavky|Yes|neúspěšné – požadavky|Počet|Průměr|Celkový počet neúspěšných žádostí během životnosti procesu|Nasazení, AppName, pod|
|GC-velikost haldy|Yes|GC-velikost haldy|Počet|Průměr|Celková velikost haldy hlášené GC (MB)|Nasazení, AppName, pod|
|gen-0-GC-Count|Yes|gen-0-GC-Count|Počet|Průměr|Počet Gců 0. generace|Nasazení, AppName, pod|
|gen-0-velikost|Yes|gen-0-velikost|Bajty|Průměr|Velikost haldy 0. generace|Nasazení, AppName, pod|
|Obecná-1-GC-Count|Yes|Obecná-1-GC-Count|Počet|Průměr|Počet Gců 1. generace|Nasazení, AppName, pod|
|Obecná-1-velikost|Yes|Obecná-1-velikost|Bajty|Průměr|Velikost haldy 1. generace|Nasazení, AppName, pod|
|Gen-2 – GC-Count|Yes|Gen-2 – GC-Count|Počet|Průměr|Počet Gců Gen 2|Nasazení, AppName, pod|
|Obecná 2 – velikost|Yes|Obecná 2 – velikost|Bajty|Průměr|Velikost haldy 2. generace|Nasazení, AppName, pod|
|JVM. GC. Live. data. Size|Yes|JVM. GC. Live. data. Size|Bajty|Průměr|Velikost staré generace fondu paměti po úplném GC|Nasazení, AppName, pod|
|JVM. GC. max. data. Size|Yes|JVM. GC. max. data. Size|Bajty|Průměr|Maximální velikost staré generace fondu paměti|Nasazení, AppName, pod|
|JVM. GC. Memory. alokovaný|Yes|JVM. GC. Memory. alokovaný|Bajty|Maximum|Zvýšeno na zvýšení velikosti fondu paměti pro mladé generaci po jednom GC na další|Nasazení, AppName, pod|
|JVM. GC. Memory. propagovaný|Yes|JVM. GC. Memory. propagovaný|Bajty|Maximum|Počet kladných zvýšení velikosti staré paměti generace před GC na po GC|Nasazení, AppName, pod|
|JVM. GC. Pause. Total. Count|Yes|JVM. GC. Pause. Total. Count|Počet|Celkem|Počet pozastavení GC|Nasazení, AppName, pod|
|JVM. GC. Pause. Total. time|Yes|JVM. GC. Pause. Total. time|Milisekund|Celkem|Celkový čas pozastavení GC|Nasazení, AppName, pod|
|JVM. Memory. potvrzený|Yes|JVM. Memory. potvrzený|Bajty|Průměr|Paměť přiřazená k JVM v bajtech|Nasazení, AppName, pod|
|JVM. Memory. max|Yes|JVM. Memory. max|Bajty|Maximum|Maximální množství paměti v bajtech, které lze použít pro správu paměti|Nasazení, AppName, pod|
|JVM. Memory .Ed – použito|Yes|JVM. Memory .Ed – použito|Bajty|Průměr|Využitá paměť aplikace v bajtech|Nasazení, AppName, pod|
|LOH-Size|Yes|LOH-Size|Bajty|Průměr|Velikost haldy LOH|Nasazení, AppName, pod|
|Monitor-Lock-Content – Count|Yes|Monitor-Lock-Content – Count|Počet|Průměr|Počet sporů při pokusu o provedení zámku monitoru|Nasazení, AppName, pod|
|Process. CPU. Usage|Yes|Process. CPU. Usage|Procento|Průměr|Poslední využití CPU pro proces JVM|Nasazení, AppName, pod|
|požadavky – za sekundu|Yes|požadavky – sazba|Počet|Průměr|Počet požadavků|Nasazení, AppName, pod|
|System. CPU. Usage|Yes|System. CPU. Usage|Procento|Průměr|Poslední využití CPU pro celý systém|Nasazení, AppName, pod|
|nedokončené dokončování – položky-počet|Yes|nedokončené dokončování – položky-počet|Počet|Průměr|Počet dokončených pracovních položek fondu vláken|Nasazení, AppName, pod|
|nevlákenná doba zařazení do fronty|Yes|nevlákenná doba zařazení do fronty|Počet|Průměr|Délka fronty pracovních položek fondu vláken|Nasazení, AppName, pod|
|počet podprocesů|Yes|počet podprocesů|Počet|Průměr|Počet podprocesů vláken|Nasazení, AppName, pod|
|doba do GC|Yes|doba do GC|Procento|Průměr|% času v GC od posledního GC|Nasazení, AppName, pod|
|Tomcat. Global. Error|Yes|Tomcat. Global. Error|Počet|Celkem|Globální chyba Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. Received|Yes|Tomcat. Global. Received|Bajty|Celkem|Celkový počet přijatých bajtů Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. Request. prům. time|Yes|Tomcat. Global. Request. prům. time|Milisekund|Průměr|Průměrná doba požadavku Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. Request. max|Yes|Tomcat. Global. Request. max|Milisekund|Maximum|Maximální čas požadavku Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. Request. Total. Count|Yes|Tomcat. Global. Request. Total. Count|Počet|Celkem|Celkový počet žádostí Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. Request. Total. time|Yes|Tomcat. Global. Request. Total. time|Milisekund|Celkem|Celkový čas žádosti Tomcat|Nasazení, AppName, pod|
|Tomcat. Global. odesláno|Yes|Tomcat. Global. odesláno|Bajty|Celkem|Celkový počet odeslaných bajtů Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. Active. Current|Yes|Tomcat. Sessions. Active. Current|Počet|Celkem|Počet aktivních relací Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. Active. max|Yes|Tomcat. Sessions. Active. max|Počet|Celkem|Maximální počet aktivních relací Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. Alive. max|Yes|Tomcat. Sessions. Alive. max|Milisekund|Maximum|Maximální doba běhu relace Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. Created|Yes|Tomcat. Sessions. Created|Počet|Celkem|Počet vytvořených relací Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. vypršela platnost|Yes|Tomcat. Sessions. vypršela platnost|Počet|Celkem|Počet vypršení relace Tomcat|Nasazení, AppName, pod|
|Tomcat. Sessions. odmítnuto|Yes|Tomcat. Sessions. odmítnuto|Počet|Celkem|Počet odmítnutých relací Tomcat|Nasazení, AppName, pod|
|tomcat.threads.config. max|Yes|tomcat.threads.config. max|Počet|Celkem|Maximální počet vláken konfigurace Tomcat|Nasazení, AppName, pod|
|Tomcat. Threads. Current|Yes|Tomcat. Threads. Current|Počet|Celkem|Počet aktuálních vláken Tomcat|Nasazení, AppName, pod|
|celkem – požadavky|Yes|celkem – požadavky|Počet|Průměr|Celkový počet žádostí za dobu života procesu|Nasazení, AppName, pod|
|pracovní sada|Yes|pracovní sada|Počet|Průměr|Množství pracovní sady používané procesem (MB)|Nasazení, AppName, pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Celkový počet úloh|Počet|Celkem|Celkový počet úloh|Runbook, stav|
|TotalUpdateDeploymentMachineRuns|Yes|Celkový počet spuštěných počítačů nasazení aktualizace|Počet|Celkem|Celkový počet spuštěných počítačů nasazení aktualizace softwaru v běhu nasazení aktualizace softwaru|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Celkový počet spuštění nasazení aktualizací|Počet|Celkem|Celkový počet spuštění nasazení aktualizací softwaru|SoftwareUpdateConfigurationName, stav|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/privateClouds

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Celková kapacita disku úložiště dat|Bajty|Průměr|Celková kapacita disku v úložišti dat|dsname|
|DiskUsedPercentage|Yes| Procento využitého disku úložiště dat|Procento|Průměr|Procento dostupného disku využitého v úložišti dat|dsname|
|EffectiveCpuAverage|Yes|Procento CPU|Procento|Průměr|Procento využitých prostředků procesoru v clusteru|clustername|
|EffectiveMemAverage|Yes|Průměrná efektivní paměť|Bajty|Průměr|Celková dostupná velikost paměti počítače v clusteru|clustername|
|OverheadAverage|Yes|Průměrná režie paměti|Bajty|Průměr|Fyzická paměť hostitele spotřebovaná infrastrukturou virtualizace|clustername|
|TotalMbAverage|Yes|Průměrná celková paměť|Bajty|Průměr|Celková paměť v clusteru|clustername|
|UsageAverage|Yes|Průměrné využití paměti|Procento|Průměr|Využití paměti jako procento z celkového nakonfigurované nebo dostupné paměti|clustername|
|UsedLatest|Yes|Využitý disk úložiště dat|Bajty|Průměr|Celkové množství disku využitého v úložišti dat|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CoreCount|No|Vyhrazený počet jader|Počet|Celkem|Celkový počet vyhrazených jader v účtu Batch|Žádné dimenze|
|CreatingNodeCount|No|Vytváření počtu uzlů|Počet|Celkem|Počet vytvořených uzlů|Žádné dimenze|
|IdleNodeCount|No|Počet nečinných uzlů|Počet|Celkem|Počet nečinných uzlů|Žádné dimenze|
|JobDeleteCompleteEvent|Yes|Události dokončení odstranění úlohy|Počet|Celkem|Celkový počet úspěšně odstraněných úloh.|Úlohy|
|JobDeleteStartEvent|Yes|Události spuštění odstranění úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k odstranění.|Úlohy|
|JobDisableCompleteEvent|Yes|Úloha zakázat kompletní události|Počet|Celkem|Celkový počet úloh, které byly úspěšně zakázány.|Úlohy|
|JobDisableStartEvent|Yes|Úloha zakázat počáteční události|Počet|Celkem|Celkový počet úloh, které byly vyžádány k zakázání.|Úlohy|
|JobStartEvent|Yes|Události spuštění úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně spuštěny.|Úlohy|
|JobTerminateCompleteEvent|Yes|Ukončit události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly úspěšně ukončeny.|Úlohy|
|JobTerminateStartEvent|Yes|Události spuštění ukončení úlohy|Počet|Celkem|Celkový počet úloh, které byly vyžádány k ukončení.|Úlohy|
|LeavingPoolNodeCount|No|Počet ponechávání uzlů fondu|Počet|Celkem|Počet uzlů opouštících fond|Žádné dimenze|
|LowPriorityCoreCount|No|Počet jader LowPriority|Počet|Celkem|Celkový počet jader s nízkou prioritou v účtu Batch|Žádné dimenze|
|OfflineNodeCount|No|Počet uzlů v režimu offline|Počet|Celkem|Počet offline uzlů|Žádné dimenze|
|PoolCreateEvent|Yes|Vytváření fondů – události|Počet|Celkem|Celkový počet vytvořených fondů|poolId|
|PoolDeleteCompleteEvent|Yes|Události dokončení odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly dokončeny|poolId|
|PoolDeleteStartEvent|Yes|Události spuštění odstranění fondu|Počet|Celkem|Celkový počet odstranění fondů, které byly spuštěny|poolId|
|PoolResizeCompleteEvent|Yes|Události dokončení změny velikosti fondu|Počet|Celkem|Celkový počet změněných velikostí fondu, které byly dokončeny|poolId|
|PoolResizeStartEvent|Yes|Události spuštění změny velikosti fondu|Počet|Celkem|Celkový počet změněných počtu fondů, které byly spuštěny|poolId|
|PreemptedNodeCount|No|Počet zrušených uzlů|Počet|Celkem|Počet zrušených uzlů|Žádné dimenze|
|RebootingNodeCount|No|Restartování počtu uzlů|Počet|Celkem|Počet restartování uzlů|Žádné dimenze|
|ReimagingNodeCount|No|Počet uzlů obnovování imagí|Počet|Celkem|Počet uzlů obnovování imagí|Žádné dimenze|
|RunningNodeCount|No|Počet spuštěných uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|StartingNodeCount|No|Počáteční počet uzlů|Počet|Celkem|Počet spuštěných uzlů|Žádné dimenze|
|StartTaskFailedNodeCount|No|Počet neúspěšných spuštění úlohy – počet uzlů|Počet|Celkem|Počet uzlů, ve kterých se spouštěcí úkol nezdařil|Žádné dimenze|
|TaskCompleteEvent|Yes|Události dokončení úlohy|Počet|Celkem|Celkový počet úloh, které byly dokončeny|poolId, jobId|
|TaskFailEvent|Yes|Události neúspěšných úloh|Počet|Celkem|Celkový počet úloh, které byly dokončeny ve stavu selhání|poolId, jobId|
|TaskStartEvent|Yes|Události zahájení úlohy|Počet|Celkem|Celkový počet úloh, které byly spuštěny|poolId, jobId|
|TotalLowPriorityNodeCount|No|Počet uzlů Low-Priority|Počet|Celkem|Celkový počet uzlů s nízkou prioritou v účtu Batch|Žádné dimenze|
|TotalNodeCount|No|Počet vyhrazených uzlů|Počet|Celkem|Celkový počet vyhrazených uzlů v účtu Batch|Žádné dimenze|
|UnusableNodeCount|No|Počet nepoužitelných uzlů|Počet|Celkem|Počet nepoužitelných uzlů|Žádné dimenze|
|WaitingForStartTaskNodeCount|No|Čekání na počet uzlů spouštěcí úlohy|Počet|Celkem|Počet uzlů, které čekají na dokončení počátečního úkolu|Žádné dimenze|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/Workspaces

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Aktivní jádra|Yes|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Aktivní uzly|Yes|Aktivní uzly|Počet|Průměr|Počet spuštěných uzlů|Scénář, název_clusteru|
|Jádra nečinných|Yes|Jádra nečinných|Počet|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nečinné uzly|Yes|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů|Scénář, název_clusteru|
|Dokončená úloha|Yes|Dokončená úloha|Počet|Celkem|Počet dokončených úloh|Scénář, název_clusteru, ResultType|
|Úloha odeslána|Yes|Úloha odeslána|Počet|Celkem|Počet odeslaných úloh|Scénář, název_clusteru|
|Vynechávání jader|Yes|Vynechávání jader|Počet|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Ukončení uzlů|Yes|Ukončení uzlů|Počet|Průměr|Počet opouštících uzlů|Scénář, název_clusteru|
|Zrušené jádra|Yes|Zrušené jádra|Počet|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Přepnuté uzly|Yes|Přepnuté uzly|Počet|Průměr|Počet zrušených uzlů|Scénář, název_clusteru|
|Procento využití kvóty|Yes|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|
|Celkový počet jader|Yes|Celkový počet jader|Počet|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Celkem uzlů|Yes|Celkem uzlů|Počet|Průměr|Počet uzlů celkem|Scénář, název_clusteru|
|Nepoužitelné jádra|Yes|Nepoužitelné jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Nepoužité uzly|Yes|Nepoužité uzly|Počet|Průměr|Počet nepoužitelných uzlů|Scénář, název_clusteru|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Počet|Průměr|Počet zpracovaných transakcí.|Uzel, kanál, typ, stav|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Počet|Průměr|Počet spuštění chaincode (init nebo Invoke), jejichž časový limit vypršel.|Uzel, chaincode|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Počet|Průměr|Počet spuštění chaincode, která se nezdařila.|Uzel, chaincode|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Počet|Průměr|Počet spuštění chaincode, jejichž časový limit vypršel.|Uzel, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Počet|Průměr|Počet dokončených požadavků na překrytí chaincode|Uzel, typ, kanál, chaincode, úspěch|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Počet|Průměr|Počet přijatých požadavků na překrytí chaincode|Uzel, typ, kanál, chaincode|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Počet|Průměr|Kapacita fronty výstupů.|Uzel, hostitel, msg_type, kanál|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Počet|Průměr|Délka fronty výstupů|Uzel, hostitel, msg_type, kanál|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Počet|Průměr|Počet pracovních procesů front-výstupů|Uzel, kanál|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Počet|Průměr|Počet datových proudů na jiné uzly.|Uzel, kanál|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Počet|Průměr|Počet připojení TLS k ostatním uzlům|Uzel|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Počet|Průměr|Počet datových proudů z jiných uzlů.|Uzel|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Počet|Průměr|Počet vynechaných zpráv|Uzel, hostitel, kanál|
|ConnectionAccepted|Yes|Přijatá připojení|Počet|Celkem|Přijatá připojení|Uzel|
|ConnectionActive|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Uzel|
|ConnectionHandled|Yes|Zpracovaná připojení|Počet|Celkem|Zpracovaná připojení|Uzel|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Počet|Průměr|Počet aktivních uzlů v tomto kanálu|Uzel, kanál|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Počet|Průměr|Počet uzlů v tomto kanálu.|Uzel, kanál|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Počet|Průměr|Blokové číslo posledního bloku potvrzeno.|Uzel, kanál|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Počet|Průměr|Celkový počet návrhů přijatých pro transakce typu konfigurace.|Uzel, kanál|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Počet|Průměr|Stav vedoucího aktuálního uzlu: 1, pokud je to vedoucí jiný, 0.|Uzel, kanál|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Počet|Průměr|Počet změn vedoucího od spuštění procesu|Uzel, kanál|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Počet|Průměr|Celkový počet návrhů přijatých pro transakce typu normálního typu.|Uzel, kanál|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Počet|Průměr|Počet selhání návrhu.|Uzel, kanál|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Počet|Průměr|Číslo bloku posledního snímku.|Uzel, kanál|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Počet|Průměr|Střední velikost dávky v bajtech odeslaných na témata.|Uzel, téma|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Počet|Průměr|Střední kompresní poměr (jako procento) pro témata.|Uzel, téma|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Počet|Průměr|Počet bajtů za sekundu, které jsou čteny od zprostředkovatelů.|Uzel, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Počet|Průměr|Posun zadaný v metadatech bloku z posledního potvrzeného bloku|Uzel, kanál|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Počet|Průměr|Počet bajtů za sekundu zapsaných do zprostředkovatelů|Uzel, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Počet|Průměr|Počet záznamů za sekundu odeslaných do témat|Uzel, téma|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Počet|Průměr|Průměrný počet záznamů odeslaných na jednotlivé požadavky na témata.|Uzel, téma|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Počet|Průměr|Střední latence požadavku v MS ke zprostředkovatelům.|Uzel, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Počet|Průměr|Požadavků za sekundu odeslaných zprostředkovatelům.|Uzel, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Počet|Průměr|Střední velikost požadavku v bajtech na zprostředkovatele.|Uzel, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Počet|Průměr|Požadavků za sekundu odeslaných zprostředkovatelům.|Uzel, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Počet|Průměr|Střední velikost odezvy v bajtech od zprostředkovatelů.|Uzel, broker_id|
|CpuUsagePercentageInDouble|Yes|Procento využití procesoru|Procento|Maximum|Procento využití procesoru|Uzel|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Počet|Průměr|Počet bloků odeslaných službou doručení.|Uzel, kanál, filtrovaný data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Počet|Průměr|Počet požadavků na doručení, které byly dokončeny.|Uzel, kanál, filtrovaný data_type, úspěch|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Počet|Průměr|Počet přijatých požadavků na doručení.|Uzel, kanál, filtrovaný data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Počet|Průměr|Počet GRPC datových proudů, které byly uzavřeny pro službu doručení.|Uzel|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Počet|Průměr|Počet GRPC datových proudů, které byly otevřeny pro službu doručení.|Uzel|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Počet|Průměr|Počet instancí chaincode nebo upgrade, které se nezdařily.|Uzel, kanál, chaincode|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Počet|Průměr|Počet neúspěšných návrhů z důvodu duplicitního ID transakce|Uzel, kanál, chaincode|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Počet|Průměr|Počet neúspěšných potvrzení|Uzel, kanál, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Počet|Průměr|Počet návrhů, u kterých došlo k neúspěšným kontrolám seznamů ACL.|Uzel, kanál, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Počet|Průměr|Počet neúspěšných simulace návrhu|Uzel, kanál, chaincode|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Počet|Průměr|Počet přijatých návrhů.|Uzel|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Počet|Průměr|Počet návrhů, u kterých došlo k neúspěšnému počátečnímu ověření.|Uzel|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Počet|Průměr|Počet úspěšných návrhů.|Uzel|
|FabricVersion|Yes|FabricVersionDisplayName|Počet|Průměr|Aktivní verze prostředků infrastruktury|Uzel, verze|
|GossipCommMessagesReceived|Yes|GossipCommMessagesReceivedDisplayName|Počet|Průměr|Počet přijatých zpráv.|Uzel|
|GossipCommMessagesSent|Yes|GossipCommMessagesSentDisplayName|Počet|Průměr|Počet odeslaných zpráv.|Uzel|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|Počet|Průměr|Počet přetečení vyrovnávací paměti odchozí fronty.|Uzel|
|GossipLeaderElectionLeader|Yes|GossipLeaderElectionLeaderDisplayName|Počet|Průměr|Partner je vedoucím (1) nebo následným (0).|Uzel, kanál|
|GossipMembershipTotalPeersKnown|Yes|GossipMembershipTotalPeersKnownDisplayName|Počet|Průměr|Celkový počet známých partnerských uzlů.|Uzel, kanál|
|GossipPayloadBufferSize|Yes|GossipPayloadBufferSizeDisplayName|Počet|Průměr|Velikost vyrovnávací paměti datové části.|Uzel, kanál|
|GossipStateHeight|Yes|GossipStateHeightDisplayName|Počet|Průměr|Aktuální výška hlavní knihy.|Uzel, kanál|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Počet|Průměr|připojení gRPC se zavřela. Otevřené mínus uzavřené je aktivní počet připojení.|Uzel|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Počet|Průměr|otevřená připojení gRPC. Otevřené mínus uzavřené je aktivní počet připojení.|Uzel|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Počet|Průměr|Počet přijatých zpráv streamu.|Uzel, služba, metoda|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Počet|Průměr|Počet odeslaných zpráv streamu.|Uzel, služba, metoda|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Počet|Průměr|Počet dokončených požadavků datového proudu.|Uzel, služba, metoda, kód|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Počet|Průměr|Počet přijatých unárních požadavků.|Uzel, služba, metoda|
|IOReadBytes|Yes|Bajty čtení v/v|Bajty|Celkem|Bajty čtení v/v|Uzel|
|IOWriteBytes|Yes|Bajty zápisu v/v|Bajty|Celkem|Bajty zápisu v/v|Uzel|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Počet|Průměr|Výška řetězce v blocích|Uzel, kanál|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Počet|Průměr|Počet zpracovaných transakcí.|Uzel, kanál, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Počet|Průměr|Počet položek protokolu zkontrolovaných u aktivní úrovně protokolování.|Uzel, úroveň|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Počet|Průměr|Počet zapsaných záznamů protokolu|Uzel, úroveň|
|MemoryLimit|Yes|Omezení paměti|Bajty|Průměr|Omezení paměti|Uzel|
|MemoryUsage|Yes|Využití paměti|Bajty|Průměr|Využití paměti|Uzel|
|MemoryUsagePercentageInDouble|Yes|Procento využití paměti|Procento|Průměr|Procento využití paměti|Uzel|
|PendingTransactions|Yes|Nedokončené transakce|Počet|Průměr|Nedokončené transakce|Uzel|
|ProcessedBlocks|Yes|Zpracované bloky|Počet|Celkem|Zpracované bloky|Uzel|
|ProcessedTransactions|Yes|Zpracované transakce|Počet|Celkem|Zpracované transakce|Uzel|
|QueuedTransactions|Yes|Transakce ve frontě|Počet|Průměr|Transakce ve frontě|Uzel|
|RequestHandled|Yes|Zpracované žádosti|Počet|Celkem|Zpracované žádosti|Uzel|
|StorageUsage|Yes|Využití úložiště|Bajty|Průměr|Využití úložiště|Uzel|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Latence požadavku|Milisekund|Celkem|Doba, kterou server zavedl ke zpracování žádosti|Operace, ověřování, protokol, datacentrum|
|RequestsTraffic|Yes|Požadavky na provoz|Procento|Počet|Počet provedených požadavků|Operace, ověřování, protokol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Přístupy do mezipaměti (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|allcachemisses|Yes|Neúspěšné přístupy do mezipaměti (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|allcacheRead|Yes|Čtení z mezipaměti (založené na instancích)|BytesPerSecond|Maximum||ShardId, port, primární|
|allcacheWrite|Yes|Zápis do mezipaměti (na základě instance)|BytesPerSecond|Maximum||ShardId, port, primární|
|allconnectedclients|Yes|Připojení klienti (založené na instancích)|Počet|Maximum||ShardId, port, primární|
|allevictedkeys|Yes|Vyřazené klíče (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|allexpiredkeys|Yes|Klíče vypršení platnosti (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|allgetcommands|Yes|Get (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|alloperationsPerSecond|Yes|Operace za sekundu (založené na instancích)|Počet|Maximum||ShardId, port, primární|
|allserverLoad|Yes|Zatížení serveru (založené na instancích)|Procento|Maximum||ShardId, port, primární|
|allsetcommands|Yes|Sady (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|alltotalcommandsprocessed|Yes|Operace celkem (založené na instancích)|Počet|Celkem||ShardId, port, primární|
|alltotalkeys|Yes|Celkem klíčů (založen na instancích)|Počet|Maximum||ShardId, port, primární|
|allusedmemory|Yes|Využitá paměť (založená na instancích)|Bajty|Maximum||ShardId, port, primární|
|allusedmemorypercentage|Yes|Procento využité paměti (založené na instancích)|Procento|Maximum||ShardId, port, primární|
|allusedmemoryRss|Yes|RSS využité paměti (na základě instance)|Bajty|Maximum||ShardId, port, primární|
|cachehits|Yes|Přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachehits0|Yes|Přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|cachehits1|Yes|Přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|cachehits2|Yes|Přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|cachehits3|Yes|Přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|cachehits4|Yes|Přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|cachehits5|Yes|Přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|cachehits6|Yes|Přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|cachehits7|Yes|Přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|cachehits8|Yes|Přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|cachehits9|Yes|Přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|cacheLatency|Yes|Mikrosekundy latence mezipaměti (Preview)|Počet|Průměr||ShardId|
|cachemisses|Yes|Neúspěšné přístupy do mezipaměti|Počet|Celkem||ShardId|
|cachemisses0|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|cachemisses1|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|cachemisses2|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|cachemisses3|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|cachemisses4|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|cachemisses5|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|cachemisses6|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|cachemisses7|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|cachemisses8|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|cachemisses9|Yes|Neúspěšné přístupy do mezipaměti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|cachemissrate|Yes|Frekvence neúspěšných přístupů do mezipaměti|Procento|cachemissrate||ShardId|
|cacheRead|Yes|Čtení z mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheRead0|Yes|Čtení z mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead1|Yes|Čtení z mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead2|Yes|Čtení z mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead3|Yes|Čtení z mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead4|Yes|Čtení z mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead5|Yes|Čtení z mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead6|Yes|Čtení z mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead7|Yes|Čtení z mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead8|Yes|Čtení z mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheRead9|Yes|Čtení z mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite|Yes|Zápis do mezipaměti|BytesPerSecond|Maximum||ShardId|
|cacheWrite0|Yes|Zápis do mezipaměti (horizontálních oddílů 0)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite1|Yes|Zápis do mezipaměti (horizontálních oddílů 1)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite2|Yes|Zápis do mezipaměti (horizontálních oddílů 2)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite3|Yes|Zápis do mezipaměti (horizontálních oddílů 3)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite4|Yes|Zápis do mezipaměti (horizontálních oddílů 4)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite5|Yes|Zápis do mezipaměti (horizontálních oddílů 5)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite6|Yes|Zápis do mezipaměti (horizontálních oddílů 6)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite7|Yes|Zápis do mezipaměti (horizontálních oddílů 7)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite8|Yes|Zápis do mezipaměti (horizontálních oddílů 8)|BytesPerSecond|Maximum||Žádné dimenze|
|cacheWrite9|Yes|Zápis do mezipaměti (horizontálních oddílů 9)|BytesPerSecond|Maximum||Žádné dimenze|
|connectedclients|Yes|Připojení klienti|Počet|Maximum||ShardId|
|connectedclients0|Yes|Připojení klienti (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|connectedclients1|Yes|Připojení klienti (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|connectedclients2|Yes|Připojení klienti (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|connectedclients3|Yes|Připojení klienti (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|connectedclients4|Yes|Připojení klienti (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|connectedclients5|Yes|Připojení klienti (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|connectedclients6|Yes|Připojení klienti (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|connectedclients7|Yes|Připojení klienti (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|connectedclients8|Yes|Připojení klienti (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|connectedclients9|Yes|Připojení klienti (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|chyby|Yes|Chyby|Počet|Maximum||ShardId, ErrorType|
|evictedkeys|Yes|Vyloučené klíče|Počet|Celkem||ShardId|
|evictedkeys0|Yes|Vyřazené klíče (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|evictedkeys1|Yes|Vyřazené klíče (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|evictedkeys2|Yes|Vyřazené klíče (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|evictedkeys3|Yes|Vyřazené klíče (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|evictedkeys4|Yes|Vyřazené klíče (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|evictedkeys5|Yes|Vyřazení klíčů (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|evictedkeys6|Yes|Vyřazené klíče (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|evictedkeys7|Yes|Vyřazení klíčů (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|evictedkeys8|Yes|Vyřazené klíče (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|evictedkeys9|Yes|Vyřazené klíče (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|expiredkeys|Yes|Prošlé klíče|Počet|Celkem||ShardId|
|expiredkeys0|Yes|Klíče vypršení platnosti (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|expiredkeys1|Yes|Klíče vypršení platnosti (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|expiredkeys2|Yes|Klíče vypršení platnosti (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|expiredkeys3|Yes|Klíče vypršení platnosti (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|expiredkeys4|Yes|Klíče vypršení platnosti (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|expiredkeys5|Yes|Klíče vypršení platnosti (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|expiredkeys6|Yes|Klíče vypršení platnosti (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|expiredkeys7|Yes|Klíče vypršení platnosti (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|expiredkeys8|Yes|Klíče vypršení platnosti (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|expiredkeys9|Yes|Klíče vypršení platnosti (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|GetCommands|Yes|Příkazy Get|Počet|Celkem||ShardId|
|getcommands0|Yes|Načtení (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|getcommands1|Yes|Načtení (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|getcommands2|Yes|Get (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|getcommands3|Yes|Get (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|getcommands4|Yes|Get (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|getcommands5|Yes|Získá (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|getcommands6|Yes|Načtení (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|getcommands7|Yes|Načtení (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|getcommands8|Yes|Získá (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|getcommands9|Yes|Načtení (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|operationsPerSecond|Yes|Operace za sekundu|Počet|Maximum||ShardId|
|operationsPerSecond0|Yes|Operací za sekundu (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond1|Yes|Operací za sekundu (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond2|Yes|Operací za sekundu (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond3|Yes|Operací za sekundu (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond4|Yes|Operací za sekundu (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond5|Yes|Operací za sekundu (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond6|Yes|Operací za sekundu (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond7|Yes|Operací za sekundu (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond8|Yes|Operací za sekundu (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|operationsPerSecond9|Yes|Operací za sekundu (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|percentProcessorTime|Yes|Procesor|Procento|Maximum||ShardId|
|percentProcessorTime0|Yes|PROCESOR (horizontálních oddílů 0)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime1|Yes|PROCESOR (horizontálních oddílů 1)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime2|Yes|PROCESOR (horizontálních oddílů 2)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime3|Yes|PROCESOR (horizontálních oddílů 3)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime4|Yes|PROCESOR (horizontálních oddílů 4)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime5|Yes|PROCESOR (horizontálních oddílů 5)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime6|Yes|PROCESOR (horizontálních oddílů 6)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime7|Yes|PROCESOR (horizontálních oddílů 7)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime8|Yes|PROCESOR (horizontálních oddílů 8)|Procento|Maximum||Žádné dimenze|
|percentProcessorTime9|Yes|PROCESOR (horizontálních oddílů 9)|Procento|Maximum||Žádné dimenze|
|serverLoad|Yes|Zatížení serveru|Procento|Maximum||ShardId|
|serverLoad0|Yes|Zatížení serveru (horizontálních oddílů 0)|Procento|Maximum||Žádné dimenze|
|serverLoad1|Yes|Zatížení serveru (horizontálních oddílů 1)|Procento|Maximum||Žádné dimenze|
|serverLoad2|Yes|Zatížení serveru (horizontálních oddílů 2)|Procento|Maximum||Žádné dimenze|
|serverLoad3|Yes|Zatížení serveru (horizontálních oddílů 3)|Procento|Maximum||Žádné dimenze|
|serverLoad4|Yes|Zatížení serveru (horizontálních oddílů 4)|Procento|Maximum||Žádné dimenze|
|serverLoad5|Yes|Zatížení serveru (horizontálních oddílů 5)|Procento|Maximum||Žádné dimenze|
|serverLoad6|Yes|Zatížení serveru (horizontálních oddílů 6)|Procento|Maximum||Žádné dimenze|
|serverLoad7|Yes|Zatížení serveru (horizontálních oddílů 7)|Procento|Maximum||Žádné dimenze|
|serverLoad8|Yes|Zatížení serveru (horizontálních oddílů 8)|Procento|Maximum||Žádné dimenze|
|serverLoad9|Yes|Zatížení serveru (horizontálních oddílů 9)|Procento|Maximum||Žádné dimenze|
|setcommands|Yes|Sady|Počet|Celkem||ShardId|
|setcommands0|Yes|Sady (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|setcommands1|Yes|Sady (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|setcommands2|Yes|Sady (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|setcommands3|Yes|Sady (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|setcommands4|Yes|Sady (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|setcommands5|Yes|Sady (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|setcommands6|Yes|Sady (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|setcommands7|Yes|Sady (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|setcommands8|Yes|Sady (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|setcommands9|Yes|Sady (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed|Yes|Celkový počet operací|Počet|Celkem||ShardId|
|totalcommandsprocessed0|Yes|Celkem operací (horizontálních oddílů 0)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed1|Yes|Celkem operací (horizontálních oddílů 1)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed2|Yes|Celkem operací (horizontálních oddílů 2)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed3|Yes|Celkem operací (horizontálních oddílů 3)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed4|Yes|Celkem operací (horizontálních oddílů 4)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed5|Yes|Celkem operací (horizontálních oddílů 5)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed6|Yes|Celkem operací (horizontálních oddílů 6)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed7|Yes|Celkem operací (horizontálních oddílů 7)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed8|Yes|Celkem operací (horizontálních oddílů 8)|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed9|Yes|Celkem operací (horizontálních oddílů 9)|Počet|Celkem||Žádné dimenze|
|totalkeys|Yes|Celkem klíčů|Počet|Maximum||ShardId|
|totalkeys0|Yes|Celkem klíčů (horizontálních oddílů 0)|Počet|Maximum||Žádné dimenze|
|totalkeys1|Yes|Celkem klíčů (horizontálních oddílů 1)|Počet|Maximum||Žádné dimenze|
|totalkeys2|Yes|Celkem klíčů (horizontálních oddílů 2)|Počet|Maximum||Žádné dimenze|
|totalkeys3|Yes|Celkem klíčů (horizontálních oddílů 3)|Počet|Maximum||Žádné dimenze|
|totalkeys4|Yes|Celkem klíčů (horizontálních oddílů 4)|Počet|Maximum||Žádné dimenze|
|totalkeys5|Yes|Celkem klíčů (horizontálních oddílů 5)|Počet|Maximum||Žádné dimenze|
|totalkeys6|Yes|Celkem klíčů (horizontálních oddílů 6)|Počet|Maximum||Žádné dimenze|
|totalkeys7|Yes|Celkem klíčů (horizontálních oddílů 7)|Počet|Maximum||Žádné dimenze|
|totalkeys8|Yes|Celkem klíčů (horizontálních oddílů 8)|Počet|Maximum||Žádné dimenze|
|totalkeys9|Yes|Celkem klíčů (horizontálních oddílů 9)|Počet|Maximum||Žádné dimenze|
|usedmemory|Yes|Využitá paměť|Bajty|Maximum||ShardId|
|usedmemory0|Yes|Využitá paměť (horizontálních oddílů 0)|Bajty|Maximum||Žádné dimenze|
|usedmemory1|Yes|Využitá paměť (horizontálních oddílů 1)|Bajty|Maximum||Žádné dimenze|
|usedmemory2|Yes|Využitá paměť (horizontálních oddílů 2)|Bajty|Maximum||Žádné dimenze|
|usedmemory3|Yes|Využitá paměť (horizontálních oddílů 3)|Bajty|Maximum||Žádné dimenze|
|usedmemory4|Yes|Využitá paměť (horizontálních oddílů 4)|Bajty|Maximum||Žádné dimenze|
|usedmemory5|Yes|Využitá paměť (horizontálních oddílů 5)|Bajty|Maximum||Žádné dimenze|
|usedmemory6|Yes|Využitá paměť (horizontálních oddílů 6)|Bajty|Maximum||Žádné dimenze|
|usedmemory7|Yes|Využitá paměť (horizontálních oddílů 7)|Bajty|Maximum||Žádné dimenze|
|usedmemory8|Yes|Využitá paměť (horizontálních oddílů 8)|Bajty|Maximum||Žádné dimenze|
|usedmemory9|Yes|Využitá paměť (horizontálních oddílů 9)|Bajty|Maximum||Žádné dimenze|
|usedmemorypercentage|Yes|Využitá paměť v procentech|Procento|Maximum||ShardId|
|usedmemoryRss|Yes|RSS využité paměti|Bajty|Maximum||ShardId|
|usedmemoryRss0|Yes|RSS využité paměti (horizontálních oddílů 0)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss1|Yes|RSS využité paměti (horizontálních oddílů 1)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss2|Yes|RSS využité paměti (horizontálních oddílů 2)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss3|Yes|RSS využité paměti (horizontálních oddílů 3)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss4|Yes|RSS využité paměti (horizontálních oddílů 4)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss5|Yes|RSS využité paměti (horizontálních oddílů 5)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss6|Yes|RSS využité paměti (horizontálních oddílů 6)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss7|Yes|RSS využité paměti (horizontálních oddílů 7)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss8|Yes|RSS využité paměti (horizontálních oddílů 8)|Bajty|Maximum||Žádné dimenze|
|usedmemoryRss9|Yes|RSS využité paměti (horizontálních oddílů 9)|Bajty|Maximum||Žádné dimenze|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. cache/redisEnterprise

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|cachehits|Yes|Přístupy do mezipaměti|Počet|Celkem||Žádné dimenze|
|cacheLatency|Yes|Mikrosekundy latence mezipaměti (Preview)|Počet|Průměr||InstanceId|
|cachemisses|Yes|Neúspěšné přístupy do mezipaměti|Počet|Celkem||InstanceId|
|cacheRead|Yes|Čtení z mezipaměti|BytesPerSecond|Maximum||InstanceId|
|cacheWrite|Yes|Zápis do mezipaměti|BytesPerSecond|Maximum||InstanceId|
|connectedclients|Yes|Připojení klienti|Počet|Maximum||InstanceId|
|chyby|Yes|Chyby|Počet|Maximum||InstanceId, ErrorType|
|evictedkeys|Yes|Vyloučené klíče|Počet|Celkem||Žádné dimenze|
|expiredkeys|Yes|Prošlé klíče|Počet|Celkem||Žádné dimenze|
|GetCommands|Yes|Příkazy Get|Počet|Celkem||Žádné dimenze|
|operationsPerSecond|Yes|Operace za sekundu|Počet|Maximum||Žádné dimenze|
|percentProcessorTime|Yes|Procesor|Procento|Maximum||InstanceId|
|serverLoad|Yes|Zatížení serveru|Procento|Maximum||Žádné dimenze|
|setcommands|Yes|Sady|Počet|Celkem||Žádné dimenze|
|totalcommandsprocessed|Yes|Celkový počet operací|Počet|Celkem||Žádné dimenze|
|totalkeys|Yes|Celkem klíčů|Počet|Maximum||Žádné dimenze|
|usedmemory|Yes|Využitá paměť|Bajty|Maximum||Žádné dimenze|
|usedmemorypercentage|Yes|Využitá paměť v procentech|Procento|Maximum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profily

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Poměr přístupů do bajtu|Procento|Průměr|Toto je poměr celkového počtu bajtů poskytovaných z mezipaměti v porovnání s celkovými bajty odpovědí.|Koncový bod|
|OriginHealthPercentage|Yes|Počáteční procento stavu|Procento|Průměr|Procento úspěšných sond stavu z AFDX do back-endu.|Původ, původce|
|OriginLatency|Yes|Latence původu|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek odeslán přes AFDX Edge do back-endu, dokud AFDX nepřijal poslední bajt odpovědi z back-endu.|Počátek, koncový bod|
|OriginRequestCount|Yes|Počet požadavků na původ|Počet|Celkem|Počet požadavků odeslaných z AFDX na počátek.|Stavu protokolu HTTP, HttpStatusGroup, počátek, koncový bod|
|Percentage4XX|Yes|Procento 4XX|Procento|Průměr|Procento všech požadavků klientů, pro které je kód stavu odpovědi 4XX|Koncový bod, ClientRegion, ClientCountry|
|Percentage5XX|Yes|Procento 5XX|Procento|Průměr|Procento všech požadavků klientů, pro které je kód stavu odpovědi 5XX|Koncový bod, ClientRegion, ClientCountry|
|RequestCount|Yes|Počet požadavků|Počet|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry, koncový bod|
|RequestSize|Yes|Velikost požadavku|Bajty|Celkem|Počet bajtů odeslaných jako požadavek od klientů do AFDX.|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry, koncový bod|
|ResponseSize|Yes|Velikost odpovědi|Bajty|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry, koncový bod|
|TotalLatency|Yes|Celková latence|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry, koncový bod|
|WebApplicationFirewallRequestCount|Yes|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/sloty/role

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty čtení z disku/s|No|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|RoleInstanceId|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId|
|Bajty zápisu na disk/s|No|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|RoleInstanceId|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId|
|Síťové vstupy|Yes|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|RoleInstanceId|
|Síťové výstupy|Yes|Síťové výstupy|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|RoleInstanceId|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty čtení z disku/s|No|Čtení z disku|BytesPerSecond|Průměr|Průměrný počet bajtů přečtených z disku během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Bajty zápisu na disk/s|No|Zápis na disk|BytesPerSecond|Průměr|Průměrný počet bajtů zapsaných na disk během období monitorování|Žádné dimenze|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Síťové vstupy|Yes|Síťové vstupy|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz).|Žádné dimenze|
|Síťové výstupy|Yes|Síťové výstupy|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz).|Žádné dimenze|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači.|Žádné dimenze|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|UsedCapacity|No|Využitá kapacita|Bajty|Průměr|Kapacita využitá účtem|Žádné dimenze|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|BlobCapacity|No|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|No|Počet objektů BLOB|Počet|Průměr|Počet objektů BLOB v Blob service účtu úložiště|BlobType, úroveň|
|ContainerCount|Yes|Počet kontejnerů objektů BLOB|Počet|Průměr|Počet kontejnerů v Blob service účtu úložiště.|Žádné dimenze|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|IndexCapacity|No|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá ADLS Gen2 (hierarchický) index v bajtech|Žádné dimenze|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/služby

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|Kapacita zařízení|No|Kapacita souboru|Bajty|Průměr|Velikost úložiště využitá službou File účtu úložiště v bajtech|Sdílená složka|
|FileCount|No|Počet souborů|Počet|Průměr|Počet souborů v Souborové službě účtu úložiště.|Sdílená složka|
|FileShareCount|No|Počet sdílených souborů|Počet|Průměr|Počet sdílených souborů v Souborové službě účtu úložiště.|Žádné dimenze|
|FileShareQuota|No|Velikost kvóty sdílení souborů|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílená složka|
|FileShareSnapshotCount|No|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílená složka|
|FileShareSnapshotSize|No|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílená složka|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování, sdílení souborů|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|QueueCapacity|Yes|Kapacita fronty|Bajty|Průměr|Velikost úložiště využitá Služba front účtu úložiště v bajtech|Žádné dimenze|
|QueueCount|Yes|Počet front|Počet|Průměr|Počet front v Služba front účtu úložiště.|Žádné dimenze|
|QueueMessageCount|Yes|Počet zpráv ve frontě|Počet|Průměr|Přibližný počet zpráv ve frontě v Služba front účtu úložiště.|Žádné dimenze|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech). Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách). Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|TableCapacity|Yes|Kapacita tabulky|Bajty|Průměr|Velikost úložiště využitá Table service účtu úložiště v bajtech|Žádné dimenze|
|TableCount|Yes|Počet tabulek|Počet|Průměr|Počet tabulek v Table service účtu úložiště|Žádné dimenze|
|TableEntityCount|Yes|Počet entit tabulky|Počet|Průměr|Počet entit tabulky v Table service účtu úložiště|Žádné dimenze|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blokovaná volání|Počet|Celkem|Počet volání, která překročily limit nebo kvótu.|ApiName, OperationName, oblast|
|CharactersTrained|Yes|Vyškolené znaky|Počet|Celkem|Celkový počet vyškolených znaků|ApiName, OperationName, oblast|
|CharactersTranslated|Yes|Přeložené znaky|Počet|Celkem|Celkový počet znaků v příchozím textovém požadavku.|ApiName, OperationName, oblast|
|ClientErrors|Yes|Chyby klienta|Počet|Celkem|Počet volání s chybou na straně klienta (kód odpovědi HTTP 4xx).|ApiName, OperationName, oblast|
|DataIn|Yes|Data v|Bajty|Celkem|Velikost příchozích dat v bajtech|ApiName, OperationName, oblast|
|Data|Yes|Výstupní data|Bajty|Celkem|Velikost odchozích dat v bajtech|ApiName, OperationName, oblast|
|Latence|Yes|Latence|Milisekund|Průměr|Latence v milisekundách|ApiName, OperationName, oblast|
|LearnedEvents|Yes|Zjištěné události|Počet|Celkem|Počet zjištěných událostí.|IsMatchBaseline, režim, RunId|
|MatchedRewards|Yes|Spárované odměny|Počet|Celkem| Počet spárovaných odměny|IsMatchBaseline, režim, RunId|
|ObservedRewards|Yes|Zjištěné ceny|Počet|Celkem|Počet pozorovaných neprospěchů|IsMatchBaseline, režim, RunId|
|ProcessedCharacters|Yes|Zpracované znaky|Počet|Celkem|Počet znaků.|ApiName, vlastnost vlastnosti, UsageChannel, oblast|
|ProcessedTextRecords|Yes|Zpracované textové záznamy|Počet|Celkem|Počet textových záznamů.|ApiName, vlastnost vlastnosti, UsageChannel, oblast|
|ServerErrors|Yes|Chyby serveru|Počet|Celkem|Počet volání s interní chybou služby (kód odpovědi HTTP 5xx).|ApiName, OperationName, oblast|
|SpeechSessionDuration|Yes|Doba trvání relace řeči|Sekundy|Celkem|Celková doba trvání relace řeči v sekundách.|ApiName, OperationName, oblast|
|SuccessfulCalls|Yes|Úspěšná volání|Počet|Celkem|Počet úspěšných volání.|ApiName, OperationName, oblast|
|TotalCalls|Yes|Celkový počet volání|Počet|Celkem|Celkový počet volání.|ApiName, OperationName, oblast|
|TotalErrors|Yes|Celkový počet chyb|Počet|Celkem|Celkový počet volání s chybovou odpovědí (kód odpovědi HTTP 4xx nebo 5xx).|ApiName, OperationName, oblast|
|TotalTokenCalls|Yes|Celkový počet volání tokenu|Počet|Celkem|Celkový počet volání tokenů|ApiName, OperationName, oblast|
|TotalTransactions|Yes|Celkový počet transakcí|Počet|Celkem|Celkový počet transakcí|Žádné dimenze|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Žádosti rozhraní API pro ověřování|Počet|Počet|Počet všech požadavků na koncový bod ověřování služby Communication Services.|Operace, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Chat – požadavky rozhraní API|Počet|Počet|Počet všech požadavků na koncový bod chatu komunikačních služeb.|Operace, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|Žádosti rozhraní API SMS|Počet|Počet|Počet všech požadavků na koncový bod serveru SMS služby Communication Services.|Operace, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. COMPUTE/cloudServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|RoleInstanceId, RoleId|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId, RoleId|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|RoleInstanceId, RoleId|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId, RoleId|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. COMPUTE/cloudServices/role

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|RoleInstanceId, RoleId|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|RoleInstanceId, RoleId|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|RoleInstanceId, RoleId|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|RoleInstanceId, RoleId|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>Microsoft. COMPUTE/disky

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty přečtené z kompozitního disku/s|No|Bajty přečtené z disku za sekundu (Preview)|Bajty|Průměr|Počet bajtů za sekundu přečtených z disku během období monitorování, Poznámka: Tato metrika je ve verzi Preview a může se změnit dřív, než je všeobecně dostupná.||
|Operace čtení složeného disku za sekundu|No|Operace čtení z disku za sekundu (Preview)|Bajty|Průměr|Počet přečtených IOs na disku během období monitorování: Tato metrika je ve verzi Preview a může se změnit předtím, než bude všeobecně dostupná.||
|Bajty zapsané do složeného disku/s|No|Bajty zápisu na disk/s (Preview)|Bajty|Průměr|Počet bajtů za sekundu zapsaných na disk během období monitorování: Tato metrika je ve verzi Preview a může se změnit předtím, než bude všeobecně dostupná.||
|Operace zápisu kompozitního disku za sekundu|No|Operace zápisu na disk za sekundu (Preview)|Bajty|Průměr|Počet zařízení s IOs pro zápis provedených na disku během období monitorování, Poznámka: Tato metrika je ve verzi Preview a může se změnit dřív, než je všeobecně dostupná.||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Spotřebované kredity procesoru|Yes|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Zbývající kredity procesoru|Yes|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Procento spotřebované šířky pásma datového disku|Yes|Procento spotřebované šířky pásma datového disku|Procento|Průměr|Procento využité šířky pásma datového disku za minutu|(|
|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Yes|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Procento|Průměr|Procento využitých vstupně-výstupních datových disků za minutu|(|
|Maximální šířka pásma shluku datového disku|Yes|Maximální šířka pásma shluku datového disku|Počet|Průměr|Maximální počet bajtů za sekundu datový disk propustnosti se může dosáhnout při roztržení.|(|
|Maximální počet shlukových vstupně-výstupních operací datového disku|Yes|Maximální počet shlukových vstupně-výstupních operací datového disku|Počet|Průměr|Maximální datový disk IOPS může dosáhnout s shluking|(|
|Hloubka fronty datového disku|Yes|Hloubka fronty datového disku|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z datového disku za sekundu|Yes|Bajty přečtené z datového disku za sekundu|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Operace čtení z datového disku za sekundu|Yes|Operace čtení z datového disku za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Šířka pásma cíle datového disku|Yes|Šířka pásma cíle datového disku|Počet|Průměr|Datový disk propustnosti v bajtech za sekundu může dosáhnout bez nárůstu zatížení.|(|
|Cílový datový disk – IOPS|Yes|Cílový datový disk – IOPS|Počet|Průměr|Datový disk s IOPS směrného plánu se může dosáhnout bez shlukování|(|
|Procento využitých kreditů v bitech za sekundu datového disku|Yes|Procento využitých kreditů v bitech za sekundu datového disku|Procento|Průměr|Procento kreditů na základě propustnosti datového disku zatím používaných|(|
|Procentuální podíl využitých kreditů v/v pro datový disk|Yes|Procentuální podíl využitých kreditů v/v pro datový disk|Procento|Průměr|Procento využitých kreditů zatížení/výstupů datového disku zatím|(|
|Bajty zapsané na datový disk/s|Yes|Bajty zapsané na datový disk/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace zápisu na datový disk/s|Yes|Operace zápisu na datový disk/s|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádné dimenze|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Příchozí toky|Yes|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření příchozích toků|Yes|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádné dimenze|
|Síťové vstupy|Yes|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádné dimenze|
|Celková síť|Yes|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Yes|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádné dimenze|
|Celkový počet síťových výstupů|Yes|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Procento spotřebované šířky pásma disku operačního systému|Yes|Procento spotřebované šířky pásma disku operačního systému|Procento|Průměr|Procento spotřebované šířky pásma disku s operačním systémem za minutu|(|
|Procento využitého počtu IOPS disku operačního systému|Yes|Procento využitého počtu IOPS disku operačního systému|Procento|Průměr|Procento počtu vstupně-výstupních disků operačního systému za minutu|(|
|Maximální šířka pásma shluku pro disk s operačním systémem|Yes|Maximální šířka pásma shluku pro disk s operačním systémem|Počet|Průměr|Maximální počet bajtů za sekundu disk s operačním systémem s propustností může dosáhnout s shluking|(|
|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Yes|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Počet|Průměr|Maximální disk s operačním systémem IOPS může dosáhnout s roztržením|(|
|Hloubka fronty disku s operačním systémem|Yes|Hloubka fronty disku s operačním systémem|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z disku s operačním systémem/s|Yes|Bajty přečtené z disku s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z disku s operačním systémem za sekundu|Yes|Operace čtení z disku s operačním systémem za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Šířka pásma cíle disku operačního systému|Yes|Šířka pásma cíle disku operačního systému|Počet|Průměr|Počet bajtů podle směrného plánu za sekundu disk s operačním systémem může dosáhnout bez roztržení.|(|
|Cílový disk s operačním systémem – IOPS|Yes|Cílový disk s operačním systémem – IOPS|Počet|Průměr|Základní disk s operačním systémem IOPS se může dosáhnout bez nárůstu zatížení.|(|
|Procento kreditů využitých za sekundu na disk s operačním systémem|Yes|Procento kreditů využitých za sekundu na disk s operačním systémem|Procento|Průměr|Procento kreditů využití šířky pásma na disku s operačním systémem zatím|(|
|Procentuální podíl využitých kreditů v/v disku operačního systému|Yes|Procentuální podíl využitých kreditů v/v disku operačního systému|Procento|Průměr|Procento kreditů vstupně-výstupních operací na disku s operačním systémem zatím používaných|(|
|Bajty zápisu na disk s operačním systémem/s|Yes|Bajty zápisu na disk s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu na disk s operačním systémem za sekundu|Yes|Operace zápisu na disk s operačním systémem za sekundu|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Odchozí toky|Yes|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření odchozích toků|Yes|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádné dimenze|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádné dimenze|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Yes|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Yes|Neúspěšné čtení mezipaměti datových disků Premium|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Yes|Počet přístupů do mezipaměti disku s operačním systémem Premium|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Yes|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma disku v mezipaměti spotřebované virtuálním počítačem|Žádné dimenze|
|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Yes|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Procento|Průměr|Procento IOPS disku uložených v mezipaměti, spotřebované virtuálním počítačem|Žádné dimenze|
|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma neuloženého disku v mezipaměti spotřebované virtuálním počítačem|Žádné dimenze|
|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Yes|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Procento|Průměr|Procento neuloženého počtu vstupně-výstupních operací disku využívané virtuálním počítačem|Žádné dimenze|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Spotřebované kredity procesoru|Yes|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Zbývající kredity procesoru|Yes|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Procento spotřebované šířky pásma datového disku|Yes|Procento spotřebované šířky pásma datového disku|Procento|Průměr|Procento využité šířky pásma datového disku za minutu|LUN, VMName|
|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Yes|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Procento|Průměr|Procento využitých vstupně-výstupních datových disků za minutu|LUN, VMName|
|Maximální šířka pásma shluku datového disku|Yes|Maximální šířka pásma shluku datového disku|Počet|Průměr|Maximální počet bajtů za sekundu datový disk propustnosti se může dosáhnout při roztržení.|LUN, VMName|
|Maximální počet shlukových vstupně-výstupních operací datového disku|Yes|Maximální počet shlukových vstupně-výstupních operací datového disku|Počet|Průměr|Maximální datový disk IOPS může dosáhnout s shluking|LUN, VMName|
|Hloubka fronty datového disku|Yes|Hloubka fronty datového disku|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|LUN, VMName|
|Bajty přečtené z datového disku za sekundu|Yes|Bajty přečtené z datového disku za sekundu|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|LUN, VMName|
|Operace čtení z datového disku za sekundu|Yes|Operace čtení z datového disku za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|LUN, VMName|
|Šířka pásma cíle datového disku|Yes|Šířka pásma cíle datového disku|Počet|Průměr|Datový disk propustnosti v bajtech za sekundu může dosáhnout bez nárůstu zatížení.|LUN, VMName|
|Cílový datový disk – IOPS|Yes|Cílový datový disk – IOPS|Počet|Průměr|Datový disk s IOPS směrného plánu se může dosáhnout bez shlukování|LUN, VMName|
|Procento využitých kreditů v bitech za sekundu datového disku|Yes|Procento využitých kreditů v bitech za sekundu datového disku|Procento|Průměr|Procento kreditů na základě propustnosti datového disku zatím používaných|LUN, VMName|
|Procentuální podíl využitých kreditů v/v pro datový disk|Yes|Procentuální podíl využitých kreditů v/v pro datový disk|Procento|Průměr|Procento využitých kreditů zatížení/výstupů datového disku zatím|LUN, VMName|
|Bajty zapsané na datový disk/s|Yes|Bajty zapsané na datový disk/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|LUN, VMName|
|Operace zápisu na datový disk/s|Yes|Operace zápisu na datový disk/s|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|LUN, VMName|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|VMName|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|VMName|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|VMName|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|VMName|
|Příchozí toky|Yes|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|VMName|
|Maximální rychlost vytváření příchozích toků|Yes|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|VMName|
|Síťové vstupy|Yes|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|VMName|
|Celková síť|Yes|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|VMName|
|Síťové výstupy|Yes|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|VMName|
|Celkový počet síťových výstupů|Yes|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|VMName|
|Procento spotřebované šířky pásma disku operačního systému|Yes|Procento spotřebované šířky pásma disku operačního systému|Procento|Průměr|Procento spotřebované šířky pásma disku s operačním systémem za minutu|LUN, VMName|
|Procento využitého počtu IOPS disku operačního systému|Yes|Procento využitého počtu IOPS disku operačního systému|Procento|Průměr|Procento počtu vstupně-výstupních disků operačního systému za minutu|LUN, VMName|
|Maximální šířka pásma shluku pro disk s operačním systémem|Yes|Maximální šířka pásma shluku pro disk s operačním systémem|Počet|Průměr|Maximální počet bajtů za sekundu disk s operačním systémem s propustností může dosáhnout s shluking|LUN, VMName|
|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Yes|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Počet|Průměr|Maximální disk s operačním systémem IOPS může dosáhnout s roztržením|LUN, VMName|
|Hloubka fronty disku s operačním systémem|Yes|Hloubka fronty disku s operačním systémem|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|VMName|
|Bajty přečtené z disku s operačním systémem/s|Yes|Bajty přečtené z disku s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Operace čtení z disku s operačním systémem za sekundu|Yes|Operace čtení z disku s operačním systémem za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Šířka pásma cíle disku operačního systému|Yes|Šířka pásma cíle disku operačního systému|Počet|Průměr|Počet bajtů podle směrného plánu za sekundu disk s operačním systémem může dosáhnout bez roztržení.|LUN, VMName|
|Cílový disk s operačním systémem – IOPS|Yes|Cílový disk s operačním systémem – IOPS|Počet|Průměr|Základní disk s operačním systémem IOPS se může dosáhnout bez nárůstu zatížení.|LUN, VMName|
|Procento kreditů využitých za sekundu na disk s operačním systémem|Yes|Procento kreditů využitých za sekundu na disk s operačním systémem|Procento|Průměr|Procento kreditů využití šířky pásma na disku s operačním systémem zatím|LUN, VMName|
|Procentuální podíl využitých kreditů v/v disku operačního systému|Yes|Procentuální podíl využitých kreditů v/v disku operačního systému|Procento|Průměr|Procento kreditů vstupně-výstupních operací na disku s operačním systémem zatím používaných|LUN, VMName|
|Bajty zápisu na disk s operačním systémem/s|Yes|Bajty zápisu na disk s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|VMName|
|Operace zápisu na disk s operačním systémem za sekundu|Yes|Operace zápisu na disk s operačním systémem za sekundu|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|VMName|
|Odchozí toky|Yes|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|VMName|
|Maximální rychlost vytváření odchozích toků|Yes|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|VMName|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|VMName|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Yes|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|LUN, VMName|
|Neúspěšné čtení mezipaměti datových disků Premium|Yes|Neúspěšné čtení mezipaměti datových disků Premium|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|LUN, VMName|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Yes|Počet přístupů do mezipaměti disku s operačním systémem Premium|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|VMName|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Yes|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|VMName|
|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma disku v mezipaměti spotřebované virtuálním počítačem|VMName|
|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Yes|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Procento|Průměr|Procento IOPS disku uložených v mezipaměti, spotřebované virtuálním počítačem|VMName|
|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma neuloženého disku v mezipaměti spotřebované virtuálním počítačem|VMName|
|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Yes|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Procento|Průměr|Procento neuloženého počtu vstupně-výstupních operací disku využívané virtuálním počítačem|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Spotřebované kredity procesoru|Yes|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných virtuálním počítačem. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Zbývající kredity procesoru|Yes|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování. K dispozici pouze na virtuálních počítačích řady B-Series|Žádné dimenze|
|Procento spotřebované šířky pásma datového disku|Yes|Procento spotřebované šířky pásma datového disku|Procento|Průměr|Procento využité šířky pásma datového disku za minutu|(|
|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Yes|Procento spotřebovaného počtu vstupně-výstupních operací datového disku|Procento|Průměr|Procento využitých vstupně-výstupních datových disků za minutu|(|
|Maximální šířka pásma shluku datového disku|Yes|Maximální šířka pásma shluku datového disku|Počet|Průměr|Maximální počet bajtů za sekundu datový disk propustnosti se může dosáhnout při roztržení.|(|
|Maximální počet shlukových vstupně-výstupních operací datového disku|Yes|Maximální počet shlukových vstupně-výstupních operací datového disku|Počet|Průměr|Maximální datový disk IOPS může dosáhnout s shluking|(|
|Hloubka fronty datového disku|Yes|Hloubka fronty datového disku|Počet|Průměr|Hloubka fronty datových disků (nebo délka fronty)|(|
|Bajty přečtené z datového disku za sekundu|Yes|Bajty přečtené z datového disku za sekundu|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování|(|
|Operace čtení z datového disku za sekundu|Yes|Operace čtení z datového disku za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování|(|
|Šířka pásma cíle datového disku|Yes|Šířka pásma cíle datového disku|Počet|Průměr|Datový disk propustnosti v bajtech za sekundu může dosáhnout bez nárůstu zatížení.|(|
|Cílový datový disk – IOPS|Yes|Cílový datový disk – IOPS|Počet|Průměr|Datový disk s IOPS směrného plánu se může dosáhnout bez shlukování|(|
|Procento využitých kreditů v bitech za sekundu datového disku|Yes|Procento využitých kreditů v bitech za sekundu datového disku|Procento|Průměr|Procento kreditů na základě propustnosti datového disku zatím používaných|(|
|Procentuální podíl využitých kreditů v/v pro datový disk|Yes|Procentuální podíl využitých kreditů v/v pro datový disk|Procento|Průměr|Procento využitých kreditů zatížení/výstupů datového disku zatím|(|
|Bajty zapsané na datový disk/s|Yes|Bajty zapsané na datový disk/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování|(|
|Operace zápisu na datový disk/s|Yes|Operace zápisu na datový disk/s|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování|(|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Bajty přečtené z disku během období monitorování|Žádné dimenze|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Čtení z disku – IOPS|Žádné dimenze|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Bajty zapsané na disk během období monitorování|Žádné dimenze|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Zápis na disk – IOPS|Žádné dimenze|
|Příchozí toky|Yes|Příchozí toky|Počet|Průměr|Příchozí toky představují počet současných toků v příchozím směru (provoz směřující do virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření příchozích toků|Yes|Maximální rychlost vytváření příchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření příchozích toků (provoz, který do virtuálního počítače směřuje)|Žádné dimenze|
|Síťové vstupy|Yes|Síť v fakturovatelný (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz) (zastaralé)|Žádné dimenze|
|Celková síť|Yes|Celková síť|Bajty|Celkem|Počet bajtů přijatých virtuálními počítači na všech síťových rozhraních (příchozí provoz)|Žádné dimenze|
|Síťové výstupy|Yes|Fakturovatelná odchozí síť (zastaralé)|Bajty|Celkem|Počet fakturovatelných bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz) (zastaralé)|Žádné dimenze|
|Celkový počet síťových výstupů|Yes|Celkový počet síťových výstupů|Bajty|Celkem|Počet bajtů vycházejících ze všech síťových rozhraní virtuálních počítačů (odchozí provoz)|Žádné dimenze|
|Procento spotřebované šířky pásma disku operačního systému|Yes|Procento spotřebované šířky pásma disku operačního systému|Procento|Průměr|Procento spotřebované šířky pásma disku s operačním systémem za minutu|(|
|Procento využitého počtu IOPS disku operačního systému|Yes|Procento využitého počtu IOPS disku operačního systému|Procento|Průměr|Procento počtu vstupně-výstupních disků operačního systému za minutu|(|
|Maximální šířka pásma shluku pro disk s operačním systémem|Yes|Maximální šířka pásma shluku pro disk s operačním systémem|Počet|Průměr|Maximální počet bajtů za sekundu disk s operačním systémem s propustností může dosáhnout s shluking|(|
|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Yes|Maximální počet shlukových vstupně-VÝSTUPních operací disku|Počet|Průměr|Maximální disk s operačním systémem IOPS může dosáhnout s roztržením|(|
|Hloubka fronty disku s operačním systémem|Yes|Hloubka fronty disku s operačním systémem|Počet|Průměr|Hloubka fronty disku s operačním systémem (nebo délka fronty)|Žádné dimenze|
|Bajty přečtené z disku s operačním systémem/s|Yes|Bajty přečtené z disku s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu přečtených z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace čtení z disku s operačním systémem za sekundu|Yes|Operace čtení z disku s operačním systémem za sekundu|CountPerSecond|Průměr|Čtení IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Šířka pásma cíle disku operačního systému|Yes|Šířka pásma cíle disku operačního systému|Počet|Průměr|Počet bajtů podle směrného plánu za sekundu disk s operačním systémem může dosáhnout bez roztržení.|(|
|Cílový disk s operačním systémem – IOPS|Yes|Cílový disk s operačním systémem – IOPS|Počet|Průměr|Základní disk s operačním systémem IOPS se může dosáhnout bez nárůstu zatížení.|(|
|Procento kreditů využitých za sekundu na disk s operačním systémem|Yes|Procento kreditů využitých za sekundu na disk s operačním systémem|Procento|Průměr|Procento kreditů využití šířky pásma na disku s operačním systémem zatím|(|
|Procentuální podíl využitých kreditů v/v disku operačního systému|Yes|Procentuální podíl využitých kreditů v/v disku operačního systému|Procento|Průměr|Procento kreditů vstupně-výstupních operací na disku s operačním systémem zatím používaných|(|
|Bajty zápisu na disk s operačním systémem/s|Yes|Bajty zápisu na disk s operačním systémem/s|BytesPerSecond|Průměr|Počet bajtů za sekundu zapsaných na jeden disk během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Operace zápisu na disk s operačním systémem za sekundu|Yes|Operace zápisu na disk s operačním systémem za sekundu|CountPerSecond|Průměr|Zápis IOPS z jednoho disku během období monitorování pro disk s operačním systémem|Žádné dimenze|
|Odchozí toky|Yes|Odchozí toky|Počet|Průměr|Odchozí toky představují počet současných toků v odchozím směru (provoz, který se odchází z virtuálního počítače).|Žádné dimenze|
|Maximální rychlost vytváření odchozích toků|Yes|Maximální rychlost vytváření odchozích toků|CountPerSecond|Průměr|Maximální rychlost vytváření odchozích toků (provoz, který se odchází z virtuálního počítače)|Žádné dimenze|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači|Žádné dimenze|
|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Yes|Počet přístupů do mezipaměti pro datový disk úrovně Premium|Procento|Průměr|Počet přístupů do mezipaměti pro datový disk úrovně Premium|(|
|Neúspěšné čtení mezipaměti datových disků Premium|Yes|Neúspěšné čtení mezipaměti datových disků Premium|Procento|Průměr|Neúspěšné čtení mezipaměti datových disků Premium|(|
|Počet přístupů do mezipaměti disku s operačním systémem Premium|Yes|Počet přístupů do mezipaměti disku s operačním systémem Premium|Procento|Průměr|Počet přístupů do mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Yes|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Procento|Průměr|Neúspěšné čtení mezipaměti disku s operačním systémem Premium|Žádné dimenze|
|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma disku v mezipaměti spotřebované virtuálním počítačem|Žádné dimenze|
|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Yes|Procento využitého procenta IOPS v mezipaměti virtuálních počítačů|Procento|Průměr|Procento IOPS disku uložených v mezipaměti, spotřebované virtuálním počítačem|Žádné dimenze|
|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Yes|Procento spotřebované šířky pásma neuložené v mezipaměti virtuálního počítače|Procento|Průměr|Procento šířky pásma neuloženého disku v mezipaměti spotřebované virtuálním počítačem|Žádné dimenze|
|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Yes|Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače|Procento|Průměr|Procento neuloženého počtu vstupně-výstupních operací disku využívané virtuálním počítačem|Žádné dimenze|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Využití procesoru|Počet|Průměr|Využití CPU na všech jádrech v millicores.|containerName|
|MemoryUsage|Yes|Využití paměti|Bajty|Průměr|Celkové využití paměti v bajtech|containerName|
|NetworkBytesReceivedPerSecond|Yes|Počet přijatých bajtů sítě za sekundu|Bajty|Průměr|Počet přijatých bajtů sítě za sekundu.|Žádné dimenze|
|NetworkBytesTransmittedPerSecond|Yes|Bajty přenášené přes síť za sekundu|Bajty|Průměr|Počet bajtů přenášených sítí za sekundu.|Žádné dimenze|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|Čas procesoru neznámá|Sekundy|Celkem|Neznámá procesor v řádu sekund|Žádné dimenze|
|RunDuration|Yes|Doba trvání běhu|Milisekund|Celkem|Doba běhu v milisekundách|Žádné dimenze|
|SuccessfulPullCount|Yes|Počet úspěšných vyžádané replikace|Počet|Průměr|Počet úspěšných stažení imagí|Žádné dimenze|
|SuccessfulPushCount|Yes|Počet úspěšných vložení|Počet|Průměr|Počet úspěšných vložení imagí|Žádné dimenze|
|TotalPullCount|Yes|Celkový počet vyžádané replikace|Počet|Průměr|Celkový počet načtených dat imagí|Žádné dimenze|
|TotalPushCount|Yes|Celkový počet nabízených oznámení|Počet|Průměr|Celkový počet nabízených oznámení imagí|Žádné dimenze|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Žádosti o inletu|Počet|Průměr|Maximální počet aktuálně použitých požadavků na apiserver za typ žádosti za poslední sekundu|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|No|Stav clusteru|Počet|Průměr|Určuje, jestli se má cluster pro automatické škálování na clusteru provést akce.||
|cluster_autoscaler_scale_down_in_cooldown|No|Horizontální navýšení kapacity cooldown|Počet|Průměr|Určuje, zda je horizontální snížení kapacity v cooldown – během tohoto časového období nebudou žádné uzly odebrány.||
|cluster_autoscaler_unneeded_nodes_count|No|Nepotřebné uzly|Počet|Průměr|Cluster auotscaler označí tyto uzly jako kandidáty na odstranění a nakonec se odstraní.||
|cluster_autoscaler_unschedulable_pods_count|No|Unschedulable lusky|Počet|Průměr|Počet lusků, které se v clusteru aktuálně unschedulable||
|kube_node_status_allocatable_cpu_cores|No|Celkový počet dostupných jader procesoru ve spravovaném clusteru|Počet|Průměr|Celkový počet dostupných jader procesoru ve spravovaném clusteru||
|kube_node_status_allocatable_memory_bytes|No|Celková velikost dostupné paměti ve spravovaném clusteru|Bajty|Průměr|Celková velikost dostupné paměti ve spravovaném clusteru||
|kube_node_status_condition|No|Stavy pro různé podmínky uzlu|Počet|Průměr|Stavy pro různé podmínky uzlu|podmínka, stav, status2, uzel|
|kube_pod_status_phase|No|Počet lusků podle fáze|Počet|Průměr|Počet lusků podle fáze|fáze, obor názvů pod|
|kube_pod_status_ready|No|Počet lusků ve stavu připraveno|Počet|Průměr|Počet lusků ve stavu připraveno|obor názvů, pod, podmínka|
|node_cpu_usage_millicores|Yes|Využití CPU Millicores|MilliCores|Průměr|Agregované měření využití CPU v millicores napříč clusterem|uzel, nodepool|
|node_cpu_usage_percentage|Yes|Procento využití procesoru|Procento|Průměr|Agregované průměrné využití procesoru měřené v procentech v rámci clusteru|uzel, nodepool|
|node_disk_usage_bytes|Yes|Využité bajty disku|Bajty|Průměr|Místo na disku využité v bajtech podle zařízení|uzel, nodepool, zařízení|
|node_disk_usage_percentage|Yes|Procento využitého disku|Procento|Průměr|Místo na disku využité v procentech podle zařízení|uzel, nodepool, zařízení|
|node_memory_rss_bytes|Yes|Paměť RSS – bajty|Bajty|Průměr|Využitá paměť RSS kontejneru v bajtech|uzel, nodepool|
|node_memory_rss_percentage|Yes|Procento paměti RSS|Procento|Průměr|Paměť RSS kontejneru využitá v procentech|uzel, nodepool|
|node_memory_working_set_bytes|Yes|Bajty pracovní sady paměti|Bajty|Průměr|Využitá paměť pracovní sady kontejneru v bajtech|uzel, nodepool|
|node_memory_working_set_percentage|Yes|Procento pracovní sady paměti|Procento|Průměr|Paměť pracovní sady kontejneru využitá v procentech|uzel, nodepool|
|node_network_in_bytes|Yes|Síť v bajtech|Bajty|Průměr|Počet přijatých bajtů sítě|uzel, nodepool|
|node_network_out_bytes|Yes|Výstupní bajty sítě|Bajty|Průměr|Přenesené bajty sítě|uzel, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Neúspěšné požadavky|Počet|Celkem|Získá dostupné protokoly pro vlastní poskytovatele prostředků.|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky vytvořené vlastním poskytovatelem|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Yes|Dostupná kapacita|Bajty|Průměr|Dostupná kapacita v bajtech během období generování sestav.|Žádné dimenze|
|BytesUploadedToCloud|Yes|Odeslané bajty v cloudu (zařízení)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze zařízení během období generování sestav.|Žádné dimenze|
|BytesUploadedToCloudPerShare|Yes|Odeslané bajty v cloudu (sdílená složka)|Bajty|Průměr|Celkový počet bajtů odeslaných do Azure ze sdílené složky během období generování sestav.|Sdílení|
|CloudReadThroughput|Yes|Propustnost stahování v cloudu|BytesPerSecond|Průměr|Propustnost stahování cloudu do Azure během období generování sestav.|Žádné dimenze|
|CloudReadThroughputPerShare|Yes|Propustnost stahování do cloudu (sdílení)|BytesPerSecond|Průměr|Propustnost stahování do Azure ze sdílené složky během období generování sestav.|Sdílení|
|CloudUploadThroughput|Yes|Propustnost nahrávání do cloudu|BytesPerSecond|Průměr|Propustnost nahrávání do cloudu do Azure během období generování sestav.|Žádné dimenze|
|CloudUploadThroughputPerShare|Yes|Propustnost nahrávání do cloudu (sdílení)|BytesPerSecond|Průměr|Odeslání propustnosti do Azure ze sdílené složky během období generování sestav.|Sdílení|
|HyperVMemoryUtilization|Yes|Výpočet využití paměti na hraničních zařízeních|Procento|Průměr|Velikost využité paměti RAM|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Výpočetní prostředí Edge – procento využití procesoru|Procento|Průměr|Procento využití procesoru|InstanceName|
|NICReadThroughput|Yes|Propustnost čtení (síť)|BytesPerSecond|Průměr|Propustnost čtení síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|NICWriteThroughput|Yes|Propustnost zápisu (síť)|BytesPerSecond|Průměr|Propustnost zápisu síťového rozhraní v zařízení v období vytváření sestav pro všechny svazky v bráně.|InstanceName|
|Úložiště|Yes|Celková kapacita|Bajty|Průměr|Celková kapacita|Žádné dimenze|


## <a name="microsoftdatacollaborationworkspaces"></a>Spolupráce a pracovní prostory Microsoft.

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Vytvořené datové prostředky|Počet|Maximum|Počet vytvořených datových assetů|Datový zdroj|
|PipelineCount|Yes|Vytvořené kanály|Počet|Maximum|Počet vytvořených kanálů|Profilace|
|ProposalCount|Yes|Vytváření návrhů|Počet|Maximum|Počet vytvořených návrhů|Návrh|
|ScriptCount|Yes|Vytvořené skripty|Počet|Maximum|Počet vytvořených skriptů|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/DataFactory

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Neúspěšná spuštění|Počet|Celkem||profilace, Activity|
|SuccessfulRuns|Yes|Úspěšná spuštění|Počet|Celkem||profilace, Activity|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Zrušené metriky spuštění aktivit|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivityFailedRuns|Yes|Neúspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|ActivitySucceededRuns|Yes|Úspěšná aktivita spustí metriky|Počet|Celkem||ActivityType, název kanálu, FailureType, název|
|FactorySizeInGbUnits|Yes|Celková velikost továrny (jednotka GB)|Počet|Maximum||Žádné dimenze|
|IntegrationRuntimeAvailableMemory|Yes|Dostupná paměť modulu runtime integrace|Bajty|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeAvailableNodeNumber|Yes|Počet dostupných uzlů prostředí Integration runtime|Počet|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Doba trvání fronty prostředí Integration runtime|Sekundy|Průměr||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|Využití procesoru prostředí Integration runtime|Procento|Průměr||IntegrationRuntimeName, Node|
|IntegrationRuntimeQueueLength|Yes|Délka fronty prostředí Integration runtime|Počet|Průměr||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Maximální povolená velikost továrny (jednotka GB)|Počet|Maximum||Žádné dimenze|
|MaxAllowedResourceCount|Yes|Maximální počet povolených entit|Počet|Maximum||Žádné dimenze|
|PipelineCancelledRuns|Yes|Zrušené metriky spuštění kanálu|Počet|Celkem||FailureType, název|
|PipelineElapsedTimeRuns|Yes|Metriky spuštění kanálu podle uplynulého času|Počet|Celkem||RunId, název|
|PipelineFailedRuns|Yes|Neúspěšná metrika spuštění kanálu|Počet|Celkem||FailureType, název|
|PipelineSucceededRuns|Yes|Úspěšné metriky spuštění kanálu|Počet|Celkem||FailureType, název|
|ResourceCount|Yes|Celkový počet entit|Počet|Maximum||Žádné dimenze|
|SSISIntegrationRuntimeStartCancel|Yes|Zrušené metriky spuštění SSIS Integration runtime|Počet|Celkem||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Nepovedlo se spustit metriky spuštění SSIS Integration runtime|Počet|Celkem||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Úspěšná metriky spuštění SSIS Integration runtime|Počet|Celkem||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Yes|Zastavení metriky SSIS Integration runtime|Počet|Celkem||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Úspěšné SSIS Integration runtime – metriky zastavení|Počet|Celkem||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|Zrušené metriky spuštění balíčku SSIS|Počet|Celkem||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Neúspěšné metriky spuštění balíčku SSIS|Počet|Celkem||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|Úspěšné metriky spuštění balíčku SSIS|Počet|Celkem||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Zrušené aktivační události spustí metriky|Počet|Celkem||Název, FailureType|
|TriggerFailedRuns|Yes|Neúspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|
|TriggerSucceededRuns|Yes|Úspěšná aktivační událost spustí metriky|Počet|Celkem||Název, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Čas AU se zrušil.|Sekundy|Celkem|Celková doba AU pro zrušené úlohy.|Žádné dimenze|
|JobAUEndedFailure|Yes|Neúspěšná doba aktualizace AU|Sekundy|Celkem|Celková doba AU pro neúspěšné úlohy.|Žádné dimenze|
|JobAUEndedSuccess|Yes|Úspěšná doba AU|Sekundy|Celkem|Celková doba AU pro úspěšné úlohy.|Žádné dimenze|
|JobEndedCancelled|Yes|Zrušené úlohy|Počet|Celkem|Počet zrušených úloh|Žádné dimenze|
|JobEndedFailure|Yes|Neúspěšné úlohy|Počet|Celkem|Počet neúspěšných úloh|Žádné dimenze|
|JobEndedSuccess|Yes|Úspěšné úlohy|Počet|Celkem|Počet úspěšných úloh|Žádné dimenze|
|JobStage|Yes|Úlohy ve fázi|Počet|Celkem|Počet úloh v každé fázi.|Žádné dimenze|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Čtení z|Yes|Přečtená data|Bajty|Celkem|Celkový objem dat načtených z účtu.|Žádné dimenze|
|Napsáno|Yes|Zapsaná data|Bajty|Celkem|Celkové množství dat zapsaných na účet.|Žádné dimenze|
|ReadRequests|Yes|Žádosti o čtení|Počet|Celkem|Počet požadavků na čtení dat pro účet.|Žádné dimenze|
|TotalStorage|Yes|Celkové úložiště|Bajty|Maximum|Celková velikost dat uložených v účtu.|Žádné dimenze|
|WriteRequests|Yes|Požadavky na zápis|Počet|Celkem|Počet požadavků na zápis dat na účet.|Žádné dimenze|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Přijaté sdílené složky se nepodařilo vytvořit snímky.|Počet|Počet|Počet přijatých snímků neúspěšných sdílení v účtu|Žádné dimenze|
|FailedShareSynchronizations|Yes|Odeslané neúspěšné snímky sdílené složky|Počet|Počet|Počet odeslaných snímků neúspěšných sdílení v účtu|Žádné dimenze|
|ShareCount|Yes|Odeslané sdílené složky|Počet|Maximum|Počet odeslaných sdílených složek v účtu|ShareName|
|ShareSubscriptionCount|Yes|Přijaté sdílené složky|Počet|Maximum|Počet přijatých sdílených složek v účtu|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Úspěšně přijaté sdílené snímky|Počet|Počet|Počet přijatých snímků s úspěšným sdílením v účtu|Žádné dimenze|
|SucceededShareSynchronizations|Yes|Úspěšně se odeslaly snímky s úspěšným sdílením|Počet|Počet|Počet odeslaných snímků s úspěšným sdílením v účtu|Žádné dimenze|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|backup_storage_used|Yes|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádné dimenze|
|connections_failed|Yes|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|io_consumption_percent|Yes|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|seconds_behind_master|Yes|Prodleva replikace v sekundách|Počet|Maximum|Prodleva replikace v sekundách|Žádné dimenze|
|serverlog_storage_limit|Yes|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_percent|Yes|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Yes|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|storage_limit|Yes|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádné dimenze|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Přerušená připojení|Počet|Celkem|Přerušená připojení|Žádné dimenze|
|active_connections|Yes|Aktivní připojení|Počet|Maximum|Aktivní připojení|Žádné dimenze|
|backup_storage_used|Yes|Využité úložiště záloh|Bajty|Maximum|Využité úložiště záloh|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru hostitele|Procento|Maximum|Procento využití procesoru hostitele|Žádné dimenze|
|io_consumption_percent|Yes|V/v procenta|Procento|Maximum|V/v procenta|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti hostitele|Procento|Maximum|Procentuální hodnota paměti hostitele|Žádné dimenze|
|network_bytes_egress|Yes|Odchozí síť hostitele|Bajty|Celkem|Odchozí přenos v síti hostitele v bajtech|Žádné dimenze|
|network_bytes_ingress|Yes|Síť hostitele v|Bajty|Celkem|Příchozí příchozí přenosy v síti hostitele v bajtech|Žádné dimenze|
|Dotazy|Yes|Dotazy|Počet|Celkem|Dotazy|Žádné dimenze|
|replication_lag|Yes|Prodleva replikace v sekundách|Sekundy|Maximum|Prodleva replikace v sekundách|Žádné dimenze|
|storage_limit|Yes|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Maximum|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Maximum|Využité úložiště|Žádné dimenze|
|total_connections|Yes|Celkový počet připojení|Počet|Celkem|Celkový počet připojení|Žádné dimenze|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|backup_storage_used|Yes|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádné dimenze|
|connections_failed|Yes|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|io_consumption_percent|Yes|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|seconds_behind_master|Yes|Prodleva replikace v sekundách|Počet|Maximum|Prodleva replikace v sekundách|Žádné dimenze|
|serverlog_storage_limit|Yes|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_percent|Yes|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Yes|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|storage_limit|Yes|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádné dimenze|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|backup_storage_used|Yes|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádné dimenze|
|connections_failed|Yes|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|connections_succeeded|Yes|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádné dimenze|
|cpu_credits_consumed|Yes|Spotřebované kredity procesoru|Počet|Průměr|Celkový počet kreditů spotřebovaných databázovým serverem|Žádné dimenze|
|cpu_credits_remaining|Yes|Zbývající kredity procesoru|Počet|Průměr|Celkový počet kreditů dostupných pro shlukování|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|disk_queue_depth|Yes|Hloubka fronty disku|Počet|Průměr|Počet nezpracovaných vstupně-výstupních operací na datový disk|Žádné dimenze|
|IOPS|Yes|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|Žádné dimenze|
|maximum_used_transactionIDs|Yes|Maximální počet použitých ID transakcí|Počet|Průměr|Maximální počet použitých ID transakcí|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|read_iops|Yes|Čtení IOPS|Počet|Průměr|Počet vstupně-výstupních operací čtení datových disků za sekundu|Žádné dimenze|
|read_throughput|Yes|Bajty propustnosti čtení/s|Počet|Průměr|Počet přečtených bajtů za sekundu z datového disku během období monitorování|Žádné dimenze|
|storage_free|Yes|Volné úložiště|Bajty|Průměr|Volné úložiště|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádné dimenze|
|txlogs_storage_used|Yes|Využité úložiště protokolu transakcí|Bajty|Průměr|Využité úložiště protokolu transakcí|Žádné dimenze|
|write_iops|Yes|Zápis IOPS|Počet|Průměr|Počet vstupně-výstupních operací zápisu datových disků za sekundu|Žádné dimenze|
|write_throughput|Yes|Bajty propustnosti zápisu/s|Počet|Průměr|Počet bajtů zapsaných za sekundu na datový disk během období monitorování|Žádné dimenze|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|ServerName|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|ServerName|
|IOPS|Yes|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|ServerName|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|ServerName|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|ServerName|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|ServerName|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|ServerName|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|backup_storage_used|Yes|Využité úložiště záloh|Bajty|Průměr|Využité úložiště záloh|Žádné dimenze|
|connections_failed|Yes|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|io_consumption_percent|Yes|V/v procenta|Procento|Průměr|V/v procenta|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|pg_replica_log_delay_in_bytes|Yes|Maximální prodleva napříč replikami|Bajty|Maximum|Prodleva z největšího zpoždění repliky v bajtech|Žádné dimenze|
|pg_replica_log_delay_in_seconds|Yes|Prodleva repliky|Sekundy|Maximum|Prodleva repliky v sekundách|Žádné dimenze|
|serverlog_storage_limit|Yes|Limit úložiště protokolu serveru|Bajty|Maximum|Limit úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_percent|Yes|Procentuální hodnota úložiště protokolu serveru|Procento|Průměr|Procentuální hodnota úložiště protokolu serveru|Žádné dimenze|
|serverlog_storage_usage|Yes|Využité úložiště protokolu serveru|Bajty|Průměr|Využité úložiště protokolu serveru|Žádné dimenze|
|storage_limit|Yes|Omezení úložiště|Bajty|Maximum|Omezení úložiště|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádné dimenze|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktivní připojení|Počet|Průměr|Aktivní připojení|Žádné dimenze|
|cpu_percent|Yes|Procento využití procesoru|Procento|Průměr|Procento využití procesoru|Žádné dimenze|
|IOPS|Yes|IOPS|Počet|Průměr|Vstupně-výstupní operace za sekundu|Žádné dimenze|
|memory_percent|Yes|Procentuální hodnota paměti|Procento|Průměr|Procentuální hodnota paměti|Žádné dimenze|
|network_bytes_egress|Yes|Síťové výstupy|Bajty|Celkem|Vyprší síť napříč aktivními připojeními|Žádné dimenze|
|network_bytes_ingress|Yes|Síťové vstupy|Bajty|Celkem|Síť v rámci aktivních připojení|Žádné dimenze|
|storage_percent|Yes|Procento úložiště|Procento|Průměr|Procento úložiště|Žádné dimenze|
|storage_used|Yes|Využité úložiště|Bajty|Průměr|Využité úložiště|Žádné dimenze|


## <a name="microsoftdeviceselasticpools"></a>Microsoft. Devices/ElasticPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|požadovaná míra využití|Procento|Průměr|požadovaná míra využití|Žádné dimenze|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|C2D. Commands. odchozí. Abandon. Success|Yes|Zrušené zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádné dimenze|
|C2D. Commands.. Complete. Complete. Success|Yes|Doručení zpráv C2D bylo dokončeno.|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádné dimenze|
|C2D. Commands. odchozí. remítat. Success|Yes|Odmítnuté zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádné dimenze|
|C2D. Methods. Failure|Yes|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádné dimenze|
|C2D. Methods. requestSize|Yes|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné dimenze|
|C2D. Methods. responseSize|Yes|Velikost odezvy volání přímých metod|Bajty|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné dimenze|
|C2D. Methods. Success|Yes|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|C2D. nevlákenný. Read. Failure|Yes|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné dimenze|
|C2D. nevlákenný. Read. Size|Yes|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné dimenze|
|C2D. vláken. Read. Success|Yes|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné dimenze|
|C2D. nevlákenná. aktualizace. selhání|Yes|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné dimenze|
|C2D. nevlákenná. Update. Size|Yes|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné dimenze|
|C2D. nevlákenná. Update. Success|Yes|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné dimenze|
|C2DMessagesExpired|Yes|C2D zpráv vypršela platnost.|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádné dimenze|
|konfiguračních|Yes|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádné dimenze|
|connectedDeviceCount|Yes|Připojená zařízení|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|D2C. Endpoints. invýstups. builtIns. events|Yes|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události).|Žádné dimenze|
|D2C. Endpoints. odchozí. eventHubs|Yes|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusQueues|Yes|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusTopics|Yes|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné dimenze|
|D2C. Endpoints. odchozí úložiště. Storage|Yes|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. výstup. Storage. BLOBs|Yes|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. invýstups. Storage. bytes|Yes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné dimenze|
|D2C. Endpoints. latence. builtIn. events|Yes|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události)|Žádné dimenze|
|D2C. Endpoints. latence. eventHubs|Yes|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusQueues|Yes|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusTopics|Yes|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné dimenze|
|D2C. Endpoints. latence. Storage|Yes|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné dimenze|
|D2C. telemetrie. výstup. vyřazeno|Yes|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné dimenze|
|D2C. telemetrie. odchozí. Fallback|Yes|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné dimenze|
|D2C. telemetrie. invýstup. neplatné|Yes|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné dimenze|
|D2C. telemetrie. výstup. osamocený|Yes|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné dimenze|
|D2C. telemetrie. odchozí. úspěch|Yes|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat allProtocol|Yes|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat sendThrottle|Yes|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat. úspěch|Yes|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné dimenze|
|D2C. nevlákenný. Read. Failure|Yes|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné dimenze|
|D2C. nevlákenný. Read. Size|Yes|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. vláken. Read. Success|Yes|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. aktualizace. selhání|Yes|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné dimenze|
|D2C. nevlákenná. Update. Size|Yes|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. Update. Success|Yes|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|dailyMessageQuotaUsed|Yes|Celkový počet použitých zpráv|Počet|Maximum|Počet celkem aktuálně využívaných zpráv|Žádné dimenze|
|deviceDataUsage|Yes|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|deviceDataUsageV2|Yes|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|Devices. connectedDevices. allProtocol|Yes|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|Devices. totalDevices|Yes|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|EventGridDeliveries|Yes|Event Grid dodávky|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události ( https://aka.ms/ioteventgrid) .|Výsledek, EventType|
|EventGridLatency|Yes|Event Grid latence|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ události|
|Jobs. cancelJob. selhání|Yes|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné dimenze|
|Jobs. cancelJob. Success|Yes|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné dimenze|
|dokončené úlohy|Yes|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|Jobs. createDirectMethodJob. selhání|Yes|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné dimenze|
|Jobs. createDirectMethodJob. Success|Yes|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné dimenze|
|Jobs. createTwinUpdateJob. selhání|Yes|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|Jobs. createTwinUpdateJob. Success|Yes|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|úlohy. nezdařilo se|Yes|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|Jobs. listJobs. selhání|Yes|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné dimenze|
|Jobs. listJobs. Success|Yes|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné dimenze|
|Jobs. queryJobs. selhání|Yes|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné dimenze|
|Jobs. queryJobs. Success|Yes|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné dimenze|
|RoutingDataSizeInBytesDelivered|Yes|Velikost zprávy o doručení směrování v bajtech (Preview)|Bajty|Celkem|Celková velikost zpráv v bajtech doručených službou IoT Hub do koncového bodu. K zobrazení velikosti zpráv v bajtech dodaných do různých koncových bodů můžete použít dimenze koncový bod a EndpointType. Hodnota metriky se zvětšuje u všech doručených zpráv, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručuje do stejného koncového bodu víckrát.|EndpointType, koncového bodu, RoutingSource|
|RoutingDeliveries|Yes|Směrování doručení (Preview)|Počet|Celkem|Počet pokusů IoT Hub o doručení zpráv do všech koncových bodů pomocí směrování. Chcete-li zobrazit počet úspěšných nebo neúspěšných pokusů, použijte výslednou dimenzi. Chcete-li zobrazit důvod selhání, jako je například neplatný, zrušený nebo osamocený, použijte dimenzi FailureReasonCategory. Můžete také použít dimenze koncový bod a EndpointType, abyste pochopili, kolik zpráv bylo doručeno do různých koncových bodů. Hodnota metriky se u každého pokusu o doručení zvyšuje o jednu, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručí do stejného koncového bodu víckrát.|EndpointType, koncového bodu, FailureReasonCategory, výsledek, RoutingSource|
|RoutingDeliveryLatency|Yes|Latence doručení směrování (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozím a příchozím zprávou pro IoT Hub a zprávy telemetrie do koncového bodu. Pomocí dimenzí koncového bodu a EndpointType můžete pochopit latenci různých koncových bodů.|EndpointType, koncového bodu, RoutingSource|
|tenantHub.requestedUsageRate|Yes|požadovaná míra využití|Procento|Průměr|požadovaná míra využití|Žádné dimenze|
|totalDeviceCount|Yes|Celkem zařízení|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|twinQueries. selhání|Yes|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné dimenze|
|twinQueries.resultSize|Yes|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné dimenze|
|twinQueries. Success|Yes|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné dimenze|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|C2D. Commands. odchozí. Abandon. Success|Yes|Zrušené zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení opuštěných zařízením|Žádné dimenze|
|C2D. Commands.. Complete. Complete. Success|Yes|Doručení zpráv C2D bylo dokončeno.|Počet|Celkem|Počet úspěšně dokončených doručení zpráv typu cloud-zařízení do zařízení|Žádné dimenze|
|C2D. Commands. odchozí. remítat. Success|Yes|Odmítnuté zprávy C2D|Počet|Celkem|Počet zpráv typu cloud-zařízení odmítnutých zařízením|Žádné dimenze|
|C2D. Methods. Failure|Yes|Neúspěšná volání přímé metody|Počet|Celkem|Počet všech neúspěšných volání metody Direct|Žádné dimenze|
|C2D. Methods. requestSize|Yes|Velikost žádosti o vyvolání přímé metody|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných požadavků na metodu Direct.|Žádné dimenze|
|C2D. Methods. responseSize|Yes|Velikost odezvy volání přímých metod|Bajty|Průměr|Průměrných, minimálních a maximálních odpovědí všech úspěšných přímých metod.|Žádné dimenze|
|C2D. Methods. Success|Yes|Úspěšná volání přímé metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádné dimenze|
|C2D. nevlákenný. Read. Failure|Yes|Neúspěšné čtení z back-endu ze zadních vláken|Počet|Celkem|Počet všech neúspěšných čtení, která byla inicializována back-endu.|Žádné dimenze|
|C2D. nevlákenný. Read. Size|Yes|Velikost odpovědi zdvojeného čtení z back-endu|Bajty|Průměr|Průměrně, minimální a maximální počet všech úspěšných zdvojených čtení, které byly spuštěny na konci.|Žádné dimenze|
|C2D. vláken. Read. Success|Yes|Úspěšné zdvojené čtení z back-endu|Počet|Celkem|Počet všech úspěšných zdvojených čtení spouštěných back-endu.|Žádné dimenze|
|C2D. nevlákenná. aktualizace. selhání|Yes|Neúspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech neúspěšných provolaných aktualizací, které selhaly po back-endu.|Žádné dimenze|
|C2D. nevlákenná. Update. Size|Yes|Velikost dvojitě aktualizovaných aktualizací z back-endu|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných kompletních aktualizací spuštěných na konci.|Žádné dimenze|
|C2D. nevlákenná. Update. Success|Yes|Úspěšné zdvojené aktualizace z back-endu|Počet|Celkem|Počet všech úspěšných dokončených aktualizací, které jsou úspěšně spouštěny po konci.|Žádné dimenze|
|C2DMessagesExpired|Yes|C2D zpráv vypršela platnost.|Počet|Celkem|Počet zpráv typu cloud-zařízení, jejichž platnost vypršela|Žádné dimenze|
|konfiguračních|Yes|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádné dimenze|
|connectedDeviceCount|No|Připojená zařízení|Počet|Průměr|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|D2C. Endpoints. invýstups. builtIns. events|Yes|Směrování: zprávy doručené zprávám/událostem|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do integrovaného koncového bodu (zprávy/události).|Žádné dimenze|
|D2C. Endpoints. odchozí. eventHubs|Yes|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů centra událostí.|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusQueues|Yes|Směrování: zprávy doručené do fronty Service Bus|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. odchozí. serviceBusTopics|Yes|Směrování: zprávy doručené do Service Bus tématu|Počet|Celkem|Počet, kolikrát IoT Hub směrování úspěšně doručuje zprávy do koncových bodů tématu Service Bus.|Žádné dimenze|
|D2C. Endpoints. odchozí úložiště. Storage|Yes|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet pokusů, IoT Hub směrování úspěšně doručuje zprávy do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. výstup. Storage. BLOBs|Yes|Směrování: objekty blob doručené do úložiště|Počet|Celkem|Počet pokusů IoT Hub směrování dodaných objektů blob do koncových bodů úložiště.|Žádné dimenze|
|D2C. Endpoints. invýstups. Storage. bytes|Yes|Směrování: data Doručená do úložiště|Bajty|Celkem|Množství dat (v bajtech) IoT Hub směrování doručené koncovým bodům úložiště.|Žádné dimenze|
|D2C. Endpoints. latence. builtIn. events|Yes|Směrování: latence zpráv pro zprávy/události|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zprávou telemetrie do integrovaného koncového bodu (zprávy/události)|Žádné dimenze|
|D2C. Endpoints. latence. eventHubs|Yes|Směrování: latence zprávy pro centrum událostí|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami IoT Hub a příchozími zprávami do koncového bodu centra událostí.|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusQueues|Yes|Směrování: latence zprávy pro Service Bus frontu|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu fronty Service Bus|Žádné dimenze|
|D2C. Endpoints. latence. serviceBusTopics|Yes|Směrování: latence zprávy pro Service Bus téma|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi vstupem zprávy a IoT Hub a zpráv telemetrie do koncového bodu Service Bus tématu.|Žádné dimenze|
|D2C. Endpoints. latence. Storage|Yes|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozími a příchozími zprávami pro IoT Hub a zprávy telemetrie do koncového bodu úložiště|Žádné dimenze|
|D2C. telemetrie. výstup. vyřazeno|Yes|Směrování: vyřazené zprávy telemetrie |Počet|Celkem|Počet pokusů o vyřazení zpráv IoT Hub směrování z důvodu nedoručených koncových bodů. Tato hodnota nepočítá počet zpráv doručených do záložní trasy, protože se tam nedoručují vyřazené zprávy.|Žádné dimenze|
|D2C. telemetrie. odchozí. Fallback|Yes|Směrování: zprávy doručené do záložního režimu|Počet|Celkem|Počet, kolikrát IoT Hub směrování doručených zpráv do koncového bodu přidruženého k záložní trase.|Žádné dimenze|
|D2C. telemetrie. invýstup. neplatné|Yes|Směrování: nekompatibilní zprávy telemetrie|Počet|Celkem|Počet, kolikrát IoT Hub směrování nedokázala doručovat zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakované pokusy.|Žádné dimenze|
|D2C. telemetrie. výstup. osamocený|Yes|Směrování: osamocené zprávy telemetrie |Počet|Celkem|Počet, kolikrát byly zprávy osamocené IoT Hub směrování, protože neodpovídaly pravidlům směrování (včetně záložního pravidla). |Žádné dimenze|
|D2C. telemetrie. odchozí. úspěch|Yes|Směrování: doručené zprávy telemetrie|Počet|Celkem|Počet pokusů o úspěšné doručení zpráv do všech koncových bodů pomocí směrování IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se u každého úspěšného doručení zvyšuje o jednu. Pokud se zpráva doručí do stejného koncového bodu víckrát, tato hodnota se u každého úspěšného doručení zvyšuje o jednu.|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat allProtocol|Yes|Počet pokusů o odeslání zprávy telemetrie|Počet|Celkem|Počet zpráv typu zařízení-Cloud telemetrie, které se pokusily o odeslání do služby IoT Hub|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat sendThrottle|Yes|Počet chyb omezování|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnosti zařízení|Žádné dimenze|
|D2C. telemetrie. příchozí přenos dat. úspěch|Yes|Odeslané zprávy telemetrie|Počet|Celkem|Počet zpráv telemetrie typu zařízení-Cloud, které se úspěšně odeslaly do služby IoT Hub|Žádné dimenze|
|D2C. nevlákenný. Read. Failure|Yes|Neúspěšná čtení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení, která byla iniciována zařízením.|Žádné dimenze|
|D2C. nevlákenný. Read. Size|Yes|Velikost odpovědi u dvojitých čtení ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální hodnota všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. vláken. Read. Success|Yes|Úspěšné čtení ze zařízení|Počet|Celkem|Počet všech úspěšných zdvojených čtení iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. aktualizace. selhání|Yes|Neúspěšné aktualizace ze zařízení se zdvojenými chybami|Počet|Celkem|Počet všech neúspěšných aktualizací inicializovaných zařízením.|Žádné dimenze|
|D2C. nevlákenná. Update. Size|Yes|Velikost dvojitě aktualizovaných aktualizací ze zařízení|Bajty|Průměr|Průměrná, minimální a maximální velikost všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|D2C. nevlákenná. Update. Success|Yes|Úspěšné nedokončené změny ze zařízení|Počet|Celkem|Počet všech úspěšných nevyžádaných aktualizací iniciované zařízením|Žádné dimenze|
|dailyMessageQuotaUsed|Yes|Celkový počet použitých zpráv|Počet|Maximum|Počet celkem aktuálně využívaných zpráv|Žádné dimenze|
|deviceDataUsage|Yes|Celkové využití dat zařízení|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|deviceDataUsageV2|Yes|Celkové využití dat zařízení (Preview)|Bajty|Celkem|Bajtů přenesených do a ze všech zařízení připojených k IotHub|Žádné dimenze|
|Devices. connectedDevices. allProtocol|Yes|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených ke službě IoT Hub|Žádné dimenze|
|Devices. totalDevices|Yes|Celkem zařízení (zastaralé)|Počet|Celkem|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|EventGridDeliveries|Yes|Event Grid dodávky|Počet|Celkem|Počet událostí IoT Hub publikovaných do Event Grid Použijte výslednou dimenzi pro počet úspěšných a neúspěšných žádostí. Dimenze EventType zobrazuje typ události ( https://aka.ms/ioteventgrid) .|Výsledek, EventType|
|EventGridLatency|Yes|Event Grid latence|Milisekund|Průměr|Průměrná latence (v milisekundách) od okamžiku, kdy byla událost centra IoT vygenerována při publikování události do Event Grid. Toto číslo je průměrem mezi všemi typy událostí. Použijte dimenzi EventType k zobrazení latence konkrétního typu události.|Typ události|
|Jobs. cancelJob. selhání|Yes|Neúspěšná zrušení úloh|Počet|Celkem|Počet všech neúspěšných volání pro zrušení úlohy.|Žádné dimenze|
|Jobs. cancelJob. Success|Yes|Úspěšná zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání pro zrušení úlohy.|Žádné dimenze|
|dokončené úlohy|Yes|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádné dimenze|
|Jobs. createDirectMethodJob. selhání|Yes|Nepovedlo se vytvořit úlohy vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh volání přímé metody.|Žádné dimenze|
|Jobs. createDirectMethodJob. Success|Yes|Úspěšné vytváření úloh vyvolání metod|Počet|Celkem|Počet všech úspěšných vytvoření úloh vyvolání přímé metody.|Žádné dimenze|
|Jobs. createTwinUpdateJob. selhání|Yes|Nepovedlo se vytvořit úlohy s dvojitou aktualizací|Počet|Celkem|Počet všech neúspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|Jobs. createTwinUpdateJob. Success|Yes|Úspěšné vytváření zdvojených úloh aktualizace|Počet|Celkem|Počet všech úspěšných vytvoření zdvojených úloh aktualizace.|Žádné dimenze|
|úlohy. nezdařilo se|Yes|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádné dimenze|
|Jobs. listJobs. selhání|Yes|Neúspěšná volání pro výpis úloh|Počet|Celkem|Počet všech neúspěšných volání pro výpis úloh|Žádné dimenze|
|Jobs. listJobs. Success|Yes|Úspěšná volání na seznam úloh|Počet|Celkem|Počet všech úspěšných volání pro výpis úloh.|Žádné dimenze|
|Jobs. queryJobs. selhání|Yes|Neúspěšné dotazy na úlohy|Počet|Celkem|Počet všech neúspěšných volání pro úlohy dotazu.|Žádné dimenze|
|Jobs. queryJobs. Success|Yes|Úspěšné dotazy na úlohy|Počet|Celkem|Počet všech úspěšných volání úloh dotazů.|Žádné dimenze|
|RoutingDataSizeInBytesDelivered|Yes|Velikost zprávy o doručení směrování v bajtech (Preview)|Bajty|Celkem|Celková velikost zpráv v bajtech doručených službou IoT Hub do koncového bodu. K zobrazení velikosti zpráv v bajtech dodaných do různých koncových bodů můžete použít dimenze koncový bod a EndpointType. Hodnota metriky se zvětšuje u všech doručených zpráv, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručuje do stejného koncového bodu víckrát.|EndpointType, koncového bodu, RoutingSource|
|RoutingDeliveries|Yes|Směrování doručení (Preview)|Počet|Celkem|Počet pokusů IoT Hub o doručení zpráv do všech koncových bodů pomocí směrování. Chcete-li zobrazit počet úspěšných nebo neúspěšných pokusů, použijte výslednou dimenzi. Chcete-li zobrazit důvod selhání, jako je například neplatný, zrušený nebo osamocený, použijte dimenzi FailureReasonCategory. Můžete také použít dimenze koncový bod a EndpointType, abyste pochopili, kolik zpráv bylo doručeno do různých koncových bodů. Hodnota metriky se u každého pokusu o doručení zvyšuje o jednu, včetně toho, jestli je zpráva Doručená do více koncových bodů, nebo jestli se zpráva doručí do stejného koncového bodu víckrát.|EndpointType, koncového bodu, FailureReasonCategory, výsledek, RoutingSource|
|RoutingDeliveryLatency|Yes|Latence doručení směrování (Preview)|Milisekund|Průměr|Průměrná latence (v milisekundách) mezi příchozím a příchozím zprávou pro IoT Hub a zprávy telemetrie do koncového bodu. Pomocí dimenzí koncového bodu a EndpointType můžete pochopit latenci různých koncových bodů.|EndpointType, koncového bodu, RoutingSource|
|totalDeviceCount|No|Celkem zařízení|Počet|Průměr|Počet zařízení zaregistrovaných ve službě IoT Hub|Žádné dimenze|
|twinQueries. selhání|Yes|Neúspěšné zdvojené dotazy|Počet|Celkem|Počet všech neúspěšných dotazů, které selhaly.|Žádné dimenze|
|twinQueries.resultSize|Yes|Velikost výsledku nevlákenných dotazů|Bajty|Průměr|Průměrnou, minimální a maximální velikost výsledku všech úspěšných vlákenných dotazů.|Žádné dimenze|
|twinQueries. Success|Yes|Úspěšné zdvojené dotazy|Počet|Celkem|Počet všech úspěšných vlákenných dotazů.|Žádné dimenze|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Pokusy o ověření identity|Počet|Celkem|Počet pokusů o ověření identity zařízení|ProvisioningServiceName, status, protokol|
|DeviceAssignments|Yes|Přiřazená zařízení|Počet|Celkem|Počet zařízení přiřazených ke centru IoT Hub|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Pokusy o registraci|Počet|Celkem|Počet pokusů o registraci zařízení|ProvisioningServiceName, IotHubName, status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|Žádosti rozhraní API|Počet|Celkem|Počet požadavků rozhraní API provedených pro digitální vlákna čtení, zápisu, odstranění a dotazování.|Operace, ověřování, protokol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|Frekvence neúspěšných požadavků rozhraní API|Procento|Průměr|Procento požadavků rozhraní API, které služba obdrží pro vaši instanci, která vrací kód odpovědi vnitřní chyby (500) pro digitální vlákna čtení, zápisu, odstranění a dotazování.|Operace, ověřování, protokol|
|ApiRequestsLatency|Yes|Latence požadavků rozhraní API|Milisekund|Průměr|Doba odezvy pro žádosti rozhraní API, tj. od okamžiku, kdy je požadavek přijat pomocí digitálních vláken Azure, dokud služba neodešle výsledek úspěch/selhání pro digitální vlákna čtení, zápisu, odstranění a dotazování.|Operace, ověřování, protokol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|Operace s rozhraním API pro fakturaci|Počet|Celkem|Metrika fakturace pro počet všech požadavků rozhraní API provedených vůči službě Azure Digital requests.|MeterId|
|BillingMessagesProcessed|Yes|Zpracované účtovací zprávy|Počet|Celkem|Metrika fakturace počtu zpráv odeslaných z digitálních vláken Azure do externích koncových bodů.|MeterId|
|BillingQueryUnits|Yes|Jednotky dotazů fakturace|Počet|Celkem|Počet jednotek dotazu, interně vypočtené míry využití prostředků služby, spotřebované pro provádění dotazů.|MeterId|
|IngressEvents|Yes|Události příchozího přenosu dat|Počet|Celkem|Počet příchozích událostí telemetrie do digitálních vláken Azure.|Výsledek|
|IngressEventsFailureRate|Yes|Frekvence neúspěšných událostí příchozího přenosu dat|Procento|Průměr|Procento příchozích událostí telemetrie, pro které služba vrací kód odpovědi vnitřní chyba (500).|Žádné dimenze|
|IngressEventsLatency|Yes|Latence událostí příchozího přenosu dat|Milisekund|Průměr|Čas od okamžiku, kdy událost dorazí, když je připravená na výstup pomocí digitálních vláken Azure, kdy služba odešle výsledek úspěch/selhání.|Výsledek|
|ModelCount|Yes|Model Count|Počet|Celkem|Celkový počet modelů v instanci digitálních vláken Azure Pomocí této metriky můžete určit, jestli se blížíte limitu služby pro maximální počet modelů povolených na jednu instanci.|Žádné dimenze|
|Směrování|Yes|Směrované zprávy|Počet|Celkem|Počet zpráv směrovaných do služby Azure Endpoint koncových bodů, jako je například centrum událostí, Service Bus nebo Event Grid.|EndpointType, výsledek|
|RoutingFailureRate|Yes|Frekvence neúspěšných směrování|Procento|Průměr|Procento událostí, kvůli kterým dojde k chybě při směrování z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid.|EndpointType|
|RoutingLatency|Yes|Latence směrování|Milisekund|Průměr|Uplynulý čas mezi událostí, která je směrována z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid.|EndpointType, výsledek|
|TwinCount|Yes|Počet vláken|Počet|Celkem|Celkový počet vláken v instanci digitálních vláken Azure Pomocí této metriky můžete určit, jestli se k limitu služby přiblížíte, aby se dosáhlo maximálního počtu vláken povolených na jednu instanci.|Žádné dimenze|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Přidání oblasti|Počet|Počet|Přidání oblasti|Oblast|
|AutoscaleMaxThroughput|No|Maximální propustnost automatického škálování|Počet|Maximum|Maximální propustnost automatického škálování|DatabaseName, CollectionName|
|AvailableStorage|No|zastaralé Dostupné úložiště|Bajty|Celkem|Možnost dostupné úložiště se odebere z Azure Monitor na konci září 2023. Velikost úložiště kolekce Cosmos DB je teď neomezená. Jediným omezením je, že velikost úložiště pro každý klíč logického oddílu je 20 GB. PartitionKeyStatistics v diagnostickém protokolu můžete povolit, abyste věděli, jakou spotřebu úložiště mají klíče oddílů pro nejvyšší oddíly. Další informace o Cosmos DB kvótě úložiště najdete v tomto dokumentu https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Po vyřazení budou zbývající pravidla upozornění stále definovaná pro vystaralou metriku automaticky zakázána publikováním data vyřazení.|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|No|Ukončení připojení Cassandra|Počet|Celkem|Počet uzavřených připojení Cassandra, která se hlásí s členitou úrovní 1 minuty|Oblast, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Průměrná ReplicationLatency Cassandra konektoru|Milisekund|Průměr|Průměrná ReplicationLatency Cassandra konektoru|Žádné dimenze|
|CassandraConnectorReplicationHealthStatus|No|Stav replikace konektoru Cassandra|Počet|Počet|Stav replikace konektoru Cassandra|NotStarted, ReplicationInProgress, chyba|
|CassandraKeyspaceCreate|No|Cassandra místo pro vytváření klíčů|Počet|Počet|Cassandra místo pro vytváření klíčů|ResourceName |
|CassandraKeyspaceDelete|No|Odstraněné místo na Cassandra|Počet|Počet|Odstraněné místo na Cassandra|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Propustnost Cassandraho místa na disku se aktualizovala|Počet|Počet|Propustnost Cassandraho místa na disku se aktualizovala|ResourceName |
|CassandraKeyspaceUpdate|No|Cassandra se aktualizované místo na disku|Počet|Počet|Cassandra se aktualizované místo na disku|ResourceName |
|CassandraRequestCharges|No|Poplatky za žádosti Cassandra|Počet|Celkem|Ru spotřebované pro vytvořené požadavky Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType|
|CassandraRequests|No|Žádosti Cassandra|Počet|Počet|Počet provedených požadavků Cassandra|DatabaseName, CollectionName, oblast, typem operace OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Tabulka Cassandra se vytvořila.|Počet|Počet|Tabulka Cassandra se vytvořila.|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Tabulka Cassandra se odstranila.|Počet|Počet|Tabulka Cassandra se odstranila.|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Propustnost tabulky Cassandra se aktualizovala.|Počet|Počet|Propustnost tabulky Cassandra se aktualizovala.|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Tabulka Cassandra se aktualizovala.|Počet|Počet|Tabulka Cassandra se aktualizovala.|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Účet vytvořen|Počet|Počet|Účet vytvořen|Žádné dimenze|
|Využití datausage|No|Využití dat|Bajty|Celkem|Celkové využití dat nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|DedicatedGatewayRequests|Yes|DedicatedGatewayRequests|Počet|Počet|Požadavky na vyhrazenou bránu|DatabaseName, CollectionName, CacheExercised, OperationName, region|
|DeleteAccount|Yes|Účet se odstranil.|Počet|Počet|Účet se odstranil.|Žádné dimenze|
|DocumentCount|No|Počet dokumentů|Počet|Celkem|Celkový počet dokumentů hlášených v rozmezí 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|No|Kvóta dokumentu|Bajty|Celkem|Celková kvóta úložiště vykazovaná s členitosti 5 minut|CollectionName, DatabaseName, region|
|GremlinDatabaseCreate|No|Databáze Gremlin se vytvořila.|Počet|Počet|Databáze Gremlin se vytvořila.|ResourceName |
|GremlinDatabaseDelete|No|Databáze Gremlin se odstranila.|Počet|Počet|Databáze Gremlin se odstranila.|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Propustnost databáze Gremlin se aktualizovala|Počet|Počet|Propustnost databáze Gremlin se aktualizovala|ResourceName |
|GremlinDatabaseUpdate|No|Databáze Gremlin se aktualizovala.|Počet|Počet|Databáze Gremlin se aktualizovala.|ResourceName |
|GremlinGraphCreate|No|Graf Gremlin se vytvořil.|Počet|Počet|Graf Gremlin se vytvořil.|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Graf Gremlin se odstranil.|Počet|Počet|Graf Gremlin se odstranil.|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Byla aktualizována propustnost grafu Gremlin|Počet|Počet|Byla aktualizována propustnost grafu Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Graf Gremlin se aktualizoval.|Počet|Počet|Graf Gremlin se aktualizoval.|ResourceName, ChildResourceName, |
|IndexUsage|No|Využití indexu|Bajty|Celkem|Celkové využití indexu nahlášené s členitosti 5 minut|CollectionName, DatabaseName, region|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Bajty|Průměr|Velikost položek vyřazených z integrované mezipaměti|CacheType, oblast|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Procento|Průměr|Rychlost přístupů do mezipaměti pro integrované mezipaměti|CacheType, oblast|
|IntegratedCacheSize|No|IntegratedCacheSize|Bajty|Průměr|Velikost integrovaných mezipamětí pro vyhrazené požadavky brány|CacheType, oblast|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Počet|Průměr|Počet položek odebraných z integrované mezipaměti z důvodu vypršení platnosti TTL|CacheType, oblast|
|MetadataRequests|No|Žádosti o metadata|Počet|Počet|Počet požadavků na metadata Cosmos DB udržuje shromažďování systémových metadat pro každý účet, což vám umožní vytvořit výčet kolekcí, databází atd. a jejich konfigurací zdarma.|DatabaseName, CollectionName, region, StatusCode |
|MongoCollectionCreate|No|Kolekce Mongo se vytvořila.|Počet|Počet|Kolekce Mongo se vytvořila.|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Kolekce Mongo se odstranila.|Počet|Počet|Kolekce Mongo se odstranila.|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Byla aktualizována propustnost kolekce Mongo|Počet|Počet|Byla aktualizována propustnost kolekce Mongo|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Kolekce Mongo se aktualizovala|Počet|Počet|Kolekce Mongo se aktualizovala|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Databáze Mongo se odstranila.|Počet|Počet|Databáze Mongo se odstranila.|ResourceName |
|MongoDatabaseThroughputUpdate|No|Propustnost databáze Mongo se aktualizovala|Počet|Počet|Propustnost databáze Mongo se aktualizovala|ResourceName |
|MongoDBDatabaseCreate|No|Databáze Mongo se vytvořila.|Počet|Počet|Databáze Mongo se vytvořila.|ResourceName |
|MongoDBDatabaseUpdate|No|Databáze Mongo se aktualizovala.|Počet|Počet|Databáze Mongo se aktualizovala.|ResourceName |
|MongoRequestCharge|Yes|Poplatek za požadavek Mongo|Počet|Celkem|Spotřebované jednotky žádosti Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequests|Yes|Žádosti Mongo|Počet|Počet|Počet provedených požadavků Mongo|DatabaseName, CollectionName, region, příkazového řádku, ErrorCode, status|
|MongoRequestsCount|No|zastaralé Počet požadavků Mongo|CountPerSecond|Průměr|Počet požadavků Mongo za sekundu|DatabaseName, CollectionName, region, ErrorCode|
|MongoRequestsDelete|No|zastaralé Frekvence žádosti o odstranění Mongo|CountPerSecond|Průměr|Mongo žádosti o odstranění za sekundu|DatabaseName, CollectionName, region, ErrorCode|
|MongoRequestsInsert|No|zastaralé Frekvence požadavků na vložení Mongo|CountPerSecond|Průměr|Mongo vložení počtu za sekundu|DatabaseName, CollectionName, region, ErrorCode|
|MongoRequestsQuery|No|zastaralé Frekvence požadavků na dotaz Mongo|CountPerSecond|Průměr|Požadavek na dotaz Mongo za sekundu|DatabaseName, CollectionName, region, ErrorCode|
|MongoRequestsUpdate|No|zastaralé Frekvence požadavků na aktualizace Mongo|CountPerSecond|Průměr|Žádost o aktualizaci Mongo za sekundu|DatabaseName, CollectionName, region, ErrorCode|
|NormalizedRUConsumption|No|Normalizovaná spotřeba RU|Procento|Maximum|Maximální procento spotřeby v procentech za minutu|CollectionName, DatabaseName, region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Zřízená propustnost|Počet|Maximum|Zřízená propustnost|DatabaseName, CollectionName|
|RegionFailover|Yes|Převzetí služeb při selhání oblasti|Počet|Počet|Převzetí služeb při selhání oblasti|Žádné dimenze|
|RemoveRegion|Yes|Oblast odebrána|Počet|Počet|Oblast odebrána|Oblast|
|ReplicationLatency|Yes|Latence replikace p99|Milisekund|Průměr|Latence replikace p99 napříč zdrojovou a cílovou oblastí pro geograficky povolený účet|SourceRegion, TargetRegion|
|ServerSideLatency|No|Latence na straně serveru|Milisekund|Průměr|Latence na straně serveru|DatabaseName, CollectionName, region, ConnectionMode, typem operace OperationType, PublicAPIType|
|ServiceAvailability|No|Dostupnost služby|Procento|Průměr|Dostupnost žádostí o účet v časovém rozmezí jedné hodiny, dne nebo měsíce|Žádné dimenze|
|SqlContainerCreate|No|Vytvořil se kontejner SQL.|Počet|Počet|Vytvořil se kontejner SQL.|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Kontejner SQL se odstranil.|Počet|Počet|Kontejner SQL se odstranil.|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Propustnost kontejneru SQL se aktualizovala|Počet|Počet|Propustnost kontejneru SQL se aktualizovala|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|Kontejner SQL se aktualizoval.|Počet|Počet|Kontejner SQL se aktualizoval.|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|Databáze SQL se vytvořila.|Počet|Počet|Databáze SQL se vytvořila.|ResourceName |
|SqlDatabaseDelete|No|Databáze SQL se odstranila|Počet|Počet|Databáze SQL se odstranila|ResourceName |
|SqlDatabaseThroughputUpdate|No|Propustnost SQL Database se aktualizovala|Počet|Počet|Propustnost SQL Database se aktualizovala|ResourceName |
|SqlDatabaseUpdate|No|SQL Database se aktualizovala|Počet|Počet|SQL Database se aktualizovala|ResourceName |
|TableTableCreate|No|Tabulka Azure se vytvořila.|Počet|Počet|Tabulka Azure se vytvořila.|ResourceName |
|TableTableDelete|No|Tabulka Azure se odstranila.|Počet|Počet|Tabulka Azure se odstranila.|ResourceName |
|TableTableThroughputUpdate|No|Aktualizace propustnosti tabulky Azure|Počet|Počet|Aktualizace propustnosti tabulky Azure|ResourceName |
|TableTableUpdate|No|Tabulka Azure se aktualizovala|Počet|Počet|Tabulka Azure se aktualizovala|ResourceName |
|TotalRequests|Yes|Požadavky celkem|Počet|Počet|Počet provedených požadavků|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|TotalRequestUnits|Yes|Celkový počet jednotek žádostí|Počet|Celkem|Spotřebované jednotky žádosti|DatabaseName, CollectionName, region, StatusCode, typem operace OperationType, status|
|UpdateAccountKeys|Yes|Klíče účtu se aktualizovaly.|Počet|Počet|Klíče účtu se aktualizovaly.|KeyType|
|UpdateAccountNetworkSettings|Yes|Nastavení sítě účtu se aktualizovala.|Počet|Počet|Nastavení sítě účtu se aktualizovala.|Žádné dimenze|
|UpdateAccountReplicationSettings|Yes|Nastavení replikace účtu se aktualizovala|Počet|Počet|Nastavení replikace účtu se aktualizovala|Žádné dimenze|
|UpdateDiagnosticsSettings|No|Nastavení diagnostiky účtu se aktualizovala|Počet|Počet|Nastavení diagnostiky účtu se aktualizovala|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné dimenze|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádné dimenze|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné dimenze|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clustery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub||
|AvailableMemory|No|Paměť k dispozici|Procento|Maximum|Dostupná paměť pro cluster centra událostí jako procento z celkové paměti.|Role|
|CaptureBacklog|No|Zachyťte nevyřízené položky.|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.||
|CapturedBytes|No|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub||
|CapturedMessages|No|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub||
|ConnectionsClosed|No|Zavřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub||
|ConnectionsOpened|No|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub||
|Procesor|No|Procesor|Procento|Maximum|Využití CPU pro cluster centra událostí jako procento|Role|
|IncomingBytes|Yes|Příchozí bajty|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub||
|IncomingMessages|Yes|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub||
|IncomingRequests|Yes|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub||
|OutgoingBytes|Yes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub||
|OutgoingMessages|Yes|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub||
|QuotaExceededErrors|No|Chyby překročení kvóty.|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|Výsledek operace uvnitř|
|ServerErrors|No|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. EventHub|Výsledek operace uvnitř|
|Velikost|No|Velikost|Bajty|Průměr|Velikost centra EventHub v bajtech|Role|
|SuccessfulRequests|No|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|ThrottledRequests|No|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub|Výsledek operace uvnitř|
|UserErrors|No|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub|Výsledek operace uvnitř|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Počet|Průměr|Celkový počet aktivních připojení pro Microsoft. EventHub||
|CaptureBacklog|No|Zachyťte nevyřízené položky.|Počet|Celkem|Zachyťte nevyřízené položky pro Microsoft. EventHub.|EntityName|
|CapturedBytes|No|Zachycené bajty.|Bajty|Celkem|Zachycené bajty pro Microsoft. EventHub|EntityName|
|CapturedMessages|No|Zachycené zprávy.|Počet|Celkem|Zachycené zprávy pro Microsoft. EventHub|EntityName|
|ConnectionsClosed|No|Zavřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|ConnectionsOpened|No|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. EventHub|EntityName|
|EHABL|Yes|Archivovat nevyřízené zprávy (zastaralé)|Počet|Celkem|Archivní zprávy centra událostí v backlogu pro obor názvů (zastaralé)||
|EHAMBS|Yes|Propustnost zpráv archivu (zastaralé)|Bajty|Celkem|Propustnost archivovaných zpráv centra událostí v oboru názvů (zastaralé)||
|EHAMSGS|Yes|Archivní zprávy (zastaralé)|Počet|Celkem|Archivované zprávy centra událostí v oboru názvů (zastaralé)||
|EHINBYTES|Yes|Příchozí bajty (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů (zastaralé)||
|EHINMBS|Yes|Příchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost příchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích bajtů (zastaralé).||
|EHINMSGS|Yes|Příchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů (zastaralé)||
|EHOUTBYTES|Yes|Odchozí bajty (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů (zastaralé)||
|EHOUTMBS|Yes|Odchozí bajty (zastaralé) (zastaralé)|Bajty|Celkem|Propustnost odchozích zpráv centra událostí pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích bajtů (zastaralé).||
|EHOUTMSGS|Yes|Odchozí zprávy (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů (zastaralé)||
|FAILREQ|Yes|Neúspěšné žádosti (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)||
|IncomingBytes|Yes|Příchozí bajty|Bajty|Celkem|Příchozí bajty pro Microsoft. EventHub|EntityName|
|IncomingMessages|Yes|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. EventHub|EntityName|
|IncomingRequests|Yes|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. EventHub|EntityName|
|INMSGS|Yes|Příchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet příchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku příchozích zpráv (zastaralé).||
|INREQS|Yes|Příchozí požadavky (zastaralé)|Počet|Celkem|Celkový počet příchozích žádostí o odeslání pro obor názvů (zastaralé)||
|MEZI sebou|Yes|Interní chyby serveru (zastaralé)|Počet|Celkem|Celkový počet interních chyb serveru pro obor názvů (zastaralé)||
|MISCERR|Yes|Další chyby (zastaralé)|Počet|Celkem|Celkový počet neúspěšných žádostí pro obor názvů (zastaralé)||
|OutgoingBytes|Yes|Odchozí bajty|Bajty|Celkem|Odchozí bajty pro Microsoft. EventHub|EntityName|
|OutgoingMessages|Yes|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. EventHub|EntityName|
|OUTMSGS|Yes|Odchozí zprávy (zastaralé) (zastaralé)|Počet|Celkem|Celkový počet odchozích zpráv pro obor názvů Tato metrika je zastaralá. Místo toho prosím použijte metriku odchozích zpráv (zastaralé).||
|QuotaExceededErrors|No|Chyby překročení kvóty.|Počet|Celkem|Při překročení kvóty se vyskytly chyby pro Microsoft. EventHub.|EntityName, výsledek operace uvnitř|
|ServerErrors|No|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|Velikost|No|Velikost|Bajty|Průměr|Velikost centra EventHub v bajtech|EntityName|
|SuccessfulRequests|No|Úspěšné požadavky|Počet|Celkem|Úspěšné požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|SUCCREQ|Yes|Úspěšné požadavky (zastaralé)|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů (zastaralé)||
|SVRBSY|Yes|Chyby zaneprázdněnosti serveru (zastaralé)|Počet|Celkem|Celkový počet chyb zaneprázdněných serverem pro obor názvů (zastaralé)||
|ThrottledRequests|No|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|
|UserErrors|No|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. EventHub|EntityName, výsledek operace uvnitř|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clustery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Zařadit požadavky na bránu|Počet|Celkem|Počet požadavků brány podle kategorií (1xx/2xx/3xx/4xx/5xx)|Stavu protokolu http|
|GatewayRequests|Yes|Žádosti o bránu|Počet|Celkem|Počet žádostí o bránu|Stavu protokolu http|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|RequestThroughput příjemce proxy REST|CountPerSecond|Celkem|Počet požadavků uživatelů na Kafka REST proxy|Počítač, téma|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Yes|Neúspěšné žádosti příjemce proxy REST|CountPerSecond|Celkem|Výjimky žádosti o příjemce|Počítač, téma|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|RequestLatency příjemce proxy REST|Milisekund|Průměr|Latence zprávy v žádosti spotřebitele prostřednictvím proxy serveru REST Kafka|Počítač, téma|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Yes|Backlog žádosti o příjemce proxy REST|Milisekund|Průměr|Délka fronty proxy serveru REST uživatele|Počítač, téma|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|MessageThroughput výrobce REST proxy|CountPerSecond|Celkem|Počet zpráv producenta prostřednictvím proxy REST Kafka|Počítač, téma|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|MessageThroughput příjemce proxy REST|CountPerSecond|Celkem|Počet zákaznických zpráv prostřednictvím proxy REST Kafka|Počítač, téma|
|KafkaRestProxy.OpenConnections|Yes|ConcurrentConnections REST proxy|Počet|Celkem|Počet souběžných připojení prostřednictvím proxy REST Kafka|Počítač, téma|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|RequestThroughput výrobce REST proxy|CountPerSecond|Celkem|Počet žádostí o producenta na Kafka REST proxy|Počítač, téma|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Yes|Neúspěšné žádosti producenta REST proxy|CountPerSecond|Celkem|Výjimky žádosti producenta|Počítač, téma|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|RequestLatency výrobce REST proxy|Milisekund|Průměr|Latence zprávy v žádosti producenta prostřednictvím proxy Kafka REST|Počítač, téma|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Yes|Backlog žádosti výrobce proxy REST|Milisekund|Průměr|Délka fronty proxy REST producenta|Počítač, téma|
|NumActiveWorkers|Yes|Počet aktivních pracovníků|Počet|Maximum|Počet aktivních pracovníků|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Míra dostupnosti služby.|Žádné dimenze|
|CosmosDbCollectionSize|Yes|Velikost kolekce Cosmos DB|Bajty|Celkem|Velikost záložního Cosmos DB kolekce v bajtech.|Žádné dimenze|
|CosmosDbIndexSize|Yes|Velikost indexu Cosmos DB|Bajty|Celkem|Velikost indexu kolekce Cosmos DB v bajtech|Žádné dimenze|
|CosmosDbRequestCharge|Yes|Použití Cosmos DB RU|Počet|Celkem|Použití RU žádostí na Cosmos DB k zálohování služby.|Operace, ResourceType|
|CosmosDbRequests|Yes|Žádosti o Cosmos DB služby|Počet|Sum|Celkový počet požadavků odeslaných na Cosmos DB v rámci služby.|Operace, ResourceType|
|CosmosDbThrottleRate|Yes|Frekvence omezení Cosmos DB služby|Počet|Sum|Celkový počet odpovědí 429 odeslaných Cosmos DB služby.|Operace, ResourceType|
|IoTConnectorDeviceEvent|Yes|Počet příchozích zpráv|Počet|Sum|Celkový počet zpráv přijatých konektorem Azure IoT Connector pro FHIR před jakýmkoli normalizací.|Operace, konektor|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Průměrná latence fáze normalizing|Milisekund|Průměr|Průměrná doba mezi dobou příjmu události a časem zpracování události pro normalizaci.|Operace, konektor|
|IoTConnectorMeasurement|Yes|Počet měření|Počet|Sum|Počet normalizovaných hodnot, které byly přijaty fází převodu FHIR ve službě Azure IoT Connector pro FHIR.|Operace, konektor|
|IoTConnectorMeasurementGroup|Yes|Počet skupin zpráv|Počet|Sum|Celkový počet jedinečných seskupení měření v rámci typu, zařízení, pacienta a nakonfigurovaného časového období vygenerovaného fází převodu FHIR.|Operace, konektor|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Průměrná latence fáze skupiny|Milisekund|Průměr|Časové období mezi tím, kdy konektor IoT přijal data zařízení a kdy jsou data zpracována fází převodu FHIR.|Operace, konektor|
|IoTConnectorNormalizedEvent|Yes|Počet normalizovaných zpráv|Počet|Sum|Celkový počet mapovaných normalizovaných hodnot vydaných ze fáze normalizace konektoru Azure IoT Connector pro FHIR.|Operace, konektor|
|IoTConnectorTotalErrors|Yes|Celkový počet chyb|Počet|Sum|Celkový počet chyb protokolovaných službou Azure IoT Connector pro FHIR|Název, operace, ErrorType, ErrorSeverity, název konektoru|
|ServiceApiErrors|Yes|Chyby služby|Počet|Sum|Celkový počet interních chyb serveru generovaných službou.|Protokol, ověřování, operace, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Latence služby|Milisekund|Průměr|Latence odpovědi služby.|Protokol, ověřování, operace, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Počet|Sum|Celkový počet požadavků přijatých službou.|Protokol, ověřování, operace, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Celkový počet chyb|Počet|Sum|Celkový počet interních chyb serveru zjištěných službou.|Protokol, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Celková latence|Milisekund|Průměr|Latence odpovědi služby.|Protokol|
|TotalRequests|Yes|Požadavky celkem|Počet|Sum|Celkový počet požadavků přijatých službou.|Protokol|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Průměrné využití procesoru|Procento|Průměr|Celkové průměrné procento využití virtuálního procesoru v jednom minutovém intervalu. Celkový počet virtuálních PROCESORů je založený na uživatelem nakonfigurované hodnotě v definici SKU. Další filtr lze použít na základě RoleName definovaného v SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Průměrné využití procesoru|Procento|Průměr|Celkové průměrné procento využití virtuálního procesoru v jednom minutovém intervalu. Celkový počet virtuálních PROCESORů je založený na uživatelem nakonfigurované hodnotě v definici SKU. Další filtr lze použít na základě RoleName definovaného v SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Prahová hodnota metriky|Počet|Průměr|Nakonfigurované prahové hodnoty automatického škálování, když se spustilo automatické škálování.|MetricTriggerRule|
|ObservedCapacity|Yes|Zjištěná kapacita|Počet|Průměr|Kapacita nahlášená pro automatické škálování při jejím spuštění.||
|ObservedMetricValue|Yes|Zaznamenaná hodnota metriky|Počet|Průměr|Hodnota vypočítaná AutoScale při spuštění|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Zahájené akce škálování|Počet|Celkem|Směr operace škálování.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Dostupnost|Procento|Průměr|Procento úspěšně dokončených testů dostupnosti|availabilityResult/název, availabilityResult/umístění|
|availabilityResults/Count|No|Testy dostupnosti|Počet|Počet|Počet testů dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|availabilityResults/doba trvání|Yes|Doba trvání testu dostupnosti|Milisekund|Průměr|Doba trvání testu dostupnosti|availabilityResult/název, availabilityResult/umístění, availabilityResult/úspěch|
|browserTimings/networkDuration|Yes|Doba připojení k síti – načtení stránky|Milisekund|Průměr|Doba mezi požadavkem uživatele a připojením k síti. Zahrnuje vyhledávání DNS a přenosové připojení.|Žádné dimenze|
|browserTimings/processingDuration|Yes|Doba zpracování klienta|Milisekund|Průměr|Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky.|Žádné dimenze|
|browserTimings/receiveDuration|Yes|Doba přijetí odezvy|Milisekund|Průměr|Čas mezi prvním a posledním bajtů nebo až do odpojení|Žádné dimenze|
|browserTimings/sendDuration|Yes|Čas požadavku na odeslání|Milisekund|Průměr|Doba mezi síťovým připojením a přijetím prvního bajtu.|Žádné dimenze|
|browserTimings/totalDuration|Yes|Doba načítání stránky v prohlížeči|Milisekund|Průměr|Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí.|Žádné dimenze|
|závislosti/počet|No|Volání závislostí|Počet|Počet|Počet volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/doba trvání|Yes|Doba trvání závislosti|Milisekund|Průměr|Doba trvání volání prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/úspěšnost, závislost/cíl, závislost/resultCode, provoz/syntetické, cloudové/roleInstance, Cloud/roleName|
|závislosti/selhání|No|Selhání volání závislostí|Počet|Počet|Počet neúspěšných volání závislostí prováděných aplikací vůči externím prostředkům|závislost/typ, závislost/performanceBucket, závislost/cíl, závislost/resultCode, provoz/syntetické, Cloud/roleInstance, Cloud/roleName|
|výjimky/prohlížeč|No|Výjimky prohlížečů|Počet|Počet|Počet nezachycených výjimek vyvolaných v prohlížeči|Cloud/roleName|
|výjimky/počet|Yes|Výjimky|Počet|Počet|Kombinovaný počet všech nezachycených výjimek.|Cloud/roleName, Cloud/roleInstance, klient/typ|
|výjimky/Server|No|Výjimky serveru|Počet|Počet|Počet nezachycených výjimek vyvolaných v serverové aplikaci|Cloud/roleName, Cloud/roleInstance|
|pageViews/Count|Yes|Zobrazení stránek|Počet|Počet|Počet zobrazení stránek|provoz/syntetické, cloudové/roleName|
|pageViews/doba trvání|Yes|Doba načítání zobrazení stránky|Milisekund|Průměr|Doba načítání zobrazení stránky|provoz/syntetické, cloudové/roleName|
|Čítače výkonu/exceptionsPerSecond|Yes|Míra výjimek|CountPerSecond|Průměr|Počet zpracovaných a nezpracovaných výjimek hlášených systému Windows, včetně výjimek .NET a nespravovaných výjimek převedených na výjimky .NET.|Cloud/roleInstance|
|Čítače výkonu/memoryAvailableBytes|Yes|Dostupná paměť|Bajty|Průměr|Fyzická paměť je okamžitě k dispozici pro přidělení procesu nebo pro použití systémem.|Cloud/roleInstance|
|Čítače výkonu/processCpuPercentage|Yes|PROCESOR procesů|Procento|Průměr|Procentuální hodnota uplynulého času, který všechny podprocesy procesu používají k provádění instrukcí. Může se lišit od 0 do 100. Tato metrika indikuje výkon samotného procesu W3wp.|Cloud/roleInstance|
|Čítače výkonu/processIOBytesPerSecond|Yes|Rychlost zpracování v/v|BytesPerSecond|Průměr|Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení.|Cloud/roleInstance|
|Čítače výkonu/processorCpuPercentage|Yes|Čas procesoru|Procento|Průměr|Procento času, které procesor stráví v nečinných vláknech|Cloud/roleInstance|
|Čítače výkonu/processPrivateBytes|Yes|Nesdílené bajty procesu|Bajty|Průměr|Paměť exkluzivně přiřazená k procesům monitorovaných aplikací.|Cloud/roleInstance|
|Čítače výkonu/requestExecutionTime|Yes|Doba provádění požadavku HTTP|Milisekund|Průměr|Čas provedení posledního požadavku.|Cloud/roleInstance|
|Čítače výkonu/requestsInQueue|Yes|Požadavky HTTP ve frontě aplikací|Počet|Průměr|Délka fronty požadavků aplikace|Cloud/roleInstance|
|Čítače výkonu/requestsPerSecond|Yes|Rychlost požadavku HTTP|CountPerSecond|Průměr|Míra všech požadavků na aplikaci za sekundu z ASP.NET.|Cloud/roleInstance|
|požadavky/počet|No|Žádosti serveru|Počet|Počet|Počet dokončených požadavků HTTP|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|žádosti/doba trvání|Yes|Doba odezvy serveru|Milisekund|Průměr|Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|požadavky/selhání|No|Neúspěšné požadavky|Počet|Počet|Počet požadavků HTTP označených jako neúspěšné Ve většině případů se jedná o žádosti s kódem odpovědi >= 400 a nerovná se 401.|Request/performanceBucket, Request/resultCode, Operational/syntetické, Cloud/roleInstance, Cloud/roleName|
|žádosti/rychlost|No|Počet požadavků serveru|CountPerSecond|Průměr|Frekvence požadavků serveru za sekundu|požadavek/performanceBucket, požadavky/resultCode, provoz/syntetické, cloudové/roleInstance, požadavky/úspěch, Cloud/roleName|
|trasování/počet|Yes|Trasování|Počet|Počet|Počet dokumentů trasování|Trace/severityLevel, provozní/syntetické, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Yes|Neúspěšná čtení vlastností zařízení z IoT Central|Počet|Celkem|Počet všech neúspěšných čtení vlastností zahájených z IoT Central|Žádné dimenze|
|C2D. Property. Read. Success|Yes|Úspěšné čtení vlastností zařízení z IoT Central|Počet|Celkem|Počet všech úspěšných čtení vlastností zahájených z IoT Central|Žádné dimenze|
|C2D. Property. Update. Failure|Yes|Neúspěšné aktualizace vlastností zařízení z IoT Central|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností iniciované z IoT Central|Žádné dimenze|
|C2D. Property. Update. Success|Yes|Úspěšná aktualizace vlastností zařízení z IoT Central|Počet|Celkem|Počet všech úspěšných aktualizací vlastností zahájených z IoT Central|Žádné dimenze|
|connectedDeviceCount|No|Celkem připojených zařízení|Počet|Průměr|Počet zařízení připojených k IoT Central|Žádné dimenze|
|D2C. Property. Read. Failure|Yes|Neúspěšná čtení vlastností zařízení ze zařízení|Počet|Celkem|Počet všech neúspěšných čtení vlastností inicializovaných ze zařízení|Žádné dimenze|
|D2C. Property. Read. Success|Yes|Úspěšná čtení vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných čtení vlastností inicializovaných ze zařízení|Žádné dimenze|
|D2C. Property. Update. Failure|Yes|Neúspěšné aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech neúspěšných aktualizací vlastností inicializovaných ze zařízení|Žádné dimenze|
|D2C. Property. Update. Success|Yes|Úspěšná aktualizace vlastností zařízení ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací vlastností inicializovaných ze zařízení|Žádné dimenze|
|DataExport. Chyba|Yes|Chyby exportu dat|Počet|Celkem|Počet chyb, ke kterým došlo při exportu dat|exportId, exportDisplayName, destinationId, destinationDisplayName|
|DataExport. Messages. Filtered|Yes|Filtrované zprávy exportu dat|Počet|Celkem|Počet zpráv, které byly předány filtry při exportu dat|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataexports. Messages. Received|Yes|Přijaté zprávy exportu dat|Počet|Celkem|Počet zpráv příchozích do exportu dat před zpracováním filtrování a obohacení|exportId, exportDisplayName, destinationId, destinationDisplayName|
|DataExport. Messages. psaná|Yes|Napsané zprávy exportu dat|Počet|Celkem|Počet zpráv zapsaných do cílového umístění|exportId, exportDisplayName, destinationId, destinationDisplayName|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. managedhsms – trezor/

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|No|Celková dostupnost služby|Procento|Průměr|Dostupnost žádostí o služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Celkový počet přístupů k rozhraní API služby|Počet|Počet|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|No|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Celkový počet výsledků rozhraní API služby|Počet|Počet|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Celková dostupnost trezoru|Procento|Průměr|Dostupnost žádostí o trezor|ActivityType, Activity, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Celkové sytosti trezoru|Procento|Průměr|Využitá kapacita trezoru|ActivityType, Activity, TransactionType|
|ServiceApiHit|Yes|Celkový počet přístupů k rozhraní API služby|Počet|Počet|Celkový počet přístupů k rozhraní API služby|ActivityType, Activity|
|ServiceApiLatency|Yes|Celková latence rozhraní API služby|Milisekund|Průměr|Celková latence požadavků na rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Celkový počet výsledků rozhraní API služby|Počet|Počet|Počet celkových výsledků rozhraní API služby|ActivityType, Activity, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. Kubernetes/connectedClusters

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Celkový počet jader procesoru v připojeném clusteru|Počet|Celkem|Celkový počet jader procesoru v připojeném clusteru||


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Počet objektů BLOB dávky|Počet|Průměr|Počet zdrojů dat v agregované dávce pro přijímání.|databáze|
|BatchDuration|Yes|Doba trvání dávky|Sekundy|Průměr|Doba trvání agregační fáze v toku přijímání.|databáze|
|BatchesProcessed|Yes|Zpracované dávky|Počet|Celkem|Počet dávek agregovaných pro ingestování. Dávkování typu: zda dávka dosáhla doby dávkového zpracování, velikosti dat nebo počtu souborů, které jsou nastaveny pomocí dávkování zásad|Databáze, SealReason|
|BatchSize|Yes|Velikost dávky|Bajty|Průměr|Nekomprimovaná očekávaná velikost dat v agregované dávce pro přijímání.|databáze|
|BlobsDropped|Yes|Vynechané objekty blob|Počet|Celkem|Počet objektů BLOB trvale odmítnutých součástí|Databáze, element componentType, Component|
|BlobsProcessed|Yes|Zpracované objekty blob|Počet|Celkem|Počet objektů BLOB zpracovaných součástí|Databáze, element componentType, Component|
|BlobsReceived|Yes|Počet přijatých objektů BLOB|Počet|Celkem|Počet objektů BLOB přijatých komponentou ze vstupního datového proudu.|Databáze, element componentType, Component|
|CacheUtilization|Yes|Využití mezipaměti|Procento|Průměr|Úroveň využití v oboru clusteru|Žádné dimenze|
|ContinuousExportMaxLatenessMinutes|Yes|Maximální zpoždění průběžného exportu|Počet|Maximum|Zpoždění (v minutách) hlášené úlohami průběžného exportu v clusteru|Žádné dimenze|
|ContinuousExportNumOfRecordsExported|Yes|Průběžný export – počet exportovaných záznamů|Počet|Celkem|Počet exportovaných záznamů, které se vyvolaly pro každý artefakt úložiště zapsaný během operace exportu|ContinuousExportName, databáze|
|ContinuousExportPendingCount|Yes|Počet nevyřízených položek průběžného exportu|Počet|Maximum|Počet probíhajících úloh průběžného exportu připravených k provedení|Žádné dimenze|
|ContinuousExportResult|Yes|Výsledek průběžného exportu|Počet|Počet|Indikuje, jestli se průběžný export zdařil nebo selhal.|ContinuousExportName, výsledek, databáze|
|Procesor|Yes|Procesor|Procento|Průměr|Úroveň využití procesoru|Žádné dimenze|
|DiscoveryLatency|Yes|Latence zjišťování|Sekundy|Průměr|Nahlášená datovými připojeními (pokud existují) Čas v sekundách od okamžiku, kdy se zpráva zařazování do fronty, nebo je vytvořena událost, dokud není zjištěna pomocí datového připojení. Tento čas není zahrnutý v celkovém trvání ingestování Azure Průzkumník dat.|Element componentType, Component|
|EventsDropped|Yes|Vyřazené události|Počet|Celkem|Počet událostí, které byly trvale vyřazeny datovým připojením. Odešle se metrika výsledku příjmu s důvodem selhání.|Element componentType, Component|
|EventsProcessed|Yes|Zpracované události|Počet|Celkem|Počet událostí zpracovaných clusterem|Element componentType, Component|
|EventsProcessedForEventHubs|Yes|Zpracované události (pro Event/centra IoT)|Počet|Celkem|Počet událostí zpracovaných clusterem při příjmu z události nebo IoT Hub|EventStatus|
|EventsReceived|Yes|Přijaté události|Počet|Celkem|Počet událostí přijatých datovým připojením.|Element componentType, Component|
|ExportUtilization|Yes|Využití exportu|Procento|Maximum|Využití exportu|Žádné dimenze|
|IngestionLatencyInSeconds|Yes|Latence příjmu dat|Sekundy|Průměr|Latence přijatých dat, od okamžiku, kdy byla data v clusteru přijata, až do doby, kdy jsou připravena pro dotazy. Období latence příjmu dat závisí na scénáři příjmu dat.|Žádné dimenze|
|IngestionResult|Yes|Výsledek ingestování|Počet|Celkem|Počet operací ingestování|IngestionResultDetails|
|IngestionUtilization|Yes|Využití příjmu dat|Procento|Průměr|Poměr využitých slotů pro přijímání na clusteru|Žádné dimenze|
|IngestionVolumeInMB|Yes|Objem příjmu dat|Bajty|Celkem|Celkový objem zpracovaných dat do clusteru|databáze|
|InstanceCount|Yes|Počet instancí|Počet|Průměr|Celkový počet instancí|Žádné dimenze|
|Udržení|Yes|Zachovat naživu|Počet|Průměr|Správnosti check indikuje, že cluster reaguje na dotazy|Žádné dimenze|
|MaterializedViewAgeMinutes|Yes|Stáří vyhodnoceného zobrazení|Počet|Průměr|Stáří vyhodnoceného zobrazení v řádu minut|Databáze, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Ztráta dat z vyhodnoceného zobrazení|Počet|Maximum|Indikuje možnou ztrátu dat v materializované zobrazení.|Databáze, MaterializedViewName, druh|
|MaterializedViewExtentsRebuild|Yes|Vyhodnocené rozsahy zobrazení – opětovné sestavení|Počet|Průměr|Počet rozsahů opětovného sestavení|Databáze, MaterializedViewName|
|MaterializedViewHealth|Yes|Stav materializované zobrazení|Počet|Průměr|Stav materializované zobrazení (1 pro v pořádku, 0 pro nefunkční)|Databáze, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Vyhodnocené záznamy zobrazení v rozdílu|Počet|Průměr|Počet záznamů v nematerializované části zobrazení|Databáze, MaterializedViewName|
|MaterializedViewResult|Yes|Výsledek materializované zobrazení|Počet|Průměr|Výsledek procesu materializace|Databáze, MaterializedViewName, výsledek|
|QueryDuration|Yes|Doba trvání dotazu|Milisekund|Průměr|Doba trvání dotazů v sekundách|QueryStatus|
|QueryResult|No|Výsledek dotazu|Počet|Počet|Celkový počet dotazů|QueryStatus|
|QueueLength|Yes|Délka fronty|Počet|Průměr|Počet čekajících zpráv ve frontě součásti.|Element componentType|
|QueueOldestMessage|Yes|Vyřadit nejstarší zprávu|Počet|Průměr|Čas v sekundách od vložení nejstarší zprávy ve frontě|Element componentType|
|ReceivedDataSizeBytes|Yes|Přijaté bajty velikosti dat|Bajty|Průměr|Velikost dat přijatých datovým připojením. Toto je velikost datového proudu nebo velikost nezpracovaných dat, pokud je k dispozici.|Element componentType, Component|
|StageLatency|Yes|Latence fáze|Sekundy|Průměr|Kumulativní doba od spuštění zprávy, dokud není přijata komponentou pro vytváření sestav pro zpracování (doba vyhledávání je nastavena při zařazení zprávy do fronty pro zpracování příjmu nebo při zjištění pomocí datového připojení).|Databáze, element componentType|
|SteamingIngestRequestRate|Yes|Četnost požadavků příjmu streamování|Počet|RateRequestsPerSecond|Míra požadavků ingestování datových proudů (počet požadavků za sekundu)|Žádné dimenze|
|StreamingIngestDataRate|Yes|Datová rychlost příjmu streamování|Počet|Průměr|Přenosová rychlost ingestování datových proudů (MB za sekundu)|Žádné dimenze|
|StreamingIngestDuration|Yes|Doba trvání příjmu streamování|Milisekund|Průměr|Doba ingestování streamování v milisekundách|Žádné dimenze|
|StreamingIngestResults|Yes|Výsledek příjmu streamování|Počet|Průměr|Výsledek ingestování streamování|Výsledek|
|TotalNumberOfConcurrentQueries|Yes|Celkový počet souběžných dotazů|Počet|Maximum|Celkový počet souběžných dotazů|Žádné dimenze|
|TotalNumberOfExtents|Yes|Celkový počet rozsahů|Počet|Celkem|Celkový počet rozsahů dat|Žádné dimenze|
|TotalNumberOfThrottledCommands|Yes|Celkový počet příkazů s omezením|Počet|Celkem|Celkový počet příkazů s omezením|CommandType|
|TotalNumberOfThrottledQueries|Yes|Celkový počet omezených dotazů|Počet|Maximum|Celkový počet omezených dotazů|Žádné dimenze|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Yes|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Yes|Neúspěšné akce |Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Yes|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionsStarted|Yes|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Yes|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Yes|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Yes|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Využití paměti konektoru pro prostředí integrační služby|Procento|Průměr|Využití paměti konektoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Využití procesoru konektoru pro prostředí integrační služby|Procento|Průměr|Využití procesoru konektoru pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Využití paměti workflowu pro prostředí integrační služby|Procento|Průměr|Využití paměti pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Využití procesoru pracovního postupu pro prostředí integrační služby|Procento|Průměr|Využití procesoru pracovního postupu pro prostředí integrační služby.|Žádné dimenze|
|RunFailurePercentage|Yes|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|RunLatency|Yes|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsCancelled|Yes|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunsCompleted|Yes|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Yes|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsStarted|Yes|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsSucceeded|Yes|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunStartThrottledEvents|Yes|Spustit omezené události|Počet|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Yes|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Yes|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|TriggerFireLatency|Yes|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Yes|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Yes|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersFailed|Yes|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Yes|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Yes|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersStarted|Yes|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Yes|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggerSuccessLatency|Yes|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Yes|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Latence akcí |Sekundy|Průměr|Latence dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsCompleted|Yes|Dokončené akce |Počet|Celkem|Počet dokončených akcí pracovního postupu.|Žádné dimenze|
|ActionsFailed|Yes|Neúspěšné akce |Počet|Celkem|Počet neúspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionsSkipped|Yes|Vynechané akce |Počet|Celkem|Počet vynechaných akcí pracovního postupu.|Žádné dimenze|
|ActionsStarted|Yes|Spuštěné akce |Počet|Celkem|Počet spuštěných akcí pracovního postupu.|Žádné dimenze|
|ActionsSucceeded|Yes|Úspěšné akce |Počet|Celkem|Počet úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionSuccessLatency|Yes|Latence úspěšných akcí |Sekundy|Průměr|Latence úspěšných akcí pracovního postupu.|Žádné dimenze|
|ActionThrottledEvents|Yes|Omezené události akcí|Počet|Celkem|Počet omezených událostí akcí pracovního postupu...|Žádné dimenze|
|BillableActionExecutions|Yes|Fakturovatelné provádění akcí|Počet|Celkem|Počet fakturovaných provedení akcí pracovního postupu|Žádné dimenze|
|BillableTriggerExecutions|Yes|Fakturovatelná spuštění triggerů|Počet|Celkem|Počet fakturovaných provedení triggerů pracovního postupu|Žádné dimenze|
|BillingUsageNativeOperation|Yes|Využití fakturace pro provádění nativních operací|Počet|Celkem|Počet fakturovaných provedení nativních operací|Žádné dimenze|
|BillingUsageStandardConnector|Yes|Využití fakturace pro spuštění standardních konektorů|Počet|Celkem|Počet fakturovaných spuštění standardních konektorů|Žádné dimenze|
|BillingUsageStorageConsumption|Yes|Využití fakturace pro provádění spotřeby úložiště|Počet|Celkem|Počet fakturovaných spuštění spotřeby úložiště|Žádné dimenze|
|RunFailurePercentage|Yes|Procento selhání spuštění|Procento|Celkem|Procento běhu pracovního postupu se nezdařilo.|Žádné dimenze|
|RunLatency|Yes|Latence spuštění|Sekundy|Průměr|Latence dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsCancelled|Yes|Zrušené běhy|Počet|Celkem|Počet zrušených běhů pracovního postupu.|Žádné dimenze|
|RunsCompleted|Yes|Dokončené běhy|Počet|Celkem|Počet dokončených běhů pracovního postupu.|Žádné dimenze|
|RunsFailed|Yes|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pracovního postupu se nezdařil.|Žádné dimenze|
|RunsStarted|Yes|Spuštěné běhy|Počet|Celkem|Počet spuštěných pracovních postupů|Žádné dimenze|
|RunsSucceeded|Yes|Úspěšná spuštění|Počet|Celkem|Počet úspěšných běhů pracovního postupu.|Žádné dimenze|
|RunStartThrottledEvents|Yes|Spustit omezené události|Počet|Celkem|Počet neomezených událostí spuštění pracovního postupu.|Žádné dimenze|
|RunSuccessLatency|Yes|Latence úspěšného spuštění|Sekundy|Průměr|Latence úspěšných spuštění pracovního postupu.|Žádné dimenze|
|RunThrottledEvents|Yes|Události omezeného spuštění|Počet|Celkem|Počet akcí pracovního postupu nebo omezené události triggeru|Žádné dimenze|
|TotalBillableExecutions|Yes|Fakturovatelná spuštění celkem|Počet|Celkem|Počet fakturovaných provedení pracovního postupu|Žádné dimenze|
|TriggerFireLatency|Yes|Latence požáru triggeru |Sekundy|Průměr|Latence aktivovaných triggerů pracovního postupu.|Žádné dimenze|
|TriggerLatency|Yes|Latence triggeru |Sekundy|Průměr|Latence dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersCompleted|Yes|Aktivační události dokončeny |Počet|Celkem|Počet dokončených triggerů pracovního postupu.|Žádné dimenze|
|TriggersFailed|Yes|Neúspěšná triggery |Počet|Celkem|Počet neúspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggersFired|Yes|Aktivační události aktivovány |Počet|Celkem|Počet vyvolaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSkipped|Yes|Aktivační události přeskočeny|Počet|Celkem|Počet vynechaných triggerů pracovního postupu.|Žádné dimenze|
|TriggersStarted|Yes|Spuštěné aktivační události |Počet|Celkem|Počet spuštěných triggerů pracovního postupu.|Žádné dimenze|
|TriggersSucceeded|Yes|Aktivační události byly úspěšné |Počet|Celkem|Počet úspěšných triggerů pracovního postupu|Žádné dimenze|
|TriggerSuccessLatency|Yes|Latence úspěšnosti triggeru |Sekundy|Průměr|Latence úspěšných triggerů pracovního postupu.|Žádné dimenze|
|TriggerThrottledEvents|Yes|Omezené události triggeru|Počet|Celkem|Počet neomezených událostí triggeru pracovního postupu.|Žádné dimenze|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Aktivní jádra|Yes|Aktivní jádra|Počet|Průměr|Počet aktivních jader|Scénář, název_clusteru|
|Aktivní uzly|Yes|Aktivní uzly|Počet|Průměr|Počet uzlů Active Jedná se o uzly, které aktivně spouštějí úlohu.|Scénář, název_clusteru|
|Zrušit požadovaná spuštění|Yes|Zrušit požadovaná spuštění|Počet|Celkem|Počet spuštění, kde se pro tento pracovní prostor požádalo o zrušení Počet se aktualizuje po přijetí žádosti o zrušení pro spuštění.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Zrušená spuštění|Yes|Zrušená spuštění|Počet|Celkem|Počet zrušených spuštění pro tento pracovní prostor Počet se aktualizuje po úspěšném zrušení běhu.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Dokončená spuštění|Yes|Dokončená spuštění|Počet|Celkem|Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen. Počet se aktualizuje po dokončení běhu a shromažďování výstupu.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|CpuUtilization|Yes|CpuUtilization|Počet|Průměr|Procento využití na uzlu procesoru Využití je hlášeno v jednom minutovém intervalu.|Scénář, runId, NodeId, název_clusteru|
|Chyby|Yes|Chyby|Počet|Celkem|Počet chyb spuštění v tomto pracovním prostoru. Počet se aktualizuje pokaždé, když při spuštění dojde k chybě.|Scenario|
|Neúspěšná spuštění|Yes|Neúspěšná spuštění|Počet|Celkem|Počet spuštění pro tento pracovní prostor se nezdařil. Počet se aktualizuje, když se spuštění nezdařilo.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Dokončují se běhy.|Yes|Dokončují se běhy.|Počet|Celkem|Počet spuštění, které vstoupily do stavu dokončení pro tento pracovní prostor. Počet se aktualizuje po dokončení běhu, ale shromažďování výstupu ještě probíhá.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Počet|Průměr|Procento využití paměti v uzlu GPU Využití je hlášeno v jednom minutovém intervalu.|Scénář, runId, NodeId, DeviceId, název_clusteru|
|GpuUtilization|Yes|GpuUtilization|Počet|Průměr|Procento využití v uzlu GPU Využití je hlášeno v jednom minutovém intervalu.|Scénář, runId, NodeId, DeviceId, název_clusteru|
|Jádra nečinných|Yes|Jádra nečinných|Počet|Průměr|Počet nečinných jader|Scénář, název_clusteru|
|Nečinné uzly|Yes|Nečinné uzly|Počet|Průměr|Počet nečinných uzlů. Nečinné uzly jsou uzly, které nespouštějí žádné úlohy, ale mohou přijmout novou úlohu, je-li k dispozici.|Scénář, název_clusteru|
|Vynechávání jader|Yes|Vynechávání jader|Počet|Průměr|Počet opouštících jader|Scénář, název_clusteru|
|Ukončení uzlů|Yes|Ukončení uzlů|Počet|Průměr|Počet opouštících uzlů. Ukončení uzlů jsou uzly, které právě dokončí zpracování úlohy a přestanou do stavu nečinnosti.|Scénář, název_clusteru|
|Nasazení modelu se nezdařilo|Yes|Nasazení modelu se nezdařilo|Počet|Celkem|Počet nasazení modelů, která v tomto pracovním prostoru selhala|Scénář, StatusCode|
|Nasazení modelu spuštěn|Yes|Nasazení modelu spuštěn|Počet|Celkem|Počet nasazení modelů spuštěných v tomto pracovním prostoru|Scenario|
|Nasazení modelu bylo úspěšné.|Yes|Nasazení modelu bylo úspěšné.|Počet|Celkem|Počet nasazení modelů, která byla v tomto pracovním prostoru úspěšná|Scenario|
|Nepovedlo se zaregistrovat model|Yes|Nepovedlo se zaregistrovat model|Počet|Celkem|Počet registrací modelů, které se v tomto pracovním prostoru nezdařily|Scénář, StatusCode|
|Registr modelu byl úspěšný.|Yes|Registr modelu byl úspěšný.|Počet|Celkem|Počet registrací modelů, které byly v tomto pracovním prostoru úspěšně dokončeny|Scenario|
|Neodpovídá spuštění|Yes|Neodpovídá spuštění|Počet|Celkem|Počet běhů nereagujících na tento pracovní prostor. Počet se aktualizuje, když běh vstoupí do stavu neodpovídá.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Nespuštěná spuštění|Yes|Nespuštěná spuštění|Počet|Celkem|Počet spuštění v neaktivním stavu pro tento pracovní prostor Počet se aktualizuje, když se přijme požadavek na vytvoření běhu, ale informace o spuštění ještě nejsou naplněné. |Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Zrušené jádra|Yes|Zrušené jádra|Počet|Průměr|Počet zrušených jader|Scénář, název_clusteru|
|Přepnuté uzly|Yes|Přepnuté uzly|Počet|Průměr|Počet zrušených uzlů. Tyto uzly jsou uzly s nízkou prioritou, které jsou mimo dostupný fond uzlů.|Scénář, název_clusteru|
|Připravují se běhy.|Yes|Připravují se běhy.|Počet|Celkem|Počet běhů, které se připravují pro tento pracovní prostor. Počet se aktualizuje, když spuštění vstoupí do stavu Příprava během přípravy prostředí pro spuštění.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Spuštění zřizování|Yes|Spuštění zřizování|Počet|Celkem|Počet spuštění, která jsou zajišťována pro tento pracovní prostor. Počet se aktualizuje, když běh čeká na vytvoření nebo zřizování cíle výpočetní funkce.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Spuštění ve frontě|Yes|Spuštění ve frontě|Počet|Celkem|Počet spuštění, které jsou zařazeny do fronty pro tento pracovní prostor. Počet se aktualizuje při zařazení běhu do fronty v cíli výpočtu. Může nastat při čekání na přípravu požadovaných výpočetních uzlů.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Procento využití kvóty|Yes|Procento využití kvóty|Počet|Průměr|Procento využité kvóty|Scénář, název_clusteru, VmFamilyName, VmPriority|
|Spuštěná spuštění|Yes|Spuštěná spuštění|Počet|Celkem|Počet spuštěných běhů pro tento pracovní prostor. Počet se aktualizuje, když se spuštění spustí na požadovaných prostředcích.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Spouštění spuštění|Yes|Spouštění spuštění|Počet|Celkem|Počet spuštění spuštěných pro tento pracovní prostor. Počet se aktualizuje po žádosti o vytvoření informací o spuštění a spuštění, jako je například ID běhu.|Scénář, RunType, PublishedPipelineId, ComputeType, PipelineStepType, experiment|
|Celkový počet jader|Yes|Celkový počet jader|Počet|Průměr|Počet jader celkem|Scénář, název_clusteru|
|Celkem uzlů|Yes|Celkem uzlů|Počet|Průměr|Počet uzlů celkem Tento součet zahrnuje některé aktivní uzly, nečinné uzly, nepoužité uzly, uzly Premepted a opouští uzly.|Scénář, název_clusteru|
|Nepoužitelné jádra|Yes|Nepoužitelné jádra|Počet|Průměr|Počet nepoužitelných jader|Scénář, název_clusteru|
|Nepoužité uzly|Yes|Nepoužité uzly|Počet|Průměr|Počet nepoužitelných uzlů. Nepoužité uzly nejsou funkční kvůli nějakému problému s nepřeložitelné. Azure bude tyto uzly recyklovat.|Scénář, název_clusteru|
|Upozornění|Yes|Upozornění|Počet|Celkem|Počet upozornění spuštění v tomto pracovním prostoru. Počet se aktualizuje pokaždé, když při spuštění dojde k upozornění.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/Accounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Dostupnost rozhraní API|ApiCategory, ApiName|
|Využití|No|Využití|Počet|Počet|Počet volání rozhraní API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Počet assetů|Počet|Průměr|Kolik prostředků již bylo vytvořeno v aktuálním účtu Media Service|Žádné dimenze|
|AssetQuota|Yes|Kvóta prostředků|Počet|Průměr|Počet povolených prostředků pro aktuální účet Media Service|Žádné dimenze|
|AssetQuotaUsedPercentage|Yes|Procento využité kvóty prostředků|Procento|Průměr|Procento využitého prostředku v aktuálním účtu Media Service|Žádné dimenze|
|ChannelsAndLiveEventsCount|Yes|Počet aktivních událostí|Počet|Průměr|Celkový počet živých událostí v aktuálním účtu Media Services|Žádné dimenze|
|ContentKeyPolicyCount|Yes|Počet zásad klíče obsahu|Počet|Průměr|Kolik zásad pro klíč obsahu se už v aktuálním účtu Media Service vytvořilo|Žádné dimenze|
|ContentKeyPolicyQuota|Yes|Kvóta zásad pro klíč obsahu|Počet|Průměr|Kolik zásad pro klíč obsahu je pro aktuální účet Media Service povolené|Žádné dimenze|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Procento využité kvóty zásad klíčů obsahu|Procento|Průměr|Procento použitých zásad klíče obsahu v aktuálním účtu Media Service|Žádné dimenze|
|MaxChannelsAndLiveEventsCount|Yes|Maximální kvóta živé události|Počet|Maximum|Maximální počet živých událostí povolených v aktuálním účtu Media Services|Žádné dimenze|
|MaxRunningChannelsAndLiveEventsCount|Yes|Maximální spuštěná kvóta živé události|Počet|Maximum|Maximální počet spuštěných živých událostí povolených v aktuálním účtu Media Services|Žádné dimenze|
|RunningChannelsAndLiveEventsCount|Yes|Spuštění počtu živých událostí|Počet|Průměr|Celkový počet spuštěných živých událostí v aktuálním účtu Media Services|Žádné dimenze|
|StreamingPolicyCount|Yes|Počet zásad streamování|Počet|Průměr|Kolik zásad streamování už je v aktuálním účtu Media Service vytvořené|Žádné dimenze|
|StreamingPolicyQuota|Yes|Kvóta zásad streamování|Počet|Průměr|Počet povolených zásad streamování pro aktuální účet Media Service|Žádné dimenze|
|StreamingPolicyQuotaUsedPercentage|Yes|Procento využité kvóty zásad streamování|Procento|Průměr|Procento využité zásady streamování v aktuálním účtu Media Service|Žádné dimenze|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/MediaServices/liveEvents

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Přenosová rychlost příjmu živých událostí|BitsPerSecond|Průměr|Příchozí přenosová rychlost ingestovaná pro živou událost v bitech za sekundu.|Stop|
|IngestDriftValue|Yes|Hodnota posunu živé události ingestování|Sekundy|Maximum|Posun mezi časovým razítkem ingestního obsahu a systémovými hodinami měřenými v sekundách za minutu. Nenulová hodnota znamená, že ingestující obsah dorazí na pomalejší než systémový čas.|Stop|
|IngestLastTimestamp|Yes|Živá událost ingesta – poslední časové razítko|Milisekund|Maximum|Poslední časové razítko ingestované pro živou událost|Stop|
|LiveOutputLastTimestamp|Yes|Poslední výstupní časové razítko|Milisekund|Maximum|Časové razítko poslední fragmenty nahrané do úložiště pro výstup živé události|Stop|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/starají

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Procesor|Yes|Využití procesoru|Procento|Průměr|Využití procesoru pro koncové body streamování Premium Tato data nejsou k dispozici pro standardní koncové body streamování.|Žádné dimenze|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Objem výstupních dat (v bajtech).|OutputFormat|
|EgressBandwidth|No|Šířka pásma pro výstup|BitsPerSecond|Průměr|Šířka pásma pro odchozí přenosy v bitech za sekundu|Žádné dimenze|
|Žádosti|Yes|Žádosti|Počet|Celkem|Požadavky na koncový bod streamování.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Koncová latence úspěch|Milisekund|Průměr|Průměrná latence pro úspěšné požadavky v milisekundách.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Aktivní relace vykreslování|Počet|Průměr|Celkový počet aktivních relací vykreslování|SessionType, SDKVersion|
|AssetsConverted|Yes|Převedené prostředky|Počet|Celkem|Celkový počet převedených prostředků|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Vytvořené kotvy|Počet|Celkem|Počet vytvořených ukotvení|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Odstraněné kotvy|Počet|Celkem|Počet odstraněných ukotvení|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Dotazované kotvy|Počet|Celkem|Počet dotazů na prostorové kotvy|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Kotvy byly aktualizovány|Počet|Celkem|Počet aktualizovaných ukotvení|DeviceFamily, SDKVersion|
|PosesFound|Yes|Nalezené pozice|Počet|Celkem|Počet vrácených pozice|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Celkový počet denních ukotvení|Počet|Průměr|Celkový počet kotev – denně|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Velikost přiděleného fondu|Bajty|Průměr|Zřízená velikost tohoto fondu|Žádné dimenze|
|VolumePoolAllocatedToVolumeThroughput|Yes|Propustnost přidělené fondu|BytesPerSecond|Průměr|Součet propustnosti všech svazků patřících do fondu|Žádné dimenze|
|VolumePoolAllocatedUsed|Yes|Fond přidělený velikosti svazku|Bajty|Průměr|Přidělená velikost fondu, která se používá|Žádné dimenze|
|VolumePoolProvisionedThroughput|Yes|Zřízená propustnost fondu|BytesPerSecond|Průměr|Zřízená propustnost tohoto fondu|Žádné dimenze|
|VolumePoolTotalLogicalSize|Yes|Velikost spotřebovaného fondu|Bajty|Průměr|Součet logické velikosti všech svazků patřících do fondu|Žádné dimenze|
|VolumePoolTotalSnapshotSize|Yes|Celková velikost snímku pro fond|Bajty|Průměr|Součet velikosti snímku všech svazků v tomto fondu|Žádné dimenze|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/svazky

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Průměrná latence čtení|Milisekund|Průměr|Průměrná latence čtení v milisekundách na operaci|Žádné dimenze|
|AverageWriteLatency|Yes|Průměrná latence zápisu|Milisekund|Průměr|Průměrná latence zápisu v milisekundách na operaci|Žádné dimenze|
|CbsVolumeBackupActive|Yes|Je zálohování svazku pozastavené|Počet|Průměr|Pozastavila se zásada zálohování pro svazek? 0, pokud ano, 1, pokud ne.|Žádné dimenze|
|CbsVolumeLogicalBackupBytes|Yes|Bajty zálohy svazku|Bajty|Průměr|Celkový počet bajtů zálohovaných pro tento svazek.|Žádné dimenze|
|CbsVolumeOperationComplete|Yes|Je dokončená operace zálohování svazku|Počet|Průměr|Dokončila se poslední operace zálohování nebo obnovení svazku úspěšně? 1, pokud ano, 0, pokud ne.|Žádné dimenze|
|CbsVolumeOperationTransferredBytes|Yes|Bajty posledního přeneseného svazku ze zálohy|Bajty|Průměr|Celkový počet bajtů přenesených pro poslední operaci zálohování nebo obnovení.|Žádné dimenze|
|CbsVolumeProtected|Yes|Je povolené zálohování svazku|Počet|Průměr|Je pro svazek povolená záloha? 1, pokud ano, 0, pokud ne.|Žádné dimenze|
|OtherThroughput|Yes|Jiná propustnost|BytesPerSecond|Průměr|Jiná propustnost (která není pro čtení nebo zápis) v bajtech za sekundu|Žádné dimenze|
|ReadIops|Yes|Čtení IOPS|CountPerSecond|Průměr|Načíst vstupně-výstupní operace za sekundu|Žádné dimenze|
|ReadThroughput|Yes|Propustnost čtení|BytesPerSecond|Průměr|Propustnost čtení v bajtech za sekundu|Žádné dimenze|
|TotalThroughput|Yes|Celková propustnost|BytesPerSecond|Průměr|Součet veškeré propustnosti v bajtech za sekundu|Žádné dimenze|
|VolumeAllocatedSize|Yes|Velikost přiděleného svazku|Bajty|Průměr|Zřízená velikost svazku|Žádné dimenze|
|VolumeConsumedSizePercentage|Yes|Procentuální velikost spotřebovaného objemu|Procento|Průměr|Procento využitého objemu, včetně snímků.|Žádné dimenze|
|VolumeLogicalSize|Yes|Velikost spotřebovaného svazku|Bajty|Průměr|Logická velikost svazku (použité bajty)|Žádné dimenze|
|VolumeSnapshotSize|Yes|Velikost snímku svazku|Bajty|Průměr|Velikost všech snímků ve svazku|Žádné dimenze|
|WriteIops|Yes|Zápis IOPS|CountPerSecond|Průměr|Zapsat vstupně-výstupní operace za sekundu|Žádné dimenze|
|WriteThroughput|Yes|Propustnost zápisu|BytesPerSecond|Průměr|Propustnost zápisu v bajtech za sekundu|Žádné dimenze|
|XregionReplicationHealthy|Yes|Je stav replikace svazku v pořádku|Počet|Průměr|Podmínka vztahu, 1 nebo 0.|Žádné dimenze|
|XregionReplicationLagTime|Yes|Prodleva při replikaci svazků|Sekundy|Průměr|Množství času v sekundách, po které se data na zrcadle prodlevy za zdrojem.|Žádné dimenze|
|XregionReplicationLastTransferDuration|Yes|Doba trvání posledního přenosu replikace svazku|Sekundy|Průměr|Doba v sekundách, kterou trvalo dokončení posledního přenosu.|Žádné dimenze|
|XregionReplicationLastTransferSize|Yes|Velikost posledního přenosu replikace svazku|Bajty|Průměr|Celkový počet bajtů přenesených jako součást posledního přenosu.|Žádné dimenze|
|XregionReplicationRelationshipProgress|Yes|Průběh replikace svazků|Bajty|Průměr|Celkové množství přenesených dat pro aktuální operaci přenosu.|Žádné dimenze|
|XregionReplicationRelationshipTransferring|Yes|Je přenos replikace svazků|Počet|Průměr|Určuje, jestli je stav replikace svazku "přenos".|Žádné dimenze|
|XregionReplicationTotalTransferBytes|Yes|Celkový přenos replikace svazků|Bajty|Průměr|Pro relaci se přenesly kumulativní bajty.|Žádné dimenze|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Čas Application Gateway celkem|Milisekund|Průměr|Průměrná doba, kterou trvá zpracování požadavku, a jeho odpověď k odeslání. Počítá se jako průměr intervalu od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že to obvykle zahrnuje dobu zpracování Application Gateway, čas, po který jsou pakety požadavků a odpovědí přenášeny přes síť, a čas, kdy server back-end trvala odpověď.|Naslouchací proces|
|AvgRequestCountPerHealthyHost|No|Počet požadavků za minutu na hostitele v pořádku|Počet|Průměr|Průměrný počet požadavků za minutu na hostitele back-endu v pořádku ve fondu|BackendSettingsPool|
|BackendConnectTime|No|Čas připojení back-endu|Milisekund|Průměr|Čas strávený navázáním spojení s back-end serverem|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Doba odezvy prvního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a přijetí prvního bajtu hlavičky odpovědi, odhad doby zpracování back-endu serveru|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendLastByteResponseTime|No|Doba odezvy posledního bajtu back-endu|Milisekund|Průměr|Časový interval mezi zahájením navázání připojení k back-endu serveru a příjem posledního bajtu textu odpovědi|Naslouchací proces, BackendServer, problémových, BackendHttpSetting|
|BackendResponseStatus|Yes|Stav odpovědi back-endu|Počet|Celkem|Počet kódů odpovědí HTTP generovaných členy back-end. Nezahrnuje žádné kódy odpovědí vygenerované Application Gateway.|BackendServer, problémových, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Distribuce pravidla Blokované požadavky brány firewall webových aplikací|Počet|Celkem|Distribuce pravidla Blokované požadavky brány firewall webových aplikací|Rules, RuleId|
|BlockedReqCount|Yes|Počet blokovaných požadavků firewallu webových aplikací|Počet|Celkem|Počet blokovaných požadavků firewallu webových aplikací|Žádné dimenze|
|BytesReceived|Yes|Přijaté bajty|Bajty|Celkem|Celkový počet bajtů přijatých Application Gateway od klientů|Naslouchací proces|
|BytesSent|Yes|Odeslané bajty|Bajty|Celkem|Celkový počet bajtů odeslaných Application Gateway klientům|Naslouchací proces|
|CapacityUnits|No|Aktuální jednotky kapacity|Počet|Průměr|Spotřebované jednotky kapacity|Žádné dimenze|
|ClientRtt|No|Čas odezvy klienta|Milisekund|Průměr|Průměrná doba odezvy mezi klienty a Application Gateway. Tato metrika indikuje, jak dlouho trvá navázání připojení a vrácení potvrzení.|Naslouchací proces|
|ComputeUnits|No|Aktuální výpočetní jednotky|Počet|Průměr|Spotřebované výpočetní jednotky|Žádné dimenze|
|CpuUtilization|No|Využití procesoru|Procento|Průměr|Aktuální využití CPU v Application Gateway|Žádné dimenze|
|CurrentConnections|Yes|Aktuální připojení|Počet|Celkem|Počet aktuálních připojení vytvořených pomocí Application Gateway|Žádné dimenze|
|EstimatedBilledCapacityUnits|No|Odhadované jednotky s rozpisem kapacity|Počet|Průměr|Odhadované jednotky kapacity, které se budou účtovat|Žádné dimenze|
|FailedRequests|Yes|Neúspěšné požadavky|Počet|Celkem|Počet neúspěšných žádostí, které Application Gateway obsluhovány|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Pevné fakturovatelné jednotky kapacity|Počet|Průměr|Minimální jednotky kapacity, které se budou účtovat|Žádné dimenze|
|HealthyHostCount|Yes|Počet hostitelů v pořádku|Počet|Průměr|Počet hostitelů back-endu, kteří jsou v pořádku|BackendSettingsPool|
|MatchedCount|Yes|Distribuce všech pravidel v firewallu webových aplikací|Počet|Celkem|Celková distribuce pravidla v bráně firewall webových aplikací pro příchozí provoz|Rules, RuleId|
|NewConnectionsPerSecond|No|Nová připojení za sekundu|CountPerSecond|Průměr|Nová připojení za sekundu vytvořená Application Gateway|Žádné dimenze|
|ResponseStatus|Yes|Stav odpovědi|Počet|Celkem|Stav odpovědi HTTP vrácený Application Gateway|HttpStatusGroup|
|Propustnost|No|Propustnost|BytesPerSecond|Průměr|Počet bajtů za sekundu, které Application Gateway zasloužily|Žádné dimenze|
|TlsProtocol|Yes|Protokol TLS klienta|Počet|Celkem|Počet požadavků TLS a non-TLS iniciované klientem, který vytvořil spojení s Application Gateway. Chcete-li zobrazit distribuci protokolu TLS, filtrujte podle protokolu TLS (Dimension TLS).|Naslouchací proces, TlsProtocol|
|TotalRequests|Yes|Požadavky celkem|Počet|Celkem|Počet úspěšných požadavků, které Application Gateway obsluhovány|BackendSettingsPool|
|UnhealthyHostCount|Yes|Počet hostitelů není v pořádku|Počet|Průměr|Počet hostitelů back-endu, kteří nejsou v pořádku|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Počet volání pravidel aplikace|Počet|Celkem|Počet přístupů k pravidlům aplikace|Stav, důvod, protokol|
|Zpracováno na zpracování|Yes|Zpracovaná data|Bajty|Celkem|Celkový objem dat zpracovaných touto bránou firewall|Žádné dimenze|
|FirewallHealth|Yes|Stav brány firewall|Procento|Průměr|Označuje celkový stav této brány firewall.|Stav, důvod|
|NetworkRuleHit|Yes|Počet volání síťových pravidel|Počet|Celkem|Počet přístupů k síťovým pravidlům|Stav, důvod, protokol|
|SNATPortUtilization|Yes|Využití portu SNAT|Procento|Průměr|Procento odchozích portů SNAT, které se aktuálně používají|Protokol|
|Propustnost|No|Propustnost|BitsPerSecond|Průměr|Propustnost zpracovaná touto bránou firewall|Žádné dimenze|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|pingmesh|No|Stav komunikace bastionu|Počet|Průměr|Stav komunikace zobrazuje hodnotu 1, pokud je veškerá komunikace dobrá a 0, pokud je její hodnota špatná.||
|rušování|No|Počet relací|Počet|Celkem|Počet relací pro bastionu. Zobrazit v součtu a na instanci.|Hostitel|
|celkem|Yes|Celková paměť|Počet|Průměr|Celkový počet statistik paměti.|Hostitel|
|usage_user|No|Použitý procesor|Počet|Průměr|Statistiky využití procesoru.|procesor, hostitel|
|protokoly|Yes|Využitá paměť|Počet|Průměr|Statistika využití paměti|Hostitel|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Žádné dimenze|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|QueryVolume|No|Svazek dotazu|Počet|Celkem|Počet dotazů poskytovaných pro zónu DNS|Žádné dimenze|
|RecordSetCapacityUtilization|No|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou DNS|Žádné dimenze|
|RecordSetCount|No|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně DNS|Žádné dimenze|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|Dostupnost protokolu ARP|Procento|Průměr|Dostupnost protokolu ARP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|BgpAvailability|Yes|Dostupnost protokolu BGP|Procento|Průměr|Dostupnost protokolu BGP z MSEE směrem ke všem partnerským uzlům.|PeeringType, partner|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Průměr|Počet vyřazených bitů dat za sekundu|Žádné dimenze|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Průměr|Výstupní bity zahozených dat za sekundu|Žádné dimenze|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Žádné dimenze|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Žádné dimenze|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Počet tras inzerovaných pro partnerský vztah (Preview)|Počet|Maximum|Počet tras inzerovaných pro partnerský vztah podle ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Počet tras získaných z partnerského vztahu (Preview)|Počet|Maximum|Počet tras zjištěných z partnerského vztahu podle ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Využití procesoru|Počet|Průměr|Využití procesoru bránou ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Četnost změn tras (Preview)|Počet|Celkem|Četnost změn tras v bráně ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Počet virtuálních počítačů v Virtual Network (Preview)|Počet|Maximum|Počet virtuálních počítačů v Virtual Network|Žádné dimenze|
|ExpressRouteGatewayPacketsPerSecond|No|Pakety za sekundu|CountPerSecond|Průměr|Počet paketů ExpressRoute brány|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Počet|Průměr|Stav Správce portu|Odkaz|
|LineProtocol|Yes|LineProtocol|Počet|Průměr|Stav protokolu linky portu|Odkaz|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Průměr|Počet bitů příchozího přenosu dat Azure za sekundu|Odkaz|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Průměr|Počet bitů výchozího přenosu dat Azure za sekundu|Odkaz|
|RxLightLevel|Yes|RxLightLevel|Počet|Průměr|Úroveň světla příjmu v dBm|Odkaz, Lane|
|TxLightLevel|Yes|TxLightLevel|Počet|Průměr|Úroveň Light tx v dBm|Odkaz, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Procento stavu back-endu|Procento|Průměr|Procento úspěšných sond stavu z proxy serveru HTTP/S do back-endu|Back-end, problémových|
|BackendRequestCount|Yes|Počet požadavků back-endu|Počet|Celkem|Počet požadavků odeslaných z proxy serveru HTTP/S do back-endu|Stavu protokolu HTTP, HttpStatusGroup, back-end|
|BackendRequestLatency|Yes|Latence žádosti back-endu|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek odeslán proxy HTTP/S do back-endu do back-endu, dokud proxy protokolu HTTP/S nedostalo poslední bajt odpovědi z back-endu|Back-end|
|BillableResponseSize|Yes|Fakturovatelná velikost odpovědi|Bajty|Celkem|Počet fakturovatelných bajtů (minimálně 2 KB na požadavek) odeslaných jako odpověď z proxy serveru HTTP/S na klienty.|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Počet požadavků|Počet|Celkem|Počet požadavků klientů obsluhovaných proxy HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Velikost požadavku|Bajty|Celkem|Počet bajtů odeslaných jako požadavek od klientů na proxy server HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Velikost odpovědi|Bajty|Celkem|Počet bajtů odeslaných jako odpověď z proxy serveru HTTP/S na klienty|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Celková latence|Milisekund|Průměr|Čas vypočítaný z doby, kdy byl požadavek klienta přijat serverem HTTP/S, dokud klient nepotvrdí poslední bajt odpovědi z proxy serveru HTTP/S|Stavu protokolu HTTP, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Počet požadavků firewallu webových aplikací|Počet|Celkem|Počet požadavků klientů zpracovaných bránou firewall webových aplikací|Policy, Rule, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Přidělené porty SNAT|Počet|Průměr|Celkový počet portů SNAT přidělených v rámci časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, |
|ByteCount|Yes|Počet bajtů|Bajty|Celkem|Celkový počet odeslaných bajtů v časovém období|FrontendIPAddress, FrontendPort, směr|
|DipAvailability|Yes|Stav sondy stavu|Počet|Průměr|Průměrný stav testu stavu Load Balancer za dobu trvání|Typprotokolu, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|FrontendIPAddress, FrontendPort, směr|
|SnatConnectionCount|Yes|Počet připojení SNAT|Počet|Celkem|Celkový počet nových připojení SNAT vytvořených během časového období|FrontendIPAddress, BackendIPAddress, vlastnost ConnectionState|
|SYNCount|Yes|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|FrontendIPAddress, FrontendPort, směr|
|UsedSnatPorts|No|Používané porty SNAT|Počet|Průměr|Celkový počet portů SNAT používaných během časového období|FrontendIPAddress, BackendIPAddress, Typprotokolu, |
|VipAvailability|Yes|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost cesty k datům Load Balancer za dobu trvání|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/natGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bajty|Bajty|Celkem|Celkový počet odeslaných bajtů v časovém období|Protokol, směr|
|DatapathAvailability|Yes|Dostupnost DataPath (Preview)|Počet|Průměr|Dostupnost DataPath brány NAT|Žádné dimenze|
|PacketCount|Yes|Rámců|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|Protokol, směr|
|PacketDropCount|Yes|Vyřazené pakety|Počet|Celkem|Počet vyřazených paketů|Žádné dimenze|
|SNATConnectionCount|Yes|Počet připojení SNAT|Počet|Celkem|Celkem souběžných aktivních připojení|Protokol, vlastnost ConnectionState|
|TotalConnectionCount|Yes|Celkový počet připojení SNAT|Počet|Celkem|Celkový počet aktivních připojení SNAT|Protokol|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Přijaté bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní přijalo|Žádné dimenze|
|BytesSentRate|Yes|Odeslané bajty|Bajty|Celkem|Počet bajtů, které síťové rozhraní odeslalo|Žádné dimenze|
|PacketsReceivedRate|Yes|Přijaté pakety|Počet|Celkem|Počet paketů, které síťové rozhraní přijalo|Žádné dimenze|
|PacketsSentRate|Yes|Odeslané pakety|Počet|Celkem|Počet paketů, které síťové rozhraní odeslalo|Žádné dimenze|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Průměrná doba odezvy (MS) (klasická)|Milisekund|Průměr|Průměrná doba odezvy sítě (MS) pro testy monitorování připojení odesílané mezi zdrojem a cílem|Žádné dimenze|
|ChecksFailedPercent|Yes|Neúspěšné kontroly v procentech|Procento|Průměr|% kontrol monitorování připojení selhalo.|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% PROBE selhalo (klasické)|Procento|Průměr|% sond monitorování připojení selhalo.|Žádné dimenze|
|RoundTripTimeMs|Yes|Round-Trip čas (MS)|Milisekund|Průměr|Doba odezvy v milisekundách pro kontroly monitorování připojení|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, Destination, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Výsledek testu|Počet|Průměr|Výsledek testu monitorování připojení|SourceAddress, source, parametr sourceresourceid, SourceType, protokol, DestinationAddress, cílový typ, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Šířka pásma P2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Yes|Počet připojení P2S|Počet|Celkem|Počet připojení typu point-to-site brány|Protokol|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Svazek dotazu|Počet|Celkem|Počet dotazů poskytovaných pro zónu Privátní DNS|Žádné dimenze|
|RecordSetCapacityUtilization|No|Využití kapacity sady záznamů|Procento|Maximum|Procento kapacity sady záznamů využité zónou Privátní DNS|Žádné dimenze|
|RecordSetCount|Yes|Počet sad záznamů|Počet|Maximum|Počet sad záznamů v zóně Privátní DNS|Žádné dimenze|
|VirtualNetworkLinkCapacityUtilization|No|Využití kapacity propojení Virtual Network|Procento|Maximum|Procento kapacity propojení Virtual Network využité zónou Privátní DNS|Žádné dimenze|
|VirtualNetworkLinkCount|Yes|Virtual Network počet odkazů|Počet|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS|Žádné dimenze|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Využití kapacity odkazu na registraci Virtual Network|Procento|Maximum|Procento Virtual Network propojení s kapacitou automatického registrace využívané Privátní DNS zónou|Žádné dimenze|
|VirtualNetworkWithRegistrationLinkCount|Yes|Počet odkazů na registraci Virtual Network|Počet|Maximum|Počet virtuálních sítí propojených se zónou Privátní DNS s povolenou automatickou registrací|Žádné dimenze|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Počet bajtů v|Počet|Celkem|Celkový počet odeslaných bajtů|PrivateEndpointId|
|PEBytesOut|Yes|Odchozí bajty|Počet|Celkem|Celkový počet odeslaných bajtů|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft. Network/privateLinkServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Počet bajtů v|Počet|Celkem|Celkový počet odeslaných bajtů|PrivateLinkServiceId|
|PLSBytesOut|Yes|Odchozí bajty|Počet|Celkem|Celkový počet odeslaných bajtů|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Využití portů NAT|Procento|Průměr|Využití portů NAT|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Počet bajtů|Bajty|Celkem|Celkový počet odeslaných bajtů v časovém období|Port, směr|
|BytesDroppedDDoS|Yes|Příchozí bajty vynechané DDoS|BytesPerSecond|Maximum|Příchozí bajty vynechané DDoS|Žádné dimenze|
|BytesForwardedDDoS|Yes|Příchozí bajty předané DDoS|BytesPerSecond|Maximum|Příchozí bajty předané DDoS|Žádné dimenze|
|BytesInDDoS|Yes|Příchozí bajty DDoS|BytesPerSecond|Maximum|Příchozí bajty DDoS|Žádné dimenze|
|DDoSTriggerSYNPackets|Yes|Příchozí pakety SYN pro aktivaci omezení rizik útoku DDoS|CountPerSecond|Maximum|Příchozí pakety SYN pro aktivaci omezení rizik útoku DDoS|Žádné dimenze|
|DDoSTriggerTCPPackets|Yes|Příchozí pakety TCP pro aktivaci omezení rizik útoku DDoS|CountPerSecond|Maximum|Příchozí pakety TCP pro aktivaci omezení rizik útoku DDoS|Žádné dimenze|
|DDoSTriggerUDPPackets|Yes|Příchozí pakety UDP pro aktivaci omezení rizik útoku DDoS|CountPerSecond|Maximum|Příchozí pakety UDP pro aktivaci omezení rizik útoku DDoS|Žádné dimenze|
|IfUnderDDoSAttack|Yes|V části útok DDoS nebo ne|Počet|Maximum|V části útok DDoS nebo ne|Žádné dimenze|
|PacketCount|Yes|Počet paketů|Počet|Celkem|Celkový počet odeslaných paketů v časovém období|Port, směr|
|PacketsDroppedDDoS|Yes|Vynechané příchozí pakety DDoS|CountPerSecond|Maximum|Vynechané příchozí pakety DDoS|Žádné dimenze|
|PacketsForwardedDDoS|Yes|DDoS předaných příchozích paketů|CountPerSecond|Maximum|DDoS předaných příchozích paketů|Žádné dimenze|
|PacketsInDDoS|Yes|DDoS příchozích paketů|CountPerSecond|Maximum|DDoS příchozích paketů|Žádné dimenze|
|SynCount|Yes|Počet SYN|Počet|Celkem|Celkový počet odeslaných paketů SYN v časovém období|Port, směr|
|TCPBytesDroppedDDoS|Yes|Příchozí bajty protokolu TCP vyhozené DDoS|BytesPerSecond|Maximum|Příchozí bajty protokolu TCP vyhozené DDoS|Žádné dimenze|
|TCPBytesForwardedDDoS|Yes|Příchozí DDoS předaných bajtů protokolu TCP|BytesPerSecond|Maximum|Příchozí DDoS předaných bajtů protokolu TCP|Žádné dimenze|
|TCPBytesInDDoS|Yes|Příchozí bajty DDoS TCP|BytesPerSecond|Maximum|Příchozí bajty DDoS TCP|Žádné dimenze|
|TCPPacketsDroppedDDoS|Yes|Zrušené příchozí pakety protokolu TCP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety protokolu TCP DDoS|Žádné dimenze|
|TCPPacketsForwardedDDoS|Yes|DDoS předaných paketů příchozího protokolu TCP|CountPerSecond|Maximum|DDoS předaných paketů příchozího protokolu TCP|Žádné dimenze|
|TCPPacketsInDDoS|Yes|DDoS příchozí pakety TCP|CountPerSecond|Maximum|DDoS příchozí pakety TCP|Žádné dimenze|
|UDPBytesDroppedDDoS|Yes|Zrušené Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Zrušené Příchozí bajty UDP DDoS|Žádné dimenze|
|UDPBytesForwardedDDoS|Yes|Příchozí DDoS předávaných bajtů UDP|BytesPerSecond|Maximum|Příchozí DDoS předávaných bajtů UDP|Žádné dimenze|
|UDPBytesInDDoS|Yes|Příchozí bajty UDP DDoS|BytesPerSecond|Maximum|Příchozí bajty UDP DDoS|Žádné dimenze|
|UDPPacketsDroppedDDoS|Yes|Zrušené příchozí pakety UDP DDoS|CountPerSecond|Maximum|Zrušené příchozí pakety UDP DDoS|Žádné dimenze|
|UDPPacketsForwardedDDoS|Yes|Příchozí pakety UDP předané DDoS|CountPerSecond|Maximum|Příchozí pakety UDP předané DDoS|Žádné dimenze|
|UDPPacketsInDDoS|Yes|Příchozí pakety UDP DDoS|CountPerSecond|Maximum|Příchozí pakety UDP DDoS|Žádné dimenze|
|VipAvailability|Yes|Dostupnost cesty k datům|Počet|Průměr|Průměrná dostupnost IP adresy za dobu trvání|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Stav koncového bodu podle koncového bodu|Počet|Maximum|1, pokud je stav testu koncového bodu zapnuto, 0 jinak.|Koncový bod|
|QpsByEndpoint|Yes|Dotazy podle vráceného koncového bodu|Počet|Celkem|Počet vrácených Traffic Managerho koncového bodu v daném časovém rámci|Koncový bod|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Šířka pásma S2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádné dimenze|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Počet tras inzerovaných pro partnerský vztah (Preview)|Počet|Maximum|Počet tras inzerovaných pro partnerský vztah podle ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Počet tras získaných z partnerského vztahu (Preview)|Počet|Maximum|Počet tras zjištěných z partnerského vztahu podle ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Využití procesoru|Počet|Průměr|Využití procesoru bránou ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Četnost změn tras (Preview)|Počet|Celkem|Četnost změn tras v bráně ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Počet virtuálních počítačů v Virtual Network (Preview)|Počet|Maximum|Počet virtuálních počítačů v Virtual Network|Žádné dimenze|
|ExpressRouteGatewayPacketsPerSecond|No|Pakety za sekundu|CountPerSecond|Průměr|Počet paketů ExpressRoute brány|roleInstance|
|P2SBandwidth|Yes|Šířka pásma P2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu|Žádné dimenze|
|P2SConnectionCount|Yes|Počet připojení P2S|Počet|Maximum|Počet připojení typu point-to-site brány|Protokol|
|TunnelAverageBandwidth|Yes|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Počet bajtů výchozího přenosu dat tunelu|Bajty|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Počet zahození paketů výchozího přenosu dat tunelu kvůli neshodě SP|Počet|Celkem|Počet zahození odchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Počet paketů výchozího přenosu dat tunelu|Počet|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Počet bajtů příchozího přenosu dat tunelu|Bajty|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Počet zahození paketů příchozího přenosu dat tunelu kvůli neshodě SP|Počet|Celkem|Počet zahození příchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Tunelové pakety pro příchozí připojení|Počet|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|Přidělení tunelového překladu adres (NAT)|Počet|Celkem|Počet přidělení pro pravidlo překladu adres (NAT) na tunelu|– Natrule, connectionName, RemoteIP|
|TunnelNatedBytes|No|Tunelové NATed bajty|Bajty|Celkem|Počet bajtů, které se NATed na tunelu pomocí pravidla překladu adres (NAT) |– Natrule, connectionName, RemoteIP|
|TunnelNatedPackets|No|Tunelové pakety NATed|Počet|Celkem|Počet paketů, které se NATed na tunelu pomocí pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP|
|TunnelNatFlowCount|No|Toky tunelového překladu NAT|Počet|Celkem|Počet toků NAT v tunelu podle typu toku a pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Vyčerpání paketů NAT tunelu|Počet|Celkem|Počet paketů NATed v tunelu, který je vyřazený pomocí typu drop a pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|NATed bajty zpětného propojení|Bajty|Celkem|Počet bajtů, které se NATed pomocí pravidla překladu adres (NAT) zpět na tunel|– Natrule, connectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Tunelové reverzní NATed pakety|Počet|Celkem|Počet paketů v tunelu, které byly reverzní NATed pravidlem překladu adres (NAT)|– Natrule, connectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Čas odezvy na virtuální počítač pomocí příkazů pro odeslání|Milisekund|Průměr|Čas odezvy pro příkazy pro odeslání do cílového virtuálního počítače|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Nepovedlo se odeslat příkazy do virtuálního počítače|Procento|Průměr|Procento počtu neúspěšných příkazů pro odeslání z testu na celkový počet odeslaných příkazů VMM cílového virtuálního počítače|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/virtualRouters

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PeeringAvailability|Yes|Dostupnost protokolu BGP|Procento|Průměr|Dostupnost protokolu BGP mezi VirtualRouter a vzdálenými partnery|Partnerská|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Šířka pásma S2S brány|BytesPerSecond|Průměr|Průměrná šířka pásma brány mezi lokalitami v bajtech za sekundu|Žádné dimenze|
|TunnelAverageBandwidth|Yes|Šířka pásma tunelu|BytesPerSecond|Průměr|Průměrná šířka pásma tunelu v bajtech za sekundu|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Počet bajtů výchozího přenosu dat tunelu|Bajty|Celkem|Odchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Počet zahození paketů výchozího přenosu dat tunelu kvůli neshodě SP|Počet|Celkem|Počet zahození odchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Počet paketů výchozího přenosu dat tunelu|Počet|Celkem|Počet odchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Počet bajtů příchozího přenosu dat tunelu|Bajty|Celkem|Příchozí bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Počet zahození paketů příchozího přenosu dat tunelu kvůli neshodě SP|Počet|Celkem|Počet zahození příchozích paketů tunelu kvůli neshodě selektoru provozu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Tunelové pakety pro příchozí připojení|Počet|Celkem|Počet příchozích paketů tunelu|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|Přidělení tunelového překladu adres (NAT)|Počet|Celkem|Počet přidělení pro pravidlo překladu adres (NAT) na tunelu|– Natrule, connectionName, RemoteIP|
|TunnelNatedBytes|No|Tunelové NATed bajty|Bajty|Celkem|Počet bajtů, které se NATed na tunelu pomocí pravidla překladu adres (NAT) |– Natrule, connectionName, RemoteIP|
|TunnelNatedPackets|No|Tunelové pakety NATed|Počet|Celkem|Počet paketů, které se NATed na tunelu pomocí pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP|
|TunnelNatFlowCount|No|Toky tunelového překladu NAT|Počet|Celkem|Počet toků NAT v tunelu podle typu toku a pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Vyčerpání paketů NAT tunelu|Počet|Celkem|Počet paketů NATed v tunelu, který je vyřazený pomocí typu drop a pravidla překladu adres (NAT)|– Natrule, connectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|NATed bajty zpětného propojení|Bajty|Celkem|Počet bajtů, které se NATed pomocí pravidla překladu adres (NAT) zpět na tunel|– Natrule, connectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Tunelové reverzní NATed pakety|Počet|Celkem|Počet paketů v tunelu, které byly reverzní NATed pravidlem překladu adres (NAT)|– Natrule, connectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/obory názvů/NotificationHubs

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|přijíman|Yes|Příchozí zprávy|Počet|Celkem|Počet všech úspěšných volání rozhraní API pro odeslání. |Žádné dimenze|
|příchozí. All. failedrequests|Yes|Všechny příchozí neúspěšné požadavky|Počet|Celkem|Celkový počet příchozích neúspěšných žádostí pro Centrum oznámení|Žádné dimenze|
|příchozí. All. – žádosti|Yes|Všechny příchozí žádosti|Počet|Celkem|Celkový počet příchozích požadavků pro Centrum oznámení|Žádné dimenze|
|příchozí. plánováno|Yes|Odeslaná naplánovaná nabízená oznámení|Počet|Celkem|Zrušená plánovaná nabízená oznámení|Žádné dimenze|
|příchozí. plánováno. Cancel|Yes|Zrušená plánovaná nabízená oznámení|Počet|Celkem|Zrušená plánovaná nabízená oznámení|Žádné dimenze|
|instalace. vše|Yes|Operace správy instalace|Počet|Celkem|Operace správy instalace|Žádné dimenze|
|instalace. odstranit|Yes|Odstranit operace instalace|Počet|Celkem|Odstranit operace instalace|Žádné dimenze|
|instalace. Get|Yes|Získat operace instalace|Počet|Celkem|Získat operace instalace|Žádné dimenze|
|instalace. patch|Yes|Operace instalace opravy|Počet|Celkem|Operace instalace opravy|Žádné dimenze|
|instalace. Upsert|Yes|Operace vytvoření nebo aktualizace instalace|Počet|Celkem|Operace vytvoření nebo aktualizace instalace|Žádné dimenze|
|notificationhub. push|Yes|Všechna odchozí oznámení|Počet|Celkem|Všechna odchozí oznámení centra oznámení|Žádné dimenze|
|odchozí. allpns. badorexpiredchannel|Yes|Chybné nebo prošlé chyby kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože v registraci vypršela platnost kanálu/tokenu nebo registrationId, nebo je neplatná.|Žádné dimenze|
|odchozí. allpns. channelerror|Yes|Chyby kanálu|Počet|Celkem|Počet nabízených oznámení, která selhala, protože kanál byl neplatný, ale není přidružený ke správné aplikaci nebo vypršela její platnost.|Žádné dimenze|
|odchozí. allpns. invalidpayload|Yes|Chyby datové části|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS vrátil chybnou datovou část.|Žádné dimenze|
|odchozí. allpns. pnserror|Yes|Chyby systému externích oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože došlo k potížím při komunikaci s PNS (nezahrnuje problémy s ověřováním).|Žádné dimenze|
|odchozí. allpns. úspěch|Yes|Úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. APNs. badchannel|Yes|Chyba služby APN Bad Channel|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože token je neplatný (Stavový kód služby APN: 8).|Žádné dimenze|
|odchozí. APNs. expiredchannel|Yes|Chyba kanálu vypršení platnosti služby APNS|Počet|Celkem|Počet tokenů, u kterých se zrušila platnost kanálu zpětné vazby APNS.|Žádné dimenze|
|odchozí. APNs. invalidcredentials|Yes|Chyby autorizace APNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. APNs. invalidnotificationsize|Yes|APN – Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (Stavový kód APNS: 7).|Žádné dimenze|
|odchozí. APNs. pnserror|Yes|Chyby služby APN|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s APNS.|Žádné dimenze|
|odchozí. APNs. Success|Yes|Úspěšná oznámení APNS|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. GCM. authenticationerror|Yes|GCM chyby ověřování|Počet|Celkem|Počet nabízených oznámení, která selhala, protože PNS nepřijaly zadané přihlašovací údaje, jsou přihlašovací údaje blokované nebo SenderId není v aplikaci správně nakonfigurovaný (výsledek GCM: MismatchedSenderId).|Žádné dimenze|
|odchozí. GCM. badchannel|Yes|Chyba GCM špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci nebyl rozpoznán (výsledek GCM: Neplatná registrace).|Žádné dimenze|
|odchozí. GCM. expiredchannel|Yes|Chyba kanálu vypršení platnosti GCM|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože vypršela platnost registrationId v registraci (výsledek GCM: NotRegistered).|Žádné dimenze|
|odchozí. GCM. invalidcredentials|Yes|GCM – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. GCM. invalidnotificationformat|Yes|GCM neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část nebyla správně naformátována (výsledek GCM: InvalidDataKey nebo InvalidTtl).|Žádné dimenze|
|odchozí. GCM. invalidnotificationsize|Yes|GCMá Chyba neplatné velikosti oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část byla příliš velká (výsledek GCM: MessageTooBig).|Žádné dimenze|
|odchozí. GCM. pnserror|Yes|GCM chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s GCM|Žádné dimenze|
|odchozí. GCM. úspěch|Yes|GCM úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. GCM. omezení|Yes|GCM omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože aplikace GCM omezila tuto aplikaci (kód stavu GCM: 501-599 nebo výsledek: nedostupný)|Žádné dimenze|
|odchozí. GCM. wrongchannel|Yes|GCM chybná chyba kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože registrationId v registraci není přidružená k aktuální aplikaci (výsledek GCM: InvalidPackageName).|Žádné dimenze|
|odchozí. MPNS. authenticationerror|Yes|MPNS chyby ověřování|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. MPNS. badchannel|Yes|Chyba MPNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav MPNS: 404 nebyl nalezen).|Žádné dimenze|
|odchozí. MPNS. channeldisconnected|Yes|MPNS kanál odpojen|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci byl odpojen (stav MPNS: 412 nebyl nalezen).|Žádné dimenze|
|odchozí. MPNS. vyřazeno|Yes|MPNS Vyřazená oznámení|Počet|Celkem|Počet nabídek, které byly vyřazeny pomocí MPNS (hlavička odpovědi MPNS: X-NotificationStatus: QueueFull nebo potlačeno).|Žádné dimenze|
|odchozí. MPNS. invalidcredentials|Yes|Neplatné přihlašovací údaje MPNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované.|Žádné dimenze|
|odchozí. MPNS. invalidnotificationformat|Yes|MPNS neplatný formát oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože datová část oznámení byla příliš velká.|Žádné dimenze|
|odchozí. MPNS. pnserror|Yes|MPNS chyby|Počet|Celkem|Počet nabídek, které nebyly úspěšné kvůli chybám při komunikaci s MPNS|Žádné dimenze|
|odchozí. MPNS. úspěch|Yes|MPNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. MPNS. omezení|Yes|MPNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože MPNS omezuje tuto aplikaci (WNS MPNS: 406 není přijatelné).|Žádné dimenze|
|odchozí. WNS. authenticationerror|Yes|WNS chyby ověřování|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s Windows Live neplatné přihlašovací údaje nebo chybný token.|Žádné dimenze|
|odchozí. WNS. badchannel|Yes|Chyba WNS špatného kanálu|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože parametr channeluri v registraci nebyl rozpoznán (stav WNS: 404 nebyl nalezen).|Žádné dimenze|
|odchozí. WNS. channeldisconnected|Yes|WNS kanál odpojen|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|odchozí. WNS. channelthrottled|Yes|WNS kanál – omezení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (hlavička odpovědi WNS: X-WNS-NotificationStatus: channelThrottled).|Žádné dimenze|
|odchozí. WNS. vyřazeno|Yes|WNS Vyřazená oznámení|Počet|Celkem|Oznámení bylo vyřazeno, protože parametr channeluri v registraci je omezené (X-WNS-NotificationStatus: vyřazeno, ale ne X-WNS-DeviceConnectionStatus: odpojeno).|Žádné dimenze|
|odchozí. WNS. expiredchannel|Yes|Chyba kanálu vypršení platnosti WNS|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože platnost parametr channeluri vypršela (stav WNS: 410 pryč).|Žádné dimenze|
|odchozí. WNS. invalidcredentials|Yes|WNS – chyby autorizace (neplatné přihlašovací údaje)|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože PNS nepřijal zadané přihlašovací údaje nebo jsou přihlašovací údaje blokované. (Windows Live nerozpoznává přihlašovací údaje.)|Žádné dimenze|
|odchozí. WNS. invalidnotificationformat|Yes|WNS neplatný formát oznámení|Počet|Celkem|Formát oznámení je neplatný (stav WNS: 400). Všimněte si, že WNS neumožňuje odmítat všechny neplatné datové části.|Žádné dimenze|
|odchozí. WNS. invalidnotificationsize|Yes|WNSá Chyba neplatné velikosti oznámení|Počet|Celkem|Datová část oznámení je příliš velká (stav WNS: 413).|Žádné dimenze|
|odchozí. WNS. invalidtoken|Yes|WNS – chyby autorizace (neplatný token)|Počet|Celkem|Token poskytnutý pro WNS není platný (stav WNS: 401 Neautorizováno).|Žádné dimenze|
|odchozí. WNS. pnserror|Yes|WNS chyby|Počet|Celkem|Oznámení nebylo doručeno kvůli chybám při komunikaci s WNS.|Žádné dimenze|
|odchozí. WNS. úspěch|Yes|WNS úspěšná oznámení|Počet|Celkem|Počet všech úspěšných oznámení|Žádné dimenze|
|odchozí. WNS. omezení|Yes|WNS omezená oznámení|Počet|Celkem|Počet nabídek, které nebyly úspěšné, protože WNS omezuje tuto aplikaci (stav WNS: 406 není přijatelné).|Žádné dimenze|
|odchozí. WNS. tokenproviderunreachable|Yes|WNS – chyby autorizace (nedostupné)|Počet|Celkem|Systém Windows Live není dostupný.|Žádné dimenze|
|odchozí. WNS. wrongtoken|Yes|WNS chyby autorizace (špatný token)|Počet|Celkem|Token poskytnutý pro WNS je platný, ale pro jinou aplikaci (stav WNS: 403 zakázaný). K tomu může dojít, pokud je parametr channeluri v registraci přidružen k jiné aplikaci. Ověřte, že je klientská aplikace přidružená ke stejné aplikaci, jejíž přihlašovací údaje jsou v centru oznámení.|Žádné dimenze|
|registrace. vše|Yes|Operace registrace|Počet|Celkem|Počet všech úspěšných operací registrace (vytváření dotazů a odstraňování aktualizací). |Žádné dimenze|
|registrace. Create|Yes|Operace vytvoření registrace|Počet|Celkem|Počet všech úspěšných vytvoření registrace.|Žádné dimenze|
|registrace. odstranění|Yes|Registrace – operace odstranění|Počet|Celkem|Počet všech úspěšných odstranění registrací.|Žádné dimenze|
|registrace. Get|Yes|Registrace – operace čtení|Počet|Celkem|Počet všech úspěšných dotazů na registraci.|Žádné dimenze|
|registrace. aktualizace|Yes|Operace aktualizace registrace|Počet|Celkem|Počet všech úspěšných aktualizací registrace.|Žádné dimenze|
|naplánováno. čeká na|Yes|Nedokončená plánovaná oznámení|Počet|Celkem|Nedokončená plánovaná oznámení|Žádné dimenze|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Average_ k dispozici paměti|Yes|% Dostupné paměti|Počet|Průměr|Average_ k dispozici paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostupného odkládacího prostoru|Yes|% Dostupného odkládacího prostoru|Počet|Průměr|Average_% dostupného odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% používaných potvrzených bajtů|Yes|% Používaných potvrzených bajtů|Počet|Průměr|Average_% používaných potvrzených bajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času DPC|Yes|% Času DPC|Počet|Průměr|Average_% času DPC|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ uzlů inode% Free|Yes|% Bezplatného uzlů inode|Počet|Průměr|Average_ uzlů inode% Free|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% volného místa|Yes|% Volného místa|Počet|Průměr|Average_% volného místa|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času nečinnosti|Yes|% Času nečinnosti|Počet|Průměr|Average_% času nečinnosti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času přerušení|Yes|% Času přerušení|Počet|Průměr|Average_% času přerušení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času čekání na v/v|Yes|% Času čekání na v/v|Počet|Průměr|Average_% času čekání na v/v|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dobrý čas|Yes|% Dobrý čas|Počet|Průměr|Average_% dobrý čas|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegovaného času|Yes|% Privilegovaného času|Počet|Průměr|Average_% privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% času procesoru|Yes|Procesorový čas v %|Počet|Průměr|Average_% času procesoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využití uzlů inode|Yes|% Použitého uzlů inode|Počet|Průměr|Average_% využití uzlů inode|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využitá paměť Average_%|Yes|% Využité paměti|Počet|Průměr|Využitá paměť Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Využité místo Average_%|Yes|% Využitého místa|Počet|Průměr|Využité místo Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% využitého místa odkládacího souboru|Yes|% Využitého místa odkládacího souboru|Počet|Průměr|Average_% využitého místa odkládacího souboru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_%|Yes|% Uživatelského času|Počet|Průměr|Čas uživatele Average_%|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Yes|Dostupná paměť v megabajtech|Počet|Průměr|Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Paměť Average_Available v MB|Yes|Dostupná paměť v MB|Počet|Průměr|Paměť Average_Available v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přeměna Average_Available MB|Yes|Dostupný počet MB swap|Počet|Průměr|Přeměna Average_Available MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/čtení|Yes|Střední doba disku/čtení|Počet|Průměr|Average_Avg. doba disku/čtení|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Doba disku/přenos|Yes|Střední doba disku/přenos|Počet|Průměr|Average_Avg. Doba disku/přenos|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. doba disku/zápis|Yes|Střední doba disku/zápis|Počet|Průměr|Average_Avg. doba disku/zápis|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté Average_Bytes za sekundu|Yes|Přijaté bajty/s|Počet|Průměr|Přijaté Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Bytes za sekundu|Yes|Odeslané bajty/s|Počet|Průměr|Odeslané Average_Bytes za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes celkem/s|Yes|Bajty celkem/s|Počet|Průměr|Average_Bytes celkem/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty disku Average_Current|Yes|Aktuální délka fronty disku|Počet|Průměr|Délka fronty disku Average_Current|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přečtených bajtů/s|Yes|Bajty čtení z disku/s|Počet|Průměr|Average_Disk přečtených bajtů/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk čtení za sekundu|Yes|Čtení z disku/s|Počet|Průměr|Average_Disk čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk přenosů za sekundu|Yes|Přenosy disku/s|Počet|Průměr|Average_Disk přenosů za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bajty zápisu za sekundu|Yes|Bajty zápisu na disk/s|Počet|Průměr|Average_Disk bajty zápisu za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Disk za sekundu|Yes|Zápisy na disk/s|Počet|Průměr|Zápisy Average_Disk za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtů|Yes|Volné megabajty|Počet|Průměr|Average_Free megabajtů|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fyzická paměť|Yes|Volná fyzická paměť|Počet|Průměr|Average_Free fyzická paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free mezera v stránkovacích souborech|Yes|Volné místo ve stránkovacích souborech|Počet|Průměr|Average_Free mezera v stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuální paměti|Yes|Volná virtuální paměť|Počet|Průměr|Average_Free virtuální paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Logical disku za sekundu|Yes|Bajty logického disku/s|Počet|Průměr|Počet bajtů Average_Logical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page čtení za sekundu|Yes|Čtení stránek/s|Počet|Průměr|Average_Page čtení za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zápisy Average_Page za sekundu|Yes|Zápisy stránek/s|Počet|Průměr|Zápisy Average_Page za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Yes|Stránky/s|Počet|Průměr|Average_Pages/s|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegovaného času|Yes|Privilegovaný čas protokolu PCT|Počet|Průměr|Average_Pct privilegovaného času|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Čas uživatele Average_Pct|Yes|Doba uživatele v protokolu PCT|Počet|Průměr|Čas uživatele Average_Pct|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Počet bajtů Average_Physical disku za sekundu|Yes|Bajty fyzického disku/s|Počet|Průměr|Počet bajtů Average_Physical disku za sekundu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Procesy|Počet|Průměr|Average_Processes|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Délka fronty Average_Processor|Yes|Délka fronty procesoru|Počet|Průměr|Délka fronty Average_Processor|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size uložený ve stránkovacích souborech|Yes|Velikost uložená ve stránkovacích souborech|Počet|Průměr|Average_Size uložený ve stránkovacích souborech|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Yes|Bajty celkem|Počet|Průměr|Average_Total bajty|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté bajty Average_Total|Yes|Celkový počet přijatých bajtů|Počet|Průměr|Přijaté bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přenesené bajty Average_Total|Yes|Celkový počet odeslaných bajtů|Počet|Průměr|Přenesené bajty Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizí|Yes|Celkový počet kolizí|Počet|Průměr|Average_Total kolizí|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Přijaté pakety Average_Total|Yes|Celkový počet přijatých paketů|Počet|Průměr|Přijaté pakety Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odeslané Average_Total pakety|Yes|Celkový počet odeslaných paketů|Počet|Průměr|Odeslané Average_Total pakety|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total chyby příjmu|Yes|Celkový počet chyb příjmu|Počet|Průměr|Average_Total chyby příjmu|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Chyby při odesílání Average_Total|Yes|Chyby odesílání celkem|Počet|Průměr|Chyby při odesílání Average_Total|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Doba provozu|Počet|Průměr|Average_Uptime|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB odkládacího prostoru|Yes|Využité místo odkládacího souboru v MB|Počet|Průměr|Average_Used MB odkládacího prostoru|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used kB paměti|Yes|Využitá paměť v kilobajtech|Počet|Průměr|Average_Used kB paměti|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used paměť v MB|Yes|Využitá paměť v MB|Počet|Průměr|Average_Used paměť v MB|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Uživatelé|Počet|Průměr|Average_Users|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual sdílená paměť|Yes|Virtuální sdílená paměť|Počet|Průměr|Average_Virtual sdílená paměť|Počítač, ObjectName, InstanceName, CounterPath, SourceSystem|
|Událost|Yes|Událost|Počet|Průměr|Událost|Zdroj, protokol událostí, počítač, EventCategory, EventLevel, EventLevelName, ID události|
|Tep|Yes|Tep|Počet|Celkem|Tep|Počítač, OSType, verze, SourceComputerId|
|Aktualizace|Yes|Aktualizace|Počet|Průměr|Aktualizace|Počítač, produkt, klasifikace, UpdateState, volitelné, schválené|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Rychlost přenosů na výstup|BitsPerSecond|Průměr|Rychlost přenosů dat v bitech za sekundu|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Rychlost příchozího přenosu dat|BitsPerSecond|Průměr|Míra přenosů příchozích dat v bitech za sekundu|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Yes|Dostupnost relace|Počet|Průměr|Dostupnost relace partnerského vztahu|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Latence předpony|Milisekund|Průměr|Střední latence předpony|Předpona|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Paměť (Gen1)|Bajty|Průměr|Memory: Rozsah 0-3 GB pro a1, 0-5 GB pro a2, 0-10 GB pro a3, 0-25 GB pro A4, 0-50 GB pro A5 a 0-100 GB pro A6. Podporováno pouze pro prostředky Power BI Embedded generace 1.|Žádné dimenze|
|memory_thrashing_metric|Yes|Paměť thrashing (datové sady) (Gen1)|Procento|Průměr|Průměrná velikost thrashing paměti Podporováno pouze pro prostředky Power BI Embedded generace 1.|Žádné dimenze|
|qpu_high_utilization_metric|Yes|Vysoké využití QPU (Gen1)|Počet|Celkem|QPU vysoké využití za poslední minutu, 1 pro vysoké využití QPU, jinak 0. Podporováno pouze pro prostředky Power BI Embedded generace 1.|Žádné dimenze|
|QueryDuration|Yes|Doba trvání dotazu (datové sady) (Gen1)|Milisekund|Průměr|Doba trvání dotazu DAX v posledním intervalu Podporováno pouze pro prostředky Power BI Embedded generace 1.|Žádné dimenze|
|QueryPoolJobQueueLength|Yes|Délka fronty úloh fondu dotazů (datové sady) (Gen1)|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazů. Podporováno pouze pro prostředky Power BI Embedded generace 1.|Žádné dimenze|


## <a name="microsoftpurviewaccounts"></a>Microsoft. dosah/účty

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Kontrola zrušena|Počet|Celkem|Určuje počet zrušených kontrol.||
|ScanCompleted|Yes|Kontrola dokončena|Počet|Celkem|Určuje počet kontrol, které byly úspěšně dokončeny.||
|ScanFailed|Yes|Kontrola se nezdařila|Počet|Celkem|Indikuje, že počet kontrol se nezdařil.||
|ScanTimeTaken|Yes|Doba kontroly provedena|Sekundy|Celkem|Určuje celkovou dobu kontroly v sekundách.||


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Počet|Celkem|Total ActiveConnections for Microsoft. Relay|EntityName|
|ActiveListeners|No|ActiveListeners|Počet|Celkem|Total ActiveListeners for Microsoft. Relay|EntityName|
|BytesTransferred|Yes|BytesTransferred|Bajty|Celkem|Total BytesTransferred for Microsoft. Relay|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Počet|Celkem|ClientError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Počet|Celkem|ServerError v ListenerConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|ListenerConnections-Success|No|ListenerConnections-Success|Počet|Celkem|Úspěšně se ListenerConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Počet|Celkem|Total ListenerConnections for Microsoft. Relay|EntityName|
|ListenerDisconnects|No|ListenerDisconnects|Počet|Celkem|Total ListenerDisconnects for Microsoft. Relay|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Počet|Celkem|ClientError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Počet|Celkem|ServerError v SenderConnections pro Microsoft. Relay|EntityName, výsledek operace uvnitř|
|SenderConnections-Success|No|SenderConnections-Success|Počet|Celkem|Úspěšně se SenderConnections pro Microsoft. Relay.|EntityName, výsledek operace uvnitř|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Počet|Celkem|Požadavky SenderConnections (celkem) pro Microsoft. Relay|EntityName|
|SenderDisconnects|No|SenderDisconnects|Počet|Celkem|Total SenderDisconnects for Microsoft. Relay|EntityName|


## <a name="microsoftresourcessubscriptions"></a>Microsoft. Resources/Subscriptions

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Latence|Yes|Data latence příchozích požadavků HTTP|Počet|Průměr|Data latence příchozích požadavků HTTP|Metoda, obor názvů, RequestRegion, ResourceType, Microsoft. SubscriptionId|
|Provoz|Yes|Provoz|Počet|Průměr|Přenos http|RequestRegion, StatusCode, StatusCodeClass, ResourceType, obor názvů, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Latence hledání|Sekundy|Průměr|Průměrná latence hledání pro vyhledávací službu|Žádné dimenze|
|SearchQueriesPerSecond|Yes|Hledání dotazů za sekundu|CountPerSecond|Průměr|Hledání dotazů za sekundu pro vyhledávací službu|Žádné dimenze|
|ThrottledSearchQueriesPercentage|Yes|Procento omezených vyhledávacích dotazů|Procento|Průměr|Procento vyhledávacích dotazů, které byly pro vyhledávací službu omezené|Žádné dimenze|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Počet|Celkem|Celkový počet aktivních připojení pro Microsoft. ServiceBus||
|ActiveMessages|No|Počet aktivních zpráv ve frontě nebo tématu.|Počet|Průměr|Počet aktivních zpráv ve frontě nebo tématu.|EntityName|
|ConnectionsClosed|No|Zavřená připojení.|Počet|Průměr|Uzavřená připojení pro Microsoft. ServiceBus|EntityName|
|ConnectionsOpened|No|Otevřená připojení.|Počet|Průměr|Otevřená připojení pro Microsoft. ServiceBus|EntityName|
|CPUXNS|No|PROCESOR (zastaralé)|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium. Tato metrika je depricated. Místo toho prosím použijte metriku CPU (NamespaceCpuUsage).|Replika|
|DeadletteredMessages|No|Počet nedoručených zpráv ve frontě nebo tématu|Počet|Průměr|Počet nedoručených zpráv ve frontě nebo tématu|EntityName|
|IncomingMessages|Yes|Příchozí zprávy|Počet|Celkem|Příchozí zprávy pro Microsoft. ServiceBus|EntityName|
|IncomingRequests|Yes|Příchozí žádosti|Počet|Celkem|Příchozí požadavky pro Microsoft. ServiceBus|EntityName|
|Zprávy|No|Počet zpráv ve frontě/tématu.|Počet|Průměr|Počet zpráv ve frontě/tématu.|EntityName|
|NamespaceCpuUsage|No|Procesor|Procento|Maximum|Metrika využití procesoru oboru názvů služby Service Bus Premium.|Replika|
|NamespaceMemoryUsage|No|Využití paměti|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium|Replika|
|OutgoingMessages|Yes|Odchozí zprávy|Počet|Celkem|Odchozí zprávy pro Microsoft. ServiceBus|EntityName|
|ScheduledMessages|No|Počet naplánovaných zpráv ve frontě nebo tématu.|Počet|Průměr|Počet naplánovaných zpráv ve frontě nebo tématu.|EntityName|
|ServerErrors|No|Chyby serveru.|Počet|Celkem|Chyby serveru pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|Velikost|No|Velikost|Bajty|Průměr|Velikost fronty nebo tématu v bajtech|EntityName|
|SuccessfulRequests|No|Úspěšné požadavky|Počet|Celkem|Celkový počet úspěšných žádostí pro obor názvů|EntityName, výsledek operace uvnitř|
|ThrottledRequests|No|Omezené požadavky.|Počet|Celkem|Omezené požadavky pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|UserErrors|No|Chyby uživatele.|Počet|Celkem|Chyby uživatele pro Microsoft. ServiceBus|EntityName, výsledek operace uvnitř|
|WSXNS|No|Využití paměti (zastaralé)|Procento|Maximum|Metrika využití paměti oboru názvů služby Service Bus Premium Tato metrika je zastaralá. Místo toho prosím použijte metriku využití paměti (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikace

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Počet|Průměr|Skutečné využití CPU v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Bajty|Průměr|Skutečné využití paměti v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Počet|Průměr|Procesor přidělený tomuto kontejneru v lisovnách|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Bajty|Průměr|Paměť přidělená tomuto kontejneru v MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Počet|Průměr|Stav aplikace Service Fabric mřížka|ApplicationName, status|
|ContainerStatus|No|ContainerStatus|Počet|Průměr|Stav kontejneru v aplikaci Service Fabric mřížka|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|CpuUtilization|No|CpuUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Procento|Průměr|Využití CPU pro tento kontejner jako procento AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Počet|Průměr|Restartování počtu kontejnerů v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Počet|Průměr|Stav repliky služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Počet|Průměr|Stav služby v aplikaci Service Fabric sítě|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Počet připojení|Počet|Maximum|Množství připojení uživatele.|Koncový bod|
|InboundTraffic|Yes|Příchozí provoz|Bajty|Celkem|Příchozí provoz služby|Žádné dimenze|
|MessageCount|Yes|Počet zpráv|Počet|Celkem|Celková velikost zpráv|Žádné dimenze|
|OutboundTraffic|Yes|Odchozí provoz|Bajty|Celkem|Odchozí provoz služby|Žádné dimenze|
|SystemErrors|Yes|Systémové chyby|Procento|Maximum|Procento systémových chyb|Žádné dimenze|
|UserErrors|Yes|Chyby uživatele|Procento|Maximum|Procento uživatelských chyb|Žádné dimenze|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Počet připojení|Počet|Maximum|Množství připojení uživatele.|Žádné dimenze|
|InboundTraffic|Yes|Příchozí provoz|Bajty|Celkem|Příchozí provoz služby|Žádné dimenze|
|OutboundTraffic|Yes|Odchozí provoz|Bajty|Celkem|Odchozí provoz služby|Žádné dimenze|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Průměrné procento procesoru|Procento|Průměr|Průměrné procento procesoru|Žádné dimenze|
|io_bytes_read|Yes|Přečtené vstupně-výstupní bajty|Bajty|Průměr|Přečtené vstupně-výstupní bajty|Žádné dimenze|
|io_bytes_written|Yes|Zapsané vstupně-výstupní bajty|Bajty|Průměr|Zapsané vstupně-výstupní bajty|Žádné dimenze|
|io_requests|Yes|Počet požadavků v/v|Počet|Průměr|Počet požadavků v/v|Žádné dimenze|
|reserved_storage_mb|Yes|Rezervované místo v úložišti|Počet|Průměr|Rezervované místo v úložišti|Žádné dimenze|
|storage_space_used_mb|Yes|Využité místo úložiště|Počet|Průměr|Využité místo úložiště|Žádné dimenze|
|virtual_core_count|Yes|Počet virtuálních jader|Počet|Průměr|Počet virtuálních jader|Žádné dimenze|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktivní dotazy|Počet|Celkem|Aktivní dotazy napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádné dimenze|
|allocated_data_storage|Yes|Přidělené datové místo|Bajty|Průměr|Přidělené úložiště dat. Neplatí pro datové sklady.|Žádné dimenze|
|app_cpu_billed|Yes|CPU aplikace se fakturuje.|Počet|Celkem|CPU aplikace se fakturuje. Platí pro databáze bez serveru.|Žádné dimenze|
|app_cpu_percent|Yes|Procento využití procesoru aplikací|Procento|Průměr|Procento využití procesoru aplikací Platí pro databáze bez serveru.|Žádné dimenze|
|app_memory_percent|Yes|Procento paměti aplikace|Procento|Průměr|Procento paměti aplikace. Platí pro databáze bez serveru.|Žádné dimenze|
|base_blob_size_bytes|Yes|Základní velikost úložiště objektů BLOB|Bajty|Maximum|Základní velikost úložiště objektů BLOB Platí pro databáze s škálovatelným škálováním.|Žádné dimenze|
|blocked_by_firewall|Yes|– Blokováno bránou firewall|Počet|Celkem|– Blokováno bránou firewall|Žádné dimenze|
|cache_hit_percent|Yes|Procento přístupů do mezipaměti|Procento|Maximum|Procento přístupů do mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|cache_used_percent|Yes|Procento využité mezipaměti|Procento|Maximum|Procento využité mezipaměti Platí jenom pro datové sklady.|Žádné dimenze|
|connection_failed|Yes|Neúspěšná připojení|Počet|Celkem|Neúspěšná připojení|Žádné dimenze|
|connection_successful|Yes|Úspěšná připojení|Počet|Celkem|Úspěšná připojení|Žádné dimenze|
|cpu_limit|Yes|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro databáze založené na vCore.|Žádné dimenze|
|cpu_percent|Yes|Procento CPU|Procento|Průměr|Procento CPU|Žádné dimenze|
|cpu_used|Yes|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro databáze založené na vCore.|Žádné dimenze|
|ukončení|Yes|Zablokování|Počet|Celkem|Zablokování. Neplatí pro datové sklady.|Žádné dimenze|
|diff_backup_size_bytes|Yes|Rozdílová velikost úložiště zálohování|Bajty|Maximum|Kumulativní velikost úložiště rozdílové zálohy. Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádné dimenze|
|dtu_consumption_percent|Yes|Procento DTU|Procento|Průměr|Procento DTU Platí pro databáze založené na DTU.|Žádné dimenze|
|dtu_limit|Yes|Limit DTU|Počet|Průměr|Limit DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|dtu_used|Yes|Využité DTU|Počet|Průměr|Používá se DTU. Platí pro databáze založené na DTU.|Žádné dimenze|
|dwu_consumption_percent|Yes|Procento DWU|Procento|Maximum|Procento DWU Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_limit|Yes|DWU limit|Počet|Maximum|DWU limit. Platí jenom pro datové sklady.|Žádné dimenze|
|dwu_used|Yes|DWU použito|Počet|Maximum|DWU použito. Platí jenom pro datové sklady.|Žádné dimenze|
|full_backup_size_bytes|Yes|Velikost úložiště pro úplné zálohování|Bajty|Maximum|Celková velikost úložiště pro úplné zálohování Platí pro databáze založené na vCore. Neplatí pro databáze s škálovatelným škálováním.|Žádné dimenze|
|local_tempdb_usage_percent|Yes|Místní procento databáze tempdb|Procento|Průměr|Místní procento databáze tempdb. Platí jenom pro datové sklady.|Žádné dimenze|
|log_backup_size_bytes|Yes|Velikost úložiště zálohy protokolu|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního protokolu. Platí pro databáze založené na vCore a na škálovatelných databázích.|Žádné dimenze|
|log_write_percent|Yes|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v. Neplatí pro datové sklady.|Žádné dimenze|
|memory_usage_percent|Yes|Procento paměti|Procento|Maximum|Procento paměti. Platí jenom pro datové sklady.|Žádné dimenze|
|physical_data_read_percent|Yes|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádné dimenze|
|queued_queries|Yes|Dotazy ve frontě|Počet|Celkem|Dotazy ve frontě napříč všemi skupinami úloh. Platí jenom pro datové sklady.|Žádné dimenze|
|sessions_percent|Yes|Procento relací|Procento|Průměr|Procento relací Neplatí pro datové sklady.|Žádné dimenze|
|snapshot_backup_size_bytes|Yes|Velikost úložiště záloh snímků|Bajty|Maximum|Velikost úložiště pro zálohu kumulativního snímku. Platí pro databáze s škálovatelným škálováním.|Žádné dimenze|
|sqlserver_process_core_percent|Yes|Základní procento procesu SQL Server|Procento|Maximum|Využití CPU jako procento procesu SQL DB. Neplatí pro datové sklady.|Žádné dimenze|
|sqlserver_process_memory_percent|Yes|% SQL Server paměti procesu|Procento|Maximum|Využití paměti jako procento procesu SQL DB. Neplatí pro datové sklady.|Žádné dimenze|
|úložiště|Yes|Využité místo pro data|Bajty|Maximum|Využité místo pro data Neplatí pro datové sklady.|Žádné dimenze|
|storage_percent|Yes|Procento využitého datového prostoru|Procento|Maximum|Procento využitého datového prostoru Neplatí pro datové sklady nebo databáze na úrovni dat.|Žádné dimenze|
|tempdb_data_size|Yes|Velikost datového souboru tempdb v kilobajtech|Počet|Maximum|Místo využité v datových souborech tempdb v kilobajtech Neplatí pro datové sklady.|Žádné dimenze|
|tempdb_log_size|Yes|Velikost souboru protokolu tempdb v kilobajtech|Počet|Maximum|Místo, které se používá v souboru protokolu transakcí tempdb v kilobajtech Neplatí pro datové sklady.|Žádné dimenze|
|tempdb_log_used_percent|Yes|Použit protokol tempdb v procentech|Procento|Maximum|Procento využitého místa v souboru protokolu transakcí tempdb Neplatí pro datové sklady.|Žádné dimenze|
|wlg_active_queries|Yes|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Vypršení časového limitu dotazu skupiny úloh|Počet|Celkem|Dotazy, jejichž časový limit pro skupinu úloh vypršel. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Přidělené procento prostředků relativních k celému systému na skupinu úloh. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Přidělení skupiny úloh podle zakončení prostředků v procentech|Procento|Maximum|Přidělené procento prostředků vzhledem k zadaným prostředkům Cap na skupinu úloh Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Procento efektivního Cap prostředku|Procento|Maximum|Pevné omezení procentuální hodnoty prostředků povolených pro skupinu úloh s ohledem na efektivní minimální procento prostředků přidělených pro jiné skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Efektivní minimální procento prostředků|Procento|Maximum|Minimální procento prostředků rezervovaných a izolovaných pro skupinu úloh s ohledem na minimum úrovně služby. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Dotazování skupin úloh ve frontě|Počet|Celkem|Dotazy zařazené do fronty v rámci skupiny úloh. Platí jenom pro datové sklady.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů Neplatí pro datové sklady.|Žádné dimenze|
|xtp_storage_percent|Yes|In-Memory OLTP úložiště v procentech|Procento|Průměr|In-Memory OLTP úložiště v procentech. Neplatí pro datové sklady.|Žádné dimenze|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/servery/elasticPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|Žádné dimenze|
|allocated_data_storage_percent|Yes|Procentuální hodnota přiděleného datového prostoru|Procento|Maximum|Procentuální hodnota přiděleného datového prostoru|Žádné dimenze|
|cpu_limit|Yes|Limit procesoru|Počet|Průměr|Limit procesoru. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|cpu_percent|Yes|Procento CPU|Procento|Průměr|Procento CPU|Žádné dimenze|
|cpu_used|Yes|Využitý procesor|Počet|Průměr|Využitý procesor. Platí pro elastické fondy založené na vCore.|Žádné dimenze|
|database_allocated_data_storage|No|Přidělené datové místo|Bajty|Průměr|Přidělené datové místo|DatabaseResourceId|
|database_cpu_limit|No|Limit procesoru|Počet|Průměr|Limit procesoru|DatabaseResourceId|
|database_cpu_percent|No|Procento CPU|Procento|Průměr|Procento CPU|DatabaseResourceId|
|database_cpu_used|No|Využitý procesor|Počet|Průměr|Využitý procesor|DatabaseResourceId|
|database_dtu_consumption_percent|No|Procento DTU|Procento|Průměr|Procento DTU|DatabaseResourceId|
|database_eDTU_used|No|využité eDTU|Počet|Průměr|využité eDTU|DatabaseResourceId|
|database_log_write_percent|No|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|DatabaseResourceId|
|database_physical_data_read_percent|No|Procento datových V/V|Procento|Průměr|Procento datových V/V|DatabaseResourceId|
|database_sessions_percent|No|Procento relací|Procento|Průměr|Procento relací|DatabaseResourceId|
|database_storage_used|No|Využité místo pro data|Bajty|Průměr|Využité místo pro data|DatabaseResourceId|
|database_workers_percent|No|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|DatabaseResourceId|
|dtu_consumption_percent|Yes|Procento DTU|Procento|Průměr|Procento DTU Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|eDTU_limit|Yes|limit eDTU|Počet|Průměr|limit eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|eDTU_used|Yes|využité eDTU|Počet|Průměr|používá se eDTU. Platí pro elastické fondy založené na DTU.|Žádné dimenze|
|log_write_percent|Yes|Procentní hodnota protokolu v/v|Procento|Průměr|Procentní hodnota protokolu v/v|Žádné dimenze|
|physical_data_read_percent|Yes|Procento datových V/V|Procento|Průměr|Procento datových V/V|Žádné dimenze|
|sessions_percent|Yes|Procento relací|Procento|Průměr|Procento relací|Žádné dimenze|
|sqlserver_process_core_percent|Yes|Základní procento procesu SQL Server|Procento|Maximum|Využití CPU jako procento procesu SQL DB. Platí pro elastické fondy.|Žádné dimenze|
|sqlserver_process_memory_percent|Yes|% SQL Server paměti procesu|Procento|Maximum|Využití paměti jako procento procesu SQL DB. Platí pro elastické fondy.|Žádné dimenze|
|storage_limit|Yes|Maximální velikost dat|Bajty|Průměr|Maximální velikost dat|Žádné dimenze|
|storage_percent|Yes|Procento využitého datového prostoru|Procento|Průměr|Procento využitého datového prostoru|Žádné dimenze|
|storage_used|Yes|Využité místo pro data|Bajty|Průměr|Využité místo pro data|Žádné dimenze|
|tempdb_data_size|Yes|Velikost datového souboru tempdb v kilobajtech|Počet|Maximum|Místo využité v datových souborech tempdb v kilobajtech|Žádné dimenze|
|tempdb_log_size|Yes|Velikost souboru protokolu tempdb v kilobajtech|Počet|Maximum|Místo, které se používá v souboru protokolu transakcí tempdb v kilobajtech|Žádné dimenze|
|tempdb_log_used_percent|Yes|Použit protokol tempdb v procentech|Procento|Maximum|Procento využitého místa v souboru protokolu transakcí tempdb|Žádné dimenze|
|workers_percent|Yes|Procento pracovních procesů|Procento|Průměr|Procento pracovních procesů|Žádné dimenze|
|xtp_storage_percent|Yes|In-Memory OLTP úložiště v procentech|Procento|Průměr|In-Memory OLTP úložiště v procentech|Žádné dimenze|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Množství výchozích dat. Toto číslo zahrnuje výstup do externího klienta z Azure Storage a také pro výstup v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|
|UsedCapacity|Yes|Využitá kapacita|Bajty|Průměr|Velikost úložiště využitého účtem úložiště U standardních účtů úložiště je to součet kapacity využité objektem blob, tabulkou, souborem a frontou. Pro účty úložiště úrovně Premium a účty BLOB Storage je stejný jako BlobCapacity nebo kapacita.|Žádné dimenze|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|BlobCapacity|No|Kapacita objektu BLOB|Bajty|Průměr|Velikost úložiště využitá Blob service účtu úložiště v bajtech|BlobType, úroveň|
|BlobCount|No|Počet objektů BLOB|Počet|Průměr|Počet objektů BLOB uložených v účtu úložiště.|BlobType, úroveň|
|BlobProvisionedSize|No|Zřízená velikost objektu BLOB|Bajty|Průměr|Velikost úložiště zřízená v účtu úložiště Blob service v bajtech.|BlobType, úroveň|
|ContainerCount|Yes|Počet kontejnerů objektů BLOB|Počet|Průměr|Počet kontejnerů v účtu úložiště.|Žádné dimenze|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Množství výchozích dat. Toto číslo zahrnuje výstup do externího klienta z Azure Storage a také pro výstup v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|IndexCapacity|No|Kapacita indexu|Bajty|Průměr|Velikost úložiště, kterou používá Azure Data Lake Storage Gen2 hierarchický index.|Žádné dimenze|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování, sdílení souborů|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Množství výchozích dat. Toto číslo zahrnuje výstup do externího klienta z Azure Storage a také pro výstup v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování, sdílení souborů|
|Kapacita zařízení|No|Kapacita souboru|Bajty|Průměr|Velikost úložiště souborů, kterou používá účet úložiště.|Sdílená složka|
|FileCount|No|Počet souborů|Počet|Průměr|Počet souborů v účtu úložiště.|Sdílená složka|
|FileShareCapacityQuota|No|Kvóta kapacity sdílené složky|Bajty|Průměr|Horní limit velikosti úložiště, který může služba soubory Azure využít v bajtech.|Sdílená složka|
|FileShareCount|No|Počet sdílených souborů|Počet|Průměr|Počet sdílených složek v účtu úložiště.|Žádné dimenze|
|FileShareProvisionedIOPS|No|Zřízené IOPS sdílené složky|Bajty|Průměr|Základní počet zřízených vstupně-výstupních operací za sekundu pro sdílenou složku v účtu úložiště prémiových souborů Toto číslo se vypočítá na základě zřízené velikosti (kvóty) kapacity sdílení.|Sdílená složka|
|FileShareSnapshotCount|No|Počet snímků sdílené složky|Počet|Průměr|Počet snímků, které jsou k dispozici ve službě soubory účtu úložiště ve sdílené složce.|Sdílená složka|
|FileShareSnapshotSize|No|Velikost snímku sdílené složky|Bajty|Průměr|Velikost úložiště využívané snímky v Souborové službě účtu úložiště v bajtech|Sdílená složka|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování, sdílení souborů|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování, sdílení souborů|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování, sdílení souborů|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Množství výchozích dat. Toto číslo zahrnuje výstup do externího klienta z Azure Storage a také pro výstup v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|QueueCapacity|Yes|Kapacita fronty|Bajty|Průměr|Velikost úložiště fronty, kterou účet úložiště používá|Žádné dimenze|
|QueueCount|Yes|Počet front|Počet|Průměr|Počet front v účtu úložiště.|Žádné dimenze|
|QueueMessageCount|Yes|Počet zpráv ve frontě|Počet|Průměr|Počet zpráv ve frontě s neplatným vypršením platnosti v účtu úložiště|Žádné dimenze|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Dostupnost|Yes|Dostupnost|Procento|Průměr|Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota TotalBillableRequests vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost.|Typ, ApiName, ověřování|
|Výchozí přenos dat|Yes|Výchozí přenos dat|Bajty|Celkem|Množství výchozích dat. Toto číslo zahrnuje výstup do externího klienta z Azure Storage a také pro výstup v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat.|Typ, ApiName, ověřování|
|Příchozí přenos dat|Yes|Příchozí přenos dat|Bajty|Celkem|Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure.|Typ, ApiName, ověřování|
|SuccessE2ELatency|Yes|Celková latence při úspěchu|Milisekund|Průměr|Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi.|Typ, ApiName, ověřování|
|SuccessServerLatency|Yes|Latence serveru při úspěchu|Milisekund|Průměr|Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu.|Typ, ApiName, ověřování|
|TableCapacity|Yes|Kapacita tabulky|Bajty|Průměr|Velikost úložiště tabulek, kterou používá účet úložiště|Žádné dimenze|
|TableCount|Yes|Počet tabulek|Počet|Průměr|Počet tabulek v účtu úložiště|Žádné dimenze|
|TableEntityCount|Yes|Počet entit tabulky|Počet|Průměr|Počet entit tabulky v účtu úložiště|Žádné dimenze|
|Transakce|Yes|Transakce|Počet|Celkem|Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. Použijte dimenzi ResponseType pro počet různých typů odpovědí.|ResponseType, typ, ApiName, ověřování|


## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/caches

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Celkový počet vstupně-výstupních operací klienta|Počet|Průměr|Počet operací klientského souboru zpracovávaných mezipamětí.|Žádné dimenze|
|ClientLatency|Yes|Průměrná latence klienta|Milisekund|Průměr|Průměrná latence operací s klientskými soubory a mezipamětí|Žádné dimenze|
|ClientLockIOPS|Yes|IOPS zámku klienta|CountPerSecond|Průměr|Operace uzamčení souborů klienta za sekundu|Žádné dimenze|
|ClientMetadataReadIOPS|Yes|Metadata klienta přečetla IOPS|CountPerSecond|Průměr|Frekvence operací s klientskými soubory odesílanými do mezipaměti s výjimkou čtení dat, která nemění trvalý stav.|Žádné dimenze|
|ClientMetadataWriteIOPS|Yes|Zápis metadat klienta za sekundu|CountPerSecond|Průměr|Počet operací klientského souboru odeslaných do mezipaměti s výjimkou zápisů dat, které upravují trvalý stav.|Žádné dimenze|
|ClientReadIOPS|Yes|Klientský vstupně-výstupní operace čtení|CountPerSecond|Průměr|Klientské operace čtení za sekundu.|Žádné dimenze|
|ClientReadThroughput|Yes|Průměrná propustnost čtení mezipaměti|BytesPerSecond|Průměr|Rychlost přenosu dat čtení klienta.|Žádné dimenze|
|ClientWriteIOPS|Yes|Zápis klientského IOPS za sekundu|CountPerSecond|Průměr|Operace zápisu klienta za sekundu|Žádné dimenze|
|ClientWriteThroughput|Yes|Průměrná propustnost zápisu mezipaměti|BytesPerSecond|Průměr|Přenosová rychlost zápisu dat klienta.|Žádné dimenze|
|StorageTargetAsyncWriteThroughput|Yes|Propustnost asynchronního zápisu StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť asynchronně zapisuje data do konkrétního StorageTarget. Jedná se o příležitostné zápisy, které nezpůsobují klientům blokování.|StorageTarget|
|StorageTargetFillThroughput|Yes|Propustnost výplně StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť čte data z StorageTarget a zpracovává tak Neúspěšné přístupy do mezipaměti|StorageTarget|
|StorageTargetHealth|Yes|Stav cíle úložiště|Počet|Průměr|Logické výsledky testu připojení mezi cíli mezipaměti a úložištěm.|Žádné dimenze|
|StorageTargetIOPS|Yes|Celkem StorageTarget IOPS|Počet|Průměr|Frekvence všech operací se soubory, které mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetLatency|Yes|Latence StorageTarget|Milisekund|Průměr|Průměrná latence odezvy všech operací se soubory, kterou mezipaměť odesílá na partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget metadata Read IOPS|CountPerSecond|Průměr|Frekvence operací se soubory, které nemění trvalý stav a s výjimkou operace čtení, kterou mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|Zápisy metadat StorageTarget IOPS|CountPerSecond|Průměr|Frekvence operací se soubory, které upravují trvalý stav a s výjimkou operace zápisu, kterou mezipaměť odesílá na konkrétní StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|Propustnost před čtením StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť opportunisticly čte data z StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget čtení IOPS|CountPerSecond|Průměr|Míra operací čtení souborů, které mezipaměť odesílá do konkrétního StorageTarget.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|Propustnost synchronního zápisu StorageTarget|BytesPerSecond|Průměr|Rychlost, jakou mezipaměť synchronně zapisuje data do konkrétního StorageTarget. Jedná se o zápisy, které způsobují blokování klientů.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|StorageTarget celkové propustnosti čtení|BytesPerSecond|Průměr|Celková míra, kterou mezipaměť čte data z konkrétní StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|StorageTarget celkové propustnosti zápisu|BytesPerSecond|Průměr|Celková míra, kterou mezipaměť zapisuje data do konkrétního StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|StorageTarget zápis IOPS|Počet|Průměr|Frekvence operací zápisu souborů, které mezipaměť odesílá do konkrétní StorageTarget.|StorageTarget|
|Doba provozu|Yes|Doba provozu|Počet|Průměr|Logické výsledky testu připojení mezi mezipamětí a systémem monitorování.|Žádné dimenze|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Výsledek relace synchronizace|Počet|Průměr|Metrika, která protokoluje hodnotu 1 pokaždé, když koncový bod serveru úspěšně dokončí relaci synchronizace s koncovým bodem cloudu|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Yes|Míra úspěšnosti odvolání ve vrstvě cloudu|Procento|Průměr|Procento všech odvolání, která byla úspěšná|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Velikost odvolání při vrstvení cloudu podle aplikace|Bajty|Celkem|Velikost dat vrácených aplikací|SyncGroupName, servername, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Velikost odvolání při vyvolání vrstvy cloudu|Bajty|Celkem|Velikost vrácených dat|SyncGroupName, servername|
|StorageSyncRecallIOTotalSizeBytes|Yes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Propustnost volání při navracení cloudových vrstev|BytesPerSecond|Průměr|Velikost propustnosti odvolání dat|SyncGroupName, servername|
|StorageSyncServerHeartbeat|Yes|Online stav serveru|Počet|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Online stav serveru|Počet|Maximum|Metrika, která protokoluje hodnotu 1 pokaždé, když server resigtered úspěšně zaznamená prezenční signál pomocí koncového bodu cloudu|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Yes|Stažení vrstvení cloudu|Bajty|Celkem|Celková velikost dat vrácených serverem|ServerResourceId, servername|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Synchronizované bajty|Bajty|Celkem|Celková velikost souborů přenesených pro relace synchronizace|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Synchronizované soubory|Počet|Celkem|Počet synchronizovaných souborů|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Soubory se nesynchronizují|Počet|Celkem|Počet souborů, jejichž synchronizace se nezdařila|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Neúspěšné žádosti o funkce|Počet|Celkem|Neúspěšné žádosti o funkce|ID logického, PartitionId|
|AMLCalloutInputEvents|Yes|Události funkcí|Počet|Celkem|Události funkcí|ID logického, PartitionId|
|AMLCalloutRequests|Yes|Žádosti o funkce|Počet|Celkem|Žádosti o funkce|ID logického, PartitionId|
|ConversionErrors|Yes|Chyby převodu dat|Počet|Celkem|Chyby převodu dat|ID logického, PartitionId|
|DeserializationError|Yes|Chyby při deserializaci vstupu|Počet|Celkem|Chyby při deserializaci vstupu|ID logického, PartitionId|
|DroppedOrAdjustedEvents|Yes|Události mimo pořadí|Počet|Celkem|Události mimo pořadí|ID logického, PartitionId|
|EarlyInputEvents|Yes|Události předčasného vstupu|Počet|Celkem|Události předčasného vstupu|ID logického, PartitionId|
|Chyby|Yes|Běhové chyby|Počet|Celkem|Běhové chyby|ID logického, PartitionId|
|InputEventBytes|Yes|Bajty vstupních událostí|Bajty|Celkem|Bajty vstupních událostí|ID logického, PartitionId|
|InputEvents|Yes|Události vstupu|Počet|Celkem|Události vstupu|ID logického, PartitionId|
|InputEventsSourcesBacklogged|Yes|Nevyřízené události vstupu|Počet|Maximum|Nevyřízené události vstupu|ID logického, PartitionId|
|InputEventsSourcesPerSecond|Yes|Přijaté vstupní zdroje|Počet|Celkem|Přijaté vstupní zdroje|ID logického, PartitionId|
|LateInputEvents|Yes|Zpožděné vstupní události|Počet|Celkem|Zpožděné vstupní události|ID logického, PartitionId|
|OutputEvents|Yes|Výstupní události|Počet|Celkem|Výstupní události|ID logického, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Zpoždění vodoznaku|Sekundy|Maximum|Zpoždění vodoznaku|ID logického, PartitionId|
|ProcessCPUUsagePercentage|Yes|Využití procesoru v% (Preview)|Procento|Maximum|Využití procesoru v% (Preview)|ID logického, PartitionId|
|ResourceUtilization|Yes|Využití SU%|Procento|Maximum|Využití SU%|ID logického, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Zpracovaná data (bajty)|Bajty|Celkem|Množství dat zpracovaných dotazy|Žádné dimenze|
|BuiltinSqlPoolLoginAttempts|No|Pokusy o přihlášení|Počet|Celkem|Počet pokusů o přihlášení, které nasadilo nebo selhaly|Výsledek|
|BuiltinSqlPoolRequestsEnded|No|Žádosti byly ukončeny.|Počet|Celkem|Počet požadavků, které úspěšně uspěly, selhaly nebo byly zrušeny|Výsledek|
|IntegrationActivityRunsEnded|No|Běh aktivity skončil.|Počet|Celkem|Počet zrušených integračních aktivit, které byly úspěšné, selhaly nebo byly zrušeny|Výsledek, FailureType, aktivita, ActivityType, kanál|
|IntegrationPipelineRunsEnded|No|Běh kanálu skončil.|Počet|Celkem|Počet zrušených běhů kanálu integrace, které byly úspěšné, selhaly nebo byly zrušeny|Výsledek, FailureType, kanál|
|IntegrationTriggerRunsEnded|No|Spuštění aktivační události skončilo.|Počet|Celkem|Počet zrušených aktivačních událostí integrace, které byly úspěšné, selhaly nebo byly zrušeny|Výsledek, FailureType, Trigger|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|přidělené virtuální jádra|Počet|Maximum|Přidělená virtuální jádra pro fond Apache Spark|SubmitterId|
|BigDataPoolAllocatedMemory|No|Přidělená paměť (GB)|Počet|Maximum|Přidělená paměť pro fond Apach Spark (GB)|SubmitterId|
|BigDataPoolApplicationsActive|No|Aktivní aplikace Apache Spark|Počet|Maximum|Celkem aktivních Apache Sparkch fondů aplikací|JobState|
|BigDataPoolApplicationsEnded|No|Ukončené aplikace Apache Spark|Počet|Celkem|Počet ukončených aplikací fondu Apache Spark|JobType, výsledek úlohy|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Aktivní dotazy|Počet|Celkem|Aktivní dotazy. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému.|IsUserDefined|
|AdaptiveCacheHitPercent|No|Procento přístupů do adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procentuální hodnoty přístupů do mezipaměti určete, jestli se má škálovat pro další kapacitu, nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádné dimenze|
|AdaptiveCacheUsedPercent|No|Procento využití adaptivní mezipaměti|Procento|Maximum|Měří, jak fungují i úlohy s adaptivní mezipamětí. Pomocí této metriky s metrikou procenta využití mezipaměti určete, jestli se má škálovat pro další kapacitu nebo znovu spustit úlohy, aby se hydratea mezipaměť.|Žádné dimenze|
|Připojení|Yes|Připojení|Počet|Celkem|Celkový počet přihlášení ke fondu SQL|Výsledek|
|ConnectionsBlockedByFirewall|No|Připojení blokovaná bránou firewall|Počet|Celkem|Počet připojení blokovaných pravidly brány firewall Přečtěte si zásady řízení přístupu pro váš fond SQL a sledujte tato připojení, pokud je počet vysoký.|Žádné dimenze|
|CPUPercent|No|Procento využití procesoru|Procento|Maximum|Využití CPU ve všech uzlech ve fondu SQL|Žádné dimenze|
|DWULimit|No|DWU limit|Počet|Maximum|Cíl na úrovni služby pro fond SQL|Žádné dimenze|
|DWUUsed|No|DWU použito|Počet|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měřeno podle limitu DWU * procento DWU|Žádné dimenze|
|DWUUsedPercent|No|Procento využitého DWU|Procento|Maximum|Představuje reprezentaci vysoké úrovně využití napříč fondem SQL. Měří se tak, že se převezme maximum mezi procenty CPU a v/v.|Žádné dimenze|
|LocalTempDBUsedPercent|No|Procento využité místní databáze tempdb|Procento|Maximum|Místní využití databáze tempdb napříč všemi výpočetními uzly – hodnoty se vysílají každých pět minut.|Žádné dimenze|
|MemoryUsedPercent|No|Procento využité paměti|Procento|Maximum|Využití paměti ve všech uzlech ve fondu SQL|Žádné dimenze|
|QueuedQueries|No|Dotazy ve frontě|Počet|Celkem|Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti|IsUserDefined|
|WLGActiveQueries|No|Aktivní dotazy skupiny úloh|Počet|Celkem|Aktivní dotazy v rámci skupiny úloh. Použití této metriky Nefiltrováno a nerozdělené zobrazí všechny aktivní dotazy běžící v systému.|IsUserDefined, pracovní vytížení|
|WLGActiveQueriesTimeouts|No|Vypršení časového limitu dotazu skupiny úloh|Počet|Celkem|Dotazy na skupinu úloh, jejichž časový limit vypršel. Vypršení časových limitů dotazů, které tato metrika oznamuje, je jenom jednou, když je dotaz spuštěný (nezahrnuje čekací dobu z důvodu uzamčení nebo čekání na prostředky).|IsUserDefined, pracovní vytížení|
|WLGAllocationByEffectiveCapResourcePercent|No|Přidělení skupiny úloh podle maximálního procenta prostředků|Procento|Maximum|Zobrazuje procento přidělení prostředků vzhledem k procentuální hodnotě prostředku efektivního Cap na skupinu úloh. Tato metrika poskytuje efektivní využití skupiny úloh.|IsUserDefined, pracovní vytížení|
|WLGAllocationBySystemPercent|No|Přidělení skupiny úloh podle systémových procent|Procento|Maximum|Procento přidělení prostředků vzhledem k celému systému|IsUserDefined, pracovní vytížení|
|WLGEffectiveCapResourcePercent|No|Procento efektivního Cap prostředku|Procento|Maximum|Procentuální hodnota prostředku efektivního Cap pro skupinu úloh. Pokud jsou k dispozici jiné skupiny úloh s min_percentage_resource > 0, effective_cap_percentage_resource se v poměru dolů.|IsUserDefined, pracovní vytížení|
|WLGEffectiveMinResourcePercent|No|Efektivní minimální procento prostředků|Procento|Maximum|Nastavení platné minimální procento prostředků s povoleným zvážením úrovně služby a nastavení skupiny úloh. Efektivní min_percentage_resource lze zvýšit na nižší úrovni služeb.|IsUserDefined, pracovní vytížení|
|WLGQueuedQueries|No|Dotazování skupin úloh ve frontě|Počet|Celkem|Kumulativní počet požadavků zařazených do fronty po dosažení maximálního počtu souběžnosti|IsUserDefined, pracovní vytížení|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze všech zdrojů událostí|Žádné dimenze|
|IngressReceivedInvalidMessages|Yes|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedMessages|Yes|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv načtených ze všech centra událostí nebo zdrojů událostí služby IoT Hub|Žádné dimenze|
|IngressReceivedMessagesCountLag|Yes|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Yes|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressStoredBytes|Yes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Yes|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|WarmStorageMaxProperties|Yes|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Yes|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Přijaté bajty příchozího přenosu dat|Bajty|Celkem|Počet přečtených bajtů ze zdroje události|Žádné dimenze|
|IngressReceivedInvalidMessages|Yes|Příchozí přenos dat přijal neplatné zprávy.|Počet|Celkem|Počet neplatných zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedMessages|Yes|Příchozí zprávy příchozího přenosu dat|Počet|Celkem|Počet zpráv přečtených ze zdroje události|Žádné dimenze|
|IngressReceivedMessagesCountLag|Yes|Prodleva počtu přijatých zpráv příchozího přenosu|Počet|Průměr|Rozdíl mezi pořadovým číslem poslední zprávy ve frontě ve zdrojovém oddílu události a pořadovým číslem zpracovávaných zpráv v příchozím přenosu dat|Žádné dimenze|
|IngressReceivedMessagesTimeLag|Yes|Časová prodleva přijatých příchozích zpráv příchozího přenosu dat|Sekundy|Maximum|Rozdíl mezi časem, kdy je zpráva zařazená do fronty ve zdroji událostí a čas zpracování v příchozím přenosu dat|Žádné dimenze|
|IngressStoredBytes|Yes|Uložené bajty příchozího přenosu dat|Bajty|Celkem|Celková velikost událostí úspěšně zpracovaných a dostupných pro dotaz|Žádné dimenze|
|IngressStoredEvents|Yes|Uložené události příchozího přenosu dat|Počet|Celkem|Počet úspěšně zpracovaných a dostupných dotazů v počtu plochých událostí|Žádné dimenze|
|WarmStorageMaxProperties|Yes|Maximální počet vlastností služby teplé úložiště|Počet|Maximum|Maximální počet vlastností používaných prostředím pro SKU S1/S2 a maximální počet vlastností povolený pro PAYG SKU v rámci služby teplé úložiště|Žádné dimenze|
|WarmStorageUsedProperties|Yes|Vlastnosti použití teplého úložiště |Počet|Maximum|Počet vlastností používaných prostředím pro SKU S1/S2 a počet vlastností, které používá služba pro zahřívání v úložišti pro PAYG|Žádné dimenze|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|Bajty čtení z disku|Yes|Bajty čtení z disku|Bajty|Celkem|Celková propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|Operace čtení z disku/s|Yes|Operace čtení z disku/s|CountPerSecond|Průměr|Průměrný počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|Bajty zápisu na disk|Yes|Bajty zápisu na disk|Bajty|Celkem|Celková propustnost disku v důsledku operací zápisu v období vzorkování.|Žádné dimenze|
|Operace zápisu na disk/s|Yes|Operace zápisu na disk/s|CountPerSecond|Průměr|Průměrný počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskReadBytesPerSecond|Yes|Bajty čtení z disku/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací čtení v období vzorkování.|Žádné dimenze|
|DiskReadLatency|Yes|Latence čtení disku|Milisekund|Průměr|Celková latence čtení Součet latencí čtení zařízení a jádra.|Žádné dimenze|
|DiskReadOperations|Yes|Operace čtení z disku|Počet|Celkem|Počet vstupně-výstupních operací čtení v předchozích ukázkových obdobích. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|DiskWriteBytesPerSecond|Yes|Bajty zápisu na disk/s|BytesPerSecond|Průměr|Průměrná propustnost disku z důvodu operací zápisu v období vzorkování.|Žádné dimenze|
|DiskWriteLatency|Yes|Latence zápisu na disk|Milisekund|Průměr|Celková latence zápisu Součet latencí zápisu zařízení a jádra.|Žádné dimenze|
|DiskWriteOperations|Yes|Operace zápisu na disk|Počet|Celkem|Počet operací zápisu v/v v předchozím období vzorkování. Všimněte si, že tyto operace mohou být proměnlivé velikosti.|Žádné dimenze|
|MemoryActive|Yes|Paměť aktivní|Bajty|Průměr|Velikost paměti, kterou virtuální počítač využíval v posledních malých oknech času. Toto je "pravdivý" počet paměti, které virtuální počítač v současnosti potřebuje. Dodatečná, nevyužitá paměť může být vyměněna nebo v bublině bez dopadu na výkon hosta.|Žádné dimenze|
|MemoryGranted|Yes|Přidělená paměť|Bajty|Průměr|Velikost paměti, která byla k virtuálnímu počítači udělena hostitelem. Hostiteli není pro hostitele udělené, dokud se nedotknete jednou, a pokud VMkernel potřebuje paměť, může se tato paměť vyměnit.|Žádné dimenze|
|MemoryUsed|Yes|Využitá paměť|Bajty|Průměr|Velikost paměti počítače, kterou virtuální počítač používá.|Žádné dimenze|
|Síťové vstupy|Yes|Síťové vstupy|Bajty|Celkem|Celková propustnost sítě pro přijatý provoz.|Žádné dimenze|
|Síťové výstupy|Yes|Síťové výstupy|Bajty|Celkem|Celková propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|NetworkInBytesPerSecond|Yes|Síť v bajtech/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přijatý provoz.|Žádné dimenze|
|NetworkOutBytesPerSecond|Yes|Výstupní bajty sítě/s|BytesPerSecond|Průměr|Průměrná propustnost sítě pro přenos přenášených dat.|Žádné dimenze|
|Procento CPU|Yes|Procento CPU|Procento|Průměr|Využití procesoru. Tato hodnota je hlášena s 100%, která představuje všechny jádra procesoru v systému. Příklad: oboustranný virtuální počítač, který používá 50% systému se čtyřmi jádry, plně používá dvě jádra.|Žádné dimenze|
|PercentageCpuReady|Yes|Procento připraveného procesoru|Milisekund|Celkem|Čas připravenosti je doba, po kterou se bude čekat na dostupnost PROCESORů v minulém intervalu aktualizace.|Žádné dimenze|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktivní požadavky (zastaralé)|Počet|Celkem|Aktivní požadavky|Instance|
|AverageResponseTime|Yes|Průměrná doba odezvy (nepoužívané)|Sekundy|Průměr|Průměrná doba trvání front-endu pro poskytování požadavků v sekundách.|Instance|
|BytesReceived|Yes|Data v|Bajty|Celkem|Průměrná příchozí šířka pásma využitá napříč všemi front-endy v souboru MiB.|Instance|
|BytesSent|Yes|Výstupní data|Bajty|Celkem|Průměrná příchozí šířka pásma využitá v rámci všech front-endu v souboru MiB.|Instance|
|CpuPercentage|Yes|Procento procesoru|Procento|Průměr|Průměrně využitý procesor napříč všemi instancemi front-endu.|Instance|
|DiskQueueLength|Yes|Délka fronty disku|Počet|Průměr|Průměrný počet požadavků na čtení i zápis, které byly zařazeny do fronty úložiště. Maximální délka fronty disku je označením aplikace, která může být zpomalena z důvodu nadměrného vstupně-výstupních operací disku.|Instance|
|Http101|Yes|Http 101|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 101.|Instance|
|Http2xx|Yes|2xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 200, ale < 300.|Instance|
|Http3xx|Yes|3xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 300, ale < 400.|Instance|
|Http401|Yes|HTTP 401|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 401|Instance|
|Http403|Yes|HTTP 403|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 403|Instance|
|Http404|Yes|HTTP 404|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 404|Instance|
|Http406|Yes|Http 406|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 406|Instance|
|Http4xx|Yes|4xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 400, ale < 500.|Instance|
|Http5xx|Yes|Chyby serveru http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 500, ale < 600.|Instance|
|HttpQueueLength|Yes|Délka fronty http|Počet|Průměr|Průměrný počet požadavků HTTP, které měly být zařazeny do fronty před jejich splněním. Vysoká nebo rostoucí délka fronty HTTP je příznakem plánu v oblasti vysokého zatížení.|Instance|
|HttpResponseTime|Yes|Doba odezvy|Sekundy|Průměr|Doba, kterou zabere front-end k obsluze požadavků (v sekundách).|Instance|
|LargeAppServicePlanInstances|Yes|Zaměstnanci s velkými App Servicey plánu|Počet|Průměr|Zaměstnanci s velkými App Servicey plánu|Žádné dimenze|
|MediumAppServicePlanInstances|Yes|Střední App Service plánování pracovních procesů|Počet|Průměr|Střední App Service plánování pracovních procesů|Žádné dimenze|
|MemoryPercentage|Yes|Procento paměti|Procento|Průměr|Průměrná velikost paměti využitá ve všech instancích front-endu.|Instance|
|Žádosti|Yes|Žádosti|Počet|Celkem|Celkový počet požadavků bez ohledu na výsledný kód stavu HTTP.|Instance|
|SmallAppServicePlanInstances|Yes|Plánování pracovníků malých App Service|Počet|Průměr|Plánování pracovníků malých App Service|Žádné dimenze|
|TotalFrontEnds|Yes|Celkový počet front-endy|Počet|Průměr|Celkový počet front-endy|Žádné dimenze|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|Procento procesoru|Procento|Průměr|Průměrná doba využití procesoru napříč všemi instancemi fondu pracovních procesů.|Instance|
|MemoryPercentage|Yes|Procento paměti|Procento|Průměr|Průměrná paměť využitá ve všech instancích fondu pracovních procesů.|Instance|
|WorkersAvailable|Yes|Zaměstnanci, kteří jsou k dispozici|Počet|Průměr|Zaměstnanci, kteří jsou k dispozici|Žádné dimenze|
|WorkersTotal|Yes|Celkový počet pracovníků|Počet|Průměr|Celkový počet pracovníků|Žádné dimenze|
|WorkersUsed|Yes|Využívané pracovní procesy|Počet|Průměr|Využívané pracovní procesy|Žádné dimenze|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/serverových farem

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Data v|Bajty|Celkem|Průměrná příchozí šířka pásma využitá ve všech instancích plánu.|Instance|
|BytesSent|Yes|Výstupní data|Bajty|Celkem|Průměrná odchozí šířka pásma využitá ve všech instancích plánu.|Instance|
|CpuPercentage|Yes|Procento procesoru|Procento|Průměr|Průměrně využitý procesor napříč všemi instancemi plánu.|Instance|
|DiskQueueLength|Yes|Délka fronty disku|Počet|Průměr|Průměrný počet požadavků na čtení i zápis, které byly zařazeny do fronty úložiště. Maximální délka fronty disku je označením aplikace, která může být zpomalena z důvodu nadměrného vstupně-výstupních operací disku.|Instance|
|HttpQueueLength|Yes|Délka fronty http|Počet|Průměr|Průměrný počet požadavků HTTP, které měly být zařazeny do fronty před jejich splněním. Vysoká nebo rostoucí délka fronty HTTP je příznakem plánu v oblasti vysokého zatížení.|Instance|
|MemoryPercentage|Yes|Procento paměti|Procento|Průměr|Průměrná paměť využitá ve všech instancích plánu.|Instance|
|SocketInboundAll|Yes|SocketInboundAll|Počet|Průměr|SocketInboundAll|Instance|
|SocketLoopback|Yes|SocketLoopback|Počet|Průměr|SocketLoopback|Instance|
|SocketOutboundAll|Yes|SocketOutboundAll|Počet|Průměr|SocketOutboundAll|Instance|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Počet|Průměr|SocketOutboundEstablished|Instance|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Počet|Průměr|SocketOutboundTimeWait|Instance|
|TcpCloseWait|Yes|Ukončení čekání protokolu TCP|Počet|Průměr|Ukončení čekání protokolu TCP|Instance|
|TcpClosing|Yes|Ukončení protokolu TCP|Počet|Průměr|Ukončení protokolu TCP|Instance|
|TcpEstablished|Yes|TCP – vytvořeno|Počet|Průměr|TCP – vytvořeno|Instance|
|TcpFinWait1|Yes|TCP FIN – čekání 1|Počet|Průměr|TCP FIN – čekání 1|Instance|
|TcpFinWait2|Yes|TCP FIN – čekání 2|Počet|Průměr|TCP FIN – čekání 2|Instance|
|TcpLastAck|Yes|Poslední potvrzení TCP|Počet|Průměr|Poslední potvrzení TCP|Instance|
|TcpSynReceived|Yes|Přijato TCP syn|Počet|Průměr|Přijato TCP syn|Instance|
|TcpSynSent|Yes|Odesláno TCP syn|Počet|Průměr|Odesláno TCP syn|Instance|
|TcpTimeWait|Yes|Doba čekání protokolu TCP|Počet|Průměr|Doba čekání protokolu TCP|Instance|


## <a name="microsoftwebsites"></a>Microsoft. Web/weby

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Připojení|Počet|Průměr|Počet vázaných soketů existujících v izolovaném prostoru (w3wp.exe a jeho podřízených procesech). Vázaný soket se vytvoří voláním rozhraní API BIND ()/Connect () a zůstane až do chvíle, kdy je tento soket uzavřený pomocí CloseHandle ()/closesocket ().|Instance|
|AverageMemoryWorkingSet|Yes|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná velikost paměti, kterou aplikace používá, v megabajtech (MiB).|Instance|
|AverageResponseTime|Yes|Průměrná doba odezvy (nepoužívané)|Sekundy|Průměr|Průměrná doba, jakou trvalo, aby aplikace sloužila požadavkům (v sekundách)|Instance|
|BytesReceived|Yes|Data v|Bajty|Celkem|Množství příchozí šířky pásma spotřebované aplikací v souboru MiB.|Instance|
|BytesSent|Yes|Výstupní data|Bajty|Celkem|Velikost odchozí šířky pásma spotřebované aplikací v souboru MiB.|Instance|
|CpuTime|Yes|Čas procesoru|Sekundy|Celkem|Množství procesoru spotřebovaného aplikací v řádu sekund Pro další informace o této metrikě. Nedá se použít pro Azure Functions. Viz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (čas procesoru vs. procento procesoru).|Instance|
|CurrentAssemblies|Yes|Aktuální sestavení|Počet|Průměr|Aktuální počet sestavení načtených napříč všemi doménami AppDomain v této aplikaci.|Instance|
|FileSystemUsage|Yes|Využití systému souborů|Bajty|Průměr|Procento kvóty systému souborů spotřebované aplikací|Žádné dimenze|
|FunctionExecutionCount|Yes|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce K dispozici pouze pro Azure Functions.|Instance|
|FunctionExecutionUnits|Yes|Jednotky spuštění funkce|Počet|Celkem|Jednotky spuštění funkce. K dispozici pouze pro Azure Functions.|Instance|
|Gen0Collections|Yes|Generace paměti gen 0|Počet|Celkem|Počet, kolikrát jsou objekty generace 0 od spuštění procesu aplikace shromažďovány z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|Instance|
|Gen1Collections|Yes|Generace paměti 1. generace|Počet|Celkem|Kolikrát jsou objekty generace 1 od spuštění procesu aplikace uvolněny z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|Instance|
|Gen2Collections|Yes|Uvolňování paměti 2. generace|Počet|Celkem|Kolikrát jsou objekty generace 2 od spuštění procesu aplikace uvolněny z paměti.|Instance|
|Handles|Yes|Počet popisovačů|Počet|Průměr|Celkový počet popisovačů aktuálně otevřených procesem aplikace.|Instance|
|HealthCheckStatus|Yes|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|Http101|Yes|Http 101|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 101.|Instance|
|Http2xx|Yes|2xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 200, ale < 300.|Instance|
|Http3xx|Yes|3xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 300, ale < 400.|Instance|
|Http401|Yes|HTTP 401|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 401|Instance|
|Http403|Yes|HTTP 403|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 403|Instance|
|Http404|Yes|HTTP 404|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 404|Instance|
|Http406|Yes|Http 406|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 406|Instance|
|Http4xx|Yes|4xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 400, ale < 500.|Instance|
|Http5xx|Yes|Chyby serveru http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 500, ale < 600.|Instance|
|HttpResponseTime|Yes|Doba odezvy|Sekundy|Průměr|Doba, kterou aplikace bude obsluhovat požadavky (v sekundách).|Instance|
|IoOtherBytesPerSecond|Yes|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace vydává bajty, do vstupně-výstupních operací, které neobsahují data, jako jsou například operace řízení.|Instance|
|IoOtherOperationsPerSecond|Yes|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|Frekvence, s jakou proces aplikace vystavuje vstupně-výstupní operace, které nejsou operacemi čtení nebo zápisu.|Instance|
|IoReadBytesPerSecond|Yes|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace čte bajty z vstupně-výstupních operací.|Instance|
|IoReadOperationsPerSecond|Yes|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace vydává vstupně-výstupní operace čtení|Instance|
|IoWriteBytesPerSecond|Yes|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Rychlost, kterou proces aplikace zapisuje bajty na vstupně-výstupní operace.|Instance|
|IoWriteOperationsPerSecond|Yes|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Frekvence, s jakou proces aplikace vydává vstupně-výstupní operace zápisu.|Instance|
|MemoryWorkingSet|Yes|Pracovní sada paměti|Bajty|Průměr|Aktuální velikost paměti, kterou aplikace používá, v souboru MiB.|Instance|
|PrivateBytes|Yes|Soukromé bajty|Bajty|Průměr|Soukromé bajty jsou aktuální velikost (v bajtech) paměti, kterou proces aplikace přidělen, který nelze sdílet s jinými procesy.|Instance|
|Žádosti|Yes|Žádosti|Počet|Celkem|Celkový počet požadavků bez ohledu na výsledný kód stavu HTTP.|Instance|
|RequestsInApplicationQueue|Yes|Žádosti ve frontě aplikací|Počet|Průměr|Počet požadavků ve frontě žádostí aplikace.|Instance|
|Vlákna|Yes|Počet vláken|Počet|Průměr|Počet vláken, která jsou aktuálně aktivní v procesu aplikace.|Instance|
|TotalAppDomains|Yes|Celkový počet domén aplikace|Počet|Průměr|Aktuální počet objektů třídy AppDomains zavedených v této aplikaci.|Instance|
|TotalAppDomainsUnloaded|Yes|Celkový počet uvolněných domén aplikace|Počet|Průměr|Celkový počet objektů třídy AppDomains uvolněných od spuštění aplikace.|Instance|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Připojení|Počet|Průměr|Počet vázaných soketů existujících v izolovaném prostoru (w3wp.exe a jeho podřízených procesech). Vázaný soket se vytvoří voláním rozhraní API BIND ()/Connect () a zůstane až do chvíle, kdy je tento soket uzavřený pomocí CloseHandle ()/closesocket ().|Instance|
|AverageMemoryWorkingSet|Yes|Průměrná pracovní sada paměti|Bajty|Průměr|Průměrná velikost paměti, kterou aplikace používá, v megabajtech (MiB).|Instance|
|AverageResponseTime|Yes|Průměrná doba odezvy (nepoužívané)|Sekundy|Průměr|Průměrná doba, jakou trvalo, aby aplikace sloužila požadavkům (v sekundách)|Instance|
|BytesReceived|Yes|Data v|Bajty|Celkem|Množství příchozí šířky pásma spotřebované aplikací v souboru MiB.|Instance|
|BytesSent|Yes|Výstupní data|Bajty|Celkem|Velikost odchozí šířky pásma spotřebované aplikací v souboru MiB.|Instance|
|CpuTime|Yes|Čas procesoru|Sekundy|Celkem|Množství procesoru spotřebovaného aplikací v řádu sekund Pro další informace o této metrikě. Nedá se použít pro Azure Functions. Viz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (čas procesoru vs. procento procesoru).|Instance|
|CurrentAssemblies|Yes|Aktuální sestavení|Počet|Průměr|Aktuální počet sestavení načtených napříč všemi doménami AppDomain v této aplikaci.|Instance|
|FileSystemUsage|Yes|Využití systému souborů|Bajty|Průměr|Procento kvóty systému souborů spotřebované aplikací|Žádné dimenze|
|FunctionExecutionCount|Yes|Počet spuštění funkce|Počet|Celkem|Počet spuštění funkce|Instance|
|FunctionExecutionUnits|Yes|Jednotky spuštění funkce|Počet|Celkem|Jednotky spuštění funkce|Instance|
|Gen0Collections|Yes|Generace paměti gen 0|Počet|Celkem|Počet, kolikrát jsou objekty generace 0 od spuštění procesu aplikace shromažďovány z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|Instance|
|Gen1Collections|Yes|Generace paměti 1. generace|Počet|Celkem|Kolikrát jsou objekty generace 1 od spuštění procesu aplikace uvolněny z paměti. GC vyšší generace zahrnuje všechny GC nižší generace.|Instance|
|Gen2Collections|Yes|Uvolňování paměti 2. generace|Počet|Celkem|Kolikrát jsou objekty generace 2 od spuštění procesu aplikace uvolněny z paměti.|Instance|
|Handles|Yes|Počet popisovačů|Počet|Průměr|Celkový počet popisovačů aktuálně otevřených procesem aplikace.|Instance|
|HealthCheckStatus|Yes|Stav kontroly stavu|Počet|Průměr|Stav kontroly stavu|Instance|
|Http101|Yes|Http 101|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 101.|Instance|
|Http2xx|Yes|2xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 200, ale < 300.|Instance|
|Http3xx|Yes|3xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 300, ale < 400.|Instance|
|Http401|Yes|HTTP 401|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 401|Instance|
|Http403|Yes|HTTP 403|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 403|Instance|
|Http404|Yes|HTTP 404|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 404|Instance|
|Http406|Yes|Http 406|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP 406|Instance|
|Http4xx|Yes|4xx http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 400, ale < 500.|Instance|
|Http5xx|Yes|Chyby serveru http|Počet|Celkem|Počet požadavků, které mají za následek stavový kód HTTP = 500, ale < 600.|Instance|
|HttpResponseTime|Yes|Doba odezvy|Sekundy|Průměr|Doba, kterou aplikace bude obsluhovat požadavky (v sekundách).|Instance|
|IoOtherBytesPerSecond|Yes|IO – ostatní bajty za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace vydává bajty, do vstupně-výstupních operací, které neobsahují data, jako jsou například operace řízení.|Instance|
|IoOtherOperationsPerSecond|Yes|V/v – ostatní operace za sekundu|BytesPerSecond|Celkem|Frekvence, s jakou proces aplikace vystavuje vstupně-výstupní operace, které nejsou operacemi čtení nebo zápisu.|Instance|
|IoReadBytesPerSecond|Yes|Bajty čtení v/v za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace čte bajty z vstupně-výstupních operací.|Instance|
|IoReadOperationsPerSecond|Yes|Vstupně-výstupní operace čtení za sekundu|BytesPerSecond|Celkem|Rychlost, s jakou proces aplikace vydává vstupně-výstupní operace čtení|Instance|
|IoWriteBytesPerSecond|Yes|Vstupně-výstupní bajty zápisu za sekundu|BytesPerSecond|Celkem|Rychlost, kterou proces aplikace zapisuje bajty na vstupně-výstupní operace.|Instance|
|IoWriteOperationsPerSecond|Yes|Vstupně-výstupní operace zápisu za sekundu|BytesPerSecond|Celkem|Frekvence, s jakou proces aplikace vydává vstupně-výstupní operace zápisu.|Instance|
|MemoryWorkingSet|Yes|Pracovní sada paměti|Bajty|Průměr|Aktuální velikost paměti, kterou aplikace používá, v souboru MiB.|Instance|
|PrivateBytes|Yes|Soukromé bajty|Bajty|Průměr|Soukromé bajty jsou aktuální velikost (v bajtech) paměti, kterou proces aplikace přidělen, který nelze sdílet s jinými procesy.|Instance|
|Žádosti|Yes|Žádosti|Počet|Celkem|Celkový počet požadavků bez ohledu na výsledný kód stavu HTTP.|Instance|
|RequestsInApplicationQueue|Yes|Žádosti ve frontě aplikací|Počet|Průměr|Počet požadavků ve frontě žádostí aplikace.|Instance|
|Vlákna|Yes|Počet vláken|Počet|Průměr|Počet vláken, která jsou aktuálně aktivní v procesu aplikace.|Instance|
|TotalAppDomains|Yes|Celkový počet domén aplikace|Počet|Průměr|Aktuální počet objektů třídy AppDomains zavedených v této aplikaci.|Instance|
|TotalAppDomainsUnloaded|Yes|Celkový počet uvolněných domén aplikace|Počet|Průměr|Celkový počet objektů třídy AppDomains uvolněných od spuštění aplikace.|Instance|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Výstupní data|Bajty|Celkem|BytesSent|Instance|
|FunctionErrors|Yes|FunctionErrors|Počet|Celkem|FunctionErrors|Instance|
|FunctionHits|Yes|FunctionHits|Počet|Celkem|FunctionHits|Instance|
|SiteErrors|Yes|SiteErrors|Počet|Celkem|SiteErrors|Instance|
|SiteHits|Yes|SiteHits|Počet|Celkem|SiteHits|Instance|

## <a name="next-steps"></a>Další kroky

- [Přečtěte si o metrikách v Azure Monitor](../data-platform.md)
- [Vytváření upozornění na metriky](../alerts/alerts-overview.md)
- [Exportovat metriky do úložiště, centra událostí nebo Log Analytics](../essentials/platform-logs-overview.md)
