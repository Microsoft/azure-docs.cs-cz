---
title: 'Rychlý Start: Získání přehledů obrázků pomocí REST API a Pythonu – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Naučte se nahrát obrázek pomocí rozhraní API pro vizuální vyhledávání Bingu a Pythonu a získat přehled o imagi.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: 712f86eeaa49c1afe281ad5ede7a6cf2cc0ada4b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074976"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Rychlý Start: Získání přehledů obrázků pomocí Vizuální vyhledávání Bingu REST API a Pythonu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání rozhraní API pro vizuální vyhledávání Bingu. Tato aplikace v Pythonu nahraje obrázek do rozhraní API a zobrazí informace, které vrátí. I když je tato aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbených IDE nebo editoru a přidejte následující `import` příkaz:

    ```python
    import requests, json
    ```

2. Vytvořte proměnné pro klíč předplatného, koncový bod a cestu k imagi, kterou právě nahráváte. Pro hodnotu `BASE_URI` můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Když nahrajete místní obrázek, data formuláře musí obsahovat `Content-Disposition` hlavičku. Nastavte jeho `name` parametr na "image" a nastavte `filename` parametr na název souboru vaší image. Obsah formuláře zahrnuje binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Vytvořte objekt Dictionary, který bude uchovávat informace v hlavičce vaší žádosti. Navažte klíč předplatného na řetězec `Ocp-Apim-Subscription-Key` .

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Vytvořte další slovník, který bude obsahovat image, která se otevře a nahraje při odeslání žádosti.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Vytvořte metodu volanou `print_json()` k přijetí odpovědi rozhraní API a vytiskněte JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Odeslat žádost

Slouží `requests.post()` k odeslání žádosti do rozhraní API pro vizuální vyhledávání Bingu. Zadejte řetězec pro informace o koncovém bodu, hlavičce a souboru. Tisknout `response.json()` pomocí `print_json()` .

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
