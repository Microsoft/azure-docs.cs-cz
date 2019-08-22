---
title: Filtry pro určení oboru výsledků hledání v indexu – Azure Search
description: Vyfiltrujte podle identity zabezpečení uživatele, jazyka, geografického umístění nebo číselných hodnot, abyste omezili výsledky hledání dotazů v Azure Search, což je hostovaná cloudová vyhledávací služba na Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 49af6f1f535df098aa45cccd7e2d629ff6ccef50
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649845"
---
# <a name="filters-in-azure-search"></a>Filtry ve službě Azure Search 

*Filtr* poskytuje kritéria pro výběr dokumentů používaných v Azure Searchm dotazu. Nefiltrované hledání zahrnuje všechny dokumenty v indexu. Filtr je oborem dotazu hledání na podmnožinu dokumentů. Filtr může například omezit fulltextové vyhledávání jenom na ty produkty, které mají konkrétní značku nebo barvu, za ceny nad určitou prahovou hodnotou.

Některé možnosti vyhledávání ukládají požadavky na filtrování v rámci implementace, ale filtry můžete použít kdykoli, když chcete omezit hledání pomocí kritérií založených na *hodnotách* (vyhledávání oborů na typ produktu knihy) pro kategorii nefiktivní vydavatelé. Simon & Schuster ").

Pokud místo toho cílíte na cílení na konkrétní datové *struktury* (vyhledávání oborů na pole Customers-recenze), existují alternativní metody popsané níže.

## <a name="when-to-use-a-filter"></a>Kdy použít filtr

Filtry jsou základem pro několik vyhledávacích funkcí, jako je "najít blízko jsem", omezující navigaci a filtry zabezpečení, které zobrazují pouze dokumenty, které uživatel může zobrazit. Pokud implementujete některé z těchto prostředí, je vyžadován filtr. Je to filtr připojený k vyhledávacímu dotazu, který poskytuje souřadnice geografického umístění, kategorii omezující vlastnosti vybranou uživatelem nebo ID zabezpečení žadatele.

Příklady scénářů zahrnují následující:

1. Pomocí filtru Vytvořte výřez indexu na základě hodnot dat v indexu. Vzhledem ke schématu s městem, typem ubytování a možností rekreačního programu můžete vytvořit filtr, který explicitně vybere dokumenty, které splňují kritéria (v Seattle, Condos, Waterfront). 

   Fulltextové vyhledávání se stejnými vstupy často vytváří podobné výsledky, ale filtr je přesnější v tom, že vyžaduje přesné porovnání termínu filtru s obsahem v indexu. 

2. Filtr použijte v případě, že se v prostředí vyhledávání nachází požadavek filtru:

   * [Omezující navigace](search-faceted-navigation.md) používá filtr k předání zpět kategorie omezující vlastnosti vybrané uživatelem.
   * Geografické vyhledávání používá filtr k předání souřadnic aktuálního umístění v aplikacích "najít blízko". 
   * Filtry zabezpečení přecházejí identifikátory zabezpečení jako kritéria filtru, kde shoda v indexu slouží jako proxy pro přístupová práva k dokumentu.

3. Filtr použijte v případě, že chcete vyhledávací kritéria pro číselné pole. 

   Číselná pole se dají v dokumentu načíst a můžou se zobrazit ve výsledcích hledání, ale nedají se prohledávat (závisí na úplném vyhledávání textu) jednotlivě. Pokud potřebujete kritéria výběru založená na číslech dat, použijte filtr.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativní metody pro omezení rozsahu

Pokud chcete ve výsledcích hledání zúžit efekt, filtry nejsou vaší volbou. V závislosti na vašem cíli můžou být tyto alternativy lépe vyhovující:

 + `searchFields`parametr dotazu Pegs hledání na konkrétní pole. Například pokud váš index poskytuje samostatná pole pro anglické a španělské popisy, můžete použít searchFields k určení, která pole se mají použít pro fulltextové vyhledávání. 

