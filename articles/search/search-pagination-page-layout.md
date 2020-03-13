---
title: Jak pracovat s výsledky hledání
titleSuffix: Azure Cognitive Search
description: Struktura a řazení výsledků hledání, získání počtu dokumentů a přidání navigace obsahu do výsledků hledání v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: e83ecb3888ed4b19933233f3ab511d1e86fb37af
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136986"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracovat s výsledky hledání v Azure Kognitivní hledání
Tento článek poskytuje informace o tom, jak implementovat standardní prvky stránky výsledků hledání, jako jsou celkové počty, načítání dokumentů, objednávky řazení a navigace. Možnosti související s stránkou, které přidávají data nebo informace do výsledků hledání, jsou zadány prostřednictvím požadavků [dokumentu vyhledávání](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) odesílaných do služby Azure kognitivní hledání. 

V REST API požadavky zahrnují parametry GET příkazu, Path a Query, které informují o tom, co se požaduje, a jak formulovat odpověď. V sadě .NET SDK je ekvivalentní rozhraní API [třídou DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Chcete-li rychle vygenerovat stránku vyhledávání pro klienta, Prozkoumejte tyto možnosti:

+ Pomocí [generátoru aplikací](search-create-app-portal.md) na portálu můžete vytvořit stránku HTML pomocí panelu hledání, omezující navigace a oblasti výsledků.
+ Při vytváření funkčního klienta postupujte podle pokynů [v části Vytvoření první aplikace v C# ](tutorial-csharp-create-first-app.md) kurzu.

Několik ukázek kódu zahrnuje webové front-endové rozhraní, které můžete najít tady: aplikace pro ukázkovou [úlohu v New Yorku](https://aka.ms/azjobsdemo), [ukázkový kód JavaScriptu s živým ukázkovým webem](https://github.com/liamca/azure-search-javascript-samples)a [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Platná žádost obsahuje počet prvků, jako je například adresa URL služby a cesta, příkaz HTTP, `api-version`atd. V případě zkrácení jsme tyto příklady vyhodili a zvýraznili jenom syntaxi, která je relevantní pro stránkování. Další informace o syntaxi žádosti najdete v tématu [rozhraní REST API pro Azure kognitivní hledání](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Celkový počet přístupů a počty stránek

Zobrazuje celkový počet výsledků vrácených dotazem a následné vrácení těchto výsledků do menších bloků je zásadní pro prakticky všechny vyhledávací stránky.

![][1]

V Azure Kognitivní hledání k vrácení těchto hodnot použít parametry `$count`, `$top`a `$skip`. Následující příklad ukazuje vzorový požadavek na celkový počet přístupů v indexu s názvem "online-Catalog", vrácený jako `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

Načíst dokumenty ve skupinách po 15 a zobrazit také celkový počet přístupů počínaje první stránkou:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Výsledky stránkování vyžadují `$top` i `$skip`, kde `$top` určuje počet položek, které se mají vrátit v dávce, a `$skip` určuje počet položek, které se mají přeskočit. V následujícím příkladu každá stránka zobrazuje další 15 položek, které jsou označeny přírůstkovým skokem v parametru `$skip`.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Rozložení

Na stránce výsledků hledání možná budete chtít zobrazit miniaturu, podmnožinu polí a odkaz na plnou stránku produktu.

 ![][2]

V Azure Kognitivní hledání byste pro implementaci tohoto prostředí použili `$select` a [vyhledávací požadavek rozhraní API](https://docs.microsoft.com/rest/api/searchservice/search-documents) .

Vrácení podmnožiny polí pro dlaždicové rozložení:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Image a mediální soubory se nedají přímo prohledávat a měly by se ukládat na jiné paměťové platformě, jako je Azure Blob Storage, aby se snížily náklady. V indexu a v dokumentech Definujte pole, ve kterém se uloží adresa URL externího obsahu. Pak můžete pole použít jako odkaz na obrázek. Adresa URL obrázku by měla být v dokumentu.

Chcete-li načíst stránku s popisem produktu pro událost události **Click** , použijte [vyhledávací dokument](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) k předání klíče dokumentu k načtení. Datový typ klíče je `Edm.String`. V tomto příkladu je *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Seřadit podle relevance, hodnocení nebo ceny

Objednávky řazení jsou často ve výchozím nastavení důležité, ale je běžné, že je možné snadno dostupné alternativní objednávky řazení, aby zákazníci mohli rychle rozložit stávající výsledky do jiného pořadí řazení.

 ![][3]

V Azure Kognitivní hledání je řazení založené na výrazu `$orderby`, pro všechna pole, která jsou indexována jako `"Sortable": true.` klauzule `$orderby`, je výraz OData. Informace o syntaxi naleznete v tématu [syntaxe výrazu OData pro filtry a klauzule ORDER by](query-odata-filter-orderby-syntax.md).

Relevance je silně přidružená k profilům vyhodnocování. Můžete použít výchozí hodnocení, které spoléhá na analýzu textu a statistické údaje na řazení všech výsledků s vyšším skóre na dokumenty s více nebo silnějšími shodami s hledaným termínem.

Alternativní objednávky řazení jsou obvykle přidruženy k událostem **Click** , které volají zpět do metody, která sestavuje pořadí řazení. Například s ohledem na tento prvek stránky:

 ![][4]

Vytvořili jste metodu, která přijme vybranou možnost řazení jako vstup a vrátí seřazený seznam pro kritéria přidružená k této možnosti.

 ![][5]

> [!NOTE]
> I když je výchozí hodnocení dostačující pro mnoho scénářů, doporučujeme místo toho použít pro vlastní profil vyhodnocování relevanci. Vlastní profil bodování vám dává možnost posílit položky, které jsou pro vaši firmu výhodnější. Další informace najdete v tématu [přidání profilů vyhodnocování](index-add-scoring-profiles.md) .
>

## <a name="hit-highlighting"></a>Zvýrazňování položek

Ve výsledcích hledání můžete použít formátování na odpovídající podmínky, což usnadňuje umístění shody. Pokyny pro zvýraznění přístupů jsou k dispozici v [žádosti o dotaz](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Formátování se aplikuje na všechny výrazy. Dotazy na částečné výrazy, například hledání přibližné nebo zástupné znaky, které vedou k rozbalování dotazů v modulu, nemůžou používat zvýrazňování přístupů.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>Fasetová navigace

Navigace vyhledávání je společná na stránce výsledků, která se často nachází na straně nebo v horní části stránky. V Azure Kognitivní hledání omezující navigace poskytuje samoobslužné vyhledávání na základě předdefinovaných filtrů. Podrobnosti najdete v tématu věnovaném navýšení [Navigace v Azure kognitivní hledání](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filtry na úrovni stránky

Pokud váš návrh řešení zahrnoval vyhrazené vyhledávací stránky pro konkrétní typy obsahu (například v online maloobchodní aplikaci, která obsahuje oddělení uvedená v horní části stránky), můžete vložit [výraz filtru](search-filters.md) vedle události události události **Click** pro otevření stránky v předem filtrovaném stavu.

Můžete odeslat filtr pomocí vyhledávacího výrazu nebo bez něj. Například následující požadavek bude filtrovat podle názvu značky a vrátí pouze ty dokumenty, které odpovídají.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Další informace o výrazech `$filter` najdete v tématu [hledání dokumentů (Azure kognitivní hledání API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

## <a name="see-also"></a>Viz také

- [REST API Kognitivní hledání Azure](https://docs.microsoft.com/rest/api/searchservice)
- [Operace indexu](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operace dokumentů](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Omezující navigace v Azure Kognitivní hledání](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
