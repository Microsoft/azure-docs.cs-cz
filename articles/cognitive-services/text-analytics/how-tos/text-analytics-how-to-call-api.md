---
title: Volání rozhraní Text Analytics API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak zavolat REST API pro analýzu textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 52c5cb640bfb861fb2da52ee711fe3955a169bcf
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244024"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Volání REST API pro analýzu textu

Volání **rozhraní Text Analytics API** jsou volání HTTP POST nebo GET, které formulujete v libovolném jazyce. V tomto článku používáme REST a [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) pro předvedení klíčových konceptů.

Každý požadavek musí obsahovat svůj přístupový klíč a koncový bod HTTP. Určuje koncový bod oblast zvolili při registraci, adresa URL služby a prostředek, který používá na žádost: `sentiment`, `keyphrases`, `languages`, a `entities`. 

Připomínáme, že pro analýzu textu je bezstavové, takže nejsou žádné datové prostředky se mají spravovat. Nahrání text analyzován přijetí, a výsledky se vrátí okamžitě volající aplikaci.

> [!Tip]
> Pro jednorázové volání, uvidíte, jak funguje rozhraní API, je možné posílat požadavky POST z integrovaného **testovací rozhraní API konzoly**, k dispozici na všech [stránky dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Neexistuje žádné nastavení, a jediným požadavkem je na dokumenty JSON a přístupový klíč vložte do požadavku. 

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pomocí rozhraní API pro analýzu textu a [koncový bod a přístupový klíč](text-analytics-how-to-access-key.md) , vygeneruje se pro vás při registraci pro služby Cognitive Services. 

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definice schématu JSON

Vstup musí být v nezpracované nestrukturovaného textu JSON. XML není podporován. Schéma je jednoduché, který se skládá z prvků, je popsáno v následujícím seznamu. 

Aktuálně můžete odeslat dokumenty stejný pro všechny operace rozhraní Text Analytics: zabarvení, klíčových frází, rozpoznávání jazyka a identifikace entit. (Schéma je pravděpodobně lišit pro každou analýzy v budoucnu.)

| Element | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datový typ je řetězec, ale v praxi ID dokumentu mají být celá čísla. | Požaduje se | Systém používá ID zadáte strukturovat výstup. Pro každé ID v požadavku se generují kódech jazyků, klíčové fráze a skóre mínění.|
|`text` | Nestrukturované nezpracovaný text, maximálně 5 000 znaků. | Požaduje se | Pro rozpoznávání jazyka lze vyjádřit text v libovolném jazyce. Pro analýzu mínění, extrakci klíčových frází a identifikace entit, musí mít text [podporovaný jazyk](../text-analytics-supported-languages.md). |
|`language` | 2 znacích [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) kód [podporovaný jazyk](../text-analytics-supported-languages.md) | Různé | Vyžaduje se pro analýzu mínění, extrakci klíčových frází a propojování entit; volitelné pro rozpoznání jazyka. Se nezobrazí žádná chyba, pokud se můžete vyloučit, ale analýza správné provedení příslušných činností bez něj. Kód jazyka, musí odpovídat `text` zadáte. |

Další informace o omezeních najdete v tématu [Text Analytics – přehled > omezení datové](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Nastavit požadavek v nástroji Postman

Služba přijímá požádat o velikost až 1 MB. Pokud používáte Postman (nebo jiný nástroj pro testování webového rozhraní API), nastavení koncového bodu zahrnout prostředek, který chcete použít a zadejte přístupový klíč v hlavičce požadavku. Každá operace požaduje připojení odpovídající prostředek ke koncovému bodu. 

1. V nástroji Postman:

   + Zvolte **příspěvek** jako typu požadavku.
   + Vložte koncový bod, který jste zkopírovali ze stránky portálu.
   + Připojte prostředek.

  Koncové body prostředků jsou následujícím způsobem (vaší oblasti se mohou lišit):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities`

2. Nastavte tři hlavičky žádosti:

   + `Ocp-Apim-Subscription-Key`: přístupový klíč k získané z webu Azure portal.
   + `Content-Type`: application/json.
   + `Accept`: application/json.

  Váš požadavek by měl vypadat podobně jako na následujícím snímku obrazovky, za předpokladu, že **/keyPhrases** prostředků.

   ![Snímek obrazovky s koncovým bodem a hlavičky požadavku](../media/postman-request-keyphrase-1.png)

4. Klikněte na tlačítko **tělo** a zvolte **nezpracovaná** formátu.

   ![Požádat o snímek obrazovky s nastavením textu](../media/postman-request-body-raw.png)

5. Vložte několik dokumentů JSON ve formátu, který je platný pro určené analýzy. Další informace o konkrétní analýzy najdete v tématech níže:

  + [Rozpoznávání jazyka](text-analytics-how-to-language-detection.md)  
  + [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)  
  + [Analýza subjektivního hodnocení](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznávání entit (Preview)](text-analytics-how-to-entity-linking.md)  


6. Klikněte na tlačítko **odeslat** odešlete žádost. Můžete odeslat až 100 požadavků za minutu. 

  V nástroji Postman by se odpověď zobrazí v dalším okně dolů, jako jeden dokument JSON, s položkou pro každé ID dokumentu, který je zadaný v požadavku.

## <a name="see-also"></a>Další informace najdete v tématech 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistit jazyk](text-analytics-how-to-language-detection.md)
