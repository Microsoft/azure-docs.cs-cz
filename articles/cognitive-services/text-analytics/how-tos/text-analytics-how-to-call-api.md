---
title: Volání rozhraní Text Analytics API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak můžete volat Azure Cognitive Services Text Analytics REST API a Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: afb576c265ccdd4a014ed678331f030a0442a197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219299"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Jak volat rozhraní REST API analýzy textu

Volání rozhraní **API pro analýzu textu** jsou volání HTTP POST/GET, která můžete formulovat v libovolném jazyce. V tomto článku používáme REST a [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) k předvedení klíčových konceptů.

Každý požadavek musí obsahovat přístupový klíč a koncový bod HTTP. Koncový bod určuje oblast, kterou jste zvolili během registrace, adresu URL `sentiment`služby `languages`a `entities`prostředek použitý v požadavku: , `keyphrases`, , a . 

Připomeňme si, že analýza textu je bezstavová, takže nejsou žádné datové prostředky ke správě. Váš text je odeslán, analyzován po obdržení a výsledky jsou okamžitě vráceny do volající aplikace.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definice schématu JSON

Vstup musí být JSON v nezpracovaném nestrukturovaném textu. Jazyk XML není podporován. Schéma je jednoduché, skládající se z prvků popsaných v následujícím seznamu. 

V současné době můžete odeslat stejné dokumenty pro všechny operace Služby Analýza textu: mínění, klíčová fráze, detekce jazyka a identifikace entity. (Schéma se pravděpodobně bude lišit pro každou analýzu v budoucnu.)

| Element | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datový typ je řetězec, ale v praxi ID dokumentu mají tendenci být celá čísla. | Požaduje se | Systém používá ID, které zadáte k strukturě výstupu. Kódy jazyků, klíčové fráze a skóre mínění jsou generovány pro každé ID v žádosti.|
|`text` | Nestrukturovaný nezpracovaný text, až 5 120 znaků. | Požaduje se | Pro detekci jazyka může být text vyjádřen v libovolném jazyce. Pro analýzu mínění, extrakci klíčových frází a identifikaci entity musí být text v [podporovaném jazyce](../text-analytics-supported-languages.md). |
|`language` | 2znakový kód [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) pro [podporovaný jazyk](../text-analytics-supported-languages.md) | Různé | Vyžadováno pro analýzu mínění, extrakci klíčových frází a propojení entit; volitelné pro detekci jazyka. Pokud ji vyloučíte, nedojde k žádné chybě, ale bez ní je analýza oslabena. Kód jazyka by měl `text` odpovídat vámi poskytnutému jazyku. |

Další informace o limitech najdete v tématu [Přehled analýzy textu > omezení dat](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Nastavení požadavku v Pošťákovi

Služba přijímá požadavek o velikosti až 1 MB. Pokud používáte Postman (nebo jiný testovací nástroj webového rozhraní API), nastavte koncový bod tak, aby zahrnoval prostředek, který chcete použít, a zadejte přístupový klíč v hlavičce požadavku. Každá operace vyžaduje připojit příslušný prostředek ke koncovému bodu. 

1. V Pošťák:

   + Jako typ požadavku zvolte **Účtovat.**
   + Vložte do koncového bodu, který jste zkopírovali ze stránky portálu.
   + Připojit prostředek.

   Koncové body prostředků jsou následující (oblast se může lišit):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Nastavte tři hlavičky požadavku:

   + `Ocp-Apim-Subscription-Key`: přístupový klíč získaný z portálu Azure.
   + `Content-Type`: aplikace/json.
   + `Accept`: aplikace/json.

   Váš požadavek by měl vypadat podobně jako následující snímek obrazovky, za předpokladu, že **prostředek /keyPhrases.**

   ![Žádost o snímek obrazovky s koncovým bodem a záhlavími](../media/postman-request-keyphrase-1.png)

4. Klikněte na **Tělo** a zvolte **nezpracovaný** formát.

   ![Žádost o snímek obrazovky s nastavením těla](../media/postman-request-body-raw.png)

5. Vložte do některých dokumentů JSON ve formátu, který je platný pro zamýšlenou analýzu. Další informace o konkrétní analýze naleznete v následujících tématech:

  + [Detekce jazyka](text-analytics-how-to-language-detection.md)  
  + [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)  
  + [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznávání entit](text-analytics-how-to-entity-linking.md)  


6. Chcete-li odeslat žádost, klepněte na tlačítko **Odeslat.** Informace o počtu požadavků, které můžete za minutu a sekundu odeslat, naleznete v části [Datové limity](../overview.md#data-limits) v přehledu.

   V Pošťák, odpověď se zobrazí v dalším okně dolů, jako jeden dokument JSON, s položkou pro každé ID dokumentu uvedené v požadavku.

## <a name="see-also"></a>Viz také 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zjištění jazyka](text-analytics-how-to-language-detection.md)
