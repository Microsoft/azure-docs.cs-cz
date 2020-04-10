---
title: Mapa aplikací v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Sledování složitých topologií aplikací pomocí mapy aplikace
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 7c5c9173704535b1e34ffde5867bd512e3e02ed8
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989523"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikací: Třídění distribuovaných aplikací

Mapa aplikací vám pomůže odhalit problémová místa výkonu nebo hotspoty selhání ve všech součástech distribuované aplikace. Každý uzel na mapě představuje součást aplikace nebo její závislosti; a má stav klíčového ukazatele výkonu a stavu výstrah. Můžete kliknout z libovolné součásti na podrobnější diagnostiku, jako jsou události Application Insights. Pokud vaše aplikace používá služby Azure, můžete taky kliknout na diagnostiku Azure, jako jsou doporučení SQL Database Advisor.

## <a name="what-is-a-component"></a>Co je komponenta?

Součásti jsou nezávisle nasaditelné části aplikace distribuovaných nebo mikroslužeb. Vývojáři a provozní týmy mají viditelnost na úrovni kódu nebo přístup k telemetrii generované těmito součástmi aplikace. 

* Součásti se liší od "pozorovaných" externích závislostí, jako je SQL, EventHub atd., ke kterým váš tým/organizace nemusí mít přístup (kód nebo telemetrie).
* Součásti jsou spuštěny v libovolném počtu instancí serveru/role/kontejneru.
* Součásti mohou být samostatné klíče instrumentace Application Insights (i když se předplatná liší) nebo různé role, které se hlásí do jednoho klíče instrumentace Application Insights. Náhled zobrazení mapy zobrazuje součásti bez ohledu na to, jak jsou nastaveny.

## <a name="composite-application-map"></a>Složená mapa aplikace

Můžete zobrazit plnou topologii aplikace na více úrovních souvisejících součástí aplikace. Součásti mohou být různé prostředky Application Insights nebo různé role v jednom prostředku. Mapa aplikace vyhledá součásti podle následujících volání závislostí HTTP mezi servery s nainstalovanou sadou Application Insights SDK. 

Tato zkušenost začíná postupné zjišťování součástí. Při prvním načtení mapy aplikace se aktivuje sada dotazů, které zjišťují součásti související s touto komponentou. Tlačítko v levém horním rohu se aktualizuje s počtem součástí v aplikaci, jak jsou zjištěny. 

Po kliknutí na tlačítko "Aktualizovat součásti mapy" je mapa aktualizována se všemi zjištěnými součástmi do tohoto okamžiku. V závislosti na složitosti aplikace to může trvat minutu načíst.

Pokud jsou všechny součásti role v rámci jednoho prostředku Application Insights, pak tento krok zjišťování není vyžadován. Počáteční zatížení pro takovou aplikaci bude mít všechny jeho součásti.

![Snímek obrazovky s mapou aplikace](media/app-map/app-map-001.png)

Jedním z klíčových cílů této zkušenosti je být schopen vizualizovat komplexní topologie se stovkami komponent.

Kliknutím na libovolnou komponentu zobrazíte související přehledy a přejdete na prostředí s tříděním výkonu a selhání pro tuto komponentu.

