---
title: Prohledání analýzy provozu – Azure Search
description: Povolení analýzy provozu vyhledávání pro službu Azure Search, vyhledávání služby hostované v cloudu v Microsoft Azure, a odhalit nové poznatky o vašich uživatelích a vaše data.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4cc7434508e49715e95c87421db2bbed7e20de05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310234"
---
# <a name="what-is-search-traffic-analytics"></a>Co je analýza provozu vyhledávání
Prohledání analýzy provozu je vzor pro implementování smyčka zpětné vazby pro vaši vyhledávací službu. Tento model popisuje potřebná data a jak ji pomocí Application Insights, monitorování služeb ve více platformách Špička v oboru shromažďovat.

Prohledání analýzy provozu vám umožní získat přehled o vaší vyhledávací služby a odhalit nové poznatky o vašich uživatelích a jejich chování. Tím, že data o co vaši uživatelé vybrat, je možné rozhodnutí, která ještě vylepšit prostředí pro vyhledávání a vraťte se zpět při výsledky nejsou, co neočekává.

Služba Azure Search nabízí řešení telemetrie, která se integruje s Azure Application Insights a Power BI poskytují podrobné monitorování a sledování. Interakce s Azure Search je pouze prostřednictvím rozhraní API, telemetrická data musí být implementované vývojáři pomocí služby search, postupujte podle pokynů na této stránce.

## <a name="identify-the-relevant-search-data"></a>Identifikujte příslušné vyhledávání dat

Pokud chcete, aby metriky užitečná hledání, je nutné protokolovat některé signály od uživatelů vyhledávací aplikaci. Tyto signály místo obsah, který uživatelé mají zájem a že považují za relevantní pro jejich potřeby.

Existují dva signály, které potřebuje prohledání analýzy provozu:

1. Hledat události generovaným uživateli: pouze zajímavé dotazy vyhledávání iniciovanou uživatelem. Žádostí o hledání použitých k naplnění omezující vlastnosti, další obsah nebo interní informace, nejsou důležité a zkosení a usměrňovat výsledky.

2. Události kliknutí generovaným uživateli: Pomocí kliknutí v tomto dokumentu označujeme uživatel výběrem konkrétní hledání výsledku vrácený z vyhledávacího dotazu. Kliknutím obecně znamená, že dokument je výsledkem relevantní pro konkrétní vyhledávací dotaz.

Díky propojení hledání a klikněte na události s id korelace, je možné k analýze chování uživatelů ve vaší aplikaci. Tyto přehledy jsou možné dosáhnout s pouze protokoly přenosů služby search.

## <a name="how-to-implement-search-traffic-analytics"></a>Jak implementovat prohledání analýzy provozu

Signály uvedených v předchozí části musí shromáždit z vyhledávací aplikaci, jak s ním uživatel pracuje. Application Insights je rozšiřitelná řešení pro monitorování, dostupné pro více platforem s možností flexibilní instrumentace. Použití služby Application Insights vám umožní využít vyhledávání sestav Power BI vytvoří služba Azure Search snazší analýzu dat.

