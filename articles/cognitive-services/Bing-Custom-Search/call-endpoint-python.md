---
title: 'Úvodní příručka: Zavolejte do koncového bodu vlastního vyhledávání Bingu pomocí Pythonu | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu pomocí Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238836"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí Pythonu

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v Pythonu, Rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Požadavky

- Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](quick-start.md)
- [Python](https://www.python.org/) 2.x nebo 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve svém oblíbeném rozhraní IDE nebo editoru a přidejte následující příkazy importu. Vytvořte proměnné pro klíč předplatného, Vlastní ID konfigurace a hledaný termín. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí požadavku na vyhledávání 

1. Vytvořte adresu URL požadavku připojením `q=` hledaného výrazu k parametru dotazu `customconfig=`a ID vlastní konfigurace instance hledání. oddělte parametry `&` znakem. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Odešlete požadavek do instance vlastního vyhledávání Bingu a vytiskněte vrácené výsledky hledání.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro vlastní vyhledávání](./tutorials/custom-search-web-page.md)
