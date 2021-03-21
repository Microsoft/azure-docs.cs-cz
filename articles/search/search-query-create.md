---
title: Vytvoření dotazu
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit žádost o dotaz v Kognitivní hledání, které nástroje a rozhraní API se mají použít pro testování a kódování a jak se dotazování rozhodnutí začíná s návrhem indexu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 5a89e9ae05b0733c865d537ffeb1714d3b3ebef1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489357"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Vytváření dotazů v Azure Kognitivní hledání

Pokud vytváříte dotaz poprvé, Tento článek popisuje přístupy a metody pro nastavení dotazů. Také zavádí požadavek na dotaz a vysvětluje, jak atributy polí a jazykové analyzátory mohou ovlivnit výsledky dotazů.

## <a name="whats-a-query-request"></a>Co je požadavek na dotaz?

Dotaz je požadavek jen pro čtení na kolekci docs v rámci jednoho vyhledávacího indexu. Určuje, že parametr Search obsahuje výraz dotazu, který se skládá z výrazů, frází uzavřených v uvozovkách a operátorů.

Další parametry poskytují další definice dotazu a odpovědi. Například obory ' searchFields ' se dotazují na konkrétní pole, ' SELECT ' určuje, která pole jsou vrácena ve výsledcích a ' count ' vrátí počet shod nalezených v indexu.

