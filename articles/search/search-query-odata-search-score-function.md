---
title: Referenční dokumentace funkcí search.score OData – Azure Search
description: Search.score funkci prostředí OData v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079688"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` funkce ve službě Azure Search

Když odešlete dotaz do služby Azure Search bez [ **$orderby** parametr](search-query-odata-orderby.md), výsledky, které se vraťte budou seřazeny v sestupném pořadí podle relevance skóre. I když použijete **$orderby**, skóre relevance se použije k rozdělení ties ve výchozím nastavení. Někdy je však vhodné použít skóre relevance jako kritéria počáteční řazení a jiných kritérií, jako zkrácené. `search.score` Funkce umožňuje udělat.

## <a name="syntax"></a>Syntaxe

Syntaxe pro `search.score` v **$orderby** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry. Je možné s `asc` nebo `desc` specifikátor pořadí řazení, stejně jako další klauzuli v **$orderby** parametru. Může vyskytovat kdekoli v seznamu kritérium řazení.

## <a name="example"></a>Příklad:

Seřadit v sestupném pořadí podle hotels `search.score` a `rating`a pak ve vzestupném pořadí podle vzdálenosti od dané souřadnice tak, aby mezi dva hotels s identické hodnocení na ten nejbližší je uvedená jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další postup  

- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
