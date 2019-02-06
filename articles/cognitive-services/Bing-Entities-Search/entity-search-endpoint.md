---
title: Koncový bod rozhraní API pro vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Další informace o koncový bod rozhraní API pro vyhledávání entit Bingu a odesílat žádosti.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: cae65c8fb7deb9f68a297de2058a86249b60136b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753976"
---
# <a name="bing-entity-search-api-endpoint"></a>Koncový bod rozhraní API pro vyhledávání entit Bingu


Rozhraní API Bingu pro vyhledávání entit má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky jsou vráceny ve formátu JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Získat výsledky entit z koncového bodu

Chcete-li získat entity výsledků s použitím **rozhraní API Bingu pro**, odeslat `GET` požadavek na následující koncový bod. Použití [záhlaví](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) a [parametrů dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) přizpůsobení požadavku hledání. Můžete být odesílány žádosti o hledání pomocí `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Co je API pro vyhledávání entit Bingu?](overview.md)

## <a name="see-also"></a>Další informace najdete v tématech 

Další informace o záhlaví, parametry, kódy na trhu, objekty odpovědi, chyby a další, najdete v článku [API pro vyhledávání entit Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) článku.
