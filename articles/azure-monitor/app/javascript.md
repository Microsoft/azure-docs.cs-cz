---
title: Azure Application Insights pro webové aplikace v JavaScriptu
description: Získejte zobrazení stránky a počty relací, data webového klienta, jednostránkové aplikace (SPA) a sledujte vzorce používání. Zjištění výjimek a problémů s výkonem na webových stránkách v jazyce JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 50ce0d57ec7395c69bf65e41b67f0cb005a43cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854979"
---
# <a name="application-insights-for-web-pages"></a>Application Insights pro webové stránky

Zjistěte informace o výkonu a využití webové stránky nebo aplikace. Pokud přidáte [Application Insights](app-insights-overview.md) do skriptu stránky, získáte časování načtení stránky a volání AJAX, počty a podrobnosti výjimek prohlížeče a selhání AJAX a také počty uživatelů a relací. Všechny tyto hodnoty mohou být segmentovány podle stránky, klientského operačního systému a verze prohlížeče, zeměpisné polohy a ostatních dimenzí. Můžete nastavit výstrahy na počet selhání nebo pomalé načítání stránky. A vložíte-li do kódu JavaScript trasování volání, můžete sledovat využití různých funkcí aplikace webové stránky.

Application Insights můžete použít s jakýmikoli webovými stránkami – stačí přidat krátký kód jazyka JavaScript. Pokud je vaše webová služba [Java](java-get-started.md) nebo [ASP.NET](asp-net.md), můžete použít sady SDK na straně serveru ve spojení se sadou JavaScript SDK na straně klienta k získání uceleného porozumění výkonu vaší aplikace.

## <a name="adding-the-javascript-sdk"></a>Přidání sady JavaScript SDK

