---
title: Oddělení telemetrie v Azure Application Insights
description: Přímá telemetrie na různé prostředky pro vývoj, testování a produkční razítka.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 565d51751ad50479f4e227b6855ac63b80bd949e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536773"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddělení telemetrie od vývoje, testování a produkce

Při vývoji další verze webové aplikace nechcete kombinovat [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetrii od nové verze a již vydané verze. Chcete-li předejít nejasnostem, zasílejte telemetrii z různých fází vývoje a oddělte Application Insights prostředky se samostatnými klíči instrumentace (instrumentační klíče). Aby bylo snazší změnit klíč instrumentace, protože verze se přesouvá z jedné fáze na jinou, může být užitečné nastavit ikey v kódu místo v konfiguračním souboru. 

(Pokud je váš systém cloudová služba Azure, existuje [Další metoda nastavení samostatné instrumentační klíče](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>O prostředcích a klíčích instrumentace

Když nastavíte Application Insights Monitoring pro webovou aplikaci, vytvoříte v Microsoft Azure *prostředek* Application Insights. Tento prostředek otevřete v Azure Portal, aby se zobrazila a analyzovala telemetrie shromážděná z vaší aplikace. Prostředek je identifikovaný *klíčem instrumentace* (ikey). Když nainstalujete balíček Application Insights pro monitorování vaší aplikace, nakonfigurujete ji pomocí klíče instrumentace, aby znala, kam se má telemetrie odeslat.

Obvykle se rozhodnete použít samostatné prostředky nebo jeden sdílený prostředek v různých scénářích:

* Různé nezávislé aplikace – pro každou aplikaci použijte samostatný prostředek a ikey.
* Více komponent nebo rolí jedné obchodní aplikace – použijte [jeden sdílený prostředek](../../azure-monitor/app/app-map.md) pro všechny aplikace komponent. Telemetrii lze filtrovat nebo rozdělit pomocí vlastnosti cloud_RoleName.
* Vývoj, testování a vydání – použití samostatného prostředku a ikey pro verze systému v "razítek" nebo ve fázi výroby.
* A | B testování – použijte jeden prostředek. Vytvořte TelemetryInitializer a přidejte do telemetrie vlastnost, která identifikuje varianty.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamický klíč instrumentace

Aby bylo snazší změnit ikey při pohybu kódu mezi fázemi výroby, nastavte ho v kódu místo v konfiguračním souboru.

Nastavte klíč v inicializační metodě, jako je například global.aspx.cs ve službě ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

V tomto příkladu jsou instrumentační klíče pro různé prostředky umístěny v různých verzích konfiguračního souboru webu. Výměna konfiguračního souboru webu, který můžete provést jako součást skriptu pro vydání, zahodí cílový prostředek.

### <a name="web-pages"></a>Webové stránky
IKey se také používá na webových stránkách vaší aplikace ve [skriptu, který jste dostali z okna rychlý Start](../../azure-monitor/app/javascript.md). Místo toho, aby se do skriptu nahlásilo, vygeneruje ho ze stavu serveru. Například v aplikaci ASP.NET:

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


## <a name="create-additional-application-insights-resources"></a>Vytvoření dalších prostředků Application Insights
Pro oddělení telemetrie pro různé součásti aplikace nebo pro různá razítka (vývoj/testování/produkce) stejné komponenty budete muset vytvořit nový prostředek Application Insights.

Do [Portal.Azure.com](https://portal.azure.com)přidejte prostředek Application Insights:

![Klikněte na tlačítko Nový, Application Insights](./media/separate-resources/01-new.png)

* **Typ aplikace** má vliv na to, co vidíte v okně Přehled, a na vlastnosti dostupné v [Průzkumníkovi metrik](../../azure-monitor/platform/metrics-charts.md). Pokud nevidíte typ vaší aplikace, vyberte jeden z webových typů pro webové stránky.
* **Skupina prostředků** je pohodlí pro správu vlastností, jako je [řízení přístupu](../../azure-monitor/app/resources-roles-access-control.md). Pro vývoj, testování a produkci můžete použít samostatné skupiny prostředků.
* **Předplatné** je váš platební účet v Azure.
* **Místo** , kde udržujeme vaše data. Aktuálně se nedá změnit. 
* **Přidat na řídicí panel** : na domovské stránce Azure se zobrazí dlaždice pro rychlý přístup k vašemu prostředku. 

Vytvoření prostředku trvá několik sekund. Po dokončení se zobrazí výstraha.

(Můžete napsat [skript prostředí PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) pro automatické vytvoření prostředku.)

### <a name="getting-the-instrumentation-key"></a>Získávání klíče instrumentace
Klíč instrumentace identifikuje prostředek, který jste vytvořili. 

![Klikněte na základy, klikněte na klíč instrumentace, CTRL + C.](./media/separate-resources/02-props.png)

Budete potřebovat klíče instrumentace všech prostředků, na které bude vaše aplikace odesílat data.

## <a name="filter-on-build-number"></a>Filtrovat podle čísla sestavení
Když publikujete novou verzi aplikace, budete chtít být schopni oddělit telemetrii od různých sestavení.

Vlastnost verze aplikace můžete nastavit tak, aby bylo možné filtrovat výsledky [hledání](../../azure-monitor/app/diagnostic-search.md) a [Průzkumníka metrik](../../azure-monitor/platform/metrics-charts.md) .

![Filtrování u vlastnosti](./media/separate-resources/050-filter.png)

Vlastnost verze aplikace se nastavuje několika různými způsoby.

* Nastavit přímo:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Zabalte tento řádek do [inicializátoru telemetrie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , aby se zajistilo, že všechny instance TelemetryClient jsou nastavené konzistentně.
* [ASP.NET] Nastavte verzi v `BuildInfo.config`. Webový modul vybere z uzlu BuildLabel verzi. Zahrňte tento soubor do projektu a nezapomeňte nastavit vlastnost kopírovat vždy v Průzkumník řešení.

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
* [ASP.NET] Vygenerujte BuildInfo. config automaticky v MSBuild. Uděláte to tak, že do `.csproj` souboru přidáte několik řádků:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Tím se vygeneruje soubor s názvem *názevvašehoprojektu*. BuildInfo. config. proces publikování se přejmenuje na BuildInfo. config.

    Popisek sestavení obsahuje zástupný symbol (AutoGen_...) při sestavování pomocí sady Visual Studio. Ale při sestavení pomocí nástroje MSBuild se naplní správným číslem verze.

    Pokud chcete, aby nástroj MSBuild vygeneroval čísla verzí, nastavte `1.0.*` verzi jako v AssemblyReference.cs.

## <a name="version-and-release-tracking"></a>Sledování verzí a vydání
Pokud chcete sledovat verzi aplikace, ujistěte se, že proces Microsoft Build Engine vygeneroval soubor `buildinfo.config`. Do `.csproj` souboru přidejte:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Pokud obsahuje informace o sestavení, webový modul Application Insights automaticky přidá položku **Verze aplikace** jako vlastnost pro každý předmět telemetrie. Díky tomu můžete při provádění [diagnostických hledání](../../azure-monitor/app/diagnostic-search.md) nebo při [zkoumání metrik](../../azure-monitor/platform/metrics-charts.md) filtrovat podle verze.

Všimněte si však, že číslo verze sestavení je generováno pouze pomocí Microsoft Build Engine, nikoli vývojářem Build ze sady Visual Studio.

### <a name="release-annotations"></a>Poznámky k verzi
Pokud používáte Azure DevOps, můžete při každém vydání nové verze [získat značku poznámky](../../azure-monitor/app/annotations.md) přidanou do vašich grafů. Následující obrázek ukazuje, jak se tato značka zobrazuje.

![Snímek obrazovky grafu s ukázkovou poznámkou k verzi](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Další kroky

* [Sdílené prostředky pro více rolí](../../azure-monitor/app/app-map.md)
* [Vytvoření inicializátoru telemetrie pro odlišení typu | B – varianty](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
