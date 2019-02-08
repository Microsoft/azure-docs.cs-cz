---
title: Použití pořadí k zobrazení odpovědi – vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít hodnocení k zobrazení odpovědi, které vrací rozhraní API Bingu pro vyhledávání entit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9e2a4075436145a0cc185b7ab1b406fa8d27b8e3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867830"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Použití pořadí pro zobrazení výsledků vyhledávání entit  

Obsahuje každou odpověď vyhledávání entit [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpověď, která určuje, jak je třeba zobrazit výsledky hledání rozhraní API Bingu pro vyhledávání entit. Hodnocení odpovědí skupiny výsledky do pole, hlavní linii a boční panel obsah. Pole výsledku je výsledkem nejdůležitější nebo viditelného a by měl být zobrazen jako první. Pokud se nezobrazují zbývající výsledky v tradiční hlavní linie a boční panel formátu, je nutné zadat hlavní linie viditelnost obsahu vyšší než postranního panelu obsahu. 
  
V rámci jednotlivých skupin [položky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) pole určuje pořadí, ve kterém obsah musí být uvedena v. Každá položka poskytuje dva způsoby, jak určit výsledek v rámci odpověď.  
 

|Pole | Popis  |
|---------|---------|
|`answerType` a `resultIndex` | `answerType` Určuje odpověď (Entity nebo vložit) a `resultIndex` identifikuje výsledků v rámci této odpovědí (například entitu). Index začíná hodnotou 0.|
|`value`    | `value` Obsahuje ID, které odpovídá ID odpověď nebo výsledků v rámci odpověď. Odpověď nebo výsledky obsahují ID, ale ne obojí. |
  
Použití `answerType` a `resultIndex` je dvoustupňový proces. Nejprve pomocí `answerType` k identifikaci odpověď, která obsahuje výsledky k zobrazení. Pak pomocí `resultIndex` index získat výsledek pro zobrazení výsledků dané odpovědi. ( `answerType` Hodnota je název pole v [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu.) Pokud už má společně zobrazit výsledky všech odpovědí, odpověď na hodnocení položky neobsahuje `resultIndex` pole.

Pomocí ID vyžaduje, abyste tak, aby odpovídaly ID hodnocení s ID odpověď nebo některou z jeho výsledky. Pokud objekt odpovědi obsahuje `id` pole, společně zobrazit výsledky všech odpovědí. Například pokud `Entities` objekt zahrnuje `id` pole, zobrazit všechny články entit najednou. Pokud `Entities` objekt neobsahuje `id` pole, pak Každá entita obsahuje `id` pole a hodnocení odpovědi kombinuje entity s výsledky místech.  
  
## <a name="ranking-response-example"></a>Hodnocení příklad odpovědi

Následující příklad ukazuje, [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Podle této odpovědi na pořadí, na bočním panelu zobrazí související se Jimi Hendrix výsledky dvě entity.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-entities-search-single-page-app.md)
