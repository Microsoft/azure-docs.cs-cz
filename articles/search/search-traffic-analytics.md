---
title: Analýza provozu vyhledávání pro službu Azure Search | Microsoft Docs
description: Povolte Analýza provozu vyhledávání pro službu Azure Search, hostované cloudové vyhledávací službu v Microsoft Azure, k odemčení přehledy o vašich dat a jak pracují uživatelé.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: ca0a00d078cd63aa8736ba9f9822fd812823304f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-search-traffic-analytics"></a>Co je analýza provozu vyhledávání
Analýza provozu vyhledávání je vzor pro implementaci zpětné vazby pro vaši službu vyhledávání. Tento vzor popisuje potřebná data a jak shromažďovat pomocí Application Insights, vedoucí odvětví pro monitorování služeb ve více platformách.

Analýza provozu vyhledávání vám umožní získat přehled o vaši službu vyhledávání a odemknutí přehledy o uživatele a jejich chování. Tak, že data o co vaši uživatelé vybrat, je možné při rozhodování, které vylepšují další možnosti vyhledávání a regrese není tom, co očekávat při jsou výsledky.

Služba Azure Search nabízí telemetrii řešení, která integruje služby Azure Application Insights a Power BI vám umožňuje hlubší monitorování a sledování. Vzhledem k interakci s Azure Search je pouze prostřednictvím rozhraní API, musí být implementované telemetrii vývojáři hledání, pomocí pokynů v této stránce.

## <a name="identify-the-relevant-search-data"></a>Identifikovat data relevantní hledání

Pokud chcete, aby užitečná hledání metriky, je potřeba protokolu některé signály od uživatelů aplikace vyhledávání. Tyto signály označují obsah, který se uživatelé chtějí a zvažte relevantní pro jejich potřeb.

Existují dva signály, které potřebuje Analýza provozu vyhledávání:

1. Uživatelem generovaný vyhledávání události: jenom zajímavé vyhledávací dotazy iniciovaná uživatelem. Požadavky Search používá k naplnění omezující, další obsah nebo nějaké interní informace o, nejsou důležité a zkreslit a usměrní výsledky.

2. Události kliknutí uživatelem generovaný: pomocí kliknutí v tomto dokumentu označujeme uživateli výběr konkrétní vyhledávání výsledku vrácený z vyhledávacího dotazu. Klikněte na obvykle znamená, že dokument je výsledku relevantní pro konkrétní vyhledávací dotaz.

Pomocí propojení vyhledávání a klikněte na události s id korelace, je možné k analýze chování uživatelů ve vaší aplikaci. Tyto přehledy hledání je možné dosáhnout s pouze provoz protokolů hledání.

## <a name="how-to-implement-search-traffic-analytics"></a>Jak implementovat Analýza provozu vyhledávání

Signály uvedeno v předchozí části musí shromáždit z vyhledávací aplikaci jako uživatel pracuje s ním. Application Insights je rozšiřitelný monitorování řešení, k dispozici pro různé platformy, s možností flexibilní instrumentace. Využití Application Insights umožňuje využít výhod vytvořené Azure Search v zájmu snazší analýzy dat sestavy Power BI vyhledávání.

