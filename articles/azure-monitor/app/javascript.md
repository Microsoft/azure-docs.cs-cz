---
title: Azure Application Insights pro webové aplikace v JavaScriptu
description: Získejte zobrazení stránky a počty relací, data webového klienta, jednostránkové aplikace (SPA) a sledujte vzorce používání. Zjištění výjimek a problémů s výkonem na webových stránkách v jazyce JavaScript.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6678c662c4646a8181b1617ccddf9b8718c957bf
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858548"
---
# <a name="application-insights-for-web-pages"></a>Application Insights pro webové stránky

Zjistěte informace o výkonu a využití webové stránky nebo aplikace. Pokud přidáte [Application Insights](app-insights-overview.md) do skriptu stránky, získáte časování načtení stránky a volání AJAX, počty a podrobnosti výjimek prohlížeče a selhání AJAX a také počty uživatelů a relací. Všechny tyto hodnoty mohou být segmentovány podle stránky, klientského operačního systému a verze prohlížeče, zeměpisné polohy a ostatních dimenzí. Můžete nastavit výstrahy na počet selhání nebo pomalé načítání stránky. A vložíte-li do kódu JavaScript trasování volání, můžete sledovat využití různých funkcí aplikace webové stránky.

Application Insights můžete použít s jakýmikoli webovými stránkami – stačí přidat krátký kód jazyka JavaScript. Pokud je vaše webová služba [Java](java-get-started.md) nebo [ASP.NET](asp-net.md), můžete použít sady SDK na straně serveru ve spojení se sadou JavaScript SDK na straně klienta k získání uceleného porozumění výkonu vaší aplikace.

## <a name="adding-the-javascript-sdk"></a>Přidání sady JavaScript SDK

> [!IMPORTANT]
> Nové oblasti Azure **vyžadují** použití připojovacích řetězců místo klíčů instrumentace. [Připojovací řetězec](./sdk-connection-string.md?tabs=js) identifikuje prostředek, ke kterému chcete přidružit data telemetrie. Umožňuje také upravit koncové body, které prostředek použije jako cíl pro vaši telemetrii. Budete muset zkopírovat připojovací řetězec a přidat ho do kódu aplikace nebo do proměnné prostředí.

