---
title: Metriky založené na protokolu Azure Application Insights | Microsoft Docs
description: V tomto článku jsou uvedené metriky Azure Application Insights s podporovanými agregacemi a dimenzemi. Podrobnosti o metrikách založených na protokolu zahrnují základní příkazy dotazů Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 400f239f3e7b736196bf950e81148fa2e39aca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609142"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights metriky založené na protokolu

Application Insights metriky založené na protokolu vám umožní analyzovat stav monitorovaných aplikací, vytvářet výkonné řídicí panely a konfigurovat výstrahy. Existují dva typy metrik:

* [Metriky založené na protokolu](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) na pozadí jsou přeloženy do [dotazů Kusto](/azure/kusto/query/) z uložených událostí.
* [Standardní metriky](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) se ukládají jako předem agregované časové řady.

Vzhledem k tomu, že *Standardní metriky* jsou během shromažďování předem shrnuty, mají lepší výkon v době dotazu. Díky tomu je lepší volbou pro řídicí panely a výstrahy v reálném čase. *Metriky založené na protokolech* mají více dimenzí, díky kterým jsou nadřazené volbou pro analýzu dat a pro diagnostiku ad-hoc. [Selektor oboru názvů](../platform/metrics-getting-started.md#create-your-first-metric-chart) použijte k přepínání mezi standardními metrikami založenými na protokolech v [Průzkumníkovi metrik](../platform/metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretace a používání dotazů z tohoto článku

V tomto článku jsou uvedeny metriky s podporovanými agregacemi a dimenzemi. Podrobnosti o metrikách založených na protokolu zahrnují základní příkazy dotazů Kusto. Pro přehlednost každý dotaz používá výchozí hodnoty pro časovou členitost, typ grafu a někdy rozdělení dimenze, které zjednodušují použití dotazu v Log Analytics bez nutnosti úpravy.

Když vykreslíte stejnou metriku v [Průzkumníkovi metrik](../platform/metrics-getting-started.md), neexistují žádné výchozí hodnoty – dotaz se dynamicky upraví na základě nastavení grafu:

- Vybraný **časový rozsah** je přeložen do další klauzule *WHERE timestamp...* , aby bylo možné vybrat pouze události z vybraného časového rozsahu. Například graf zobrazující data za posledních 24 hodin, dotaz obsahuje *| místo > před časovým razítkem (24 h)*.

- Vybraná **Časová členitost** je vložena do konečného *souhrnu... podle klauzule bin (timestamp, [časový interval])* .

- Všechny vybrané dimenze **filtru** jsou přeloženy do dalších klauzulí *WHERE* .

- Vybraná dimenze **rozděleného grafu** se převede na další vlastnost Shrnutí. Například pokud rozdělíte graf podle *umístění* a sekreslíte pomocí členitosti s časem 5 minut, je shrnuta klauzule *Shrnutí* *... po přihrádce (časové razítko, 5 m), umístění*.

> [!NOTE]
> Pokud začínáte s dotazovacím jazykem Kusto, začnete zkopírováním a vložením příkazů Kusto do podokna dotazu Log Analytics bez provedení jakýchkoli úprav. Kliknutím na tlačítko **Spustit** zobrazíte základní graf. Jak začínáte pochopit syntaxi dotazovacího jazyka, můžete začít provádět malé úpravy a vidět dopad změny. Seznámení s vlastními daty představuje skvělý způsob, jak začít s plným výkonem [Log Analytics](../log-query/log-analytics-tutorial.md) a [Azure monitor](../overview.md).

## <a name="availability-metrics"></a>Metriky dostupnosti

Metriky v kategorii dostupnosti umožňují zobrazit stav webové aplikace, jak je zjištěno od bodů po celém světě. [Nakonfigurujte testy dostupnosti](../app/monitor-web-app-availability.md) tak, aby se v této kategorii spouštěly všechny metriky.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostupnost (availabilityResults/availabilityPercentage)
Metrika *dostupnosti* zobrazuje procento běhů webového testu, u kterých nebyly zjištěny žádné problémy. Nejnižší možná hodnota je 0, což znamená, že všechny spuštěné webové testy se nezdařily. Hodnota 100 znamená, že všechna spuštění webového testu prošla ověřovacími kritérii.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Procento|Průměr|Umístění pro spuštění, název testu|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Doba trvání testu dostupnosti (availabilityResults/Duration)

Metrika *doby trvání testu dostupnosti* ukazuje, kolik času trvalo spuštění webového testu. U [webových testů s více kroky](../app/availability-multistep.md)odráží metrika celkovou dobu provádění všech kroků.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Milisekund|Average, min, Max|Umístění pro spuštění, název testu, výsledek testu

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testy dostupnosti (availabilityResults/Count)

Metrika *testů dostupnosti* odráží počet webových testů, které jsou spouštěny pomocí Azure monitor.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Počet|Počet|Umístění pro spuštění, název testu, výsledek testu|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metriky prohlížeče

Metriky prohlížeče jsou shromažďovány sadou Application Insights JavaScript SDK z reálných prohlížečů pro koncové uživatele. Poskytují skvělé přehledy o zkušenostech uživatelů s vaší webovou aplikací. Metriky prohlížeče nejsou většinou vzorkované, což znamená, že poskytují vyšší přesnost čísel využití ve srovnání se metrikami na straně serveru, která může být vydělena vzorkováním.

> [!NOTE]
> Aby bylo možné shromažďovat metriky prohlížeče, musí být aplikace instrumentovaná pomocí [Application Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Doba načítání stránky v prohlížeči (browserTimings/totalDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Doba zpracování klienta (browserTiming/processingDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Doba připojení k síti načítání stránky (browserTimings/networkDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Doba přijetí odezvy (browserTimings/receiveDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Čas žádosti o odeslání (browserTimings/sendDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Average, min, Max|Žádné|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metriky selhání

Metriky při **selhání** zobrazují problémy se zpracováním požadavků, volání závislostí a vyvolaných výjimek.

### <a name="browser-exceptions-exceptionsbrowser"></a>Výjimky prohlížeče (výjimky/prohlížeč)

Tato metrika odráží počet vyvolaných výjimek z kódu aplikace spuštěného v prohlížeči. Metrika obsahuje pouze výjimky, které jsou sledovány pomocí ```trackException()``` volání rozhraní Application Insights API.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Žádné|Verze založená na protokolu používá agregaci **Sum** .|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Selhání volání závislostí (závislosti/neúspěšné)

Počet neúspěšných volání závislostí

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Žádné|Verze založená na protokolu používá agregaci **Sum** .|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Výjimky (výjimky/počet)

Pokaždé, když zaznamenáte výjimku pro Application Insights, dojde k volání [metody trackException ()](../app/api-custom-events-metrics.md#trackexception) sady SDK. Metrika výjimek zobrazuje počet protokolovaných výjimek.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Název cloudové role, instance cloudové role, typ zařízení|Verze založená na protokolu používá agregaci **Sum** .|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Neúspěšné žádosti (požadavky/neúspěšné)

Počet sledovaných požadavků serveru, které byly označeny jako *neúspěšné*. Ve výchozím nastavení sada Application Insights SDK automaticky označí každou žádost serveru, která vrátila kód odpovědi HTTP 5xx nebo 4xx jako neúspěšný požadavek. Tuto logiku můžete přizpůsobit úpravou vlastnosti  *úspěch* položky telemetrie žádosti ve [vlastním inicializátoru telemetrie](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Instance cloudové role, název cloudové role, skutečný nebo syntetický provoz, požadavky na výkon, kód odpovědi|Verze založená na protokolu používá agregaci **Sum** .|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Výjimky serveru (výjimky/Server)

Tato metrika znázorňuje počet výjimek serveru.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Název cloudové role, instance cloudové role|Verze založená na protokolu používá agregaci **Sum** .|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Čítače výkonu

Pomocí metrik v kategorii **čítače výkonu** můžete získat přístup k [čítačům výkonu systému shromážděným pomocí Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostupná paměť (čítače výkonu/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Míra výjimek (čítače výkonu/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Doba provádění požadavku HTTP (čítače výkonu/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Míra požadavků HTTP (čítače výkonu/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Požadavky HTTP ve frontě aplikací (čítače výkonu/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>PROCESOR procesu (čítače výkonu/processCpuPercentage)

Metrika ukazuje, kolik celkové kapacity procesoru spotřebovává proces, který je hostitelem monitorované aplikace.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Average, min, Max|Instance cloudové role

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Frekvence v/v procesu (čítače výkonu/processIOBytesPerSecond)

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajtů za sekundu|Average, min, Max|Instance cloudové role

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Zpracovat soukromé bajty (čítače výkonu/processPrivateBytes)

Množství nesdílené paměti, které monitorovaný proces přidělen pro svá data.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajty|Average, min, Max|Instance cloudové role

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Čas procesoru (čítače výkonu/processorCpuPercentage)

Spotřeba procesoru u *všech* procesů spuštěných v instanci monitorovaného serveru.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Average, min, Max|Instance cloudové role

>[!NOTE]
> Pro aplikace hostované v Azure App Services není dostupná časová metrika procesoru. Pomocí metriky  [procesoru procesu](#process-cpu-performancecountersprocesscpupercentage) můžete sledovat využití procesoru webových aplikací hostovaných ve službě App Services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metriky serveru

### <a name="dependency-calls-dependenciescount"></a>Volání závislostí (závislosti/počet)

Tato metrika je ve vztahu k počtu volání závislostí.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Trvání závislosti (závislosti/trvání)

Tato metrika odkazuje na dobu trvání volání závislostí.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Žádosti serveru (požadavky/počet)

Tato metrika odráží počet příchozích požadavků serveru, které byly přijaty vaší webovou aplikací.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Doba odezvy serveru (žádosti o dobu trvání)

Tato metrika odráží dobu, kterou trvalo, aby servery zpracovaly příchozí požadavky.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metriky využití

### <a name="page-view-load-time-pageviewsduration"></a>Doba načítání zobrazení stránky (pageViews/Duration)

Tato metrika odkazuje na množství času, který trvalo, aby se PageView události načetly.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Zobrazení stránky (pageViews/Count)

Počet událostí PageView protokolovaných pomocí rozhraní API TrackPageView () Application Insights.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Relace (relace/počet)

Tato metrika odkazuje na počet jedinečných identifikátorů relací.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Trasování (Traces/Count)

Počet příkazů trasování protokolovaných pomocí volání TrackTrace () Application Insights rozhraní API.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Uživatelé (uživatelé/počet)

Počet různých uživatelů, kteří získali aplikaci. Přesnost této metriky může být významně ovlivněna použitím vzorkování a filtrování telemetrie.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Uživatelé, ověření (uživatelé/ověřeno)

Počet různých uživatelů, kteří se do vaší aplikace ověřili.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```