+ `$select`parametr se používá k určení, která pole se mají zahrnout do sady výsledků, aby se před odesláním do volající aplikace účinně vystřihoval odpověď. Tento parametr neupřesňuje dotaz ani neomezuje kolekci dokumentů, ale pokud je menší odezva na váš cíl, je tento parametr možnost zvážit. 

Další informace o obou parametrech naleznete v tématu [Search documents > Request > dotazu Parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Jak se spouštějí filtry

Analyzátor filtru v době dotazu přijímá kritéria jako vstup, převede výraz na atomické logické výrazy reprezentované jako strom a pak vyhodnotí strom filtru přes pole, která lze filtrovat v indexu.

K filtrování dochází v kombinaci se službou Search a je kvalifikováno, které dokumenty zahrnout do zpracování pro příjem dat pro načtení dokumentů a hodnocení relevance. Při párování s hledaným řetězcem filtr efektivně snižuje sadu odvolání následné operace vyhledávání. Při samostatném použití (například při prázdném řetězci dotazu, kde `search=*`) je kritérium filtru jediným vstupem. 

## <a name="defining-filters"></a>Definování filtrů

Filtry jsou výrazy OData kloubované pomocí podmnožiny [syntaxe OData v4 podporované v Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pro každou operaci **hledání** můžete zadat jeden filtr, ale samotný filtr může zahrnovat více polí, více kritérií a pokud použijete funkci **Match** , více výrazů fulltextového vyhledávání. Ve výrazu filtru s více částmi lze predikáty zadat v libovolném pořadí (v souladu s pravidly priority operátoru). V případě, že se pokusíte změnit uspořádání predikátů v určité sekvenci, neexistuje žádný výrazný nárůst výkonu.

Jedním z omezení výrazu filtru je omezení maximální velikosti požadavku. Celý požadavek, včetně filtru, může mít maximálně 16 MB pro POST nebo 8 KB pro GET. Existuje také omezení počtu klauzulí ve výrazu filtru. Dobrým pravidlem je, že pokud máte stovky klauzulí, riskujete, že se nebudete muset provozovat. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neohraničené velikosti.

Následující příklady znázorňují definice filtru typický v několika rozhraních API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrovat vzorce použití

Následující příklady ilustrují několik vzorů použití pro scénáře filtru. Další nápady najdete v tématu [Syntaxe výrazů OData > příklady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Samostatný **$Filter**bez řetězce dotazu, který je užitečný, když výraz filtru dokáže plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu neexistují žádné lexikální ani lingvistické analýzy, žádné bodování a žádné hodnocení. Všimněte si, že hledaný řetězec je jenom hvězdička, což znamená "odpovídá všem dokumentům".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinace řetězce dotazu a **$Filter**, kde filtr vytvoří podmnožinu, a řetězec dotazu poskytuje termínové vstupy pro fulltextové vyhledávání nad filtrovanou podmnožinou. Nejběžnějším vzorem použití je použití filtru s řetězcem dotazu.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Složené dotazy oddělené znakem "nebo", z nichž každá má vlastní kritéria filtru (například ' Beagles ' v ' pes ' nebo ' Siamese ' v ' Cat '). Výrazy kombinované s `or` jsou vyhodnocovány jednotlivě a sjednocením dokumentů, které odpovídají jednotlivým výrazům, odeslaným zpět v odpovědi. Tento vzor využití se dosahuje prostřednictvím `search.ismatchscoring` funkce. Můžete také použít verzi bez bodování, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Je také `search.ismatchscoring` možné kombinovat fulltextové vyhledávání přes s filtry pomocí `and` místo `or`, ale je `search` to funkčně ekvivalentní použití parametrů a `$filter` v žádosti o vyhledávání. Například následující dva dotazy vytvoří stejný výsledek:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

V těchto článcích najdete kompletní pokyny pro konkrétní případy použití:

+ [Filtry omezující vlastnost](search-filters-facets.md)
+ [Filtry jazyka](search-filters-language.md)
+ [Oříznutí zabezpečení](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Požadavky na pole pro filtrování

V REST API je filtr pro jednoduché pole *ve* výchozím nastavení zapnutý. Filtrovatelné pole zvyšují velikost indexu; Nezapomeňte nastavit `"filterable": false` pro pole, která nechcete ve filtru skutečně použít. Další informace o nastavení definic polí najdete v tématu [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index).

V sadě .NET SDK je filtr ve výchozím nastavení *vypnutý* . Můžete nastavit filtrování pole nastavením [vlastnosti Filter](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) odpovídajícího objektu [pole](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) na `true`. Můžete to provést také deklarativně pomocí [atributu Filter](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). V následujícím příkladu je atribut nastaven na `BaseRate` vlastnost třídy modelu, která je mapována na definici indexu.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Zpřístupnění existujícího pole s filtrem

Existující pole nemůžete upravovat, aby je bylo možné filtrovat. Místo toho je nutné přidat nové pole nebo znovu sestavit index. Další informace o opětovném sestavení indexu nebo přeplňování polí naleznete v tématu [jak znovu sestavit index Azure Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Základy textových filtrů

Textové filtry odpovídají polím řetězců v řetězcích literálů, které zadáte do filtru. Na rozdíl od fulltextového vyhledávání neexistuje žádná lexikální analýza ani zalamování slov pro textové filtry, takže porovnání jsou pouze pro přesné shody. Například Předpokládejme, že pole *f* obsahuje "Slunečné Day", `$filter=f eq 'Sunny'` neodpovídá, ale `$filter=f eq 'sunny day'` bude. 

V textových řetězcích se rozlišují velká a malá písmena. Nejsou k dispozici malá a velká písmena použitach slov: `$filter=f eq 'Sunny day'` "Slunečné Day" (den) se nenalezne.

### <a name="approaches-for-filtering-on-text"></a>Přístupy k filtrování textu

| Přístup | Popis | Kdy je použít |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funkce, která odpovídá poli s odděleným seznamem řetězců. | Doporučuje se pro [filtry zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry, u kterých je potřeba spárovat mnoho nezpracovaných textových hodnot pomocí pole řetězce. Funkce **Search.in** je navržena pro rychlost a je mnohem rychlejší než explicitní porovnání pole s každým řetězcem pomocí `eq` a `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funkce, která umožňuje kombinovat operace fulltextového vyhledávání s přísnými logickými operacemi filtru ve stejném výrazu filtru. | Pokud chcete více kombinací vyhledávacího filtru v jednom požadavku, použijte **Search** . ismatchscoring (nebo jeho ekvivalent pro vyhodnocování, **Search.** ). Můžete ji také použít pro filtr *obsahuje* pro filtrování částečného řetězce v rámci většího řetězce. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uživatelsky definovaný výraz tvořený poli, operátory a hodnotami. | Toto použijte, pokud chcete najít přesné shody mezi řetězcovým polem a řetězcovou hodnotou. |

## <a name="numeric-filter-fundamentals"></a>Základy číselného filtru

Číselná pole `searchable` nejsou v kontextu fulltextového vyhledávání. Pouze řetězce jsou předmětem fulltextového vyhledávání. Pokud například zadáte 99,99 jako hledaný termín, nebudou se za položky vracet ceny za $99,99. Místo toho se zobrazí položky, které mají číslo 99 v polích řetězců dokumentu. Pokud tedy máte číselná data, předpokládáme, že je budete používat pro filtry, včetně rozsahů, omezujících vlastností, skupin a tak dále. 

Dokumenty, které obsahují číselná pole (Price, Size, SKU, ID), poskytují tyto hodnoty ve výsledcích hledání, pokud `retrievable`je pole označeno. Zde je uvedeno, že fulltextové vyhledávání samotné není použitelné pro číselné typy polí.

## <a name="next-steps"></a>Další postup

Nejprve na portálu zkuste **Vyhledat** dotazy pomocí parametrů **$Filter** . [Index reálného vzorku](search-get-started-portal.md) poskytuje zajímavé výsledky pro následující filtrované dotazy při jejich vkládání do panelu hledání:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Chcete-li pracovat s dalšími příklady, viz [Příklady syntaxe výrazů filtru OData > příklady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Viz také:

+ [Jak funguje úplné hledání textu v Azure Search](search-lucene-query-architecture.md)
+ [Hledat dokumenty REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
