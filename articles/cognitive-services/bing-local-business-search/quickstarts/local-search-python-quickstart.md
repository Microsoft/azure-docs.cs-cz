---
title: Rychlý Start – odeslání dotazu do rozhraní API v Pythonu – vyhledávání místních obchodů Bingu
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít používat rozhraní API Bingu pro vyhledávání místních obchodních aplikací v Pythonu.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: adbf3d9abddf01ba67046cfa433ffd46f713ff83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536670"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rychlý Start: odeslání dotazu do rozhraní API Bingu pro vyhledávání místních obchodních hledání v Pythonu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

V tomto rychlém startu se dozvíte, jak odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful, která je kompatibilní s jakýmkoli programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místních odpovědí z rozhraní API pro vyhledávací dotaz.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Python](https://www.python.org/) 2. x nebo 3. x.
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" vytvořte prostředek vyhledávání Bingu vytvoření prostředku "  target="_blank"> Vyhledávání Bingu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.

## <a name="run-the-complete-application"></a>Spuštění úplné aplikace

Následující příklad získá lokalizované výsledky, které jsou implementovány v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte parametr dotazu. 
3. Definujte vyhledávací funkci, která vytvoří požadavek a přidá `Ocp-Apim-Subscription-Key` hlavičku.
4. Nastavte `Ocp-Apim-Subscription-Key` hlavičku. 
5. Vytvořte připojení a odešlete žádost.
6. Zobrazte výsledky ve formátu JSON.

Úplný kód pro tuto ukázku je následující:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Další kroky
- [Rychlý Start pro místní vyhledávání v jazyce Java](local-search-java-quickstart.md)
- [Rychlé zprovoznění hledání v jazyce C# pro místní obchod](local-quickstart.md)
- [Rychlý Start Node.js místního hledání firmy](local-search-node-quickstart.md)
