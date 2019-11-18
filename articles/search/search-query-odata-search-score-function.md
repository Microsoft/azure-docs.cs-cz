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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113133"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funkce `search.score` OData v Azure Kognitivní hledání

Když odešlete dotaz do Azure Kognitivní hledání bez [parametru **$OrderBy** ](search-query-odata-orderby.md), výsledky, které se vrátí, se seřadí v sestupném pořadí podle relevance. I když použijete **$OrderBy**, bude ve výchozím nastavení k přerušení vazeb použito skóre relevance. Někdy je ale vhodné použít skóre relevance jako počáteční kritéria řazení a některá další kritéria jako modul pro dělení na více koncových bodů. Tato funkce umožňuje. `search.score`

## <a name="syntax"></a>Syntaxe

Syntaxe pro `search.score` v **$OrderBy** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry. Dá se použít s specifikátorem pořadí řazení `asc` nebo `desc` stejným způsobem jako jakákoli jiná klauzule v parametru **$OrderBy** . Může se objevit kdekoli v seznamu kritérií řazení.

## <a name="example"></a>Příklad

Seřaďte hotely v sestupném pořadí podle `search.score` a `rating`a pak ve vzestupném pořadí podle vzdálenosti od daných souřadnic, takže mezi dvěma hotely a identickým hodnocením je nejbližší uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání Est API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
