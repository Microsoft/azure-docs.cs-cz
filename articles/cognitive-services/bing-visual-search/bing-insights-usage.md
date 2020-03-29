---
title: Příklady přehledů Služby Bing – Vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje příklady toho, jak může vizuální vyhledávání Bingu používat a zobrazovat přehledy obrázků v Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111632"
---
# <a name="examples-of-bing-insights-usage"></a>Příklady využití přehledů Bingu

Tento článek obsahuje příklady toho, jak může Bing používat a zobrazovat přehledy obrázků na Bing.com.

## <a name="pagesincluding-insight-example"></a>StránkyVčetně příkladu přehledu

Následující text zobrazí odkaz na první webovou stránku a umožní uživateli rozbalit a sbalit seznam dalších webových stránek, které obrázek obsahují:

![Rozbalené stránky včetně](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Příklad přehledu ShoppingSources

Následující informace ukazují, jak může Bing zobrazit nákupní zdroje produktů zobrazených na obrázku:

![Nákupní zdroje](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Příklad přehledu visualsearch

Následující text ukazuje, jak může Bing zobrazovat vizuálně podobné obrázky (viz **Související obrázky** v příkladu):

![Vizuálně podobné obrázky](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Příklad přehledu receptů

Následující text ukazuje, jak může Bing zobrazovat recepty na potraviny zobrazené na obrázku. Příklad umožňuje uživateli vědět, že jsou k dispozici recepty:

![Recepty a stránky včetně](./media/recipes-pages-including.PNG)

 A poskytuje odkaz na recepty, když uživatel rozbalí seznam:

![Rozšířené stránky receptů včetně](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches příklad vhled

Následující text ukazuje, jak může Bing zobrazovat související vyhledávání obrázků provedených jinými uživateli. Pokud uživatel klepne na obrázek, uživatel se přenese na stránku s výsledky hledání Bing.com/images pro daný související dotaz.

![Související vyhledávání obrázků](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Příklad přehledu entit

Následující text ukazuje, jak může Bing zobrazovat informace o entitě (osobě, místě nebo věci) zobrazené na obrázku. Pokud uživatel klikne na odkaz entity, přejde se na stránku s Bing.com výsledků hledání entity:

![Entita zobrazená na obrázku](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Zobrazení dalších přehledů, které by uživatel mohl prozkoumat

Následující text ukazuje, jak může Bing zobrazit další informace o obrázku, které může uživatel prozkoumat.

![Prozkoumejte další poznatky o obrázku](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Ohraničovací boxy a horké body

Jiné než výchozí značky obsahují ohraničovací rámeček, který identifikuje oblast zájmu v obrázku, na který se značka vztahuje. Pokud ohraničovací rámeček neidentifikuje celý obraz, vytvořte aktivní bod v obraze pomocí ohraničovacího rámečku. Uživatel může kliknout na aktivní bod a získat informace týkající se obsahu nalezeného pod aktivním bodem (nebo obdélníkem). Pokud je například obrázek vysoce módní, mohou výsledky obsahovat značky (a ohraničovací rámečky) pro příslušenství zobrazené na obrázku, například kabelku, šperky, šály a tak dále. Následující příklad ukazuje obdélník aktivního bodu pro sluneční brýle zobrazené na obrázku:

![Ohraničovací rámeček a aktivní bod](./media/click-to-search.PNG)

## <a name="next-steps"></a>Další kroky

Chcete-li začít s prvním požadavkem, podívejte se na rychlé starty: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





