---
title: Monitorování aplikací Docker ve službě Azure Application Insights | Microsoft Docs
description: Čítače výkonu docker, události a výjimek lze zobrazit na Application Insights, společně s telemetrii z kontejnerizované aplikací.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: a0476e2f0bf08f76b45e1342ec38137e46008cb1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitorování Docker aplikací ve službě Application Insights
Události životního cyklu a výkonu čítače z [Docker](https://www.docker.com/) kontejnery může být v grafu zobrazena na Application Insights. Nainstalujte [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) bitové kopie v kontejneru v hostiteli a zobrazí čítače výkonu pro hostitele, a také pro jiné bitové kopie.

Pomocí Docker distribuovat aplikace v lightweight kontejnery kompletní s všechny závislosti. Budete běží na jakýkoli počítač hostitele, který spouští modul Docker.

Při spuštění [Application Insights image](https://hub.docker.com/r/microsoft/applicationinsights/) hostiteli Docker získáte tyto výhody:

* Životní cyklus telemetrii týkající se všechny kontejnery spuštěná v hostiteli - spuštění, zastavení a tak dále.
* Čítače výkonu pro všechny kontejnery. Využití procesoru, paměti, využití sítě a další.
* Pokud jste [nainstalován Application Insights SDK pro jazyk Java](app-insights-java-live.md) ve aplikace běžící v kontejnerech, budou všechny telemetrická těchto aplikací mít další vlastnosti identifikace počítače kontejneru a hostitele. Tak například pokud máte instance aplikace spuštěná ve více než jednomu hostiteli, lze snadno filtrovat telemetrie aplikace hostitele.

![Příklad](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Nastavit zdroje Application Insights
1. Přihlaste se k [portálu Microsoft Azure](https://azure.com) a otevřete prostředek Application Insights pro vaše aplikace, nebo [vytvořte novou](app-insights-create-new-resource.md). 
   
    *Který prostředek by měl používat?* Pokud aplikace, které jsou spuštěné v hostiteli vyvinul někdo jiný, pak budete muset [vytvořte nový prostředek Application Insights](app-insights-create-new-resource.md). Toto je, kde můžete zobrazit a analyzovat telemetrii. (Vyberte obecné pro typ aplikace.)
   
    Ale pokud jste vývojář aplikace, pak Věříme, že jste [přidat Application Insights SDK](app-insights-java-live.md) ke každému z nich. Pokud jsou všechny skutečně součásti jeden obchodní aplikace, můžete pak nakonfigurovat všechny z nich k odesílání telemetrie na jeden prostředek a použijete stejný prostředku pro zobrazení dat Docker životního cyklu a výkonu. 
   
    Třetí scénář je, že vyvinuté většinu aplikací, ale používáte samostatné prostředků k zobrazení jejich telemetrie. V takovém případě budete pravděpodobně také chtít vytvořit samostatnou prostředků pro Docker data. 
2. Přidat dlaždice Docker: Zvolte **přidat dlaždici**, přetáhněte dlaždici Docker z galerie a pak klikněte na tlačítko **provádí**. 
   
    ![Příklad](./media/app-insights-docker/03.png)

> [!NOTE]
> V podokně přehled ve službě Application Insights je nyní uzamčen a neumožňuje přidávat dlaždice z galerie. Můžete přesto přidat Docker dlaždice, jak je popsáno výše prostřednictvím rozhraní řídicího panelu Azure.

3. Klikněte **Essentials** rozevíracího seznamu a zkopírujte klíč instrumentace. To slouží k říct sadě SDK, kam má posílat jeho telemetrie.

    ![Příklad](./media/app-insights-docker/02-props.png)

Nechte okno prohlížeče užitečný, jak budete se vraťte k jeho brzo podívejte se na vaší telemetrie.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Spustit monitorování Application Insights na hostiteli
Teď, když máte k dispozici někde zobrazit telemetrii, můžete nastavit kontejnerizované aplikaci, která bude shromažďovat a odesílat je.

1. Připojte k hostiteli Docker. 
2. Upravit klíč instrumentace do tento příkaz a potom ho spusťte:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Pouze jeden obrázek Application Insights je vyžadován na Docker hostitele. Pokud vaše aplikace je nasazená na několika hostitelích Docker, pak opakujte příkaz na každém hostiteli.

## <a name="update-your-app"></a>Aktualizace aplikace
Pokud vaše aplikace není instrumentována s [Application Insights SDK pro jazyk Java](app-insights-java-get-started.md), přidejte následující řádek do souboru ApplicationInsights.xml ve vašem projektu v části `<TelemetryInitializers>` element:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Tento postup přidá informace o Docker například kontejneru a id hostitele každé položce telemetrie odeslaných z vaší aplikace.

## <a name="view-your-telemetry"></a>Zobrazení telemetrie
Přejděte zpět do zdroje Application Insights na portálu Azure.

Proklikejte se prostřednictvím Docker dlaždice.

Krátce zobrazí dat odesílaných z aplikace Docker, zvláště pokud máte jiné kontejnery systémem Docker modul.

Zde jsou některé zobrazení, která můžete získat.

### <a name="perf-counters-by-host-activity-by-image"></a>Čítače výkonu hostitele, aktivita podle bitové kopie
![Příklad](./media/app-insights-docker/10.png)

![Příklad](./media/app-insights-docker/11.png)

Klikněte na libovolný název hostitele nebo bitové kopie pro více podrobností.

Chcete-li přizpůsobit zobrazení, klikněte na libovolný graf mřížky záhlaví, nebo použijte přidat graf. 

[Další informace o Průzkumníku metrik](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Události kontejner docker
![Příklad](./media/app-insights-docker/13.png)

Chcete-li prozkoumat jednotlivé události, klikněte na tlačítko [vyhledávání](app-insights-diagnostic-search.md). Hledat a filtr k událostem, které chcete najít. Kliknutím na libovolnou událost zobrazíte další podrobnosti.

### <a name="exceptions-by-container-name"></a>Výjimky podle názvu kontejneru
![Příklad](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Kontext docker přidat do aplikace telemetrie
Žádost o telemetrické zprávy odesílané z aplikace instrumentovány s AI SDK a rozšíření s kontextem Docker:

![Příklad](./media/app-insights-docker/16.png)

Čas procesoru a paměti k dispozici čítače výkonu, rozšíření a seskupené podle Docker název kontejneru:

![Příklad](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Dotazy a odpovědi
*Co Application Insights mě, nemůže dostat z Docker?*

* Podrobné rozpis čítačů výkonu tak, že kontejner a bitové kopie.
* Integrace aplikace a kontejneru dat v jednom řídicím.
* [Export telemetrie](app-insights-export-telemetry.md) k další analýze databáze, Power BI nebo jiné řídicí panel.

*Získání telemetrických dat z aplikace*

* Nainstalujte službu Application Insights SDK v aplikaci. Zjistěte, jak pro: [webové aplikace v jazyce Java](app-insights-java-get-started.md), [Windows webové aplikace](app-insights-asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup

* [Application Insights pro jazyk Java](app-insights-java-get-started.md)
* [Application Insights pro Node.js](app-insights-nodejs.md)
* [Application Insights pro ASP.NET](app-insights-asp-net.md)
