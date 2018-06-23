---
title: Rychlý start Python pro náhled adresy URL projektu - kognitivní služby Microsoft | Microsoft Docs
description: Ukázka skriptu rychle začít pomocí verze Preview adresa URL projektu v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343242"
---
# <a name="url-preview-python-quickstart"></a>Adresa URL Preview Python rychlý start

Následující příklad Python vytvoří Náhled adresy Url pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

Tento příklad používá Python 3.6.

## <a name="code-scenario"></a>Scénář kódu 

Následující kód vytvoří adresu URL náhledu.
Jsou implementované v následujících krocích:
1. Deklarujte proměnné zadat koncový bod hostitele a cestu.
2. Zadejte adresu URL dotazu si verzi preview a přidat parametr dotazu.  
3. Nastavte parametr dotazu.
4. Definování funkce vyhledávání, která vytvoří žádost a přidá *Ocp-Apim-Subscription-Key* záhlaví.
5. Nastavte *Ocp-Apim-Subscription-Key* záhlaví. 
6. Zkontrolujte připojení a odeslat žádost.
7. Tisk výsledků JSON.

Kód dokončení v této ukázce takto:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>Další postup
- [Rychlý start C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Adresa URL uzlu rychlý start](node-quickstart.md)