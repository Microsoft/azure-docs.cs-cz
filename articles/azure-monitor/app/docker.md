---
title: Monitorování aplikací Dockeru ve službě Azure Application Insights | Dokumentace Microsoftu
description: Docker čítače výkonu, událostí a výjimek lze zobrazit v Application Insights, spolu se telemetrie z kontejnerizovaných aplikací.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: mbullwin
ms.openlocfilehash: a4e4ca1ca4878a2a405b12413e4378a2cb79aef6
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999114"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitorování aplikací Dockeru ve službě Application Insights

Události životního cyklu a výkonu čítače z [Docker](https://www.docker.com/) kontejnery lze převést na Application Insights graf. Nainstalujte [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) bitové kopie v kontejneru v hostiteli který se zobrazí čítače výkonu pro hostitele, stejně jako u jiných imagí.

S Dockerem distribuujte aplikace v dokončení se všemi závislostmi prostých kontejnerů. Spustit na hostitelském počítači, který spustí modul Docker.

Při spuštění [Application Insights image](https://hub.docker.com/r/microsoft/applicationinsights/) na hostitele Docker, získáte tyto výhody:

* Životní cyklus telemetrie o všechny kontejnery spuštěné na hostiteli – spuštění, zastavení a tak dále.
* Čítače výkonu pro všechny kontejnery. Procesoru, paměti, využití sítě a další.
* Pokud jste [nainstalovat sadu Application Insights SDK pro Javu](../../azure-monitor/app/java-live.md) v aplikace spuštěné v kontejnerech, bude mít veškerá telemetrická data těchto aplikací další vlastnosti identifikující kontejner a hostitele počítače. Například pokud máte instancí aplikace běžící ve více než jednoho hostitele, můžete snadno filtrovat telemetrie vaší aplikace od hostitele.

> [!NOTE]
> Toto řešení je zastaralá. Další informace o naší stávající investice do monitorování kontejnerů doporučujeme rezervace [monitorování Azure pro kontejnery](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="set-up-your-application-insights-resource"></a>Nastavení prostředku Application Insights

1. Přihlaste se do [portálu Microsoft Azure](https://azure.com) a otevřete prostředek Application Insights pro vaši aplikaci; nebo [vytvořte novou](../../application-insights/app-insights-create-new-resource.md). 
   
    *Který prostředek by měl použít?* Pokud aplikace, které jsou spuštěné v hostiteli vytvořené někým jiným, pak budete muset [vytvořit nový prostředek Application Insights](../../application-insights/app-insights-create-new-resource.md). To je, kde můžete zobrazit a analyzovat telemetrická data. (Vyberte obecné pro typ aplikace.)
   
    Ale pokud jste vývojář aplikací a Věříme, že jste [přidat sadu Application Insights SDK](../../azure-monitor/app/java-live.md) u každého z nich. Pokud jsou všechny skutečně komponenty jednu obchodní aplikace, pak můžete nakonfigurovat všechny z nich k odesílání telemetrie na jeden prostředek a použijete tento stejný prostředek pro zobrazení údajů o Docker životního cyklu a výkonu. 
   
    Třetí scénář je, že jste vyvinuli většinu aplikací, ale používáte samostatné prostředky k zobrazení jejich telemetrická data. V takovém případě budete pravděpodobně taky chtít vytvořit samostatný prostředek pro data Dockeru.

2. Klikněte na tlačítko **Essentials** rozevíracího seznamu a zkopírujte klíč instrumentace. To vám říct sadě SDK, kam má odesílat jeho telemetrická data.

Nechte okno prohlížeče po ruce, protože budete se vrátit do ho brzy se podívat na telemetrii.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Spustit monitorování Application Insights na hostiteli

Teď, když máte někde zobrazit telemetrická data, můžete nastavit kontejnerizovanou aplikaci, která bude shromažďovat a jeho odeslání.

1. Připojte se k hostitele Dockeru.
2. Upravte svůj Instrumentační klíč do tento příkaz a potom ho spusťte:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Jenom jednu image pro Application Insights na se vyžaduje hostitele Dockeru. Pokud vaše aplikace bude nasazena na několika hostitelích Dockeru, opakujte příkaz na každém hostiteli.

## <a name="update-your-app"></a>Aktualizovat aplikaci
Pokud je vaše aplikace používaná s [Application Insights SDK pro Javu](../../azure-monitor/app/java-get-started.md), přidejte následující řádek do souboru ApplicationInsights.xml ve vašem projektu, v části `<TelemetryInitializers>` element:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

To přidá informace o Dockeru jako je například kontejneru a id hostitele každé položce telemetrie odesílané z vaší aplikace.

## <a name="view-your-telemetry"></a>Zobrazení telemetrie
Přejděte zpět do prostředku Application Insights na webu Azure Portal.

Proklikejte se prostřednictvím dlaždice Dockeru.

Za chvíli uvidíte dat přicházejících z aplikace Dockeru, zejména v případě, že máte jiné kontejnery běží na vašich modul Docker.

### <a name="docker-container-events"></a>Události kontejneru dockeru
![Příklad](./media/docker/13.png)

K prozkoumání jednotlivé události, klikněte na tlačítko [hledání](../../azure-monitor/app/diagnostic-search.md). Vyhledávání a filtrování událostí, které chcete najít. Kliknutím na libovolnou událost zobrazíte další podrobnosti.

### <a name="exceptions-by-container-name"></a>Výjimky podle názvu kontejneru
![Příklad](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Přidání telemetrie aplikace kontextu dockeru
Žádost o telemetrická data odesílaná z aplikace používaná s AI SDK, je rozšiřují o informace o kontextu Dockeru.

## <a name="q--a"></a>Dotazy a odpovědi
*Co Application Insights uvést, které se nedaří se mi zprovoznit od Dockeru?*

* Podrobný přehled čítačů výkonu o kontejneru a image.
* Integrujte data kontejneru a aplikace na jednom řídicím panelu.
* [Export telemetrie](export-telemetry.md) k další analýze do databáze, Power BI nebo jiný řídicí panel.

*Jak získat telemetrická data z sama aplikace?*

* Nainstalujte službu Application Insights SDK v aplikaci. Zjistěte, jak pro: [Webové aplikace v Javě](../../azure-monitor/app/java-get-started.md), [Windows webové aplikace](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup

* [Application Insights pro Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights pro Node.js](../../azure-monitor/app/nodejs.md)
* [Application Insights pro ASP.NET](../../azure-monitor/app/asp-net.md)
