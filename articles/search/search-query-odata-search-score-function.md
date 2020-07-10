---
title: Odkaz na funkci hledání OData. skore
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití funkce Search. skore v Azure Kognitivní hledání dotazů.
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
ms.openlocfilehash: c31304228d9629b0df7f7511ecca2616b4891ee7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206952"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Funkce OData v Azure kognitivní hledání

Když odešlete dotaz do Azure Kognitivní hledání bez [parametru **$OrderBy** ](search-query-odata-orderby.md), výsledky, které se vrátí, se seřadí v sestupném pořadí podle relevance. I když použijete **$OrderBy**, bude ve výchozím nastavení k přerušení vazeb použito skóre relevance. Někdy je ale vhodné použít skóre relevance jako počáteční kritéria řazení a některá další kritéria jako modul pro dělení na více koncových bodů. `search.score`Tato funkce vám to umožňuje.

## <a name="syntax"></a>Syntaxe

Syntaxe pro `search.score` v **$OrderBy** je `search.score()` . Funkce `search.score` nepřijímá žádné parametry. Dá se použít se `asc` `desc` specifikátorem pořadí řazení nebo, stejně jako jakékoli jiné klauzule v parametru **$OrderBy** . Může se objevit kdekoli v seznamu kritérií řazení.

## <a name="example"></a>Příklad

Seřadit hotely v sestupném pořadí podle `search.score` a a `rating` potom ve vzestupném pořadí podle vzdálenosti od daných souřadnic, takže mezi dvěma hotely a identickým hodnocením je nejbližší, který je uveden jako první:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Další kroky  

- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;rozhraní API Azure Kognitivní hledání EST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
