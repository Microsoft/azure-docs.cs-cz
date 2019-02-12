---
title: Syntaxe výrazů OData pro filtry a klauzule order by – klauzule – Azure Search
description: Filtr a klauzule order by výraz syntaxe OData pro Azure Search dotazy.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: c6dbd95cfd17f5ce49245fcadea299c5a0dfd582
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008444"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Syntaxe výrazů OData pro filtry a klauzulemi klauzule order by ve službě Azure Search

Služba Azure Search podporuje podmnožinou syntaxe výrazů OData pro **$filter** a **$orderby** výrazy. Filtrovací výrazy jsou vyhodnocovány během zpracování dotazu parsování, omezení hledání konkrétních polí nebo přidání kritéria shody využít při kontrolách indexu. Výrazy Order by se použijí jako krok následného zpracování v rámci sady výsledků. Filtry a výrazy order by jsou zahrnuty v žádosti o dotaz týkajícími se syntaxe OData, která je nezávislá [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) použít v syntaxi dotazu **hledání** parametr. Tento článek obsahuje referenční dokumentaci pro OData výrazy použité ve výrazech řazení a filtry.

## <a name="filter-syntax"></a>Řiďte se syntaxí filtru

A **$filter** výrazu můžete provést samostatná jako plně vyjádřené dotaz nebo upřesnění dotazu, který má další parametry. Následující příklady znázorňují několik klíčových scénářů. V prvním příkladu je filtr látku dotazu.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11  
    {  
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"  
    }  
```

Dalším běžným případem použití je filtry zkombinovat "faceting", kde filtr snižuje styčné plochy dotaz na základě výběru uživatele řízené omezující vlastnost navigace:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11  
    {  
      "search": "test",  
      "facets": [ "tags", "baseRate,values:80|150|220" ],  
      "filter": "rating eq 3 and category eq 'Motel'"  
    }  
```

### <a name="filter-operators"></a>Operátory filtru  

-   Logické operátory (a, nebo ne).  

-   Výrazy porovnání (`eq, ne, gt, lt, ge, le`). Při porovnávání řetězců se rozlišují malá a velká písmena.  

-   Konstanty z podporovaných [modelu Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) typů (EDM) (viz [podporované datové typy &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) seznam podporovaných typů). Konstanty kolekci typů nejsou podporovány.  

-   Odkazy na názvy polí. Pouze `filterable` pole můžete používat ve výrazech filtru.  

-   `any` bez parametrů. To testuje, jestli pole typu `Collection(Edm.String)` neobsahuje žádné prvky.  

-   `any` a `all` s podporou omezené lambda výrazu. 
    
    -   `any/all` jsou podporovány na pole typu `Collection(Edm.String)`. 
    
    -   `any` jde použít jenom s výrazy rovnosti jednoduché nebo `search.in` funkce. Jednoduché výrazy skládat z porovnání mezi službou jedno pole a hodnota literálu, třeba `Title eq 'Magna Carta'`.
    
    -   `all` jde použít jenom s jednoduchou nerovnost výrazy nebo `not search.in`.   

