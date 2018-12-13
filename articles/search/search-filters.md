---
title: Filtry pro vymezení výsledky hledání v rejstříku – Azure Search
description: Filtrovat podle identity zabezpečení uživatele, jazyka, geografického umístění nebo číselné hodnoty ke snížení výsledky hledání na dotazy ve službě Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d99196e231d122fcb0e707d30aed4d3b3eb2b89d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310347"
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

+ `$select` parametr slouží k určení nastavení pole, která chcete zahrnout do výsledku efektivně ořezávání odpověď před odesláním ho do volající aplikace. Tento parametr upřesnění dotazu nebo snižte kolekce dokumentů, ale pokud je vaším cílem podrobnou odpověď, je tento parametr, zvažte možnost. 

Další informace o obou parametrů najdete v tématu [vyhledávání dokumentů > požádat o > parametrů dotazu](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtry v kanálu dotazu

V době zpracování dotazu filtr analyzátor přijímá kritéria jako vstup, převede výraz na Atomický logických výrazů a sestavení stromu filtrů, které se pak vyhodnocuje přes filtrovatelných polích v indexu.  

Filtrování předchází hledání opravňujících dokumenty, které mají být zahrnuty zpracování příjmu dat pro načtení dokumentu a hodnocení závažnosti. V kombinaci s hledaný řetězec filtru efektivně snižuje útoku na další vyhledávací operace. Při použití samostatně (například pokud řetězec dotazu je prázdný where `search=*`), kritéria filtru, která je výhradně vstup. 

## <a name="filter-definition"></a>Definice filtru

Filtry jsou výrazy OData, kloubové pomocí [podmnožinou syntaxe OData V4, které jsou podporované ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Můžete zadat jeden filtr pro každý **hledání** operace, ale samotný filtr může obsahovat více polí, více kritérií, a pokud používáte **ismatch** funkce, více výrazů. Ve výrazu vícedílný filtru můžete zadat predikáty v libovolném pořadí. Neexistuje žádné výrazné zvýšení výkonu, pokud se pokusíte změnit uspořádání predikáty v určitém pořadí.

Vynucené omezení na výraz filtru je maximální limit požadavku. Celou žádost, včetně filtrů, může být maximálně 16 MB pro metodu POST nebo velikosti 8 KB u metody GET. Doporučené omezení korelovat víc klauzulí ve výrazu filtru. Základním pravidlem je, že pokud máte stovky klauzule vám hrozí, že narazíte omezení. Doporučujeme, abyste návrhu vaší aplikace tak negeneruje filtry neomezené velikosti.

Následující příklady představují definice typický filtrů v několik rozhraní API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

```

## <a name="filter-design-patterns"></a>Filtr vzory návrhu

Následující příklady znázorňují několika způsoby návrhu pro scénáře filtru. Další informace najdete v článku [syntaxe výrazů OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Samostatné **$filter**, bez řetězce dotazu, užitečné, pokud výraz filtru je možné k plnému určení dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádná lexikální nebo jazyková analýza, žádné bodování a žádné řazení. Všimněte si, že se že hledaný řetězec je prázdný.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinace řetězec dotazu a **$filter**, kde filtr vytvoří dílčí a řetězec dotazu obsahuje vstupy termín pro fulltextové vyhledávání přes filtrované podmnožinu. Pomocí filtru s řetězcem dotazu je nejběžnější vzorek kódu.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Složené dotazy, které jsou odděleny "nebo", každý s vlastní kritéria filtru (například "beagles' v"pes") nebo"siamese"v"cat". NEBO měl výrazy jsou vyhodnocovány jednotlivě, s odpovědí z každé z nich zkombinovat do jedné odpovědi odeslána zpět na volající aplikace. Tento vzor návrhu se dosahuje prostřednictvím funkce search.ismatch. Můžete použít verzi vyhodnocování (search.ismatch) nebo vyhodnocení verzi (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Zpracování v těchto článcích pro komplexní pokyny konkrétní případy použití:

+ [Filtry omezující vlastnost](search-filters-facets.md)
+ [Filtry jazyka](search-filters-language.md)
+ [Oříznutí zabezpečení](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Požadavky na pole pro filtrování

V rozhraní REST API, filtrovat je *na* ve výchozím nastavení. Filtrovatelná pole zvýšit velikost indexu; Nezapomeňte nastavit `filterable=FALSE` pro pole, které nechcete použít ve skutečnosti ve filtru. Další informace o nastavení pole definic najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

V sadě .NET SDK dají filtrovat je *vypnout* ve výchozím nastavení. Rozhraní API pro nastavení vlastnosti filterable je [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). V příkladu níže, jeho nastavení v definici BaseRate pole.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Přeindexování požadavky

Pokud chcete provést filtrování pole se nedají filtrovat, budete muset přidat nové pole, nebo znovu vytvořit existující pole. Fyzická struktura indexu mění definici pole mění. Ve službě Azure Search jsou všechny cesty byl povolen přístup indexovány pro rychlost rychlé dotazu, což vyžaduje opětovné sestavení datových struktur při změně definice pole. 

Znovu sestavit jednotlivá pole může být operace malý vliv, vyžaduje pouze operace sloučení, která odesílá existující klíč dokumentu a přidružené hodnoty do indexu, zůstanou nedotčena zbytek každého dokumentu. Pokud narazíte na požadavek na opětovné sestavení, najdete v článku instrukce prostřednictvím následujících odkazů:

 + [Indexování akce pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Indexování akce pomocí rozhraní REST API](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Základy text filtru

Textové filtry jsou platné pro pole řetězce, ze kterých chcete získat některé libovolné kolekce dokumentů na základě hodnot v rámci ve vyhledávacím korpusu služby.

Pro textové filtry skládá z řetězce neexistuje žádný provést lexikální analýzu nebo dělení slov, takže pro pouze přesné shody. Předpokládejme například, pole *f* obsahuje "Slunečný den", `$filter=f eq 'Sunny'`neodpovídá, ale `$filter=f eq 'Sunny day'` bude. 

Textové řetězce jsou malá a velká písmena. Neexistuje žádné nižší-malá a velká písmena slov velká: `$filter=f eq 'Sunny day'` nenajde "Slunečný den".


| Přístup | Popis | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkci poskytování čárkami oddělený seznam řetězců pro dané pole. Řetězce tvoří kritéria filtru, které se použijí pro všechna pole v oboru pro dotaz. <br/><br/>`search.in(f, ‘a, b, c’)` je sémanticky ekvivalentní `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, s tím rozdílem, že provede mnohem rychleji, je-li seznam hodnot velké.<br/><br/>Doporučujeme, abyste **Hledat.v** fungovat [filtry zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry se skládá z nezpracované text, který má být porovnáváno podle hodnot v daném poli. Tento přístup je určený pro rychlost. Můžete očekávat subsecond doba odezvy pro stovky až tisíce hodnoty. Neplatí žádné explicitní limitu počtu položek, které můžete předat do funkce, zvyšuje latenci poměru k počtu řetězců, které zadáte. | 
| [Search.IsMatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkce, která umožňuje kombinovat fulltextové vyhledávání operace s operacemi výhradně Booleovský filtr ve stejném výrazu filtru. Umožňuje více kombinací filtr dotazu v jedné žádosti. Můžete ji také používat *obsahuje* filtr na částečné řetězce v rámci většího řetězce. |  
| [$filter = řetězec pole – operátor](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uživatelem definovaný výraz se skládá z pole, operátory a hodnoty. | 

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

Práci s další příklady naleznete v tématu [syntaxe výrazů filtru OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