Následující příklad poskytuje obecnou představu o požadavku na dotaz tím, že zobrazuje podmnožinu dostupných parametrů. Další informace o složení dotazů naleznete v tématu [typy a kompozice dotazů](search-query-overview.md) a [hledání dokumentů (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Zvolit klienta

Budete potřebovat nástroj, jako je Azure Portal nebo post, nebo kód, který vytvoří instanci klienta dotazů pomocí rozhraní API. Pro prvotní vývoj a testování testování konceptů doporučujeme Azure Portal nebo rozhraní REST API.

### <a name="permissions"></a>Oprávnění

Všechny operace, včetně požadavků na dotazy, budou fungovat v [klíči rozhraní API pro správu](search-security-api-keys.md), ale požadavky na dotazy můžou volitelně používat [klíč rozhraní API pro dotazy](search-security-api-keys.md#create-query-keys). Důrazně se doporučuje používat klíče rozhraní API pro dotazování. Pro každou službu můžete vytvořit až 50 a přiřadit různé klíče různým aplikacím.

V Azure Portal přístup k nástrojům, průvodcům a objektům vyžaduje členství v roli přispěvatel nebo výše služby. 

### <a name="use-azure-portal-to-query-an-index"></a>Použití Azure Portal k dotazování indexu

[Průzkumník služby Search (portál)](search-explorer.md) je rozhraní pro dotazy v Azure Portal, které spouští dotazy pro indexy v základní vyhledávací službě. Interně portál provádí [hledání požadavků dokumentů](/rest/api/searchservice/search-documents) , ale nemůže vyvolat automatické dokončování, návrhy nebo vyhledávání dokumentů. 

Můžete vybrat jakýkoli index a REST API verzi, včetně verze Preview. Řetězec dotazu může používat jednoduchou nebo úplnou syntaxi s podporou pro všechny parametry dotazu (Filter, SELECT, searchFields a tak dále). Při otevření indexu můžete na portálu pracovat s Průzkumníkem služby Search společně s definicí JSON indexu na souběžných kartách pro snadný přístup k atributům polí. Zkontroluje, jaká pole jsou při testování dotazů prohledávatelné, seřaditelné, filtrovatelné a plošky.

### <a name="use-a-rest-client"></a>Použití klienta REST

Post i Visual Studio Code (s rozšířením pro Azure Kognitivní hledání) můžou fungovat jako klient dotazů. Pomocí kteréhokoli nástroje se můžete připojit k vaší vyhledávací službě a odesílat požadavky na [hledání dokumentů (REST)](/rest/api/searchservice/search-documents) . Řada kurzů a příkladů ukazuje klienty REST pro dotazování na indexování. 

V některém z těchto článků se dozvíte o jednotlivých klientech (zahrnuje i pokyny k dotazům):

+ [Vytvoření indexu vyhledávání pomocí REST a předzálohovacího](search-get-started-rest.md)
+ [Začínáme s Visual Studio Code a Azure Kognitivní hledání](search-get-started-vs-code.md)

Každý požadavek je samostatný, takže musíte zadat koncový bod, název indexu a verzi rozhraní API pro každý požadavek. Další vlastnosti, typ obsahu a klíč rozhraní API se předávají v hlavičce žádosti. Další informace najdete v tématu [vyhledávání dokumentů (REST)](/rest/api/searchservice/search-documents) pro nápovědu k formulování požadavků na dotazy.

### <a name="use-an-sdk"></a>Použití sady SDK

Pro Kognitivní hledání sady SDK Azure implementují všeobecně dostupné funkce. V takovém případě můžete k dotazování indexu použít kteroukoli sadu SDK. Všechny z nich poskytují **SearchClient** , který obsahuje metody pro interakci s indexem, od načtení indexu pomocí vyhledávacích dokumentů, aby bylo možné formulovat požadavky na dotazy.

| Azure SDK | Klient | Příklady |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Vyberte typ dotazu: jednoduchý | kompletní

Pokud je dotaz fulltextovým vyhledáváním, bude použit analyzátor dotazu ke zpracování veškerého textu, který je předán jako hledané výrazy a fráze. Azure Kognitivní hledání nabízí dva analyzátory dotazů. 

+ Jednoduchý analyzátor rozumí [jednoduché syntaxi dotazů](query-simple-syntax.md). Tento analyzátor byl vybrán jako výchozí pro rychlost a efektivitu v textových dotazech bezplatného formuláře. Syntaxe podporuje běžné operátory hledání (a, ne) pro vyhledávání termínů a frází a hledání předpony ( `*` jako v "moře *" pro Seattle a Seaside). Obecně doporučujeme, abyste nejprve vyzkoušeli jednoduchý analyzátor a pak přesunuli na úplný analyzátor, pokud požadavky aplikace volají na výkonnější dotazy.

+ [Úplná syntaxe dotazů Lucene](query-Lucene-syntax.md#bkmk_syntax), která je povolená, když přidáváte `queryType=full` žádost, je založená na [analyzátoru Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Úplná syntaxe a jednoduchá syntaxe se překrývají v rozsahu, který podporuje stejnou předponu a logické operace, ale plná syntaxe poskytuje více operátorů. V plném rozsahu existuje více operátorů pro logické výrazy a další operátory pro pokročilé dotazy, jako je hledání přibližných hodnot, hledání pomocí zástupných znaků, hledání blízkosti a regulární výrazy.

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

Pokud jste dříve zkontrolovali [typy a kompozici dotazů](search-query-overview.md), můžete si uvědomit, že parametry žádosti o dotaz závisí na tom, jak jsou pole v indexu. Například pro použití v dotazu, filtru nebo pořadí řazení musí být pole *prohledávatelné*, *filtrovatelné* a *seřaditelné*. Podobně mohou být ve výsledcích zobrazeny pouze pole *, která jsou* označena jako načístelné. Jakmile začnete zadávat `search` parametry, a `filter` `orderby` v žádosti, nezapomeňte zkontrolovat atributy při přechodu, abyste se vyhnuli neočekávaným výsledkům.

Na snímku obrazovky pod [ukázkovým indexem hotelů](search-get-started-portal.md)se dá použít jenom poslední dvě pole "části lastrenovationdate" a "hodnocení" v `"$orderby"` klauzuli Only.

![Definice indexu pro ukázku hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definice indexu pro ukázku hotelu")

Popis atributů pole naleznete v tématu [Create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Znát tokeny

Při indexování používá vyhledávací modul analyzátor k analýze textu řetězců a maximalizaci potenciálu pro porovnání v době dotazu. Minimálně řetězce jsou použita, ale můžou také podléhat lemmatizátor nebo předzpracování a zastavovat odstraňování slov. Větší řetězce nebo složená slova jsou obvykle rozdělená prázdnými znaky, pomlčkami nebo pomlčkami a indexované jako samostatné tokeny. 

Tady je bod, který je třeba vzít v úvahu, protože co si myslíte, že váš index obsahuje a co je v něm vlastněné, může být odlišné. Pokud dotazy nevrátí očekávané výsledky, můžete zkontrolovat tokeny vytvořené nástrojem Analyzer prostřednictvím [analyzovat text (REST API)](/rest/api/searchservice/test-analyzer). Další informace o tokenizace a dopadu na dotazy naleznete v části [hledání částečného termínu a vzory se speciálními znaky](search-query-partial-matching.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte lepší přehled o tom, jak požadavky na dotazy fungují, zkuste následující rychlé starty pro praktické prostředí.

+ [Průzkumník vyhledávání](search-explorer.md)
+ [Dotazování v REST](search-get-started-rest.md)
+ [Dotazování v .NET](search-get-started-dotnet.md)
+ [Dotazování v Pythonu](search-get-started-python.md)
+ [Dotazování v JavaScriptu](search-get-started-javascript.md)