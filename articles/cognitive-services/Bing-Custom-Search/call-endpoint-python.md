---
title: 'Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Pythonu | Dokumentace Microsoftu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu pomocí Pythonu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: a6f8d3964c71d036053fe0d4136468fde690f872
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337273"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Pythonu

V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsaný v Pythonu, rozhraní API pro vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Požadavky

- Instanci vlastního vyhledávání Bingu. Zobrazit [rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu](quick-start.md) Další informace.
- [Python](https://www.python.org/) 2.x nebo 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. Vytvořte nový soubor Pythonu ve vašich oblíbených prostředím IDE nebo editorem a přidejte následující příkazy pro import. Vytváření proměnných pro váš klíč předplatného, ID konfigurace vlastní a hledaný termín. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslat a přijmout žádost o vyhledávání 

1. Vytvořit žádost o adresu URL připojením hledaný termín `q=` parametr dotazu a search instance vlastní ID konfigurace `customconfig=`. oddělení parametrů s `&` znak. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Odeslat požadavek do vaší instance vlastního vyhledávání Bingu a vytiskněte výsledky vráceného vyhledávání.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](./tutorials/custom-search-web-page.md)
