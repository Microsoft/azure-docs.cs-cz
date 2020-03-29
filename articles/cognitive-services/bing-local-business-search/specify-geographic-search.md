---
title: Filtrování výsledků z rozhraní API pro vyhledávání pro místní firmy Bingu pomocí geografických hranic
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak filtrovat výsledky hledání z rozhraní API pro vyhledávání v místní firmě Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906243"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Filtrování výsledků z rozhraní API pro vyhledávání pro místní firmy Bingu pomocí geografických hranic

Rozhraní API pro vyhledávání místních společností Bingu umožňuje nastavit hranice pro `localCircularView` konkrétní `localMapView` geografickou oblast, kterou chcete prohledávat pomocí parametrů nebo dotazu. Ujistěte se, že v dotazech používáte pouze jeden parametr. 

Pokud vyhledávací dotaz obsahuje explicitní geografickou polohu, rozhraní API pro místní firmu Bing upraví automaticky hranice pro výsledky hledání. Pokud je `sailing in San Diego`například hledaný `San Diego` termín , bude použito jako umístění a všechna další zadaná umístění v parametrech dotazu nebo záhlavích uživatelů budou ignorována. 

Pokud není ve vyhledávacím dotazu zjištěna geografická poloha a pomocí parametrů dotazu není zadáno žádné zeměpisné umístění, `X-Search-ClientIP` pokusí `X-Search-Location` se rozhraní API pro místní vyhledávání služby Bing určit umístění z požadavků nebo záhlaví. Pokud není zadána ani jedna hlavička, rozhraní API určí umístění z IP adresy klienta požadavku nebo souřadnic GPS pro mobilní zařízení.

## <a name="localcircularview"></a>localCircularView

Parametr `localCircularView` vytvoří kruhovou zeměpisnou oblast kolem sady souřadnic zeměpisné šířky a délky definovaných poloměrem. Při použití tohoto parametru budou odpovědi z rozhraní API pro vyhledávání místních společností Bingu obsahovat pouze umístění v tomto kruhu, na rozdíl od parametru, `localMapView` který může obsahovat umístění mírně mimo oblast vyhledávání.

Chcete-li určit kruhovou zeměpisnou oblast hledání, vyberte zeměpisnou šířku a délku, která bude sloužit jako střed kruhu, a poloměr v metrech. Tento parametr pak lze připojit k řetězci `q=Restaurants&localCircularView=47.6421,-122.13715,5000`dotazu, například: .

Kompletní dotaz:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

Parametr `localMapView` určuje obdélníkovou zeměpisnou oblast pro vyhledávání pomocí dvou sad souřadnic pro určení jeho jihovýchodních a severozápadních rohů. Při použití tohoto parametru mohou odpovědi z rozhraní API pro vyhledávání místních společností Bingu zahrnovat umístění v rámci zadané oblasti a mimo ni, na rozdíl od parametru, `localCircularView` který zahrnuje pouze umístění v oblasti vyhledávání.

Chcete-li určit obdélníkovou oblast hledání, vyberte dvě sady souřadnic zeměpisné šířky a délky, které budou sloužit jako jihovýchodní a severozápadní roh hranice. Nejprve definujte jihovýchodní souřadnice, jako `localMapView=47.619987,-122.181671,47.6421,-122.13715`v následujícím příkladu: .

Kompletní dotaz:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Další kroky
- [Rychlý start java vyhledávání místních společností](quickstarts/local-search-java-quickstart.md)
- [Úvodní příručka pro místní obchodní vyhledávání C#](quickstarts/local-quickstart.md)
- [Rychlý začátek uzlu hledání místní firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý start pythonu pro místní obchodní vyhledávání](quickstarts/local-search-python-quickstart.md)
