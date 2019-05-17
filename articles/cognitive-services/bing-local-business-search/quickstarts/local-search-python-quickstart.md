---
title: Rychlý start – odeslat dotaz do API Bingu pro místní obchodní vyhledávání v Pythonu | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pokud chcete začít používat API Bingu pro místní obchodní vyhledávání v Pythonu pomocí tohoto článku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e6626c1243f9f05a56effe3538870a5be9ac6b34
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796567"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rychlý start: Odešle dotaz do API Bingu pro místní obchodní vyhledávání v Pythonu

V tomto rychlém startu můžete zahájit odesílání požadavků na místní firmy hledání rozhraní API Bingu, což je kognitivní služby Azure. Když je tato jednoduchá aplikace napsané v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s libovolný programovací jazyk umožňuje zasílat požadavky HTTP a parsování formátu JSON.

Tato ukázková aplikace získá data místní odpověď z rozhraní API pro vyhledávací dotaz `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* [Python](https://www.python.org/) 2.x nebo 3.x
 
Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API pro Bing. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Použijte přístupový klíč poskytuje bezplatná zkušební verze.  Viz také [služeb Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Spusťte kompletní aplikace

Následující kód načte lokalizované výsledky. Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte parametr dotazu. 
3. Definujte funkci vyhledávání, který vytvoří žádost a přidá záhlaví Ocp-Apim-Subscription-Key.
4. Nastavte záhlaví Ocp-Apim-Subscription-Key. 
5. Ujistěte se, připojení a odešlete žádost.
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

## <a name="next-steps"></a>Další postup
- [Místní pracovní hledání Java Quickstart](local-search-java-quickstart.md)
- [Místní pracovní hledání C# rychlý start](local-quickstart.md)
- [Místní pracovní hledání uzlu rychlý start](local-search-node-quickstart.md)
