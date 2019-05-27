---
title: Mapa aplikace ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování topologie komplexních aplikací s Mapa aplikace
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 70d1f54aed5e83801b1d1e249d7a412dd6d9a49a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964039"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikace: Třídění distribuovaných aplikací

Mapa aplikace umožňuje přímé výkonnostní kritické body nebo selhání hotspotům pro všechny součásti distribuované aplikace. Každý uzel na mapě představuje určité součásti aplikace nebo její závislosti; a má stav klíčového ukazatele výkonu a výstrahy stavu. Můžete kliknutím z libovolné komponenty na podrobnější diagnostiku, jako jsou události služby Application Insights. Pokud vaše aplikace používá služby Azure, můžete také kliknout prostřednictvím a Diagnostika Azure, jako je SQL Database Advisor doporučení.

## <a name="what-is-a-component"></a>Co je komponenta?

Součásti jsou umožňují nezávislé nasazení částí aplikace distribuovat/mikroslužeb. Vývojáři provozní týmy a týmy mají viditelnost na úrovni kódu nebo přístup k telemetrii generovanou těmito součásti aplikace. 

* Komponenty se liší od "zjištěnou" externí závislosti, jako je SQL, atd centra událostí, které tým nebo organizace nemusí mít přístup k (kód nebo telemetrické údaje).
* Komponenty jsou spuštěny na libovolný počet instancí role/server/kontejner.
* Součástí může být samostatný instrumentačních klíčů Application Insights (i v případě, že předplatná se liší) nebo různé role, generování sestav na jeden Instrumentační klíč Application Insights. Mapování prostředí ve verzi preview se zobrazí komponenty bez ohledu na to, jak jsou nastavené.

## <a name="composite-application-map"></a>Mapa kompozitní aplikace

Zobrazí se topologie celou aplikaci napříč několika úrovněmi souvisejících aplikací komponenty. Součástí může být různé prostředky Application Insights nebo různé role v jediném prostředku. Mapa aplikace vyhledá součásti podle následujícího volání závislostí protokolu HTTP mezi servery pomocí Application Insights SDK nainstalovat. 

Toto prostředí začíná progresivní zjišťování komponent. Při prvním načtení mapy aplikace, se aktivuje sadu dotazů ke zjištění součásti související se tuto komponentu. Tlačítko v levém horním se aktualizuje počet součástí v aplikaci při jejich zjištění. 

Po kliknutí na tlačítko "Součásti mapy aktualizace", mapy aktualizují se všechny součásti zjištěny, dokud, které ukazují. Podle složitosti vaší aplikace může to trvat minutu načíst.

Pokud jsou všechny komponenty rolí v rámci jednoho prostředku Application Insights, pak tento krok zjišťování se nevyžaduje. Počáteční načtení pro takové aplikace budou mít všechny jeho součásti.

![Snímek obrazovky aplikace mapy](media/app-map/app-map-001.png)

Jedním z klíčových cílů s toto prostředí je možné k vizualizaci komplexní topologie spolu se stovkami komponenty.

Klikněte na libovolné součásti najdete v článku související přehledy a přejít k výkonu a selhání prostředí pro třídění pro danou součást.

