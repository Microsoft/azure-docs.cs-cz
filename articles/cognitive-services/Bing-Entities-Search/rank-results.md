---
title: Použití řazení pro zobrazení odpovědi | Microsoft Docs
description: Ukazuje, jak používat řazení zobrazíte odpovědi, které vrací rozhraní API služby Bing Entity Search.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059987"
---
# <a name="using-ranking-to-display-results"></a>Chcete-li zobrazit výsledky pomocí řazení  

Každá odpověď vyhledávání entity zahrnuje [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) odpovědět, podobně jako v odpovědi vyhledávání webové služby Bing, který určuje, jak je třeba zobrazit výsledky hledání. Odpověď hodnocení skupiny výsledky do pólu nejdůležitějších a obsah na bočním panelu. Výsledek pólu je nejdůležitější nebo viditelného výsledek a by měl být zobrazen jako první. Pokud se nezobrazují zbývající výsledky v tradičním nejdůležitějších a formát bočním panelu, je nutné zadat nejdůležitějších viditelnost obsahu vyšší než obsah bočním panelu. 
  
V rámci jednotlivých skupin [položky](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) pole identifikuje obsah musí být zadány v požadovaném pořadí. Každá položka nabízí dva způsoby, jak identifikovat výsledek v rámci odpověď.  
  
-   `answerType` a `resultIndex` – `answerType` pole identifikuje odpovědí (entit nebo místní) a `resultIndex` identifikuje výsledek v rámci odpovědí (například entity). Index je od nuly.  
  
-   `value` — `value` Pole obsahuje ID, které odpovídá ID odpověď nebo výsledek v rámci odpověď. Odpověď nebo výsledky obsahovat ID, ale ne obojí.  
  
Pomocí ID vyžaduje, abyste shodovat s ID hodnocení s ID odpověď nebo jednoho z jeho výsledky. Pokud obsahuje objekt odpovědí `id` pole, společně zobrazit výsledky všech odpovědí. Například pokud `Entities` objekt zahrnuje `id` pole, společně zobrazit všechny články entity. Pokud `Entities` objekt se nenachází `id` pole, pak Každá entita obsahuje `id` pole a odpovědi řazení zkombinuje entity ve výsledcích místech.  
  
Pomocí `answerType` a `resultIndex` je dvoustupňový proces. Nejprve pomocí `answerType` k identifikaci odpovědí, který obsahuje výsledky pro zobrazení. Potom pomocí `resultIndex` index do výsledků dané odpovědi získat výsledek, který má zobrazit. ( `answerType` Hodnota je název v poli [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektu.) Pokud se má zobrazit výsledky všech odpovědí společně, odpovědi řazení položky neobsahuje `resultIndex` pole.

## <a name="ranking-response-example"></a>Příklad odpovědi řazení

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

Podle této odpovědi řazení, by na bočním panelu zobrazí výsledky dvě entity související se Jimi Hendrix.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz služby Bing Entity Search](tutorial-bing-entities-search-single-page-app.md)
