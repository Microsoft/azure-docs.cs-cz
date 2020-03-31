---
title: Telemetrie pro analýzu provozu vyhledávání
titleSuffix: Azure Cognitive Search
description: Povolte analýzy návštěvnosti vyhledávání pro Azure Cognitive Search, shromažďujte telemetrii a události iniciované uživateli pomocí Application Insights a pak analyzujte zjištění v sestavě Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258205"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Shromažďování telemetrických dat pro analýzu provozu vyhledávání

Analýza návštěvnosti vyhledávání je vzor pro shromažďování telemetrie o interakcích uživatelů s vaší aplikací Azure Cognitive Search, jako jsou události kliknutí iniciované uživatelem a vstupy klávesnice. Pomocí těchto informací můžete určit účinnost vyhledávacího řešení, včetně oblíbených vyhledávacích dotazů, míry prokliku a toho, které vstupy dotazu přinášejí nulové výsledky.

Tento vzor trvá závislost na [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (funkce [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)) ke shromažďování uživatelských dat. Vyžaduje, abyste do kódu klienta přidali instrumentaci, jak je popsáno v tomto článku. Nakonec budete potřebovat mechanismus vykazování k analýze dat. Doporučujeme Power BI, ale můžete použít řídicí panel aplikace nebo jakýkoli nástroj, který se připojuje k Application Insights.

> [!NOTE]
> Vzor popsaný v tomto článku je pro pokročilé scénáře a clickstream data generovaná kódem, který přidáte do klienta. Naproti tomu protokoly služeb se snadno nastavují, poskytují řadu metrik a lze je provést na portálu bez nutnosti kódu. Povolení protokolování diagnostiky se doporučuje pro všechny scénáře. Další informace naleznete v [tématu Shromažďování a analýza dat protokolu](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identifikace relevantních vyhledávacích dat

Chcete-li mít užitečné metriky pro analýzu provozu vyhledávání, je nutné protokolovat některé signály od uživatelů vaší vyhledávací aplikace. Tyto signály jsou známkou obsahu, který uživatelé zajímají a který považují za relevantní. Pro analýzu provozu vyhledávání mezi ně patří:

+ Události vyhledávání generované uživateli: Zajímavé jsou pouze vyhledávací dotazy iniciované uživatelem. Požadavky na vyhledávání používané k naplnění omezujících fasejí, dalšího obsahu nebo jakýchkoli interních informací nejsou důležité a zkreslují a zkreslují vaše výsledky.

+ Události kliknutí generované uživatelem: Na stránce s výsledky hledání událost kliknutí obecně znamená, že dokument je relevantním výsledkem pro konkrétní vyhledávací dotaz.

Propojením událostí vyhledávání a kliknutí s ID korelace získáte hlubší představu o tom, jak dobře si výkon y funkce vyhledávání vaší aplikace.

## <a name="add-search-traffic-analytics"></a>Přidání analýzy návštěvnosti vyhledávání

Na stránce [portálu](https://portal.azure.com) pro službu Azure Cognitive Search obsahuje stránka Search Traffic Analytics tahák pro sledování tohoto vzoru telemetrie. Na této stránce můžete vybrat nebo vytvořit prostředek Application Insights, získat klíč instrumentace, zkopírovat fragmenty, které můžete přizpůsobit pro vaše řešení, a stáhnout sestavu Power BI, která je vytvořená nad schématem, která se odráží ve vzoru.

![Stránka Analýza provozu na portálu](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Stránka Analýza provozu na portálu")

## <a name="1---set-up-application-insights"></a>1 – Nastavení přehledů aplikací

Vyberte existující prostředek Application Insights nebo [ho vytvořte,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) pokud ho ještě nemáte. Pokud používáte stránku Analýza provozu vyhledávání, můžete zkopírovat klíč instrumentace, který vaše aplikace potřebuje k připojení k Přehledům aplikací.

Jakmile budete mít prostředek Application Insights, můžete podle [pokynů pro podporované jazyky a platformy](https://docs.microsoft.com/azure/azure-monitor/app/platforms) zaregistrovat aplikaci. Registrace je jednoduše přidání instrumentace klíč z Application Insights do kódu, který nastaví přidružení. Klíč najdete na portálu nebo na stránce Analýza provozu ve vyhledávání, když vyberete existující prostředek.

Zástupce, který funguje pro některé typy projektů sady Visual Studio se projeví v následujících krocích. Vytvoří prostředek a zaregistruje vaši aplikaci několika kliknutími.

1. Pro vývoj Sady Visual Studio a ASP.NET otevřete řešení a vyberte **Project** > **Add Application Insights Telemetry**.

1. Klepněte na **tlačítko Začínáme**.

1. Zaregistrujte svou aplikaci poskytnutím účtu Microsoft, předplatného Azure a prostředku Application Insights (výchozí je nový prostředek). Klepněte na tlačítko **Registrovat**.

V tomto okamžiku je aplikace nastavena pro monitorování aplikací, což znamená, že všechna načtení stránky jsou sledována s výchozí metriky. Další informace o předchozích krocích naleznete v [tématu Povolení telemetrie na straně serveru Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Přidat instrumentaci

Tento krok je, kde nástroj vlastní vyhledávací aplikace pomocí application insights prostředek vytvořené v kroku výše. Existují čtyři kroky k tomuto procesu, počínaje vytvořením klienta telemetrie.

### <a name="step-1-create-a-telemetry-client"></a>Krok 1: Vytvoření klienta telemetrie

Vytvořte objekt, který odesílá události do Application Insights. Instrumentace můžete přidat do kódu aplikace na straně serveru nebo do kódu na straně klienta spuštěného v prohlížeči, vyjádřeného zde jako varianty Jazyka C# a JavaScriptu (pro ostatní jazyky se podívejte na úplný seznam [podporovaných platforem a architektur](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Zvolte přístup, který vám poskytne požadovanou hloubku informací.

Telemetrie na straně serveru zachycuje metriky na aplikační vrstvě, například v aplikacích spuštěných jako webová služba v cloudu nebo jako místní aplikace v podnikové síti. Telemetrie na straně serveru zachycuje události hledání a kliknutí, umístění dokumentu ve výsledcích a informace o dotazu, ale shromažďování dat bude vymezeno na jakékoli informace, které jsou v dané vrstvě k dispozici.

Na straně klienta můžete mít další kód, který manipuluje se vstupy dotazu, přidává navigaci nebo zahrnuje kontext (například dotazy iniciované z domovské stránky versus stránky produktu). Pokud to popisuje vaše řešení, můžete se rozhodnout pro instrumentaci na straně klienta tak, aby vaše telemetrie odráží další podrobnosti. Jak se tento další podrobnosti shromažďují přesahuje rozsah tohoto vzoru, ale můžete zkontrolovat [Application Insights pro webové stránky](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) pro další směr. 

**Použití C #**

Pro C#, **InstrumentationKey** se nachází v konfiguraci aplikace, jako je například appsettings.json, pokud je váš projekt ASP.NET. Pokud si nejste jisti umístěním klíče, vraťte se k pokynům k registraci.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2: Žádost o ID vyhledávání pro korelaci

Chcete-li korelovat požadavky na vyhledávání s kliknutí, je nutné mít ID korelace, která se týká těchto dvou odlišných událostí. Azure Cognitive Search poskytuje id vyhledávání, když o to požádáte s hlavičkou HTTP.

S ID vyhledávání umožňuje korelaci metrik vyzařovaných Azure Cognitive Search pro samotný požadavek, s vlastní metriky, které se přihlašujete do Application Insights.  

**Použití C #**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Použití JavaScriptu (volání REST API)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Krok 3: Protokolovat události hledání

Pokaždé, když je požadavek na vyhledávání vydán uživatelem, měli byste protokolovat, že jako událost hledání s následujícím schématem na application insights vlastní událost. Nezapomeňte protokolovat pouze uživatelem generované vyhledávací dotazy.

+ **SearchServiceName**: (řetězec) název služby vyhledávání
+ **SearchId**: (guid) jedinečný identifikátor vyhledávacího dotazu (je součástí odpovědi na vyhledávání)
+ **IndexName**: (řetězec) index vyhledávací služby, který má být dotazován
+ **QueryTerms**: (řetězec) hledané termíny zadané uživatelem
+ **ResultCount**: (int) počet dokumentů, které byly vráceny (je součástí odpovědi hledání)
+ **ScoringProfile**: (řetězec) název bodového profilu použitého, pokud existuje

> [!NOTE]
> Požádejte o počet dotazů generovaných uživatelem přidáním $count=true do vyhledávacího dotazu. Další informace naleznete v [tématu Hledání dokumentů (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Použití C #**

```csharp
var properties = new Dictionary <string, string> {
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

### <a name="step-4-log-click-events"></a>Krok 4: Události protokolu pro klepněte na tlačítko

Pokaždé, když uživatel klikne na dokument, je to signál, který musí být zaznamenán pro účely analýzy vyhledávání. Pomocí vlastních událostí Application Insights můžete tyto události protokolovat pomocí následujícího schématu:

+ **Název_služby ServiceName**: (řetězec) název vyhledávací služby
+ **SearchId**: (guid) jedinečný identifikátor souvisejícího vyhledávacího dotazu
+ **DocId**: (řetězec) identifikátor dokumentu
+ **Pozice**: (int) pořadí dokumentu na stránce s výsledky vyhledávání

> [!NOTE]
> Pozice odkazuje na základní pořadí ve vaší žádosti. Můžete nastavit toto číslo, pokud je vždy stejné, aby bylo možné porovnat.
>

**Použití C #**

```csharp
var properties = new Dictionary <string, string> {
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

## <a name="3---analyze-in-power-bi"></a>3 – Analýza v Power BI

Po instrumentaci aplikace a ověření, že je aplikace správně připojená k Application Insights, stáhnete předdefinovanou šablonu sestavy pro analýzu dat na ploše Power BI. Sestava obsahuje předdefinované grafy a tabulky užitečné pro analýzu dalších dat zachycených pro analýzu provozu vyhledávání.

1. V levém navigačním podokně ovládacího panelu Azure Cognitive Search klikněte v části **Nastavení**na **Search traffic analytics**.

1. Na stránce **Analýza provozu ve Vyhledávání** klikněte v kroku 3 na Získat Power BI **Desktop** a nainstalujte Power BI.

   ![Získání sestav Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Získání sestav Power BI")

1. Na stejné stránce klikněte na **Stáhnout sestavu Power BI**.

1. Sestava se otevře v Power BI Desktopu a budete vyzváni k připojení k Application Insights a zadání přihlašovacích údajů. Informace o připojení najdete na stránkách portálu Azure pro váš prostředek Application Insights. Pro pověření zadejte stejné uživatelské jméno a heslo, které používáte pro přihlášení portálu.

   ![Připojte se k Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Připojte se k Application Insights")

1. Klepněte na tlačítko **Načíst**.

Sestava obsahuje grafy a tabulky, které vám pomohou činit informovanější rozhodnutí ke zlepšení výkonu a relevance vyhledávání.

Metriky zahrnovaly následující položky:

+ Objem vyhledávání a nejoblíbenější dvojice termínů a dokumentů: termíny, které vedou ke kliknutí na stejný dokument seřazené kliknutími.
+ Vyhledávání bez kliknutí: termíny pro nejčastější dotazy, které registrují žádná kliknutí

Následující snímek obrazovky ukazuje, jak může vypadat předdefinovaná sestava, pokud jste použili všechny prvky schématu.

![Řídicí panel Power BI pro Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Řídicí panel Power BI pro Azure Cognitive Search")

## <a name="next-steps"></a>Další kroky

Instrumentujte svou vyhledávací aplikaci, abyste získali výkonná a přehledná data o vaší vyhledávací službě.

Další informace o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) najdete na stránce [s cenami,](https://azure.microsoft.com/pricing/details/application-insights/) kde najdete další informace o jejich různých úrovních služeb.

Přečtěte si další informace o vytváření úžasných přehledů. Podrobnosti [najdete v tématu Začínáme s Power BI Desktopem.](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)