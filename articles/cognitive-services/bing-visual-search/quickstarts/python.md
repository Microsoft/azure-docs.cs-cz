---
title: 'Úvodní příručka: Získání přehledů obrázků pomocí rozhraní REST API a Pythonu – vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat o něm přehledy.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446589"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Úvodní příručka: Získejte přehledy obrázků pomocí rozhraní API PRO Vizuální vyhledávání Bingu REST API a Pythonu

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vizuální vyhledávání Bingu a zobrazit výsledky. Tato aplikace Pythonu nahraje obrázek do rozhraní API a zobrazí informace, které vrátí. Ačkoli tato aplikace je napsána v Pythonu, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializovat aplikaci

1. Vytvořte nový soubor Pythonu ve svém oblíbeném `import` ide nebo editoru a přidejte následující příkaz:

    ```python
    import requests, json
    ```

2. Vytvořte proměnné pro klíč předplatného, koncový bod a cestu k obrázku, který nahráváte. `BASE_URI`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na webu Azure Portal pro váš prostředek:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Při nahrávání místního obrázku musí data `Content-Disposition` formuláře obsahovat záhlaví. Je nutné `name` nastavit jeho parametr na "image" `filename` a můžete nastavit parametr na libovolný řetězec. Obsah formuláře obsahuje binární data obrazu. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Vytvořte objekt slovníku pro uložení informací záhlaví požadavku. Spojte klíč předplatného `Ocp-Apim-Subscription-Key`s řetězcem , jak je znázorněno níže:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Vytvořte jiný slovník, který bude obsahovat obrázek, který se otevře a nahraje při odeslání žádosti:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

1. Vytvořte metodu volanou `print_json()` pro odpověď rozhraní API a vytiskněte json:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Odeslat žádost

1. Slouží `requests.post()` k odeslání požadavku do rozhraní API vizuálního vyhledávání Bingu. Zahrňte řetězec pro koncový bod, záhlaví a informace o souboru. Tisk `response.json()` `print_json()`s :

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](../tutorial-bing-visual-search-single-page-app.md)
