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
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122161"
---
# <a name="monitor-azure-app-service-performance"></a>Sledování výkonu služby Azure App Service
V [webu Azure portal](https://portal.azure.com) můžete nastavit application performance monitoring pro aplikace pro webové aplikace, back-endů mobilních a API apps v [služby Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) využívá vaši aplikaci k odesílání telemetrických dat o jejích aktivitách do služby Application Insights, kde se ukládají a analyzují. Tam lze grafy metrik a vyhledávací nástroje použít při řešení problémů s diagnostikou, při zvyšování výkonu a při vyhodnocování využití.

## <a name="runtime-or-build-time"></a>Modul runtime nebo sestavení
Monitorování s použitím aplikace můžete konfigurovat dvěma způsoby:

* **Modul runtime** – můžete vybrat rozšíření pro službu app service je již živá monitorování výkonu. Není nutné znovu sestavit nebo znovu nainstalovat aplikaci. Obdržíte standardní sadu balíčků, které monitorují dobu odezvy, úspěšnost, výjimky, závislosti a další.

* **Při sestavení** – Do aplikace můžete balíček nainstalovat během vývoje. Tato možnost nabízí větší variabilitu. Kromě stejných standardních balíčků můžete napsat kód pro přizpůsobení telemetrických dat nebo pro odesílání vlastních telemetrických dat. Můžete protokolovat konkrétní aktivity nebo zaznamenávat události podle sémantiky domény aplikace. To také vám možnost otestovat nejnovější sadu SDK Application Insights, protože se můžete rozhodnout k hodnocení beta verze sady SDK, že monitorování modulu runtime je omezen na nejnovější stabilní verze.

## <a name="runtime-instrumentation-with-application-insights"></a>Instrumentace modulu runtime pomocí nástroje Application Insights
Pokud aktuálně používáte službu app service v Azure, které již nabízí některá monitorování: požadavky a chybovost ve výchozím nastavení. Přidejte Application Insights a získejte další možnosti, například zaznamenávání doby odezvy, monitorování volání závislostí, inteligentní detekci a přístup k výkonný dotazovací jazyk Kusto. 

1. **Vyberte Application Insights** Azure ovládacího panelu pro službu app service.

    ![V části Nastavení zvolte Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Můžete vytvořit nový prostředek, pokud jste už nastavili prostředek Application Insights pro tuto aplikaci. 

     > [!NOTE]
     > Po kliknutí na **OK** k vytvoření nového prostředku, zobrazí se výzva k **použít nastavení monitorování**. Výběr **pokračovat** propojí nový prostředek Application Insights do vaší služby app service, to může také **aktivujte restartování služby app service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource.png)

2. Po určení, který prostředek se má použít, můžete nastavit, jak chcete application insights shromažďovat data pro jednotlivé platformy pro vaši aplikaci. Monitorování aplikace ASP.NET je ve výchozím nastavení se dvou různých úrovních kolekce.

    ![Vyberte možnosti jednotlivé platformy](./media/azure-web-apps/choose-options-new.png)

   * .NET **základní kolekce** úroveň nabízí základní možnosti APM jednou instancí.
    
   * .NET **doporučuje kolekce** úroveň:
       * Přidá trendy využití procesoru, paměti a vstupně-výstupních operací.
       * Koreluje mikroslužby napříč hranicemi požadavku nebo závislost.
       * Shromažďuje trendy využití a umožňuje spojitost s míněním z výsledky dostupnosti transakcí.
       * Shromažďuje výjimky ošetřena hostitelský proces.
       * Při vzorkování se používá, zvyšují přesnost metriky APM v zatížení.
    
     .NET core nabízí **doporučuje kolekce** nebo **zakázané** pro .NET Core 2.0 a 2.1.

3. **Instrumentace vaší služby app service** po instalaci Application Insights.

   **Povolit monitorování na straně klienta** pro stránku zobrazení a telemetrii uživatelů.

    (To je povoleno standardně pro aplikace .NET Core s **doporučuje kolekce**, bez ohledu na to, zda je k dispozici nastavení "APPINSIGHTS_JAVASCRIPT_ENABLED" aplikace. Podpora detailní uživatelského rozhraní pro zakázání monitorování na straně klienta není aktuálně k dispozici pro .NET Core.)
    
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

   1. Vytvoří prostředek Application Insights v Azure, kde ukládají, analyzují a zobrazí telemetrie.
   2. Přidá do kódu balíček NuGet Application Insights (pokud tam ještě není) a nakonfiguruje ho tak, aby odesílal telemetrická data do příslušného prostředku Azure.
2. **Otestujte telemetrická data** spuštěním aplikace v počítači pro vývoj (F5).
3. **Publikujte aplikaci** v Azure obvyklým způsobem. 

*Jak lze přepnout na odesílání do jiného prostředku Application Insights?*

* V sadě Visual Studio, klikněte pravým tlačítkem na projekt, zvolte **konfigurovat Application Insights**a zvolte požadovaný prostředek. Budete mít možnost vytvořit nový prostředek. Proveďte opětné sestavení a nasazení.

## <a name="automate-monitoring"></a>Automatizace monitorování

Chcete-li povolit shromažďování telemetrie s Application Insights nutné nastavit pouze nastavení aplikace:

   ![Nastavení aplikace app Service s dostupnými nastaveními Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definice nastavení aplikace

|Název nastavení aplikace |  Definice | Hodnota |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hlavní rozšíření, které řídí monitorování modulu runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Ve výchozím režimu pouze základní funkce jsou povolené s cílem zajistit optimální výkon. | `default` nebo `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Řídí, jestli modul přepisování binární `InstrumentationEngine` zapne. Toto nastavení má vliv na výkon a má vliv na čas úplné spuštění nebo spuštění. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Určuje, zda text tabulky SQL a Azure budou zachyceny spolu s volání závislostí. Upozornění ohledně výkonu: to vyžaduje, aby `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Aplikaci služby nastavení aplikace pomocí Azure Resource Manageru

Nastavení aplikace pro App Services můžete spravovat a nakonfigurovanou [šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tuto metodu lze použít při nasazování nové prostředky App Service pomocí automatizace Azure Resource Manageru, nebo pro úpravu nastavení stávající prostředky.

Základní struktura JSON nastavení aplikace pro app service je nižší než:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Příklad z Azure Resource Manageru šablony s nastavením aplikace nakonfigurovaná pro službu Application Insights to [šablony](https://github.com/Andrew-MSFT/BasicImageGallery) může být užitečné, konkrétně část od [řádek 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizace vytvoření prostředku Application Insights a odkaz na nově vytvořené služby App Service.

Pokud chcete vytvořit šablonu Azure Resource Manageru se všemi nastaveními výchozí Application Insights, která je nakonfigurovaná, zahájíte proces jako, pokud se chystáte vytvořit novou webovou aplikaci s Application Insights aktivovaných.

Vyberte **možnosti služby Automation**

   ![Nabídce vytvoření webové aplikace služby App Service](./media/azure-web-apps/create-web-app.png)

Tím se vygeneruje nejnovější šablony Azure Resource Manageru s všechna požadovaná nastavení, které jsou nakonfigurované.

  ![Šablony webové aplikace služby App Service](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> Šablona se generování nastavení aplikace v režimu "Výchozí". Tento režim je optimalizované pro výkon, i když můžete upravit šablonu, kterou chcete aktivovat podle toho, která funkce dáváte přednost.

## <a name="more-telemetry"></a>Další telemetrická data

* [Data načítání webové stránky](../../azure-monitor/app/javascript.md)
* [Vlastní telemetrická data](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Stále potřebujeme, abychom na přejděte na rozšíření – přidání – rozšíření Application Insights pro nové aplikace služby App Service?

Ne, už nepotřebujete přidat rozšíření ručně. Povolením Application Insights prostřednictvím okna nastavení přidá všechny potřebné nastavení aplikace lze povolit monitorování. To je teď je to možné, protože jsou soubory dříve přidaného rozšířením [předinstalovaným](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako součást image služby App Service. Soubory jsou umístěny v `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Pokud modul runtime a monitorování v čase sestavení jsou povoleny I skončit s duplicitních dat?

Ne, ve výchozím nastavení v případě monitorování v čase sestavení zjištění runtime monitorování prostřednictvím rozšíření se zastaví odesílání dat a pouze čas sestavení konfigurace monitorování se neuplatňují. Určení, zda chcete zakázat monitorování modulu runtime je založena na některý z těchto tří souborů zjišťování:

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

Je důležité si pamatovat, že v mnoha verzí sady Visual Studio, některé nebo všechny tyto soubory jsou přidány ve výchozím nastavení pro soubory šablon ASP.NET a ASP.NET Core, Visual Studio. Pokud váš projekt byl vytvořen na základě z jedné z šablon, i v případě, že jste ještě nepovolili explicitně Application Insights, přítomnost závislostí souboru by jinak znemožňovaly aktivaci monitorování modulu runtime.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED způsobí, že neúplné odpovědi HTML ve webových aplikací .NET CORE.

Povolení jazyka Javascript pomocí App Services může způsobit, že odpovědi html se oříznou.

* Alternativní řešení 1: nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED na hodnotu false nebo ho úplně odeberte a restartování
* Alternativní řešení 2: přidání sady sdk prostřednictvím kódu a odebrání rozšíření (Profiler a snímek ladicí program nebude fungovat s touto konfigurací.)

Pokud chcete sledovat tento problém, přejít na [rozšíření Azure způsobit neúplné odpovědi HTML](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

Pro .NET Core jsou aktuálně **nepodporuje**:

* Samostatná nasazení.
* Aplikace určené pro rozhraní .NET Framework.
* Aplikace .NET core 2.2.

> [!NOTE]
> .NET core 2.0 a .NET Core 2.1 jsou podporovány. Tento článek bude aktualizován, když se přidá podpora .NET Core 2.2.

## <a name="next-steps"></a>Další postup
* [Spusťte profiler v živé aplikaci](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../../azure-monitor/platform/data-collection.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../../azure-monitor/platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](../../azure-monitor/app/javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.