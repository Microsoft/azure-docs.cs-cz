---
title: Telemetrie pro vyhledávání analýz provozu
titleSuffix: Azure Cognitive Search
description: Povolte vyhledávání v analýze provozu pro Azure Kognitivní hledání, shromáždění telemetrie a událostí iniciované uživatelem pomocí Application Insights a pak Analyzujte výsledky v sestavě Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485465"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Shromažďování dat telemetrie pro vyhledávání analýz provozu

Prohledat analýzu provozu je vzor pro shromažďování telemetrie o interakcích uživatelů s vaší aplikací Azure Kognitivní hledání, jako jsou události kliknutí iniciované uživatelem a vstupy na klávesnici. Pomocí těchto informací můžete zjistit efektivitu svého řešení hledání, včetně oblíbených hledaných výrazů, míry úspěšnosti a toho, které vstupy dotazů dávají žádné výsledky.

Tento model získá závislost na [Application Insights](../azure-monitor/app/app-insights-overview.md) (funkce [Azure monitor](../azure-monitor/index.yml)) ke shromažďování uživatelských dat. Vyžaduje přidání instrumentace do klientského kódu, jak je popsáno v tomto článku. Nakonec budete potřebovat mechanismus vytváření sestav k analýze dat. Doporučujeme Power BI, ale můžete použít řídicí panel aplikace nebo jakýkoli nástroj, který se připojuje k Application Insights.