![Kontextová nabídka](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Prověřit chyby

Vyberte **vyšetřování chyb** ke spuštění v podokně selhání.

![Snímek obrazovky prošetřit selhání tlačítko](media/app-map/investigate-failures.png)

![Snímek obrazovky selhání prostředí](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prověřit výkon

Chcete-li vyřešit problémy s výkonem, vyberte **vyšetřování výkonu**.

![Snímek obrazovky zkoumání výkonu tlačítko](media/app-map/investigate-performance.png)

![Snímek obrazovky výkon](media/app-map/performance.png)

### <a name="go-to-details"></a>Přejít na podrobnosti

Vyberte **přejděte na podrobnosti** prozkoumat prostředí začátku do konce transakce, které může nabídnout zobrazení až na úroveň zásobníku volání.

![Snímek obrazovky tlačítko Přejít na podrobnosti](media/app-map/go-to-details.png)

![Snímek obrazovky podrobností transakcí začátku do konce](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Zobrazit v Analytics

Pro dotazování a prozkoumání dalších dat aplikací, klikněte na tlačítko **zobrazit v analytics**.

![Snímek obrazovky zobrazení v tlačítko analytics](media/app-map/view-in-analytics.png)

![Snímek obrazovky s analytics prostředí](media/app-map/analytics.png)

### <a name="alerts"></a>Výstrahy

Chcete-li zobrazit aktivní výstrahy a základní pravidla, které způsobují upozornění aktivovat, vyberte **výstrahy**.

![Snímek obrazovky tlačítka pro výstrahy](media/app-map/alerts.png)

![Snímek obrazovky s analytics prostředí](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Sada název cloudové role

Mapa aplikace používá **název cloudové role** vlastnost k identifikaci komponenty na mapě. Vlastnost názvu role cloud sadu SDK Application Insights automaticky přidá do telemetrických dat, protože ho vygeneroval komponenty. Sada SDK se například přidat název webového serveru nebo název role služby pro vlastnost název cloudové role. Existují však případy, kde můžete chtít potlačit výchozí hodnotu. Název cloudové role přepsat a změňte, co zobrazí na mapě aplikace:

### <a name="netnet-core"></a>.NET/.NET Core

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

**Inicializátor zatížení pro aktivní TelemetryConfiguration**

In ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

> [!NOTE]
> Přidáváním inicializátoru pomocí `ApplicationInsights.config` není platný pro aplikace ASP.NET Core.

K vytvoření instance inicializátoru v kódu, například v souboru Global.aspx.cs je alternativní metoda pro ASP.NET Web apps:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

Pro [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) aplikace, přidání nového `TelemetryInitializer` se provádí tak, že přidáte do kontejneru injektáž závislostí, jak je znázorněno níže. To se provádí v `ConfigureServices` metodu vaše `Startup.cs` třídy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternativní metoda pro Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Pokud používáte Spring Boot s Application Insights Spring Boot starter, je pouze požadovaná změna nastavit váš vlastní název aplikace v souboru application.properties souboru.

`spring.application.name=<name-of-app>`

Spring Boot starter automaticky přiřadí název cloudové role zadaná pro vlastnost spring.application.name hodnota.

Další informace o Javě korelace a jak nakonfigurovat roli v cloudových procesech název rezervovat aplikace bez SpringBoot to [části](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na korelace.

### <a name="clientbrowser-side-javascript"></a>JavaScript a prohlížeči klientů

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Principy název cloudové role v rámci kontextu Mapa aplikace

Pokud je to jak uvažovat o **název cloudové role**, může být užitečné podívat se na mapu aplikace, která má více rolí cloud názvů k dispozici:

![Snímek obrazovky aplikace mapy](media/app-map/cloud-rolename.png)

V mapě aplikace nad jednotlivé názvy v zelené polí jsou cloudové hodnoty název role pro různé aspekty této konkrétní distribuované aplikace. Takže pro tuto aplikaci jeho role se skládají z: `Authentication`, `acmefrontend`, `Inventory Management`, `Payment Processing Worker Role`. 

V případě této aplikace každý z těchto názvů rolí cloud také představuje jiný jedinečný prostředek Application Insights s vlastními klíči instrumentace. Protože vlastníka této aplikace má přístup ke každé z těchto čtyř nesourodé prostředky Application Insights, mapa aplikace je možné spojit dohromady mapu základní relace.

Pro [oficiální definice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativně **instance cloudové role** může být užitečné pro scénáře, ve kterém **název cloudové role** zjistíte problém je chyba někde webového front-endu, ale může být spuštěn webového front-endu napříč více s vyrovnáváním zatížení serverů tak nebudou moct zobrazit podrobnosti ve vrstvě hlubší pomocí dotazů Kusto a vědět, pokud je tento problém vliv na všechny front-endové servery pro/instance webové nebo pouze jedna může být velmi důležité.

Scénář, kde můžete chtít přepsat hodnotu pro instanci role cloud může být, pokud vaše aplikace běží v prostředí kontejnerizovaných kde stačí vědět, jednotlivých server nemusí být dostatek informací k vyhledání daného problému.

Další informace o tom, jak přepsat vlastnosti název cloudové role s inicializátory telemetrická data, najdete v části [přidat vlastnosti: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte problémy dostat se mapa aplikace fungovat podle očekávání, proveďte následující kroky:

### <a name="general"></a>Obecné

1. Ujistěte se, že používáte oficiálně podporovanou sadu SDK. Nepodporované nebo komunitní sady SDK nemusí podporovat korelaci.

    Projít tento [článku](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) seznam podporovaných sad SDK.

2. Všechny součásti upgradujte na nejnovější verzi sady SDK.

3. Pokud používáte Azure Functions s C#, proveďte upgrade na [funkce V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Potvrďte [název cloudové role](#set-cloud-role-name) je správně nakonfigurovaný.

5. Pokud vám chybí závislost, ujistěte se, že je v seznamu [automaticky shromažďovaných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud ne, můžete ji stále najít ručně pomocí [volání TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Příliš mnoho uzly na mapě

Mapa aplikace vytvoří do uzlu aplikace pro každý jedinečné cloudové název role k dispozici v žádosti o telemetrii a uzel závislosti pro každou jedinečnou kombinaci typu, cíl a název cloudové role v telemetrii závislostí. Pokud existuje víc než 10 000 uzly v telemetrii, mapa aplikace nebude možné načíst všechny uzly a odkazy, takže vaše mapa nebudou úplné. Pokud k tomu dojde, zobrazí se zpráva upozornění při zobrazení na mapě.

Mapa aplikace navíc podporuje jenom až 1 000 samostatné neseskupení uzly vykreslovány najednou. Mapa aplikace pro snížení složitosti visual prostřednictvím seskupování závislosti, které mají stejný typ a volající, ale pokud telemetrických dat má příliš mnoho názvů rolí jedinečná Cloudová nebo příliš mnoho typy závislostí, tuto skupinu, budou mít dostatek a mapy nebude moci vykreslení.

To pokud chcete napravit, budete muset změnit váš Instrumentační správně nastavit název cloudové role, typ závislosti a závislosti cílového pole.

* Logický název závislost by měla představovat cíle závislosti. V mnoha případech je ekvivalentní k serveru nebo název prostředku závislosti. Například v případě závislosti protokolu HTTP je nastavený na název hostitele. Neměl by obsahovat jedinečné ID nebo parametry, které se mění z jednoho požadavku na jiný.

* Typ závislosti by měla představovat logický typ závislosti. Závislost typické typy jsou například HTTP, SQL nebo objektů Blob v Azure. Neměl by obsahovat jedinečné identifikátory.

* Účel název cloudové role je popsán v [nad části](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Portálu zpětné vazby

Pokud chcete poskytnout zpětnou vazbu, použijte možnost zpětnou vazbu.

![Obrázek MapLink 1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Další postup

* [Principy korelace](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
