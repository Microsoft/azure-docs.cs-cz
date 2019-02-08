---
title: 'Rychlý start: Rozhraní API Python pro kontrolu pravopisu Bingu'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro kontrolu pravopisu Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 5aa67c0e582d64f258da7abd01a9492daaf91efd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882280"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>Rychlý start pro rozhraní API pro kontrolu pravopisu Bingu s využitím Pythonu 

V tomto článku se dozvíte, jak používat [API kontrola pravopisu Bingu](https://azure.microsoft.com/services/cognitive-services/spell-check/) pomocí Pythonu. Rozhraní API pro kontrolu pravopisu vrací seznam slov, která nerozpozná, a jejich navrhované náhrady. Většinou odešlete do tohoto rozhraní API text a potom buď provedete v textu navrhovaná nahrazení, nebo návrhy ukážete uživateli vaší aplikace, aby se mohl rozhodnout, zda chce nahrazení provést. V tomto článku se dozvíte, jak odeslat požadavek obsahující text „Hollo, wrld!“. Navrhované náhrady jsou „Hello“ a „world“.

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu budete potřebovat [Python 3.x](https://www.python.org/downloads/).

Potřebujete [účet rozhraní API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API pro kontrolu pravopisu Bingu v7**. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/#lang). Při aktivaci bezplatné zkušební verze budete potřebovat poskytnutý přístupový klíč nebo můžete použít klíč placeného předplatného z řídicího panelu Azure. Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro kontrolu pravopisu Bingu](../tutorials/spellcheck.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přehled rozhraní API pro kontrolu pravopisu Bingu](../proof-text.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
