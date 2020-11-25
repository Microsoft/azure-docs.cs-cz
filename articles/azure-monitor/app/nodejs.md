---
title: Monitorování služeb Node.js pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorujte výkon a diagnostikujte problémy ve službách Node.js pomocí Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 982adf6c6d7cd825d185802321ce30a04bd2f216
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020887"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorování služeb a aplikací Node.js pomocí Application Insights

[Application Insights](./app-insights-overview.md) po nasazení monitorovat vaše back-end služby a komponenty, které vám pomůžou zjistit a rychle diagnostikovat výkon a další problémy. Application Insights můžete použít pro Node.js služby, které jsou hostované ve vašem datovém centru, virtuálních počítačích Azure a webových aplikací, a dokonce i v jiných veřejných cloudech.

Pokud chcete přijímat, ukládat a prozkoumávat data monitorování, vložte do svého kódu sadu SDK a pak v Azure nastavte odpovídající prostředek Application Insights. Sada SDK do tohoto prostředku odesílá data pro další analýzy a prozkoumávání.

Sada Node.js SDK dokáže automaticky monitorovat příchozí a odchozí požadavky HTTP, výjimky a některé systémové metriky. Počínaje verzí 0,20 může sada SDK také monitorovat některé běžné [balíčky třetích stran](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules), jako jsou MongoDB, MySQL a Redis. Všechny události související s příchozím požadavkem HTTP se korelují za účelem rychlejšího řešení potíží.

Pomocí rozhraní TelemetryClient API můžete ručně instrumentovat a monitorovat další aspekty aplikace a systému. Rozhraní TelemetryClient API popisujeme podrobněji dále v tomto článku.

## <a name="get-started"></a>Začínáme

