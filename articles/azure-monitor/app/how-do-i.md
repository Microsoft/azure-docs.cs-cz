---
title: Jak mohu ... v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Nejčastější dotazy v přehledech aplikací.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 8d4b1e79c48b14ed7dce756468e4c48d633c3f04
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536858"
---
# <a name="how-do-i--in-application-insights"></a>Jak mám udělat ... pomocí Application Insights?
## <a name="get-an-email-when-"></a>Získejte e-mail, když ...
### <a name="email-if-my-site-goes-down"></a>E-mail, pokud můj web jde dolů
Nastavte [webový test dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-mail, pokud je můj web přetížený
Nastavte [výstrahu](../../azure-monitor/app/alerts.md) na **dobu odezvy serveru**. Prahová hodnota mezi 1 a 2 sekundami by měla fungovat.

![](./media/how-do-i/030-server.png)

Vaše aplikace může také vykazovat známky namáhání vrácením kódů selhání. Nastavte upozornění na **neúspěšné požadavky**.

Pokud chcete nastavit výstrahu na **výjimky serveru**, budete muset provést [další nastavení,](../../azure-monitor/app/asp-net-exceptions.md) aby se zoírá data.

### <a name="email-on-exceptions"></a>E-mail s výjimkami
1. [Nastavit monitorování výjimek](../../azure-monitor/app/asp-net-exceptions.md)
2. [Nastavení výstrahy](../../azure-monitor/app/alerts.md) na metriku Počet výjimek

### <a name="email-on-an-event-in-my-app"></a>Odeslání e-mailu na událost v mé aplikaci
Předpokládejme, že chcete získat e-mail, když dojde k určité události. Application Insights neposkytuje toto zařízení přímo, ale může [odeslat výstrahu, když metrika překročí prahovou hodnotu](../../azure-monitor/app/alerts.md).

Výstrahy lze nastavit na [vlastní metriky](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), i když ne vlastní události. Napište nějaký kód pro zvýšení metriky, když dojde k události:

    telemetry.TrackMetric("Alarm", 10);

nebo:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Vzhledem k tomu, že výstrahy mají dva stavy, musíte odeslat nízkou hodnotu, když uvážíte, že výstraha skončila:

    telemetry.TrackMetric("Alarm", 0.5);

Vytvořte graf v [průzkumníku metrik,](../../azure-monitor/platform/metrics-charts.md) abyste viděli budík:

![](./media/how-do-i/010-alarm.png)

Nyní nastavte výstrahu k požáru, když metrika přejde nad střední hodnotu na krátkou dobu:

![](./media/how-do-i/020-threshold.png)

Nastavte průměrovací období na minimum.

E-maily dostanete jak když metrika překročí, tak pod prahovou hodnotu.

Některé body ke zvážení:

* Výstraha má dva stavy ("výstraha" a "v pořádku"). Stav je vyhodnocen pouze v případě, že je přijata metrika.
* E-mail je odeslán pouze v případě, že se změní stav. To je důvod, proč musíte odeslat metriky s vysokou i nízkou hodnotou.
* K vyhodnocení výstrahy se zřete průměr přijatých hodnot za předchozí období. K tomu dochází při každém přijetí metriky, takže e-maily mohou být odesílány častěji než nastavené období.
* Vzhledem k tomu, že e-maily jsou odesílány jak na "upozornění", tak "zdravé", možná budete chtít zvážit přehodnocení události jednoho výstřelu jako stavu dvou států. Například místo události "dokončení úlohy" mají podmínku "nedokončená úloha", kde dostanete e-maily na začátku a na konci úlohy.

### <a name="set-up-alerts-automatically"></a>Automatické nastavení výstrah
[Vytvoření nových výstrah pomocí PowerShellu](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Správa přehledů aplikací pomocí PowerShellu
* [Vytvoření nových zdrojů](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Vytváření nových výstrah](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Oddělení telemetrie od různých verzí

* Více rolí v aplikaci: Použijte jeden prostředek Application Insights a filtrujte [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Oddělení vývojových, testovacích a verzí: Použijte různé prostředky Application Insights. Vyzvedněte si klíče instrumentace z web.config. [Další informace](../../azure-monitor/app/separate-resources.md)
* Vytváření sestav verze: Přidejte vlastnost pomocí telemetrické inicializátoru. [Další informace](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Sledování serverů back-end a aplikací klasické pracovní plochy
[Použijte modul sady Windows Server SDK](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Vizualizace dat
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Řídicí panel s metrikami z více aplikací
* V [Průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md)přizpůsobte graf a uložte ho jako oblíbenou. Připněte ji na řídicí panel Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Řídicí panel s daty z jiných zdrojů a přehledy aplikací
* [Export telemetrie do Power BI](../../azure-monitor/app/export-power-bi.md ).

Nebo

* Použijte SharePoint jako řídicí panel a zobrazte data ve webových částech SharePointu. [K exportu do SQL použijte nepřetržitý export a analýzu datových proudů](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Pomocí PowerView zkontrolujte databázi a vytvořte webovou část SharePointu pro PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Odfiltrovat anonymní nebo ověřené uživatele
Pokud se uživatelé přihlásí, můžete nastavit [ověřené ID uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (To se nestane automaticky.)

Pak můžete:

* Vyhledávání na konkrétních ID uživatelů

![](./media/how-do-i/110-search.png)

* Filtrování metrik anonymním nebo ověřeným uživatelům

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Změna názvů nebo hodnot vlastností
Vytvořte [filtr](../../azure-monitor/app/api-filtering-sampling.md#filtering). To vám umožní upravit nebo filtrovat telemetrii před odesláním z vaší aplikace do Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Seznam konkrétních uživatelů a jejich použití
Pokud chcete pouze [vyhledat konkrétní uživatele](#search-specific-users), můžete nastavit [ověřené ID uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Pokud chcete seznam uživatelů s daty, například na jaké stránky se dívají nebo jak často se přihlašují, máte dvě možnosti:

* [Nastavte ověřené ID uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [exportujte do databáze](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) a použijte vhodné nástroje k analýze uživatelských dat.
* Pokud máte jen malý počet uživatelů, odesílat vlastní události nebo metriky, používat data zájmu jako hodnotu metriky nebo název události a nastavení ID uživatele jako vlastnost. Chcete-li analyzovat zobrazení stránek, nahraďte standardní volání trackPageView jazyka JavaScript. Chcete-li analyzovat telemetrii na straně serveru, přidejte ID uživatele do všech telemetrií serveru pomocí inicializátoru telemetrie telemetrie telemetrie. Potom můžete filtrovat a segmentovat metriky a vyhledávání na ID uživatele.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Snížení návštěvnosti z mé aplikace do přehledů aplikací
* V [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)zakažte všechny moduly, které nepotřebujete, například kolektor čítače výkonu.
* Použijte [vzorkování a filtrování](../../azure-monitor/app/api-filtering-sampling.md) v sadě SDK.
* Na svých webových stránkách omezte počet volání Ajax nahlášených pro každé zobrazení stránky. Do fragmentu skriptu `instrumentationKey:...` za položkou vložte: `,maxAjaxCallsPerView:3` (nebo vhodné číslo).
* Pokud používáte [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), vypočítat agregaci dávek hodnot metriky před odesláním výsledku. Je přetížení TrackMetric(), který poskytuje pro to.

Přečtěte si další informace o [cenách a kvótách](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Zakázat telemetrii
Dynamické **zastavení a spuštění** shromažďování a přenosu telemetrie ze serveru:

### <a name="aspnet-classic-applications"></a>aplikace ASP.NET Classic

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Ostatní aplikace
Nedoporučuje se používat `TelemetryConfiguration.Active` singleton na konzoli nebo ASP.NET aplikace Core.
Pokud jste `TelemetryConfiguration` vytvořili `DisableTelemetry` instanci sami - nastaveno na `true`.

Pro ASP.NET základní aplikace `TelemetryConfiguration` můžete přistupovat k instanci pomocí [ASP.NET vkládání závislostí Jádra](/aspnet/core/fundamentals/dependency-injection/). Další podrobnosti naleznete v článku [ApplicationInsights for ASP.NET Základní aplikace.](../../azure-monitor/app/asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Zakázání vybraných standardních kolekcí
Můžete zakázat standardní kolektory (například čítače výkonu, požadavky HTTP nebo závislosti)

* **ASP.NET aplikace** - Odstranit nebo zakomentovat příslušné řádky v [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET základní aplikace** – postupujte podle možností konfigurace modulů telemetrie v [applicationinsights ASP.NET jádru](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Zobrazit čítače výkonu systému
Mezi metriky, které můžete zobrazit v průzkumníku metrik, jsou sada čítačů výkonu systému. Existuje předdefinované okno s názvem **Servery,** které zobrazuje několik z nich.

![Otevření prostředku Application Insights a kliknutí na Servery](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Pokud nevidíte žádná data čítače výkonu
* **Server služby IIS** na vlastním počítači nebo na virtuálním počítači. [Nainstalujte sledování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Web Azure** – ještě nepodporujeme čítače výkonu. Existuje několik metrik, které můžete získat jako standardní součást ovládacího panelu webu Azure.
* **Unix server** - [Instalace sbírat](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Zobrazení více čítačů výkonu
* Nejprve [přidejte nový graf](../../azure-monitor/platform/metrics-charts.md) a zjistěte, zda je čítač v základní sadě, kterou nabízíme.
* Pokud ne, [přidejte čítač do sady shromážděné modulem čítače výkonu](../../azure-monitor/app/performance-counters.md).
