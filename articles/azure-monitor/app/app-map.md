---
title: Mapa aplikace v Azure Application Insights | Microsoft Docs
description: Monitorování složitých topologií aplikace s mapou aplikace
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 49efad50b988da263a715c1aba9d53ad4b4a7121
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678388"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikace: třídění distribuovaných aplikací

Mapa aplikace vám pomůže odhalit slabá místa výkonu nebo neúspěšné oblasti selhání napříč všemi komponentami distribuované aplikace. Každý uzel na mapě představuje komponentu aplikace nebo její závislosti; a má stav klíčových ukazatelů výkonu a výstrah. Můžete kliknout na libovolnou komponentu a podrobnou diagnostiku, například Application Insights události. Pokud vaše aplikace používá služby Azure, můžete také kliknout na Azure Diagnostics, jako je SQL Database Advisor doporučení.

## <a name="what-is-a-component"></a>Co je komponenta?

Komponenty jsou nezávisle nasaditelné části distribuované aplikace nebo mikroslužeb. Vývojáři a provozní týmy mají viditelnost na úrovni kódu nebo přístup k telemetrie generované těmito komponentami aplikací. 

* Komponenty se liší od "pozorovaných" vnějších závislostí, jako je například SQL, EventHub atd. který tým nebo organizace nemusí mít přístup k (kód nebo telemetrie).
* Komponenty běží na jakémkoli počtu instancí serveru/role/kontejneru.
* Komponenty mohou být odděleny Application Insights klíče instrumentace (i když se odběry liší) nebo různé role, které se hlásí do jediného klíče instrumentace Application Insights. Funkce mapa ve verzi Preview zobrazuje komponenty bez ohledu na to, jak se nastavují.

## <a name="composite-application-map"></a>Mapa kompozitní aplikace

Celou topologii aplikace můžete zobrazit napříč několika úrovněmi souvisejících komponent aplikace. Komponenty mohou být různé Application Insights prostředky nebo různé role v jednom prostředku. Mapa aplikace vyhledá součásti pomocí volání závislosti protokolu HTTP provedených mezi servery s nainstalovanou sadou Application Insights SDK. 

Toto prostředí začíná progresivním zjišťováním komponent. Při prvním načtení mapy aplikace se aktivuje sada dotazů, aby bylo možné zjistit komponenty související s touto součástí. Tlačítko v levém horním rohu se aktualizuje o počet komponent ve vaší aplikaci, jak jsou zjištěny. 

Po kliknutí na možnost aktualizovat součásti mapy se mapa aktualizuje o všechny součásti zjištěné do tohoto bodu. V závislosti na složitosti vaší aplikace může načtení trvat několik minut.

Pokud jsou všechny součásti role v rámci jednoho Application Insights prostředku, tento krok zjišťování se nevyžaduje. Počáteční zatížení této aplikace bude mít všechny své součásti.

![Snímek obrazovky s mapou aplikace](media/app-map/app-map-001.png)

Jedním z klíčových cílů tohoto prostředí je, aby bylo možné vizualizovat komplexní topologie se stovkami komponent.

Kliknutím na libovolnou komponentu zobrazíte související přehledy a přejdete na prostředí pro posouzení výkonu a selhání této součásti.

