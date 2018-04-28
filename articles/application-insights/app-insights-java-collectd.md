---
title: Monitorování výkonu aplikací Java webů v systému Linux - Azure | Microsoft Docs
description: Rozšířené monitorování výkonu aplikací z vašeho webu Java pomocí modulu plug-in CollectD pro službu Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 56e30ff4ee7765cd0950293999d93a9cf1fe5d36
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: metriky výkonu Linux ve službě Application Insights


Prozkoumat metriky výkonu systému Linux v [Application Insights](app-insights-overview.md), nainstalujte [collectd](http://collectd.org/), společně s jeho modulu plug-in Application Insights. Toto řešení open source shromáždí různé statistické údaje systému a sítě.

Obvykle použijete collectd, pokud již máte [instrumentovány webovou službu Java pomocí Application Insights][java]. Nabízí více dat můžete vylepšit výkon vaší aplikace nebo diagnostikovat problémy. 

![Ukázkové grafy](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Získejte klíč instrumentace
V [portálu Microsoft Azure](https://portal.azure.com), otevřete [Application Insights](app-insights-overview.md) prostředku, kde chcete data zobrazí. (Nebo [vytvořte nový prostředek](app-insights-create-new-resource.md).)

Trvat kopii klíč instrumentace, který identifikuje prostředek.

![Procházet všechny, otevřete prostředek a pak v Essentials rozevíracího seznamu, vyberte a zkopírujte klíč instrumentace](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Nainstalujte collectd a modulu plug-in
V počítačích serverů Linux:

1. Nainstalujte [collectd](http://collectd.org/) verze 5.4.0 nebo novější.
2. Stažení [plug-in Application Insights collectd zapisovače](https://aka.ms/aijavasdk). Poznamenejte si číslo verze.
3. Tento modul plug-in JAR do kopie `/usr/share/collectd/java`.
4. Upravit `/etc/collectd/collectd.conf`:
   * Ujistěte se, že [modul Java plug-in](https://collectd.org/wiki/index.php/Plugin:Java) je povoleno.
   * Aktualizujte JVMArg pro java.class.path zahrnout následující JAR. Aktualizujte číslo verze tak, aby odpovídaly ten, který jste stáhli:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Přidejte tento fragment kódu, pomocí klíč instrumentace z prostředku:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Tady je součástí vzorový konfigurační soubor:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfigurace dalších [modulů plug-in collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), který může shromažďovat různých dat z různých zdrojů.

Restartujte collectd podle jeho [ruční](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Zobrazení dat ve službě Application Insights
V prostředku Application Insights, otevřete [Průzkumníku metrik a grafy přidáte][metrics], výběr metriky, které chcete z vlastní kategorie.

![](./media/app-insights-java-collectd/result.png)

Ve výchozím nastavení jsou metriky agregovat přes všechny hostitele počítače, ze kterých byly shromážděny metriky. K zobrazení metriky na hostiteli, v okně podrobností graf, zapněte seskupování a poté zvolte podle CollectD hostitele.

## <a name="to-exclude-upload-of-specific-statistics"></a>Vyloučit odesílání konkrétní Statistika
Ve výchozím nastavení odešle modulem plug-in Application Insights všechna data shromažďovaná společností povoleno collectd číst modulů plug-in. 

Vyloučení dat z konkrétní modulů plug-in nebo zdrojů dat:

* Upravte konfigurační soubor. 
* V `<Plugin ApplicationInsightsWriter>`, přidejte direktivy řádky takto:

| – Direktiva | Efekt |
| --- | --- |
| `Exclude disk` |Vyloučit všechny data shromažďovaná společností `disk` modulu plug-in |
| `Exclude disk:read,write` |Vyloučení zdrojů s názvem `read` a `write` z `disk` modulu plug-in. |

Samostatné direktivy s nový řádek.

## <a name="problems"></a>Problémy?
*Data na portálu se nezobrazí*

* Otevřete [vyhledávání] [ diagnostic] chcete zobrazit, pokud byly přijaty nezpracované události. Někdy se trvat déle, než se objeví v Průzkumníku metrik.
* Možná budete muset [nastavit výjimky brány firewall pro odchozí data](app-insights-ip-addresses.md)
* Povolte trasování v modulem plug-in Application Insights. Přidejte tento řádek v rámci `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Otevřete terminál a spusťte collectd v režimu s komentářem, zobrazíte jakýchkoliv problémů, které je generování sestav:
  * `sudo collectd -f`

## <a name="known-issue"></a>Známý problém

Modul plug-in Application Insights zápisu není kompatibilní s určitým modulů plug-in pro čtení. Některé moduly plug-in někdy odeslat "NaN", kde modulem plug-in Application Insights očekává číslo s plovoucí desetinnou čárkou.

Příznak: Protokol collectd zobrazuje chyby, které zahrnují "AI:... Chyba syntaxe: Neočekávaný token N ".

Alternativní řešení: Vyloučit data shromažďovaná společností modulů plug-in zápisu problém. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


