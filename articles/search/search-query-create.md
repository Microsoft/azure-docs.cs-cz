---
title: Vytvoření základního dotazu
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit žádost o dotaz v Kognitivní hledání, které nástroje a rozhraní API se mají použít pro testování a kódování a jak se dotazování rozhodnutí začíná s návrhem indexu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: a02d51d66b9d2b8bf3c08d4515713ecb062e0c8e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400212"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Vytvoření dotazu v Azure Kognitivní hledání

Pokud vytváříte dotaz poprvé, Tento článek popisuje nástroje a rozhraní API, které budete potřebovat, které metody slouží k vytvoření dotazu a jak může mít struktura indexu a obsah vliv na výsledky dotazu. Chcete-li se seznámit s tím, co požadavek na dotaz vypadá, začněte s [typy a kompozici dotazů](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Výběr nástrojů a rozhraní API

K vytvoření dotazu budete potřebovat nástroj nebo rozhraní API. Některé z následujících návrhů jsou užitečné při testování a produkčních úlohách.

| Metodologie | Popis |
|-------------|-------------|
| Portál| [Průzkumník služby Search (portál)](search-explorer.md) je rozhraní pro dotazy v Azure Portal, které spouští dotazy pro indexy v základní vyhledávací službě. Portál zajišťuje REST API volání na pozadí do operace [hledání dokumentů](/rest/api/searchservice/search-documents) , ale nemůže vyvolat funkci automatického dokončování, návrhů nebo vyhledávání dokumentů.<br/><br/> Můžete vybrat jakýkoli index a REST API verzi, včetně verze Preview. Řetězec dotazu může používat jednoduchou nebo úplnou syntaxi s podporou pro všechny parametry dotazu (Filter, SELECT, searchFields a tak dále). Při otevření indexu můžete na portálu pracovat s Průzkumníkem služby Search společně s definicí JSON indexu na souběžných kartách pro snadný přístup k atributům polí. Zkontroluje, jaká pole jsou při testování dotazů prohledávatelné, seřaditelné, filtrovatelné a plošky. <br/>Doporučuje se pro prvotní šetření, testování a ověřování. [Další informace](search-explorer.md) |
| Nástroje pro testování webu| [Post nebo Visual Studio Code](search-get-started-rest.md) jsou silné možnosti pro formulování žádosti o [vyhledávání dokumentů](/rest/api/searchservice/search-documents) a jakékoli jiné žádosti v klidovém umístění. Rozhraní REST API podporují všechny možné programové operace v Azure Kognitivní hledání a když použijete nástroj, jako je například post nebo Visual Studio Code, můžete požadavky vydávat interaktivně, abyste porozuměli tomu, jak funkce funguje před investicem do kódu. Nástroj pro testování webu je vhodný, pokud nemáte práva přispěvatele nebo správce v Azure Portal. Pokud máte adresu URL pro vyhledávání a klíč rozhraní API pro dotaz, můžete použít nástroje ke spouštění dotazů pro existující index. |
| Azure SDK | Až budete připraveni k psaní kódu, můžete použít klientské knihovny Azure.Search.Document v sadách Azure SDK pro .NET, Python, JavaScript nebo Java. Každá sada SDK má vlastní plán vydaných verzí, můžete je ale vytvářet a dotazovat na ně. <br/><br/>[SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v jazyce C#.  [Další informace](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v Pythonu. [Další informace](search-get-started-python.md)<br/><br/>[SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) se dá použít k dotazování indexu vyhledávání v JavaScriptu. [Další informace](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Nastavení vyhledávacího klienta

Vyhledávací klient se ověřuje pro vyhledávací službu, odesílá požadavky a zpracovává odpovědi. Bez ohledu na to, který nástroj nebo rozhraní API používáte, musí mít klient vyhledávání následující:

| Vlastnosti | Popis |
|------------|-------------|
| Koncový bod | Vyhledávací služba je adresa URL v tomto formátu: `https://[service-name].search.windows.net` . |
| Přístupový klíč rozhraní API (správce nebo dotaz) | Ověří požadavek služby Search. |
| Název indexu | Dotazy jsou vždy směrovány na kolekci dokumentů jednoho indexu. Nemůžete spojit indexy nebo vytvářet vlastní nebo dočasné datové struktury jako cíl dotazu. |
| Verze rozhraní API | Volání REST explicitně vyžadují `api-version` na žádost. Naproti tomu klientské knihovny v sadě Azure SDK mají verzi na určitou verzi REST API. Pro sady SDK `api-version` je implicitní. |

### <a name="in-the-portal"></a>Na portálu

Průzkumník služby Search a další nástroje portálu mají integrované klientské připojení ke službě s přímým přístupem k indexům a dalším objektům ze stránek portálu. Přístup k nástrojům, průvodcům a objektům vyžaduje členství v roli přispěvatel nebo výše služby. 

### <a name="using-rest"></a>S využitím rozhraní REST

V případě volání REST můžete jako klienta určit požadavek na [hledání dokumentů](/rest/api/searchservice/search-documents) pomocí možností [post nebo podobných nástrojů](search-get-started-rest.md) . Každý požadavek je samostatný, takže musíte zadat koncový bod, název indexu a verzi rozhraní API pro každý požadavek. Další vlastnosti, typ obsahu a klíč rozhraní API se předávají v hlavičce žádosti. 

K dotazování indexu můžete použít POST nebo GET. POST s parametry určenými v textu požadavku je snazší pracovat s. Pokud použijete POST, nezapomeňte zahrnout `docs/search` do adresy URL:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Používání sad Azure SDK

Pokud používáte sadu Azure SDK, vytvoříte klienta v kódu. Všechny sady SDK poskytují klientům hledání, které můžou uchovávat stav, a umožňují opakované použití připojení. Pro operace dotazů vytvoříte instanci **`SearchClient`** a a uvedete hodnoty pro následující vlastnosti: koncový bod, klíč, index. Potom můžete zavolat metodu **`Search method`** pro předání řetězce dotazu. 

| Jazyk | Klient | Příklad |
|----------|--------|---------|
| C# a .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v jazyce C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v Pythonu](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Odeslání prvního vyhledávacího dotazu v jazyce Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Odeslání prvního vyhledávacího dotazu v JavaScriptu](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Zvolit analyzátor: jednoduché | kompletní

Pokud je dotaz fulltextovým vyhledáváním, bude použit analyzátor pro zpracování obsahu vyhledávacího parametru. Azure Kognitivní hledání nabízí dva analyzátory dotazů. Jednoduchý analyzátor rozumí [jednoduché syntaxi dotazů](query-simple-syntax.md). Tento analyzátor byl vybrán jako výchozí pro rychlost a efektivitu v textových dotazech bezplatného formuláře. Syntaxe podporuje běžné operátory hledání (a, ne) pro vyhledávání termínů a frází a hledání předpony ( `*` jako v "moře *" pro Seattle a Seaside). Obecně doporučujeme, abyste nejprve vyzkoušeli jednoduchý analyzátor a pak přesunuli na úplný analyzátor, pokud požadavky aplikace volají na výkonnější dotazy.

[Úplná syntaxe dotazů Lucene](query-Lucene-syntax.md#bkmk_syntax), která je povolená, když přidáváte `queryType=full` žádost, je založená na [analyzátoru Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Úplná syntaxe je rozšíření jednoduché syntaxe s dalšími operátory, abyste mohli vytvářet pokročilé dotazy, jako je hledání přibližných hodnot, hledání pomocí zástupných znaků, hledání blízkosti a regulární výrazy. Následující příklady ilustrují bod: stejný dotaz, ale s různými **`queryType`** nastaveními, která poskytují různé výsledky. V prvním jednoduchém dotazu `^3` `historic` je potom považován za součást hledaného termínu. Výsledek nejvyšší úrovně pro tento dotaz je "Marquis Plaza & sady", jejichž popis má *oceánu* .

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

## <a name="choose-query-methods"></a>Výběr metod dotazů

Hledání je základem uživatelsky řízeného cvičení, kdy se termíny nebo fráze shromažďují z vyhledávacího pole, nebo z událostí kliknutí na stránce. Následující tabulka shrnuje mechanismy, pomocí kterých můžete shromažďovat vstup uživatele spolu s očekávaným vyhledávacím prostředím.

| Vstup | Prostředí |
|-------|---------|
| [Search – metoda](/rest/api/searchservice/search-documents) | Do vyhledávacího pole s operátory nebo bez se uživateli zadají výrazy nebo fráze a kliknutím na Hledat odeslání žádosti. Hledání se dá použít s filtry na stejný požadavek, ale ne s funkcí automatického dokončování nebo návrhů. |
| [AutoComplete – metoda](/rest/api/searchservice/autocomplete) | Uživatel může zadat několik znaků a dotazy budou iniciovány po zadání každého nového znaku. Odpověď je dokončený řetězec z indexu. Je-li zadaný řetězec platný, uživatel klikne na tlačítko Hledat a odešle dotaz do služby. |
| [Návrhy – metoda](/rest/api/searchservice/suggestions) | Stejně jako u automatického dokončování uživatel zadá několik znaků a vygeneruje se přírůstkové dotazy. Odpověď je rozevírací seznam vyhovujících dokumentů, který je obvykle reprezentován několika jedinečnými nebo popisnými poli. Pokud je některý z výběrů platný, uživatel klikne na jednu a vrátí se shodný dokument. |
| [Fasetová navigace](/rest/api/searchservice/search-documents#query-parameters) | Stránka zobrazuje navigační odkazy nebo cesty, které mají zúžený rozsah hledání. Omezující navigační struktura se skládá dynamicky na základě počátečního dotazu. Například `search=*` k naplnění podrobného navigačního stromu složeného ze všech možných kategorií. Vlastnost s omezující navigační strukturou je vytvořena z odpovědi na dotaz, ale je to také mechanismus pro vyjádření dalšího dotazu. n REST API odkaz `facets` je dokumentován jako parametr dotazu operace hledání dokumentů, ale lze jej použít bez `search` parametru.|
| [Filter – metoda](/rest/api/searchservice/search-documents#query-parameters) | Filtry se používají s omezujícími vlastnostmi k zúžení výsledků. Můžete také implementovat filtr za stránku, například pro inicializaci stránky s poli konkrétního jazyka. V odkazu na REST API `$filter` je popsán jako parametr dotazu operace hledání dokumentů, ale lze jej použít bez `search` parametru.|

## <a name="know-your-field-attributes"></a>Znát atributy polí

Pokud jste dříve zkontrolovali [základy požadavku na dotaz](search-query-overview.md), můžete si všimnout, že parametry v žádosti o dotaz závisí na tom, jak jsou pole v indexu. Například pro použití v dotazu, filtru nebo pořadí řazení musí být pole *prohledávatelné*, *filtrovatelné* a *seřaditelné*. Podobně mohou být ve výsledcích zobrazeny pouze pole *, která jsou* označena jako načístelné. Jakmile začnete zadávat `search` parametry, a `filter` `orderby` v žádosti, nezapomeňte zkontrolovat atributy při přechodu, abyste se vyhnuli neočekávaným výsledkům.

Na snímku obrazovky pod [ukázkovým indexem hotelů](search-get-started-portal.md)se dá použít jenom poslední dvě pole "části lastrenovationdate" a "hodnocení" v `"$orderby"` klauzuli Only.

![Definice indexu pro ukázku hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definice indexu pro ukázku hotelu")

Popis atributů pole naleznete v tématu [Create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Znát tokeny

Při indexování používá dotazovací modul analyzátor k provádění analýzy textu řetězců a maximalizaci potenciálu pro porovnání v době dotazu. Minimálně řetězce jsou použita, ale můžou také podléhat lemmatizátor nebo předzpracování a zastavovat odstraňování slov. Větší řetězce nebo složená slova jsou obvykle rozdělená prázdnými znaky, pomlčkami nebo pomlčkami a indexované jako samostatné tokeny. 

Tady je bod, který je třeba vzít v úvahu, protože co si myslíte, že váš index obsahuje a co je v něm vlastněné, může být odlišné. Pokud dotazy nevrátí očekávané výsledky, můžete zkontrolovat tokeny vytvořené nástrojem Analyzer prostřednictvím [analyzovat text (REST API)](/rest/api/searchservice/test-analyzer). Další informace o tokenizace a dopadu na dotazy naleznete v části [hledání částečného termínu a vzory se speciálními znaky](search-query-partial-matching.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte lepší přehled o tom, jak je žádost o dotaz vytvořená, zkuste pro praktické prostředí využít následující rychlé starty.

+ [Průzkumník vyhledávání](search-explorer.md)
+ [Dotazování v REST](search-get-started-rest.md)
+ [Dotazování v .NET](search-get-started-dotnet.md)
+ [Dotazování v Pythonu](search-get-started-python.md)
+ [Dotazování v JavaScriptu](search-get-started-javascript.md)