---
title: Koncové body vyhledávání entity | Microsoft Docs
description: Souhrn rozhraní API pro vyhledávání Entity koncového bodu.
services: cognitive-services
author: v-jaswel
manager: kaiq
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: 3d5da30102712baf399c245cc7678eeddbce796a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342377"
---
# <a name="entity-search-endpoints"></a>Koncové body vyhledávání entity
**Rozhraní API pro vyhledávání Entity** obsahuje jeden koncový bod.

## <a name="endpoint"></a>Koncový bod
O výsledky hledání entity odeslat požadavek na následující koncový bod. Můžete definovat další specifikace pomocí hlaviček a parametrů adresy URL.

Koncový bod `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Jsou vyžadovány následující parametry adresy URL:
- mkt. Na trhu, odkud pocházejí výsledky. 
- otázky. Vyhledávací dotaz entity.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Hledání Entity Bing – elementy QuickStart](quickstarts/csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled vyhledávání Entity Bing](search-the-web.md )
[referenční dokumentace rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
