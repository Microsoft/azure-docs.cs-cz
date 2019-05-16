---
title: Filtry pro vymezení výsledky hledání v rejstříku – Azure Search
description: Filtrovat podle identity zabezpečení uživatele, jazyka, geografického umístění nebo číselné hodnoty ke snížení výsledky hledání na dotazy ve službě Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1871fee2734d347ff54d6aa70d90d1c28bd1f6f1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597277"
---
# <a name="filters-in-azure-search"></a>Filtry ve službě Azure Search 

A *filtr* obsahuje kritéria pro výběr dokumenty používané v dotazu Azure Search. Nefiltrované hledání zahrnuje všechny dokumenty v indexu. Filtr oborů Vyhledávací dotaz na podmnožinu dokumentů. Například filtr může omezit fulltextové vyhledávání pouze tyto produkty s konkrétní značku nebo barvu na cenových bodech nad určitou mezí.

Některé možnosti vyhledávání uložit filtr požadavky jako součást provádění, ale můžete použít filtry, kdykoli budete chtít omezit vyhledávání pomocí *založené na hodnotách* kritéria (oboru hledání na typ produktu "knihy" kategorie" bez fiction"publikováno"Simon & Schuster").

Pokud místo toho se cílené hledání na konkrétní data *struktury* (oboru hledání pole zapracováním připomínek zákazníků), existuje alternativní metody, je popsáno níže.

## <a name="when-to-use-a-filter"></a>Kdy použít filtr

Filtry jsou základní několik možnosti vyhledávání, včetně "najít poblíž", Fasetové navigace a zabezpečení filtry, které zobrazují pouze dokumenty, které uživatele je povoleno. Pokud se rozhodnete implementovat některou z těchto možností, filtr je povinný. Je filtr připojit k vyhledávací dotaz, který poskytuje informace o zeměpisné poloze souřadnice kategorie omezující vlastnost vybrané uživatele nebo ID zabezpečení žadatel.

Ukázkové scénáře zahrnují následující:

1. Pomocí filtru do průřezu zahrnout indexu na základě hodnot dat v indexu. Zadané schéma s Město, bydlení typu a zařízení, můžete například vytvořit filtr pro explicitní výběr dokumenty, které splňují vaše kritéria (v Praze, condos, waterfront). 

   Fulltextové vyhledávání se stejnými vstupy často vytváří podobné výsledky, ale v tom, že vyžaduje přesnou shodu období filtr obsahu v indexu je přesnější filtru. 

2. Pokud vyhledávání se dodává s filtru požadavků pomocí filtru:

   * [Fasetová navigace](search-faceted-navigation.md) používá filtr, aby předávání zpátky kategorie omezující vlastnost vybrané uživatelem.
   * Geografické vyhledávání používá filtr, aby předat souřadnice aktuálního umístění v "najít poblíž" aplikace. 
   * Filtry zabezpečení předat identifikátory zabezpečení jako kritéria filtru, kde se shoda v indexu slouží jako proxy pro přístupová práva k dokumentu.

3. Pokud chcete kritéria vyhledávání pro číselné pole, použijte filtr. 

   Číselná pole se retrievable v dokumentu a může zobrazit ve výsledcích hledání, ale nejsou prohledávatelná (v souladu s fulltextové vyhledávání) samostatně. Pokud potřebujete kritéria pro výběr na základě číselných dat, použijte filtr.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativní metody pro snížení oboru

Pokud chcete efekt zužující ve výsledcích vyhledávání, filtry nejsou pouze podle vašeho výběru. Tyto možnosti může být lépe vyhovovat, v závislosti na vaší cíle:

 + `searchFields` parametr dotazu váže hodnotu hledání konkrétních polí. Například pokud indexu poskytuje samostatných polích pro angličtinu a slovenštinu popisy, můžete použít searchFields cílit na pole, která chcete použít pro fulltextové vyhledávání. 

+ `$select` parametr slouží k určení nastavení pole, která chcete zahrnout do výsledku efektivně ořezávání odpověď před odesláním ho do volající aplikace. Tento parametr upřesnění dotazu nebo snižte kolekce dokumentů, ale pokud je vaším cílem menší odpověď, je tento parametr, zvažte možnost. 