-   Geoprostorové funkce `geo.distance` a `geo.intersects`. `geo.distance` Funkce vrací vzdálenost v kilometrech mezi dvěma body, jeden je pole a druhý je konstantní předanou v rámci filtru. `geo.intersects` Funkce vrátí hodnotu true, pokud je daný bod v rámci dané mnohoúhelníku, kde je bod je pole a mnohoúhelník je zadána jako konstanta předanou v rámci filtru.  

    Mnohoúhelník je dvojrozměrné povrch uložené jako posloupnost body definující ohraničující vyzvánět (viz následující příklad). Mnohoúhelník musí zavřou, což znamená, první a poslední bod sad musí být stejné. [Bodů mnohoúhelníku musí být v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    `geo.distance` vrací vzdálenost v kilometrech ve službě Azure Search. Tím se liší od jiných služeb, které podporují operace geoprostorové OData, které obvykle vrací vzdálenost v metrech.  

    > [!NOTE]  
    >  Při použití geo.distance ve filtru, musí porovnat vzdálenost vrácené funkcí s použitím konstantní `lt`, `le`, `gt`, nebo `ge`. Operátory `eq` a `ne` nejsou podporovány při porovnávání vzdálenosti. To je třeba správné použití geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

-   `search.in` Funkce testuje, jestli daný řetězec pole je rovna jednomu z daného seznamu hodnot. To lze také v některé nebo všechny porovnat jednu hodnotu pole řetězce kolekce s daný seznam hodnot. Rovnost mezi poli a každá hodnota v seznamu je určena stejným způsobem jako pro malá a velká písmena způsobem `eq` operátor. Proto výraz jako `search.in(myfield, 'a, b, c')` je ekvivalentní `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, s tím rozdílem, že `search.in` předá mnohem lepší výkon. 

    První parametr `search.in` funkce je odkaz na pole řetězce (nebo proměnnou rozsahu přes kolekce pole řetězce v případě, kde `search.in` je použit uvnitř `any` nebo `all` výraz). Druhý parametr je řetězec obsahující seznam hodnot, oddělené mezerami nebo čárkami. Pokud je potřeba použít oddělovače kromě mezery a čárky, protože hodnoty obsahovat tyto znaky, můžete zadat volitelný třetí parametr pro `search.in`. 

    Tento třetí parametr je řetězec, ve kterém každý znak řetězci, nebo podmnožina tento řetězec je považován za oddělovačem při analýze seznamu hodnot ve druhém parametru.

    > [!NOTE]   
    >  Některé scénáře vyžadují porovnání pole pro velký počet konstantní hodnoty. Implementace oříznutí zabezpečení pomocí filtrů například může vyžadovat porovnání pole ID dokumentu na seznam ID, ke kterým je žádajícího uživatele udělit oprávnění ke čtení. Ve scénářích takto důrazně doporučujeme používat `search.in` funkci místo složitější disjunkce rovnosti výrazů. Například použít `search.in(Id, '123, 456, ...')` místo `Id eq 123 or Id eq 456 or ....`. 

>  Pokud používáte `search.in`, můžete očekávat, že když druhý parametr obsahuje seznam stovky nebo tisíce hodnoty doby odezvy sekunda. Všimněte si, že není žádná explicitní omezený počet položek, které můžete předat `search.in`, i když jsou dál omezené na žádost o maximální velikosti. Však latence bude růst počet hodnot.

-   `search.ismatch` Funkce vyhodnocuje vyhledávací dotaz jako součást výrazu filtru. V sadě výsledků se vrátí dokumenty, které odpovídají vyhledávacímu dotazu. K dispozici jsou následující přetížení této funkce:
    - `search.ismatch(search)`
    - `search.ismatch(search, searchFields)`
    - `search.ismatch(search, searchFields, queryType, searchMode)`

    kde: 
  
    - `search`: vyhledávací dotaz (buď [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxe dotazu). 
    - `queryType`: "jednoduchý" nebo "úplné", výchozí hodnota je "jednoduchý". Určuje, jaký jazyk dotazu byla použita v `search` parametru.
    - `searchFields`: čárkami oddělený seznam prohledávatelná pole pro hledání, výchozí hodnota je všechna prohledatelná pole v indexu.    
    - `searchMode`: "libovolné" nebo "vše", výchozí hodnota je "žádný". Určuje, zda některé nebo všechny hledané termíny musí odpovídat k sečtení dokument jako shoda.

    Všechny výše uvedené parametry jsou ekvivalentní odpovídající [parametry požadavku hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents).

-   `search.ismatchscoring` Funkce, jako je třeba `search.ismatch` fungovat, vrátí hodnotu true pro dokumenty, které odpovídají vyhledávacímu dotazu předaného jako parametr. Rozdíl mezi nimi je, že skóre relevance odpovídajících dokumentů `search.ismatchscoring` dotazu přispěje k celkové skóre dokument, zatímco v případě `search.ismatch`, skóre dokumentu se nezmění. Následující přetížení této funkce je k dispozici s parametry, které jsou stejné jako `search.ismatch`:
    - `search.ismatchscoring(search)`
    - `search.ismatchscoring(search, searchFields)`
    - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  `search.ismatch` a `search.ismatchscoring` funkce jsou plně ortogonální sebou a zbytek algebraický filtru. To znamená, že obě funkce je možné ve stejném výrazu filtru. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geoprostorové dotazy a pokrývání uzlů 180th poledníků mnohoúhelníky  
 Pro mnoho geoprostorové dotazy knihovny formulování dotazu, který zahrnuje 180th poledníků (téměř datová hranice) je buď off-limits nebo vyžaduje alternativní řešení, jako je například rozdělení na dva na obou stranách poledníky mnohoúhelníku.  

 Ve službě Azure Search, geoprostorové dotazy, které obsahují zeměpisnou délku 180 stupňů bude fungovat podle očekávání, pokud je obdélníkový tvar dotazu a vaše souřadnice zarovnají k rozložení mřížky podél zeměpisnou šířku a délku (například `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). V opačném případě pro neobdélníkových nebo nezarovnaných tvary, zvažte rozdělení mnohoúhelníku přístup.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Omezení velikosti filtru 

 Existují omezení velikosti a složitosti filtru výrazů, které můžete odeslat do služby Azure Search. Omezení jsou zhruba podle počtu klauzule ve výrazu filtru. Základním pravidlem je, že pokud máte stovky klauzule vám hrozí, že narazíte omezení. Doporučujeme, abyste návrhu vaší aplikace tak negeneruje filtry neomezené velikosti.  


