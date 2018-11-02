---
title: Použití geografické hranice pro filtrování výsledků místní firmy API pro vyhledávání Bingu | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku se dozvíte, jak filtrovat výsledky hledání z místní firmy hledání rozhraní API Bingu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 246b0d3f2edcf941a49e7e57043225ff4c276064
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748630"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Použití geografické hranice pro filtrování výsledků místní firmy API pro vyhledávání Bingu

Místní firmy API pro vyhledávání Bingu umožňuje nastavit hranice v konkrétní geografické oblasti, které chcete hledat s použitím `localCircularView` nebo `localMapView` parametrů dotazu. Nezapomeňte použít pouze jeden parametr v dotazech. 

Pokud hledaný výraz obsahuje explicitní zeměpisné polohy, API Bingu pro místní obchodní automaticky použije nastavení hranice pro výsledky hledání. Například, pokud je hledaný termín `sailing in San Diego`, pak `San Diego` se použije jako umístění a jakékoli jiné zadané umístění v parametrech dotazu nebo záhlaví uživatel se bude ignorovat. 

Pokud geografické umístění není zjištěna v hledaný termín a žádné geografické umístění je určeno pomocí parametrů dotazu, API Bingu pro místní obchodní vyhledávání se pokusí zjistit umístění z identifikátoru požadavku `X-Search-ClientIP` nebo `X-Search-Location` záhlaví. Pokud není zadána žádná záhlaví, rozhraní API určí umístění z IP adresu klienta požadavku nebo souřadnice GPS pro mobilní zařízení.

## <a name="localcircularview"></a>localCircularView

`localCircularView` Parametr vytvoří cyklický geografické oblasti kolem sadu souřadnice zeměpisné šířky a délky, určené poloměr. Při použití tohoto parametru odpovědi místní firmy API pro vyhledávání Bingu bude obsahovat pouze umístění v rámci této kruh, na rozdíl od `localMapView` parametrů, které mohou zahrnovat umístění mírně mimo oblast hledání.

Pokud chcete zadat do kruhové geografické vyhledávání oblasti, vyberte zeměpisnou šířku a zeměpisnou délku, která bude sloužit jako střed kruhu a poloměr v metrech. Tento parametr lze následně připojit k řetězce dotazu, například: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Dokončení dotazu:

````
https://www.bingapis.com/api/v7/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="localmapview"></a>localMapView

`localMapView` Parametr určuje obdélníkovou oblast geografické vyhledávání, zadejte jeho – jihovýchod a severozápadního rohu pomocí dvě sady souřadnic. Při použití tohoto parametru, odpovědí z místní firmy hledání rozhraní API Bingu mohou zahrnovat umístění v rámci a pouze mimo zadanou oblast, na rozdíl od `localCircularView` parametru, která bude obsahovat pouze umístění v rámci oblasti vyhledávání.

Chcete-li určit oblasti obdélníkový vyhledávání, vyberte dvě sady zeměpisnou šířkou/délkou souřadnice, kde bude sloužit jako – jihovýchod a severozápadního rohu hranice. Je potřeba nejdřív definuje jihovýchodní souřadnice, jako v následujícím příkladu: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Dokončení dotazu:

````
https://www.bingapis.com/api/v7/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="next-steps"></a>Další postup
- [Místní pracovní hledání Java Quickstart](quickstarts/local-search-java-quickstart.md)
- [Místní pracovní hledání C# rychlý start](quickstarts/local-quickstart.md)
- [Místní pracovní hledání uzlu rychlý start](quickstarts/local-search-node-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](quickstarts/local-search-python-quickstart.md)
