---
title: Referenční informace o funkci hledání OData. skóre – Azure Search
description: Funkce hledání OData ve Azure Searchch dotazech.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647522"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Funkce `search.score` OData v Azure Search

Když odešlete dotaz Azure Search bez [parametru **$OrderBy** ](search-query-odata-orderby.md), výsledky, které se vrátí, se seřadí v sestupném pořadí podle relevance. I když použijete **$OrderBy**, bude ve výchozím nastavení k přerušení vazeb použito skóre relevance. Někdy je ale vhodné použít skóre relevance jako počáteční kritéria řazení a některá další kritéria jako modul pro dělení na více koncových bodů. Tato `search.score` funkce vám to umožňuje.

## <a name="syntax"></a>Syntaxe

Syntaxe pro `search.score` v **$OrderBy** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry. Dá se použít se `asc` specifikátorem pořadí řazení nebo `desc` , stejně jako jakékoli jiné klauzule v parametru **$OrderBy** . Může se objevit kdekoli v seznamu kritérií řazení.

## <a name="example"></a>Příklad

Seřadit hotely v sestupném `search.score` pořadí `rating`podle a a potom ve vzestupném pořadí podle vzdálenosti od daných souřadnic, takže mezi dvěma hotely a identickým hodnocením je nejbližší, který je uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
