---
title: Monitorování výkonu webové aplikace Java v systému Linux – Azure | Microsoft Docs
description: Rozšířené monitorování výkonu aplikace vašeho webu Java pomocí shromážděného modulu plug-in pro Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 08d8deb4c7769c4f8a202050d7b5515439d691f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573844"
---
# <a name="collectd-linux-performance-metrics-in-application-insights-deprecated"></a>shromážděné: metriky výkonu Linux v Application Insights [zastaralé]

> [!IMPORTANT]
> **Doporučený postup** pro monitorování aplikací Java je použití automatické instrumentace beze změny kódu. Postupujte prosím podle pokynů pro **[Application Insights agenta Java 3,0](./java-in-process-agent.md)**.

Pokud chcete prozkoumat metriky výkonu systému Linux v [Application Insights](./app-insights-overview.md), [nainstalujte společně](https://collectd.org/)s modulem plug-in Application Insights. Toto open source řešení shromažďuje různé statistiky systému a sítě.

V případě, že jste již [svou webovou službu Java][java]nastavili Application Insights, obvykle budete používat Collect. Poskytuje vám další data, která vám pomůžou vylepšit výkon a diagnostikovat problémy vaší aplikace. 

## <a name="get-your-instrumentation-key"></a>Získat klíč instrumentace
V [portál Microsoft Azure](https://portal.azure.com)otevřete prostředek [Application Insights](./app-insights-overview.md) , ve kterém chcete zobrazit data. (Nebo [vytvořte nový prostředek](./create-new-resource.md).)

Poznamenejte si kopii klíče instrumentace, který identifikuje prostředek.

![Přejděte na vše, otevřete prostředek a potom v rozevíracím seznamu základy vyberte a zkopírujte klíč instrumentace.](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Nainstalovat shromážděné a modul plug-in
Na počítačích se systémem Linux Server:

1. Nainstalujte [shromážděnou](https://collectd.org/) verzi 5.4.0 nebo novější.
2. Stáhněte si [modul plug-in Application Insights Collected Writer](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal). Poznamenejte si číslo verze.
3. Zkopírujte SKLENICi modulu plug-in do `/usr/share/collectd/java` .
4. Upravit `/etc/collectd/collectd.conf` :
   * Ujistěte se, že je povolený [modul plug-in Java](https://collectd.org/wiki/index.php/Plugin:Java) .
   * Aktualizujte JVMArg pro Java. Class. Path, aby obsahovala následující JAR. Aktualizujte číslo verze tak, aby odpovídalo vašemu staženému:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Pomocí klíče instrumentace z prostředku přidejte tento fragment kódu:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Tady je část ukázkového konfiguračního souboru:

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

Nakonfigurujte další [shromážděné moduly plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins), které mohou shromažďovat různá data z různých zdrojů.

Restart se shromáždí na základě jeho [ručního](https://collectd.org/wiki/index.php/First_steps)spuštění.

## <a name="view-the-data-in-application-insights"></a>Zobrazit data v Application Insights
V Application Insights prostředku otevřete [metriky a přidejte grafy a][metrics]vyberte metriky, které chcete zobrazit, z vlastní kategorie.

Ve výchozím nastavení jsou metriky agregované napříč všemi hostitelskými počítači, ze kterých byly metriky shromažďovány. Chcete-li zobrazit metriky na hostitele, v okně podrobností grafu zapněte seskupování a pak zvolte seskupení podle shromážděného-Host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Vyloučení odeslání konkrétních statistik
Ve výchozím nastavení odesílá modul plug-in Application Insights všechna data shromážděná všemi povolenými moduly plug-in "čtení". 

Vyloučení dat z konkrétních modulů plug-in nebo zdrojů dat:

* Upravte konfigurační soubor. 
* V `<Plugin ApplicationInsightsWriter>` přidejte řádky direktivy takto:

| Směrnici | Účinek |
| --- | --- |
| `Exclude disk` |Vyloučit všechna data shromažďovaná `disk` modulem plug-in |
| `Exclude disk:read,write` |Vylučte ze zdrojů s názvem `read` a `write` z `disk` modulu plug-in. |

Oddělte direktivy novým řádkem.

## <a name="problems"></a>Problémy?
*Na portálu se nezobrazují data*

* Otevřete [vyhledávání][diagnostic] a zjistěte, jestli nezpracované události byly doručeny. Někdy trvá i déle, než se objeví v Průzkumníku metrik.
* [Pro odchozí data možná budete muset nastavit výjimky brány firewall](./ip-addresses.md) .
* Povolte trasování v modulu plug-in Application Insights. Přidat tento řádek do `<Plugin ApplicationInsightsWriter>` :
  * `SDKLogger true`
* Otevřete terminál a spusťte shromažďování v podrobném režimu, aby se zobrazily případné problémy, které hlásí:
  * `sudo collectd -f`

## <a name="known-issue"></a>Známý problém

Modul plug-in pro zápis Application Insights není kompatibilní s některými moduly pro čtení. Některé moduly plug-in někdy odesílají "NaN", kde Application Insights modul plug-in očekává číslo s plovoucí desetinnou čárkou.

Příznak: zachycený protokol zobrazuje chyby, které zahrnují AI:... SyntaxError: Neočekávaný token N

Alternativní řešení: vylučte data shromážděná potížemi s zápisem modulů plug-in. 

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

