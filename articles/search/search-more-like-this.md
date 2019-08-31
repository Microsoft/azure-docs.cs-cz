---
title: moreLikeThis v Azure Search (Preview) – Azure Search
description: Předběžná dokumentace k funkci moreLikeThis (Preview), která je dostupná v REST API Azure Search
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182321"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis v Azure Search

> [!Note]
> moreLikeThis je ve verzi Preview a není určená pro použití v produkčním prostředí. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.

`moreLikeThis=[key]`je parametr dotazu v [rozhraní API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) , který najde dokumenty podobné dokumentu určenému klíčem dokumentu. Když se `moreLikeThis`vytvoří požadavek na hledání, vygeneruje se dotaz s hledanými výrazy z daného dokumentu, které tento dokument nejlépe popisují. Vygenerovaný dotaz se pak použije k vytvoření žádosti o vyhledávání. Ve výchozím nastavení se považuje obsah všech prohledávatelných polí, mínus všechna pole s omezením, která jste zadali `searchFields` pomocí parametru. Parametr nelze použít společně s `search=[string]`parametrem Search. `moreLikeThis`

Ve výchozím nastavení se považuje obsah všech vyhledávacích polí na nejvyšší úrovni. Chcete-li místo toho zadat konkrétní pole, můžete použít `searchFields` parametr. 

MoreLikeThis nelze použít pro prohledávatelné dílčí pole ve [složitém typu](search-howto-complex-data-types.md).

## <a name="examples"></a>Příklady 

Níže je uveden příklad dotazu moreLikeThis. Dotaz vyhledá dokumenty, jejichž pole popisu jsou nejvíce podobná poli zdrojového dokumentu, jak je uvedeno v `moreLikeThis` parametru.

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


## <a name="next-steps"></a>Další postup

K experimentování s touto funkcí můžete použít libovolný nástroj webového testování.  Pro toto cvičení doporučujeme použít metodu post.

> [!div class="nextstepaction"]
> [Prozkoumejte Azure Search rozhraní REST API pomocí post](search-get-started-postman.md)