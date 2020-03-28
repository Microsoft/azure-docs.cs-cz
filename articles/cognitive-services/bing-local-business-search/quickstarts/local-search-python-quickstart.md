---
title: Úvodní příručka – Odeslání dotazu do rozhraní API v Pythonu – Hledání místní chod ů Služby Bing
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro vyhledávání místních společností Bingu v Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: c7e7ef7f052fccfea18b246f41109d5fa7528b4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379739"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Úvodní příručka: Odeslání dotazu do rozhraní API pro vyhledávání pro místní firmy Bingu v Pythonu

Tento rychlý start můžete začít odesílat požadavky do rozhraní API pro vyhledávání místních společností Bingu, což je služba Azure Cognitive Service. Zatímco tato jednoduchá aplikace je napsána v Pythonu, API je restful webová služba kompatibilní s libovolným programovacím jazykem schopným provádět požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místní odpovědi `hotel in Bellevue`z rozhraní API pro vyhledávací dotaz .

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2.x nebo 3.x
 
Musíte mít [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API Bing. Pro účely tohoto rychlého startu vám bude stačit [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč poskytnutý bezplatnou zkušební verzí.  Viz také [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Spuštění úplné aplikace

Následující kód získá lokalizované výsledky. Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte parametr dotazu. 
3. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku Ocp-Apim-Subscription-Key.
4. Nastavte hlavičku Ocp-Apim-Subscription-Key. 
5. Vytvořte připojení a odešlete požadavek.
6. Zobrazte výsledky ve formátu JSON.

Následuje celý kód pro tuto ukázku:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
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
- [Rychlý start java vyhledávání místních společností](local-search-java-quickstart.md)
- [Úvodní příručka pro místní obchodní vyhledávání C#](local-quickstart.md)
- [Rychlý začátek uzlu hledání místní firmy](local-search-node-quickstart.md)
