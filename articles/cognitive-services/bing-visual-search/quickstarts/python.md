---
title: 'Rychlý Start: Získání přehledů obrázků pomocí REST API a Pythonu – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat přehled o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446589"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Rychlý Start: Získání přehledů obrázků pomocí Vizuální vyhledávání Bingu REST API a Pythonu

V tomto rychlém startu můžete provést první volání rozhraní API pro vizuální vyhledávání Bingu a zobrazit výsledky. Tato aplikace v Pythonu nahraje obrázek do rozhraní API a zobrazí informace, které vrátí. I když je tato aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbených IDE nebo editoru a přidejte následující `import` příkaz:

    ```python
    import requests, json
    ```

2. Vytvořte proměnné pro klíč předplatného, koncový bod a cestu k imagi, kterou právě nahráváte. `BASE_URI`může to být globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Když nahrajete místní obrázek, data formuláře musí obsahovat `Content-Disposition` hlavičku. Je nutné nastavit jeho `name` parametr na hodnotu "image" a můžete nastavit `filename` parametr na libovolný řetězec. Obsah formuláře zahrnuje binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Vytvořte objekt Dictionary, který bude uchovávat informace v hlavičce vaší žádosti. Navažte klíč předplatného na `Ocp-Apim-Subscription-Key`řetězec, jak je znázorněno níže:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Vytvořte další slovník, který bude obsahovat image, která se otevře a nahraje při odeslání žádosti:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

1. Vytvořte metodu volanou `print_json()` v odpovědi rozhraní API a vytiskněte JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Odeslat žádost

1. Slouží `requests.post()` k odeslání žádosti do rozhraní API pro vizuální vyhledávání Bingu. Zadejte řetězec pro informace o koncovém bodu, hlavičce a souboru. Tisk `response.json()` pomocí `print_json()`:

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
> [Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](../tutorial-bing-visual-search-single-page-app.md)