V [portál](https://portal.azure.com) stránka pro službu Azure Search, v okně Analýza provozu vyhledávání obsahuje tahák podle tohoto vzoru telemetrie. Můžete také vyberte nebo vytvořte prostředek Application Insights a najdete v části potřebná data z jednoho místa.

![Pokyny Analýza provozu vyhledávání][1]

### <a name="1-select-an-application-insights-resource"></a>1. Vyberte prostředek Application Insights

Je nutné vybrat prostředek Application Insights nebo, pokud již nemáte vytvořit. Můžete použít na prostředek, který se už používá k přihlášení vyžaduje vlastní události.

Když vytváříte nový prostředek Application Insights, všechny typy aplikací jsou platné pro tento scénář. Vyberte ten, který nejlépe vyhovuje platformy, které používáte.

Klíč instrumentace můžete potřebovat pro vytvoření klienta telemetrie pro aplikaci. Můžete ho získat na řídicím panelu portálu Application Insights, nebo můžete ho získat na stránce Analýza provozu vyhledávání výběr instance, kterou chcete použít.

### <a name="2-instrument-your-application"></a>2. Instrumentace vaší aplikace

Tato fáze je, kde instrumentace vlastní vyhledávací aplikaci, pomocí vaší vytvořený prostředek Application Insights v předchozích krocích. Existují čtyři kroky tohoto procesu:

**I. Vytvoření klienta telemetrie** to je objekt, který odesílá události pro prostředek Application Insights.

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

Pro jiné jazyky a platformy, najdete v úplných [seznamu](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Vyhledávání ID pro korelace požadavku** ke korelaci požadavky search kliknutími, je potřeba mít id korelace, které se týkají těchto dvou různých událostí. Vyhledávání systému Azure poskytuje Id vyhledávání, pokud budete požadovat s záhlaví:

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

**III. Protokolování událostí, které vyhledávání**

Pokaždé, když je žádost o vyhledávání vydané určitým uživatelem, je zapotřebí přihlásit, jako událost vyhledávání s následující schéma na vlastní událost Application Insights:

**ServiceName**: název vyhledávací služby (string) **SearchId**: Jedinečný identifikátor (guid) vyhledávací dotaz (dodává v odpovědi vyhledávání) **IndexName**: index služby search (string) jako dotaz **QueryTerms**: (string) hledaných termínů zadá uživatel **element resultcount element**: číslo (int) dokumentů, které byly vráceny (dodává v odpovědi vyhledávání)  **ScoringProfile**: (string) název profilu vyhodnocování používá, pokud existuje

> [!NOTE]
> Počet požadavků na dotazy na uživatelem generovaný přidáním $count = true vyhledávací dotaz. Další informace najdete v části [sem](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Mějte na paměti se protokolovat jenom vyhledávací dotazy, které jsou generovány uživatelé.
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

**IV. Přihlaste se kliknutím na události**

Pokaždé, když uživatel klikne na dokument, který je signál, musíte být přihlášeni pro účely analýzy vyhledávání. Použijte vlastní události Application Insights do protokolu tyto události se schématem následující:

**ServiceName**: název vyhledávací služby (string) **SearchId**: Jedinečný identifikátor (guid) související vyhledávací dotaz **identifikátorů DocId**: identifikátor dokumentu (string) **pozice**: stránka s výsledky pořadí (int) dokumentu do vyhledávání

> [!NOTE]
> Pozice odkazuje mohutnosti pořadí, v aplikaci. Jste volné nastavit toto číslo, dokud je vždy stejný, aby pro porovnání.
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

    appInsights.TrackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Analýza s Power BI Desktop

Po instrumentovány vaší aplikace a ověřit, zda že je aplikace správně připojený k Application Insights, můžete použít předdefinované šablony vytvořené Azure Search pro Power BI desktop.
Tato šablona obsahuje grafů a tabulek, které vám pomůžou přijímání rozhodnutí za účelem zvýšení výkonu vyhledávání a závažnosti.

K vytvoření instance šablony klienta Power BI, potřebujete tři údaje o Application Insights. Tato data naleznete na stránce Analýza provozu vyhledávání vyberete-li použít tento prostředek

![Přehled Data aplikací v okně Analýza provozu vyhledávání][2]

Metrika součástí šablony klienta Power BI:

*   Klikněte na tlačítko prostřednictvím rychlost (PEV.cenu): poměr uživatelů, kteří klikněte na konkrétní dokument počet celkový vyhledávání.
*   Hledání bez kliknutí: podmínky pro Nejoblíbenější dotazy, které registrují bez kliknutí
*   Většina kliknutí na dokumenty: nejvíce kliknutí na dokumenty podle ID za posledních 24 hodin, 7 dní až 30 dní.
*   Dvojice oblíbených termín document: podmínky, jejichž výsledkem stejné dokumentu klikli, seřazené podle kliknutí.
*   Čas klikněte na tlačítko: kliknutí kategorizovaná podle doby od vyhledávací dotaz.

![Power BI šablonu pro čtení ze služby Application Insights][3]


## <a name="next-steps"></a>Další kroky
Instrumentace aplikace vyhledávání výkonný a pronikavého údaje o vaši službu vyhledávání.

Můžete najít další informace o Application Insights [zde](https://go.microsoft.com/fwlink/?linkid=842905). Navštivte Application Insights [stránce s cenami](https://azure.microsoft.com/pricing/details/application-insights/) Další informace o jejich různých úrovních služby.

Další informace o vytváření úžasné sestav. V tématu [Začínáme s Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) podrobnosti

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
