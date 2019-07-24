---
title: Rychlý Start – odeslání dotazu do rozhraní API Bingu pro vyhledávání místních obchodních hledání v Pythonu
titleSuffix: Azure Cognitive Services
description: Tento článek vám umožní začít používat rozhraní API Bingu pro vyhledávání místních obchodních aplikací v Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: c6da3c9a169f3b6e5885499d3a7bc5347902782e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423327"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rychlý start: Odeslání dotazu do rozhraní API Bingu pro vyhledávání na místních obchodech v Pythonu

Tento rychlý Start vám umožní začít odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v Pythonu, je rozhraní API webovou službou RESTful, která je kompatibilní s jakýmkoli programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místní odpovědi z rozhraní API vyhledávacího dotazu `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2. x nebo 3. x
 
Musíte mít [Cognitive Services účet rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API Bingu. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč, který je k dispozici v bezplatné zkušební verzi.  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Spuštění úplné aplikace

Následující kód získá lokalizované výsledky. Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte parametr dotazu. 
3. Zadejte vyhledávací funkci, která vytvoří požadavek a přidá hlavičku OCP-APIM-Subscription-Key.
4. Nastavte hlavičku OCP-APIM-Subscription-Key. 
5. Vytvořte připojení a odešlete žádost.
6. Zobrazte výsledky ve formátu JSON.

Následuje celý kód pro tuto ukázku:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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
- [Rychlý start hledání C# místních obchodních obchodů](local-quickstart.md)
- [Rychlý Start uzlu místního hledání firmy](local-search-node-quickstart.md)
