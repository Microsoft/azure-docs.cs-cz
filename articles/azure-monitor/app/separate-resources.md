---
title: Oddělení telemetrie v Přehledech aplikací Azure
description: Přímé telemetrie do různých prostředků pro vývoj, testování a výrobní razítka.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671456"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddělení telemetrie od vývoje, testování a výroby

Při vývoji další verze webové aplikace, nechcete míchat telemetrie [Application Insights](../../azure-monitor/app/app-insights-overview.md) z nové verze a již vydané verze. Chcete-li se vyhnout nejasnostem, odešlete telemetrická data z různých fází vývoje do samostatných prostředků Application Insights se samostatnými klíči instrumentace (ikeys). Chcete-li usnadnit změnu klíče instrumentace jako verze přesune z jedné fáze do druhé, může být užitečné nastavit ikey v kódu namísto v konfiguračním souboru. 

(Pokud je váš systém Cloudovou službou Azure, existuje [další metoda nastavení samostatných ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>O zdrojích a klíčích instrumentace

Když nastavíte monitorování Application Insights pro webovou aplikaci, vytvoříte *prostředek* Application Insights v Microsoft Azure. Tento prostředek otevřete na webu Azure Portal, abyste viděli a analyzovali telemetrii shromážděnou z vaší aplikace. Prostředek je identifikován *klíč instrumentace* (ikey). Když nainstalujete balíček Application Insights pro monitorování vaší aplikace, nakonfigurujete ho pomocí instrumentačního klíče, aby věděl, kam má být telemetrická data odeslána.

Obvykle se rozhodnete použít samostatné prostředky nebo jeden sdílený prostředek v různých scénářích:

* Různé, nezávislé aplikace – pro každou aplikaci použijte samostatný prostředek a iklíč.
* Více součástí nebo rolí jedné obchodní aplikace – použijte [jeden sdílený prostředek](../../azure-monitor/app/app-map.md) pro všechny aplikace komponent. Telemetrie může být filtrována nebo segmentována vlastností cloud_RoleName.
* Vývoj, testování a vydání – použijte samostatný prostředek a iklíč pro verze systému v "razítku" nebo fázi výroby.
* A | B testování - Použití jednoho prostředku. Vytvořte TelemetryInitializer přidat vlastnost telemetrie, která identifikuje varianty.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamický klíč instrumentace

Chcete-li usnadnit změnu ikey jako kód přesune mezi fázemi výroby, nastavte jej v kódu namísto v konfiguračním souboru.

Nastavte klíč v metodě inicializace, například global.aspx.cs ve službě ASP.NET:

*C #*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

V tomto příkladu jsou ikeys pro různé prostředky umístěny v různých verzích webového konfiguračního souboru. Výměna webového konfiguračního souboru , což můžete udělat jako součást skriptu vydání, zamění cílový prostředek.

### <a name="web-pages"></a>Webové stránky
IKey se také používá na webových stránkách aplikace, ve [skriptu, který jste získali z okna rychlého startu](../../azure-monitor/app/javascript.md). Namísto kódování doslova do skriptu, vygenerujte jej ze stavu serveru. Například v ASP.NET aplikaci:

*JavaScript v břitvě*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Vytvoření dalších prostředků application insights
Chcete-li oddělit telemetrii pro různé součásti aplikace nebo pro různá razítka (vývoj/test/výroba) stejné součásti, budete muset vytvořit nový prostředek Application Insights.

V [portal.azure.com](https://portal.azure.com)přidejte prostředek Application Insights:

![Klikněte na tlačítko Nový, Application Insights](./media/separate-resources/01-new.png)

* **Typ aplikace** ovlivňuje to, co vidíte na okně přehledu a vlastnosti dostupné v [průzkumníku metrik](../../azure-monitor/app/metrics-explorer.md). Pokud typ aplikace nevidíte, vyberte jeden z webových typů webových stránek.
* **Skupina prostředků** je pohodlí pro správu vlastností, jako je [řízení přístupu](../../azure-monitor/app/resources-roles-access-control.md). Pro vývoj, testování a výrobu můžete použít samostatné skupiny prostředků.
* **Předplatné** je váš platební účet v Azure.
* **Místo,** kde uchováváme vaše údaje. V současné době jej nelze změnit. 
* **Přidat na řídicí panel** umístí rychlý přístup dlaždice pro váš prostředek na domovské stránce Azure. 

Vytvoření prostředku trvá několik sekund. Až bude hotové, zobrazí se výstraha.

(Můžete napsat [skript prostředí PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) a vytvořit prostředek automaticky.)

### <a name="getting-the-instrumentation-key"></a>Získání klíče instrumentace
Klíč instrumentace identifikuje prostředek, který jste vytvořili. 

![Klikněte na Základní, klikněte na klávesu Instrumentace, CTRL+C](./media/separate-resources/02-props.png)

Potřebujete instrumentační klíče všech prostředků, do kterých bude aplikace odesílat data.

## <a name="filter-on-build-number"></a>Filtrování čísla sestavení
Když publikujete novou verzi aplikace, budete chtít oddělit telemetrii od různých sestavení.

Můžete nastavit vlastnost Verze aplikace, abyste mohli filtrovat výsledky [hledání](../../azure-monitor/app/diagnostic-search.md) a [průzkumníka metrik.](../../azure-monitor/app/metrics-explorer.md)

![Filtrování vlastnosti](./media/separate-resources/050-filter.png)

Existuje několik různých metod nastavení vlastnosti Application Version.

* Nastavit přímo:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zalomit tento řádek v [inicializátoru telemetrie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) k zajištění, že všechny instance TelemetryClient jsou nastaveny konzistentně.
* [ASP.NET] Nastavte verzi `BuildInfo.config`v . Webový modul převezme verzi z uzlu BuildLabel. Zahrňte tento soubor do projektu a nezapomeňte nastavit vlastnost Kopírovat vždy v Průzkumníku řešení.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] V msbuildu automaticky vygenerujte soubor BuildInfo.config. Chcete-li to provést, přidejte do `.csproj` souboru několik řádků:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Tím se vygeneruje soubor s názvem *yourProjectName*. BuildInfo.config. Proces publikování přejmenuje na BuildInfo.config.

    Popisek sestavení obsahuje zástupný symbol (AutoGen_...) při vytváření pomocí sady Visual Studio. Ale při sestavení s MSBuild, je naplněn správné číslo verze.

    Chcete-li povolit, aby msbuild `1.0.*` generoval čísla verzí, nastavte verzi jako v AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Sledování verzí a vydání
Pokud chcete sledovat verzi aplikace, ujistěte se, že proces Microsoft Build Engine vygeneroval soubor `buildinfo.config`. Do `.csproj` souboru přidejte:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Pokud obsahuje informace o sestavení, webový modul Application Insights automaticky přidá položku **Verze aplikace** jako vlastnost pro každý předmět telemetrie. Díky tomu můžete při provádění [diagnostických hledání](../../azure-monitor/app/diagnostic-search.md) nebo při [zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) filtrovat podle verze.

Všimněte si však, že číslo verze sestavení je generováno pouze microsoft build engine, nikoli vývojářsestavení z visual studia.

### <a name="release-annotations"></a>Poznámky k verzi
Pokud používáte Azure DevOps, můžete [získat značku poznámky](../../azure-monitor/app/annotations.md) přidané do grafů při každém vydání nové verze. Následující obrázek ukazuje, jak se tato značka zobrazuje.

![Snímek obrazovky grafu s ukázkovou poznámkou k verzi](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Další kroky

* [Sdílené prostředky pro více rolí](../../azure-monitor/app/app-map.md)
* [Vytvoření telemetrickéinizátoru pro rozlišení A| Varianty B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
