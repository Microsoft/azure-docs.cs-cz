---
title: Dotazy ve službě Azure Search | Dokumentace Microsoftu
description: Sestavení vyhledávacího dotazu ve službě Azure Search a použití parametrů hledání k filtrování a řazení výsledků vyhledávání.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/13/2017
ms.openlocfilehash: a7b32bad8a9f1d039b17fe0f1be167cffbc86547
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006201"
---
# <a name="queries-in-azure-search"></a>Dotazy ve službě Azure Search
> [!div class="op_single_selector"]
> * [Přehled](search-query-overview.md)
> * [Azure Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Při odesílání vyhledávacích dotazů do služby Azure Search existuje několik parametrů, které lze zadat vedle vlastních slov zadaných do vyhledávacího pole vaší aplikace. Tyto parametry dotazu vám umožňují získat větší kontrolu nad [fulltextovým vyhledáváním](search-lucene-query-architecture.md).

Níže je seznam se stručným vysvětlením běžných použití parametrů dotazu ve službě Azure Search. Úplné vysvětlení parametrů dotazu a jejich chování naleznete na podrobných stránkách pro [REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Typy dotazů
Služba Azure Search nabízí mnoho možností pro vytvoření velmi výkonných dotazů. Dva hlavní typy dotazů, které budete používat, jsou `search` a `filter`. Dotaz `search` vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu a funguje podobně, jak jste zvyklí u vyhledávacích webů Google nebo Bing. Dotaz `filter` vyhodnocuje logický výraz na všech *filtrovatelných* polích v indexu. Na rozdíl od dotazů `search` dotazy `filter` porovnávají přesný obsah pole, to znamená, že u polí s řetězci rozlišují malá a velká písmena.

Vyhledávání a filtrování lze používat společně nebo samostatně. Budete-li je používat společně, nejdříve se na celý index použije filtr a na výsledcích filtru se pak provede vyhledávání. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

Syntaxe pro výrazy filtru je podmnožinou [jazyka filtrování OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Pro vyhledávací dotazy můžete použít [zjednodušenou syntaxi](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) nebo [syntaxi dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search), o kterých pojednáváme níže.

### <a name="simple-query-syntax"></a>Jednoduchá syntaxe dotazů
[Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) je výchozím jazykem dotazů ve službě Azure Search. Jednoduchá syntaxe dotazů podporuje mnoho běžných operátorů hledání, včetně operátorů AND, OR, NOT a operátorů fráze, přípony a priority.

### <a name="lucene-query-syntax"></a>Syntaxe dotazů Lucene
[Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) vám umožňuje používat velmi rozšířený a expresivní jazyk dotazů vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Pomocí této syntaxe dotazů můžete snadno dosáhnout následujících schopností: [dotazy v rámci pole](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [přibližné vyhledávání](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [vyhledávání blízkých výrazů](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [zvýšení skóre termínu](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [vyhledávání pomocí regulárních výrazů](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [vyhledávání pomocí zástupných znaků](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [základy syntaxe](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) a [dotazy s logickými operátory](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Řazení výsledků
Když přijímáte výsledky vyhledávacího dotazu, můžete požadovat, aby služba Azure Search vracela výsledky seřazené podle hodnot v určitém poli. Ve výchozím nastavení služba Azure Search řadí výsledky podle skóre vyhledávání každého dokumentu, které je odvozeno z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Chcete-li, aby služba Azure Search vracela výsledky seřazené podle hodnoty místo skóre vyhledávání, můžete použít parametr vyhledávání `orderby`. Můžete zadat hodnotu parametru `orderby`, chcete-li zahrnout názvy polí a volání [funkce `geo.distance()`](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) pro geoprostorové hodnoty. U každého výrazu může následovat `asc` k určení, že výsledky mají být ve vzestupném pořadí, nebo `desc` k určení, že výsledky mají být v sestupném pořadí. Ve výchozím nastavení se používá vzestupné pořadí.

## <a name="paging"></a>Stránkování
Služba Azure Search umožňuje snadnou implementaci stránkování výsledků vyhledávání. Pomocí parametrů `top` a `skip` můžete plynule vydávat vyhledávací požadavky, které vám umožní získat úplnou sadu výsledků vyhledávání ve spravovatelných, seřazených podmnožinách, které umožňují dobré postupy v uživatelském rozhraní vyhledávání. Při získávání těchto menších podmnožin výsledků můžete také získat počet dokumentů v úplné sadě výsledků vyhledávání.

Další informace o stránkování výsledků vyhledávání naleznete v článku [Stránkování výsledků vyhledávání ve službě Azure Search](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Zvýrazňování položek
Ve službě Azure Search je zvýrazňování přesné části výsledků vyhledávání, která odpovídá vyhledávacímu dotazu, umožněno pomocí parametrů `highlight`, `highlightPreTag` a `highlightPostTag`. Můžete určit, u jakých *prohledávatelných* polí má být odpovídající text zvýrazněný, stejně tak můžete zadat značky řetězce, které se mají připojit k začátku a ke konci odpovídajícího text vráceného službou Azure Search.

## <a name="try-out-query-syntax"></a>Vyzkoušení syntaxe dotazů

Nejlepším způsobem, jak porozumět rozdílům v syntaxi, je odesílat dotazy a kontrolovat výsledky.

+ Použití [Průzkumníka služby Search](search-explorer.md) na webu Azure Portal. Po nasazení [ukázkového indexu](search-get-started-portal.md) můžete pomocí nástrojů v portálu dávat dotazy na index během pár minut.

+ K zadávání dotazů na index, který jste odeslali do služby vyhledávání, použijte Telerik Fiddler nebo Chrome Postman. Oba nástroje podporují volání REST pro koncový bod HTTP. 