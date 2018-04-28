---
title: Filtry ve službě Azure Search | Microsoft Docs
description: Filtrovat podle identity uživatele zabezpečení, jazyk, geografického umístění nebo číselné hodnoty omezit výsledky vyhledávání na dotazy do služby Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: 9f891dbe3f051f2fb5bfd242830f3c30abede487
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="filters-in-azure-search"></a>Filtry ve službě Azure Search 

A *filtru* obsahuje kritéria pro výběr dokumenty používanými v dotaz služby Azure Search. Nefiltrované vyhledávání zahrnuje všechny dokumenty v indexu. Filtr rozsahy vyhledávací dotaz na podmnožinu dokumentů. Například filtr může omezit fulltextového vyhledávání a pouze tyto produkty s konkrétní značku nebo barvu v bodech ceny nad určitou mezí.

Některé možnosti vyhledávání uložit filtr požadavky v rámci implementace, ale můžete pomocí filtrů můžete kdykoli chcete omezit vyhledávání pomocí *na základě hodnoty* kritéria (oboru vyhledávání typ produktu "knihy" pro kategorii" není fiction"publikováno"Simon a Schuster").

Pokud chcete místo toho cílové vyhledávání na konkrétní data *struktury* (oboru vyhledávání zákaznické recenze pole), existují alternativní metody, které jsou popsané dál.

## <a name="when-to-use-a-filter"></a>Kdy použít filtr

Filtry jsou základní do několika prostředí hledání, včetně "najít v mém okolí", Fasetové navigace a zabezpečení filtry, zobrazující pouze dokumenty, které může uživatel v tématu. Pokud budete implementovat některého z těchto prostředí, je třeba filtr. Je filtr připojené k vyhledávací dotaz, který poskytuje informace o zeměpisné poloze souřadnice vybraná uživatelem nebo ID zabezpečení žadatel kategorie omezující vlastnosti.

Ukázkové scénáře patří:

1. Pomocí filtru řezu indexu na základě hodnot data v indexu. Zadané schéma s města, bydlení typu a veřejných zařízení, můžete vytvořit filtr explicitně vybrat dokumenty, které splňují vaše kritéria (v Praze, Družstevní domy, waterfront). 

  Fulltextové vyhledávání se stejnými vstupy často poskytuje podobné výsledky, ale filtr je přesnější v v tom, že vyžaduje přesnou shodu filtr podmínek obsahu v indexu. 

2. Pokud možnosti vyhledávání se dodává s filtru požadavků pomocí filtru:

 * [Fasetové navigace](search-faceted-navigation.md) používá filtr předat zpět kategorii omezující vlastnosti vybrané uživatelem.
 * Geograficky hledání používá filtr předat souřadnice aktuální umístění v "najít v mém okolí" aplikace. 
 * Filtry zabezpečení předat identifikátory zabezpečení jako kritéria filtru, pokud je nalezena shoda v indexu slouží jako proxy pro přístupová práva k dokumentu.

3. Pokud chcete kritéria vyhledávání na číselné pole, použijte filtr. 

  Číselná pole se dá načíst v dokumentu a může se ve výsledcích hledání, ale nejsou searchable (přičemž podléhá fulltextové vyhledávání) jednotlivě. Pokud potřebujete kritéria výběru na základě číselných dat, použijte filtr.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativní metody zúžíte

Pokud chcete ve výsledcích hledání narrowing vliv, nejsou zvoleného pouze filtry. Tyto možnosti může být lépe vyhovovat, v závislosti na váš cíl:

 + `searchFields` parametr dotazu váže hodnotu vyhledávání konkrétních polí. Například pokud indexu poskytuje samostatná pole pro angličtinu a slovenštinu popisy, můžete searchFields cílové pole, která chcete použít pro fulltextové vyhledávání. 

+ `$select` parametr slouží k určení pole, která chcete zahrnout do výsledku nastavit, efektivně ořezávání odpověď před odesláním do volající aplikace. Tento parametr upřesnění dotazu nebo snížit kolekce dokumentů, ale pokud je vaším cílem granulární odpověď, tento parametr je, zvažte možnost. 

