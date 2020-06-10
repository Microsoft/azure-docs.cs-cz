---
title: Rychlý Start – odeslání dotazu do rozhraní API v Pythonu – vyhledávání místních obchodů Bingu
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít používat rozhraní API Bingu pro vyhledávání místních obchodních aplikací v Pythonu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606271"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rychlý Start: odeslání dotazu do rozhraní API Bingu pro vyhledávání místních obchodních hledání v Pythonu

V tomto rychlém startu se dozvíte, jak odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v Pythonu, rozhraní API je webová služba RESTful, která je kompatibilní s jakýmkoli programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místních odpovědí z rozhraní API pro vyhledávací dotaz.

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2. x nebo 3. x.
* [Cognitive Services účet rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraní API pro vyhledávání Bingu. Pro tento rychlý Start je [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) dostačující. Uložte klíč rozhraní API, který je k dispozici při aktivaci bezplatné zkušební verze. Další informace najdete v tématu [Cognitive Services ceny – vyhledávání BINGU API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

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
- [Rychlé zprovoznění Node. js pro místní hledání v obchodu](local-search-node-quickstart.md)
