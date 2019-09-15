---
title: 'Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Pythonu'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: fef329a46594071b5f1621e9a46140f407eeb415
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996726"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Rychlý start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Pythonu

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace v Pythonu pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) .

## <a name="prerequisites"></a>Požadavky

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbeném prostředí IDE nebo editoru a přidejte následující příkaz import.

   ```python
   import requests
   import json
   ```

2. Vytvořte proměnné pro text, který chcete kontrolovat pravopis, klíč předplatného a váš koncový bod Kontrola pravopisu Bingu.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Vytvoření parametrů pro požadavek

1. Vytvořte nový slovník s `text` jako klíč a text jako hodnotu.

    ```python
    data = {'text': example_text}
    ```

2. Přidejte parametry pro vaši žádost. Přidejte svůj kód na trhu `mkt=`po. Kód na trhu je země, ze které provedete požadavek. Také přidejte svůj režim kontroly pravopisu po `&mode=`. Režim je buď `proof` (catch nejvíc pravopisné/gramatické chyby `spell` ), nebo (catch nejvíc pravopisu, ale ne tolik gramatických chyb).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Přidejte do `Ocp-Apim-Subscription-Key` záhlaví hlavičkuaklíčpředplatného.`Content-Type`

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat požadavek a přečíst odpověď

1. Odešlete požadavek POST pomocí knihovny požadavků.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Získejte odpověď JSON a vytiskněte ji.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Příklad odpovědi JSON

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
> [Vytvoření webové aplikace na jedné stránce](../tutorials/spellcheck.md)

- [Co je rozhraní API Bingu pro kontrolu pravopisu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