Další informace o obou parametrů najdete v tématu [vyhledávání dokumentů > požádat o > parametrů dotazu](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Způsob provedení filtrů

V době zpracování dotazu filtr analyzátor přijímá kritéria jako vstup, převede výraz na Atomický logické výrazy reprezentována jako strom a pak vyhodnotí strom filtru přes filtrovatelných polích v indexu.

Filtrování probíhá v kombinaci s hledání opravňujících dokumenty, které mají být zahrnuty zpracování příjmu dat pro načtení dokumentu a hodnocení závažnosti. V kombinaci s hledaný řetězec filtru efektivně snižuje spojené s vracením sadu následných vyhledávací operace. Při použití samostatně (například pokud řetězec dotazu je prázdný where `search=*`), kritéria filtru, která je výhradně vstup. 

## <a name="defining-filters"></a>Definování filtrů

Filtry jsou výrazy OData, kloubové pomocí [podmnožinou syntaxe OData V4, které jsou podporované ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Můžete zadat jeden filtr pro každý **hledání** operace, ale samotný filtr může obsahovat více polí, více kritérií, a pokud používáte **ismatch** funkce, více výrazů fulltextové vyhledávání. Ve výrazu vícedílný filtru můžete zadat predikáty v libovolném pořadí (dle pravidel priority operátorů). Neexistuje žádné výrazné zvýšení výkonu, pokud se pokusíte změnit uspořádání predikáty v určitém pořadí.

Jedním z omezení pro výraz filtru je maximální limit velikosti požadavku. Celou žádost, včetně filtrů, může být maximálně 16 MB pro metodu POST nebo velikosti 8 KB u metody GET. Platí omezení na počet klauzulí ve výrazu filtru. Základním pravidlem je, že pokud máte stovky klauzule vám hrozí, že narazíte omezení. Doporučujeme, abyste návrhu vaší aplikace tak negeneruje filtry neomezené velikosti.

Následující příklady představují definice typický filtrů v několik rozhraní API.

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

## <a name="filter-usage-patterns"></a>Filtr vzory využití

Následující příklady znázorňují několik vzory využití pro scénáře filtru. Další informace najdete v článku [syntaxe výrazů OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Samostatné **$filter**, bez řetězce dotazu, užitečné, pokud výraz filtru je možné k plnému určení dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádná lexikální nebo jazyková analýza, žádné bodování a žádné řazení. Všimněte si, že se že hledaný řetězec je právě hvězdičky, což znamená, že "odpovídat všechny dokumenty".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinace řetězec dotazu a **$filter**, kde filtr vytvoří dílčí a řetězec dotazu obsahuje vstupy termín pro fulltextové vyhledávání přes filtrované podmnožinu. Pomocí filtru s řetězcem dotazu je nejběžnější vzor používání.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Složené dotazy, které jsou odděleny "nebo", každý s vlastní kritéria filtru (například "beagles' v"pes") nebo"siamese"v"cat". Výrazů v kombinaci s `or` se vyhodnocují zvlášť, s sjednocení dokumentů, které vyhovují každý výraz odeslána zpět v odpovědi. Tento vzor využití se dosahuje prostřednictvím `search.ismatchscoring` funkce. Můžete také použít verzi vyhodnocování `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Je také možné kombinovat fulltextové vyhledávání přes `search.ismatchscoring` pomocí filtrování pomocí `and` místo `or`, ale to je funkčně ekvivalentní k použití `search` a `$filter` parametrů v požadavku hledání. Například následující dva dotazy přinesou stejný výsledek:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Zpracování v těchto článcích pro komplexní pokyny konkrétní případy použití:

+ [Filtry omezující vlastnost](search-filters-facets.md)
+ [Filtry jazyka](search-filters-language.md)
+ [Oříznutí zabezpečení](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Požadavky na pole pro filtrování

V rozhraní REST API, filtrovat je *na* ve výchozím nastavení pro jednoduché pole. Filtrovatelná pole zvýšit velikost indexu; Nezapomeňte nastavit `"filterable": false` pro pole, které nechcete použít ve skutečnosti ve filtru. Další informace o nastavení pole definic najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

V sadě .NET SDK dají filtrovat je *vypnout* ve výchozím nastavení. Pole můžete filtrovat díky nastavení [IsFilterable vlastnost](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) k odpovídající položce [pole](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) objektu `true`. Můžete také uděláte deklarativně pomocí [IsFilterable atribut](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). V následujícím příkladu je atribut nastaven na `BaseRate` vlastnost třídu modelu, který se mapuje na definici indexu.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Provádění filterable existující pole

Nelze upravovat existující pole, aby se daly filterable. Místo toho budete muset přidat nové pole, nebo znovu sestavte index. Další informace o nové sestavení indexu nebo opětovného vyplnění polí naleznete v tématu [postup nového sestavení indexu Azure Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Základy text filtru

Filtry textu spárovat pole řetězce pro řetězcové literály, které zadáte do pole filtrovat. Na rozdíl od fulltextové vyhledávání neexistuje žádný provést lexikální analýzu nebo dělení slov textové filtry, takže pro pouze přesné shody. Předpokládejme například, pole *f* obsahuje "Slunečný den", `$filter=f eq 'Sunny'` neodpovídá, ale `$filter=f eq 'sunny day'` bude. 

Textové řetězce jsou malá a velká písmena. Neexistuje žádné nižší-malá a velká písmena slov velká: `$filter=f eq 'Sunny day'` nenajde "Slunečný den".

### <a name="approaches-for-filtering-on-text"></a>Přístupy k filtrování podle textu

| Přístup | Popis | Kdy je použít | 
|----------|-------------|-------------|
| [search.in](query-odata-filter-orderby-syntax.md) | Funkce, která odpovídá poli proti oddělený seznam řetězců. | Doporučuje se pro [filtry zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry, které je potřeba mít odpovídající pole řetězce mnoho nezpracované textové hodnoty. **Hledat.v** funkce je určená pro rychlost a je mnohem rychlejší než explicitní porovnávání na pole pro každý řetězec pomocí `eq` a `or`. | 
| [search.ismatch](query-odata-filter-orderby-syntax.md) | Funkce, která umožňuje kombinovat fulltextové vyhledávání operace s operacemi výhradně Booleovský filtr ve stejném výrazu filtru. | Použití **search.ismatch** (nebo ekvivalent bodování **search.ismatchscoring**) Pokud chcete, aby více kombinací vyhledávací filtr v jedné žádosti. Můžete ji také používat *obsahuje* filtr na částečné řetězce v rámci většího řetězce. |
| [$filter = řetězec pole – operátor](query-odata-filter-orderby-syntax.md) | Uživatelem definovaný výraz se skládá z pole, operátory a hodnoty. | Použijte ho, když chcete najít přesné shody mezi hodnotu řetězce a pole řetězce. |

## <a name="numeric-filter-fundamentals"></a>Základy číselný filtr

Číselná pole nejsou `searchable` v kontextu fulltextové vyhledávání. Pouze řetězce jsou v souladu s fulltextové vyhledávání. Například pokud zadáte 99,99 jako hledaný termín, nezískáte zpět počítají $99,99 položky. Místo toho zobrazí položky, které mají číslo 99 v polích řetězce dokumentu. Proto pokud máte číselná data, předpokladem je, použijeme je pro filtry, včetně rozsahů, omezující vlastnosti, skupiny a tak dále. 

Dokumenty, které obsahují číselná pole (cena, velikost, SKU, ID) zadejte tyto hodnoty ve výsledcích hledání, pokud je označeno pole `retrievable`. Tady je bod je že hledání textu v plném znění samotné neplatí pro číselné pole typů.

## <a name="next-steps"></a>Další postup

Nejprve zkuste **Průzkumníka služby Search** na portálu a odesílání dotazů s **$filter** parametry. [Reálné. místo ukázkového indexu](search-get-started-portal.md) poskytuje zajímavé výsledky pro následující filtrovat dotazy, když je vložte do panelu hledání:

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

Práci s další příklady naleznete v tématu [syntaxe výrazů filtru OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
