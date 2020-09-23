---
title: Monitorování aplikací Docker v Azure Application Insights | Microsoft Docs
description: Čítače výkonu Docker, události a výjimky lze zobrazit v Application Insights společně s telemetrie z kontejnerových aplikací.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977580"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorovat aplikace Docker v Application Insights (zastaralé)

> [!NOTE]
> Toto řešení je zastaralé. Pokud chcete získat další informace o našich aktuálních investicích do monitorování kontejnerů, doporučujeme rezervovat [Azure monitor pro kontejnery](../insights/container-insights-overview.md).

Události životního cyklu a čítače výkonu z kontejnerů [Docker](https://www.docker.com/) lze v grafu Application Insights. Nainstalujte [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) image do kontejneru na hostiteli a zobrazí se čítače výkonu pro hostitele a také pro ostatní image.

S Docker distribuujete své aplikace v odlehčených kontejnerech se všemi závislostmi. Spustí se na jakémkoli hostitelském počítači, na kterém běží modul Docker.

Když na hostiteli Docker spustíte [Application Insights image](https://hub.docker.com/r/microsoft/applicationinsights/) , získáte tyto výhody:

* Telemetrie životního cyklu o všech kontejnerech spuštěných na hostiteli – spuštění, zastavení a tak dále.
* Čítače výkonu pro všechny kontejnery. Využití procesoru, paměti, sítě a dalších.
* Pokud jste [nainstalovali sadu Application Insights SDK pro jazyk Java](./java-get-started.md) v aplikacích spuštěných v kontejnerech, budou mít všechny telemetrie těchto aplikací další vlastnosti identifikující kontejner a hostitelský počítač. Pokud například máte instance aplikace spuštěné na více než jednom hostiteli, můžete snadno filtrovat telemetrie aplikací podle hostitele.

## <a name="set-up-your-application-insights-resource"></a>Nastavení prostředku Application Insights

1. Přihlaste se [portál Microsoft Azure](https://azure.com) a otevřete Application Insights prostředek pro vaši aplikaci. nebo [vytvořte novou](./create-new-resource.md). 
   
    *Který prostředek mám použít?* Pokud aplikace, které spouštíte na hostiteli, byly vyvinuty někým jiným, je nutné [vytvořit nový prostředek Application Insights](./create-new-resource.md). Tady můžete zobrazit a analyzovat telemetrii. (Pro typ aplikace vyberte "Obecné".)
   
    Ale pokud jste vývojářem aplikací, doufáme, že jste do každého z nich [přidali Application Insights SDK](./java-get-started.md) . Pokud se jedná o všechny vlastněné komponenty jedné obchodní aplikace, můžete je nakonfigurovat tak, aby odesílaly telemetrii k jednomu prostředku. stejný prostředek pak použijete k zobrazení životního cyklu Docker a údajů o výkonu. 
   
    Třetím scénářem je, že jste vytvořili většinu aplikací, ale používáte samostatné prostředky k zobrazení telemetrie. V takovém případě pravděpodobně budete chtít vytvořit samostatný prostředek pro data Docker.

2. Klikněte na rozevírací seznam **základy** a zkopírujte klíč instrumentace. Pomocí této informace můžete sadě SDK sdělit, kam má poslat svou telemetrii.

Mějte přehled o tom, jak se k němu přiblížíte, protože se k němu dostanete, abyste se mohli podívat na telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Spuštění monitorování Application Insights na hostiteli

Teď, když jste se dostali k zobrazení telemetrie, můžete nastavit kontejnerové aplikace, které se budou shromažďovat a odesílat.

1. Připojte se k hostiteli Docker.
2. Upravte klíč instrumentace do tohoto příkazu a pak ho spusťte:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Pro hostitele Docker je vyžadován pouze jeden Application Insights obrázek. Pokud je vaše aplikace nasazená na více hostitelích Docker, pak tento příkaz opakujte na každém hostiteli.

## <a name="update-your-app"></a>Aktualizace aplikace
Pokud je aplikace instrumentovaná pomocí [sady Application Insights SDK pro jazyk Java](./java-get-started.md), přidejte následující řádek do souboru ApplicationInsights.xml v projektu v rámci `<TelemetryInitializers>` elementu:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Tím se do každé položky telemetrie odeslané z vaší aplikace přidá informace o Docker, jako je například kontejner a ID hostitele.

## <a name="view-your-telemetry"></a>Zobrazení telemetrie
V Azure Portal se vraťte k prostředku Application Insights.

Klikněte na dlaždici Docker.

Za chvíli uvidíte, že se data přicházejí z aplikace Docker, a to zejména v případě, že máte v modulu Docker spuštěné další kontejnery.

### <a name="docker-container-events"></a>Události kontejneru Docker
![Snímek obrazovky znázorňuje výběr hledání, okno výsledků diagnostického vyhledávání se šipkou ukazující z vlastní události do okna s vlastními daty.](./media/docker/13.png)

Chcete-li prozkoumat jednotlivé události, klikněte na tlačítko [Hledat](./diagnostic-search.md). Hledáním a filtrováním Najděte požadované události. Podrobnější informace získáte kliknutím na libovolnou událost.

### <a name="exceptions-by-container-name"></a>Výjimky podle názvu kontejneru
![Snímek obrazovky ukazuje stránku Průzkumník metrik s vybraným grafem a otevře se okno podrobností grafu na pravé straně.](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Přidaný kontext Docker do telemetrie aplikací
Telemetrie žádostí odeslanou z aplikace instrumentovaná pomocí AI SDK je obohacena informacemi o kontextu Docker.

## <a name="q--a"></a>Otázky a odpovědi
*Co mi Application Insights mám vědět, že nemůžu získat z Docker?*

* Podrobný rozpis čítačů výkonu podle kontejneru a image.
* Integrujte data kontejneru a aplikace do jednoho řídicího panelu.
* [Exportujte telemetrii](export-telemetry.md) pro další analýzu do databáze, Power BI nebo jiného řídicího panelu.

*Návody získat telemetrii přímo z aplikace?*

* Nainstalujte do aplikace sadu Application Insights SDK. Naučte se: [Java Web Apps](./java-get-started.md), [Windows Web Apps](./asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další kroky

* [Application Insights pro jazyk Java](./java-get-started.md)
* [Application Insights pro Node.js](./nodejs.md)
* [Application Insights pro ASP.NET](./asp-net.md)

