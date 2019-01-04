---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Pythonu'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3930de4d8d1f50c0ba6908ea642fc152c29b7371
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744798"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Rychlý start: Svůj první dotaz v jazyce Python pro vizuální vyhledávání Bingu

V tomto rychlém startu můžete provést první volání do rozhraní API vizuální vyhledávání Bingu a zobrazení výsledků hledání. Tato jednoduchá aplikace JavaScript odešle obrázek do rozhraní API a zobrazí informace vrácené o něm. Zatímco tato aplikace je napsána v jazyce JavaScript, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Při nahrávání místní image, data formuláře POST musí obsahovat hlavičku Content-Disposition. Její parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkaz importu.

    ```python
    import requests, json
    ```

2. Vytváření proměnných pro váš klíč předplatného, koncový bod a cestu k obrázku, který se nahrávání.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Vytvořte objekt slovníku k ukládání informací hlavičky vašich požadavků. Váš klíč předplatného svázat řetězec `Ocp-Apim-Subscription-Key`, jak je znázorněno níže.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Vytvořte jiný slovník obsahující bitové kopie, která bude odeslána při odeslání požadavku a otevřít. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

1. Vytvořit metodu nazvanou `print_json()` využít v odpovědi rozhraní API a tisk ve formátu JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Odeslání požadavku

1. Použití `requests.post()` odeslat požadavek na rozhraní API vizuální vyhledávání Bingu. Zahrňte řetězec pro koncový bod, záhlaví a informace o souboru. Tisk `response.json()` s `print_json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](../tutorial-bing-visual-search-single-page-app.md)