> [!NOTE]
> Vzor popsaný v tomto článku je pro pokročilé scénáře a navštívených data generovaná kódem, který přidáte do svého klienta. Naproti tomu protokoly služeb se snadno nastavují, poskytují rozsah metrik a můžou se dělat na portálu bez nutnosti kódu. Povolení protokolování se doporučuje pro všechny scénáře. Další informace najdete v tématu [shromažďování a analýza dat protokolu](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifikujte relevantní hledaná data

Abyste měli k dispozici užitečné metriky pro vyhledávání analýz provozu, je potřeba protokolovat některé signály od uživatelů vaší aplikace pro hledání. Tyto signály označují obsah, který si uživatelé zajímá a považují za relevantní. Pro vyhledávání analýz provozu patří mezi ně:

+ Uživatelem vygenerované události vyhledávání: jenom vyhledávací dotazy iniciované uživatelem jsou zajímavé. Jiné požadavky na hledání, jako jsou například ty, které se použily k naplnění omezujících podmínek nebo načtení interních informací, nejsou důležité. Nezapomeňte jenom instrumentovat události iniciované uživatelem, abyste se vyhnuli zkosení nebo posunu výsledků.

+ Uživatelem vygenerované události kliknutí: na stránce výsledků hledání obecně znamená událost Click, že dokument je relevantní výsledek pro konkrétní vyhledávací dotaz.

Když propojíte hledání a kliknete na události s ID korelace, získáte hlubší přehled o tom, jak dobře funguje vyhledávání vaší aplikace.

## <a name="add-search-traffic-analytics"></a>Přidat analýzu provozu hledání

Na stránce [portálu](https://portal.azure.com) pro vaši službu Azure kognitivní hledání otevřete stránku vyhledávání Analýza provozu pro přístup k listu tahák pro následující vzor telemetrie. Na této stránce můžete vybrat nebo vytvořit prostředek Application Insights, získat klíč instrumentace, kopírovat fragmenty, které můžete přizpůsobit pro vaše řešení, a stáhnout sestavu Power BI, která je vytvořená ve schématu ve vzorku.

![Stránka Analýza provozu hledání na portálu](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Stránka Analýza provozu hledání na portálu")

## <a name="1---set-up-application-insights"></a>1. nastavení Application Insights

Vyberte existující prostředek Application Insights nebo [ho vytvořte](../azure-monitor/app/create-new-resource.md) , pokud ho ještě nemáte. Pokud použijete stránku Analýza provozu hledání, můžete zkopírovat klíč instrumentace, který aplikace potřebuje pro připojení k Application Insights.

Jakmile budete mít prostředek Application Insights, můžete podle [pokynů pro podporované jazyky a platformy](../azure-monitor/app/platforms.md) zaregistrovat vaši aplikaci. Registrace jednoduše přidá klíč instrumentace z Application Insights do kódu, který nastaví přidružení. Klíč můžete najít na portálu nebo na stránce Analýza provozu hledání, když vyberete existující prostředek.

Zástupce, který funguje pro některé typy projektů aplikace Visual Studio, se projeví v následujících krocích. Vytvoří prostředek a zaregistruje aplikaci během několika kliknutí.

1. Pro vývoj aplikací Visual Studio a ASP.NET otevřete řešení a vyberte **projekt**  >  **Přidat telemetrie Application Insights**.

1. Klikněte na **Začít**.

1. Zaregistrujte svou aplikaci poskytnutím účet Microsoft, předplatným Azure a prostředku Application Insights (výchozí prostředek je výchozí). Klikněte na **Zaregistrovat**.

V tuto chvíli je vaše aplikace nastavená pro monitorování aplikací, což znamená, že všechny načtené stránky jsou sledovány s výchozími metrikami. Další informace o předchozích krocích najdete v tématu [Povolení telemetrie Application Insights na straně serveru](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2. Přidání instrumentace

V tomto kroku Instrumentujte svoji vlastní aplikaci pro hledání pomocí Application Insights prostředku, který jste vytvořili v předchozím kroku. Pro tento proces existují čtyři kroky, počínaje vytvořením klienta telemetrie.

### <a name="step-1-create-a-telemetry-client"></a>Krok 1: Vytvoření klienta telemetrie

Vytvoří objekt, který odesílá události do Application Insights. Instrumentaci můžete přidat do kódu aplikace na straně serveru nebo kódu na straně klienta běžícího v prohlížeči, který je zde vyjádřen jako varianty jazyka C# a JavaScriptu (pro jiné jazyky, viz úplný seznam [podporovaných platforem a architektur](../azure-monitor/app/platforms.md)). Vyberte přístup, který poskytuje požadovanou hloubku informací.

Telemetrie na straně serveru zachycuje metriky na aplikační vrstvě, například v aplikacích, které běží jako webová služba v cloudu, nebo jako místní aplikace v podnikové síti. Telemetrie na straně serveru zachycuje hledání a kliknutí na události, umístění dokumentu ve výsledcích a informace o dotazech, ale vaše kolekce dat bude vymezená na jakékoli dostupné informace v této vrstvě.

Na straně klienta můžete mít další kód, který zpracovává vstupy dotazů, přidává navigaci nebo zahrnuje kontext (například dotazy iniciované z domovské stránky oproti stránce produktu). Pokud to popisuje vaše řešení, můžete se rozhodnout pro instrumentaci na straně klienta, aby vaše telemetrie odráželo další podrobnosti. Způsob shromažďování těchto dalších podrobností překračuje rozsah tohoto vzoru, ale můžete si prohlédnout [Application Insights pro webové stránky](../azure-monitor/app/javascript.md#explore-browserclient-side-data) a zobrazit tak další směr. 

**Použití jazyka C#**

V jazyce C# by měla být **InstrumentationKey** definována v konfiguraci aplikace, například appsettings.jsv případě, že je projekt ASP.NET. Pokud si nejste jisti klíčem umístění, přečtěte si pokyny k registraci.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Použití JavaScriptu**

Aktuální fragment (uvedený níže) je verze "5", verze je ve fragmentu kódování zakódována jako sv: "#" a [aktuální verze je také k dispozici na GitHubu](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Z důvodu čitelnosti a snížení možných chyb JavaScriptu jsou všechny možné možnosti konfigurace uvedeny na novém řádku ve výše uvedeném kódu fragmentu, pokud nechcete změnit hodnotu řádku s komentářem, který je možné odebrat.


### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2: vyžádání ID vyhledávání pro korelaci

Chcete-li sladit žádosti o vyhledávání pomocí kliknutí, je nutné mít ID korelace, které se týká těchto dvou různých událostí. Když si vyžádáte hlavičku protokolu HTTP, Kognitivní hledání Azure vám poskytne ID hledání.

ID vyhledávání umožňuje korelaci metrik vysílaných službou Azure Kognitivní hledání pro samotný požadavek s vlastními metrikami, které přihlašujete Application Insights.

**Použití jazyka C# (novější sada V11 SDK)**

Nejnovější sada SDK vyžaduje použití kanálu HTTP pro nastavení hlavičky, jak je popsáno v této [ukázce](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Použití jazyka C# (starší sada v10 za účelem SDK)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Použití JavaScriptu (volání rozhraní REST API)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Krok 3: události prohledávání protokolu

Pokaždé, když uživatel vydává požadavek na hledání, byste se měli přihlásit jako událost hledání s následujícím schématem na Application Insights vlastní události. Nezapomeňte protokolovat pouze uživatelsky vygenerované vyhledávací dotazy.

+ **SearchServiceName**: (String) název vyhledávací služby
+ **Searchid**: (GUID) jedinečný identifikátor vyhledávacího dotazu (přichází v odpovědi pro hledání)
+ **Indexer**: (String) index služby vyhledávání, na který se má dotazovat
+ **QueryTerms**: (String) hledané výrazy zadané uživatelem
+ **Element resultcount nastavený**: (int) počet vrácených dokumentů (v odpovědi na hledání)
+ **ScoringProfile**: (řetězec) název použitého profilu vyhodnocování, pokud existuje

> [!NOTE]
> Vyžádejte si počet uživatelem generovaných dotazů tak, že do vyhledávacího dotazu přidáte $count = true. Další informace najdete v tématu [hledání dokumentů (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**Použití jazyka C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Použití JavaScriptu**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Krok 4: protokolování událostí kliknutí

Pokaždé, když uživatel klikne na dokument, je signál, který musí být protokolován pro účely analýzy vyhledávání. Pomocí Application Insights vlastní události Zaprotokolujte tyto události s následujícím schématem:

+ **ServiceName**: (String) název vyhledávací služby
+ **Searchid**: (GUID) jedinečný identifikátor souvisejícího vyhledávacího dotazu
+ **Fulltextovém identifikátorů docid**: (String) identifikátor dokumentu
+ **Pozice**: (int) pořadí dokumentu na stránce výsledků hledání

> [!NOTE]
> Pozice odkazuje na pořadí mohutnosti ve vaší aplikaci. Můžete nastavit toto číslo, pokud je vždy stejné, aby bylo možné porovnat.
>

**Použití jazyka C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Použití JavaScriptu**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3. analýza v Power BI

Po instrumentaci aplikace a ověření, že je aplikace správně připojená k Application Insights, si stáhněte šablonu předdefinované sestavy, která bude analyzovat data v Power BI desktopu. Sestava obsahuje předdefinované grafy a tabulky, které jsou užitečné při analýze dalších dat zaznamenaných pro vyhledávání analýz provozu.

1. V levém navigačním podokně Azure Kognitivní hledání řídicího panelu v části **Nastavení** klikněte na **Prohledat analýzu provozu**.

1. Na stránce **Prohledat analýzu provozu** v kroku 3 klikněte na **získat Power BI Desktop** a nainstalujte Power BI.

   ![Získání sestav Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Získání sestav Power BI")

1. Na stejné stránce klikněte na **stáhnout Power BI sestavu**.

1. Sestava se otevře v Power BI Desktop a zobrazí se výzva, abyste se připojili k Application Insights a zadali přihlašovací údaje. Informace o připojení najdete na stránce Azure Portal pro prostředek Application Insights. Pro přihlašovací údaje zadejte stejné uživatelské jméno a heslo, které používáte pro přihlášení k portálu.

   ![Připojte se k Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Připojte se k Application Insights")

1. Klikněte na **načíst**.

Sestava obsahuje grafy a tabulky, které vám pomůžou dělat podrobnější rozhodnutí o vylepšení výkonu a relevance hledání.

Metriky obsahují následující položky:

+ Hledání ve svazku a nejoblíbenější páry termínů a dokumentů: podmínky, které vedou ke stejnému kliknutí na stejný dokument, seřazené kliknutím na tlačítko.
+ Hledání bez kliknutí: podmínka pro nejčastější dotazy, které registrují žádné kliknutí.

Následující snímek obrazovky ukazuje, co může předdefinovaná sestava vypadat, například pokud jste použili všechny prvky schématu.

![Řídicí panel Power BI pro Azure Kognitivní hledání](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Řídicí panel Power BI pro Azure Kognitivní hledání")

## <a name="next-steps"></a>Další kroky

Instrumentujte svoji vyhledávací aplikaci, abyste získali výkonná a přehledné data o vaší vyhledávací službě.

Další informace o [Application Insights](../azure-monitor/app/app-insights-overview.md) najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/application-insights/) , kde najdete další informace o různých úrovních služeb.

Přečtěte si další informace o vytváření úžasnéch sestav. Podrobnosti najdete v tématu [Začínáme s Power BI Desktop](/power-bi/fundamentals/desktop-getting-started) .
