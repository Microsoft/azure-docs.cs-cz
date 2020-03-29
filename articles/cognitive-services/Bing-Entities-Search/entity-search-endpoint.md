---
title: Koncový bod rozhraní API pro vyhledávání entit Bingu
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro vyhledávání entit Bingu má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky hledání jsou vráceny v JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072670"
---
# <a name="bing-entity-search-api-endpoint"></a>Koncový bod rozhraní API pro vyhledávání entit Bingu


Rozhraní API pro vyhledávání entit Bingu má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky hledání jsou vráceny v JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Získání výsledků entit z koncového bodu

Chcete-li získat výsledky entity pomocí `GET` rozhraní API **Bing**, odešlete požadavek na následující koncový bod. K přizpůsobení požadavku na vyhledávání použijte [záhlaví](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) a [parametry dotazu.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) Požadavky na vyhledávání lze `?q=` odeslat pomocí parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je rozhraní API pro vyhledávání entit Bingu?](overview.md)

## <a name="see-also"></a>Viz také 

Další informace o záhlavích, parametrech, kódech trhu, objektech odpovědí, chybách a dalšíinformace naleznete v referenčním článku [rozhraní API pro vyhledávání entit Bingu v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
