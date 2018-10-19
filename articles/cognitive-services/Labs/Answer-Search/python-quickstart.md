---
title: 'Rychlý start: Project Answer Search, Python'
titlesuffix: Azure Cognitive Services
description: Začněte používat Project Answer Search s jazykem Python.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: c33a88b65dcdf5ddddff9f5109afbe0cca7247c4
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869824"
---
# <a name="quickstart-project-answer-search-with-python"></a>Rychlý start: Project Answer Search s jazykem Python

Následující příklad v jazyce Python vytvoří a odešle žádost o informace o výrazu „Rock of Gibraltar“.

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

V tomto příkladu se používá Python 3.6.4.

## <a name="code-scenario"></a>Scénář kódu 

Následující kód vytvoří náhled adresy URL.
Implementuje se v následujících krocích:
1. Deklarujte proměnné, které za použití hostitele a cesty určují koncový bod.
2. Zadejte adresu URL dotazu k náhledu a přidejte parametr dotazu.  
3. Nastavte parametr dotazu.
4. Definujte funkci vyhledávání, která vytvoří požadavek a přidá hlavičku *Ocp-Apim-Subscription-Key*.
5. Nastavte hlavičku *Ocp-Apim-Subscription-Key*. 
6. Vytvořte připojení a odešlete požadavek.
7. Zobrazte výsledky ve formátu JSON.

Následuje celý kód pro tuto ukázku:

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````
## <a name="next-steps"></a>Další kroky
- [Rychlý start pro jazyk C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)