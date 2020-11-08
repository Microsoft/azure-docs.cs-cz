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
ms.openlocfilehash: 1d9b7c79919569830834915fc609b849e717dce8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365835"
---
# <a name="bing-entity-search-api-endpoint"></a>rozhraní API Bingu pro vyhledávání entit koncový bod

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).


Rozhraní API Bingu pro vyhledávání entit má jeden koncový bod, který vrací entity z webu na základě dotazu. Tyto výsledky hledání se vrátí ve formátu JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Získat výsledky entity z koncového bodu

Pokud chcete získat výsledky entity pomocí **rozhraní API Bingu** , odešlete `GET` požadavek na následující koncový bod. K přizpůsobení žádosti o vyhledávání použijte [záhlaví](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) a [Parametry dotazů](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) . Žádosti o vyhledávání lze odeslat pomocí `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je rozhraní API Bingu pro vyhledávání entit?](overview.md)

## <a name="see-also"></a>Viz také 

Další informace o hlavičkách, parametrech, kódech trhu, objektech odpovědí, chybách a dalších najdete v článku referenční informace o [rozhraní API Bingu pro vyhledávání entit v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .