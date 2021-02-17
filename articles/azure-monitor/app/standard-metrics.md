---
title: Metriky Azure Application Insights Standard | Microsoft Docs
description: V tomto článku jsou uvedené metriky Azure Application Insights s podporovanými agregacemi a dimenzemi.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572360"
---
# <a name="application-insights-standard-metrics"></a>Application Insights standardní metriky

Standardní metriky jsou během shromažďování předem shrnuty, mají lepší výkon v době dotazu. Díky tomu je to nejlepší volbou pro řídicí panely a výstrahy v reálném čase.

## <a name="availability-metrics"></a>Metriky dostupnosti

Metriky v kategorii dostupnosti umožňují zobrazit stav webové aplikace, jak je zjištěno od bodů po celém světě. [Nakonfigurujte testy dostupnosti](../app/monitor-web-app-availability.md) tak, aby se v této kategorii spouštěly všechny metriky.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostupnost (availabilityResults/availabilityPercentage)
Metrika *dostupnosti* zobrazuje procento běhů webového testu, u kterých nebyly zjištěny žádné problémy. Nejnižší možná hodnota je 0, což znamená, že všechny spuštěné webové testy se nezdařily. Hodnota 100 znamená, že všechna spuštění webového testu prošla ověřovacími kritérii.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Procento|Průměr|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Doba trvání testu dostupnosti (availabilityResults/Duration)

Metrika *doby trvání testu dostupnosti* ukazuje, kolik času trvalo spuštění webového testu. U [webových testů s více kroky](../app/availability-multistep.md)odráží metrika celkovou dobu provádění všech kroků.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Milisekund|Average, min, Max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Testy dostupnosti (availabilityResults/Count)

Metrika *testů dostupnosti* odráží počet webových testů, které jsou spouštěny pomocí Azure monitor.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Počet|Počet|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Metriky prohlížeče

Metriky prohlížeče jsou shromažďovány sadou Application Insights JavaScript SDK z reálných prohlížečů pro koncové uživatele. Poskytují skvělé přehledy o zkušenostech uživatelů s vaší webovou aplikací. Metriky prohlížeče nejsou většinou vzorkované, což znamená, že poskytují vyšší přesnost čísel využití ve srovnání se metrikami na straně serveru, která může být vydělena vzorkováním.