Další informace o buď parametru najdete v tématu [vyhledávání dokumentů > požádat o > parametrů dotazu](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtry v kanálu dotazu

V době dotazů filtru analyzátor přijímá kritéria jako vstup, převede výraz atomic logických výrazů a vytvoří strom filtru, který je pak vyhodnoceného v rámci filtrovatelných polí v indexu.  

Filtrování předchází hledání, které dokumenty, které chcete zahrnout do zpracování příjmu dat pro načtení dokumentu a vyhodnocování relevance kvalifikující. Když byl spárován s hledaný řetězec, filtr efektivně snižuje možnosti útoku operace následné vyhledávání. Při použití samostatného (například pokud řetězec dotazu je prázdný, kdy `search=*`), kritéria filtru, která je jedinou vstup. 

## <a name="filter-definition"></a>Definice filtru

Filtry jsou výrazy OData, kloubové pomocí [dílčí syntaxe OData V4, které jsou podporovány ve službě Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Můžete zadat jeden filtr pro každou **vyhledávání** operaci, ale samotný filtr může obsahovat několik polí, více kritérií, a pokud používáte **IsMatch –** funkce, více výrazů. Ve výrazu vícedílné filtru můžete zadat predikáty v libovolném pořadí. Neexistuje žádné výrazné zvýšení výkonu, pokud se pokusíte změnit uspořádání predikáty v určitém pořadí.

Pevný limit výrazu filtru je maximální limit v žádosti. Celá žádost, která se zaměřuje na filtr, může být maximálně 16 MB pro metodu POST nebo 8 KB pro GET. Doporučené omezení korelovat počet klauzulí ve výrazu filtru. Dobré pravidlo je, že pokud máte stovky klauzule, hrozí běžící do limitu. Doporučujeme, abyste návrhu aplikace tak, že negeneruje filtry velikosti bez vazby.

Následující příklady představují definice Typickým případem filtrů v několik rozhraní API.

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

## <a name="filter-design-patterns"></a>Vzory návrhu filtru

Následující příklady ilustrují několik vzory návrhu pro scénáře filtru. Další nápady najdete v části [syntaxe výrazu OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Samostatné **$filter**, bez řetězce dotazu, užitečné, pokud výraz filtru je možné k plnému určení dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádný lexikální nebo lingvistické analýzy, žádné vyhodnocování a žádné hodnocení. Všimněte si, že je prázdný řetězec pro hledání.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinace řetězec dotazu a **$filter**, kde filtr vytvoří podmnožinu a řetězec dotazu obsahuje vstupy termín pro fulltextové vyhledávání přes filtrované podmnožina. Pomocí filtru s řetězec dotazu je nejběžnější vzor kódu.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Složené dotazy, oddělených "nebo", každou s vlastní kritéria filtru (například "beagles' v 'PSA') nebo"siamese' v 'cat'. NEBO měl s odpovědí z každé z nich zkombinované do jednoho odpověď zpět poslat volající aplikace jednotlivě, vyhodnotí výrazy. Tento vzor návrhu se dosahuje prostřednictvím funkce search.ismatch. Můžete použít verze vyhodnocování (search.ismatch) nebo vyhodnocování verze (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Následnou akci s tyto články komplexní pokyny na konkrétní použití případech:

+ [Filtry omezující vlastnost](search-filters-facets.md)
+ [Filtry jazyka](search-filters-language.md)
+ [Oříznutí zabezpečení](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Požadavky na pole pro filtrování

V rozhraní REST API filtrovatelných je *na* ve výchozím nastavení. Filtrování pole zvýšit velikost indexu; Nezapomeňte nastavit `filterable=FALSE` pro pole, která nemáte v úmyslu použít ve skutečnosti ve filtru. Další informace o nastavení pro definice polí najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

V sadě SDK .NET filtrovatelných je *vypnout* ve výchozím nastavení. Rozhraní API pro nastavení vlastnosti filtrovatelných je [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). V příkladu níže jeho sady v definici BaseRate pole.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Požadavky na novou indexaci

Pokud pole není filtrování a chcete, aby bylo filtrovatelných, budete muset přidat nové pole nebo znovu sestavte stávající pole. Změna definice pole mění fyzická struktura indexu. Ve službě Azure Search jsou všechny cesty povolené přístupu indexovaný pro rychlost rychlé dotazu, který vyžaduje opakované datové struktury, pokud definice pole změnit. 

Opětovné sestavení jednotlivých polí může být nízkou dopad operace, vyžadování pouze operace sloučení odesílá index, a zbytek každý dokument ponechá existující klíč dokumentu a přidružené hodnoty. Pokud dojde k opětovné sestavení požadavek, v následujících tématech pokyny:

 + [Indexování akce pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Indexování akce s použitím rozhraní REST API](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Základy filtru text

Text filtry jsou platné pro pole řetězce, z nichž chcete některé libovolný kolekci dokumentů na základě hodnot v rámci hledání souhrnu pro vyžádání obsahu.

Pro text filtry skládá z řetězce není žádné lexikální analýzy nebo dělení slov, takže srovnání se týkají pouze přesné shody. Předpokládejme například, pole *f* obsahuje "slunečného dne" `$filter=f eq 'Sunny'`neodpovídá, ale `$filter=f eq 'Sunny day'` bude. 

Textové řetězce rozlišují velká a malá písmena. Neexistuje žádné nižší-malá a velká písmena velká slova: `$filter=f eq 'Sunny day'` nebude možné najít, slunečného dne ".


| Přístup | Popis | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkci poskytování čárkami oddělený seznam řetězců pro dané pole. Řetězce tvoří kritéria filtru, které se použijí pro každé pole v oboru pro dotaz. <br/><br/>`search.in(f, ‘a, b, c’)` je sémanticky ekvivalentní `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`kromě toho, že provede mnohem rychlejší, když se seznam hodnot je velká.<br/><br/>Doporučujeme, abyste **Hledat.v** funkce pro [filtrů zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry tvořený nezpracovaný text odpovídal na hodnoty v dané pole. Tento přístup je určený pro rychlost. Můžete očekávat subsecond doba odezvy pro několika set k tisícům hodnot. Když neexistuje žádné explicitní omezení počtu položek, které lze předat funkci, v závislosti na počtu řetězce, které zadáte zvyšování latence. | 
| [Search.IsMatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkce, která umožňuje kombinovat fulltextové vyhledávání operace s operacemi výhradně Booleovský filtr ve stejném výrazu filtru. Umožňuje víc kombinací filtr dotazu v jedné žádosti. Můžete také použít ho *obsahuje* filtr na částečné řetězce v rámci většího řetězce. |  
| [$filter = operátor řetězec pole](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Výraz uživatelem definované skládá z pole, operátory a hodnoty. | 

## <a name="numeric-filter-fundamentals"></a>Základy číselné filtru

Číselná pole nejsou `searchable` v kontextu fulltextové vyhledávání. Pouze řetězce podléhají fulltextové vyhledávání. Například pokud zadáte 99,99 jako hledaný termín, nebude získáte zpět za cenu v $99,99 položky. Místo toho zobrazí se položky, které mají číslo 99 v polí s řetězcem dokumentu. Proto pokud máte číselná data, předpokladem je, že jste se použít pro filtry, včetně rozsahů, omezující vlastnosti, skupiny a tak dále. 

Dokumenty, které obsahují číselná pole (ceny, velikost, SKU, ID) zadejte tyto hodnoty ve výsledcích hledání, pokud je pole označeno `retrievable`. Tady je bod je že tento fulltextové vyhledávání, samotné není použitelný pro číselné pole typů.

## <a name="next-steps"></a>Další postup

Nejprve zkuste **Průzkumník služby Search** portálu k odesílání dotazů s **$filter** parametry. [Real majetku sample index](search-get-started-portal.md) poskytuje zajímavé výsledky pro následující filtrované dotazy při vkládání do panelu Hledat:

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

Chcete-li pracovat s další příklady, přečtěte si téma [syntaxe výrazu filtru OData > Příklady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak úplné textové vyhledávání funguje ve službě Azure Search](search-lucene-query-architecture.md)
+ [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
