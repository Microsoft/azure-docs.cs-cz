---
title: 'Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Pythonu.'
titlesuffix: Azure Cognitive Services
description: Začněte používat rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 1cf46fd5ec55f0b240f6bb4adbe49c1344a4663b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384870"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní REST API kontrolu pravopisu Bingu a Pythonu.

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST. Tato jednoduchá aplikace Python odešle žádost na rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Požadavky

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkaz importu.

   ```python
   import requests
   import json
   ```

2. Vytváření proměnných pro text, který má napsat kontrolu, váš klíč předplatného a váš koncový bod pro kontrolu pravopisu Bingu.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Parametry pro žádost o vytvoření

1. Vytvořit nový slovník s `text` jako klíč a text jako hodnotu.

    ```python
    data = {'text': example_text}
    ```

2. Přidáte parametry pro vaši žádost. Připojit na trhu kódu po `mkt=`. Kód na trhu je země, které provedete žádost z. Navíc vaše režimu kontroly pravopisu po připojení `&mode=`. Režim je buď `proof` (zachytí většina pravopisné nebo gramatické chyby) nebo `spell` (zachycuje většinu pravopisu, ale ne tolik gramatické chyby).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Přidat `Content-Type` záhlaví a klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat požadavek a čtení odpovědi

1. Odešlete požadavek POST pomocí knihovny požadavky.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Získání odpovědi JSON a vytisknout je.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Příklad JSON odpovědi

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
> [Vytvoření webové jednostránkové – aplikace](../tutorials/spellcheck.md)

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
