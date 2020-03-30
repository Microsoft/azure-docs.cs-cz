---
title: Metriky založené na protokolu Azure Application Insights | Dokumenty společnosti Microsoft
description: Tento článek uvádí metriky Azure Application Insights s podporovanými agregacemi a dimenzemi. Podrobnosti o metriky založené na protokolu patří základní příkazy dotazu Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664911"
---
# <a name="application-insights-log-based-metrics"></a>Metriky založené na protokolu Application Insights

Metriky založené na protokolu Application Insights umožňují analyzovat stav monitorovaných aplikací, vytvářet výkonné řídicí panely a konfigurovat výstrahy. Existují dva druhy metrik:

* [Metriky založené na protokolu](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) za scénou jsou přeloženy do [dotazů Kusto](https://docs.microsoft.com/azure/kusto/query/) z uložených událostí.
* [Standardní metriky](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) jsou uloženy jako předem agregované časové řady.

Vzhledem k tomu, *že standardní metriky* jsou předem agregovány během shromažďování, mají lepší výkon v době dotazu. Díky tomu jsou lepší volbou pro řídicí panel a v reálném čase. *Metriky založené na protokolu* mají více dimenzí, což z nich činí vynikající možnost pro analýzu dat a diagnostiku ad hoc. Pomocí [voliče oboru názvů](metrics-getting-started.md#create-your-first-metric-chart) můžete přepínat mezi metrikami založenými na protokolu a standardními metrikami v [průzkumníku metrik](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretace a použití dotazů z tohoto článku

V tomto článku jsou uvedeny metriky s podporovanými agregacemi a dimenzemi. Podrobnosti o metriky založené na protokolu patří základní příkazy dotazu Kusto. Pro větší pohodlí každý dotaz používá výchozí hodnoty pro rozlišovací schopnost času, typ grafu a někdy rozdělení dimenze, která zjednodušuje použití dotazu v Log Analytics bez nutnosti změny.

Když [vykreslíte](metrics-getting-started.md)stejnou metriku v průzkumníku metrik , nejsou k dispozici žádné výchozí hodnoty – dotaz se dynamicky upravuje na základě nastavení grafu:

- Vybraný **časový rozsah** je přeložen do další *klauzule where timestamp...* pro výběr událostí pouze z vybraného časového rozsahu. Například graf zobrazující data za posledních 24 hodin, dotaz obsahuje *| kde časové razítko > před (24 h)*.

- Vybraná **časová rozlišovací schopnost** je vložena do konečného *shrnutí ... klauzulí bin(timestamp, [time grain]).*

- Všechny vybrané dimenze **filtru** jsou přeloženy do *dalších klauzulí.*

- Vybraná dimenze **Rozděleného grafu** je přeložena do extra sumarizovat vlastnost. Pokud například rozdělíte graf podle *umístění*a vykreslíte pomocí 5minutové časové granularity, shrne se shrnutá klauzule *souhrnu* *... bin (časové razítko, 5 m), umístění*.

> [!NOTE]
> Pokud s dotazovacím jazykem Kusto tečujete, začnete kopírováním a vložením příkazů Kusto do podokna dotazů Log Analytics bez jakýchkoli úprav. Chcete-li zobrazit základní graf, klepněte na tlačítko **Spustit.** Jakmile začnete chápat syntaxi dotazovacího jazyka, můžete začít provádět malé úpravy a vidět dopad změny. Zkoumání vlastních dat je skvělý způsob, jak začít realizovat plný výkon [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) a [Azure Monitor](../../azure-monitor/overview.md).

## <a name="availability-metrics"></a>Metriky dostupnosti

Metriky v kategorii Dostupnost umožňují zobrazit stav webové aplikace, jak je pozorováno z bodů po celém světě. [Nakonfigurujte testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) tak, aby začaly používat všechny metriky z této kategorie.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostupnost (dostupnostVýsledky/availabilityPercentage)
Metrika *dostupnost* zobrazuje procento spuštění webového testu, které nezjistilo žádné problémy. Nejnižší možná hodnota je 0, což znamená, že všechny webové testovací spuštění se nezdařilo. Hodnota 100 znamená, že všechny webové testovací spuštění prošel ověřovací kritéria.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Procento|Průměr|Spustit umístění, název testu|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Doba trvání testu dostupnosti (dostupnostVýsledky/doba trvání)

Metrika *trvání testu dostupnosti* ukazuje, kolik času trvalo spuštění webového testu. Pro [vícekrokové webové testy](../../azure-monitor/app/availability-multistep.md)metrika odráží celkovou dobu provádění všech kroků.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Milisekund|Průměr, Min, Max|Spustit umístění, Název testu, Výsledek testu

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testy dostupnosti (dostupnostVýsledky/počet)

Metrika *testů dostupnosti* odráží počet webových testů spuštěných službou Azure Monitor.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|---|---|---|
|Počet|Počet|Spustit umístění, Název testu, Výsledek testu|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metriky prohlížeče

Metriky prohlížeče shromažďuje sada JavaScript SDK Application Insights z reálných prohlížečů koncových uživatelů. Poskytují skvělý přehled o zkušenostech uživatelů s vaší webovou aplikací. Metriky prohlížeče obvykle nejsou vzorkovány, což znamená, že poskytují vyšší přesnost počtu využití ve srovnání s metrikami na straně serveru, které mohou být zkosené vzorkováním.

> [!NOTE]
> Chcete-li shromažďovat metriky prohlížeče, musí být vaše aplikace instrumentována sadou [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Doba načítání stránky prohlížeče (browserTimings/totalDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Průměr, Min, Max|Žádný|

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
|Milisekund|Průměr, Min, Max|Žádný|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Čas připojení sítě načítání stránky (browserTimings/networkDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Průměr, Min, Max|Žádný|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Příjem doby odezvy (browserTimings/receiveDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Průměr, Min, Max|Žádný|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Odeslat čas požadavku (browserTimings/sendDuration)

|Jednotka měření|Podporované agregace|Předem agregované dimenze|
|---|---|---|
|Milisekund|Průměr, Min, Max|Žádný|

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

Metriky v **selhání** zobrazit problémy se zpracováním požadavků, volání závislostí a vyvolání výjimky.

### <a name="browser-exceptions-exceptionsbrowser"></a>Výjimky prohlížeče (výjimky/prohlížeč)

Tato metrika odráží počet vyzdvižených výjimek z kódu aplikace spuštěného v prohlížeči. Do metriky jsou zahrnuty ```trackException()``` pouze výjimky, které jsou sledovány pomocí volání rozhraní API Application Insights.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Žádný|Verze založená na protokolu používá agregaci **součtů.**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Selhání volání závislostí (závislosti/selhání)

Počet neúspěšných volání závislostí.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Žádný|Verze založená na protokolu používá agregaci **součtů.**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Výjimky (výjimky/počet)

Pokaždé, když při přihlášení výjimku Application Insights, je volání [trackException() metoda](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) SDK. Metrika Výjimky zobrazuje počet protokolovaných výjimek.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Název role cloudu, instance role Cloud, typ zařízení|Verze založená na protokolu používá agregaci **součtů.**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Neúspěšné požadavky (požadavky/neúspěšné)

Počet sledovaných požadavků serveru, které byly označeny jako *neúspěšné*. Ve výchozím nastavení sada Application Insights SDK automaticky označí každý požadavek serveru, který vrátil kód odpovědi HTTP 5xx nebo 4xx jako neúspěšný požadavek. Tuto logiku můžete přizpůsobit úpravou vlastnosti *úspěchu* položky telemetrie požadavku ve [vlastním inicializátoru telemetrie](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Instance role cloudu, název role cloudu, reálný nebo syntetický provoz, výkon požadavku, kód odpovědi|Verze založená na protokolu používá agregaci **součtů.**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Výjimky serveru (výjimky/server)

Tato metrika zobrazuje počet výjimek serveru.

|Jednotka měření|Podporované agregace|Předem agregované dimenze|Poznámky|
|---|---|---|---|
|Počet|Počet|Název role cloudu, instance role Cloud|Verze založená na protokolu používá agregaci **součtů.**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Čítače výkonu

Pomocí metrik v kategorii **Čítače výkonu** můžete získat přístup k [výkonovým čítačům systému shromážděným aplikací Insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostupná paměť (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Míra výjimek (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Doba spuštění požadavku HTTP (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Rychlost požadavků HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP požadavky ve frontě aplikací (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Procesní procesor (performanceCounters/processCpuPercentage)

Metrika ukazuje, jak velkou část celkové kapacity procesoru spotřebovává proces, který hostuje vaši monitorovanou aplikaci.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Průměr, Min, Max|Instance role cloudu

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Rychlost vstupně-to-vi procesu (performanceCounters/processIOBytesPerSecond)

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajty za sekundu|Průměr, Min, Max|Instance role cloudu

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Proces soukromé bajty (performanceCounters/processPrivateBytes)

Množství nesdílené paměti, kterou sledovaný proces přidělil pro svá data.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Bajty|Průměr, Min, Max|Instance role cloudu

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Čas procesoru (performanceCounters/processorCpuPercentage)

Využití procesoru *všemi* procesy spuštěnými na monitorované instanci serveru.

|Jednotka měření|Podporované agregace|Podporované dimenze|
|---|---|---|
|Procento|Průměr, Min, Max|Instance role cloudu

>[!NOTE]
> Metrika času procesoru není dostupná pro aplikace hostované ve službách Azure App Services. Pomocí metriky [Proces procesoru](#process-cpu-performancecountersprocesscpupercentage) můžete sledovat využití procesoru webových aplikací hostovaných ve službách App Services.

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

### <a name="dependency-duration-dependenciesduration"></a>Doba trvání závislostí (závislosti/doba trvání)

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

### <a name="server-requests-requestscount"></a>Serverové požadavky (požadavky/počet)

Tato metrika odráží počet příchozích požadavků serveru, které byly přijaty vaší webovou aplikací.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Doba odezvy serveru (požadavky/doba trvání)

Tato metrika odráží dobu, kterou servery zpracovaly ke zpracování příchozích požadavků.

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

### <a name="page-view-load-time-pageviewsduration"></a>Doba načítání zobrazení stránky (zobrazení stránky/doba trvání)

Tato metrika odkazuje na dobu trvalo pageview události načíst.

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

### <a name="page-views-pageviewscount"></a>Zobrazení stránky (zobrazení/počet stránek)

Počet událostí PageView zaznamenaných pomocí rozhraní Api Pro přehledy aplikací TrackPageView().

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Relace (relace/počet)

Tato metrika odkazuje na počet různých ID relace.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Stopy (stopy/počet)

Počet příkazů trasování protokolovaných pomocí volání rozhraní API Rozhraní API TrackTrace() Application Insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Uživatelé (uživatelé/počet)

Počet různých uživatelů, kteří přistupovali k vaší aplikaci. Přesnost této metriky může být významně ovlivněna pomocí vzorkování telemetrie a filtrování.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Uživatelé, Ověřeno (uživatelé/ověřeno)

Počet různých uživatelů, kteří se ověřili do vaší aplikace.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
