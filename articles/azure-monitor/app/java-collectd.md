---
title: Sledování výkonu java webových aplikací na Linuxu – Azure | Dokumenty společnosti Microsoft
description: Rozšířené sledování výkonu aplikací na vašich webových stránkách java s modulem plug-in CollectD pro Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 687f97c305bffdfb408feb314ccded4f93ac574a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660729"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Metriky výkonu Linuxu v Application Insights


Chcete-li prozkoumat metriky výkonu systému Linux v [Application Insights](../../azure-monitor/app/app-insights-overview.md), nainstalujte [shromažďované](https://collectd.org/)a jeho modul plug-in Application Insights. Toto open-source řešení shromažďuje různé systémové a síťové statistiky.

Obvykle budete používat sbírat, pokud jste již [instrumentované java webové služby s Application Insights][java]. Poskytuje další data, která vám pomohou zvýšit výkon aplikace nebo diagnostikovat problémy. 

## <a name="get-your-instrumentation-key"></a>Pochutněte si na instrumentační klíč
Na [portálu Microsoft Azure](https://portal.azure.com)otevřete prostředek [Application Insights,](../../azure-monitor/app/app-insights-overview.md) kde se mají data zobrazit. (Nebo [vytvořit nový zdroj](../../azure-monitor/app/create-new-resource.md ).)

Vezměte kopii klíče instrumentace, který identifikuje prostředek.

![Procházet vše, otevřít svůj zdroj a pak v rozevíracím panelu Essentials vybrat a zkopírovat klíč instrumentace](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Instalujte shromažďované a zásuvné
Na vašich počítačích s linuxovými servery:

1. Nainstalujte [shromažďovku](https://collectd.org/) verzi 5.4.0 nebo novější.
2. Stáhněte si [plugin pro shromažďovaný modul pro zápisy z aplikací](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Poznamenejte si číslo verze.
3. Zkopírujte plugin `/usr/share/collectd/java`JAR do .
4. Upravit `/etc/collectd/collectd.conf`:
   * Ujistěte se, že je povolen [plugin Java.](https://collectd.org/wiki/index.php/Plugin:Java)
   * Aktualizujte JVMArg pro java.class.path zahrnout následující JAR. Aktualizujte číslo verze tak, aby odpovídalo číslu, který jste si stáhli:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Přidejte tento úryvek pomocí klíče instrumentace z vašeho prostředku:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Zde je část ukázkového konfiguračního souboru:

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

Konfigurovat další [shromažďované pluginy](https://collectd.org/wiki/index.php/Table_of_Plugins), které mohou shromažďovat různá data z různých zdrojů.

Restart sbírat podle jeho [manuálu](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Zobrazení dat v přehledech aplikací
Ve zdroji Application Insights otevřete [metriky a přidejte grafy][metrics]a vyberte metriky, které chcete zobrazit v kategorii Vlastní.

Ve výchozím nastavení jsou metriky agregovány ve všech hostitelských počítačích, ze kterých byly metriky shromážděny. Chcete-li zobrazit metriky na hostitele, v okně Podrobnosti grafu zapněte seskupování a pak zvolte seskupení podle collectd-host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Vyloučení nahrávání konkrétních statistik
Ve výchozím nastavení plugin Application Insights odesílá všechna data shromážděná všemi povolenými moduly plug-in "read". 

Vyloučení dat z konkrétních pluginů nebo zdrojů dat:

* Upravte konfigurační soubor. 
* V `<Plugin ApplicationInsightsWriter>`, přidejte direktivy řádky, jako je tento:

| Směrnice | Účinek |
| --- | --- |
| `Exclude disk` |Vyloučit všechna data `disk` shromážděná pluginem |
| `Exclude disk:read,write` |Vylučte `read` `write` pojmenované `disk` zdroje a z pluginu. |

Samostatné směrnice s novým řádkem.

## <a name="problems"></a>Problémy?
*Na portálu se nezobrazují data*

* Otevřete [hledání][diagnostic] a zjistěte, zda nezpracované události dorazily. Někdy trvá déle, než se zobrazí v průzkumníku metrik.
* Možná budete muset [nastavit výjimky brány firewall pro odchozí data.](../../azure-monitor/app/ip-addresses.md)
* Povolte trasování ve pluginu Application Insights. Přidejte tento `<Plugin ApplicationInsightsWriter>`řádek do :
  * `SDKLogger true`
* Otevřete terminál a spusťte shromažďování v podrobném režimu, abyste viděli všechny problémy, které hlásí:
  * `sudo collectd -f`

## <a name="known-issue"></a>Známý problém

Modul plug-in Application Insights Write není kompatibilní s určitými moduly plug-in pro čtení. Některé pluginy někdy posílají "NaN", kde plugin Application Insights očekává číslo s plovoucí desetinnou tázkou.

Příznak: Shromažďovaný protokol zobrazuje chyby, které zahrnují "AI: ... Syntaxerror: Neočekávaný token N".

Řešení: Vyloučit data shromážděná problémem Zapisovat pluginy pro zápis. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


