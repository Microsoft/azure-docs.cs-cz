---
title: Vytvoření základního dotazu
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit žádost o dotaz v Kognitivní hledání, které nástroje a rozhraní API se mají použít pro testování a kódování a jak se dotazování rozhodnutí začíná s návrhem indexu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371122"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Vytvoření základního dotazu v Azure Kognitivní hledání

Tento článek vysvětluje krok vytváření dotazů krok za krokem. Příklady jsou zbývající, takže můžete zkopírovat řetězce do Průzkumníka služby **Search** na portálu nebo interaktivní sestavení dotazů pomocí post nebo Visual studia Code. V příkladech v tomto článku můžete použít libovolnou úroveň nebo verzi Kognitivní hledání.

## <a name="choose-a-tool-or-api"></a>Volba nástroje nebo rozhraní API

Vyberte si z následujících nástrojů a rozhraní API a vytvořte dotazy pro testovací nebo produkční úlohy.

| Metodologie | Popis |
|-------------|-------------|
| Portál| [Průzkumník služby Search (portál)](search-explorer.md) poskytuje panel hledání a možnosti pro indexování a výběry verzí rozhraní API. Výsledky se vrátí jako dokumenty JSON. Doporučuje se pro prvotní šetření, testování a ověřování. <br/>[Další informace](search-explorer.md) |
| Nástroje pro testování webu| [Post nebo Visual Studio Code](search-get-started-rest.md) jsou silné možnosti pro formulování volání REST v [dokumentech pro hledání](/rest/api/searchservice/search-documents) . REST API podporuje každou programovou operaci ve službě Azure Kognitivní hledání, takže můžete požadavky vystavovat interaktivně, abyste porozuměli tomu, jak funguje před investicem do kódu.  |
| Azure SDK | [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v jazyce C#.  [Další informace](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v Pythonu. [Další informace](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v JavaScriptu. [Další informace](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Nastavení vyhledávacího klienta

Vyhledávací klient se ověřuje pro vyhledávací službu, odesílá požadavky a zpracovává odpovědi. Dotazy jsou vždy směrovány na kolekci dokumentů jednoho indexu. Nemůžete spojit indexy nebo vytvářet vlastní nebo dočasné datové struktury jako cíl dotazu.

### <a name="in-the-portal"></a>Na portálu

Průzkumník služby Search a další nástroje portálu mají integrované klientské připojení ke službě s přímým přístupem k indexům a dalším objektům ze stránek portálu. Přístup k nástrojům, průvodcům a objektům předpokládá, že máte práva správce k této službě. V Průzkumníku hledání se můžete zaměřit na zadání vyhledávacího řetězce a dalších parametrů. 

### <a name="using-rest"></a>S využitím rozhraní REST

V případě volání REST můžete jako klienta určit požadavek na [hledání dokumentů](/rest/api/searchservice/search-documents) pomocí možností [post nebo podobných nástrojů](search-get-started-rest.md) . Každý požadavek je samostatný, takže musíte pro přístup zadat koncový bod (adresu URL služby) a správce nebo klíč rozhraní API pro dotazy. V závislosti na požadavku může adresa URL zahrnovat také název indexu, kolekci dokumentů a další vlastnosti. V hlavičce žádosti se předává několik vlastností, jako je typ obsahu a klíč rozhraní API. Další parametry lze předat v adrese URL nebo v těle žádosti. Všechna volání REST vyžadují klíč rozhraní API pro ověřování a verzi API-Version.

### <a name="using-azure-sdks"></a>Používání sad Azure SDK

Sady Azure SDK poskytují klientům hledání, které můžou zachovat stav a umožňují opakované použití připojení. Pro operace dotazů budete vytvářet instance SearchClient a poskytovat hodnoty pro následující vlastnosti: koncový bod, klíč, index. Pak můžete zavolat metodu vyhledávání a zadat řetězec dotazu. 

| Jazyk | Klient | Příklad |
|----------|--------|---------|
| C# a .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v jazyce C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v Pythonu](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v jazyce Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Odeslání prvního vyhledávacího dotazu v JavaScriptu](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Zvolit analyzátor: jednoduché | kompletní

Azure Kognitivní hledání nabízí možnost volby mezi dvěma analyzátory dotazů pro zpracování typických a specializovaných dotazů. Požadavky využívající jednoduchý analyzátor jsou obvykle fulltextové vyhledávací dotazy, které jsou formulovány pomocí [jednoduché syntaxe dotazu](query-simple-syntax.md), která je vybrána jako výchozí pro rychlost a efektivitu v textových dotazech bezplatného formuláře. Tato syntaxe podporuje řadu běžných operátorů hledání, včetně operátorů a, nebo, NOT, fráze, přípony a priority.

[Úplná syntaxe dotazů Lucene](query-Lucene-syntax.md#bkmk_syntax), která je povolená, když přidáváte `queryType=full` žádost, zveřejňuje široce přijatý a exprese dotazový jazyk vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Úplná syntaxe rozšiřuje jednoduchou syntaxi. Každý dotaz, který zapíšete pro jednoduchou syntaxi, se spustí s úplným analyzátorem Lucene. 

Následující příklady ilustrují bod: stejný dotaz, ale s různými **`queryType`** nastaveními, která poskytují různé výsledky. V prvním dotazu `^3` je potom považován za `historic` součást hledaného termínu. Výsledek nejvyšší úrovně pro tento dotaz je "Marquis Plaza & sady", jejichž popis má *oceánu* .

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

Stejný dotaz používající kompletní analyzátor Lucene se interpretuje `^3` jako rozbuška termínů v terénu. Přepínáním analyzátorů se mění pořadí s výsledky, které obsahují *historické* přesuny do horní části.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Povolení chování dotazů v indexu

Návrh indexu a návrh dotazu jsou úzce spojeny v Azure Kognitivní hledání. *Schéma indexu* s atributy každého pole určuje druh dotazu, který můžete sestavit.

Atributy indexu v poli nastavily povolené operace – určuje, jestli je pole možné *Prohledávat* v indexu, *získat ve výsledcích* , *seřaditelné*, *filtrovatelné* a tak dále. V ukázkových dotazech `"$orderby": "Rating desc"` funguje pouze to, protože pole hodnocení je ve schématu indexu označeno jako *seřaditelné* .

![Definice indexu pro ukázku hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definice indexu pro ukázku hotelu")

Výše uvedený snímek obrazovky je částečný seznam atributů indexu pro [ukázkový index hotelů](search-get-started-portal.md). V portálu můžete vytvořit a zobrazit celé schéma indexu. Další informace o atributech indexu naleznete v tématu [Create index (REST API)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Další kroky

Teď, když jste pochopili, jak je žádost vytvořená, vyzkoušejte příklady pomocí jednoduché i úplné syntaxe.

+ [Příklady jednoduchých dotazů](search-query-simple-examples.md)
+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)