## <a name="filter-examples"></a>Příklady filtrů  

 Najdete všechny hotels základní sazba menší než 100 USD, který jsou hodnoceny dosahovalo nebo přesahovalo 4:  

```  
$filter=baseRate lt 100.0 and rating ge 4  
```  

 Najdete všechny hotels než "Roach Motel", které mají byla renovovanou od 2010:  

```  
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z  
```  

 Najdete všechny hotels základní sazba menší než 200 USD, které mají byla renovovanou od 2012 DateTime literál, který obsahuje informace o časovém pásmu pro Tichomořský běžný čas:  

```  
$filter=baseRate lt 200 and lastRenovationDate ge 2012-01-01T00:00:00-08:00  
```  

 Najdete všechny hotely, které mají parkovací zahrnuté a neumožňují kouření:  

```  
$filter=parkingIncluded and not smokingAllowed  
```  

 \- OR -  

```  
$filter=parkingIncluded eq true and smokingAllowed eq false  
```  

 Najdete všechny hotely, které jsou luxusní nebo parkovací patří a mají hodnocení 5:  

```  
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5  
```  

 Najdete všechny hotely se značkou "Wi-Fi" (kde každý hotelu má značky, které jsou uloženy v poli Collection(Edm.String)):  

```  
$filter=tags/any(t: t eq 'wifi')  
```  

 Najdete všechny hotels bez značky "motel":  

```  
$filter=tags/all(t: t ne 'motel')  
```  

 Najdete všechny hotely se žádné značky:  

```  
$filter=tags/any()  
```  

 Najdete všechny hotely v rámci 10 kilometrů bodu daného odkazu (kde umístění je pole typu Edm.GeographyPoint):  