> [!NOTE]
> Aby bylo možné shromažďovat metriky prohlížeče, musí být aplikace instrumentovaná pomocí [Application Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Doba načítání stránky v prohlížeči (browserTimings/totalDuration)

|Jednotka měření|Podporované agregace| Podporované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Doba zpracování klienta (browserTiming/processingDuration)

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Doba připojení k síti načítání stránky (browserTimings/networkDuration)

|Jednotka měření|Podporované agregace| Podporované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Doba přijetí odezvy (browserTimings/receiveDuration)

|Jednotka měření|Podporované agregace| Podporované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

### <a name="send-request-time-browsertimingssendduration"></a>Čas žádosti o odeslání (browserTimings/sendDuration)

|Jednotka měření|Podporované agregace| Podporované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

## <a name="failure-metrics"></a>Metriky selhání

Metriky při **selhání** zobrazují problémy se zpracováním požadavků, volání závislostí a vyvolaných výjimek.

### <a name="browser-exceptions-exceptionsbrowser"></a>Výjimky prohlížeče (výjimky/prohlížeč)

Tato metrika odráží počet vyvolaných výjimek z kódu aplikace spuštěného v prohlížeči. Metrika obsahuje pouze výjimky, které jsou sledovány pomocí ```trackException()``` volání rozhraní Application Insights API.

|Jednotka měření|Podporované agregace | Podporované dimenze|
|---|---|---|---|
| Počet | Počet | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Selhání volání závislostí (závislosti/neúspěšné)

Počet neúspěšných volání závislostí

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
|Počet|Počet| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Výjimky (výjimky/počet)

Pokaždé, když zaznamenáte výjimku pro Application Insights, dojde k volání [metody trackException ()](../app/api-custom-events-metrics.md#trackexception) sady SDK. Metrika výjimek zobrazuje počet protokolovaných výjimek.

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
|Počet|Počet|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Neúspěšné žádosti (požadavky/neúspěšné)

Počet sledovaných požadavků serveru, které byly označeny jako *neúspěšné*. Ve výchozím nastavení sada Application Insights SDK automaticky označí každou žádost serveru, která vrátila kód odpovědi HTTP 5xx nebo 4xx jako neúspěšný požadavek. Tuto logiku můžete přizpůsobit úpravou vlastnosti  *úspěch* položky telemetrie žádosti ve [vlastním inicializátoru telemetrie](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
|Počet|Počet|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Výjimky serveru (výjimky/Server)

Tato metrika znázorňuje počet výjimek serveru.

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
|Počet|Počet|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Čítače výkonu

Pomocí metrik v kategorii **čítače výkonu** můžete získat přístup k [čítačům výkonu systému shromážděným pomocí Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostupná paměť (čítače výkonu/availableMemory)

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
|Závislá data: megabajtů, gigabajtů|Average, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Míra výjimek (čítače výkonu/exceptionRate)

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
| Počet | Average, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Doba provádění požadavku HTTP (čítače výkonu/requestExecutionTime)

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
| Milisekund | Average, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Míra požadavků HTTP (čítače výkonu/requestsPerSecond)

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
| Počet žádostí za sekundu | Average, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Požadavky HTTP ve frontě aplikací (čítače výkonu/requestsInQueue)

|Jednotka měření|Podporované agregace | Podporované dimenze |
|---|---|---|---|
| Počet | Average, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>PROCESOR procesu (čítače výkonu/processCpuPercentage)

Metrika ukazuje, kolik celkové kapacity procesoru spotřebovává proces, který je hostitelem monitorované aplikace.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Average, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Frekvence v/v procesu (čítače výkonu/processIOBytesPerSecond)

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajtů za sekundu|Average, min, Max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Zpracovat soukromé bajty (čítače výkonu/processPrivateBytes)

Množství nesdílené paměti, které monitorovaný proces přidělen pro svá data.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajty|Average, min, Max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Čas procesoru (čítače výkonu/processorCpuPercentage)

Spotřeba procesoru u *všech* procesů spuštěných v instanci monitorovaného serveru.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Average, min, Max|`Cloud role instance` |

>[!NOTE]
> Pro aplikace hostované v Azure App Services není dostupná časová metrika procesoru. Pomocí metriky  [procesoru procesu](#process-cpu-performancecountersprocesscpupercentage) můžete sledovat využití procesoru webových aplikací hostovaných ve službě App Services.

## <a name="server-metrics"></a>Metriky serveru

### <a name="dependency-calls-dependenciescount"></a>Volání závislostí (závislosti/počet)

Tato metrika je ve vztahu k počtu volání závislostí.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Počet | Počet | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Trvání závislosti (závislosti/trvání)

Tato metrika odkazuje na dobu trvání volání závislostí.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Čas | Average, min, Max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Počet požadavků na server (počet požadavků za sekundu)

Tato metrika odráží počet příchozích požadavků serveru, které byly přijaty vaší webovou aplikací.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Počet | Průměr | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Žádosti serveru (požadavky/počet)

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Počet | Počet | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Doba odezvy serveru (žádosti o dobu trvání)

Tato metrika odráží dobu, kterou trvalo, aby servery zpracovaly příchozí požadavky.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Čas | Average, min, Max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Metriky využití

### <a name="page-view-load-time-pageviewsduration"></a>Doba načítání zobrazení stránky (pageViews/Duration)

Tato metrika odkazuje na množství času, který trvalo, aby se PageView události načetly.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Čas | Average, min, Max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Zobrazení stránky (pageViews/Count)

Počet událostí PageView protokolovaných pomocí rozhraní API TrackPageView () Application Insights.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Počet | Počet | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Trasování (Traces/Count)

Počet příkazů trasování protokolovaných pomocí volání TrackTrace () Application Insights rozhraní API.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
| Počet | Počet | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Další kroky

* Seznamte [se s předem agregovanými metrikami založenými na protokolech](./pre-aggregated-metrics-log-metrics.md).
* [Dotazy a definice metrik založené na protokolu](../essentials/app-insights-metrics.md)