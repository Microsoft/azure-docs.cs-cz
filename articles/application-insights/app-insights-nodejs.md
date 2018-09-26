---
title: Monitorování služeb Node.js pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorujte výkon a diagnostikujte problémy ve službách Node.js pomocí Application Insights.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: mbullwin
ms.openlocfilehash: 28be3a1734639ac175e4d18d9e9f21b83b9a7e7c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093998"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorování služeb a aplikací Node.js pomocí Application Insights

[Azure Application Insights](app-insights-overview.md) monitoruje po nasazení vaše služby a komponenty back-end a tím vám pomáhá [zjišťovat a rychle diagnostikovat problémy nejen s výkonem](app-insights-detect-triage-diagnose.md). Application Insights můžete použít pro služby Node.js hostované ve vašem datovém centru, na virtuálních počítačích a ve webových aplikacích Azure a dokonce i v jiných veřejných cloudech.

Pokud chcete přijímat, ukládat a prozkoumávat data monitorování, vložte do svého kódu sadu SDK a pak v Azure nastavte odpovídající prostředek Application Insights. Sada SDK do tohoto prostředku odesílá data pro další analýzy a prozkoumávání.

Sada Node.js SDK dokáže automaticky monitorovat příchozí a odchozí požadavky HTTP, výjimky a některé systémové metriky. Od verze 0.20 dokáže sada SDK monitorovat také některé běžné balíčky třetích stran, například MongoDB, MySQL nebo Redis. Všechny události související s příchozím požadavkem HTTP se korelují za účelem rychlejšího řešení potíží.

Pomocí rozhraní TelemetryClient API můžete ručně instrumentovat a monitorovat další aspekty aplikace a systému. Rozhraní TelemetryClient API popisujeme podrobněji dále v tomto článku.

![Příklady tabulek sledování výkonu](./media/app-insights-nodejs/10-perf.png)

## <a name="get-started"></a>Začínáme

