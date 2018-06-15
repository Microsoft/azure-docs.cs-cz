---
title: Postup stránkování výsledků vyhledávání ve službě Azure Search | Microsoft Docs
description: Stránkování ve službě Azure Search, hostované cloudové vyhledávací službě v Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 516760031918c667b39cc8b3dd94d91c42623efc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186874"
---
# <a name="how-to-page-search-results-in-azure-search"></a>Jak v Azure Search stránkovat výsledky hledání
Tento článek obsahuje pokyny týkající se používání rozhraní API REST služby vyhledávání Azure k implementaci standardní elementy stránka s výsledky hledání, například celkový počet, načtení dokumentu, pořadí řazení a navigace.

V každém případě dál uvedených souvisejících se stránkami možnosti, které přispívají data nebo informace na stránce s výsledky hledání jsou zadaná pomocí [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) požadavky odeslané do služby Azure Search. Žádosti o zahrnovat příkaz GET, cestu a parametry dotazu, které informovat o tom, co je požadované služby a postup formulovali odpovědi.

> [!NOTE]
> Počet elementů, například adresa URL služby a cesta, příkaz HTTP, zahrnuje žádost platná `api-version`a tak dále. Jako stručný výtah jsme oříznut příklady ke zvýraznění syntaxe, které se týkají stránkování. Najdete v tématu [rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice) dokumentace podrobnosti o žádosti o syntaxi.
> 
> 

## <a name="total-hits-and-page-counts"></a>Celkový počet přístupů a počty stránky
Zobrazuje celkový počet výsledků vrácených z dotazu a pak vrácení výsledků v menší bloky dat, je nezbytné, aby prakticky všechny stránky vyhledávání.

![][1]

Ve službě Azure Search, můžete použít `$count`, `$top`, a `$skip` parametry, které vrátí tyto hodnoty. Následující příklad ukazuje ukázková žádost pro celkový počet přístupů, vrátí jako `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Dokumenty ve skupinách 15 načíst a také se zobrazují celkový počet přístupů do začínající na první stránce:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Přestránkování výsledky vyžaduje obě `$top` a `$skip`, kde `$top` Určuje, kolik položek k vrácení v dávce, a `$skip` Určuje, kolik položek Pokud chcete vynechat. V následujícím příkladu každý stránka zobrazuje vedle 15 položky indikován přírůstkové přeskočí v `$skip` parametr.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Rozložení
Na stránce výsledky hledání můžete zobrazit miniatury, podmnožinu pole a odkaz na stránku plnou verzi produktu.

 ![][2]

Ve službě Azure Search, byste použili `$select` a příkaz vyhledávání implementovat toto prostředí.

Chcete-li vrátit podmnožinu pole vedle sebe rozložení:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Bitové kopie a mediálních souborů nejsou přímo s možností vyhledávání a by měly být uložené v jiné platformy úložiště, jako je například úložiště objektů Blob v Azure, jak snížit náklady. V indexu a dokumenty zadejte pole, které obsahuje adresu URL externí obsah. Pak můžete toto pole jako odkaz na obrázek. Adresu URL do bitové kopie musí být v dokumentu.

Načtení stránky produktu popis pro **onClick** událostí, použijte [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) předávání v klíči dokumentu pro načtení. Datový typ klíče je `Edm.String`. V tomto příkladu je *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Řazení podle relevance, hodnocení a ceny
Pořadí řazení často výchozí nastavení relevance, ale je běžné, aby alternativní řazení objednávky snadno dostupné, aby zákazníci můžete rychle změnit pořadí existující výsledky do různých rank pořadí.

 ![][3]

Ve službě Azure Search, řazení podle `$orderby` výrazu pro všechna pole, které jsou uloženy jako `"Sortable": true.`

Relevance důrazně souvisí s profily vyhodnocování. Můžete vyhodnocování výchozí, což závisí na text analýzy a statistiky zařadit všechny výsledky, pořadí s vyšší skóre přejít do dokumentů s více nebo silnější odpovídá na hledaný termín.

Alternativní pořadí řazení obvykle souvisejí s **onClick** události, které zpětné volání pro metodu, která vytvoří pořadí řazení. Například uděleno tento element stránky:

 ![][4]

Vytvoříte metodu, která přijme jako vstup možnost vybrané řazení a vrátí seřazený seznam pro kritéria spojená s tuto možnost.

 ![][5]

> [!NOTE]
> Při vyhodnocování výchozí stačí pro mnoho scénářů, doporučujeme místo vytvoření relevance na základě vlastní profil vyhodnocování. Vlastní profil vyhodnocování poskytuje způsob, jak nárůst položky, které jsou více užitečné k vaší firmě. V tématu [přidat profil vyhodnocování](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) Další informace. 
> 
> 

## <a name="faceted-navigation"></a>Fasetová navigace
Navigace vyhledávání je běžné na stránka s výsledky, často nacházející se v horní části stránky na straně. Ve službě Azure Search poskytuje Fasetové navigace řízené samotným hledání podle předdefinovaných filtrů. V tématu [Fasetové navigace ve službě Azure Search](search-faceted-navigation.md) podrobnosti.

## <a name="filters-at-the-page-level"></a>Filtry na úrovni stránky
Pokud návrh vašeho řešení zahrnuty vyhrazené vyhledávání stránky pro určité typy obsahu (například prodejní online aplikace, která má oddělení uvedených v horní části stránky), můžete vložit výraz filtru společně **onClick** událost a otevřete stránku v odkazující stavu. 

Můžete odeslat filtr s nebo bez výrazu vyhledávání. Například následující požadavek bude filtrovat název značky, vrácení pouze dokumenty, které odpovídají ho.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

V tématu [vyhledávání dokumentů (API služby Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) Další informace o `$filter` výrazy.

## <a name="see-also"></a>Viz také
* [Rozhraní API REST služby vyhledávání systému Azure](https://docs.microsoft.com/rest/api/searchservice)
* [Operace indexu](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operace dokumentu](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Kurzy o službě Azure Search a video](search-video-demo-tutorial-list.md)
* [Fasetové navigace ve službě Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