```  
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10  
```  

 Najdete všechny hotely v rámci daného zobrazení popsány mnohoúhelníku (kde umístění je pole typu Edm.GeographyPoint). Všimněte si, že je uzavřena mnohoúhelníku (první a poslední bod sad musí být stejná) a [body musí být uvedeny v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```  
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')  
```  

 Najít všechny hotely, které buď nemají žádnou hodnotu v poli "Popis", nebo explicitně je hodnota nastavena na hodnotu null:  

```  
$filter=description eq null  
```  

Najít všechny hotels s názvem rovná buď Roach motel "nebo"Rozpočtu hotel"):  

```  
$filter=search.in(name, 'Roach motel,Budget hotel', ',') 
```

Najít všechny hotels s názvem rovná buď Roach motel "nebo rozpočtu hotelu oddělené" | "):  

```  
$filter=search.in(name, 'Roach motel|Budget hotel', '|') 
```

Nalezení hotelů všechny značky wifi nebo "fondu":  

```  
$filter=tags/any(t: search.in(t, 'wifi, pool'))  
```

Najdete všechny hotels bez značky "motel" ani "kabině":  

```  
$filter=tags/all(t: not search.in(t, 'motel, cabin'))  
```  

Najdete dokumenty obsahující slovo "waterfront". Tento dotaz filtru je stejný jako [požadavek hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) s `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Hledání dokumentů s slovo "hostel" a hodnocení větší nebo rovna 4 nebo dokumenty obsahující slovo "motel" a hodnocení roven hodnotě 5. Všimněte si, že tuto žádost nelze vyjádřen bez `search.ismatchscoring` funkce.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Hledání dokumentů bez slovo "luxusní".

```
$filter=not search.ismatch('luxury') 
```

Hledání dokumentů pomocí fráze "oceánu zobrazení" nebo hodnocení rovna 5. `search.ismatchscoring` Dotazu se provede pouze s poli `hotelName` a `description`.
Všimněte si, dokumenty, které odpovídají druhé klauzule disjunkce vrátí se příliš – hotels s hodnocením rovna 5. Aby bylo jasné těchto dokumentech nebyly shodují s některým z Vyhodnocená části výrazu, vrátí se skóre rovna hodnotě nula.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Hledání dokumentů, kde jsou podmínky "hotel" a "letiště" v rámci 5 slov od sebe navzájem v popisu hotelu a kde kouření nepovoluje. Tento dotaz používá [úplný jazyk dotazu Lucene](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Syntaxe klauzule ORDER by

**$Orderby** parametr přijímá čárkami oddělený seznam až 32 výrazů formuláře `sort-criteria [asc|desc]`. Kritéria řazení může být název `sortable` pole nebo volání na buď `geo.distance` nebo `search.score` funkce. Můžete použít buď `asc` nebo `desc` explicitně zadávat pořadí řazení. Výchozí pořadí je vzestupně.

Pokud máte více dokumentů stejná kritéria řazení a `search.score` nepoužívá – funkce (např. Pokud můžete seřadit podle číselná `rating` pole a dokumenty tři všechny mají hodnocení 4), vazby nebudou fungovat podle dokumentu skóre v sestupném pořadí. Pokud dokument skóre, které se shodují (například, když není žádný dotaz fulltextové vyhledávání v požadavku je zadaná), pak relativní řazení vázané dokumentů je neurčitý.
 
Můžete zadat více kritéria řazení. Pořadí výrazy Určuje konečné řazení. Například, chcete-li seřadit sestupně podle skóre, za nímž následuje hodnocení, syntaxe by `$orderby=search.score() desc,rating desc`.

Syntaxe pro `geo.distance` v **$orderby** je stejný jako v **$filter**. Při použití `geo.distance` v **$orderby**, ke kterému se vztahuje pole musí být typu `Edm.GeographyPoint` a musí být také `sortable`.  

Syntaxe pro `search.score` v **$orderby** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry.  
 

## <a name="order-by-examples"></a>Příklady klauzule ORDER by

Seřadit vzestupně podle základní sazba hotels:

```
$orderby=baseRate asc
```

Seřadit sestupně podle hodnocení, pak vzestupně podle základní sazba hotels (mějte na paměti, že ascending je výchozí hodnota):

```
$orderby=rating desc,baseRate
```

Seřadit sestupně podle hodnocení, pak vzestupně podle vzdálenosti od dané souřadnice hotels:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Seřadit v sestupném pořadí tak, že search.score a hodnocení a potom ve vzestupném pořadí podle vzdálenosti od dané souřadnice tak, aby mezi dva hotels s identické hodnocení na ten nejbližší je uvedená jako první hotels:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Nepodporovaná syntaxe OData

-   Aritmetické výrazy  

-   Funkce (s výjimkou vzdálenost a protíná geoprostorové funkce)  

-   `any/all` pomocí libovolného lambda výrazů  

## <a name="see-also"></a>Další informace najdete v tématech  

+ [Fasetová navigace ve službě Azure Search](search-faceted-navigation.md) 
+ [Filtry ve službě Azure Search](search-filters.md) 
+ [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Jednoduchá syntaxe dotazů ve službě Azure Search](query-simple-syntax.md)   