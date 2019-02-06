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
ms.date: 12/17/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f2bd1d863a7900b50712eb23c1088c6b271befa3
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755885"
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

Toto prostředí začíná progresivní zjišťování komponent. Při prvním načtení mapy aplikace, se spouštějí sady dotazů ke zjištění součásti související se tuto komponentu. Tlačítko v levém horním se aktualizuje počet součástí v aplikaci při jejich zjištění. 

Po kliknutí na tlačítko "Součásti mapy aktualizace", mapy aktualizují se všechny součásti zjištěny, dokud, které ukazují. Podle složitosti vaší aplikace může to trvat minutu načíst.

Pokud jsou všechny komponenty rolí v rámci jednoho prostředku Application Insights, pak tento krok zjišťování se nevyžaduje. Počáteční načtení pro takové aplikace budou mít všechny jeho součásti.

![Snímek obrazovky aplikace mapy](media/app-map/001.png)

Jedním z klíčových cílů s toto prostředí je možné k vizualizaci komplexní topologie spolu se stovkami komponenty.

Klikněte na libovolné součásti najdete v článku související přehledy a přejít k výkonu a selhání prostředí pro třídění pro danou součást.

![Kontextová nabídka](media/app-map/application-map-001.png)

### <a name="investigate-failures"></a>Prověřit chyby

Vyberte **vyšetřování chyb** ke spuštění v podokně selhání.

![Snímek obrazovky prošetřit selhání tlačítko](media/app-map/investigate-failures.png)

![Snímek obrazovky selhání prostředí](media/app-map/failures.png)

### <a name="investigate-performance"></a>Prověřit výkon

Chcete-li vyřešit problémy s výkonem, vyberte **vyšetřování výkonu**.

![Snímek obrazovky zkoumání výkonu tlačítko](media/app-map/investigate-performance.png)

![Snímek obrazovky výkon](media/app-map/performance.png)

### <a name="go-to-details"></a>Přejít na podrobnosti

Vyberte **přejděte na podrobnosti** prozkoumat prostředí začátku do konce transakce, které může nabídnout zobrazení provést na úrovni zásobníku volání.

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

## <a name="set-cloudrolename"></a>Sada cloud_RoleName

Mapa aplikace používá `cloud_RoleName` vlastnost k identifikaci komponenty na mapě. Sada SDK služby Application Insights automaticky přidá `cloud_RoleName` vlastnost telemetrická data, protože ho vygeneroval komponenty. Například sada SDK přidá název webového serveru nebo název služby role `cloud_RoleName` vlastnost. Existují však případy, kde můžete chtít potlačit výchozí hodnotu. Přepsat cloud_RoleName a změňte, co zobrazí na mapě aplikace:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleName = "RoleName";
            }
        }
    }
}
```

**Načíst vaše inicializátor**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Je alternativní metoda pro vytvoření instance inicializátoru v kódu, například v souboru Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
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

Spring Boot starter bude automaticky přiřadit hodnotu zadanou pro vlastnost spring.application.name cloudRoleName.

Další informace o korelaci Java a konfigurování cloudRoleName rezervovat aplikace bez SpringBoot [části](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) na korelace.

### <a name="clientbrowser-side-javascript"></a>JavaScript a prohlížeči klientů

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

Další informace o tom, jak přepsat vlastnost cloud_RoleName inicializátory telemetrická data, najdete v části [přidat vlastnosti: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte problémy dostat se mapa aplikace fungovat podle očekávání, proveďte následující kroky:

1. Ujistěte se, že používáte oficiálně podporovanou sadu SDK. Nepodporované nebo komunitní sady SDK nemusí podporovat korelaci.

    Projít tento [článku](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) seznam podporovaných sad SDK.

2. Všechny součásti upgradujte na nejnovější verzi sady SDK.

3. Pokud používáte Azure Functions s C#, proveďte upgrade na [funkce V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Potvrďte [cloud_RoleName](app-map.md#set-cloudrolename) je správně nakonfigurovaný.

5. Pokud vám chybí závislost, ujistěte se, že je v seznamu [automaticky shromažďovaných závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Pokud ne, můžete ji stále najít ručně pomocí [volání TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="portal-feedback"></a>Portálu zpětné vazby
Pokud chcete poskytnout zpětnou vazbu, použijte možnost zpětnou vazbu.

![Obrázek MapLink 1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Další postup

* [Principy korelace](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
