---
title: 'Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí Pythonu | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu pomocí Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 24416bc2259fdd35581699b11f599ea48e349d5a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564626"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Rychlý start: Volání koncového bodu Vlastní vyhledávání Bingu pomocí Pythonu

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu. I když je tato aplikace napsaná v Pythonu, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Požadavky

- Instance Vlastní vyhledávání Bingu. Další [informace najdete v tématu rychlý Start: Pro další informace vytvořte první](quick-start.md) instanci vlastní vyhledávání Bingu.
- [Python](https://www.python.org/) 2. x nebo 3. x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu v oblíbeném prostředí IDE nebo editoru a přidejte následující příkazy importu. Vytvořte proměnné pro svůj klíč předplatného, ID vlastní konfigurace a hledaný termín. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání 

1. Vytvořte adresu URL žádosti připojením hledaného termínu k `q=` parametru dotazu a `customconfig=`ID vlastní konfigurace vaší instance hledání. parametry oddělte `&` znakem. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Odešlete požadavek do vaší instance Vlastní vyhledávání Bingu a vytiskněte vrácené výsledky hledání.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
