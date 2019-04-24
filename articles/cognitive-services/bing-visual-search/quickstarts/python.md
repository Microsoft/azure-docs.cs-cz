---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Pythonu'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510849"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Pythonu

V tomto rychlém startu můžete provést první volání rozhraní API vizuální vyhledávání Bingu a prohlédněte si výsledky. Tuto aplikaci v Pythonu odešle obrázek do rozhraní API a zobrazí informace, které vrátí. I když je tato aplikace napsané v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Při nahrávání místní image musíte zahrnout data formuláře `Content-Disposition` záhlaví. Je nutné nastavit jeho `name` parametr "image" kde můžete nastavit `filename` parametr libovolný řetězec. Obsah ve formátu zahrnout binární data bitové kopie. Maximální velikost, kterou můžete nahrát je 1 MB.

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

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující `import` – příkaz:

    ```python
    import requests, json
    ```

2. Vytváření proměnných pro váš klíč předplatného, koncový bod a cesta k image, kterou jste nahrávání:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Vytvořte objekt slovníku k ukládání informací hlavičky vaši žádost. Váš klíč předplatného svázat řetězec `Ocp-Apim-Subscription-Key`, jak je znázorněno níže:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Vytvořte jiný slovník obsahující bitové kopie, což je otevřít a odeslána při odeslání požadavku:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

1. Vytvořit metodu nazvanou `print_json()` využít v odpovědi rozhraní API a tisk ve formátu JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Odeslání požadavku

1. Použití `requests.post()` odeslat požadavek na rozhraní API vizuální vyhledávání Bingu. Zahrňte řetězec pro koncový bod, záhlaví a informace o souboru. Tisk `response.json()` s `print_json()`:

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
> [Vytvořit pro vizuální vyhledávání jednostránkovou webovou aplikaci](../tutorial-bing-visual-search-single-page-app.md)
