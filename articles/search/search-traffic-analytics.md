---
title: implementace analýzy provozu vyhledávání – Azure Search
description: Povolení služby Search Analytics pro Azure Search k přidání událostí telemetrie a uživatelem iniciované události do souborů protokolu.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb12ed2f18df100ab3f679e7a8a3ef1e7c1aca45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647803"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementace vyhledávání analýzy provozu v Azure Search
Prohledat analýzu provozu je vzor pro implementaci smyčky zpětné vazby pro vaši vyhledávací službu. Tento model popisuje potřebná data a jejich shromažďování pomocí Application Insights, což je oborový vedoucí služby pro monitorování služeb na různých platformách.

Prohledat analýzu provozu vám umožní získat přehled o službě vyhledávání a vysílat přehledy o vašich uživatelích a jejich chování. Díky datům o tom, co vaši uživatelé zvolí, je možné učinit rozhodnutí, která ještě více vylepší možnosti vyhledávání, a vrátit se zpátky, pokud výsledky nejsou očekávané.

Azure Search nabízí řešení telemetrie, které integruje službu Azure Application Insights a Power BI, aby poskytovala podrobné sledování a sledování. Vzhledem k tomu, že interakce s Azure Search je pouze prostřednictvím rozhraní API, telemetrie musí implementovat vývojáři pomocí vyhledávání, a to podle pokynů na této stránce.

## <a name="identify-relevant-search-data"></a>Identifikujte relevantní hledaná data

Aby bylo možné využít metriky hledání, je nutné protokolovat některé signály od uživatelů aplikace pro hledání. Tyto signály označují obsah, o který si uživatelé zajímá a které považují za důležité k jejich potřebám.

Existují dva signály hledání Analýza provozu potřeby:

1. Uživatelem vygenerované události vyhledávání: jenom vyhledávací dotazy iniciované uživatelem jsou zajímavé. Žádosti o vyhledávání, které se použily k naplnění omezujících podmínek, dalšího obsahu nebo jakýchkoli interních informací, nejsou důležité a jejich výsledky se zkosí a posunou.

2. Události kliknutí generované uživatelem: Kliknutím v tomto dokumentu na něj odkazujeme na uživatele, který vybírá konkrétní výsledky hledání vrácené z vyhledávacího dotazu. V takovém případě se v případě konkrétního vyhledávacího dotazu obvykle jedná o relevantní výsledek.

Když propojíte hledání a kliknete na události s ID korelace, je možné analyzovat chování uživatelů ve vaší aplikaci. Tyto přehledy hledání neumožňují získat pouze protokoly provozu hledání.

## <a name="add-search-traffic-analytics"></a>Přidat analýzu provozu hledání

Signály uvedené v předchozí části se musí shromažďovat z aplikace pro hledání, když s ním uživatel pracuje. Application Insights je rozšiřitelné řešení monitorování, které je dostupné pro více platforem s flexibilními možnostmi instrumentace. Využití Application Insights umožňuje využít sestavy Power BIho vyhledávání, které vytvořila Azure Search, aby bylo možné snadněji analyzovat data.

