---
title: Koncový bod volání pomocí Python - Bing vlastní vyhledávání - Microsoft kognitivní služby
description: Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí Python k vyvolání koncový bod služby Bing vlastní vyhledávání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343815"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Volání hledání vlastní Bing koncový bod (Python)

Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí Python k vyvolání koncový bod služby Bing vlastní vyhledávání. 

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu je potřeba:

- Vlastní hledání instance. V tématu [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).

-  [Python](https://www.python.org/) nainstalována.

- A [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure. 

## <a name="run-the-code"></a>Spuštění kódu

Volání hledání vlastní Bing koncový bod, postupujte takto:

1. Vytvořte složku pro váš kód.
2. Z příkazového řádku správce nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Nainstalujte **požadavky** modul python:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Vytvoření souboru BingCustomSearch.py a zkopírujte do ní následující kód.
8. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** pomocí svého ID klíče a konfigurace (viz krok 1).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. Spuštění kódu pomocí následujícího příkazu.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)