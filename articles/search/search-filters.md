---
title: Filtrování výsledků hledání
titleSuffix: Azure Cognitive Search
description: Filtrováním podle identity zabezpečení uživatele, jazyka, geografického umístění nebo číselných hodnot snížíte výsledky hledání na dotazech v Azure Cognitive Search, hostované službě cloudového vyhledávání v Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191020"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtry v Azure Cognitive Search 

*Filtr* poskytuje kritéria pro výběr dokumentů použitých v dotazu Azure Cognitive Search. Nefiltrované hledání zahrnuje všechny dokumenty v indexu. Filtr obory vyhledávací dotaz na podmnožinu dokumentů. Filtr může například omezit fulltextové vyhledávání pouze na produkty, které mají určitou značku nebo barvu, a to v cenových bodech nad určitou prahovou hodnotou.

Některá vyhledávací prostředí ukládají požadavky na filtry jako součást implementace, ale filtry můžete použít kdykoli chcete omezit vyhledávání pomocí kritérií *založených na hodnotách* (vymezení vyhledávání na typ produktu "knihy" pro kategorii "non-fiction" publikovanou "Simon & Schuster").

Pokud je místo toho vaším cílem cílené vyhledávání na konkrétních *datových strukturách* (oborhledání do pole hodnocení zákazníků), existují alternativní metody popsané níže.

## <a name="when-to-use-a-filter"></a>Kdy použít filtr

Filtry jsou základní pro několik možností vyhledávání, včetně "najít v mém", fazetované navigace a filtry zabezpečení, které zobrazují pouze ty dokumenty, které uživatel může vidět. Pokud implementujete některou z těchto zkušeností, je vyžadován filtr. Jedná se o filtr připojený k vyhledávacímu dotazu, který poskytuje souřadnice zeměpisné polohy, kategorii omezující vlastnost vybranou uživatelem nebo ID zabezpečení ožadatele.

Příklady scénářů zahrnují následující:

1. Pomocí filtru můžete index rozdělit na základě hodnot dat v indexu. Vzhledem k schématu s městem, typem bydlení a vybavením můžete vytvořit filtr, který explicitně vybere dokumenty, které splňují vaše kritéria (v Seattlu, byty, nábřeží). 

   Fulltextové vyhledávání se stejnými vstupy často vytváří podobné výsledky, ale filtr je přesnější v tom, že vyžaduje přesnou shodu výrazu filtru s obsahem v indexu. 

2. Pokud je vyhledávací prostředí dodáváno s požadavkem filtru, použijte filtr:

   * [Fazetovaná navigace](search-faceted-navigation.md) používá filtr k předání kategorie omezující vlastnosti vybrané uživatelem.
   * Geografické vyhledávání používá filtr k předání souřadnic aktuální polohy v aplikacích "najít blízko mě". 
   * Filtry zabezpečení projdou identifikátory zabezpečení jako kritéria filtru, kde shoda v indexu slouží jako proxy pro přístupová práva k dokumentu.

3. Pokud chcete kritéria vyhledávání v číselném poli použít, použijte filtr. 

   Číselná pole lze v dokumentu načíst a mohou se zobrazit ve výsledcích hledání, ale nelze je prohledávat (při fulltextovém vyhledávání) jednotlivě. Pokud potřebujete kritéria výběru na základě číselných dat, použijte filtr.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativní metody pro omezení rozsahu

Pokud chcete ve výsledcích hledání efekt zužujícího efekt, filtry nejsou jedinou volbou. Tyto alternativy by mohly být vhodnější, v závislosti na vašem cíli:

 + `searchFields`kolíky parametrů dotazu vyhledávají do určitých polí. Pokud například index obsahuje samostatná pole pro popisy angličtiny a španělštiny, můžete pomocí funkce searchFields cílit na pole, která mají být používána pro fulltextové vyhledávání. 

+ `$select`parametr se používá k určení, která pole mají být zahrnuta do sady výsledků, a účinně ořízne odpověď před jejím odesláním volající aplikaci. Tento parametr neupřesňuje dotaz nebo zmenšuje kolekci dokumentů, ale pokud je vaším cílem menší odpověď, je tento parametr možností, kterou je třeba zvážit. 

