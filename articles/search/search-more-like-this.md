---
title: moreLikeThis ve službě Azure Search (preview) – Azure Search
description: Předběžná dokumentace pro funkci moreLikeThis (preview) v rozhraní REST API Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4d1c691e570d3cfc7e0475c02e4c60ed6ffa8440
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485353"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis ve službě Azure Search

> [!Note]
> moreLikeThis je ve verzi preview a není určen pro použití v produkčním prostředí. [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) tuto funkci poskytuje. Není dostupná podpora .NET SDK v současnosti.

`moreLikeThis=[key]` je parametr dotazu v [rozhraní API pro vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) , který vyhledá dokumenty podobný dokument určený pomocí klíče dokumentu. Po provedení požadavku na hledání s `moreLikeThis`, dotazu je vytvořen s hledané termíny extrahují z daného dokumentu, které popisují nejlepší tohoto dokumentu. Generování dotazu se pak použije k odeslání požadavku hledání. Ve výchozím nastavení, obsah všechna prohledatelná pole jsou považovány za, po odečtení všech s omezeným přístupem polí, které jste zadali pomocí `searchFields` parametru. `moreLikeThis` Parametr nelze použít s parametrem vyhledávání `search=[string]`.

Ve výchozím nastavení jsou považovány za obsah všechna prohledatelná pole nejvyšší úrovně. Pokud chcete místo toho zadejte konkrétní pole, můžete použít `searchFields` parametru. 

MoreLikeThis nelze použít v prohledávatelných dílčích polí v [komplexní typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Příklady 

Níže je příklad dotazu moreLikeThis. Tento dotaz najde dokumentů, jejichž popis pole jsou nejvíc podobný pole zdrojový dokument určený `moreLikeThis` parametru.

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

Všechny webové nástroje pro testování můžete experimentovat s touto funkcí.  Doporučujeme použít nástroj Postman pro účely tohoto cvičení.

> [!div class="nextstepaction"]
> [Prozkoumejte službu REST API služby Azure Search pomocí nástroje Postman](search-get-started-postman.md)