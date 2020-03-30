---
title: Azure Application Insights pro webové aplikace JavaScript
description: Získejte zobrazení stránky a počty relací, data webového klienta, jednostránkové aplikace (SPA) a sledujte vzorce využití. Zjištění výjimek a problémů s výkonem na webových stránkách v jazyce JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276072"
---
# <a name="application-insights-for-web-pages"></a>Application Insights pro webové stránky

Zjistěte informace o výkonu a využití webové stránky nebo aplikace. Pokud do stránkového skriptu přidáte [Application Insights,](app-insights-overview.md) získáte časování načítání stránky a volání AJAX, počty a podrobnosti o výjimkách prohlížeče a selháních AJAX, stejně jako počty uživatelů a relací. Všechny tyto hodnoty mohou být segmentovány podle stránky, klientského operačního systému a verze prohlížeče, zeměpisné polohy a ostatních dimenzí. Můžete nastavit výstrahy na počet selhání nebo pomalé načítání stránky. A vložíte-li do kódu JavaScript trasování volání, můžete sledovat využití různých funkcí aplikace webové stránky.

Application Insights můžete použít s jakýmikoli webovými stránkami – stačí přidat krátký kód jazyka JavaScript. Pokud je vaše webová služba [Java](java-get-started.md) nebo [ASP.NET](asp-net.md), můžete pomocí sad SDK na straně serveru ve spojení s sadou JavaScript SDK na straně klienta získat komplexní znalosti výkonu vaší aplikace.

## <a name="adding-the-javascript-sdk"></a>Přidání sady JavaScript SDK

