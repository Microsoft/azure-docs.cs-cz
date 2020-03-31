---
title: Jak pracovat s výsledky hledání
titleSuffix: Azure Cognitive Search
description: Strukturujte a seřaďte výsledky hledání, získejte počet dokumentů a přidejte navigaci obsahu k výsledkům hledání ve službě Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481598"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracovat s výsledky hledání v Azure Cognitive Search
Tento článek obsahuje pokyny k implementaci standardních prvků stránky s výsledky hledání, jako je například celkový počet, načítání dokumentů, pořadí řazení a navigace. Možnosti související se stránkou, které přispívají data nebo informace k výsledkům hledání jsou určeny prostřednictvím požadavků [vyhledávacího dokumentu](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) odeslaných do služby Azure Cognitive Search. 

V rozhraní REST API požadavky zahrnují příkaz GET, cestu a parametry dotazu, které informují službu o tom, co je požadováno, a jak formulovat odpověď. V sdk .NET SDK ekvivalentní rozhraní API je [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Chcete-li rychle vygenerovat vyhledávací stránku pro svého klienta, prozkoumejte tyto možnosti:

+ Pomocí [generátoru aplikací](search-create-app-portal.md) na portálu vytvořte stránku HTML s vyhledávacím panelem, famitovou navigací a oblastí výsledků.
+ Chcete-li vytvořit funkčního klienta, vytvořte tak první aplikaci v kurzu [C#.](tutorial-csharp-create-first-app.md)

Několik ukázek kódu obsahuje webové front-end rozhraní, které najdete zde: [Demo aplikace New York City Jobs](https://aka.ms/azjobsdemo), [ukázkový kód JavaScriptu s živým demo webem](https://github.com/liamca/azure-search-javascript-samples)a [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Platný požadavek obsahuje řadu prvků, jako je například adresa `api-version`URL služby a cesta, sloveso HTTP a tak dále. Pro stručnost jsme zkrátili příklady, abychom zvýraznili pouze syntaxi, která je relevantní pro stránkování. Další informace o syntaxi požadavku najdete [v tématu Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Celkový počet přístupů a počty stránek

Zobrazení celkového počtu výsledků vrácených z dotazu a vrácení těchto výsledků v menších blocích je zásadní pro prakticky všechny vyhledávací stránky.

![][1]

V Azure Cognitive Search, `$count` `$top`můžete `$skip` použít , a parametry vrátit tyto hodnoty. Následující příklad ukazuje ukázkový požadavek na celkový počet přístupů v `@odata.count`indexu s názvem "online katalog", vrácený jako :

    GET /indexes/online-catalog/docs?$count=true

Načtení dokumentů ve skupinách po 15 a také zobrazení celkového počtu přístupů počínaje první stránkou:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Stránkování výsledky vyžaduje `$top` jak `$skip`a `$top` , kde určuje, kolik položek `$skip` vrátit v dávce a určuje, kolik položek přeskočit. V následujícím příkladu každá stránka zobrazuje dalších 15 položek, které `$skip` jsou označeny přírůstkovými skoky v parametru.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Rozložení

Na stránce s výsledky hledání můžete chtít zobrazit miniaturu, podmnožinu polí a odkaz na celou stránku produktu.

 ![][2]

V Azure Cognitive Search `$select` byste použít a [požadavek rozhraní API vyhledávání](https://docs.microsoft.com/rest/api/searchservice/search-documents) k implementaci tohoto prostředí.

Vrácení podmnožinu polí pro rozložení vedle tlačítek:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Obrázky a mediální soubory nejsou přímo prohledávatelné a měly by být uloženy v jiné platformě úložiště, jako je například úložiště objektů blob Azure, aby se snížily náklady. V rejstříku a dokumentech definujte pole, ve které je uložena adresa URL externího obsahu. Pole pak můžete použít jako odkaz na obrázek. Adresa URL obrázku by měla být v dokumentu.

Chcete-li načíst stránku s popisem produktu pro událost **onClick,** použijte [vyhledávací dokument](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) k předání klíče dokumentu k načtení. Datový typ klíče je `Edm.String`. V tomto příkladu je *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Řazení podle relevance, hodnocení nebo ceny

Řazení objednávek je často ve výchozím nastavení ve výchozím nastavení relevance, ale je běžné, že alternativní objednávky řazení jsou snadno dostupné, aby zákazníci mohli rychle přeřadit stávající výsledky do jiného pořadí.

 ![][3]

V Azure Cognitive Search řazení je `$orderby` založena na výrazu, pro `"Sortable": true.` `$orderby` všechna pole, která jsou indexována jako Klauzule Je OData výraz. Informace o syntaxi naleznete v [tématu Syntaxe výrazu OData pro filtry a klauzule podle pořadí](query-odata-filter-orderby-syntax.md).

Relevance je silně spojena s profily hodnocení. Můžete použít výchozí bodování, které se spoléhá na analýzu textu a statistiky pro pořadí pořadí všech výsledků, s vyšším skóre jít do dokumentů s více nebo silnější zápasy na hledaný výraz.

Alternativní řazení objednávky jsou obvykle spojeny s **událostmi onClick,** které volají zpět na metodu, která vytváří pořadí řazení. Například vzhledem k tomuto prvku stránky:

 ![][4]

Vytvořili byste metodu, která přijme vybranou možnost řazení jako vstup a vrátí seřazený seznam pro kritéria přidružená k této možnosti.

 ![][5]

> [!NOTE]
> Zatímco výchozí bodování je dostatečná pro mnoho scénářů, doporučujeme místo toho založit relevanci na vlastním profilu hodnocení. Vlastní profil hodnocení vám dává způsob, jak zvýšit položky, které jsou výhodnější pro vaši firmu. Další informace najdete [v tématu Přidání profilů hodnocení.](index-add-scoring-profiles.md)
>

## <a name="hit-highlighting"></a>Zvýrazňování položek

Ve výsledcích vyhledávání můžete použít formátování odpovídajících výrazů, což usnadňuje zobrazení shody. Pokyny pro zvýraznění přístupů jsou k dispozici v [žádosti o dotaz](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Formátování se použije na dotazy na celý termín. Dotazy na částečné termíny, jako je například přibližné vyhledávání nebo hledání se zástupnými kódy, které vedou k rozšíření dotazu v modulu, nelze použít zvýraznění přístupů.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Služby vytvořené po 15.7.2020 budou poskytovat různé zvýraznění zkušenosti. Služby vytvořené před tímto datem se nezmění v jejich chování zvýraznění. S touto změnou budou vráceny pouze fráze, které odpovídají úplné frázi dotaz. Také bude možné určit velikost fragmentu vrácené pro zvýraznění.
>
> Při psaní klientského kódu, který implementuje zvýraznění přístupů, uvědomte si tuto změnu. Všimněte si, že to nebude mít vliv na vás, pokud nevytvoříte zcela novou vyhledávací službu.

## <a name="faceted-navigation"></a>Fasetová navigace

Navigace při hledání je běžná na stránce s výsledky, která se často nachází na boku nebo v horní části stránky. V Azure Cognitive Search, famitové navigace poskytuje samořízené vyhledávání na základě předdefinovaných filtrů. Podrobnosti najdete [v tématu Famface navigace v Azure Cognitive Search](search-faceted-navigation.md) podrobnosti.

## <a name="filters-at-the-page-level"></a>Filtry na úrovni stránky

Pokud návrh řešení zahrnoval vyhrazené vyhledávací stránky pro určité typy obsahu (například online maloobchodní aplikace, která má oddělení uvedená v horní části stránky), můžete vložit [výraz filtru](search-filters.md) vedle události **onClick** a otevřít stránku v předem filtrovaném stavu.

Filtr můžete odeslat s hledaným výrazem nebo bez něj. Následující požadavek bude například filtrovat podle názvu značky a vrátí pouze ty dokumenty, které se s ním shodují.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Další informace o `$filter` výrazech najdete v tématu Search Documents [(Azure Cognitive Search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

## <a name="see-also"></a>Viz také

- [Rozhraní REST API služby Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice)
- [Index operace](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operace s dokumenty](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Fazetovaná navigace v Azure Cognitive Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