![Informační rámeček](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Prošetření selhání

Vyberte **prozkoumat selhání** spuštění podokna selhání.

![Snímek obrazovky s tlačítkem prozkoumání selhání](media/app-map/investigate-failures.png)

![Snímek obrazovky se selháním](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prozkoumat výkon

Chcete-li odstranit problémy s výkonem, vyberte **možnost prozkoumat výkon**.

![Snímek obrazovky s tlačítkem prozkoumat výkon](media/app-map/investigate-performance.png)

![Snímek obrazovky s výkonem](media/app-map/performance.png)

### <a name="go-to-details"></a>Přejít na podrobnosti

Vyberte **přejít na podrobnosti** a prozkoumejte prostředí transakcí od konce, které může nabízet zobrazení až na úroveň zásobníku volání.

![Snímek obrazovky s tlačítkem Přejít na podrobnosti](media/app-map/go-to-details.png)

![Snímek obrazovky s podrobnostmi o transakcích od konce](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Zobrazit protokoly (Analytics)

Chcete-li dále dotazovat a zkoumat data aplikací, klikněte na **zobrazení v protokolech (Analytics).**

![Snímek obrazovky s tlačítkem Zobrazení v analytice](media/app-map/view-logs.png)

![Snímek obrazovky s analytickým prostředím Spojinový graf shrnující průměrnou dobu trvání odpovědi požadavku za posledních 12 hodin.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Výstrahy

Chcete-li zobrazit aktivní výstrahy a základní pravidla, která způsobí aktivaci výstrah, vyberte **výstrahy**.

![Snímek obrazovky s tlačítkem upozornění](media/app-map/alerts.png)

![Snímek obrazovky s analytickým prostředím](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Nastavení názvu role cloudu

Mapa aplikací používá vlastnost **název role cloudu** k identifikaci součástí na mapě. Sada Application Insights SDK automaticky přidá vlastnost název role cloudu do telemetrie vyzařované součástmi. Sada SDK například přidá název webu nebo název role služby do vlastnosti názvu role cloudu. Existují však případy, kdy můžete chtít přepsat výchozí hodnotu. Chcete-li přepsat název role cloudu a změnit, co se zobrazí na mapě aplikace:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Napište vlastní TelemetryInitializer, jak je uvedeno níže.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET aplikace: Načtení inicializátoru do aktivní telemetrické konfigurace**

V applicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternativní metodou pro ASP.NET webových aplikací je vytvoření instance inicializátoru v kódu, například v Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Přidání inicializéru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není platné pro ASP.NET základní aplikace. 

**ASP.NET základní aplikace: Načtení inicializátoru do telemetrieKonfigurace**

Pro [ASP.NET základní](asp-net-core.md#adding-telemetryinitializers) aplikace, `TelemetryInitializer` přidání nového se provádí přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se `ConfigureServices` provádí metodou `Startup.cs` vaší třídy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java agent**

Pro [agenta Java 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) je název role cloudu nastaven takto:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Můžete také nastavit název role cloudu ```APPLICATIONINSIGHTS_ROLE_NAME```pomocí proměnné prostředí .

**Java SDK**

Pokud používáte sadu SDK, počínaje application insights Java SDK 2.5.0, `<RoleName>` můžete `ApplicationInsights.xml` zadat název role cloudu přidáním do souboru, například.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Pokud používáte jarní spuštění se startérem Jarní spouštěcí vlastnosti Application Insights, jedinou požadovanou změnou je nastavení vlastního názvu aplikace v souboru application.properties.

`spring.application.name=<name-of-app>`

Startér jarního spuštění automaticky přiřadí název role cloudu hodnotě, kterou zadáte pro vlastnost spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternativní metoda pro soubor Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Principy názvu role cloudu v kontextu mapy aplikací

Pokud jde o to, jak přemýšlet o **názvu role cloudu**, může být užitečné podívat se na mapu aplikací, která má více názvů rolí cloudu:

![Snímek obrazovky s mapou aplikace](media/app-map/cloud-rolename.png)

V mapě aplikace nad každý z názvů v zelených polích jsou hodnoty názvů rolí cloud pro různé aspekty této konkrétní distribuované aplikace. Takže pro tuto aplikaci `Authentication`jeho `acmefrontend` `Inventory Management`role `Payment Processing Worker Role`se skládají z: , , , a . 

V případě této aplikace každý z těchto názvů rolí cloudu také představuje jiný jedinečný prostředek Application Insights s vlastními klíči instrumentace. Vzhledem k tomu, že vlastník této aplikace má přístup ke každému z těchto čtyř různorodých prostředků Application Insights, mapa aplikací je schopna sešít mapu základních vztahů.

Pro [úřední definice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Případně **instance role cloudu** může být užitečná pro scénáře, kde **název role cloudu** říká, že problém je někde ve vašem webovém front-endu, ale můžete být spuštěn ý webový front-end na více serverech s vyrovnáváním zatížení, takže můžete přejít k podrobnostem ve vrstvě hlouběji prostřednictvím dotazů Kusto a vědět, zda problém ovlivňuje všechny webové front-endové servery / instance nebo jen jeden může být nesmírně důležitý.

Scénář, kde můžete chtít přepsat hodnotu pro instanci role cloudu může být, pokud vaše aplikace běží v kontejnerizovaném prostředí, kde jen znalost jednotlivých serverů nemusí být dostatek informací k vyhledání daného problému.

Další informace o tom, jak přepsat vlastnost názvu role cloudu pomocí inicializátorů telemetrie, naleznete [v tématu Add properties: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže s tím, aby Mapa aplikací fungovala podle očekávání, vyzkoušejte tyto kroky:

### <a name="general"></a>Obecné

1. Ujistěte se, že používáte oficiálně podporovanou sadu SDK. Nepodporované nebo komunitní sady SDK nemusí podporovat korelaci.

    Seznam podporovaných sad SDK naleznete v tomto [článku.](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)

2. Upgradujte všechny součásti na nejnovější verzi sady SDK.

3. Pokud používáte Funkce Azure s C#, upgradujte na [funkce V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Potvrďte, že [název role cloudu](#set-cloud-role-name) je správně nakonfigurován.

5. Pokud vám chybí závislost, ujistěte se, že je v seznamu [automaticky shromažďovaných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud ne, můžete ji stále najít ručně pomocí [volání TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Příliš mnoho uzlů na mapě

Mapa aplikací vytvoří uzel aplikace pro každý jedinečný název cloudové role, který se vyskytuje v telemetrii požadavku, a uzel závislostí pro každou jedinečnou kombinaci typu, cíle a názvu role cloudu v telemetrii závislostí. Pokud je více než 10 000 uzlů ve vaší telemetrii, mapa aplikace nebude moct načíst všechny uzly a odkazy, takže vaše mapa bude neúplná. Pokud k tomu dojde, zobrazí se při zobrazení mapy varovná zpráva.

Kromě toho mapa aplikací podporuje pouze až 1000 samostatných neseskupených uzlů vykreslených najednou. Mapování aplikací snižuje vizuální složitost seskupením závislostí, které mají stejný typ a volající, ale pokud vaše telemetrie obsahuje příliš mnoho jedinečných názvů cloudových rolí nebo příliš mnoho typů závislostí, bude toto seskupení nedostatečné a mapa nebude moci vykreslit.

Chcete-li tento problém vyřešit, budete muset změnit instrumentaci správně nastavit název role cloudu, typ závislosti a cílové pole závislostí.

* Cíl závislostí by měl představovat logický název závislosti. V mnoha případech je ekvivalentní název serveru nebo prostředku závislosti. Například v případě závislostí HTTP je nastavena na název hostitele. Nemělby obsahovat jedinečné ID nebo parametry, které se mění z jednoho požadavku na jiný.

* Typ závislosti by měl představovat logický typ závislosti. Například HTTP, SQL nebo Azure Blob jsou typické typy závislostí. Nemělby obsahovat jedinečná ID.

* Účel názvu role cloudu je popsán ve [výše uvedené části](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Zpětná vazba portálu

Chcete-li poskytnout zpětnou vazbu, použijte možnost zpětné vazby.

![MapLink-1 obrázek](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak funguje korelace v Application Insights, naleznete v [článku korelace telemetrie](correlation.md).
* [Komplexní prostředí diagnostiky transakcí](transaction-diagnostics.md) koreluje telemetrickou na straně serveru ze všech monitorovaných součástí application insights do jednoho zobrazení.
* Pokročilé scénáře korelace v ASP.NET jádra a ASP.NET naleznete v článku [sledování vlastních operací.](custom-operations-tracking.md)
