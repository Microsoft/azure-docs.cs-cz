---
title: Příklady insights Bingu – vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Jsou uvedeny příklady insights obrázku je znázorněno na adrese Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: b120ad6c116b5722e5d2a982e93960232b0baf94
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869343"
---
# <a name="examples-of-bing-insights-usage"></a>Příklady použití insights Bingu

Tento článek obsahuje příklady toho, jak může Bing použít a zobrazit přehledy obrázků na adrese Bing.com.

## <a name="pagesincluding-insight-example"></a>Příklad PagesIncluding insight

Následující zobrazí odkaz na první webovou stránku a umožní uživateli rozbalit nebo sbalit seznam jiných webových stránek, které obsahují image.

![Rozšířené stránek, včetně](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Příklad ShoppingSources insight

Následující obrázek znázorňuje jak Bingu se může zobrazit nákupní zdroje pro produkty vidět na obrázku.

![Nákupní zdroje](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Příklad VisualSearch insight

Následující ukazuje, jak může Bing zobrazit vizuálně podobné obrázky (viz **související s imagí** v příkladu).

![Vizuálně podobné obrázky](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Příklad insight recepty

Následující ukazuje, jak může Bing zobrazit receptů potravin je znázorněno na obrázku. Příklad umožňuje uživateli vědět, že nejsou k dispozici recepty.

![Recepty a stránek, včetně](./media/recipes-pages-including.PNG)

 A poskytuje odkaz recepty, když je uživatel rozbalí v seznamu.

![Rozšířené předpisu stránek, včetně](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Příklad RelatedSearches insight

Následující obrázek znázorňuje jak Bingu se může zobrazit související hledání obrázků provedených ostatními. Pokud uživatel klikne na obrázku, uživatel věnovat Bing.com/images stránka výsledků hledání pro daný dotaz související.

![Související hledání obrázků](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Příklad insight entity

Následuje ukázka, jak Bingu může zobrazovat informace o entitě (osobě, místě nebo věc, kterou) je znázorněno na obrázku. Pokud uživatel klikne odkaz na entitu, uživatel se přesunete na Bing.com stránka výsledků hledání pro entitu.

![Entita je znázorněno na obrázku](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Zobrazení další přehledy, které uživatel může prozkoumat

Následující ukazuje, jak může Bing zobrazit další informace o imagi, můžete prozkoumat uživatele.

![Prozkoumejte další informace o obrázku](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Ohraničující polí a aktivní body

Jiné než výchozí značky se týkají třeba ohraničovací rámeček, který slouží k identifikaci oblasti zájmu v obrázku, který značky se vztahuje na. Pokud ohraničujícího rámečku neidentifikuje celého obrázku, použijte k vytvoření aktivní bod na obrázku ohraničujícího rámečku. Uživatel můžete kliknout na aktivního informace související s obsahem v aktivní bod (nebo obdélník). Například pokud image je image vysoce způsobem, výsledky může obsahovat značky (a ohraničující polí) pro příslušenství je znázorněno na obrázku, jako je například košelkových špercích, scarfs, atd. Následující příklad ukazuje aktivního rámeček pro prodejci slunečních brýlí je znázorněno na obrázku.

![Ohraničující rámeček a aktivního bodu](./media/click-to-search.PNG)



## <a name="next-steps"></a>Další postup

Abyste mohli rychle začít s vaší první žádost, najdete v článku postupů rychlý start: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





