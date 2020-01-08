---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a Pythonu – Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448453"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a Pythonu

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

2. Vytvořte proměnné pro text, který chcete kontrolovat pravopis, klíč předplatného a váš koncový bod Kontrola pravopisu Bingu. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

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

2. Přidejte parametry pro vaši žádost. Po `mkt=`přidejte svůj kód na trhu. Kód na trhu je země, ze které provedete požadavek. Také po `&mode=`připojit režim kontroly pravopisu. Režim je buď `proof` (catch nejvíc pravopis/gramatické chyby) nebo `spell` (zachycuje většinu pravopisů, ale ne tolik gramatických chyb).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Přidejte hlavičku `Content-Type` a klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key`.

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
> [Vytvoření webové aplikace na jedné stránce](../tutorials/spellcheck.md)

- [Co je rozhraní API Bingu pro kontrolu pravopisu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
