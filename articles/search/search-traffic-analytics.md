---
title: Telemetrie pro vyhledávání analýz provozu
titleSuffix: Azure Cognitive Search
description: Povolte vyhledávání v analýze provozu pro Azure Kognitivní hledání, shromáždění telemetrie a událostí iniciované uživatelem pomocí Application Insights a pak Analyzujte výsledky v sestavě Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d93ced4b45befec207494909de61d30a98d2a67e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333728"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Shromažďování dat telemetrie pro vyhledávání analýz provozu

Prohledat analýzu provozu je vzor pro shromažďování telemetrie o interakcích uživatelů s vaší aplikací Azure Kognitivní hledání, jako jsou události kliknutí iniciované uživatelem a vstupy na klávesnici. Pomocí těchto informací můžete zjistit efektivitu svého řešení hledání, včetně oblíbených hledaných výrazů, míry úspěšnosti a toho, které vstupy dotazů dávají žádné výsledky.

Tento model získá závislost na [Application Insights](../azure-monitor/app/app-insights-overview.md) (funkce [Azure monitor](../azure-monitor/index.yml)) ke shromažďování uživatelských dat. Vyžaduje přidání instrumentace do klientského kódu, jak je popsáno v tomto článku. Nakonec budete potřebovat mechanismus vytváření sestav k analýze dat. Doporučujeme, abyste Power BI, ale můžete použít řídicí panel aplikace nebo jakýkoli nástroj, který se připojuje k Application Insights.

> [!NOTE]
> Vzor popsaný v tomto článku je pro pokročilé scénáře a navštívených data generovaná kódem, který přidáte do svého klienta. Naproti tomu protokoly služeb se snadno nastavují, poskytují rozsah metrik a můžou se dělat na portálu bez nutnosti kódu. Povolení protokolování se doporučuje pro všechny scénáře. Další informace najdete v tématu [shromažďování a analýza dat protokolu](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifikujte relevantní hledaná data

Abyste měli k dispozici užitečné metriky pro vyhledávání analýz provozu, je potřeba protokolovat některé signály od uživatelů vaší aplikace pro hledání. Tyto signály označují obsah, který si uživatelé zajímá a považují za relevantní. Pro vyhledávání analýz provozu patří mezi ně:

+ Uživatelem vygenerované události vyhledávání: jenom vyhledávací dotazy iniciované uživatelem jsou zajímavé. Žádosti o vyhledávání, které se použily k naplnění omezujících podmínek, dalšího obsahu nebo jakýchkoli interních informací, nejsou důležité a jejich výsledky se zkosí a posunou.

+ Uživatelem vygenerované události kliknutí: na stránce výsledků hledání obecně znamená událost Click, že dokument je relevantní výsledek pro konkrétní vyhledávací dotaz.

Když propojíte hledání a kliknete na události s ID korelace, získáte hlubší přehled o tom, jak dobře funguje vyhledávání vaší aplikace.

## <a name="add-search-traffic-analytics"></a>Přidat analýzu provozu hledání

Na stránce [portálu](https://portal.azure.com) pro vaši službu Azure kognitivní hledání obsahuje stránka hledání Analýza provozu seznam tahák pro následující vzor telemetrie. Na této stránce můžete vybrat nebo vytvořit prostředek Application Insights, získat klíč instrumentace, kopírovat fragmenty, které můžete přizpůsobit pro vaše řešení, a stáhnout sestavu Power BI, která je vytvořená ve schématu ve vzorku.

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

V jazyce C# je **InstrumentationKey** nalezen v konfiguraci aplikace, například appsettings.jsv případě, že je projekt ASP.NET. Pokud si nejste jisti klíčem umístění, přečtěte si pokyny k registraci.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Použití JavaScriptu**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2: vyžádání ID vyhledávání pro korelaci

Chcete-li sladit žádosti o vyhledávání pomocí kliknutí, je nutné mít ID korelace, které se týká těchto dvou různých událostí. Když si vyžádáte hlavičku protokolu HTTP, Kognitivní hledání Azure vám poskytne ID hledání.

ID vyhledávání umožňuje korelaci metrik vysílaných službou Azure Kognitivní hledání pro samotný požadavek s vlastními metrikami, které přihlašujete Application Insights.  

**Použití jazyka C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

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
> Vyžádejte si počet uživatelem generovaných dotazů tak, že do vyhledávacího dotazu přidáte $count = true. Další informace najdete v tématu [hledání dokumentů (REST)](/rest/api/searchservice/search-documents#counttrue--false).
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

1. V levém navigačním podokně Azure Kognitivní hledání řídicího panelu v části **Nastavení**klikněte na **Prohledat analýzu provozu**.

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