1. Nejdřív potřebujete prostředek Application Insights. Pokud ještě nemáte prostředek a klíč instrumentace, postupujte podle [pokynů pro vytvoření nového prostředku](create-new-resource.md).
2. Zkopírujte _klíč instrumentace_ (označovaný také jako "ikey") nebo [připojovací řetězec](#connection-string-setup) pro prostředek, ve kterém chcete odeslat telemetrii JavaScriptu (z kroku 1). Přidáte ho do `instrumentationKey` `connectionString` nastavení nebo Application Insights JavaScript SDK.
3. Přidejte sadu Application Insights JavaScript SDK do své webové stránky nebo aplikace pomocí jedné z následujících dvou možností:
    * [Nastavení npm](#npm-based-setup)
    * [Fragment kódu JavaScriptu](#snippet-based-setup)

> [!IMPORTANT]
> K přidání sady JavaScript SDK do aplikace použijte pouze jednu metodu. Použijete-li instalační program NPM, tento fragment kódu nepoužívejte ani naopak.

> [!NOTE]
> Instalační program NPM nainstaluje sadu JavaScript SDK jako závislost k vašemu projektu a umožní technologii IntelliSense, zatímco fragment kódu načte sadu SDK za běhu. Obě podporují stejné funkce. Vývojáři, kteří chtějí další vlastní události a konfigurace, obecně můžou souhlasit s NPM nastavením, zatímco uživatelé hledají rychlé povolení okamžitého zapnutí webové analýzy pro fragment.

### <a name="npm-based-setup"></a>nastavení založené na npm

Nainstalujte prostřednictvím NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Do tohoto balíčku jsou zahrnutá zápisy**, takže **nemusíte** instalovat samostatný balíček pro psaní.
    
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

Pokud vaše aplikace nepoužívá NPM, můžete své webové stránky přímo instrumentovat pomocí Application Insights vložením tohoto fragmentu do horní části každé stránky. V takovém případě by měl být prvním skriptem v `<head>` oddílu, aby mohl monitorovat případné problémy se všemi vašimi závislostmi a volitelně také chyby JavaScriptu. Pokud používáte aplikaci Blazor Server, přidejte fragment kódu na začátek souboru `_Host.cshtml` v `<head>` části.

Abychom vám pomohli sledovat, kterou verzi fragmentu aplikace používá, počínaje verzí 2.5.5 události zobrazení stránky bude obsahovat novou značku "AI. Internal. fragment", která bude obsahovat identifikovanou verzi fragmentu.

Aktuální fragment kódu (uvedený níže) bude identifikován jako verze "3".

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
//name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
//ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
//useXhr: 1, // Use XHR instead of fetch to report failures (if available),
//crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Z důvodu čitelnosti a snížení možných chyb JavaScriptu jsou všechny možné možnosti konfigurace uvedeny na novém řádku ve výše uvedeném kódu fragmentu, pokud nechcete změnit hodnotu řádku s komentářem, který je možné odebrat.


#### <a name="reporting-script-load-failures"></a>Selhání načtení skriptu vytváření sestav

Tato verze fragmentu detekuje a nahlásí selhání při načítání sady SDK ze sítě CDN jako výjimku z portálu Azure Monitor (v &gt; &gt; prohlížeči výjimek), tato výjimka poskytuje přehled o chybách tohoto typu, abyste se dozvěděli, že vaše aplikace nehlásí telemetrii (nebo jiné výjimky) podle očekávání. Tento signál představuje důležité měření v porozumění, že jste ztratili telemetrii, protože sada SDK nebyla načtena nebo inicializována, což může vést k těmto akcím:
- V části vytváření sestav o tom, jak uživatelé používají (nebo se pokoušejí použít) váš web;
- Chybí telemetrie o tom, jak vaši koncoví uživatelé používají vaši lokalitu.
- Došlo k chybě JavaScriptu, která by mohla potenciálně blokovat koncovým uživatelům v úspěšném použití vašeho webu.

Podrobnosti o této výjimce najdete na stránce věnované odstraňování potíží [při selhání načtení sady SDK](javascript-sdk-load-failure.md) .

Vytváření sestav této chyby jako výjimka z portálu nepoužívá možnost konfigurace ```disableExceptionTracking``` z konfigurace Application Insights, a proto v případě, že k této chybě dojde, bude tento fragment kódu vždy ohlášen i v případě, že je zakázána podpora Window. Error.

Generování sestav selhání načtení sady SDK se konkrétně nepodporuje v IE 8 (nebo méně). To pomáhá snižovat velikost minifikovaného fragmentu za předpokladu, že většina prostředí není výhradně IE 8 nebo méně. Pokud máte tento požadavek a chcete přijmout tyto výjimky, budete potřebovat buď vyplnit Poly naplnit, nebo vytvořit vlastní verzi fragmentu, která používá ```XDomainRequest``` místo ```XMLHttpRequest``` , doporučujeme použít [poskytnutý zdrojový kód fragmentu](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) jako výchozí bod.

> [!NOTE]
> Pokud používáte předchozí verzi fragmentu, důrazně doporučujeme, abyste aktualizovali na nejnovější verzi, abyste mohli obdržet tyto dříve neohlášené problémy.

#### <a name="snippet-configuration-options"></a>Možnosti konfigurace fragmentu

Všechny možnosti konfigurace se teď přesunuly na konec skriptu, aby se zabránilo nechtěnému zavlečení chyb JavaScriptu, které nezpůsobí načtení sady SDK, ale zároveň by tím bylo zakázané hlášení o selhání.

Každá možnost konfigurace je uvedená výše na novém řádku, pokud nechcete přepsat výchozí hodnotu položky uvedené jako [volitelné], můžete odebrat tuto čáru, abyste minimalizovali výslednou velikost vrácené stránky.

Dostupné možnosti konfigurace jsou

| Název | Typ | Popis
|------|------|----------------
| src | řetězec **[povinné]** | Úplná adresa URL, ze které se má načíst sada SDK Tato hodnota se používá pro atribut src dynamicky přidávaného &lt; skriptu nebo &gt; značky. Můžete použít veřejné umístění CDN nebo vlastní soukromý hostovaný.
| name | řetězec *[nepovinné]* | Globální název inicializované sady SDK, výchozí hodnota `appInsights` . Proto ```window.appInsights``` bude odkaz na inicializovaná instanci. Poznámka: Pokud zadáte hodnotu názvu nebo předchozí instanci, která má být přiřazena (prostřednictvím globálního názvu appInsightsSDK), bude tato hodnota názvu také definována v globálním oboru názvů jako ```window.appInsightsSDK=<name value>``` , to je vyžadováno inicializačním kódem sady SDK, aby bylo zajištěno, že se inicializuje a aktualizuje správné kostry fragmentů a metod proxy.
| ld | číslo v MS *[nepovinné]* | Definuje zpoždění zátěže, které se má počkat, než se pokusí načíst sadu SDK. Výchozí hodnota je 0ms a jakákoliv záporná hodnota okamžitě Přidá značku skriptu do &lt; hlavní &gt; oblasti stránky. tím se pak zablokuje událost načtení stránky, dokud není načten skript (nebo selže).
| useXhr | logická hodnota *[nepovinné]* | Toto nastavení se používá pouze při selhání načtení sady SDK pro generování sestav. Vytváření sestav se nejprve pokusí použít metodu Fetch (), je-li k dispozici a pak přechod na XHR, nastavení této hodnoty na hodnotu true pouze obchází kontrolu načtení. Použití této hodnoty se vyžaduje jenom v případě, že se vaše aplikace používá v prostředí, kde se při načítání nepodaří odeslat události selhání.
| crossOrigin | řetězec *[nepovinné]* | Zahrnutím tohoto nastavení se značka skriptu přidaná ke stažení SDK bude týkat atributu crossOrigin s touto řetězcovou hodnotou. Pokud není definován (výchozí), není přidán žádný atribut crossOrigin. Doporučené hodnoty nejsou definovány (výchozí nastavení); ""; nebo "anonymní" (pro všechny platné hodnoty viz [atribut HTML: `crossorigin` ](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) dokumentace)
| Priorita | objekt **[povinné]** | Konfigurace předaná do sady Application Insights SDK během inicializace.

### <a name="connection-string-setup"></a>Nastavení připojovacího řetězce

Pro nastavení NPM nebo fragmentu kódu můžete také nakonfigurovat instanci Application Insights pomocí připojovacího řetězce. Jednoduše nahraďte `instrumentationKey` pole `connectionString` polem.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
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
Inicializátory telemetrie slouží k úpravě obsahu shromážděné telemetrie před jejich odesláním z prohlížeče uživatele. Můžete je také použít k zabránění odeslání určité telemetrie, a to vrácením `false` . Do instance Application Insights lze přidat více inicializátorů telemetrie a jsou spouštěny v pořadí jejich přidávání.

Vstupní argument pro `addTelemetryInitializer` je zpětné volání, které přijímá [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) jako argument a vrací `boolean` nebo `void` . Při vrácení se `false` položka telemetrie nepošle, jinak pokračuje k dalšímu inicializátoru telemetrie, pokud existuje, nebo se pošle do koncového bodu kolekce telemetrie.

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
Většina polí konfigurace je pojmenována tak, aby mohla být nastavená na hodnotu false. Všechna pole jsou volitelná s výjimkou `instrumentationKey` .

| Název | Výchozí | Popis |
|------|---------|-------------|
| instrumentationKey | null | **Povinné**<br>Klíč instrumentace, který jste získali z Azure Portal. |
| accountId | null | Volitelné ID účtu, pokud vaše aplikace seskupí uživatele na účty. Žádné mezery, čárky, středníky, rovny nebo svislé čáry |
| sessionRenewalMs | 1800000 | Pokud je uživatel neaktivní po dobu v milisekundách, dojde k zaznamenání relace. Výchozí hodnota je 30 minut. |
| sessionExpirationMs | 86400000 | Relace je zaznamenána v případě, že v milisekundách pokračuje po dobu. Výchozí hodnota je 24 hodin. |
| maxBatchSizeInBytes | 10000 | Maximální velikost dávky telemetrie Pokud dávka tento limit překročí, pošle se hned a spustí se nová dávka. |
| maxBatchInterval | 15 000 | Doba, po kterou se má telemetrie v dávce před odesláním (milisekundy) |
| disableExceptionTracking | false (nepravda) | Je-li nastavena hodnota true, výjimky se nebudou shromažďovat. Výchozí hodnota je false. |
| disableTelemetry | false (nepravda) | Pokud je nastaveno na true, telemetrie se neshromažďuje ani neposílá. Výchozí hodnota je false. |
| enableDebug | false (nepravda) | Při hodnotě true se **interní** data ladění vydávají jako výjimka **namísto** zaznamenávání bez ohledu na nastavení protokolování SDK. Výchozí hodnota je false. <br>**_Poznámka:_* _ povolení tohoto nastavení bude mít za následek vyřazení telemetrie při každém výskytu vnitřní chyby. To může být užitečné, pokud chcete rychle identifikovat problémy s konfigurací nebo využitím sady SDK. Pokud nechcete při ladění přijít o telemetrii, zvažte použití `consoleLoggingLevel` nebo `telemetryLoggingLevel` místo `enableDebug` . |
| loggingLevelConsole | 0 | Protokoluje *interní** Application Insights chyby do konzoly. <br>0: vypnuto, <br>1: jenom kritické chyby, <br>2: vše (chyby & upozornění) |
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
| isBrowserLinkTrackingEnabled | false (nepravda) | Výchozí hodnota je false. Pokud má hodnotu true, SDK bude sledovat všechny požadavky na [propojení prohlížeče](/aspnet/core/client-side/using-browserlink) . |
| appId | null | AppId se používá pro korelaci mezi závislostmi AJAX, které probíhají na straně klienta s požadavky na straně serveru. Pokud je zapnuté rozhraní API pro signalizaci, nedá se použít automaticky, ale v konfiguraci je možné ho nastavit ručně. Výchozí hodnota je null. |
| enableCorsCorrelation | false (nepravda) | V případě hodnoty true SDK přidá dvě hlavičky (' Request-ID ' a ' Request-Context ') do všech požadavků CORS ke sladění odchozích závislostí AJAX s odpovídajícími požadavky na straně serveru. Výchozí hodnota je false. |
| namePrefix | nedefinované | Volitelná hodnota, která bude použita jako přípona názvu pro localStorage a název souboru cookie.
| enableAutoRouteTracking | false (nepravda) | Automatické sledování změn směrování v aplikacích s jednou stránkou (SPA). Pokud má hodnotu true, každá změna trasy pošle nové PageView Application Insights. Změny trasy algoritmu hash ( `example.com/foo#bar` ) jsou také zaznamenávány jako nová zobrazení stránky.
| enableRequestHeaderTracking | false (nepravda) | Pokud je nastaveno na true, jsou sledovány hlavičky požadavku Fetch & AJAX, výchozí hodnota je false.
| enableResponseHeaderTracking | false (nepravda) | Pokud je nastaveno na true, jsou sledovány hlavičky odpovědi požadavku načítající & AJAX, výchozí hodnota je false.
| distributedTracingMode | `DistributedTracingModes.AI` | Nastaví režim distribuovaného trasování. Pokud je nastaven režim AI_AND_W3C nebo W3C, budou se vygenerovat hlavičky kontextu trasování W3C (traceparent/tracestate) a budou zahrnuty do všech odchozích požadavků. AI_AND_W3C se poskytuje kvůli zpětné kompatibilitě se všemi staršími službami Application Insights instrumentované služby. Viz příklad [zde](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).
| enableAjaxErrorStatusText | false (nepravda) | Výchozí hodnota je false. Pokud má hodnotu true, zahrňte text chyby odezvy v události závislosti u neúspěšných požadavků AJAX.
| enableAjaxPerfTracking | false (nepravda) | Výchozí hodnota je false. Příznak pro povolení vyhledávání a zahrnutí dalšího okna prohlížeče. časování výkonu v hlášené `ajax` (XHR a Fetch) ohlásilo metriky.
| maxAjaxPerfLookupAttempts | 3 | Výchozí hodnota je 3. Maximální počet pokusů, kolikrát se má okno Hledat. časování výkonu (Pokud je k dispozici) je vyžadováno, protože ne všechny prohlížeče naplní okno. výkon před ohlášením konce žádosti XHR a pro žádosti o načtení, které jsou přidány po dokončení.
| ajaxPerfLookupDelay | 25 | Výchozí hodnota je 25 ms. Doba, po kterou se má počkat, než se znovu pokusí najít Windows. časování výkonu pro `ajax` požadavek, čas je v milisekundách a předává se přímo do setTimeout ().
| enableUnhandledPromiseRejectionTracking | false (nepravda) | V případě hodnoty true budou se Neošetřená zamítnutí slíbit shromažďovat a nahlásí se jako chyba JavaScriptu. Pokud má disableExceptionTracking hodnotu true (nesleduje výjimky), konfigurační hodnota se bude ignorovat a Neošetřená zamítnutí se nebudou hlásit.

## <a name="enable-time-on-page-tracking"></a>Povolit sledování času na stránce

Nastavením se `autoTrackPageVisitTime: true` sleduje čas strávený uživateli na každé stránce. V každém novém PageView je doba, kterou uživatel strávil na *Předchozí* stránce, odeslána jako [vlastní metrika](../platform/metrics-custom-overview.md) s názvem `PageVisitTime` . Tato vlastní metrika je zobrazitelná v [Průzkumník metrik](../platform/metrics-getting-started.md) jako metrika založená na protokolu.

## <a name="enable-correlation"></a>Povolit korelaci

Korelace generuje a odesílá data, která umožňují distribuované trasování a nanáší [mapu aplikace](../app/app-map.md), [zobrazení na konci transakce](../app/app-map.md#go-to-details)a další diagnostické nástroje.

Následující příklad zobrazuje všechny možné konfigurace potřebné k povolení korelace s poznámkami pro konkrétní scénář níže:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Pokud některý ze serverů jiných výrobců, které klient komunikuje, nemůže přijmout `Request-Id` hlavičky a a `Request-Context` nemůžete aktualizovat jejich konfiguraci, pak je budete muset vložit do seznamu vyloučení prostřednictvím `correlationHeaderExcludeDomains` Vlastnosti konfigurace. Tato vlastnost podporuje zástupné znaky.

Na straně serveru musí být možné přijmout připojení k těmto hlavičkám. V závislosti na `Access-Control-Allow-Headers` konfiguraci na straně serveru je často potřeba rozšíření seznamu na straně serveru tím, že ručně přidáte `Request-Id` a `Request-Context` .

Access-Control-Allow-Headers: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> Pokud používáte OpenTelemtry nebo Application Insights sady SDK vydané v 2020 nebo novějších verzích, doporučujeme použít [WC3 TraceContext](https://www.w3.org/TR/trace-context/). [Tady](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)najdete pokyny k konfiguraci.

## <a name="single-page-applications"></a>Jednostránkové aplikace

Ve výchozím nastavení tato sada SDK **nezpracovává změny** směrování na základě stavu, ke kterým dochází v aplikacích s jednou stránkou. Pokud chcete povolit automatické sledování změn směrování pro jednu stránkovou aplikaci, můžete přidat `enableAutoRouteTracking: true` do konfigurace instalace.

V současné době nabízíme samostatný [modul plug-in pro reakce](javascript-react-plugin.md), který můžete inicializovat pomocí této sady SDK. Bude také plnit sledování změn směrování pro vás a shromažďovat další reakce na konkrétní telemetrii.
> [!NOTE]
> Použijte `enableAutoRouteTracking: true` pouze v případě,  že nepoužíváte modul plug-in reakce. Obě jsou schopné posílat nové PageViews při změně trasy. Pokud jsou obě povolené, může se odeslat duplicitní PageViews.

## <a name="extensions"></a>Rozšíření

| Rozšíření |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md) |

## <a name="explore-browserclient-side-data"></a>Prozkoumat data v prohlížeči nebo na straně klienta

Data v prohlížeči nebo na straně klienta si můžete prohlédnout na základě **metrik** a přidat jednotlivé metriky, které vás zajímají:

![Snímek obrazovky se stránkou metrik v Application Insights znázorňující grafická zobrazení dat metrik pro webovou aplikaci.](./media/javascript/page-view-load-time.png)

Data můžete také zobrazit ze sady JavaScript SDK prostřednictvím prostředí prohlížeče na portálu.

Vyberte **prohlížeč** a pak zvolte **selhání** nebo **výkon**.

![Snímek obrazovky stránky prohlížeče v Application Insights ukazuje, jak přidat selhání prohlížeče nebo výkon prohlížeče k metrikám, které můžete zobrazit pro vaši webovou aplikaci.](./media/javascript/browser.png)

### <a name="performance"></a>Výkon

![Snímek obrazovky se stránkou Performance (výkon) v Application Insights znázorňující grafické zobrazení metrik operací pro webovou aplikaci.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Závislosti

![Snímek obrazovky se stránkou Performance (výkon) v Application Insights znázorňující grafické zobrazení metrik závislostí pro webovou aplikaci.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analýzy

Chcete-li zadat dotaz na telemetrii shromážděnou sadou JavaScript SDK, vyberte tlačítko **Zobrazit v protokolech (Analytics)** . Přidáním `where` příkazu `client_Type == "Browser"` se zobrazí pouze data z sady JavaScript SDK a všechny telemetrie na straně serveru shromážděné jinými sadami SDK budou vyloučeny.
 
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
2. Určete, která zdrojová mapování odpovídají tomuto zásobníku volání. Zdrojová mapa musí odpovídat zdrojovému souboru rámce zásobníku, ale má příponu. `.map`
3. Přetáhněte zdrojové mapy do zásobníku volání v Azure Portal ![ animovaný obrázek ukazující, jak přetahovat zdrojové soubory mapování ze složky sestavení do okna zásobník volání v Azure Portal.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights web Basic

Pro zjednodušené prostředí můžete místo toho nainstalovat základní verzi Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Tato verze je dodávána s minimálním počtem funkcí a funkcemi, které vám závisí na jejich sestavování podle potřeby. Například neprovede žádnou Autocollection (nezachycené výjimky, AJAX atd.). Tato verze neobsahuje rozhraní API pro odesílání určitých typů telemetrie, například, `trackTrace` `trackException` atd., takže budete muset zadat vlastní obálku. K dispozici je jediné rozhraní API `track` . Tady se nachází [Ukázka](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Příklady

Příklady spustitelný naleznete v tématu [Application Insights JavaScript SDK Samples](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade ze starší verze Application Insights

Přerušující se změny v verzi sady SDK v2:
- Pro lepší signatury rozhraní API se některá volání rozhraní API, například trackPageView a trackException, aktualizovala. Spuštění v aplikaci Internet Explorer 8 a starších verzích prohlížeče se nepodporuje.
- Obálka telemetrie má název pole a strukturu, které se mění kvůli aktualizacím schématu dat.
- Přesunuto `context.operation` do `context.telemetryTrace` . Některá pole se také změnila ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Chcete-li ručně aktualizovat aktuální ID PageView (například v aplikacích SPA), použijte `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Aby ID trasování bylo jedinečné, kde jste předtím používali `Util.newId()` , teď použijte `Util.generateW3CId()` . Oba nakonec mají ID operace.

Pokud používáte aktuální sadu SDK 1.0.20 (Application Insights produkční SDK) a chcete zjistit, jestli nová sada SDK funguje v modulu runtime, aktualizujte adresu URL v závislosti na vašem aktuálním scénáři načítání sady SDK.

- Stáhnout přes CDN scénář: aktualizujte fragment kódu, který aktuálně používáte k odkazování na následující adresu URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- scénář npm: volání `downloadAndSetup` ke stažení úplného skriptu ApplicationInsights z CDN a jeho inicializaci pomocí klíče instrumentace:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test v interním prostředí, aby se ověřilo, že telemetrie monitorování funguje podle očekávání. Pokud vše funguje, aktualizujte signatury rozhraní API odpovídajícím způsobem verze sady SDK v2 a nasaďte je v produkčním prostředí.

## <a name="sdk-performanceoverhead"></a>Výkon nebo režie sady SDK

Na pouhých 36 KB komprimovaný jako gzip a při inicializaci jenom ~ 15 MS, Application Insights na web přidá zanedbatelné množství loadtime. Pomocí fragmentu kódu se rychle načtou minimální součásti knihovny. Do té doby se celý skript stáhne na pozadí.

I když se skript stahuje ze sítě CDN, veškeré sledování vaší stránky je ve frontě. Po dokončení asynchronní inicializace staženého skriptu budou sledovány všechny události, které byly zařazeny do fronty. V důsledku toho nedojde ke ztrátě žádné telemetrie během celého životního cyklu stránky. Tento proces instalace poskytuje stránku se systémovou analýzou, která je pro vaše uživatele neviditelná.

> Souhrn:
> - ![verze npm](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![komprimovaná velikost gzip](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - Celkový čas inicializace na **15 MS**
> - V průběhu životního cyklu stránky nebylo vynecháno **žádné** sledování.

## <a name="browser-support"></a>Podpora prohlížečů

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Nejnovější ✔ pro Chrome |  Nejnovější ✔ Firefox | Internet Explorer 9 + & Edge ✔<br>Kompatibilní s IE 8 – kompatibilní | Nejnovější ✔ Opera | Nejnovější ✔ Safari |

## <a name="es3ie8-compatibility"></a>Kompatibilita ES3/IE8

V rámci sady SDK existuje mnoho uživatelů, kteří nemůžou řídit prohlížeče, které používají jejich zákazníci. V takovém případě je potřeba zajistit, aby tato sada SDK nadále byla "Work" a nedošlo k přerušení provádění JS, když je načtena ze staršího prohlížeče. I když by to bylo ideální pro nepodporované prohlížeče IE8 a starší generace (ES3), existuje mnoho velkých zákazníků nebo uživatelů, kteří nadále potřebují stránky na práci, a jak se jim poznamenalo, že můžou nebo nemůžou řídit, který prohlížeč má jejich koncoví uživatelé používat.

To neznamená, že budeme podporovat jenom nejnižší běžnou sadu funkcí, stačí, když musíme zachovat kompatibilitu s kódem ES3 a když přidáváte nové funkce, které se budou muset přidat způsobem, který by nenarušil ES3 analýzu JavaScriptu a přidal jako volitelnou funkci.

[Úplné podrobnosti o podpoře IE8 najdete na GitHubu.](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Open-Source sada SDK

Sada Application Insights JavaScript SDK je open source, aby zobrazila zdrojový kód nebo přispívala k projektu na [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-JS). 

Nejnovější aktualizace a opravy chyb [najdete v poznámkách k verzi](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Další kroky
* [Sledování využití](usage-overview.md)
* [Vlastní události a metriky](api-custom-events-metrics.md)
* [Sestavení vyhodnocení poučení](usage-overview.md)
* [Řešení potíží se selháním načtení sady SDK](javascript-sdk-load-failure.md)