1. Nejprve potřebujete prostředek Application Insights. Pokud ještě nemáte klíč zdroje a instrumentace, postupujte podle [pokynů k vytvoření nového zdroje](create-new-resource.md).
2. Zkopírujte klíč instrumentace ze zdroje, kam chcete odeslat telemetrii JavaScriptu.
3. Přidejte javascriptovou sadu Application Insights na webovou stránku nebo aplikaci pomocí jedné z následujících dvou možností:
    * [nastavení npm](#npm-based-setup)
    * [Úryvek javascriptu](#snippet-based-setup)

> [!IMPORTANT]
> K přidání sady JavaScript SDK do aplikace použijte pouze jednu metodu. Pokud používáte nastavení NPM, nepoužívejte úryvek a naopak.

> [!NOTE]
> Instalační program NPM nainstaluje sadu JavaScript SDK jako závislost na projektu a povolí program IntelliSense, zatímco výstřižk načte sdk za běhu. Obě podporují stejné funkce. Vývojáři, kteří si přejí více vlastních událostí a konfigurace, se však obecně rozhodnou pro nastavení NPM, zatímco uživatelé, kteří hledají rychlé povolení out-of-the-box webové analýzy rozhodnou pro úryvek.

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

### <a name="snippet-based-setup"></a>Nastavení založené na úryvcích

Pokud vaše aplikace nepoužívá npm, můžete přímo instrumentovat webové stránky pomocí Application Insights vložením tohoto fragmentu do horní části každé stránky. Pokud možno by měl být první `<head>` skript ve vaší části tak, aby mohl sledovat všechny potenciální problémy se všemi vaše závislosti. Pokud používáte aplikaci Blazor Server, přidejte fragment v `_Host.cshtml` horní `<head>` části souboru v sekci.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Odesílání telemetrie na portál Azure

Ve výchozím nastavení sada Application Insights JavaScript SDK automaticky shromažďuje řadu položek telemetrie, které jsou užitečné při určování stavu vaší aplikace a základního uživatelského prostředí. Mezi ně patří:

- **Nezachycené výjimky** ve vaší aplikaci, včetně informací o
    - Trasování zásobníku
    - Podrobnosti o výjimce a zpráva doprovázející chybu
    - Řádek & číslo sloupce chyby
    - Adresa URL, kde byla vyvolána chyba
- **Požadavky na síťové závislosti** provedené vaší aplikací **XHR** a **Fetch** (kolekce načítání je ve výchozím nastavení zakázána), obsahují informace o
    - Adresa URL zdroje závislostí
    - Metoda & příkazu používaná k vyžádání závislosti
    - Doba trvání žádosti
    - Kód výsledku a stav úspěchu požadavku
    - ID (pokud existuje) uživatele, který žádost posnese
    - Korelační kontext (pokud existuje) v případě, že je žádost podána
- **Informace o uživateli** (například Umístění, síť, IP)
- **Informace o zařízení** (například prohlížeč, operační systém, verze, jazyk, rozlišení, model)
- **informace o relaci**,

### <a name="telemetry-initializers"></a>Inicializátory telemetrie
Inicializátory telemetrie se používají k úpravě obsahu shromážděné telemetrie před odesláním z prohlížeče uživatele. Mohou být také použity k zabránění odeslání určité `false`telemetrie vrácením . Do instance Application Insights lze přidat více inicializátorů telemetrie a jsou spouštěny v pořadí jejich přidání.

Vstupní argument `addTelemetryInitializer` je zpětné volání, [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) které bere jako `boolean` argument `void`a vrátí nebo . Pokud vrací `false`, položka telemetrie není odeslána, jinak pokračuje na další telemetrie inicializátor, pokud existuje, nebo je odeslána do koncového bodu kolekce telemetrie.

Příklad použití telemetrických inicializátorů:
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
Většina konfiguračních polí je pojmenována tak, že mohou být výchozí pro false. Všechna pole jsou `instrumentationKey`nepovinná kromě .

| Name (Název) | Výchozí | Popis |
|------|---------|-------------|
| instrumentaceKlíč | null | **Požadováno**<br>Instrumentace klíč, který jste získali z portálu Azure. |
| accountId | null | Volitelné ID účtu, pokud vaše aplikace seskupuje uživatele do účtů. Žádné mezery, čárky, středníky, rovnoprávné nebo svislé pruhy |
| sessionRenewalMs | 1800000 | Relace je zaznamenána, pokud je uživatel neaktivní pro tuto dobu v milisekundách. Výchozí hodnota je 30 minut |
| sessionExpirationMs | 86400000 | Relace je zaznamenána, pokud pokračovala po tuto dobu v milisekundách. Výchozí hodnota je 24 hodin |
| maxBatchSizeInBytes | 10000 | Maximální velikost dávky telemetrie. Pokud dávka tento limit překročí, je okamžitě odeslána a spuštěna nová dávka. |
| maxBatchInterval | 15 000 | Jak dlouho dávkovat telemetrická data před odesláním (milisekundy) |
| zakázatprogramování výjimek | false (nepravda) | Pokud true, výjimky nejsou automaticky shromažďovány. Výchozí hodnota je false. |
| disableTelemetrie | false (nepravda) | Pokud true, telemetrie není shromažďována nebo odeslána. Výchozí hodnota je false. |
| povolitLadění | false (nepravda) | Pokud true, **interní** ladění dat je vyvolána jako výjimka **namísto** protokolu, bez ohledu na nastavení protokolování sady SDK. Výchozí hodnota je false. <br>***Poznámka:*** Povolení tohoto nastavení bude mít za následek vynechání telemetrie vždy, když dojde k vnitřní chybě. To může být užitečné pro rychlou identifikaci problémů s konfigurací nebo používáním sady SDK. Pokud nechcete ztratit telemetrii při ladění, `consoleLoggingLevel` zvažte použití nebo `telemetryLoggingLevel` místo `enableDebug`. |
| loggingLevelConsole | 0 | Protokoluje **interní** chyby Application Insights do konzoly. <br>0: vypnuto, <br>1: pouze kritické chyby, <br>2: Vše (chyby & varování) |
| logLevelTelemetrie | 1 | Odešle **interní** chyby Application Insights jako telemetrie. <br>0: vypnuto, <br>1: pouze kritické chyby, <br>2: Vše (chyby & varování) |
| diagnosticLogInterval | 10000 | (interní) Interval dotazování (v ms) pro interní frontu protokolování |
| odběr vzorkůProcento | 100 | Procento událostí, které budou odeslány. Výchozí hodnota je 100, což znamená, že jsou odesílány všechny události. Tuto otázku nastavte, pokud chcete zachovat limit dat pro rozsáhlé aplikace. |
| autoTrackPageVisitTime | false (nepravda) | Pokud true, v zobrazení stránky předchozí instrumentované stránky je sledována a odeslána jako telemetrie a nový časovač je spuštěn pro aktuální zobrazení stránky. Výchozí hodnota je false. |
| zakázatAjaxTracking | false (nepravda) | Pokud true, Ajax volání nejsou automaticky shromažďovány. Výchozí hodnota je false. |
| zakázatfunkce FetchTracking | true | Pokud true, načíst požadavky nejsou automaticky shromažďovány. Výchozí hodnota je true |
| přepsatzobrazení zobrazení pageduration | false (nepravda) | Pokud true, výchozí chování trackPageView se změní na záznam intervalu trvání zobrazení stránky při volání trackPageView. Pokud false a žádná vlastní doba trvání je k dispozici trackPageView, výkon zobrazení stránky se vypočítá pomocí rozhraní API časování navigace. Výchozí hodnota je false. |
| maxAjaxCallsPerView | 500 | Výchozí 500 - určuje, kolik ajax volání bude sledováno na stránce zobrazení. Nastavte na -1 pro sledování všech (neomezených) volání Ajax na stránce. |
| zakázatanalýzu Ztráty dat | true | Pokud false, vnitřní telemetrické odevzdání vyrovnávacích pamětí bude kontrolována při spuštění pro položky, které ještě nebyly odeslány. |
| disableCorrelationHeaders | false (nepravda) | Pokud false, sada SDK přidá dvě záhlaví ('Request-Id' a 'Request-Context') na všechny požadavky na závislost korelovat s odpovídajícími požadavky na straně serveru. Výchozí hodnota je false. |
| correlationHeaderExcludedDomains |  | Zakázat záhlaví korelace pro určité domény |
| correlationHeaderDomains |  | Povolení záhlaví korelace pro určité domény |
| zakázatFlushOnBeforeUnload | false (nepravda) | Výchozí false. Pokud true, flush metoda nebude volána, když onBeforeUnload aktivační události aktivační události |
| enableSessionBuffer | true | Výchozí hodnota true. Pokud true, vyrovnávací paměti se všemi neodeslané telemetrie je uložen v úložišti relace. Vyrovnávací paměť je obnovena při načítání stránky |
| isCookieUseDisabled | false (nepravda) | Výchozí false. Pokud true, SDK nebude ukládat nebo číst žádná data ze souborů cookie.|
| doména cookie | null | Vlastní doména souborů cookie. To je užitečné, pokud chcete sdílet soubory cookie Application Insights mezi subdoménami. |
| isRetryDisabled (RetryDisabled) | false (nepravda) | Výchozí false. Pokud false, opakujte na 206 (částečný úspěch), 408 (časový plán), 429 (příliš mnoho požadavků), 500 (chyba interního serveru), 503 (služba není k dispozici) a 0 (offline, pouze pokud je zjištěna) |
| isStorageUseDisabled | false (nepravda) | Pokud true, sada SDK nebude ukládat ani číst žádná data z místního úložiště a úložiště relace. Výchozí hodnota je false. |
| isBeaconApiDisabled | true | Pokud false, SDK odešle všechny telemetrie pomocí [Rozhraní API Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false (nepravda) | Výchozí false. když je karta zavřená, sada SDK odešle všechny zbývající telemetrie pomocí [rozhraní BEACON API](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Nastaví název rozšíření sady SDK. Povoleny jsou pouze abecední znaky. Název rozšíření je přidán jako předpona ke značce 'ai.internal.sdkVersion' (například "ext_javascript:2.0.0"). Výchozí hodnota je null. |
| isBrowserLinkTrackingEnabled | false (nepravda) | Výchozí hodnota je false. Pokud true, sdk bude sledovat všechny požadavky [odkazu prohlížeče.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | null | AppId se používá pro korelaci mezi závislostí AJAX děje na straně klienta s požadavky na straně serveru. Pokud je povoleno rozhraní BEACON API, nelze jej použít automaticky, ale lze jej nastavit ručně v konfiguraci. Výchozí hodnota je null. |
| enableCorsCorrelation | false (nepravda) | Pokud true, Sada SDK přidá dvě záhlaví ('Request-Id' a 'Request-Context') ke všem požadavkům CORS korelovat odchozí závislosti AJAX s odpovídajícími požadavky na straně serveru. Výchozí hodnota je false |
| názevPrefix | Nedefinované | Volitelná hodnota, která bude použita jako přípona názvu pro localStorage a název souboru cookie.
| enableAutoRouteTracking | false (nepravda) | Automaticky sledovat změny trasy v jednostránkových aplikacích (SPA). Pokud true, každá změna trasy odešle nové zobrazení stránky do Application Insights. Změny trasy hash (`example.com/foo#bar`) jsou také zaznamenány jako nová zobrazení stránky.
| enableRequestHeaderTracking | false (nepravda) | Pokud true, AJAX & načíst hlavičky požadavku je sledována, výchozí hodnota je false.
| enableResponseHeaderTracking | false (nepravda) | Pokud true, AJAX & načíst hlavičky odpovědí požadavku je sledována, výchozí hodnota je false.
| distributedTracingMode | `DistributedTracingModes.AI` | Nastaví režim distribuovaného trasování. Pokud AI_AND_W3C režim nebo W3C režim je nastaven, W3C záhlaví kontextu trasování (traceparent/tracestate) budou generovány a zahrnuty do všech odchozích požadavků. AI_AND_W3C je k dispozici pro zpětnou kompatibilitu s všechny starší služby s nástroji Application Insights.

## <a name="single-page-applications"></a>Aplikace s jednou stránkou

Ve výchozím nastavení **nebude** tato sada SDK zpracovávat změnu trasy založenou na stavu, ke které dochází v jednostránkových aplikacích. Chcete-li povolit automatické sledování změn trasy `enableAutoRouteTracking: true` pro aplikaci s jednou stránkou, můžete přidat do konfigurace nastavení.

V současné době nabízíme samostatný [react plugin,](#react-extensions) který můžete inicializovat pomocí této sady SDK. Bude také provádět sledování změn trasy pro vás, stejně jako shromažďovat [další reagovat specifické telemetrie](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Reagujte rozšíření

| Rozšíření |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagovat nativní](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Prozkoumání dat prohlížeče/klienta

Data prohlížeče/klienta lze zobrazit tak, že přejdete na **metriky** a přidáte jednotlivé metriky, které vás zajímají:

![](./media/javascript/page-view-load-time.png)

Data ze sady JavaScript SDK můžete také zobrazit prostřednictvím prostředí prohlížeče na portálu.

Vyberte **Prohlížeč** a pak zvolte **Selhání** nebo **Výkon**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Výkon

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Závislosti

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analýza

Chcete-li zadat dotaz na telemetrii shromážděnou sadou JavaScript SDK, vyberte tlačítko **Zobrazení v protokolech (Analytics).** Přidáním `where` příkazu `client_Type == "Browser"`se zobrazí pouze data ze sady JavaScript SDK a všechny telemetrie na straně serveru shromážděné jinými sadami SDK budou vyloučeny.
 
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

### <a name="source-map-support"></a>Podpora zdrojové mapy

Minified volání zásobníku vaší výjimky telemetrie může být unminified na webu Azure Portal. Všechny existující integrace na panelu Podrobnosti o výjimce budou fungovat s nově neinified zásobníku volání.

#### <a name="link-to-blob-storage-account"></a>Odkaz na účet úložiště objektů Blob

Prostředek Application Insights můžete propojit s vlastním kontejnerem úložiště objektů blob Azure a automaticky zrušit tak zásobníky volání. Chcete-li začít, přečtěte si informace o [podpoře automatické zdrojové mapy](./source-map-support.md).

### <a name="drag-and-drop"></a>Přetažení

1. Vyberte položku telemetrie výjimek na portálu Azure a zobrazte její podrobnosti o transakcích od konce.
2. Určete, které zdrojové mapy odpovídají tomuto zásobníku volání. Zdrojová mapa musí odpovídat zdrojovému souboru rámce zásobníku, ale musí být`.map`
3. Přetažení zdrojových map do zásobníku volání na webu Azure Portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Aplikace Insights Web Basic

Pro zjednodušené prostředí můžete místo toho nainstalovat základní verzi Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Tato verze je dodávána s minimálním počtem funkcí a funkcí a spoléhá se na vás, abyste ji vytvořili, jak uznáte za vhodné. Například neprovádí žádnou autocollection (uncaught výjimky, AJAX, atd.). Api pro odesílání určitých typů `trackTrace`telemetrie, jako je , `trackException`, atd., nejsou zahrnuty v této verzi, takže budete muset poskytnout vlastní obálku. Jediné rozhraní API, `track`které je k dispozici, je . [Ukázka](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) je umístěna zde.

## <a name="examples"></a>Příklady

Příklady runnable najdete v [tématu Application Insights JavaScript SDK Samples](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade ze staré verze Application Insights

Nejnovější změny ve verzi sady SDK V2:
- Chcete-li povolit lepší podpisy rozhraní API, některé volání rozhraní API, jako je například trackPageView a trackException, byly aktualizovány. Spuštění v aplikaci Internet Explorer 8 a starších verzích prohlížeče není podporováno.
- Obálka telemetrie obsahuje změny názvu pole a struktury z důvodu aktualizací schématu dat.
- `context.operation` Přesunuto `context.telemetryTrace`do . Některá pole byla`operation.id` --> `telemetryTrace.traceID`také změněna ( ).
  - Chcete-li ručně aktualizovat aktuální ID zobrazení stránky (například v aplikacích SPA), použijte `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Chcete-li zachovat id trasování `Util.newId()`jedinečné, `Util.generateW3CId()`kde jste dříve používali , použijte nyní . Oba nakonec skončí id operace.

Pokud používáte aktuální aplikace přehledy produkční sdk (1.0.20) a chcete zjistit, zda nová sada SDK funguje v době běhu, aktualizujte adresu URL v závislosti na aktuální scénář načítání sady SDK.

- Stáhnout prostřednictvím scénáře CDN: Aktualizujte fragment kódu, který aktuálně používáte, aby překázal na následující adresu URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm scénář: `downloadAndSetup` Volání stáhnout celý ApplicationInsights skript z CDN a inicializovat s instrumentace klíč:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test v interním prostředí k ověření monitorování telemetrie funguje podle očekávání. Pokud vše funguje, aktualizujte podpisy rozhraní API odpovídajícím způsobem na verzi SDK V2 a nasaďte je ve svých produkčních prostředích.

## <a name="sdk-performanceoverhead"></a>Výkon/režie sady SDK

Na pouhých 25 KB gzip, a přičemž pouze ~ 15 ms inicializovat, Application Insights přidává zanedbatelné množství doby načítání na vaše webové stránky. Pomocí výstřižku se rychle načtou minimální součásti knihovny. Do té doby, celý skript je stažen na pozadí.

Během stahování skriptu z sítě CDN je veškeré sledování stránky zařazeno do fronty. Jakmile stažený skript dokončí asynchronně inicializaci, jsou sledovány všechny události, které byly zařazeny do fronty. V důsledku toho neztratíte žádnou telemetrickou během celého životního cyklu stránky. Tento proces nastavení poskytuje vaší stránce bezproblémový analytický systém, který je pro uživatele neviditelný.

> Souhrn:
> - **25 KB** gzip
> - Celkový čas inicializace **15 ms**
> - **Nulové** sledování zmeškané během životního cyklu stránky

## <a name="browser-support"></a>Podpora prohlížečů

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome Nejnovější ✔ |  Firefox Nejnovější ✔ | IE 9+ & edge ✔ | Opera Nejnovější ✔ | Safari Nejnovější ✔ |

## <a name="open-source-sdk"></a>Sada SDK s otevřeným zdrojovým kódem

JavaScript SDK Application Insights je open-source pro zobrazení zdrojového kódu nebo pro přispívání do projektu navštívit [oficiální úložiště GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Další kroky
* [Sledování využití](usage-overview.md)
* [Vlastní události a metriky](api-custom-events-metrics.md)
* [Sestavení vyhodnocení poučení](usage-overview.md)