Proveďte následující úlohy a nastavte monitorování pro aplikaci nebo službu.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte předplatné Azure nebo [zdarma získejte nové předplatné][azure-free-offer]. Pokud vaše organizace již má předplatné Azure, správce vás do něj může přidat pomocí [těchto pokynů][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Nastavení prostředku Application Insights

1. Přihlaste se na [Azure Portal][portal].
2. [Vytvoření prostředku Application Insights](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a>Nastavení sady Node.js SDK

Vložte do své aplikace sadu SDK, aby mohla shromažďovat data.

1. Zkopírujte klíč instrumentace prostředku (označovaný také jako *ikey*) z nově vytvořeného prostředku. Application Insights pomocí instrumentačního klíče mapuje data na váš prostředek Azure. Než bude sada SDK moci váš instrumentační klíč použít, musíte jej zadat v proměnné prostředí nebo ve svém kódu.  

   ![Zkopírování instrumentačního klíče](./media/nodejs/instrumentation-key-001.png)

2. Přidejte do závislostí aplikace knihovnu sady Node.js SDK prostřednictvím souboru package.json. Z kořenové složky aplikace spusťte:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Pokud používáte TypeScript, neinstalujte samostatné balíčky "psaní". Tento balíček NPM obsahuje integrované úseky kódu.

3. Explicitně načtěte knihovnu v kódu. Vzhledem k tomu, že sada SDK vkládá instrumentaci do mnoha dalších knihoven, načtěte knihovnu co nejdříve, dokonce ještě před dalšími příkazy `require`.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Můžete také zadat ikey prostřednictvím proměnné prostředí `APPINSIGHTS_INSTRUMENTATIONKEY` místo ručního předání do  `setup()` nebo `new appInsights.TelemetryClient()` . Tento postup umožňuje oddělit instrumentační klíče od potvrzeného zdrojového kódu a pro různá prostředí můžete určit různé instrumentační klíče. Pro ruční konfiguraci volání `appInsights.setup('[your ikey]');` .

    Další možnosti konfigurace najdete v následujících částech.

    Sadu SDK můžete vyzkoušet bez odesílání telemetrie nastavením `appInsights.defaultClient.config.disableAppInsights = true`.

5. Zahajte automatické shromažďování a posílání dat voláním `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Monitorování aplikace

Sada SDK automaticky shromažďuje telemetrii o modulu runtime Node.js a některých běžných modulech třetích stran. Použijte svou aplikaci k vygenerování nějakých dat.

Potom na webu [Azure Portal][portal] přejděte k prostředku Application Insights, který jste vytvořili dříve. V části **Časová osa přehledu** vyhledejte vašich prvních pár datových bodů. Pokud chcete zobrazit podrobnější data, vyberte v grafech různé komponenty.

Chcete-li zobrazit topologii, která je zjištěna pro vaši aplikaci, můžete použít [mapu aplikace](app-map.md).

#### <a name="no-data"></a>Žádná data

Vzhledem k tomu, že sada SDK seskupuje data do dávek pro odesílání, může docházet k prodlevám v zobrazení položek na portálu. Pokud ve svém prostředku nevidíte data, vyzkoušejte některou z následujících oprav:

* Pokračujte v používání aplikace. Proveďte více akcí pro vygenerování další telemetrie.
* V zobrazení prostředku na portálu klikněte na **Aktualizovat**. Grafy se samy pravidelně aktualizují, ale ruční aktualizace vynutí jejich okamžitou aktualizaci.
* Ověřte, že jsou otevřené [požadované výchozí porty](./ip-addresses.md).
* Pomocí [Vyhledávání](./diagnostic-search.md) vyhledejte konkrétní události.
* Projděte si [Nejčastější dotazy][FAQ].

## <a name="basic-usage"></a>Základní využití

Pro předem připravenou kolekci požadavků HTTP, oblíbených událostí knihovny třetích stran, neošetřených výjimek a systémových metrik:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Pokud je klíč instrumentace nastavený v proměnné prostředí `APPINSIGHTS_INSTRUMENTATIONKEY` , dá se `.setup()` volat bez argumentů. Díky tomu můžete snadno používat různé instrumentační klíče pro různá prostředí.

`require("applicationinsights")`Před načtením dalších balíčků načtěte Application Insights knihovnu, co nejdříve ve skriptech. To je potřeba, aby Knihovna Application Insights mohla připravit pozdější balíčky pro sledování. Pokud narazíte na konflikty s jinými knihovnami s podobným přípravou, zkuste po nich načíst knihovnu Application Insights.

Z důvodu způsobu, jakým JavaScript zpracovává zpětná volání, je nutné další práci ke sledování požadavku napříč externími závislostmi a pozdějšími zpětnými voláními. Ve výchozím nastavení je toto další sledování povoleno; Zakažte ho voláním `setAutoDependencyCorrelation(false)` , jak je popsáno v části [Konfigurace](#sdk-configuration) níže.

## <a name="migrating-from-versions-prior-to-022"></a>Migrace z verzí starších než 0,22

Existují zásadní změny mezi verzemi před verzí 0,22 a novější. Tyto změny jsou navržené tak, aby byly konzistentní s jinými Application Insights SDK a umožňují budoucí rozšiřitelnost.

Obecně platí, že můžete migrovat pomocí následujících možností:

- Nahraďte odkazy na `appInsights.client` s `appInsights.defaultClient` .
- Nahradit odkazy na `appInsights.getClient()` s `new appInsights.TelemetryClient()`
- Nahradí všechny argumenty na Client. Track * metody s jedním objektem, který obsahuje pojmenované vlastnosti jako argumenty. Podívejte se na integrované pomocné informace o typu integrovaného vývojového prostředí ( [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) ) pro objekt s výjimkou objektu pro každý typ telemetrie.

Pokud máte přístup ke konfiguračním funkcím sady SDK bez jejich zřetězení do `appInsights.setup()` , můžete tyto funkce nyní najít na `appInsights.Configurations` (například `appInsights.Configuration.setAutoCollectDependencies(true)` ). Projděte si změny výchozí konfigurace v následující části.

## <a name="sdk-configuration"></a>Konfigurace sady SDK

`appInsights`Objekt poskytuje řadu metod konfigurace. Jsou uvedeny v následujícím fragmentu kódu s výchozími hodnotami.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Pro úplnou korelaci událostí ve službě nezapomeňte nastavit `.setAutoDependencyCorrelation(true)`. Nastavení této možnosti umožní sadě SDK sledovat kontext napříč asynchronními zpětnými voláními v Node.js.

Přečtěte si jejich popisy v integrovaném hintu typu integrovaného vývojového prostředí (IDE) nebo [ApplicationInsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) , kde najdete podrobné informace o tomto ovládacím prvku a volitelných sekundárních argumentech.

> [!NOTE]
>  Ve výchozím nastavení `setAutoCollectConsole` je nakonfigurované tak, aby *vyloučil* volání `console.log` (a jiné metody konzoly). Budou shromažďována pouze volání podporovaných protokolovacích nástrojů třetích stran (například Winston a Bunyan). Toto chování můžete změnit tak, aby zahrnovalo volání `console` metod pomocí `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Vzorkování

Ve výchozím nastavení bude sada SDK odesílat všechna shromážděná data do služby Application Insights. Pokud shromáždíte spoustu dat, možná budete chtít povolit vzorkování, abyste snížili množství odesílaných dat. Nastavte `samplingPercentage` pole na `config` objektu klienta, který chcete dosáhnout. Nastavení `samplingPercentage` na hodnotu 100 (výchozí) znamená, že všechna data budou odeslána a 0 znamená, že nebudou odeslány žádné údaje.

Pokud používáte automatickou korelaci, budou všechna data přidružená k jedné žádosti zahrnuta nebo vyloučena jako jednotka.

Pro povolení vzorkování přidejte kód, například následující:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Více rolí pro aplikace s více komponentami

Pokud se vaše aplikace skládá z několika součástí, které chcete instrumentovat pomocí stejného klíče instrumentace, a tyto komponenty pořád vidíte jako samostatné jednotky na portálu, jako kdyby byly použity samostatné klíče instrumentace (například jako samostatné uzly na mapě aplikace), bude pravděpodobně nutné ručně nakonfigurovat pole RoleName, aby se odlišila jedna telemetrie součásti od jiných komponent odesílajících data do vašeho prostředku Application Insights.

K nastavení pole RoleName použijte následující:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Automatické instrumentace třetí strany

Aby bylo možné sledovat kontext napříč asynchronními voláními, jsou některé změny požadovány v knihovnách třetích stran, jako jsou MongoDB a Redis. Ve výchozím nastavení Application Insights použít [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) k opice-patch některé z těchto knihoven. To lze zakázat nastavením `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` proměnné prostředí.

> [!NOTE]
> Nastavením této proměnné prostředí již nemusí být události správně přidruženy k správné operaci.

 Individuální opice – opravy lze zakázat nastavením `APPLICATION_INSIGHTS_NO_PATCH_MODULES` proměnné prostředí na seznam balíčků, které jsou odděleny čárkou (například), aby se `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` zabránilo opravování `console` `redis` balíčků a.

V současné době jsou k dispozici devět balíčků, které jsou instrumentované: `bunyan` , `console` , `mongodb` , `mongodb-core` , `mysql` , `redis` , `winston` , a `pg` `pg-pool` . Informace o přesně tom, která verze těchto balíčků je opravená, najdete v [souboru Readme pro diagnostiku kanálu](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) .

`bunyan`Opravy, `winston` a `console` se vygenerují Application Insights události trasování na základě toho, jestli `setAutoCollectConsole` je povolený. V závislosti na tom, jestli je povolený, bude zbývající vygenerovat události závislosti Application Insights `setAutoCollectDependencies` .

### <a name="live-metrics"></a>Live Metrics

Pokud chcete povolit odesílání živých metrik z vaší aplikace do Azure, použijte `setSendLiveMetrics(true)` . Filtrování živých metrik na portálu se v tuto chvíli nepodporuje.

### <a name="extended-metrics"></a>Rozšířené metriky

> [!NOTE]
> Do verze 1.4.0 se přidala možnost odesílat rozšířené nativní metriky.

Pokud chcete povolit odesílání rozšířených nativních metrik z vaší aplikace do Azure, nainstalujte samostatný balíček s nativními metrikami. Sada SDK se automaticky načte při instalaci a začne shromažďovat Node.js nativní metriky.

```bash
npm install applicationinsights-native-metrics
```

V současné době balíček nativních metrik provádí další shromažďování času procesoru uvolňování paměti, impulsů smyček událostí a využití haldy:

- **Uvolňování paměti**: množství času procesoru stráveného na každém typu uvolňování paměti a počet výskytů každého typu.
- **Smyčka událostí**: kolik taktů a kolik času CPU bylo stráveno celkem.
- **Halda vs. nehalda**: kolik využití paměti vaší aplikace je v haldě nebo jiné haldě.

### <a name="distributed-tracing-modes"></a>Režimy distribuovaného trasování

Ve výchozím nastavení SDK pošle hlavičky srozumitelné pro jiné aplikace nebo služby, které jsou instrumentované pomocí sady Application Insights SDK. Volitelně můžete povolit odesílání a příjem hlaviček [kontextu trasování W3C](https://github.com/w3c/trace-context) kromě existujících hlaviček AI, takže nebudete přerušovat korelace s žádnou z existujících služeb starší verze. Povolením hlaviček W3C umožníte vaší aplikaci korelaci s jinými službami, které nejsou instrumentované pomocí Application Insights, ale přijímají tuto technologii W3C Standard.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

Úplný popis rozhraní TelemetryClient API najdete v tématu [Rozhraní API Application Insights pro vlastní události a metriky](./api-custom-events-metrics.md).

Pomocí sady Application Insights Node.js SDK můžete sledovat jakékoli požadavky, události, metriky nebo výjimky. Následující příklad kódu ukazuje některá z rozhraní API, která můžete použít:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
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
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Ukázkový nástroj, pomocí `trackMetric` kterého lze změřit, jak dlouho plánování smyčky událostí trvá:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Přidání vlastní vlastnosti do všech událostí

Pomocí následující kód můžete do všech událostí přidat vlastní vlastnost:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Sledování požadavků HTTP GET

Chcete-li ručně sledovat požadavky HTTP GET, použijte následující kód:

> [!NOTE]
> Ve výchozím nastavení jsou všechny požadavky sledovány. Chcete-li zakázat automatické shromažďování, volejte. setAutoCollectRequests (false) před voláním metody Start ().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Alternativně můžete sledovat žádosti pomocí `trackNodeHttpRequest` metody:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
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

### <a name="preprocess-data-with-telemetry-processors"></a>Předzpracování dat pomocí procesorů telemetrie

Shromážděná data můžete zpracovávat a filtrovat předtím, než se odešlou pro uchování pomocí *procesorů telemetrie*. Procesory telemetrie se v pořadí, v jakém byly přidány před odesláním položky telemetrie do cloudu, nazývají jednu.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Pokud procesor telemetrie vrátí hodnotu false, tato položka telemetrie nebude odeslána.

Všechny procesory telemetrie obdrží data telemetrie a její obálku pro kontrolu a úpravu. Také dostanou kontextový objekt. Obsah tohoto objektu je definován `contextObjects` parametrem při volání metody Track pro ručně sledovanou telemetrii. Pro automaticky shromážděnou telemetrii je tento objekt vyplněn pomocí dostupných informací o žádosti a obsahu trvalé žádosti, který `appInsights.getCorrelationContext()` je poskytován (Pokud je povolena automatická korelace závislosti).

Typ TypeScript pro procesor telemetrie je:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Například procesor, který odebere data trasování zásobníku z výjimek, může být napsán a přidán takto:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Použít více klíčů instrumentace

Můžete vytvořit několik prostředků Application Insights a do každého odeslat různá data pomocí příslušných klíčů instrumentace ("ikey").

 Například:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Rozšířené možnosti konfigurace

Objekt klienta obsahuje `config` vlastnost s mnoha nepovinnými nastaveními pro pokročilé scénáře. Můžete je nastavit takto:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Tyto vlastnosti jsou specifické pro konkrétního klienta, takže je můžete nakonfigurovat `appInsights.defaultClient` odděleně od klientů vytvořených pomocí nástroje `new appInsights.TelemetryClient()` .

| Vlastnost                        | Popis                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Identifikátor prostředku Application Insights.                                                      |
| endpointUrl                     | Koncový bod příjmu pro posílání datových částí telemetrie.                                                      |
| quickPulseHost                  | Hostitel Live Metrics Stream k odeslání telemetrie živých metrik do.                                            |
| proxyHttpUrl                    | Proxy server provozu sady SDK HTTP (volitelné, výchozí z `http_proxy` proměnné prostředí).     |
| proxyHttpsUrl                   | Proxy server provozu protokolu HTTPS sady SDK (volitelné, výchozí nastavení z `https_proxy` proměnné prostředí).   |
| httpAgent                       | Protokol HTTP. Agent, který se má použít pro přenosy HTTP přes SDK (volitelné, výchozí nedefinované).                                   |
| httpsAgent                      | Protokol HTTPS. Agent, který se má použít pro přenosy přes SDK HTTPS (volitelné, výchozí nedefinované).                                 |
| maxBatchSize                    | Maximální počet položek telemetrie, které se mají zahrnout do datové části pro koncový bod příjmu (výchozí `250` ).   |
| maxBatchIntervalMs              | Maximální doba, po kterou se má čekat na datovou část maxBatchSize (výchozí `15000` )               |
| disableAppInsights              | Příznak označující, zda je přenos telemetrie zakázán (výchozí `false` )                                 |
| samplingPercentage              | Procento sledovaných položek telemetrie, které mají být přeneseny (výchozí `100` ).                      |
| correlationIdRetryIntervalMs    | Doba, po kterou se má počkat, než se znovu pokusí načíst ID pro korelaci mezi komponentami (výchozí `30000` ).     |
| correlationHeaderExcludedDomains| Seznam domén, které se mají vyloučit z injektáže hlaviček korelace mezi komponentami (výchozí viz [config. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Další kroky

* [Monitorování vaší telemetrie na portálu](./overview-dashboard.md)
* [Zápis analytických dotazů nad telemetrií](../log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md

