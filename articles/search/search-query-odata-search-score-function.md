---
title: Odkaz na funkci OData search.score
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití funkce search.score v dotazech Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113133"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funkce `search.score` OData v Azure Cognitive Search

Když odešlete dotaz do Azure Cognitive Search bez [ **$orderby** parametr](search-query-odata-orderby.md), výsledky, které se vrátí budou seřazeny v sestupném pořadí podle skóre relevance. I když použijete **$orderby**, skóre relevance bude ve výchozím nastavení použito k přerušení vazeb. Někdy je však užitečné použít skóre relevance jako počáteční kritéria řazení a některá další kritéria jako rozstřel. Funkce `search.score` umožňuje provést.

## <a name="syntax"></a>Syntaxe

Syntaxe `search.score` pro v `search.score()` **$orderby** je . Funkce `search.score` nepřijímá žádné parametry. Lze jej použít `asc` s `desc` specifikátorem nebo pořadí řazení, stejně jako jakákoli jiná klauzule v **parametru $orderby.** Může se zobrazit kdekoli v seznamu kritérií řazení.

## <a name="example"></a>Příklad

Seřaďte hotely `search.score` `rating`v sestupném pořadí podle a nastupujícípořadí podle vzdálenosti od daných souřadnic tak, aby mezi dvěma hotely se stejným hodnocením byl nejbližší uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
