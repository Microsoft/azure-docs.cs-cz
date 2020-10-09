---
title: Koncový bod rozhraní API Bingu pro vyhledávání entit
titleSuffix: Azure Cognitive Services
description: Rozhraní API Bingu pro vyhledávání entit má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky hledání se vrátí ve formátu JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072670"
---
# <a name="bing-entity-search-api-endpoint"></a>rozhraní API Bingu pro vyhledávání entit koncový bod


Rozhraní API Bingu pro vyhledávání entit má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky hledání se vrátí ve formátu JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Získat výsledky entity z koncového bodu

Pokud chcete získat výsledky entity pomocí **rozhraní API Bingu**, odešlete `GET` požadavek na následující koncový bod. K přizpůsobení žádosti o vyhledávání použijte [záhlaví](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) a [Parametry dotazů](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . Žádosti o vyhledávání lze odeslat pomocí `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je rozhraní API Bingu pro vyhledávání entit?](overview.md)

## <a name="see-also"></a>Viz také 

Další informace o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách a dalších najdete v článku referenční informace o [rozhraní API Bingu pro vyhledávání entit v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
