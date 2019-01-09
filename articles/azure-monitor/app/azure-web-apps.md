---
title: Monitorování výkonu Azure app services | Dokumentace Microsoftu
description: Sledování výkonu aplikací pro Azure app Service. Můžete vytvářet grafy zatížení a doby odezvy nebo informací o závislosti a nastavovat upozornění týkající se výkonu.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 17d8eff39eabb2f7b4968bf74d2482b980fe8060
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116615"
---
# <a name="monitor-azure-app-service-performance"></a>Sledování výkonu služby Azure App Service
V [webu Azure Portal](https://portal.azure.com) můžete nastavit application performance monitoring pro aplikace pro webové aplikace, back-endů mobilních a API apps v [služby Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) využívá vaši aplikaci k odesílání telemetrických dat o jejích aktivitách do služby Application Insights, kde se ukládají a analyzují. Tam lze grafy metrik a vyhledávací nástroje použít při řešení problémů s diagnostikou, při zvyšování výkonu a při vyhodnocování využití.

## <a name="run-time-or-build-time"></a>Za běhu nebo při sestavení
Monitorování s použitím aplikace můžete konfigurovat dvěma způsoby:

* **Za běhu** – můžete vybrat rozšíření pro službu app service je již živá monitorování výkonu. Aplikaci není třeba znovu sestavit ani instalovat. Obdržíte standardní sadu balíčků, které monitorují dobu odezvy, úspěšnost, výjimky, závislosti a další. 
* **Při sestavení** – Do aplikace můžete balíček nainstalovat během vývoje. Tato možnost nabízí větší variabilitu. Kromě stejných standardních balíčků můžete napsat kód pro přizpůsobení telemetrických dat nebo pro odesílání vlastních telemetrických dat. Můžete protokolovat konkrétní aktivity nebo zaznamenávat události podle sémantiky domény aplikace. 

## <a name="run-time-instrumentation-with-application-insights"></a>Použití za běhu s Application Insights
Pokud už máte službu app service v Azure, již nabízí některá monitorování: požadavky a chybovost. Přidejte Application Insights a získejte další možnosti, například zaznamenávání doby odezvy, monitorování volání závislostí, inteligentní detekci a výkonný dotazovací jazyk Log Analytics. 

1. **Vyberte Application Insights** Azure ovládacího panelu pro službu app service.

    ![V části Nastavení zvolte Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Můžete vytvořit nový prostředek, pokud jste už nastavili prostředek Application Insights pro tuto aplikaci. 

    > [!NOTE]
    > Po kliknutí na **OK** k vytvoření nového prostředku, zobrazí se výzva k **použít nastavení monitorování**. Výběr **pokračovat** propojí nový prostředek Application Insights do vaší služby app service, to může také **aktivujte restartování služby app service**. 

    ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource.png)

2. Po určení, který prostředek se má použít, můžete nastavit, jak chcete application insights shromažďovat data pro jednotlivé platformy pro vaši aplikaci.

    ![Vyberte možnosti jednotlivé platformy](./media/azure-web-apps/choose-options.png)

3. **Instrumentace vaší služby app service** po instalaci Application Insights.

   **Povolte monitorování na straně klienta** pro zobrazení stránek a telemetrii uživatelů.

   * Klikněte na Nastavení > Nastavení aplikace.
   * V části Nastavení aplikace přidejte novou dvojici klíče a hodnoty:

    Klíč: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Hodnota: `true`
   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.
4. Prozkoumejte data monitorování vaší aplikace tak, že vyberete **nastavení** > **Application Insights** > **zobrazit více v Application Insights**.

Později můžete pomocí Application Insights aplikaci sestavit, pokud budete chtít.

*Jak lze odebrat Application Insights nebo přepnout na odesílání do jiného prostředku?*

* V Azure, otevřete ovládací okno webové aplikace a v části nastavení, otevřete **Application Insights**. Application Insights můžete vypnout kliknutím **zakázat** v horní části stránky, nebo vybrat nový prostředek v **změnit váš prostředek** oddílu.

## <a name="build-the-app-with-application-insights"></a>Sestavení aplikace s použitím Application Insights
Application Insights může poskytovat podrobnější telemetrie po nainstalování sady SDK do příslušné aplikace. Konkrétně je možné shromažďovat protokoly trasování, [psát vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md)a získávat podrobnější sestavy výjimek.

1. **V sadě Visual Studio** (2013 s aktualizací 2 nebo novější) nakonfigurujte Application Insights pro svůj projekt.

    Klikněte pravým tlačítkem na webový projekt a vyberte **Přidat > Application Insights** nebo **projektu** > **Application Insights**  >  **Nakonfigurovat Application Insights**.

    ![Klikněte pravým tlačítkem myši na webový projekt a zvolte Přidat nebo Nakonfigurovat Application Insights.](./media/azure-web-apps/03-add.png)

    Pokud budete vyzváni k přihlášení, použijte přihlašovací údaje ke svému účtu Azure.

    Operace má dva důsledky:

   1. Vytvoří prostředek Application Insights v Azure, kde se ukládají, analyzují a zobrazují telemetrická data.
   2. Přidá do kódu balíček NuGet Application Insights (pokud tam ještě není) a nakonfiguruje ho tak, aby odesílal telemetrická data do příslušného prostředku Azure.
2. **Otestujte telemetrická data** spuštěním aplikace v počítači pro vývoj (F5).
3. **Publikujte aplikaci** v Azure obvyklým způsobem. 

*Jak lze přepnout na odesílání do jiného prostředku Application Insights?*

* V sadě Visual Studio klikněte pravým tlačítkem na projekt, zvolte **Nakonfigurovat Application Insights** a zvolte požadovaný prostředek. Budete mít možnost vytvořit nový prostředek. Proveďte opětné sestavení a nasazení.

## <a name="more-telemetry"></a>Další telemetrická data

* [Data načítání webové stránky](../../azure-monitor/app/javascript.md)
* [Vlastní telemetrická data](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="troubleshooting"></a>Řešení potíží

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED způsobí, že neúplné odpovědi HTML ve webových aplikací .NET CORE.

Povolení jazyka Javascript pomocí App Services může způsobit, že odpovědi html se oříznou.

- Alternativní řešení 1: nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED na hodnotu false nebo ho úplně odeberte a restartování
- Alternativní řešení 2: přidání sady sdk prostřednictvím kódu a odebrání rozšíření (Profiler a Snapshot debugger nebudou s touto konfigurací)

Tento problém sledujeme [zde](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)

## <a name="next-steps"></a>Další postup
* [Spusťte profiler v živé aplikaci](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../../azure-monitor/platform/data-collection.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../../azure-monitor/platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](../../azure-monitor/app/javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.

