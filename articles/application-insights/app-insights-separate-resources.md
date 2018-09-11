---
title: Oddělení telemetrická data z vývoje, testování a vydání ve službě Azure Application Insights | Dokumentace Microsoftu
description: Přímé telemetrii pro různé prostředky pro vývoj, testování a produkční razítka.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 88626c3a4bfd4a1ff3a2e9cbc8c3f2b1c5553295
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303621"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddělení telemetrická data z vývoj, testování a produkce

Při vývoji další verze webové aplikace, které nechcete kombinovat [Application Insights](app-insights-overview.md) telemetrie z nové verze a již vydanou verzi. Aby nedocházelo k záměně, odešlete telemetrická data z různých vývojářských fáze k oddělení prostředků Application Insights pomocí samostatných instrumentačních klíčů (Instrumentační klíče). Aby bylo snazší změnit klíč instrumentace, protože verze přesune z jedné fáze do druhé, může být užitečné nastavit Instrumentační klíč v kódu namísto v konfiguračním souboru. 

(Pokud je systém Azure Cloud Service, je [jinou metodu nastavení samostatné Instrumentační klíče](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>O prostředcích a instrumentačních klíčů

Při nastavování monitorování pomocí Application Insights pro vaši webovou aplikaci, vytvoříte Application Insights *prostředků* v Microsoft Azure. Tento prostředek otevřít na portálu Azure, aby bylo možné zobrazit a analyzovat telemetrii získanou z vašich aplikací. Prostředek je identifikován *Instrumentační klíč* (Instrumentační klíč). Při instalaci balíčku Application Insights pro sledování aplikace, nakonfigurujete ji s instrumentačním klíčem, tak, aby věděl, kam má odesílat telemetrická data.

Obvykle budete chtít použít samostatné prostředky nebo jeden sdílený prostředek v různých scénářích:

* Jiný, nezávislé aplikací – pomocí samostatného prostředku a Instrumentační klíč pro každou aplikaci.
* Použití více komponent nebo role jednu obchodní aplikace – [jeden sdílený prostředek](app-insights-monitor-multi-role-apps.md) pro všechny součásti aplikace. Telemetrii můžete filtrovat nebo segmentované podle vlastnosti cloud_RoleName.
* Vývoj, testování a vydání – použijte samostatný prostředek a Instrumentační klíč pro verze systému v "razítko" nebo fáze produkce.
* A | B testování – použít jeden prostředek. Vytvoření TelemetryInitializer k přidání vlastnosti do telemetrických dat, který identifikuje varianty.


## <a name="dynamic-ikey"></a> Dynamické Instrumentační klíč

Aby bylo snazší změnit Instrumentační klíč, protože kód přesouvá mezi jednotlivými fázemi výroby, nastavte v kódu místo v konfiguračním souboru.

Nastavte klíč v metodě inicializace, jako je například souboru global.aspx.cs v rámci služby technologie ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

V tomto příkladu Instrumentační klíče pro různé prostředky jsou umístěné v různých verzích souboru webové konfigurace. Prohození – což lze provést jako součást skriptu release - konfiguračním souboru webu budou provádět záměnu cílový prostředek.

### <a name="web-pages"></a>Webové stránky
IKey se také používá ve vaší aplikace webové stránky v [skript, který jste získali z okna rychlý start](app-insights-javascript.md). Místo psaní kódu to doslova do skriptu, je vygenerujte ze stavu serveru. Například v aplikaci ASP.NET:

*JavaScript v Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Vytvořit další prostředky Application Insights
K oddělení telemetrie pro součásti různé aplikace, nebo u různých razítek (dev/testovací/produkční) pod stejnou komponentou, pak budete muset vytvořit nový prostředek Application Insights.

V [portal.azure.com](https://portal.azure.com), přidejte prostředek Application Insights:

![Klikněte na tlačítko Nový, Application Insights](./media/app-insights-separate-resources/01-new.png)

* **Typ aplikace** ovlivňuje, co se zobrazí na kartě s přehledem a k dispozici ve vlastnosti [Průzkumník metrik](app-insights-metrics-explorer.md). Pokud se váš typ aplikace, zvolte jeden z typů webových pro webové stránky.
* **Skupina prostředků** je usnadnění pro vlastnosti, jako je Správa [řízení přístupu](app-insights-resources-roles-access-control.md). Můžete použít samostatné skupiny prostředků pro vývoj, testování a produkce.
* **Předplatné** je váš účet platby v Azure.
* **Umístění** je, kde jsme zachovejte si svá data. Aktuálně nejde změnit. 
* **Přidat na řídicí panel** umístí rychlý přístup k dlaždici pro váš prostředek Azure domovskou stránku. 

Vytvoření prostředku trvá několik sekund. Po dokončení se zobrazí upozornění.

(Můžete napsat [skript prostředí PowerShell](app-insights-powershell-script-create-resource.md) automaticky vytvořit prostředek.)

### <a name="getting-the-instrumentation-key"></a>Získávání Instrumentační klíč
Instrumentační klíč identifikuje prostředek, který jste vytvořili. 

![Klikněte na tlačítko Essentials, klikněte na klíč instrumentace, CTRL + C](./media/app-insights-separate-resources/02-props.png)

Budete potřebovat Instrumentační klíče všech prostředků, do kterého vaše aplikace bude posílat data.

## <a name="filter-on-build-number"></a>Filtrovat podle čísla sestavení
Při publikování nové verze aplikace, budete chtít mít oddělení telemetrická data z různých sestavení.

Vlastnost verze aplikace můžete nastavit tak, aby můžete filtrovat [hledání](app-insights-diagnostic-search.md) a [Průzkumník metrik](app-insights-metrics-explorer.md) výsledky.

![Filtrování u vlastnosti](./media/app-insights-separate-resources/050-filter.png)

Existuje několik různých metod nastavení vlastností verze aplikace.

* Nastavte přímo:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zabalení tento řádek v [inicializátor telemetrie](app-insights-api-custom-events-metrics.md#defaults) zajistit, že všechny instance TelemetryClient nastavené konzistentně.
* [ASP.NET] Nastavit verzi `BuildInfo.config`. Verze z uzlu BuildLabel převezmou modulu web. Zahrnout tento soubor projektu a nezapomeňte nastavit vlastnost vždy kopírovat v Průzkumníku řešení.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Automaticky generovat BuildInfo.config v nástroji MSBuild. K tomu přidat pár řádků do vaší `.csproj` souboru:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Tím se vytvoří soubor s názvem *yourProjectName*. BuildInfo.config. Proces publikování se přejmenuje na BuildInfo.config.

    Popisek sestavení obsahuje zástupný symbol (AutoGen_...), když vytvoříte pomocí sady Visual Studio. Ale při sestavení pomocí nástroje MSBuild, se vyplní na správné číslo verze.

    Povolit MSBuild ke generování čísla verzí, nastavit verzi třeba `1.0.*` v AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Sledování verzí a vydání
Pokud chcete sledovat verzi aplikace, ujistěte se, že proces Microsoft Build Engine vygeneroval soubor `buildinfo.config`. Do souboru .csproj přidejte:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Pokud obsahuje informace o sestavení, webový modul Application Insights automaticky přidá položku **Verze aplikace** jako vlastnost pro každý předmět telemetrie. Díky tomu můžete při provádění [diagnostických hledání](app-insights-diagnostic-search.md) nebo při [zkoumání metrik](app-insights-metrics-explorer.md) filtrovat podle verze.

Všimněte si však, že číslo verze sestavení je generováno pouze pomocí procesu Microsoft Build Engine, ne sestavením vývojáře v sadě Visual Studio.

### <a name="release-annotations"></a>Poznámky k verzi
Pokud používáte Azure DevOps, můžete si [získat značku poznámek](app-insights-annotations.md) přidat do svých grafů pokaždé, když vydáte novou verzi. Následující obrázek ukazuje, jak se tato značka zobrazuje.

![Snímek obrazovky grafu s ukázkovou poznámkou k verzi](./media/app-insights-asp-net/release-annotation.png)
## <a name="next-steps"></a>Další postup

* [Sdílené prostředky pro víc rolí.](app-insights-monitor-multi-role-apps.md)
* [Vytvoření inicializátor Telemetrie k rozlišení A | Varianty B](app-insights-api-filtering-sampling.md#add-properties)
