---
title: Závislost sledování ve službě Azure Application Insights | Microsoft Docs
description: Analýza místního využití, dostupnosti a výkonu nebo webová aplikace Microsoft Azure s nástrojem Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mbullwin
ms.openlocfilehash: ee628f137761445be8871cf4df44e48231342f50
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234592"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Nastavte Application Insights: sledování závislostí
A *závislostí* je externí komponenta, která je volána aplikace. Obvykle se jedná o službu volat pomocí protokolu HTTP, nebo databázi nebo systému souborů. [Application Insights](app-insights-overview.md) měří, jak dlouho aplikace čeká závislosti a jak často závislostí volání selže. Můžete prozkoumat konkrétní volání a propojovat je na požadavky a výjimkami.

![ukázkové grafy](./media/app-insights-asp-net-dependencies/10-intro.png)

Toto monitorování závislostí na více systémů pole sestavy aktuálně volání na tyto typy závislosti:

* Server
  * Databáze SQL
  * Technologie ASP.NET a služby WCF, které používají vazby založené na protokolu HTTP
  * Místní nebo vzdálené volání protokolu HTTP
  * Azure Cosmos DB, table, úložiště objektů blob a fronty
* Webové stránky
  * Volání AJAX

Monitorování funguje pomocí [bajtů kód instrumentace](https://msdn.microsoft.com/library/z9z62c29.aspx) kolem vybrané metody. Nároky na výkon je minimální.

Můžete taky napsat vlastní volání sady SDK k monitorování Další závislosti, oba seznamy v kód klienta a serveru pomocí [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Nastavení monitorování závislostí
Částečné závislostí informace jsou shromažďovány automaticky pomocí [Application Insights SDK](app-insights-asp-net.md). Chcete-li získat kompletní datový, nainstalujte příslušné agenta pro hostitelský server.

| Platforma | Instalace |
| --- | --- |
| Server služby IIS |Buď [nainstalujte monitorování stavu na serveru](app-insights-monitor-performance-live-website-now.md) nebo [upgradu vaší aplikace rozhraní .NET Framework 4.6 nebo novější](http://go.microsoft.com/fwlink/?LinkId=528259) a nainstalujte [Application Insights SDK](app-insights-asp-net.md) ve vaší aplikaci. |
| Webové aplikace Azure |Ve webové aplikaci ovládacího panelu [otevřete okno Application Insights ve webové aplikaci ovládacího panelu](app-insights-azure-web-apps.md) a instalace zvolte, pokud se zobrazí výzva. |
| Cloudové služby Azure |[Úloha spuštění použití](app-insights-cloudservices.md) nebo [nainstalovat rozhraní .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí
* [Mapa aplikace](#application-map) vizualizuje závislosti mezi aplikací a sousedních součásti.
* [Okna výkonu, prohlížeče a selhání](#performance-and-blades) zobrazit závislosti dat serveru.
* [Okno prohlížečů](#ajax-calls) ukazuje volání AJAX z prohlížečů uživatelů.
* [Klikněte na položku přes pomalé nebo chybných požadavků](#diagnose-slow-requests) zkontrolujte jejich závislost volání.
* [Analýza](#analytics) lze použít k dotazování na data závislostí.

## <a name="application-map"></a>Mapa aplikace
Mapa aplikace funguje jako vizuální pomůcka zjišťování závislostí mezi součástmi aplikace. Automaticky se generují z telemetrie z vaší aplikace. Tento příklad ukazuje volání AJAX z prohlížeče skriptů a volání REST z aplikace serveru dvě externích služeb.

![Mapa aplikace](./media/app-insights-asp-net-dependencies/08.png)

* **Přejděte z políčka** relevantní závislosti a jinými grafy.
* **Připnout mapy** k [řídicí panel](app-insights-dashboards.md), kde budou plně funkční.

[Další informace](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Výkon a selhání oken
Okno výkon zobrazuje dobu trvání závislosti volání aplikace serveru. Není souhrnné graf a tabulku oddělených volání.

![Grafy závislostí okno výkonu.](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Proklikejte se prostřednictvím souhrnné grafy nebo položky, které se mají hledat nezpracovaná výskyty těchto volání.

![Instance volání závislostí](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Počet selhání** se zobrazují na **selhání** okno. Selhání je jakýkoli návratový kód, který není v rozsahu 200-399, nebo neznámý.

> [!NOTE]
> **100 % selhání?** – Pravděpodobně to znamená, jsou jenom získávání dat částečné závislostí. Budete muset [nastavení monitorování závislostí vhodné pro vaši platformu](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Volání AJAX
V okně prohlížeče zobrazuje míru doba trvání a selhání volání AJAX z [JavaScript na webových stránkách](app-insights-javascript.md). Zobrazí se jako závislosti.

## <a name="diagnosis"></a> Diagnostika pomalé požadavků
Každá událost požadavku je přidružen volání závislostí, výjimek a další události, které jsou sledovány, zatímco aplikace je zpracování požadavku. Takže pokud chybně provedli některé požadavky, můžete zjistit ať to je z důvodu zpomalení odezvy ze závislost.

Projděme příklad této.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování z požadavky na závislosti
Otevřete okno výkon a podívejte se na mřížky požadavků:

![Seznam požadavků s průměry a počty](./media/app-insights-asp-net-dependencies/02-reqs.png)

Horní jeden trvá velmi dlouho. Podívejme se, pokud jsme můžete zjistit, kde čas strávený.

Klikněte na daném řádku zobrazíte události jednotlivých žádostí:

![Seznam výskytů požadavku](./media/app-insights-asp-net-dependencies/03-instances.png)

Kliknutím na jakoukoli instanci dlouho běžící další zkontrolovat a posuňte se dolů a volání vzdálené závislosti související s tuto žádost:

![Najít volání vzdálené závislosti, identifikovat neobvyklé doba trvání](./media/app-insights-asp-net-dependencies/04-dependencies.png)

To vypadá většinu času údržby, které tento požadavek byl stráven v volání k místní službě.

Vyberte tento řádek získat další informace:

![Klikněte na tlačítko prostřednictvím tohoto vzdáleného závislostí pro identifikaci, který](./media/app-insights-asp-net-dependencies/05-detail.png)

Vypadá to jde, kde je problém. Jsme jste přesně vymezená problém, takže teď jsme právě měli zjistit, proč tento volání trvá tak dlouho.

### <a name="request-timeline"></a>Časová osa požadavku
V případě různých je volání žádné závislosti, které je zvláště dlouhý. Ale přepnutím na zobrazení časové osy, uvidíme, kde došlo k zpoždění v našem interní zpracování:

![Najít volání vzdálené závislosti, identifikovat neobvyklé doba trvání](./media/app-insights-asp-net-dependencies/04-1.png)

Nejspíš velké mezery po volání první závislost, takže by měl podíváme na našem kódu, uvidíte, proč je.

### <a name="profile-your-live-site"></a>Profil živý web

Žádné představu, kde přejde čas? [Application Insights profileru](app-insights-profiler.md) trasování HTTP volání živý web a ukazuje, které funkce ve vašem kódu trvalo nejdelší dobu.

## <a name="failed-requests"></a>Neúspěšné požadavky
Neúspěšné požadavky může být také přidružen selhání volání závislosti. Jsme znovu, klikněte prostřednictvím sledovat problém.

![Klikněte na graf neúspěšných požadavků](./media/app-insights-asp-net-dependencies/06-fail.png)

Proklikejte se k výskytu chybné žádosti a podívejte se na jeho přidružené události.

![Klikněte na typ požadavku, klikněte na instanci systému na získat do jiného zobrazení stejné instance, klikněte na něj získat podrobnosti o výjimce.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analýzy
Můžete sledovat v závislosti [analýzy protokolů dotazu jazyka](https://docs.loganalytics.io/). Zde je několik příkladů:

* Najděte žádné volání se nezdařilo závislost:

```

    dependencies | where success != "True" | take 10
```

* Najděte volání AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Najděte závislostí volání přidružené k žádosti:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Volání AJAX najít přidružené zobrazení stránky:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Vlastní závislost sledování
Standardní modul sledování závislosti automaticky zjišťuje externí závislosti, jako jsou databáze a rozhraní REST API. Ale můžete chtít některé další součásti považován stejným způsobem.

Můžete napsat kód, který odesílá informace o závislostech, používající stejný [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) používané standardní moduly.

Například pokud vytvoříte kódu se sestavením, které nebylo napsat sami, může čas všechna volání, a zjistěte, jaký příspěvek umožňuje na váš doby odezvy. Pokud chcete, aby tato data zobrazí v grafech závislosti ve službě Application Insights, odeslat pomocí `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Pokud chcete vypnout modul sledování standardní závislostí, odeberte odkaz na DependencyTrackingTelemetryModule v [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Řešení potíží
*Závislost úspěch příznak vždycky zobrazí hodnotu PRAVDA nebo NEPRAVDA.*

*Úplné to není znázorněné dotazu SQL.*

Projděte si následující tabulky a zajistěte, že jste vybrali správnou konfiguraci chcete-li povolit monitorování závislostí pro vaši aplikaci.

| Platforma | Instalace |
| --- | --- |
| Server služby IIS |Buď [nainstalujte monitorování stavu na serveru](app-insights-monitor-performance-live-website-now.md). Nebo [upgradu vaší aplikace rozhraní .NET Framework 4.6 nebo novější](http://go.microsoft.com/fwlink/?LinkId=528259) a nainstalujte [Application Insights SDK](app-insights-asp-net.md) ve vaší aplikaci. |
| Webové aplikace Azure |Ve webové aplikaci ovládacího panelu [otevřete okno Application Insights ve webové aplikaci ovládacího panelu](app-insights-azure-web-apps.md) a instalace zvolte, pokud se zobrazí výzva. |
| Cloudové služby Azure |[Úloha spuštění použití](app-insights-cloudservices.md) nebo [nainstalovat rozhraní .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
* [Výjimky](app-insights-asp-net-exceptions.md)
* [Data uživatele a stránky](app-insights-javascript.md)
* [Dostupnost](app-insights-monitor-web-app-availability.md)
