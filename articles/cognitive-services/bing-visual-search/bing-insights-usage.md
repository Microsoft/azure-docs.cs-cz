---
title: Příklady Bing Insights – Vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje příklady, jak může Vizuální vyhledávání Bingu používat a zobrazovat přehledy obrázků v Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: acb84a11318175c17976c5dc570ce5d5f6aa6b6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512687"
---
# <a name="examples-of-bing-insights-usage"></a>Příklady využití Bing Insights

Tento článek obsahuje příklady, jak může Bing používat a zobrazovat přehledy imagí v Bing.com.

## <a name="pagesincluding-insight-example"></a>Příklad PagesIncluding Insight

Následující obrázek zobrazí odkaz na první webovou stránku a umožní uživateli rozbalit a sbalit seznam dalších webových stránek, které obsahují obrázek:

![Rozšířené stránky včetně](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Příklad ShoppingSources Insight

Následující příklad ukazuje, jak může Bing Zobrazit nákupní zdroje pro produkty, které jsou vidět v imagi:

![Nákupy zdrojů](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Příklad VisualSearch Insight

Následující příklad ukazuje, jak může Bing zobrazit vizuálně podobné obrázky (viz **Související obrázky** v příkladu):

![Vizuálně podobné obrázky](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Příklad přehledu recepty

V následující části se dozvíte, jak může Bing zobrazovat recepty pro jídlo zobrazená na obrázku. Tento příklad umožňuje uživateli zjistit, že jsou k dispozici recepty:

![Recepty a stránky, včetně](./media/recipes-pages-including.PNG)

 A poskytuje odkaz na recepty, když uživatel rozbalí seznam:

![Rozbalené stránky receptu včetně](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Příklad RelatedSearches Insight

Následující příklad ukazuje, jak může Bing zobrazit související hledání imagí provedených ostatními. Pokud uživatel klikne na obrázek, uživatel přejde na stránku s výsledky hledání Bing.com/images pro daný dotaz.

![Související hledání imagí](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Příklad přehledu entit

Následující příklad ukazuje, jak může Bing zobrazit informace o entitě (osoba, místo nebo věc) zobrazené na obrázku. Pokud uživatel klikne na odkaz entita, uživatel přejde na stránku výsledků hledání Bing.com pro danou entitu:

![Entita zobrazená v obrázku](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Zobrazení dalších přehledů, které může uživatel prozkoumat

Následující příklad ukazuje, jak může Bing zobrazit další informace o imagi, kterou může uživatel prozkoumat.

![Prozkoumat další přehledy o imagi](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Ohraničovací čtverečky a aktivní body

Mezi nevýchozí značky patří ohraničovací rámeček, který identifikuje oblast zájmu v obrázku, na který se značka vztahuje. Pokud ohraničující rámeček neidentifikuje celý obrázek, použijte ohraničovací rámeček pro vytvoření aktivního bodu na obrázku. Uživatel může kliknout na aktivní bod a získat informace týkající se obsahu nalezeného pod aktivním bodem (nebo obdélníkem). Pokud se například jedná o obrázek s vysokou mírou, výsledky mohou obsahovat značky (a ohraničovací čtverečky) pro příslušenství zobrazená na obrázku, jako je například košelkových, Jewelry, scarfs a tak dále. Následující příklad ukazuje obdélník pro Sunglasses, který je zobrazený na obrázku:

![Ohraničovací rámeček a aktivní bod](./media/click-to-search.PNG)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s prvním požadavkem, přečtěte si rychlý Start:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)





