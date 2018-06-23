---
title: Příklady Bing insights | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Zobrazuje příklady zobrazené na vyhledávače Bing.com přehledy bitové kopie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 102bd0e916491738d74956c209829008d779bcbf
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343280"
---
# <a name="examples-of-bing-insights-usage"></a>Příklady Bing Statistika využití

Tato část obsahuje příklady, jak může zobrazit Statistika na vyhledávače Bing.com Bing.

## <a name="pagesincluding-insight-example"></a>Příklad přehledy PagesIncluding

Následující ukazuje, jak může Bing zobrazit webové stránky, které zahrnují bitovou kopii. V příkladu se zobrazí odkaz na první webovou stránku a umožní uživateli rozbalení a sbalení seznamu jiných webových stránek, které zahrnují bitovou kopii.

![Rozšířené stránky, včetně](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>Příklad přehledy ShoppingSources

Následující ukazuje, jak může Bing Zobrazit nákupní zdroje pro produkty vidět na obrázku.

![Nákupní zdroje](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>Příklad přehledy VisualSearch

Následující ukazuje, jak může Bing zobrazit vizuálně podobné bitové kopie (viz **související s obrázky** v příkladu).

![Vizuální podobné bitové kopie](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Příklad přehledy recepty

Následující ukazuje, jak může Bing zobrazit recepty pro jídlo znázorněno na obrázku. V příkladu umožňuje uživateli vědět, že nejsou k dispozici recepty.

![Recepty a stránky, včetně](./media/recipes-pages-including.PNG)

 A poskytuje odkaz recepty, když uživatel rozbalí seznam.

![Rozšířené recepturách stránky, včetně](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>Příklad přehledy RelatedSearches

Následující ukazuje, jak může Bing zobrazit související hledání bitové kopie vytvořené jinými uživateli. Pokud uživatel kliknutím na bitovou kopii, bude uživatel úvahu Bing.com/images stránky s výsledky hledání pro tuto relaci dotazu.

![Související hledání pro bitové kopie](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Příklad přehledy entity

Následující příklad zobrazuje, jak Bing může zobrazit informace o entitě (osoby, místní nebo věcí) znázorněno na obrázku. Pokud uživatel klikne na odkaz entity, uživatel se provede na stránky s výsledky hledání vyhledávače Bing.com pro entitu.

![Viz obrázek entity](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>Zobrazení dalších přehledy, které může uživatel prozkoumat

Následující ukazuje, jak může Bing zobrazit další informace o bitovou kopii, která můžete prozkoumat uživatele.

![Prozkoumejte další statistiky o bitové kopii](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>Ohraničující polí a aktivní body

Jiné než výchozí značky se týkají ohraničující pole, které identifikují oblasti zájmu v bitovou kopii, která značky se vztahuje na. Pokud pole ohraničující neidentifikuje celého obrázku, použijte pole ohraničující vytvořit aktivní bod na bitovou kopii. Uživatele můžete kliknout na aktivní bod získat informace vztahující se k obsahu v aktivního bodu (nebo obdélníku). Například pokud bitovou kopii je bitová kopie vysokou způsobem, výsledky může obsahovat značky (a ohraničující polí) pro příslušenství znázorněno na obrázku, například košelkových, špercích, scarfs atd. Následující příklad ukazuje aktivního bodu obdélníku pro sluneční brýle znázorněno na obrázku.

![Ohraničující pole a aktivního bodu](./media/click-to-search.PNG)



## <a name="next-steps"></a>Další postup

Podívejte se na JSON za tyto příklady, najdete v tématu [výchozí insights](default-insights-tag.md) a [odpověď JSON](overview.md#the-response).

Abyste mohli rychle začít s první žádost, najdete v článku elementy QuickStart: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)





