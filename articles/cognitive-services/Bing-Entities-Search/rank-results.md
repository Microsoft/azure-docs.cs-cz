---
title: Použití hodnocení k zobrazení odpovědí – Vyhledávání entit Bingu
titleSuffix: Azure Cognitive Services
description: Naučte se používat hodnocení k zobrazení odpovědí, které rozhraní API Bingu pro vyhledávání entit vrátí.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: d5fbecd9c2fd6e3a9f1be29598bad50da4b77bbb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084608"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Použití hodnocení k zobrazení výsledků hledání entit  

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Každá odpověď hledání entity obsahuje odpověď [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) , která určuje, jak je nutné zobrazit výsledky hledání vrácené rozhraní API Bingu pro vyhledávání entit. Skupiny odpovědí na řazení jsou výsledkem do pole, hlavní a obsahu bočního panelu. Výsledek pole je nejdůležitější nebo výrazný výsledek a měl by se zobrazit jako první. Pokud se zbývající výsledky nezobrazí v tradičním formátu hlavní a postranního panelu, musíte poskytnout obsah hlavní větší viditelnost než obsah bočního panelu. 
  
V rámci každé skupiny pole [položek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifikuje pořadí, ve kterém se obsah musí vyskytovat. Každá položka poskytuje dva způsoby, jak identifikovat výsledek v odpovědi.  
 

|Pole | Description  |
|---------|---------|
|`answerType` a `resultIndex` | `answerType` Identifikuje odpověď (entitu nebo místo) a `resultIndex` identifikuje výsledek v rámci této odpovědi (například entity). Index začíná na 0.|
|`value`    | `value` Obsahuje ID, které odpovídá ID odpovědi nebo výsledku v odpovědi. Buď odpověď, nebo výsledky obsahují ID, ale ne obojí. |
  
Použití `answerType` a `resultIndex` je proces se dvěma kroky. Nejprve použijte `answerType` k identifikaci odpovědi, která obsahuje výsledky k zobrazení. Pak použijte `resultIndex` k indexování výsledků této odpovědi, aby se zobrazil výsledek. ( `answerType` Hodnota je název pole v objektu [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Pokud chcete zobrazit všechny výsledky odpovědi společně, položka odpovědi na řazení neobsahuje `resultIndex` pole.

Použití ID vyžaduje, abyste se shodovali s ID hodnocení s ID odpovědi nebo jedním z jeho výsledků. Pokud objekt odpovědi obsahuje `id` pole, zobrazí se všechny výsledky odpovědi společně. Například pokud `Entities` objekt obsahuje `id` pole, Zobrazit všechny články dohromady. Pokud `Entities` objekt neobsahuje `id` pole, pak Každá entita obsahuje `id` pole a odpověď na řazení kombinuje entity s výsledky míst.  
  
## <a name="ranking-response-example"></a>Příklad odpovědi na řazení

Následující příklad ukazuje příklad [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Na základě této odpovědi na toto řazení by postranní panel zobrazil výsledky dvou entit souvisejících s Jimi Hendrix.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](tutorial-bing-entities-search-single-page-app.md)
