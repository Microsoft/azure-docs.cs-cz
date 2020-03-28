---
title: 'Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API a Pythonu – kontrola pravopisu bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448453"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Úvodní příručka: Kontrola pravopisu pomocí rozhraní API PRO KONTROLU pravopisu Bingu REST API a Pythonu

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní REST API kontroly pravopisu Bingu. Tato jednoduchá aplikace Pythonu odešle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Požadavky

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializovat aplikaci

1. Vytvořte nový soubor Pythonu ve svém oblíbeném rozhraní IDE nebo editoru a přidejte následující příkaz importu.

   ```python
   import requests
   import json
   ```

2. Vytvořte proměnné pro text, který chcete zkontrolovat pravopisem, klíč předplatného a koncový bod kontroly pravopisu Bingu. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Vytvořit parametry pro požadavek

1. Vytvořte nový slovník `text` s jako klíč a text jako hodnotu.

    ```python
    data = {'text': example_text}
    ```

2. Přidejte parametry pro váš požadavek. Připojit kód trhu `mkt=`za . Kód trhu je země, ze které podáváte žádost. Také připojit režim kontroly pravopisu po `&mode=`. Režim je `proof` buď (zachytí většinu `spell` pravopisných/gramatických chyb) nebo (zachytí většinu pravopisu, ale ne tolik gramatické chyby).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Přidejte `Content-Type` záhlaví a klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Odeslat žádost a přečíst odpověď

1. Odešlete požadavek POST pomocí knihovny požadavků.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Získejte odpověď JSON a vytiskněte ji.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Spuštění aplikace

Pokud používáte příkazový řádek, spusťte aplikaci pomocí následujícího příkazu.

```bash
python <FILE_NAME>.py
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
