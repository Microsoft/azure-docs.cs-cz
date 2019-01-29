---
title: Koncové body pro vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu rozhraní API pro vyhledávání entit.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: aahi
ms.openlocfilehash: 561c64db2b97ed8792acab6cc87de861ecc30fe9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183981"
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
- mkt. Trh, odkud pochází výsledky. 
- q. Vyhledávací dotaz entity.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Šablony rychlý start pro vyhledávání entit Bingu](quickstarts/csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Vyhledávání entit Bingu přehled](search-the-web.md )
[Reference k rozhraní API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
