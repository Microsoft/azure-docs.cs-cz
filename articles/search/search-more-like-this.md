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
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793477"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (Preview) v Azure Kognitivní hledání

> [!Note]
> moreLikeThis je ve verzi Preview a není určená pro použití v produkčním prostředí. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.

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