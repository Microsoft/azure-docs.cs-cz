---
title: Monitorování aplikací Dockeru v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Čítače, události a výjimky pro Docker perf lze zobrazit na Application Insights, spolu s telemetrií z kontejnerizovaných aplikací.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 5ff3d6a2b31425a3fb6857c8c6aed45f7ef18d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669603"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorování aplikací Dockeru v přehledech aplikací (zastaralé)

> [!NOTE]
> Toto řešení bylo zastaralé. Chcete-li se dozvědět více o našich aktuálních investicích do monitorování kontejnerů, doporučujeme prověřit [Azure Monitor pro kontejnery](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Události životního cyklu a čítače výkonu z kontejnerů [Dockeru](https://www.docker.com/) lze grafovat na Application Insights. Nainstalujte image [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) v kontejneru v hostiteli a zobrazí čítače výkonu pro hostitele i pro ostatní image.

S Dockerem distribuujete aplikace v lehkých kontejnerech s kompletními všemi závislostmi. Budou spuštěny na libovolném hostitelském počítači, který spouští Docker Engine.

Když spustíte [image Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) na vašem hostiteli Dockeru, získáte tyto výhody:

* Telemetrie životního cyklu o všech kontejnerech spuštěných na hostiteli – spuštění, zastavení a tak dále.
* Čítače výkonu pro všechny kontejnery. PROCESOR, paměť, využití sítě a další.
* Pokud jste [nainstalovali Application Insights SDK pro Java](../../azure-monitor/app/java-get-started.md) v aplikacích spuštěných v kontejnerech, všechny telemetrie těchto aplikací bude mít další vlastnosti identifikující kontejner a hostitelský počítač. Takže například pokud máte instance aplikace spuštěné ve více než jednom hostiteli, můžete snadno filtrovat telemetrii aplikace podle hostitele.

## <a name="set-up-your-application-insights-resource"></a>Nastavení prostředku Application Insights

1. Přihlaste se k [portálu Microsoft Azure](https://azure.com) a otevřete prostředek Application Insights pro vaši aplikaci. nebo [vytvořit nový](../../azure-monitor/app/create-new-resource.md ). 
   
    *Který zdroj mám použít?* Pokud aplikace, které používáte na hostiteli, byly vyvinuty někým jiným, musíte [vytvořit nový prostředek Application Insights](../../azure-monitor/app/create-new-resource.md ). Toto je místo, kde můžete zobrazit a analyzovat telemetrie. (Vyberte pro typ aplikace možnost Obecné.)
   
    Ale pokud jste vývojář aplikací, pak doufáme, že jste [přidali Application Insights SDK](../../azure-monitor/app/java-get-started.md) do každé z nich. Pokud jsou všechny skutečně součásti jedné obchodní aplikace, můžete nakonfigurovat všechny z nich k odesílání telemetrie do jednoho prostředku a budete používat stejný prostředek k zobrazení životního cyklu Dockeru a data o výkonu. 
   
    Třetím scénářem je, že jste vyvinuli většinu aplikací, ale používáte samostatné prostředky k zobrazení jejich telemetrie. V takovém případě pravděpodobně také chcete vytvořit samostatný prostředek pro data Dockeru.

2. Klikněte na rozbalovací panel **Essentials** a zkopírujte klíč instrumentace. Pomocí této funkce můžete sdělit sadu SDK, kam má odeslat telemetrii.

Mějte toto okno prohlížeče po ruce, protože se k němu brzy vrátíte, abyste se podívali na svou telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Spuštění monitoru Application Insights na hostiteli

Teď, když máte někde zobrazit telemetrii, můžete nastavit kontejnerizované aplikace, která bude shromažďovat a odesílat.

1. Připojte se k hostiteli Dockeru.
2. Upravte klíč instrumentace do tohoto příkazu a spusťte jej:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Na jednoho hostitele Dockeru je vyžadována pouze jedna image Application Insights. Pokud je vaše aplikace nasazená na více hostitelích Dockeru, opakujte příkaz na každém hostiteli.

## <a name="update-your-app"></a>Aktualizace aplikace
Pokud je vaše aplikace instrumentována sadou [Application Insights SDK pro Jazyk Java](../../azure-monitor/app/java-get-started.md), přidejte `<TelemetryInitializers>` pod element do souboru ApplicationInsights.xml následující řádek do souboru ApplicationInsights.xml:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Tím přidáte informace dockeru, jako je kontejner a ID hostitele, ke každé položce telemetrie odeslané z vaší aplikace.

## <a name="view-your-telemetry"></a>Zobrazení telemetrie
Vraťte se k prostředku Application Insights na webu Azure Portal.

Proklikejte se dlaždicí Dockeru.

Brzy uvidíte data přicházející z aplikace Docker, zejména pokud máte další kontejnery spuštěné v modulu Dockeru.

### <a name="docker-container-events"></a>Události kontejneru Dockeru
![příklad](./media/docker/13.png)

Chcete-li prozkoumat jednotlivé události, klepněte na tlačítko [Hledat](../../azure-monitor/app/diagnostic-search.md). Vyhledání a filtrováním vyhledáte požadované události. Kliknutím na libovolnou událost získáte další podrobnosti.

### <a name="exceptions-by-container-name"></a>Výjimky podle názvu kontejneru
![příklad](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Kontext Dockeru přidaný do telemetrie aplikace
Požadavek telemetrie odeslané z aplikace instrumentované s AI SDK, je obohacen o informace o kontextu Dockeru.

## <a name="q--a"></a>Otázky a odpovědi
*Co mi Application Insights dává, co nemůžu získat z Dockeru?*

* Podrobný rozpis čítačů výkonu podle kontejneru a obrázku.
* Integrujte data kontejnerů a aplikací do jednoho řídicího panelu.
* [Exporttelemetrie](export-telemetry.md) pro další analýzu do databáze, Power BI nebo jiného řídicího panelu.

*Jak získám telemetrii ze samotné aplikace?*

* Nainstalujte do aplikace stesku SDK application insights. Přečtěte si, jak: [Webové aplikace v Jazyce Java](../../azure-monitor/app/java-get-started.md), Webové aplikace pro [Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další kroky

* [Přehledy aplikací pro Jazyk Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights pro Node.js](../../azure-monitor/app/nodejs.md)
* [Přehledy aplikací pro ASP.NET](../../azure-monitor/app/asp-net.md)
