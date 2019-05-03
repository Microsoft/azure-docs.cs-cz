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
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024678"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis ve službě Azure Search (preview)

`moreLikeThis=[key]` je parametr dotazu v [rozhraní API pro vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents) , který vyhledá dokumenty podobný dokument určený pomocí klíče dokumentu. Po provedení požadavku na hledání s `moreLikeThis`, dotazu je vytvořen s hledané termíny extrahují z daného dokumentu, které popisují nejlepší tohoto dokumentu. Generování dotazu se pak použije k odeslání požadavku hledání. Ve výchozím nastavení, obsah všechna prohledatelná pole jsou považovány za, po odečtení všech s omezeným přístupem polí, které jste zadali pomocí `searchFields` parametru. `moreLikeThis` Parametr nelze použít s parametrem vyhledávání `search=[string]`.

Ve výchozím nastavení jsou považovány za obsah všechna prohledatelná pole nejvyšší úrovně. Pokud chcete místo toho zadejte konkrétní pole, můžete použít `searchFields` parametru. 

> [!NOTE]
> `moreLikeThis` ve verzi Preview na sekundární prohledávatelná pole v nefunguje [komplexní typ](search-howto-complex-data-types.md).

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

## <a name="feature-availability"></a>Dostupnost funkcí

`moreLikeThis` Parametr je k dispozici ve verzi preview rozhraní REST API jen (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>Další postup

Všechny webové nástroje pro testování můžete experimentovat s touto funkcí.  Doporučujeme použít nástroj Postman pro účely tohoto cvičení.

> [!div class="nextstepaction"]
> [Prozkoumejte službu REST API služby Azure Search pomocí nástroje Postman](search-fiddler.md)