1. Nejdřív potřebujete prostředek Application Insights. Pokud ještě nemáte prostředek a klíč instrumentace, postupujte podle [pokynů pro vytvoření nového prostředku](create-new-resource.md).
2. Zkopírujte klíč instrumentace z prostředku, kde chcete odeslat telemetrii JavaScriptu.
3. Přidejte sadu Application Insights JavaScript SDK do své webové stránky nebo aplikace pomocí jedné z následujících dvou možností:
    * [Nastavení npm](#npm-based-setup)
    * [Fragment kódu JavaScriptu](#snippet-based-setup)

> [!IMPORTANT]
> K přidání sady JavaScript SDK do aplikace použijte pouze jednu metodu. Použijete-li instalační program NPM, tento fragment kódu nepoužívejte ani naopak.

> [!NOTE]
> Instalační program NPM nainstaluje sadu JavaScript SDK jako závislost k vašemu projektu a umožní technologii IntelliSense, zatímco fragment kódu načte sadu SDK za běhu. Obě podporují stejné funkce. Vývojáři, kteří chtějí další vlastní události a konfigurace, obecně můžou souhlasit s NPM nastavením, zatímco uživatelé hledají rychlé povolení okamžitého zapnutí webové analýzy pro fragment.

### <a name="npm-based-setup"></a>nastavení založené na npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Nastavení na základě fragmentů kódu

Pokud vaše aplikace nepoužívá NPM, můžete své webové stránky přímo instrumentovat pomocí Application Insights vložením tohoto fragmentu do horní části každé stránky. Nejlépe by měl být první skript v `<head>` oddílu, aby mohl monitorovat případné problémy se všemi vašimi závislostmi. Pokud používáte aplikaci Blazor Server, přidejte fragment kódu na začátek souboru `_Host.cshtml` v `<head>` části.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Posílání telemetrie do Azure Portal

Ve výchozím nastavení Application Insights JavaScript SDK automaticky shromáždí řadu položek telemetrie, které jsou užitečné při určování stavu aplikace a podkladového uživatelského prostředí. Mezi ně patří:

- **Nezachycené výjimky** v aplikaci, včetně informací o
    - Trasování zásobníku
    - Podrobnosti o výjimce a zpráva doprovázející chybu
    - Řádek & sloupce – počet chyb
    - Adresa URL, kde byla vyvolána chyba
- Požadavky na **závislosti sítě** , které provedla vaše aplikace **XHR** a **Fetch** (načtení kolekce je ve výchozím nastavení zakázané), obsahují informace o
    - Adresa URL zdroje závislosti
    - Metoda & příkazu použitá pro vyžádání závislosti
    - Doba trvání žádosti
    - Kód výsledku a stav úspěchu žádosti
    - ID (pokud existuje) uživatele, který vytváří požadavek
    - Kontext korelace (pokud existuje), kde je učiněn požadavek
- **Informace o uživateli** (například umístění, síť, IP adresa)
- **Informace o zařízení** (například prohlížeč, operační systém, verze, jazyk, model)
- **informace o relaci**,

### <a name="telemetry-initializers"></a>Inicializátory telemetrie
Inicializátory telemetrie slouží k úpravě obsahu shromážděné telemetrie před jejich odesláním z prohlížeče uživatele. Můžete je také použít k zabránění odeslání určité telemetrie, a to vrácením `false`. Do instance Application Insights lze přidat více inicializátorů telemetrie a jsou spouštěny v pořadí jejich přidávání.

Vstupní argument `addTelemetryInitializer` pro je zpětné volání, které [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) přijímá jako argument a vrací `boolean` nebo. `void` Při vrácení `false`se položka telemetrie nepošle, jinak pokračuje k dalšímu inicializátoru telemetrie, pokud existuje, nebo se pošle do koncového bodu kolekce telemetrie.

Příklad použití inicializátorů telemetrie:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfigurace
Většina polí konfigurace je pojmenována tak, aby mohla být nastavená na hodnotu false. Všechna pole jsou volitelná s `instrumentationKey`výjimkou.

| Name | Výchozí | Popis |
|------|---------|-------------|
| instrumentationKey | null | **Požadováno**<br>Klíč instrumentace, který jste získali z Azure Portal. |
| accountId | null | Volitelné ID účtu, pokud vaše aplikace seskupí uživatele na účty. Žádné mezery, čárky, středníky, rovny nebo svislé čáry |
| sessionRenewalMs | 1800000 | Pokud je uživatel neaktivní po dobu v milisekundách, dojde k zaznamenání relace. Výchozí hodnota je 30 minut. |
| sessionExpirationMs | 86400000 | Relace je zaznamenána v případě, že v milisekundách pokračuje po dobu. Výchozí hodnota je 24 hodin. |
| maxBatchSizeInBytes | 10000 | Maximální velikost dávky telemetrie Pokud dávka tento limit překročí, pošle se hned a spustí se nová dávka. |
| maxBatchInterval | 15 000 | Doba, po kterou se má telemetrie v dávce před odesláním (milisekundy) |
| disableExceptionTracking | false (nepravda) | Je-li nastavena hodnota true, výjimky nebudou shromažďovány. Výchozí hodnota je false. |
| disableTelemetry | false (nepravda) | Pokud je nastaveno na true, telemetrie se neshromažďuje ani neposílá. Výchozí hodnota je false. |
| enableDebug | false (nepravda) | Při hodnotě true se **interní** data ladění vydávají jako výjimka **namísto** zaznamenávání bez ohledu na nastavení protokolování SDK. Výchozí hodnota je false. <br>***Poznámka:*** Povolení tohoto nastavení způsobí, že dojde k zahození telemetrie při každém výskytu vnitřní chyby. To může být užitečné, pokud chcete rychle identifikovat problémy s konfigurací nebo využitím sady SDK. Pokud nechcete při ladění přijít o telemetrii, zvažte použití `consoleLoggingLevel` nebo `telemetryLoggingLevel` místo. `enableDebug` |
| loggingLevelConsole | 0 | Zaznamená **vnitřní** chyby Application Insights do konzoly. <br>0: vypnuto, <br>1: jenom kritické chyby, <br>2: vše (chyby & upozornění) |
| loggingLevelTelemetry | 1 | Odesílá **interní** chyby Application Insights jako telemetrii. <br>0: vypnuto, <br>1: jenom kritické chyby, <br>2: vše (chyby & upozornění) |
| diagnosticLogInterval | 10000 | vnitřních Interval dotazování (v MS) pro interní frontu protokolování |
| samplingPercentage | 100 | Procento událostí, které budou odeslány. Výchozí hodnota je 100, což znamená, že jsou odesílány všechny události. Tuto hodnotu nastavte, pokud chcete zachovat svůj limit dat pro aplikace ve velkém měřítku. |
| autoTrackPageVisitTime | false (nepravda) | Pokud je hodnota true, v PageView je čas zobrazení předchozí instrumentované stránky sledován a odeslán jako telemetrie a pro aktuální PageView se spustí nový časovač. Výchozí hodnota je false. |
| disableAjaxTracking | false (nepravda) | V případě hodnoty true nejsou volání AJAX shromažďována znovu. Výchozí hodnota je false. |
| disableFetchTracking | true | Je-li nastavena hodnota true, žádosti o načtení nejsou shromažďovány. Výchozí hodnota je true. |
| overridePageViewDuration | false (nepravda) | Při hodnotě true se výchozí chování trackPageView změní na konec intervalu trvání zobrazení stránky při volání trackPageView. Pokud je hodnota false a pro trackPageView není k dispozici žádná vlastní doba trvání, vypočítává se výkon zobrazení stránky pomocí rozhraní API pro navigaci. Výchozí hodnota je false. |
| maxAjaxCallsPerView | 500 | Výchozí 500 – určuje, kolik volání AJAX bude monitorováno na zobrazení stránky. Nastavte na hodnotu-1, pokud chcete monitorovat všechna (neomezená) volání AJAX na stránce. |
| disableDataLossAnalysis | true | Pokud je hodnota false, budou se při spuštění kontrolovat vyrovnávací paměti pro interní telemetrie pro položky, které ještě nebyly odeslány. |
| disableCorrelationHeaders | false (nepravda) | V případě hodnoty false přidá sada SDK dvě hlavičky ("Request-ID" a "Request-Context") pro všechny požadavky závislosti, které je korelují s odpovídajícími požadavky na straně serveru. Výchozí hodnota je false. |
| correlationHeaderExcludedDomains |  | Zakázat korelační hlavičky pro konkrétní domény |
| correlationHeaderDomains |  | Povolit korelační hlavičky pro konkrétní domény |
| disableFlushOnBeforeUnload | false (nepravda) | Výchozí hodnota je false. Je-li nastavena hodnota true, metoda flush nebude volána při triggerech události onBeforeUnload |
| enableSessionStorageBuffer | true | Výchozí hodnota je true. Pokud je nastaveno na true, uloží se do úložiště relace vyrovnávací paměť s veškerou neodeslanou telemetrie. Vyrovnávací paměť se při načtení stránky obnoví. |
| isCookieUseDisabled | false (nepravda) | Výchozí hodnota je false. Pokud má hodnotu true, SDK nebude ukládat ani číst žádná data z souborů cookie.|
| cookieDomain | null | Vlastní doména souborů cookie. To je užitečné, pokud chcete sdílet Application Insights soubory cookie mezi subdoménami. |
| isRetryDisabled | false (nepravda) | Výchozí hodnota je false. Pokud je hodnota false, zkuste to znovu v 206 (částečný úspěch), 408 (timeout), 429 (příliš mnoho požadavků), 500 (interní chyba serveru), 503 (služba není dostupná) a 0 (offline, jenom pokud se zjistilo). |
| isStorageUseDisabled | false (nepravda) | Pokud má hodnotu true, SDK nebude ukládat ani číst žádná data z místního úložiště a úložiště relací. Výchozí hodnota je false. |
| isBeaconApiDisabled | true | Pokud má hodnotu false, SDK pošle veškerou telemetrii pomocí [rozhraní Beacon API](https://www.w3.org/TR/beacon) . |
| onunloadDisableBeacon | false (nepravda) | Výchozí hodnota je false. Když se karta zavře, SDK pošle veškerou zbývající telemetrii pomocí [rozhraní API pro maják](https://www.w3.org/TR/beacon) . |
| sdkExtension | null | Nastaví název rozšíření sady SDK. Jsou povoleny pouze abecední znaky. Název rozšíření se přidá jako předpona do značky AI. Internal. sdkVersion (například ' ext_javascript: 2.0.0 '). Výchozí hodnota je null. |
| isBrowserLinkTrackingEnabled | false (nepravda) | Výchozí hodnota je false. Pokud má hodnotu true, SDK bude sledovat všechny požadavky na [propojení prohlížeče](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) . |
| appId | null | AppId se používá pro korelaci mezi závislostmi AJAX, které probíhají na straně klienta s požadavky na straně serveru. Pokud je zapnuté rozhraní API pro signalizaci, nedá se použít automaticky, ale v konfiguraci je možné ho nastavit ručně. Výchozí hodnota je null. |
| enableCorsCorrelation | false (nepravda) | V případě hodnoty true SDK přidá dvě hlavičky (' Request-ID ' a ' Request-Context ') do všech požadavků CORS ke sladění odchozích závislostí AJAX s odpovídajícími požadavky na straně serveru. Výchozí hodnota je false. |
| namePrefix | nedefinované | Volitelná hodnota, která bude použita jako přípona názvu pro localStorage a název souboru cookie.
| enableAutoRouteTracking | false (nepravda) | Automatické sledování změn směrování v aplikacích s jednou stránkou (SPA). Pokud má hodnotu true, každá změna trasy pošle nové PageView Application Insights. Změny trasy algoritmu`example.com/foo#bar`hash () jsou také zaznamenávány jako nová zobrazení stránky.
| enableRequestHeaderTracking | false (nepravda) | Pokud je nastaveno na true, jsou sledovány hlavičky požadavku Fetch & AJAX, výchozí hodnota je false.
| enableResponseHeaderTracking | false (nepravda) | Pokud je nastaveno na true, jsou sledovány hlavičky odpovědi požadavku načítající & AJAX, výchozí hodnota je false.
| distributedTracingMode | `DistributedTracingModes.AI` | Nastaví režim distribuovaného trasování. Pokud je nastaven režim AI_AND_W3C nebo W3C, budou se vygenerovat hlavičky kontextu trasování W3C (traceparent/tracestate) a budou zahrnuty do všech odchozích požadavků. AI_AND_W3C se poskytuje kvůli zpětné kompatibilitě se všemi staršími službami Application Insights instrumentované služby.

## <a name="single-page-applications"></a>Jednostránkové aplikace

Ve výchozím nastavení tato sada SDK **nezpracovává změny** směrování na základě stavu, ke kterým dochází v aplikacích s jednou stránkou. Pokud chcete povolit automatické sledování změn směrování pro jednu stránkovou aplikaci, můžete přidat `enableAutoRouteTracking: true` do konfigurace instalace.

V současné době nabízíme samostatný [modul plug-in pro reakce](#react-extensions), který můžete inicializovat pomocí této sady SDK. Bude také plnit sledování změn směrování pro vás a shromažďovat [Další reakce na konkrétní telemetrii](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

> [!NOTE]
> Použijte `enableAutoRouteTracking: true` pouze v případě, že **nepoužíváte** modul plug-in reakce. Obě jsou schopné posílat nové PageViews při změně trasy. Pokud jsou obě povolené, může se odeslat duplicitní PageViews.

## <a name="configuration-autotrackpagevisittime"></a>Konfigurace: autoTrackPageVisitTime

Nastavením `autoTrackPageVisitTime: true`se sleduje čas strávený uživateli na každé stránce. V každém novém PageView je doba, kterou uživatel strávil na *Předchozí* stránce, odeslána jako [vlastní metrika](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview) s názvem `PageVisitTime`. Tato vlastní metrika je zobrazitelná v [Průzkumník metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) jako metrika založená na protokolu.

## <a name="react-extensions"></a>Rozšíření reakce

| Rozšíření |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagovat nativní](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Prozkoumat data v prohlížeči nebo na straně klienta

Data v prohlížeči nebo na straně klienta si můžete prohlédnout na základě **metrik** a přidat jednotlivé metriky, které vás zajímají:

![](./media/javascript/page-view-load-time.png)

Data můžete také zobrazit ze sady JavaScript SDK prostřednictvím prostředí prohlížeče na portálu.

Vyberte **prohlížeč** a pak zvolte **selhání** nebo **výkon**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Výkon

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Závislosti

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analýzy

Chcete-li zadat dotaz na telemetrii shromážděnou sadou JavaScript SDK, vyberte tlačítko **Zobrazit v protokolech (Analytics)** . Přidáním `where` příkazu `client_Type == "Browser"`se zobrazí pouze data z sady JavaScript SDK a všechny telemetrie na straně serveru shromážděné jinými sadami SDK budou vyloučeny.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Podpora zdrojového mapování

Minifikovaného zásobník volání telemetrie výjimek může být unminified v Azure Portal. Všechna existující integrace na panelu podrobností výjimky budou fungovat s nově unminified zásobník volání.

#### <a name="link-to-blob-storage-account"></a>Odkaz na účet úložiště BLOB

Prostředek Application Insights můžete propojit s vlastním kontejnerem Azure Blob Storage a automaticky zrušení minifikace zásobníky volání. Informace o tom, jak začít, najdete v článku [podpora automatického mapování zdrojů](./source-map-support.md).

### <a name="drag-and-drop"></a>Přetažení

1. Vyberte položku telemetrie výjimek v Azure Portal, abyste zobrazili její "Podrobnosti o koncových transakcích".
2. Určete, která zdrojová mapování odpovídají tomuto zásobníku volání. Zdrojová mapa musí odpovídat zdrojovému souboru rámce zásobníku, ale má příponu.`.map`
3. Přetáhněte zdrojové mapy do zásobníku volání v Azure Portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights web Basic

Pro zjednodušené prostředí můžete místo toho nainstalovat základní verzi Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Tato verze je dodávána s minimálním počtem funkcí a funkcemi, které vám závisí na jejich sestavování podle potřeby. Například neprovede žádnou Autocollection (nezachycené výjimky, AJAX atd.). Tato verze neobsahuje rozhraní API pro odesílání určitých typů `trackTrace`telemetrie `trackException`, například, atd., takže budete muset zadat vlastní obálku. K dispozici je `track`jediné rozhraní API. Tady se nachází [Ukázka](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Příklady

Příklady spustitelný naleznete v tématu [Application Insights JavaScript SDK Samples](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade ze starší verze Application Insights

Přerušující se změny v verzi sady SDK v2:
- Pro lepší signatury rozhraní API se některá volání rozhraní API, například trackPageView a trackException, aktualizovala. Spuštění v aplikaci Internet Explorer 8 a starších verzích prohlížeče se nepodporuje.
- Obálka telemetrie má název pole a strukturu, které se mění kvůli aktualizacím schématu dat.
- `context.operation` Přesunuto `context.telemetryTrace`do. Některá pole se také změnila`operation.id` --> `telemetryTrace.traceID`().
  - Chcete-li ručně aktualizovat aktuální ID PageView (například v aplikacích SPA), použijte `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Aby ID trasování bylo jedinečné, kde jste předtím používali `Util.newId()`, teď použijte `Util.generateW3CId()`. Oba nakonec mají ID operace.

Pokud používáte aktuální sadu SDK 1.0.20 (Application Insights produkční SDK) a chcete zjistit, jestli nová sada SDK funguje v modulu runtime, aktualizujte adresu URL v závislosti na vašem aktuálním scénáři načítání sady SDK.

- Stáhnout přes CDN scénář: aktualizujte fragment kódu, který aktuálně používáte k odkazování na následující adresu URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- scénář npm: volání `downloadAndSetup` ke stažení úplného skriptu APPLICATIONINSIGHTS z CDN a jeho inicializaci pomocí klíče instrumentace:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test v interním prostředí, aby se ověřilo, že telemetrie monitorování funguje podle očekávání. Pokud vše funguje, aktualizujte signatury rozhraní API odpovídajícím způsobem verze sady SDK v2 a nasaďte je v produkčním prostředí.

## <a name="sdk-performanceoverhead"></a>Výkon nebo režie sady SDK

V pouhých 25 KB komprimovaný jako gzip a při inicializaci jenom ~ 15 MS, Application Insights na web přidá zanedbatelné množství loadtime. Pomocí fragmentu kódu se rychle načtou minimální součásti knihovny. Do té doby se celý skript stáhne na pozadí.

I když se skript stahuje ze sítě CDN, veškeré sledování vaší stránky je ve frontě. Po dokončení asynchronní inicializace staženého skriptu budou sledovány všechny události, které byly zařazeny do fronty. V důsledku toho nedojde ke ztrátě žádné telemetrie během celého životního cyklu stránky. Tento proces instalace poskytuje stránku se systémovou analýzou, která je pro vaše uživatele neviditelná.

> Souhrn:
> - **25 KB** komprimovaný jako gzip
> - Celkový čas inicializace na **15 MS**
> - V průběhu životního cyklu stránky nebylo vynecháno **žádné** sledování.

## <a name="browser-support"></a>Podpora prohlížečů

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Nejnovější ✔ pro Chrome |  Nejnovější ✔ Firefox | Internet Explorer 9 + & Edge ✔ | Nejnovější ✔ Opera | Nejnovější ✔ Safari |

## <a name="open-source-sdk"></a>Open-Source sada SDK

Sada Application Insights JavaScript SDK je open source, aby zobrazila zdrojový kód nebo přispívala k projektu na [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Další kroky
* [Sledování využití](usage-overview.md)
* [Vlastní události a metriky](api-custom-events-metrics.md)
* [Sestavení vyhodnocení poučení](usage-overview.md)
