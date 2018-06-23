---
title: Rychlý start Python pro kognitivní služby společnosti Microsoft, vyhledání odpovědí projektu | Microsoft Docs
description: Příklad Python začít používat projektu odpovědí hledání, kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343214"
---
# <a name="project-answer-search-python-quickstart"></a>Projekt Python vyhledávání odpovědí rychlý start

Následující příklad Python vytvoří a odešle žádost o informace o "Rock Gibraltar".

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

Tento příklad používá Python 3.6.4

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
## <a name="next-steps"></a>Další postup
- [Rychlý start C#](c-sharp-quickstart.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlé spuštění uzlu](node-quickstart.md)