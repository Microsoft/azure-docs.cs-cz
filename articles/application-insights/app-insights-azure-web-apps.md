---
title: Monitorování výkonu webových aplikací Azure | Dokumentace Microsoftu
description: Monitorování výkonu webových aplikací Azure. Můžete vytvářet grafy zatížení a doby odezvy nebo informací o závislosti a nastavovat upozornění týkající se výkonu.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 1a031ede814a963f25f1a3a3fc9725d9e5670a8e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824638"
---
# <a name="monitor-azure-web-app-performance"></a>Monitorování výkonu webových aplikací Azure
Na webu [Azure Portal](https://portal.azure.com) můžete pro své [webové aplikace Azure](../app-service/app-service-web-overview.md) nastavit monitorování výkonu. [Azure Application Insights](app-insights-overview.md) využívá vaši aplikaci k odesílání telemetrických dat o jejích aktivitách do služby Application Insights, kde se ukládají a analyzují. Tam lze grafy metrik a vyhledávací nástroje použít při řešení problémů s diagnostikou, při zvyšování výkonu a při vyhodnocování využití.

## <a name="run-time-or-build-time"></a>Za běhu nebo při sestavení
Monitorování s použitím aplikace můžete konfigurovat dvěma způsoby:

* **Za běhu** – Můžete vybrat rozšíření pro monitorování výkonu, když je webová aplikace již v živém provozu. Aplikaci není třeba znovu sestavit ani instalovat. Obdržíte standardní sadu balíčků, které monitorují dobu odezvy, úspěšnost, výjimky, závislosti a další. 
* **Při sestavení** – Do aplikace můžete balíček nainstalovat během vývoje. Tato možnost nabízí větší variabilitu. Kromě stejných standardních balíčků můžete napsat kód pro přizpůsobení telemetrických dat nebo pro odesílání vlastních telemetrických dat. Můžete protokolovat konkrétní aktivity nebo zaznamenávat události podle sémantiky domény aplikace. 

## <a name="run-time-instrumentation-with-application-insights"></a>Použití za běhu s Application Insights
Pokud už webovou aplikaci v Azure spouštíte, máte již monitorování do jisté míry k dispozici: můžete monitorovat požadavky a chybovost. Přidejte Application Insights a získejte další možnosti, například zaznamenávání doby odezvy, monitorování volání závislostí, inteligentní detekci a výkonný dotazovací jazyk Log Analytics. 

1. **Vyberte Application Insights** pro svou webovou aplikaci na ovládacím panelu Azure.

    ![V části Nastavení zvolte Application Insights](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * Můžete vytvořit nový prostředek, pokud jste už nastavili prostředek Application Insights pro tuto aplikaci. 

    > [!NOTE]
    > Po kliknutí na **OK** k vytvoření nového prostředku, zobrazí se výzva k **použít nastavení monitorování**. Výběr **pokračovat** propojí nový prostředek Application Insights do své webové aplikace, to může také **aktivujte restartování vaší webové aplikace**. 

    ![Používejte webovou aplikaci.](./media/app-insights-azure-web-apps/create-resource.png)

2. Po vytvoření vyberte váš prostředek **kontrola a aktualizace rozšíření Application Insights pro webovou aplikaci** potvrďte, že rozšíření je aktuální.

     ![Zkontrolujte a aktualizujte rozšíření webu](./media/app-insights-azure-web-apps/check-and-update.png)

3. Po instalaci Application Insights **webovou aplikaci používejte**.

   **Povolte monitorování na straně klienta** pro zobrazení stránek a telemetrii uživatelů.

   * Klikněte na Nastavení > Nastavení aplikace.
   * V části Nastavení aplikace přidejte novou dvojici klíče a hodnoty:

    Klíč: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Hodnota: `true`
   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.
4. Prozkoumejte data monitorování vaší aplikace tak, že vyberete **nastavení** > **Application Insights** > **zobrazit více v Application Insights**.

Později můžete pomocí Application Insights aplikaci sestavit, pokud budete chtít.

*Jak lze odebrat Application Insights nebo přepnout na odesílání do jiného prostředku?*

* V Azure otevřete okno s ovládacími prvky webové aplikace a v části Vývojové nástroje otevřete **Rozšíření**. Odstraňte rozšíření Application Insights. Potom v části Monitorování zvolte Application Insights a vytvořte nebo vyberte požadovaný prostředek.

## <a name="build-the-app-with-application-insights"></a>Sestavení aplikace s použitím Application Insights
Application Insights může poskytovat podrobnější telemetrie po nainstalování sady SDK do příslušné aplikace. Konkrétně je možné shromažďovat protokoly trasování, [psát vlastní telemetrii](app-insights-api-custom-events-metrics.md)a získávat podrobnější sestavy výjimek.

1. **V sadě Visual Studio** (2013 s aktualizací 2 nebo novější) nakonfigurujte Application Insights pro svůj projekt.

    Klikněte pravým tlačítkem na webový projekt a vyberte **Přidat > Application Insights** nebo **projektu** > **Application Insights**  >  **Nakonfigurovat Application Insights**.

    ![Klikněte pravým tlačítkem myši na webový projekt a zvolte Přidat nebo Nakonfigurovat Application Insights.](./media/app-insights-azure-web-apps/03-add.png)

    Pokud budete vyzváni k přihlášení, použijte přihlašovací údaje ke svému účtu Azure.

    Operace má dva důsledky:

   1. Vytvoří prostředek Application Insights v Azure, kde se ukládají, analyzují a zobrazují telemetrická data.
   2. Přidá do kódu balíček NuGet Application Insights (pokud tam ještě není) a nakonfiguruje ho tak, aby odesílal telemetrická data do příslušného prostředku Azure.
2. **Otestujte telemetrická data** spuštěním aplikace v počítači pro vývoj (F5).
3. **Publikujte aplikaci** v Azure obvyklým způsobem. 

*Jak lze přepnout na odesílání do jiného prostředku Application Insights?*

* V sadě Visual Studio klikněte pravým tlačítkem na projekt, zvolte **Nakonfigurovat Application Insights** a zvolte požadovaný prostředek. Budete mít možnost vytvořit nový prostředek. Proveďte opětné sestavení a nasazení.

## <a name="more-telemetry"></a>Další telemetrická data

* [Data načítání webové stránky](app-insights-javascript.md)
* [Vlastní telemetrická data](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup
* [Spusťte profiler v živé aplikaci](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../azure-monitor/platform/data-collection.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../monitoring-and-diagnostics/monitoring-overview-alerts.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](app-insights-javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](app-insights-monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.