Další informace o obou parametrech naleznete [v tématu Hledání dokumentů > parametry požadavku > dotazu](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Způsob provádění filtrů

V době dotazu analyzátor filtru přijímá kritéria jako vstup, převádí výraz na atomické logické výrazy reprezentované jako strom a poté vyhodnotí strom filtru přes filtrovatelná pole v indexu.

Filtrování probíhá v tandemu s hledáním, kvalifikační, které dokumenty zahrnout do následného zpracování pro načítání dokumentů a vyhodnocování relevance. Při spárování s vyhledávacím řetězcem filtr účinně snižuje sadu odvolání následné operace hledání. Při použití samostatně (například když je `search=*`řetězec dotazu prázdný kde ), kritéria filtru je jediný vstup. 

## <a name="defining-filters"></a>Definování filtrů
Filtry jsou výrazy OData, které jsou formulovány pomocí [podmnožiny syntaxe OData V4 podporované v Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Pro každou **vyhledávací** operaci můžete zadat jeden filtr, ale samotný filtr může obsahovat více polí, více kritérií a pokud použijete funkci **ismatch,** více fulltextových vyhledávacích výrazů. Ve vícesložkovém výrazu filtru můžete zadat predikáty v libovolném pořadí (v závislosti na pravidlech priority operátoru). Neexistuje žádný znatelný zisk ve výkonu, pokud se pokusíte změnit uspořádání predikáty v určitém pořadí.

Jedním z omezení výrazu filtru je maximální limit velikosti požadavku. Celý požadavek včetně filtru může být maximálně 16 MB pro POST nebo 8 KB pro GET. Je také limit na počet klauzulí ve výrazu filtru. Dobrým pravidlem je, že pokud máte stovky klauzulí, jste v nebezpečí, že běží do limitu. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neomezené velikosti.

Následující příklady představují prototypové definice filtrů v několika souborech API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Vzorce použití filtru

Následující příklady ilustrují několik vzorců použití pro scénáře filtrů. Další nápady naleznete v [tématu Syntaxe výrazu OData > Příklady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Samostatný **$filter**bez řetězce dotazu, užitečné, pokud je výraz filtru schopen plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádná lexikální nebo jazyková analýza, žádné vyhodnocování a žádné pořadí. Všimněte si, že vyhledávací řetězec je jen hvězdička, což znamená "porovnat všechny dokumenty".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Kombinace řetězce dotazu a **$filter**, kde filtr vytvoří podmnožinu, a řetězec dotazu poskytuje termín vstupy pro fulltextové vyhledávání nad filtrovanou podmnožinou. Přidání termínů (divadla na pěší vzdálenosti) zavádí výsledky vyhledávání ve výsledcích, kde jsou dokumenty, které nejlépe odpovídají termínům, hodnoceny výše. Použití filtru s řetězcem dotazu je nejběžnější vzor použití.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Složené dotazy, oddělené "nebo", každý s vlastními kritérii filtru (například "bígl" v "pes" nebo "siamská" v "kočka"). Výrazy v `or` kombinaci s jsou vyhodnocovány jednotlivě, se sjednocením dokumentů odpovídajících každému výrazu odeslaným zpět v odpovědi. Tento vzor použití je `search.ismatchscoring` dosaženo prostřednictvím funkce. Můžete také použít verzi bez `search.ismatch`vyhodnocování .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Je také možné kombinovat fulltextové `search.ismatchscoring` vyhledávání pomocí `and` filtrů pomocí `or`, ale to `search` je `$filter` funkčně ekvivalentní použití a parametry v požadavku vyhledávání. Například následující dva dotazy vytvářejí stejný výsledek:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Postupujte podle těchto článků pro komplexní pokyny pro konkrétní případy použití:

+ [Filtry omezujících vlastností](search-filters-facets.md)
+ [Filtry jazyka](search-filters-language.md)
+ [Oříznutí zabezpečení](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Požadavky na pole pro filtrování

V rozhraní REST API *je* filtrovatelná ve výchozím nastavení zapnutá pro jednoduchá pole. Filtrovatelná pole zvětšují velikost indexu. Nezapomeňte nastavit `"filterable": false` pole, která ve filtru skutečně nechcete použít. Další informace o nastavení definic polí naleznete v [tématu Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

V sdk .NET je filtrovatelný ve výchozím nastavení *vypnutý.* Pole můžete nastavit jako filtrovatelné nastavením [vlastnosti IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) odpovídajícího objektu [Pole](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) na `true`. Můžete to také provést deklarativně pomocí [isfilterable atribut](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). V níže uvedeném příkladu je `BaseRate` atribut nastaven na vlastnost třídy modelu, která se mapuje na definici indexu.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Nastavení existujícího pole jako filtrovatelného

Existující pole nelze upravit tak, aby byla filtrovatelná. Místo toho je třeba přidat nové pole nebo znovu vytvořit index. Další informace o opětovném sestavení indexu nebo znovunaplnění polí najdete v [tématu Jak znovu sestavit index Azure Cognitive Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Základy filtru textu

Textové filtry odpovídají řetězcovým polím s literálovými řetězci, které zadáte ve filtru. Na rozdíl od fulltextového vyhledávání neexistuje žádná lexikální analýza nebo rozdělení slov pro textové filtry, takže porovnání jsou pouze pro přesné shody. Předpokládejme například, že pole *f* `$filter=f eq 'Sunny'` obsahuje "slunečný `$filter=f eq 'sunny day'` den", neodpovídá, ale bude. 

Textové řetězce rozlišují malá a velká písmena. Neexistuje žádný dolní plášť velkých slov: `$filter=f eq 'Sunny day'` nenajde "slunečný den".

### <a name="approaches-for-filtering-on-text"></a>Přístupy pro filtrování textu

| Přístup | Popis | Kdy je použít |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funkce, která porovnává pole s odděleným seznamem řetězců. | Doporučeno pro [filtry zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry, u kterých je třeba spárovat mnoho nezpracovaných textových hodnot s řetězcovým polem. Funkce **search.in** je určena pro rychlost a je mnohem rychlejší než `eq` `or`explicitní porovnání pole s každým řetězcem pomocí a . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funkce, která umožňuje kombinovat fulltextové vyhledávací operace s přísně logickými operacemi filtru ve stejném výrazu filtru. | Použijte **search.ismatch** (nebo jeho bodování ekvivalent, **search.ismatchscoring**) pokud chcete více kombinací hledání a filtru v jednom požadavku. Můžete také použít pro *filtr obsahuje* filtr pro filtrování na částečný řetězec v rámci větší řetězec. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uživatelem definovaný výraz složený z polí, operátorů a hodnot. | Tuto hodnotu použijte, pokud chcete najít přesné shody mezi řetězcové pole a řetězcovou hodnotou. |

## <a name="numeric-filter-fundamentals"></a>Základy číselného filtru

Číselná pole `searchable` nejsou v kontextu fulltextového vyhledávání. Fulltextové vyhledávání podléhá pouze řetězcům. Pokud například jako hledaný výraz zadáte 99,99, nezískáte zpět položky v ceně 99,99 USD. Místo toho by se zobrazí položky, které mají číslo 99 v řetězcových polích dokumentu. Pokud tedy máte číselná data, předpokládá se, že je budete používat pro filtry, včetně rozsahů, omezujících stran, skupin a tak dále. 

Dokumenty obsahující číselná pole (cena, velikost, skladová položka, ID) poskytují `retrievable`tyto hodnoty ve výsledcích hledání, pokud je pole označeno . Jde o to, že samotné fulltextové vyhledávání není použitelné pro typy číselných polí.

## <a name="next-steps"></a>Další kroky

Nejprve zkuste **Průzkumníka vyhledávání** na portálu odeslat dotazy s **$filter** parametry. [Index ukázkového indexu nemovitostí](search-get-started-portal.md) poskytuje zajímavé výsledky pro následující filtrované dotazy, když je vložíte do vyhledávacího panelu:

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

Chcete-li pracovat s dalšími příklady, přečtěte si informace [o syntaxi výrazu filtru OData > příklady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Viz také

+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Rozhraní API pro vyhledávání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
