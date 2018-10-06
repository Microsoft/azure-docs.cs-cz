---
title: Koncové body pro vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu rozhraní API pro vyhledávání entit.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: b1b89cbacf43df544261847d4e3fae396a52f423
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816272"
---
# <a name="entity-search-endpoints"></a>Hledat entity koncové body
**API pro vyhledávání entit** zahrnuje jeden koncový bod.

## <a name="endpoint"></a>Koncový bod
Požádat o výsledků vyhledávání entit, odesílejte požadavek na následující koncový bod. Použijte k definování další specifikace hlaviček a parametrů adresy URL.

Koncový bod `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Jsou vyžadovány následující parametry adresy URL:
- mkt. Na trhu, odkud pochází výsledky. 
- Q. Vyhledávací dotaz entity.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Šablony rychlý start pro vyhledávání entit Bingu](quickstarts/csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Vyhledávání entit Bingu přehled](search-the-web.md )
[Reference k rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
