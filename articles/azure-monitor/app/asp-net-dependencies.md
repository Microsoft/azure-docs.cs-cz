---
title: Závislost sledování ve službě Azure Application Insights | Dokumentace Microsoftu
description: Analýza místního využití, dostupnosti a výkonu nebo webová aplikace Microsoft Azure s nástrojem Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: d86df171de807c23aad6dd2cb8e2128098dede81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812445"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Nastavení Application Insights: Sledování závislostí
A *závislost* je externí komponenta, která volá vaši aplikaci. Obvykle se jedná o službu volána pomocí protokolu HTTP, nebo databázi nebo systému souborů. [Application Insights](../../azure-monitor/app/app-insights-overview.md) měří jak dlouho aplikaci čeká závislosti a jak často závislosti volání selže. Můžete prozkoumat konkrétní volání a spojit je žádosti a výjimky.

Toto monitorování závislostí out-of-the-box aktuálně hlásí volání pro tyto typy závislostí:

* Server
  * Databáze SQL
  * Rozhraní ASP.NET web a služby WCF, které používají vazby založené na protokolu HTTP
  * Místní nebo vzdálené volání HTTP
  * Azure Cosmos DB, table, úložiště objektů blob a fronty 
* Webové stránky
  * Volání AJAX

Monitorování funguje pomocí [bajtů kód instrumentace](https://msdn.microsoft.com/library/z9z62c29.aspx) přibližně vyberte metody, nebo podle DiagnosticSource zpětných volání (nejnovější sady .NET SDK) z rozhraní .NET Framework. Je minimální nároky na výkon.

Můžete je zapsat také vlastních volání sady SDK a monitorujte Další závislosti, v kódu klienta a serveru pomocí [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> Azure Cosmos DB se automaticky sleduje pouze v případě [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) se používá. Režim TCP nebude zachycena Application Insights.

## <a name="set-up-dependency-monitoring"></a>Nastavení monitorování závislostí
Závislost částečné informace jsou shromažďovány automaticky [Application Insights SDK](asp-net.md). Pokud chcete získat kompletní data, nainstalujte příslušného agenta pro hostitelský server.

| Platforma | Instalace |
| --- | --- |
| Server služby IIS |Buď [nainstalujte na server monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md) nebo [Upgrade vaší aplikace na rozhraní .NET framework 4.6 nebo novější](https://go.microsoft.com/fwlink/?LinkId=528259) a nainstalujte [Application Insights SDK](asp-net.md) ve vaší aplikaci. |
| Webové aplikace Azure |V váš řídicí panel webové aplikace [otevřete okno Application Insights v ovládacím panelu webové aplikace](../../azure-monitor/app/azure-web-apps.md) a možnost instalace, pokud se zobrazí výzva. |
| Cloudové služby Azure |[Úlohy po spuštění použijte](../../azure-monitor/app/cloudservices.md) nebo [nainstalovat rozhraní .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Kde najít data závislostí
* [Mapa aplikace](#application-map) vizualizuje závislosti mezi vaší aplikací a sousední komponenty.
* [Výkon, prohlížeče a selhání oken](#performance-and-blades) zobrazit závislosti dat serveru.
* [Okno prohlížečů](#ajax-calls) ukazuje volání AJAX z prohlížečů uživatelů.
* Klikněte na z pomalý nebo neúspěšné žádosti ke kontrole jejich voláním závislostí.
* [Analytics](#analytics) slouží k dotazování na data závislostí.

## <a name="application-map"></a>Mapa aplikace
Mapa aplikace se chová jako vizuální pomůcky zjišťování závislosti mezi komponentami vaší aplikace. Je generována automaticky na základě telemetrie z vaší aplikace. Tento příklad ukazuje volání AJAX ze skriptů prohlížeče a volání REST z aplikace serveru dvě externí služby.

![Mapa aplikace](./media/asp-net-dependencies/08.png)

* **Přejděte v polích** relevantní závislosti a další grafy.
* **Připněte mapu** k [řídicí panel](../../azure-monitor/app/app-insights-dashboards.md), kde bude plně funkční.

[Další informace](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Okna výkonu a selhání
V okně výkonu se zobrazí dobu trvání volání závislostí prováděných aplikací serveru. Je souhrn graf a tabulku segmentované podle volání.

![Grafy závislosti okně výkonu.](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Proklikejte se prostřednictvím souhrnné grafy nebo položky, které se mají hledat nezpracovaná výskytů těchto volání.

![Instance volání závislostí](./media/asp-net-dependencies/dependency-call-instance.png)

**Počet selhání** jsou uvedeny na **selhání** okno. Selhání je jakýkoli návratový kód, který není v rozsahu 200-399 nebo neznámý.

> [!NOTE]
> **100 % selhání?** -Pravděpodobně označuje, že se zobrazuje jenom data částečné závislostí. Je potřeba [nastavení monitorování závislostí pro vaši platformu](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Volání AJAX
V okně prohlížeče zobrazuje dobu trvání a selhání míru volání AJAX z [JavaScript na webových stránkách](../../azure-monitor/app/javascript.md). Zobrazí se jako závislosti.

## <a name="diagnosis"></a> Diagnostika pomalé žádosti
Každý požadavek událost je přidružena volání závislostí, výjimek a dalších událostí, které jsou sledovány během zpracování požadavku aplikace. Takže pokud některé požadavky jsou nízkého výkonu, můžete zjistit, jestli je z důvodu zpomalení odezvy ze závislostí.

Projděme si příklad, který.

### <a name="tracing-from-requests-to-dependencies"></a>Trasování požadavků závislostí
Otevřete okno výkon a podívejte se na mřížky požadavků:

![Seznam požadavků s průměry a počty](./media/asp-net-dependencies/02-reqs.png)

Ten hlavní trvá velmi dlouho. Podívejme se, pokud jsme můžete zjistit, kde byl stráven čas.

Klikněte na tento řádek a zobrazte jednotlivé žádosti o události:

![Seznam opakování žádosti](./media/asp-net-dependencies/03-instances.png)

Kliknutím na jakoukoli instanci dlouhotrvající kontrolovat další, a posuňte se dolů volání vzdálené závislosti týkající se této žádosti:

![Volání vzdálených závislostí vyhledat, identifikovat neobvyklé doba trvání](./media/asp-net-dependencies/04-dependencies.png)

Vypadá jako většinu času údržby, které této žádosti se využilo na volání do místní služby.

Vyberte tento řádek, chcete-li získat další informace:

![Proklikejte se k identifikaci nadměrné spotřeby dané vzdálené závislosti](./media/asp-net-dependencies/05-detail.png)

Vypadá to, čem problém spočívá. My jsme přesně vymezená problém, tak teď jsme právě potřebujete zjistit, proč toto volání je trvá tak dlouho.

### <a name="request-timeline"></a>Časová osa žádosti
V případě různých neexistuje žádná volání závislosti, které je zvláště dlouhý. Ale přepnutím na zobrazení časové osy, můžeme vidět, kde došlo k zpoždění v našich interních zpracování:

![Volání vzdálených závislostí vyhledat, identifikovat neobvyklé doba trvání](./media/asp-net-dependencies/04-1.png)

Zdá velké mezery po volání první závislostí, takže by měl podíváme na naše kódem, abyste viděli, proč je.

### <a name="profile-your-live-site"></a>Profil živého webu

Představu kde čas prochází? [Application Insights profiler](../../azure-monitor/app/profiler.md) trasy HTTP zavolá na váš živý web a ukazuje, které funkce ve vašem kódu trvalo nejdelší dobu.

## <a name="failed-requests"></a>Neúspěšné požadavky
Neúspěšné požadavky může být také přidružen neúspěšných volání závislostí. Opět jsme proklikat ke sledování problému.

![Klikněte na graf neúspěšných žádostí](./media/asp-net-dependencies/06-fail.png)

Proklikejte se k výskytu neúspěšných požadavků a podívejte se na jeho související události.

![Klikněte na typ požadavku, klikněte na instance, kterou chcete získat do jiného zobrazení stejné instance, klikněte na něj zobrazíte podrobnosti o výjimce.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analýzy
Můžete sledovat v závislosti [dotazovací jazyk Log Analytics](https://aka.ms/LogAnalyticsLanguage). Zde je několik příkladů:

* Najdete všechna neúspěšná volání:

```

    dependencies | where success != "True" | take 10
```

* Najděte volání AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Vyhledejte žádosti spotřebují volání závislostí:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Najít volání AJAX přidružený k zobrazení stránek:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Vlastní sledování závislostí
Standardní modul sledování závislosti automaticky zjišťuje externí závislosti, jako jsou databáze a rozhraní REST API. Nicméně je možné pracovat stejně jako některé další komponenty.

Můžete napsat kód, který odesílá informace o závislostech, pomocí stejných [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) , který se používá standardní moduly.

Například pokud vytváření kódu pomocí sestavení, které jste sami nenapsali může čas všechna volání, a zjistěte, jaké příspěvek, odešle vaše doby odezvy. Pokud chcete, aby tato data zobrazí v grafech závislostí ve službě Application Insights, odeslat ho pomocí `TrackDependency`.

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

Pokud chcete vypnout sledování modulu standardních závislostí, odeberte odkaz na DependencyTrackingTelemetryModule v [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Řešení potíží
*Závislost úspěch příznak vždycky zobrazí hodnotu true nebo false.*

*Dotaz SQL není zobrazené v plném rozsahu.*

Naleznete v následující tabulce a zajistit, že jste zvolili správnou konfiguraci chcete povolit monitorování závislostí pro vaši aplikaci.

| Platforma | Instalace |
| --- | --- |
| Server služby IIS |Buď [nainstalujte na server monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md). Nebo [Upgrade vaší aplikace na rozhraní .NET framework 4.6 nebo novější](https://go.microsoft.com/fwlink/?LinkId=528259) a nainstalujte [Application Insights SDK](asp-net.md) ve vaší aplikaci. |
| Služba IIS Express |Místo toho použijte Server služby IIS. |
| Webové aplikace Azure |V váš řídicí panel webové aplikace [otevřete okno Application Insights v ovládacím panelu webové aplikace](../../azure-monitor/app/azure-web-apps.md) a možnost instalace, pokud se zobrazí výzva. |
| Cloudová služba Azure |[Úlohy po spuštění použijte](../../azure-monitor/app/cloudservices.md) nebo [nainstalovat rozhraní .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
* [Výjimky](../../azure-monitor/app/asp-net-exceptions.md)
* [Data uživatele a stránky](../../azure-monitor/app/javascript.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