Proveďte následující úlohy a nastavte monitorování pro aplikaci nebo službu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte předplatné Azure nebo [zdarma získejte nové předplatné][azure-free-offer]. Pokud vaše organizace již má předplatné Azure, správce vás do něj může přidat pomocí [těchto pokynů][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a>Nastavení prostředku Application Insights


1. Přihlaste se na web [Azure Portal][portal].
2. Vyberte **Vytvořit prostředek** > **Vývojářské nástroje** > **Application Insights**. Prostředek zahrnuje koncový bod pro příjem telemetrických dat, úložiště pro tato data, uložené sestavy a řídicí panely, konfigurace pravidel a upozornění a ještě více.

  ![Vytvořte prostředek Application Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

3. Na stránce vytváření prostředku v poli **Typ aplikace** vyberte **Aplikace Node.js**. Typ aplikace určuje výchozí řídicí panely a sestavy, které se vytvoří. (Každý prostředek Application Insights může shromažďovat data z jakéhokoli jazyka a libovolné platformy.)

  ![Formulář Nový prostředek Application Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="sdk"></a>Nastavení sady Node.js SDK

Vložte do své aplikace sadu SDK, aby mohla shromažďovat data. 

1. Zkopírujte instrumentační klíč vašeho prostředku (označuje se také jako *ikey*) z webu Azure Portal. Application Insights pomocí instrumentačního klíče mapuje data na váš prostředek Azure. Než bude sada SDK moci váš instrumentační klíč použít, musíte jej zadat v proměnné prostředí nebo ve svém kódu.  

  ![Zkopírování instrumentačního klíče](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

2. Přidejte do závislostí aplikace knihovnu sady Node.js SDK prostřednictvím souboru package.json. Z kořenové složky aplikace spusťte:

  ```bash
  npm install applicationinsights --save
  ```

3. Explicitně načtěte knihovnu v kódu. Vzhledem k tomu, že sada SDK vkládá instrumentaci do mnoha dalších knihoven, načtěte knihovnu co nejdříve, dokonce ještě před dalšími příkazy `require`. 

  Na začátek vašeho prvního souboru .js přidejte následující kód. Metoda `setup` konfiguruje instrumentační klíč (a tedy prostředek Azure), který se použije jako výchozí pro všechny sledované položky.

  ```javascript
  const appInsights = require("applicationinsights");
  appInsights.setup("<instrumentation_key>");
  appInsights.start();
  ```
   
  Instrumentační klíč můžete místo ručního předávání do metody `setup()` nebo `new appInsights.TelemetryClient()` zadat také přes proměnnou prostředí APPINSIGHTS\_INSTRUMENTATIONKEY. Tento postup umožňuje oddělit instrumentační klíče od potvrzeného zdrojového kódu a pro různá prostředí můžete určit různé instrumentační klíče.

  Další možnosti konfigurace najdete v následujících částech.

  Sadu SDK můžete vyzkoušet bez odesílání telemetrie nastavením `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor"></a> Monitorování aplikace

Sada SDK automaticky shromažďuje telemetrii o modulu runtime Node.js a některých běžných modulech třetích stran. Použijte svou aplikaci k vygenerování nějakých dat.

Potom na webu [Azure Portal][portal] přejděte k prostředku Application Insights, který jste vytvořili dříve. V části **Časová osa přehledu** vyhledejte vašich prvních pár datových bodů. Pokud chcete zobrazit podrobnější data, vyberte v grafech různé komponenty.

![První datové body](./media/app-insights-nodejs/12-first-perf.png)

Pokud chcete zobrazit zjištěnou topologii vaší aplikace, vyberte tlačítko **Mapa aplikace**. Výběrem komponent na mapě zobrazíte další podrobnosti.

![Mapa jedné aplikace](./media/app-insights-nodejs/06-appinsights_appmap.png)

Výběrem dalších dostupných zobrazení v části **PROZKOUMÁVÁNÍ** můžete zjistit další informace o vaší aplikaci a řešit problémy.

![Část Prozkoumávání](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Žádná data?

Vzhledem k tomu, že sada SDK seskupuje data do dávek pro odesílání, může docházet k prodlevám v zobrazení položek na portálu. Pokud ve svém prostředku nevidíte data, vyzkoušejte některou z následujících oprav:

* Pokračujte v používání aplikace. Proveďte více akcí pro vygenerování další telemetrie.
* V zobrazení prostředku na portálu klikněte na **Aktualizovat**. Grafy se samy pravidelně aktualizují, ale ruční aktualizace vynutí jejich okamžitou aktualizaci.
* Ověřte, že jsou otevřené [požadované výchozí porty](app-insights-ip-addresses.md).
* Pomocí [Vyhledávání](app-insights-diagnostic-search.md) vyhledejte konkrétní události.
* Přečtěte si [Nejčastější dotazy][FAQ].


## <a name="sdk-configuration"></a>Konfigurace sady SDK

Metody konfigurace sady SDK a výchozí hodnoty jsou uvedeny v následujícím příkladu kódu.

Pro úplnou korelaci událostí ve službě nezapomeňte nastavit `.setAutoDependencyCorrelation(true)`. Nastavení této možnosti umožní sadě SDK sledovat kontext napříč asynchronními zpětnými voláními v Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient API

Úplný popis rozhraní TelemetryClient API najdete v tématu [Rozhraní API Application Insights pro vlastní události a metriky](app-insights-api-custom-events-metrics.md).

Pomocí sady Application Insights Node.js SDK můžete sledovat jakékoli požadavky, události, metriky nebo výjimky. Následující příklad kódu ukazuje některá z rozhraní API, která můžete použít:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Sledování závislostí

Pomocí následujícího kódu můžete sledovat závislosti:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Přidání vlastní vlastnosti do všech událostí

Pomocí následující kód můžete do všech událostí přidat vlastní vlastnost:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Sledování požadavků HTTP GET

Pomocí následujícího kódu můžete sledovat požadavky HTTP GET:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Sledování času spuštění serveru

Pomocí následujícího kódu můžete čas spuštění serveru:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Další postup

* [Monitorování telemetrických dat na portálu](app-insights-dashboards.md)
* [Psaní analytických dotazů do telemetrických dat](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md

