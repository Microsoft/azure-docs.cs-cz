---
title: Jak pracovat s výsledky hledání – Azure Search
description: Struktury a seřadit výsledky hledání, získat počet dokumentů a přidání navigaci v obsahu na výsledky hledání ve službě Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8cf65f0ed3ecd5c9a86d6adcdd5defd930522f85
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301549"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Jak pracovat s hledání výsledků ve službě Azure Search
Tento článek obsahuje pokyny pro implementaci standardní elementy na stránce výsledků hledání, jako je například celkového počtu, načtení dokumentu, řazení a navigaci. Možnosti stránky, které přispívají data nebo informace, které výsledky hledání jsou určeny pomocí [hledání v dokumentech](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) požadavky odeslané do služby Azure Search. 

V rozhraní REST API zahrnují požadavky GET command, cestu a parametry dotazu, které informují o co je požadované služby a jak formulovali odpovědi. V sadě .NET SDK je ekvivalentní rozhraní API [DocumentSearchResult třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet).

Několik vzorových kódů patří webového front-endové rozhraní, který najdete tady: [New York City úlohy ukázkovou aplikaci](http://azjobsdemo.azurewebsites.net/) a [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Žádost platná zahrnuje celou řadu prvků, jako je například adresa URL služby a cestu, příkaz HTTP `api-version`, a tak dále. Pro zkrácení jsme oříznut tyto příklady zvýrazňovat syntaxi, která je relevantní pro stránkování. Další informace o žádosti o syntaxi naleznete v tématu [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice). > 
> 

## <a name="total-hits-and-page-counts"></a>Celkový počet přístupů a stránka počítá
Zobrazuje celkový počet výsledků vrácená z dotazu a vrácení výsledků do menších bloků, je nezbytné k prakticky veškerému vyhledávací stránky.

![][1]

Ve službě Azure Search, můžete použít `$count`, `$top`, a `$skip` parametry se mají vracet tyto hodnoty. Následující příklad ukazuje požadavky na ukázky pro celkový počet přístupů na indexu s názvem "onlineCatalog", vrací jako `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Načtení dokumentů ve skupinách 15 a také zobrazit celkový počet přístupů, počínaje první stránka:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Přestránkování výsledky vyžaduje `$top` a `$skip`, kde `$top` Určuje, kolik položek k vrácení v dávce, a `$skip` Určuje, kolik položek pro přeskočení. V následujícím příkladu, každá stránka zobrazuje vedle 15 položky indikován přírůstkové přejde v `$skip` parametru.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout
Na stránce výsledků hledání můžete chtít zobrazit obrázek miniatury, dílčí sadu polí a odkaz na stránku plné verze produktu.

 ![][2]

Ve službě Azure Search, můžete využít `$select` a vyhledávání příkaz k implementaci tohoto prostředí.

Chcete-li vrátí podmnožinu polí pro zobrazení vedle sebe:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Image a mediální soubory nejsou přímo s možností vyhledávání a by měly být uloženy v jiné úložiště platformy, jako je například úložiště objektů Blob v Azure, abyste snížili náklady na. Index a dokumenty definujte pole, které obsahuje adresu URL externího obsahu. Pak můžete pole jako odkaz na obrázek. Adresa URL obrázku musí být v dokumentu.

K načtení stránce popisu produktu **onClick** událost, použijte [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) a zajistěte tak předání klíč dokumentu, který má načíst. Datový typ klíče je `Edm.String`. V tomto příkladu je *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Seřadit podle relevance, hodnocení a ceny
Pořadí řazení často výchozí relevance, ale je společná zpřístupnění alternativní řazení objednávky snadno tak, aby zákazníci můžete rychle změnit pořadí existující výsledky do jiné pořadí řazení.

 ![][3]

Ve službě Azure Search je řazení na základě `$orderby` výrazu pro všechna pole, která jsou indexována jako `"Sortable": true.` `$orderby` je klauzule výrazu OData. Informace o syntaxi najdete v tématu [syntaxe výrazů OData pro filtry a klauzule order by – klauzule](query-odata-filter-orderby-syntax.md).

Je asociován s profily vyhodnocování podle relevance. Vám pomůže výchozí vyhodnocování, které spoléhá na analýzu textu a statistiky řadit pořadí všechny výsledky, s vyšší skóre, které se děje na dokumenty s více nebo silnější shody hledaného termínu.

Alternativní pořadí řazení jsou obvykle přidruženy k **onClick** události, které zpětné volání pro metodu, která vytvoří pořadí řazení. Mějme například tento prvek stránky:

 ![][4]

Vytvoříte metodu, která přijímá možnost vybrané řazení jako vstup a vrátí seřazený seznam pro kritéria přidružené k této možnosti.

 ![][5]

> [!NOTE]
> Při vyhodnocování výchozí je dostačující pro řadu scénářů, doporučujeme místo toho odvození podle relevance na vlastní bodovací profil. Vlastní bodovací profil, který poskytuje způsob, jak boost položky, které jsou užitečné informace pro vaši firmu. Zobrazit [přidat bodovací profily](index-add-scoring-profiles.md) Další informace. 
> 
> 

## <a name="faceted-navigation"></a>Fasetová navigace
Navigace vyhledávání je běžné, na stránce výsledky, často nachází v horní části stránky nebo na straně. Fasetová navigace ve službě Azure Search poskytuje usnadní nastavení hledání na základě předdefinovaných filtrů. Zobrazit [Fasetové navigace ve službě Azure Search](search-faceted-navigation.md) podrobnosti.

## <a name="filters-at-the-page-level"></a>Filtry na úrovni stránky
Pokud návrh vašeho řešení zahrnuje vyhrazené vyhledávací stránky pro určité typy obsahu (například online maloobchodní prodej aplikace, která má oddělení uvedených v horní části stránky), můžete vložit [výrazu filtru](search-filters.md) společně **onClick** událostí otevřít stránku v odkazující stavu. 

Můžete odeslat filtr s nebo bez něj hledaný výraz. Například následující požadavek vyfiltrujete na název značky, vrací pouze dokumenty, které jí odpovídaly.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Zobrazit [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) Další informace o `$filter` výrazy.

## <a name="see-also"></a>Viz také
* [Rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operace indexu](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operace](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Fasetová navigace ve službě Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
