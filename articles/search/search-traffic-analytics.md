---
title: Sestava dat analýzy provozu vyhledávání
titleSuffix: Azure Cognitive Search
description: Povolte vyhledávání v analýze provozu pro Azure Kognitivní hledání, shromáždění telemetrie a událostí iniciované uživatelem pomocí Application Insights a pak Analyzujte výsledky v sestavě Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 84e60b0a942bad94d8e36eb20b5be8e3f55af80a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190946"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementace vyhledávání analýzy provozu v Azure Kognitivní hledání

Prohledat analýzu provozu je vzor pro implementaci smyčky zpětné vazby pro vaši vyhledávací službu. Cílem je shromáždit telemetrii na události kliknutí iniciované uživatelem a vstupy klávesnice. Pomocí těchto informací můžete zjistit efektivitu svého řešení hledání, včetně oblíbených hledaných výrazů, míry úspěšnosti a toho, které vstupy dotazů dávají žádné výsledky.

Tento model získá závislost na [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (funkce [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) ke shromažďování uživatelských dat. Také budete muset přidat instrumentaci do kódu klienta, jak je popsáno v tomto článku. Nakonec budete potřebovat mechanismus vytváření sestav k analýze dat. Doporučujeme, abyste Power BI, ale můžete použít jakýkoli nástroj, který se připojuje k Application Insights.

> [!NOTE]
> Vzor popsaný v tomto článku je pro pokročilé scénáře a navštívených data generovaná vaším klientem. Případně můžete ohlásit informace protokolu vygenerované službou vyhledávání. Další informace o sestavách protokolu služby najdete v tématu [monitorování využití prostředků a aktivity dotazů](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Identifikujte relevantní hledaná data

Aby bylo možné využít metriky hledání, je nutné protokolovat některé signály od uživatelů aplikace pro hledání. Tyto signály označují obsah, o který si uživatelé zajímá a které považují za důležité k jejich potřebám.

Existují dva signály hledání Analýza provozu potřeby:

+ Uživatelem vygenerované události vyhledávání: jenom vyhledávací dotazy iniciované uživatelem jsou zajímavé. Žádosti o vyhledávání, které se použily k naplnění omezujících podmínek, dalšího obsahu nebo jakýchkoli interních informací, nejsou důležité a jejich výsledky se zkosí a posunou.

+ Uživatelem generované události kliknutí: kliknutím na tento dokument odkazujeme na uživatele, který vybral konkrétní výsledky hledání vrácené z vyhledávacího dotazu. V takovém případě se v případě konkrétního vyhledávacího dotazu obvykle jedná o relevantní výsledek.

Když propojíte hledání a kliknete na události s ID korelace, je možné analyzovat chování uživatelů ve vaší aplikaci. Tyto přehledy hledání neumožňují získat pouze protokoly provozu hledání.

## <a name="add-search-traffic-analytics"></a>Přidat analýzu provozu hledání

Signály uvedené v předchozí části se musí shromažďovat z aplikace pro hledání, když s ním uživatel pracuje. Application Insights je rozšiřitelné řešení monitorování, které je dostupné pro více platforem s flexibilními možnostmi instrumentace. Využití Application Insights umožňuje využít sestavy Power BIho vyhledávání, které vytvořila služba Azure Kognitivní hledání, aby bylo možné snadněji analyzovat data.

Na stránce [portálu](https://portal.azure.com) pro vaši službu Azure kognitivní hledání obsahuje stránka hledání Analýza provozu seznam tahák pro následující vzor telemetrie. Můžete také vybrat nebo vytvořit prostředek Application Insights a zobrazit potřebná data na jednom místě.

![Pokyny pro hledání Analýza provozu][1]

## <a name="1---select-a-resource"></a>1\. výběr prostředku

Musíte vybrat prostředek Application Insights, který se má použít, nebo ho vytvořit, pokud ho ještě nemáte. K protokolování požadovaných vlastních událostí můžete použít prostředek, který se už používá.

Při vytváření nového prostředku Application Insights jsou všechny typy aplikací platné pro tento scénář. Vyberte ten, který nejlépe odpovídá platformě, kterou používáte.

Pro vytvoření klienta telemetrie pro vaši aplikaci potřebujete klíč instrumentace. Můžete ji získat z řídicího panelu portálu Application Insights, nebo ji můžete získat na stránce hledání Analýza provozu a vybrat instanci, kterou chcete použít.

## <a name="2---add-instrumentation"></a>2\. Přidání instrumentace

V tomto kroku Instrumentujte svoji vlastní aplikaci pro hledání pomocí Application Insights prostředku, který jste vytvořili v předchozím kroku. Tento postup obsahuje čtyři kroky:

**Krok 1: Vytvoření klienta telemetrie**

Toto je objekt, který odesílá události do prostředku Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Další jazyky a platformy najdete v [seznamu](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)úplný.

**Krok 2: vyžádání ID vyhledávání pro korelaci**

Chcete-li sladit žádosti o vyhledávání pomocí kliknutí, je nutné mít ID korelace, které se týká těchto dvou různých událostí. Azure Kognitivní hledání poskytuje ID hledání, když si ho vyžádáte s hlavičkou:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Krok 3: události prohledávání protokolu**

Pokaždé, když uživatel vydává požadavek na hledání, byste se měli přihlásit jako událost hledání s následujícím schématem na Application Insights vlastní události:

**SearchServiceName**: (String) název vyhledávací služby **searchid**: (GUID) jedinečný identifikátor vyhledávacího dotazu (v odpovědi na hledání) **indexeru**: (String) index služby vyhledávání, který se má dotazovat na **QueryTerms**: (řetězce) hledané výrazy zadané uživatelem **element resultcount nastavený**: (int) počet vrácených dokumentů (je k dispozici v odpovědi hledání) **ScoringProfile**: (řetězec) název používaného profilu vyhodnocování

> [!NOTE]
> Počet požadavků u dotazů generovaných uživatelem přidáním $count = true do vyhledávacího dotazu. Další informace najdete [tady](/rest/api/searchservice/search-documents#counttrue--false).
>

> [!NOTE]
> Nezapomeňte pouze dotazy na hledání v protokolu, které jsou generovány uživateli.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Krok 4: protokolování událostí kliknutí**

Pokaždé, když uživatel klikne na dokument, je signál, který musí být protokolován pro účely analýzy vyhledávání. Pomocí Application Insights vlastní události Zaprotokolujte tyto události s následujícím schématem:

**ServiceName**: (String) název vyhledávací služby **searchid**: (GUID) jedinečný identifikátor relačního vyhledávacího dotazu **fulltextovém identifikátorů docid**: (String) umístění identifikátoru dokumentu **pozice**: (int) v dokumentu na stránce výsledků hledání

> [!NOTE]
> Pozice odkazuje na pořadí mohutnosti ve vaší aplikaci. Můžete nastavit toto číslo, pokud je vždy stejné, aby bylo možné porovnat.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3\. analýza v Power BI

Po instrumentaci aplikace a ověření, že je aplikace správně připojená k Application Insights, si stáhněte šablonu předdefinované sestavy, která bude analyzovat data v Power BI desktopu. Sestava obsahuje předdefinované grafy a tabulky, které jsou užitečné při analýze dalších dat zaznamenaných pro vyhledávání analýz provozu. 

1. V levém navigačním podokně Azure Kognitivní hledání řídicího panelu v části **Nastavení**klikněte na **Prohledat analýzu provozu**.

2. Na stránce **Prohledat analýzu provozu** v kroku 3 klikněte na **získat Power BI Desktop** a nainstalujte Power BI.

   ![Získání sestav Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Získání sestav Power BI")

2. Na stejné stránce klikněte na **stáhnout Power BI sestavu**.

3. Sestava se otevře v Power BI Desktop a zobrazí se výzva, abyste se připojili k Application Insights a zadali přihlašovací údaje. Informace o připojení najdete na stránce Azure Portal pro prostředek Application Insights. Pro přihlašovací údaje zadejte stejné uživatelské jméno a heslo, které používáte pro přihlášení k portálu.

   ![Připojení k Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Připojení k Application Insights")

4. Klikněte na **načíst**.

Sestava obsahuje grafy a tabulky, které vám pomůžou dělat podrobnější rozhodnutí o vylepšení výkonu a relevance hledání.

Metriky obsahují následující položky:

* Hledání ve svazku a nejoblíbenější páry termínů a dokumentů: podmínky, které vedou ke stejnému kliknutí na stejný dokument, seřazené kliknutím na tlačítko.
* Hledání bez kliknutí: podmínka pro nejčastější dotazy, které registrují žádné kliknutí.

Následující snímek obrazovky zobrazuje předdefinované sestavy a grafy pro analýzu analýz provozu vyhledávání.

![Řídicí panel Power BI pro Azure Kognitivní hledání](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Řídicí panel Power BI pro Azure Kognitivní hledání")

## <a name="next-steps"></a>Další kroky
Instrumentujte svoji vyhledávací aplikaci, abyste získali výkonná a přehledné data o vaší vyhledávací službě.

Další informace o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/application-insights/) , kde najdete další informace o různých úrovních služeb.

Další informace o vytváření úžasných sestav. Podrobnosti najdete v tématu [Začínáme s Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
