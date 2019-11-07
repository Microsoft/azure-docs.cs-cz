---
title: funkce dotazu moreLikeThis (Preview)
titleSuffix: Azure Cognitive Search
description: Popisuje funkci moreLikeThis (Preview), která je k dispozici ve verzi Preview REST API Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721730"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (Preview) v Azure Kognitivní hledání

> [!IMPORTANT] 
> Tato funkce je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

`moreLikeThis=[key]` je parametr dotazu v [rozhraní API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) , který najde dokumenty podobné dokumentu určenému klíčem dokumentu. Když se v `moreLikeThis`vytvoří požadavek hledání, vygeneruje se dotaz s hledanými výrazy získanými z daného dokumentu, který tento dokument nejlépe popisuje. Vygenerovaný dotaz se pak použije k vytvoření žádosti o vyhledávání. Ve výchozím nastavení se považuje obsah všech prohledávatelných polí, mínus všechna pole s omezením, která jste zadali pomocí parametru `searchFields`. Parametr `moreLikeThis` nelze použít společně s parametrem Search `search=[string]`.

Ve výchozím nastavení se považuje obsah všech vyhledávacích polí na nejvyšší úrovni. Pokud chcete místo toho zadat konkrétní pole, můžete použít parametr `searchFields`. 

MoreLikeThis nelze použít pro prohledávatelné dílčí pole ve [složitém typu](search-howto-complex-data-types.md).

## <a name="examples"></a>Příklady 

Níže je uveden příklad dotazu moreLikeThis. Dotaz vyhledá dokumenty, jejichž pole popisu jsou nejvíce podobná poli zdrojového dokumentu, jak je určeno parametrem `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Další kroky

K experimentování s touto funkcí můžete použít libovolný nástroj webového testování.  Pro toto cvičení doporučujeme použít metodu post.

> [!div class="nextstepaction"]
> [Prozkoumejte rozhraní REST API pro Azure Kognitivní hledání pomocí post](search-get-started-postman.md)