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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113133"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funkce `search.score` OData v Azure kognitivní hledání

Když odešlete dotaz do Azure Kognitivní hledání bez [parametru **$OrderBy** ](search-query-odata-orderby.md), výsledky, které se vrátí, se seřadí v sestupném pořadí podle relevance. I když použijete **$OrderBy**, bude ve výchozím nastavení k přerušení vazeb použito skóre relevance. Někdy je ale vhodné použít skóre relevance jako počáteční kritéria řazení a některá další kritéria jako modul pro dělení na více koncových bodů. Tato `search.score` funkce vám to umožňuje.

## <a name="syntax"></a>Syntaxe

Syntaxe pro `search.score` v **$OrderBy** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry. Dá se použít se specifikátorem `asc` pořadí `desc` řazení nebo, stejně jako jakékoli jiné klauzule v parametru **$OrderBy** . Může se objevit kdekoli v seznamu kritérií řazení.

## <a name="example"></a>Příklad

Seřadit hotely v sestupném `search.score` pořadí `rating`podle a a potom ve vzestupném pořadí podle vzdálenosti od daných souřadnic, takže mezi dvěma hotely a identickým hodnocením je nejbližší, který je uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;rozhraní API Azure Kognitivní hledání EST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
