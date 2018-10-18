---
title: 'Rychlý start: Rozhraní API pro kontrolu pravopisu Bingu, Python'
titlesuffix: Azure Cognitive Services
description: Umožňuje získat informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní API pro kontrolu pravopisu Bingu.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 8614e4388f45c3bc9f71697bad44589e2165f6c8
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803286"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>Rychlý start pro rozhraní API pro kontrolu pravopisu Bingu s využitím Pythonu 

V tomto článku se dozvíte, jak používat [rozhraní API pro kontrolu pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) společně s Pythonem. Rozhraní API pro kontrolu pravopisu vrací seznam slov, která nerozpozná, a jejich navrhované náhrady. Většinou odešlete do tohoto rozhraní API text a potom buď provedete v textu navrhovaná nahrazení, nebo návrhy ukážete uživateli vaší aplikace, aby se mohl rozhodnout, zda chcete nahrazení provést. V tomto článku se dozvíte, jak odeslat požadavek obsahující text „Hollo, wrld!“. Navrhované náhrady jsou „Hello“ a „world“.

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu budete potřebovat [Python 3.x](https://www.python.org/downloads/).

Potřebujete [účet rozhraní API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API pro kontrolu pravopisu Bingu v7**. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/#lang). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč nebo můžete použít klíč placeného předplatného z řídicího panelu Azure.

## <a name="get-spell-check-results"></a>Získání výsledků kontroly pravopisu

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
import http.client, urllib.parse, json

text = 'Hollo, wrld!'

data = {'text': text}

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

headers = {'Ocp-Apim-Subscription-Key': key,
'Content-Type': 'application/x-www-form-urlencoded'}

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>

conn = http.client.HTTPSConnection(host)
body = urllib.parse.urlencode (data)
conn.request ("POST", path + params, body, headers)
response = conn.getresponse ()
output = json.dumps(json.loads(response.read()), indent=4)
print (output)
```

**Odpověď**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz o kontrole pravopisu Bingu](../tutorials/spellcheck.md)

## <a name="see-also"></a>Viz také

- [Přehled kontroly pravopisu Bingu](../proof-text.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
