---
title: Použití pořadí k zobrazení odpovědi – vyhledávání entit Bingu
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak použít hodnocení k zobrazení odpovědi, které vrací rozhraní API Bingu pro vyhledávání entit.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 4a336ccaea18ab84464f28aef170ccdc423b216d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814589"
---
# <a name="using-ranking-to-display-results"></a>Chcete-li zobrazit výsledky pomocí hodnocení  

Obsahuje každou odpověď vyhledávání entit [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpovědět, podobně jako v odpovědi na vyhledávání na webu Bingu, která určuje, jak je třeba zobrazit výsledky hledání. Hodnocení odpovědí skupiny výsledky do pole, hlavní linii a boční panel obsah. Pole výsledku je výsledkem nejdůležitější nebo viditelného a by měl být zobrazen jako první. Pokud se nezobrazují zbývající výsledky v tradiční hlavní linie a boční panel formátu, je nutné zadat hlavní linie viditelnost obsahu vyšší než postranního panelu obsahu. 
  
V rámci jednotlivých skupin [položky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) pole určuje pořadí, ve kterém obsah musí být uvedena v. Každá položka poskytuje dva způsoby, jak určit výsledek v rámci odpověď.  
  
-   `answerType` a `resultIndex` – `answerType` pole identifikuje odpovědí (Entity nebo vložit) a `resultIndex` identifikuje výsledků v rámci odpovědí (například entitu). Index je od nuly.  
  
-   `value` — `value` Pole obsahuje ID, které odpovídá ID odpověď nebo výsledků v rámci odpověď. Odpověď nebo výsledky obsahují ID, ale ne obojí.  
  
Pomocí ID vyžaduje, abyste tak, aby odpovídaly ID hodnocení s ID odpověď nebo některou z jeho výsledky. Pokud objekt odpovědi obsahuje `id` pole, společně zobrazit výsledky všech odpovědí. Například pokud `Entities` objekt zahrnuje `id` pole, zobrazit všechny články entit najednou. Pokud `Entities` objekt neobsahuje `id` pole, pak Každá entita obsahuje `id` pole a hodnocení odpovědi kombinuje entity s výsledky místech.  
  
Použití `answerType` a `resultIndex` je dvoustupňový proces. Nejprve pomocí `answerType` k identifikaci odpověď, která obsahuje výsledky k zobrazení. Pak použijete `resultIndex` index získat výsledek pro zobrazení výsledků dané odpovědi. ( `answerType` Hodnota je název pole v [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu.) Pokud už má společně zobrazit výsledky všech odpovědí, odpověď na hodnocení položky neobsahuje `resultIndex` pole.

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
> [Kurz pro vyhledávání entit Bingu](tutorial-bing-entities-search-single-page-app.md)