Na stránce [portálu](https://portal.azure.com) pro vaši službu Azure Search okno hledání Analýza provozu obsahuje list tahák pro následující vzor telemetrie. Můžete také vybrat nebo vytvořit prostředek Application Insights a zobrazit potřebná data na jednom místě.

![Pokyny pro hledání Analýza provozu][1]

## <a name="1---select-a-resource"></a>1\. výběr prostředku

Musíte vybrat prostředek Application Insights, který se má použít, nebo ho vytvořit, pokud ho ještě nemáte. K protokolování požadovaných vlastních událostí můžete použít prostředek, který se už používá.

Při vytváření nového prostředku Application Insights jsou všechny typy aplikací platné pro tento scénář. Vyberte ten, který nejlépe odpovídá platformě, kterou používáte.

Pro vytvoření klienta telemetrie pro vaši aplikaci potřebujete klíč instrumentace. Můžete ji získat z řídicího panelu portálu Application Insights, nebo ji můžete získat na stránce hledání Analýza provozu a vybrat instanci, kterou chcete použít.

## <a name="2---add-instrumentation"></a>2\. Přidání instrumentace

V této fázi si můžete instrumentovat svoji vlastní aplikaci pro hledání pomocí Application Insights prostředku vytvořeného v předchozím kroku. Tento postup obsahuje čtyři kroky:

**Krok 1: Vytvořte klienta** telemetrie. Jedná se o objekt odesílající události do prostředku Application Insights.

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

**Krok 2: Vyžádejte si ID hledání korelace** pro korelaci žádostí o vyhledávání s kliknutími, je nutné mít ID korelace, které se vztahuje na tyto dvě odlišné události. Azure Search vám poskytne ID hledání, když si ho vyžádáte s hlavičkou:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

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

**Krok 3: Události prohledávání protokolu**

Pokaždé, když uživatel vydává požadavek na hledání, byste se měli přihlásit jako událost hledání s následujícím schématem na Application Insights vlastní události:

**SearchServiceName**: (String) název služby vyhledávání **searchid**: (GUID) jedinečný identifikátor vyhledávacího dotazu (v odpovědi na hledání) **indexer**: (String) vyhledávací služba pro dotazování **QueryTerms**: (String) Search výrazy zadané uživatelem **element resultcount nastavený**: (int) počet vrácených dokumentů (v odpovědi na hledání) **ScoringProfile**: (v řetězci) se použije název používaného profilu vyhodnocování (pokud nějaký existuje).

> [!NOTE]
> Počet požadavků u dotazů generovaných uživatelem přidáním $count = true do vyhledávacího dotazu. Další informace najdete [tady](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) .
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

**Krok 4: Události kliknutí na protokol**

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

Po instrumentaci aplikace a ověření, že je aplikace správně připojená k Application Insights, můžete použít předdefinovanou šablonu vytvořenou Azure Search pro Power BI Desktop. 

Azure Search poskytuje monitorování [Power BI balíčku obsahu](https://app.powerbi.com/getdata/services/azure-search) , abyste mohli analyzovat data protokolu. Sada Content Pack T přidává předdefinované grafy a tabulky, které jsou užitečné při analýze dalších dat zaznamenaných pro vyhledávání analýz provozu. Další informace najdete v tématu [stránce nápovědy balíčku obsahu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. V levém navigačním podokně Azure Search řídicího panelu vyberte v části **Nastavení**možnost **Prohledat analýzu provozu**.

2. Na stránce **Prohledat analýzu provozu** v kroku 3 klikněte na **získat Power BI Desktop** a nainstalujte Power BI.

   ![Získání sestav Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Získání sestav Power BI")

2. Na stejné stránce klikněte na **Stáhnout sestavu PowerBI**.

3. Sestava se otevře v Power BI Desktop a zobrazí se výzva, abyste se připojili k Application Insights. Tyto informace můžete najít na stránce Azure Portal Application Insights prostředku.

   ![Připojení k Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Připojení k Application Insights")

4. Klikněte na **načíst**.

Sestava obsahuje grafy a tabulky, které vám pomůžou dělat podrobnější rozhodnutí o vylepšení výkonu a relevance hledání.

Metriky obsahují následující položky:

* Klikněte na rychlost (centrum): poměr uživatelů, kteří na konkrétní dokument kliknou, na celkový počet hledání.
* Hledání bez kliknutí: podmínka pro nejčastější dotazy, které registrují žádné kliknutí.
* Nejpoužívanější dokumenty: nejvíce kliknuli dokumenty podle ID za posledních 24 hodin, 7 dní a 30 dnů.
* Oblíbené páry termínů: podmínky, které mají za následek kliknutí na stejný dokument, seřazené kliknutím na tlačítko.
* Doba, po kterou se má kliknout: po dobu od vyhledávacího dotazu se odblokuje na sebe.

Následující snímek obrazovky zobrazuje předdefinované sestavy a grafy pro analýzu analýz provozu vyhledávání.

![Řídicí panel Power BI pro Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Řídicí panel Power BI pro Azure Search")

## <a name="next-steps"></a>Další postup
Instrumentujte svoji vyhledávací aplikaci, abyste získali výkonná a přehledné data o vaší vyhledávací službě.

Další informace o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/application-insights/) , kde najdete další informace o různých úrovních služeb.

Další informace o vytváření úžasných sestav. Zobrazit [Začínáme s Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) podrobnosti

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
