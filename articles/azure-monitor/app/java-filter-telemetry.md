---
title: Filtrovat telemetrii Azure Application Insights do vaší webové aplikace v Javě | Dokumentace Microsoftu
description: Snížení provozu telemetrie se tak filtrování událostí, které není nutné k monitorování.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 81bfd5e0354fefea4690e6871dd5e846f41dc6d8
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980667"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrování telemetrických dat ve vaší webové aplikace v Javě

Filtry poskytují způsob, jak vybrat telemetrická data, která vaše [odesílá webové aplikace v Javě do služby Application Insights](java-get-started.md). Existují některé out-of-the-box filtry, které můžete použít, a můžete taky psát vlastní filtry.

Out-of-the-box filtry zahrnují:

* Trasovat úroveň závažnosti
* Konkrétní adresy URL, klíčová slova nebo kódy odpovědí
* Rychlé odpovědi – to znamená požadavků, u kterých vaše aplikace poslaných rychle
* Názvy konkrétních událostí

> [!NOTE]
> Filtry zkosení metriky vaší aplikace. Například můžete rozhodnout, že pokud chcete diagnostikovat pomalé odezvy, můžete nastavit filtr zahodíte krátké doby odezvy. Musíte však být vědomi, Průměrná doba odezvy hlášené službou Application Insights se pak budou pomalejší než skutečná rychlost a počet požadavků bude menší, než je skutečný počet.
> Pokud je to žádný problém, použijte [vzorkování](../../application-insights/app-insights-sampling.md) místo.

## <a name="setting-filters"></a>Nastavení filtrů

Přidejte soubor ApplicationInsights.xml, `TelemetryProcessors` oddílu jako v tomto příkladu:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Zkontrolujte úplnou sadu předdefinovaných procesory](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Integrované filtry

### <a name="metric-telemetry-filter"></a>Filtr Telemetrie metrik

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Čárkami oddělený seznam názvů vlastní metriky.


### <a name="page-view-telemetry-filter"></a>Filtrování Telemetrie zobrazení stránky

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` – Doba trvání odkazuje na čas potřebný k načtení stránky. Pokud je nastaveno, stránky, které načíst rychleji, než tuto chvíli se nezobrazují.
* `NotNeededNames` -Čárkami oddělený seznam názvů stránky.
* `NotNeededUrls` -Počet fragmentů čárkami oddělený seznam adresy URL. Například `"home"` vyfiltruje všechny stránky, které mají "Domů" v adrese URL.


### <a name="request-telemetry-filter"></a>Filtr Telemetrie požadavku


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Syntetické zdrojový filtr

Odfiltruje všechny telemetrická data, která se mají hodnoty ve vlastnosti SyntheticSource. Patří sem požadavky od robotů, pavouci a testy dostupnosti.

Filtrování telemetrických dat pro všechny syntetické požadavky:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrování telemetrických dat pro konkrétní syntetické zdroje:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` -Čárkami oddělený seznam názvů syntetické zdroje.

### <a name="telemetry-event-filter"></a>Filtr událostí telemetrie

Filtruje vlastní události (zaprotokolované pomocí [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` -Čárkami oddělený seznam názvů událostí.


### <a name="trace-telemetry-filter"></a>Filtrování Telemetrie trasování

Trasování protokolů filtry (zaprotokolované pomocí [použitím metod TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) nebo [protokolování framework kolekcí](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` Platné hodnoty jsou:
 *  VYPNOUT - odfiltrovat všechna trasování
 *  TRASOVÁNÍ – bez filtrování. se rovná úroveň trasování
 *  Informace o – filtrování úroveň trasování
 *  WARN – filtrování trasování a informace
 *  Chyba – filtrování UPOZORNIT, informace o trasování
 *  DŮLEŽITÉ – filtr všechny ale důležité


## <a name="custom-filters"></a>Vlastní filtry

### <a name="1-code-your-filter"></a>1. Kód filtru

Ve vašem kódu, vytvořte třídu, která implementuje `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Vyvolání filtr v konfiguračním souboru

V ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Řešení potíží

*Tento filtr není funkční.*

* Zkontrolujte, zda jste zadali platný parametr hodnoty. Například dob trvání by měl být celá čísla. Neplatné hodnoty způsobí, že filtr, aby se ignorovat. Pokud vaše vlastní filtr vyvolá výjimku konstruktoru nebo metody set, bude se ignorovat.

## <a name="next-steps"></a>Další postup

* [Vzorkování](../../application-insights/app-insights-sampling.md) – zvažte jako alternativu, která není zkosení metriky vzorkování.
