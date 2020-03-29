---
title: Použití hodnocení k zobrazení odpovědí – Vyhledávání entit Bingu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí hodnocení zobrazit odpovědi, které vrátí rozhraní API pro vyhledávání entit Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423915"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Použití pořadí k zobrazení výsledků vyhledávání entit  

Každá odpověď hledání entity obsahuje odpověď [RankingResponse,](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) která určuje, jak je nutné zobrazit výsledky hledání vrácené rozhraním API pro vyhledávání entit Bingu. Výsledky v hodnocení odpovědí do pole, mainline a sidebar obsahu. Výsledek pólu je nejdůležitější nebo prominentní výsledek a měl by být zobrazen jako první. Pokud nezobrazíte zbývající výsledky v tradičním formátu hlavní a postranní čáry, je nutné poskytnout obsah hlavní čáry vyšší viditelnost než obsah postranního panelu. 
  
V rámci každé skupiny pole [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifikuje pořadí, ve které se musí obsah zobrazit. Každá položka poskytuje dva způsoby, jak identifikovat výsledek v rámci odpovědi.  
 

|Pole | Popis  |
|---------|---------|
|`answerType` a `resultIndex` | `answerType`identifikuje odpověď (entita nebo `resultIndex` místo) a identifikuje výsledek v rámci této odpovědi (například entita). Index začíná na 0.|
|`value`    | `value`Obsahuje ID, které odpovídá ID odpovědi nebo výsledku v rámci odpovědi. Odpověď nebo výsledky obsahují ID, ale ne obojí. |
  
Použití `answerType` a `resultIndex` je dvoustupňový proces. Nejprve `answerType` použijte k identifikaci odpovědi, která obsahuje výsledky k zobrazení. Pak `resultIndex` použijte k indexování výsledků této odpovědi, abyste získali výsledek k zobrazení. (Hodnota `answerType` je název pole v objektu [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Pokud máte zobrazit všechny výsledky odpovědi společně, položka odpovědi pořadí neobsahuje `resultIndex` pole.

Pomocí ID vyžaduje, abyste spárovali ID pořadí s ID odpovědi nebo jednoho z jeho výsledků. Pokud objekt odpovědi `id` obsahuje pole, zobrazte všechny výsledky odpovědi společně. Pokud například `Entities` objekt obsahuje `id` pole, zobrazte všechny články entit společně. Pokud `Entities` objekt `id` pole neobsahuje, každá entita obsahuje `id` pole a odpověď pořadí promíchá entity s výsledky místa.  
  
## <a name="ranking-response-example"></a>Příklad odpovědi na pořadí

Následující ukazuje příklad [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Na základě této odpovědi v hodnocení by se na postranním panelu zobrazovaly výsledky dvou entit souvisejících s Jimi Hendrixem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-entities-search-single-page-app.md)