V [portál](https://portal.azure.com) stránku pro službu Azure Search, okno prohledání analýzy provozu obsahuje tahák pro postup dle tohoto modelu telemetrická data. Můžete také vybrat nebo vytvořte prostředek Application Insights a zobrazit nezbytná data vše na jednom místě.

![Pokyny pro analýzu provozu hledání][1]

### <a name="1-select-an-application-insights-resource"></a>1. Vyberte prostředek Application Insights

Musíte vybrat prostředek Application Insights nebo, pokud již nemáte vytvořit. Můžete použít prostředek, který se už používá pro přihlášení vyžaduje vlastní události.

Při vytváření nového prostředku Application Insights, všechny typy aplikací jsou platné pro tento scénář. Vyberte ten, který nejlépe vyhovuje požadavkům platformu, kterou používáte.

Budete potřebovat Instrumentační klíč pro vytvoření klienta telemetrie pro vaši aplikaci. Můžete ho získat na řídicím panelu portálu služby Application Insights a můžete ho získat na stránce Analýza provozu vyhledávání vyberte instanci, kterou chcete použít.

### <a name="2-instrument-your-application"></a>2. Instrumentovat aplikaci

Tato fáze je, kde jste instrumentovali vlastní vyhledávací aplikaci pomocí prostředku Application Insights vytvořili v předchozím kroku. Existují čtyři kroky k tomuto procesu:

**MŮŽU. Vytvoření klienta telemetrie** jedná se o objekt, který odesílá události do prostředku Application Insights.

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

Pro ostatní jazyky a platformy, najdete v úplných [seznamu](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. ID vyhledávání pro korelace požadavku** ke koordinaci požadavků hledání pomocí kliknutí, je potřeba mít id korelace, které se týkají těchto dvou různých událostí. Azure Search poskytuje Id hledání při požadavku s hlavičkou:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**III. Hledat události protokolu**

Pokaždé, když požadavek hledání vydává uživatelem, je zapotřebí přihlásit, který jako vyhledávání událostí s následující schéma na vlastní události Application Insights:

**ServiceName**: název vyhledávací služby (řetězec) **SearchId**: Jedinečný identifikátor (guid) vyhledávací dotaz (nastane v odpovědi na vyhledávání) **IndexName**: index služby search (string) bude dotazovat **QueryTerms**: hledané termíny (řetězec) zadané uživatelem **ResultCount**: (int). počet dokumentů, které byly vráceny (nastane v odpovědi na vyhledávání)  **ScoringProfile**: název bodovací profil používá, pokud existuje (string)

> [!NOTE]
> Počet požadavků na dotazy uživatelů vygenerované přidáním $count = true vašemu vyhledávacímu dotazu. Další informace najdete v článku [zde](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Mějte na paměti se protokolovat jenom vyhledávací dotazy, které jsou generovány podle uživatele.
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

**VEKTOR IV. Klikněte na tlačítko Přihlásit události**

Pokaždé, když uživatel klikne na dokumentu, který je signál, který musíte být přihlášeni k analýze vyhledávání. Použití vlastních událostí Application Insights k protokolování těchto událostí s následující schéma:

**ServiceName**: název vyhledávací služby (řetězec) **SearchId**: Jedinečný identifikátor (guid) související vyhledávací dotaz **DocId**: identifikátor dokumentu (řetězec) **pozice**: stránka s výsledky (int) řád dokumentu v hledání

> [!NOTE]
> Pozice odkazuje na mohutnosti pořadí ve vaší aplikaci. Můžete libovolně nastavit toto číslo je vždy stejný, aby pro porovnání.
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

### <a name="3-analyze-with-power-bi-desktop"></a>3. Analýza v Power BI Desktopu

Po instrumentovat aplikaci a ověřit, že je vaše aplikace správně připojené k Application Insights, můžete použít předdefinované šablony vytvořené službou Azure Search pro Power BI desktopu.
Tato šablona obsahuje grafy a informovaněji rozhodovat v tabulkách, které vám pomůžou zlepšit výkon vyhledávání a závažnosti.

K vytvoření instance šablony klienta Power BI, potřebujete tři údaje o Application Insights. Tato data najdete na stránce prohledání analýzy provozu při výběru prostředek, který chcete použít

![Data Application Insights v okně prohledání analýzy provozu][2]

Metriky součástí Power BI desktop šablony:

*   Klikněte na tlačítko prostřednictvím frekvence (KONT): poměr uživatelů, kteří kliknutím na počet celkový počet prohledávání určitého dokumentu.
*   Vyhledávání bez kliknutí: podmínky pro nejčastější dotazy, které registrují bez kliknutí
*   Většina kliknutí na dokumenty: nejvíce kliknutí na dokumenty podle ID za posledních 24 hodin, 7 dní až 30 dní.
*   Dvojice oblíbený termín dokumentu: podmínky, které vyplývají ve stejném dokumentu kliknutí, seřazené podle kliknutí.
*   Čas na: kliknutí kterých rozdělit podle času od vyhledávací dotaz

![Šablona Power BI pro čtení ze služby Application Insights][3]


## <a name="next-steps"></a>Další kroky
Instrumentujte vaše vyhledávací aplikace k získání dat výkonné a přehledné informace o vaší vyhledávací služby.

Můžete najít další informace o Application Insights [tady](https://go.microsoft.com/fwlink/?linkid=842905). Navštivte web Application Insights [stránce s cenami](https://azure.microsoft.com/pricing/details/application-insights/) získat další informace o jejich různých úrovní služeb.

Další informace o vytváření úžasných sestav. Zobrazit [Začínáme s Power BI Desktopu](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) podrobnosti

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
