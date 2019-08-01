---
title: Volání rozhraní Text Analytics API
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak volat Analýza textu REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 14d3864f654dac42566441b3729de0cf88482295
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697862"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Způsob volání Analýza textu REST API

Volání **rozhraní API pro analýzu textu** jsou volání http post/Get, která lze formulovat v libovolném jazyce. V tomto článku k předvedení klíčových konceptů používáme REST a [post](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) .

Každý požadavek musí zahrnovat váš přístupový klíč a koncový bod HTTP. Koncový bod určuje oblast, kterou jste zvolili při registraci, adresu URL služby a prostředek, který se používá na žádosti `sentiment`: `keyphrases`, `languages`, a `entities`. 

Odvolání tohoto Analýza textu je bezstavové, takže neexistují žádné datové assety, které by bylo možné spravovat. Váš text se nahraje, analyzuje se po přijetí a výsledky se vrátí hned do volající aplikace.

> [!Tip]
> Chcete-li zjistit, jak funguje rozhraní API, můžete odeslat požadavky POST z integrované **testovací konzoly API**, které jsou k dispozici na libovolné stránce s [dokumentem rozhraní API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Není k dispozici žádná instalace a jediným požadavkem je vložit přístupový klíč a dokumenty JSON do žádosti. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>Definice schématu JSON

Vstup musí být JSON v nezpracovaném nestrukturovaném textu. KÓD XML není podporován. Schéma je jednoduché a skládá se z prvků popsaných v následujícím seznamu. 

V současné době můžete pro všechny operace Analýza textu odeslat stejné dokumenty: mínění, klíčová fráze, rozpoznávání jazyka a identifikace entit. (Schéma se může v budoucnu lišit pro každou analýzu.)

| Prvek | Platné hodnoty | Požadováno? | Použití |
|---------|--------------|-----------|-------|
|`id` |Datovým typem je řetězec, ale v praxi se ID dokumentů považují za celá čísla. | Požadováno | Systém používá ID, která zadáte k strukturování výstupu. Pro každé ID v žádosti jsou vygenerovány kódy jazyka, klíčové fráze a výsledky mínění.|
|`text` | Nestrukturovaný nezpracovaný text, maximálně 5 120 znaků. | Požadováno | V případě detekce jazyka lze text vyjádřit v jakémkoli jazyce. Pro analýzu mínění, extrakci klíčových frází a identifikaci entit musí být text v [podporovaném jazyce](../text-analytics-supported-languages.md). |
|`language` | 2 – znakový kód [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) pro [podporovaný jazyk](../text-analytics-supported-languages.md) | Různé | Vyžaduje se pro analýzu míněníí, extrakci klíčových frází a propojení entit. volitelné pro detekci jazyka. Pokud vyloučíte, nedošlo k žádné chybě, ale analýza je bez něj oslabena. Kód jazyka by měl odpovídat `text` vašemu zadání. |

Další informace o omezeních najdete v tématu [Analýza textu přehled >ch omezení dat](../overview.md#data-limits). 

## <a name="set-up-a-request-in-postman"></a>Nastavení žádosti na post

Služba přijme požadavek o velikosti až 1 MB. Pokud používáte metodu post (nebo jiný nástroj pro testování webového rozhraní API), nastavte koncový bod tak, aby zahrnoval prostředek, který chcete použít, a zadejte přístupový klíč v hlavičce požadavku. Každá operace vyžaduje, abyste ke koncovému bodu připojili příslušný prostředek. 

1. V příspěvku:

   + Jako typ žádosti vyberte **post** .
   + Vložte do koncového bodu, který jste zkopírovali ze stránky portálu.
   + Připojit prostředek.

   Koncové body prostředků jsou následující (vaše oblast se může lišit):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. Nastavte tři hlavičky žádosti:

   + `Ocp-Apim-Subscription-Key`: přístupový klíč získaný z Azure Portal.
   + `Content-Type`: Application/JSON.
   + `Accept`: Application/JSON.

   Váš požadavek by měl vypadat podobně jako na následujícím snímku obrazovky za předpokladu, že **/keyPhrases** prostředek.

   ![Snímek obrazovky žádosti s koncovým bodem a záhlavími](../media/postman-request-keyphrase-1.png)

4. Klikněte na **text** a pro formát vyberte **raw** .

   ![Snímek obrazovky žádosti s nastavením textu](../media/postman-request-body-raw.png)

5. Vložte je do některých dokumentů JSON ve formátu, který je platný pro zamýšlenou analýzu. Další informace o konkrétní analýze najdete v následujících tématech:

  + [Rozpoznávání jazyka](text-analytics-how-to-language-detection.md)  
  + [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)  
  + [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)  
  + [Rozpoznávání entit](text-analytics-how-to-entity-linking.md)  


6. Žádost odešlete kliknutím na **Odeslat** . Informace o počtu požadavků, které můžete poslat za minutu a sekundu, najdete v části [omezení dat](../overview.md#data-limits) v přehledu.

   V poli post se odpověď zobrazuje v následujícím okně jako jeden dokument JSON s položkou pro každé ID dokumentu, které je v požadavku k dispozici.

## <a name="see-also"></a>Viz také: 

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistit jazyk](text-analytics-how-to-language-detection.md)
