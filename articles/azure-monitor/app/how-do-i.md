---
title: Jak na to... ve službě Azure Application Insights | Dokumentace Microsoftu
description: Nejčastější dotazy týkající se ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 5e22a3f3b362811fd87460ec41b61a990f4d83fb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074202"
---
# <a name="how-do-i--in-application-insights"></a>Jak mám udělat ... pomocí Application Insights?
## <a name="get-an-email-when-"></a>Získejte e-mailu při...
### <a name="email-if-my-site-goes-down"></a>Pokud web přestane fungovat e-mailu
Nastavení [dostupnosti webového testu](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-mailu, pokud je přetížena Můj web
Nastavení [výstraha](../../azure-monitor/app/alerts.md) na **doba odezvy serveru**. Prahová hodnota mezi 1 a 2 sekundy, by mělo fungovat.

![](./media/how-do-i/030-server.png)

Aplikace může také zobrazovat známky kmen vrácením Kódy selhání. Nastavení upozornění na **neúspěšné požadavky**.

Pokud chcete nastavení upozornění na **výjimky serveru**, možná budete muset provést [některé další nastavení](../../azure-monitor/app/asp-net-exceptions.md) Chcete-li zobrazit data.

### <a name="email-on-exceptions"></a>Odeslání e-mailu výjimky
1. [Nastavit monitorování výjimek](../../azure-monitor/app/asp-net-exceptions.md)
2. [Nastavení upozornění](../../azure-monitor/app/alerts.md) o výjimce počet metrik

### <a name="email-on-an-event-in-my-app"></a>Odeslání e-mailu událost ve své aplikaci
Předpokládejme, že chcete dostávat e-maily, když dojde k určité události. Application Insights neposkytuje Azure přímo, ale může [Odeslat výstrahu, pokud metrika překročí mezní hodnotu](../../azure-monitor/app/alerts.md).

Upozornění je možné nastavit na [vlastní metriky](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), i když není vlastní události. Napsání kódu pro zvýšení metriku při výskytu události:

    telemetry.TrackMetric("Alarm", 10);

nebo:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Protože výstrahy mají dva stavy, budete muset odeslat nízkou hodnotu, pokud zvažujete výstrahu, kterou chcete být ukončeny:

    telemetry.TrackMetric("Alarm", 0.5);

Vytvoření grafu v [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md) zobrazíte vaše upozornění:

![](./media/how-do-i/010-alarm.png)

Nyní nastavte výstrahu, která se aktivuje, když metriky překročí hodnotu mid na krátkou dobu:

![](./media/how-do-i/020-threshold.png)

Nastavení je průměrované období na minimum.

Když metriky překročí i pod prahovou hodnotou, dostanete e-mailů.

Některé body ke zvážení:

* Upozornění má dva stavy ("alert" a "v pořádku"). Stav je vyhodnocen pouze v případě, že se metriky přijetí.
* E-mail je odeslán, pouze při změně stavu. Toto je Proč je nutné posílat vysokou a nízkou hodnotu metriky.
* K vyhodnocení výstrahy, je průměr pořídí přijaté hodnoty předchozím období. K tomu dochází pokaždé, když metriky přijetí, aby byla odeslána e-mailů častěji, než je doba, kterou jste nastavili.
* Vzhledem k tomu, jak na "alert" a "v pořádku", pošlou se e-maily, můžete chtít zvážit znovu uvažujete jednorázové události jako podmínku dvou stavů. Například místo v případě události "úloha dokončena" máte podmínku "probíhající úloze", kde získat e-mailů na začátku a konci úlohy.

### <a name="set-up-alerts-automatically"></a>Automaticky nastavit výstrahy
[Použití Powershellu k vytvoření nové výstrahy](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Použití Powershellu ke správě služby Application Insights
* [Vytvářet nové prostředky](../../azure-monitor/app/powershell-script-create-resource.md)
* [Vytvořit nové výstrahy](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Samostatné telemetrická data z různých verzí

* Více rolí v aplikaci: Použít na jeden prostředek Application Insights a vyfiltrujte [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Oddělení vývoje, testování a vydání verze: Použijte různé prostředky Application Insights. Sbírání instrumentačních klíčů ze souboru web.config. [Další informace](../../azure-monitor/app/separate-resources.md)
* Vytváření sestav verze sestavení: Přidání vlastnosti pomocí inicializátoru telemetrie. [Další informace](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorování back-end serverů a aplikací klasické pracovní plochy
[Použít modul Windows Server SDK](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Vizualizace dat
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Řídicí panel s metrikami z více aplikací
* V [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md), přizpůsobení grafu a uložit jako oblíbenou položku. Připnete na řídicí panel Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Řídicí panel s daty z jiných zdrojů a Application Insights
* [Export telemetrie do Power BI](../../azure-monitor/app/export-power-bi.md ).

Nebo

* Používání služby SharePoint jako řídicí panel, zobrazení dat ve webové části služby SharePoint. [Export do SQL pomocí průběžný export a Stream Analytics](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Pomocí PowerView zkontrolujte databázi a vytvářet webové části služby SharePoint pro PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Vyfiltrování ověřený nebo anonymní uživatelé
Pokud se vaši uživatelé přihlásí, můžete nastavit [ověřit id uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Je tomu tak není automaticky.)

Pak můžete:

* Hledat na ID konkrétního uživatele

![](./media/how-do-i/110-search.png)

* Filtrovat metriky pro anonymní nebo ověřeného uživatele

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Upravit názvy vlastností nebo hodnot
Vytvoření [filtr](../../azure-monitor/app/api-filtering-sampling.md#filtering). To umožňuje změnit nebo filtrování telemetrických dat před odesláním z vaší aplikace do služby Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Seznam konkrétních uživatelů a jejich používání
Pokud chcete jen [vyhledávání pro konkrétní uživatele](#search-specific-users), můžete nastavit [ověřit id uživatele](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Pokud chcete seznam uživatelů s daty, jako jsou například jaké stránky, podívejte se na nebo jak často přihlášení, máte dvě možnosti:

* [Id ověřeného uživatele sady](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [export do databáze](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) a použijte vhodné nástroje k analýze dat uživatele existuje.
* Pokud máte pouze malý počet uživatelů, posílat vlastní události nebo metriky, jako název metriky hodnotu nebo událost pomocí dat, které vás zajímají a nastavení id uživatele jako vlastnost. K analýze zobrazení stránek, nahraďte standardní trackPageView volání JavaScriptu. K analýze telemetrických dat na straně serveru, pomocí inicializátoru telemetrie přidat id uživatele k veškeré telemetrii serveru. Pak můžete filtrujte a segmentujte metrik a vyhledávání na id uživatele.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Omezit přenos z aplikace do služby Application Insights
* V [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), zakažte všechny moduly, které už nebudete potřebovat, takový výkon čítače kolektoru.
* Použití [vzorkování a filtrování](../../azure-monitor/app/api-filtering-sampling.md) v sadě SDK.
* Na webových stránkách omezte počet volání Ajax hlášených pro každé zobrazení stránky. V tomto fragmentu kódu skriptu po `instrumentationKey:...` , vložte: `,maxAjaxCallsPerView:3` (nebo vhodný číslo).
* Pokud používáte [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), compute agregace dávky hodnoty metrik před odesláním výsledek. Přetížení metody TrackMetric(), která poskytuje pro, který není k dispozici.

Další informace o [ceny a kvóty](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Zakázat telemetrii
K **dynamicky zastavení a spuštění** shromažďování a předávání telemetrických dat ze serveru:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



K **zakázat vybrané standardní Kolektory** – například čítače výkonu, požadavky HTTP nebo závislosti – odstranit nebo okomentovat odpovídající řádky v [soubor ApplicationInsights.config](../../azure-monitor/app/api-custom-events-metrics.md). Může to provedete, například pokud chcete poslat TrackRequest data.

## <a name="view-system-performance-counters"></a>Zobrazit čítače výkonu systému
Mezi metriky, které můžete zobrazit v Průzkumníku metrik představují sadu systému čítače výkonu. Není předdefinovanou okno s názvem **servery** , který zobrazí několik z nich.

![Otevřete prostředek Application Insights a klikněte na servery](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Pokud se nezobrazují žádná data čítače výkonu
* **Server služby IIS** na vlastním počítači nebo na virtuálním počítači. [Nainstalujte monitorování stavu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Webový server** – zatím nepodporujeme čítače výkonu. Existuje několik metrik, které můžete získat jako standardní součást ovládacího panelu webu Azure.
* **Server systému UNIX** - [nainstalujte collectd](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Chcete-li zobrazit další čítače výkonu
* Nejprve je potřeba [přidejte nový graf](../../azure-monitor/app/metrics-explorer.md) a podívejte se, pokud čítač je v základní sadě, kterou nabízíme.
* V opačném případě [přidat čítače do sady modul čítače výkonu shromážděné](../../azure-monitor/app/performance-counters.md).