![Ukotven](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Prozkoumat selhání

Vyberte možnost **prozkoumat selhání** a spusťte tak podokno selhání.

![Snímek obrazovky s tlačítkem prozkoumat selhání](media/app-map/investigate-failures.png)

![Snímek obrazovky s problémy s chybami](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prozkoumat výkon

Pokud chcete řešit problémy s výkonem, vyberte **prozkoumat výkon**.

![Snímek obrazovky s tlačítkem prozkoumat výkon](media/app-map/investigate-performance.png)

![Snímek obrazovky s výkonem](media/app-map/performance.png)

### <a name="go-to-details"></a>Přejít na podrobnosti

Vyberte **Přejít k podrobnostem** a prozkoumejte koncové prostředí transakce, které může nabídnout zobrazení do úrovně zásobníku volání.

![Snímek obrazovky s tlačítkem přejít k podrobnostem](media/app-map/go-to-details.png)

![Snímek obrazovky s podrobnostmi o transakci od začátku do konce](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Zobrazit v analýzách

Pokud chcete svá data aplikací dotazovat a prozkoumat dál, klikněte na **Zobrazit v analýze**.

![Snímek obrazovky s tlačítkem zobrazit v analýze](media/app-map/view-in-analytics.png)

![Snímek obrazovky s možností analýzy](media/app-map/analytics.png)

### <a name="alerts"></a>Výstrahy

Chcete-li zobrazit aktivní výstrahy a podkladová pravidla, která způsobují aktivované výstrahy, vyberte možnost **výstrahy**.

![Snímek obrazovky s tlačítkem výstrahy](media/app-map/alerts.png)

![Snímek obrazovky s možností analýzy](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Nastavit název cloudové role

Mapa aplikace používá vlastnost **název cloudové role** k identifikaci komponent na mapě. Sada Application Insights SDK automaticky přidá vlastnost název cloudové role do telemetrie emitované součástmi. Sada SDK například přidá název webu nebo název role služby do vlastnosti název cloudové role. Existují však případy, kdy je vhodné přepsat výchozí hodnotu. Přepsání názvu cloudové role a změna toho, co se zobrazí na mapě aplikace:

### <a name="netnet-core"></a>.NET/.NET Core

**Pište vlastní TelemetryInitializer, jak je uvedeno níže.**

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

**Aplikace ASP.NET: inicializátor zatížení pro aktivní TelemetryConfiguration**

V souboru ApplicationInsights. config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternativním způsobem pro webové aplikace ASP.NET je vytvoření instance inicializátoru v kódu, například v Global.aspx.cs:

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
> Přidání inicializátoru pomocí `ApplicationInsights.config` nebo použití `TelemetryConfiguration.Active` není pro ASP.NET Core aplikace platné. 

**ASP.NET Core aplikace: inicializátor zátěže na TelemetryConfiguration**

U [ASP.NET Corech](asp-net-core.md#adding-telemetryinitializers) aplikací je přidání nového `TelemetryInitializer` provedeno přidáním do kontejneru vkládání závislostí, jak je znázorněno níže. To se provádí v `ConfigureServices` metoda vaší `Startup.cs` třídy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternativní metoda pro Node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Pokud používáte jarní spouštění s Application Insightsm Starter Boot Starter, jediná požadovaná změna je nastavení vlastního názvu aplikace v souboru Application. Properties.

`spring.application.name=<name-of-app>`

Jaře Boot Starter automaticky přiřadí název cloudové role k hodnotě, kterou zadáte do vlastnosti spring.application.name.

Další informace o korelaci Java a o tom, jak nakonfigurovat název cloudové role pro aplikace, které nejsou SpringBoot, jsou v této [části](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) u korelace.

### <a name="clientbrowser-side-javascript"></a>JavaScript na straně klienta nebo prohlížeče

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Principy názvu cloudové role v rámci mapy aplikace

Vzhledem k tomu, jak si představit **název cloudové role**, může být užitečné si prohlédnout mapu aplikace, která má několik názvů cloudových rolí, které jsou k dispozici:

![Snímek obrazovky s mapou aplikace](media/app-map/cloud-rolename.png)

V mapě aplikace nad každým názvem v zelených polích jsou hodnoty názvu cloudové role pro různé aspekty této konkrétní distribuované aplikace. Takže pro tuto aplikaci se tyto role skládají z: `Authentication`, `acmefrontend`, `Inventory Management` a `Payment Processing Worker Role`. 

V případě této aplikace všechny názvy cloudových rolí také představují jiný jedinečný Application Insights prostředek s vlastními klíči instrumentace. Vzhledem k tomu, že vlastník této aplikace má přístup ke každému z těchto čtyř různorodých Application Insightsch prostředků, může mapa aplikace spojovat mapu základních vztahů.

[Oficiální definice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativně může být **instance cloudové** role užitečná ve scénářích, kde **název cloudové role** oznamuje, že k problému dochází ve webovém front-endu, ale může se jednat o webový front-end na více serverech s vyrovnáváním zatížení, aby bylo možné Podrobné zobrazení ve vrstvě je hlubší prostřednictvím dotazů Kusto a s vědomím, jestli problém má vliv na všechny webové front-end servery/instance nebo jenom na jeden z nich může být mimořádně důležitý.

Situaci, kdy byste mohli chtít přepsat hodnotu pro instanci cloudové role, může být, pokud vaše aplikace běží v prostředí s kontejnerem, kde stačí jenom vědět, že jednotlivý server nemusí být k dispozici dostatek informací, aby mohl najít daný problém.

Další informace o tom, jak přepsat vlastnost název cloudové role pomocí inicializátorů telemetrie, najdete v tématu věnovaném [Přidání vlastností: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže s tím, jak mapa aplikace funguje podle očekávání, zkuste postupovat takto:

### <a name="general"></a>Obecné

1. Ujistěte se, že používáte oficiálně podporovanou sadu SDK. Nepodporované nebo komunitní sady SDK nemusí podporovat korelaci.

    Seznam podporovaných sad SDK najdete v tomto [článku](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) .

2. Upgradujte všechny součásti na nejnovější verzi sady SDK.

3. Pokud používáte Azure Functions s C#, upgradujte na [Functions v2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Ověřte, že je [název cloudové role](#set-cloud-role-name) správně nakonfigurovaný.

5. Pokud vám chybí závislost, ujistěte se, že je v seznamu [automaticky shromažďovaných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud ne, můžete ji stále najít ručně pomocí [volání TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Příliš mnoho uzlů na mapě

Mapa aplikace vytvoří uzel aplikace pro každý jedinečný název cloudové role přítomný v telemetrie požadavků a uzel závislostí pro každou jedinečnou kombinaci typu, cíle a názvu cloudové role v telemetrii závislosti. Pokud je v telemetrii více než 10 000 uzlů, mapa aplikace nebude moci načíst všechny uzly a odkazy, takže vaše mapa nebude kompletní. Pokud k tomu dojde, zobrazí se při zobrazení mapy zpráva s upozorněním.

Kromě toho mapa aplikace podporuje až 1000 samostatné neseskupené uzly vykreslené najednou. Mapa aplikace zjednodušuje vizuální složitost seskupením závislostí, které mají stejný typ a volající, ale pokud má vaše telemetrie příliš mnoho jedinečných názvů cloudových rolí nebo příliš mnoho typů závislostí, toto seskupení nebude dostatečné a mapa nebude moci činit.

Pokud to chcete opravit, budete muset změnit instrumentaci a správně nastavit název cloudové role, typ závislosti a cílová pole závislosti.

* Cíl závislosti by měl představovat logický název závislosti. V mnoha případech se jedná o ekvivalent názvu serveru nebo prostředku závislosti. Například v případě závislostí HTTP je nastaveno na název hostitele. Neměl by obsahovat jedinečné identifikátory nebo parametry, které se mění z jedné žádosti na jinou.

* Typ závislosti by měl představovat logický typ závislosti. Například protokol HTTP, SQL nebo Azure Blob jsou typické typy závislostí. Neměl by obsahovat jedinečné identifikátory.

* Název cloudové role je popsaný v [části výše](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Váš názor na portál

Pokud chcete poskytnout zpětnou vazbu, použijte možnost zpětné vazby.

![Obrázek MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak korelace funguje v Application Insights, najdete v [článku korelace telemetrie](https://docs.microsoft.com/azure/application-insights/application-insights-correlation).
* [Koncové prostředí pro diagnostiku transakcí](transaction-diagnostics.md) koreluje telemetrie na straně serveru ze všech vašich Application Insights monitorovaných komponent do jednoho zobrazení.
* Pro pokročilé scénáře korelace v ASP.NET Core a ASP.NET si projděte článek [sledování vlastních operací](custom-operations-tracking.md) .
