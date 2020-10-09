---
title: Použijte geografické hranice k filtrování výsledků z rozhraní API Bingu pro vyhledávání v místním obchodu.
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak filtrovat výsledky hledání z rozhraní API pro vyhledávání místních obchodních aplikací Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "69906243"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Použijte geografické hranice k filtrování výsledků z rozhraní API Bingu pro vyhledávání v místním obchodu.

Rozhraní API pro místní vyhledávání Bingu umožňuje nastavit hranice pro konkrétní geografickou oblast, kterou chcete prohledávat pomocí `localCircularView` `localMapView` parametrů dotazu nebo. Nezapomeňte použít jenom jeden parametr v dotazech. 

Pokud hledaný termín obsahuje explicitní zeměpisnou polohu, rozhraní API Bingu ho automaticky použije k nastavení hranic pro výsledky hledání. Pokud je například hledaný termín `sailing in San Diego` , `San Diego` bude použit jako umístění a jakékoli jiné zadané umístění v parametrech dotazu nebo hlavičky uživatele budou ignorovány. 

Pokud se v hledaném termínu nezjistí geografické umístění a v parametrech dotazu není zadané geografické umístění, rozhraní API Bingu pro vyhledávání v místním prostředí se pokusí určit umístění z `X-Search-ClientIP` hlavičky nebo žádosti `X-Search-Location` . Pokud není zadána žádná hlavička, rozhraní API určí umístění buď z IP adresy klienta žádosti, nebo souřadnic GPS pro mobilní zařízení.

## <a name="localcircularview"></a>localCircularView

`localCircularView`Parametr vytvoří kruhovou geografickou oblast kolem sady souřadnic zeměpisné šířky a délky definované poloměrem. Při použití tohoto parametru budou odpovědi z rozhraní API pro místní vyhledávání Bingu zahrnovat jenom umístění v rámci tohoto kruhu, na rozdíl od `localMapView` parametru, který může zahrnovat umístění mírně mimo oblast hledání.

Chcete-li zadat kruhovou geografickou oblast hledání, vyberte zeměpisnou šířku a délku, která bude sloužit jako střed kruhu, a poloměr v metrech. Tento parametr lze následně připojit k řetězci dotazu, například: `q=Restaurants&localCircularView=47.6421,-122.13715,5000` .

Úplný dotaz:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView`Parametr určuje obdélníkovou geografickou oblast, kterou chcete vyhledat, pomocí dvou sad souřadnic pro určení jeho jihovýchodních rohů a severozápadních rohů. Při použití tohoto parametru můžou odpovědi z rozhraní API Bingu pro vyhledávání místních obchodních hledání zahrnovat umístění v rámci zadané oblasti a přímo mimo ni, na rozdíl od `localCircularView` parametru, který obsahuje jenom umístění v oblasti hledání.

Chcete-li určit obdélníkovou oblast hledání, vyberte dvě sady souřadnic zeměpisných šířky a délky, které budou sloužit jako jihovýchodní a severozápadní rohy hranice. Nezapomeňte nejprve definovat souřadnice jihovýchodů, jako v následujícím příkladu: `localMapView=47.619987,-122.181671,47.6421,-122.13715` .

Úplný dotaz:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Další kroky
- [Rychlý Start pro místní vyhledávání v jazyce Java](quickstarts/local-search-java-quickstart.md)
- [Rychlé zprovoznění hledání v jazyce C# pro místní obchod](quickstarts/local-quickstart.md)
- [Rychlý Start uzlu místního hledání firmy](quickstarts/local-search-node-quickstart.md)
- [Rychlý Start pro místní vyhledávání v Pythonu](quickstarts/local-search-python-quickstart.md)
